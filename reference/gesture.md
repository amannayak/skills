# Gesture Design Flow

How to design and implement premium, fluid native gestural interactions in Expo using `react-native-gesture-handler` and `react-native-reanimated`.

---

## Core Philosophy of Mobile Gestures

On mobile, gestures are the physical link between the user and the software. They must feel responsive, predictable, and aligned with physical laws:
1. **Zero JS-Thread Block**: All gesture tracking and translation must execute strictly on the native UI thread via Reanimated worklets. Never route drag values back to the JS thread.
2. **Spring Physics over Linear Timing**: Never use linear timing for swipe dismissals or drawer reveals. Use spring physics (`withSpring`) to model physical weight, inertia, and friction.
3. **Reduced Motion Support**: Always check if the user has requested reduced motion (`useReducedMotion()` from Reanimated 3) and degrade to instant transitions or fading cross-slides where necessary.
4. **Bridge to JS with `runOnJS`**: Gesture callbacks (`.onChange`, `.onEnd`) and `useAnimatedStyle`/`useAnimatedReaction` bodies are **worklets** that run on the UI thread. Calling any plain JS function from inside them — `Haptics.*`, `setState`, navigation, analytics — throws `Tried to synchronously call a non-worklet function on the UI thread` and crashes in release builds. You MUST wrap such calls in `runOnJS`:
   ```tsx
   import { runOnJS } from 'react-native-reanimated';
   import { triggerHaptic } from '@/lib/haptics';

   const gesture = Gesture.Pan().onEnd(() => {
     translationX.value = withSpring(0);
     runOnJS(triggerHaptic)('medium'); // NEVER call triggerHaptic() directly here
   });
   ```

---

## 1. Swipe-to-Action Pattern

Used for swiping items in a list to reveal context actions (delete, flag, edit).

### Implementation Rules:
- Avoid web-like sudden translation shifts. Wrap the swipe area in `<PanGestureHandler>` or Reanimated's modern `<GestureDetector>`.
- Use a resistance curve when the user pulls past the maximum action threshold to simulate a physical limit.
- Provide clear visual indicators (icon opacity, background color shifts) showing what will happen when released.

```tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';

// Inside list item:
const translationX = useSharedValue(0);
const threshold = -80; // Swiping left to reveal actions

const gesture = Gesture.Pan()
  .onChange((event) => {
    // If pulling past action threshold, apply resistance
    if (event.translationX < threshold) {
      translationX.value = threshold + (event.translationX - threshold) * 0.3;
    } else {
      translationX.value = event.translationX;
    }
  })
  .onEnd((event) => {
    if (event.velocityX < -500 || translationX.value < threshold / 2) {
      // Swipe successfully revealed action
      translationX.value = withSpring(threshold, { damping: 15 });
    } else {
      // Snapped back to closed
      translationX.value = withSpring(0);
    }
  });
```

---

## 2. Interactive Bottom Sheets

Bottom sheets provide focus and detail. They are the standard container for complex inputs, selections, or secondary information screens.

### Design Standards:
- **Snap Points**: Define explicit snap points (e.g., `['25%', '50%', '90%']`) using `@gorhom/bottom-sheet` or native bottom sheets as described in `building-native-ui`.
- **Drag Handle Target**: Ensure the dragging handle area at the top of the sheet has a minimum tap target height of 24px and includes an intuitive visual line indicator.
- **Backdrop Dimming**: Include an interactive, semi-transparent backdrop behind the sheet. Tapping the backdrop must immediately trigger a smooth springy dismissal of the sheet.
- **Scroll Synchronization**: When a sheet contains a list, synchronize the drag gesture so that dragging up scrolls the list only after the sheet reaches its maximum snap point.

---

## 3. Swipe-to-Dismiss / Pull-Down Modals

For modal screens (like image viewers or card detailed views), a full swipe-down or swipe-right gesture is the most satisfying way to exit.

### Implementation Cues:
- **Velocity Detection**: Do not just check the translation distance. If a user swipes down with high velocity (`velocityY > 800`), dismiss the screen immediately regardless of translation distance.
- **Background Fade Alignment**: Bind the background overlay opacity directly to the translation scale:
  ```tsx
  const animatedStyle = useAnimatedStyle(() => {
    const opacity = 1 - Math.min(Math.abs(translationY.value) / 300, 1);
    return {
      opacity: opacity,
      transform: [{ translateY: translationY.value }]
    };
  });
  ```

---

## 4. Drag-and-Drop Reordering

For lists where hierarchy matters (such as prioritizing tasks or editing favorites).
- Keep animations light and responsive.
- Apply a slight elevation and scale expansion (scale up to `1.03` with a soft drop shadow) to the dragged element to represent it lifting off the canvas.
- Provide brief, discrete selection haptic clicks as the dragged item slides over adjacent rows. Because the row-crossing detection happens inside a worklet, fire the haptic through `runOnJS` (see Core Philosophy rule 4) — never call `Haptics.*` directly from the gesture/reaction body.
