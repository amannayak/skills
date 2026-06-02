# Craft Flow

The end-to-end command to build a high-fidelity mobile screen, screen-state, or visual flow. This flow is built around strict, non-negotiable gates: **you must never compress planning and coding into a single step.**

---

## Phase 1: Planning Gates (Non-Negotiable)

### Gate 1: Shape Confirmation
- Before writing any code, run a short discovery to clarify the screen's purpose, target elements, and primary actions.
- Present a **Design Brief** summarizing the screen's layout, interaction model, and required states.
- **The user must explicitly approve the Design Brief before you move to Gate 2.**

### Gate 2: Direction & Layout Choices
- Present **2 distinct visual options** for the layout, components, or style (e.g., Option A: card-based feed vs Option B: edge-to-edge list feed).
- Describe the UX tradeoffs of each choice (cognitive load, Fitts's law thumb zone, scanning patterns).
- **The user must explicitly choose or mix-and-match the directions before you write any code.**

---

## Phase 2: Implementation Quality Bar

Once the planning gates are passed and the direction is approved, implement the screen following these strict mobile production standards:

### 1. Style & Spacing Cleanliness
- **Flexbox Only**: Write clean Tailwind/NativeWind utility classes. Never use web CSS Grid or layout properties that don't translate to native (e.g., absolute offsets based on web document assumptions).
- **8-Point Spacing**: Margin, padding, gap, and height values must strictly map to divisible values of 8 or 4 (`gap-4` (16px), `p-6` (24px), `m-2` (8px)).
- **Tap Hitboxes**: Enforce a minimum hit target size of **44×44pt** (iOS) / **48×48dp** (Android) for all interactable elements. Add generous padding inside touchable surfaces:
  ```tsx
  // GOOD: Clear tap target with padding and hitSlop
  <TouchableOpacity 
    className="p-3 justify-center items-center" 
    hitSlop={{ top: 12, bottom: 12, left: 12, right: 12 }}
    onPress={handlePress}
  >
    <LucideIcon name="chevron-left" />
  </TouchableOpacity>
  ```

### 2. Complete State Matrix
A production screen is never just "completed." You must implement and review all of these states:
- **Default State**: The golden path with clean, high-fidelity mock data. No "Lorem Ipsum."
- **Empty State**: An engaging illustration, informative copy, and a clear call to action to onboard the user.
- **Loading State**: Smooth skeleton loaders (using Reanimated opacity fades) instead of raw white screens or blocking spinner modals.
- **Error State**: Non-disruptive, friendly warning banners or inline cards with an obvious "Retry" action.
- **Success State**: Rich visual feedback (a springy success badge, haptic confirmation) celebrating the completion.

### 3. Tactile Feedback (Haptic Integration)
- Every primary action (submitting a form, completing a task, tapping a primary CTA) must have a subtle haptic trigger linked to it.
- **Call on Both Platforms**: `expo-haptics` works on iOS and Android and safely no-ops where unsupported. Do not gate it behind an iOS-only check (that silences Android). See `reference/haptic.md` for the full taxonomy:
  ```tsx
  import * as Haptics from 'expo-haptics';
  
  const triggerSuccess = () => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  };
  ```

### 4. Interactive Feel (Motion & Physics)
- All interactive buttons must have a physical response when pressed. Scale the element down slightly (to `0.97`) to simulate compression:
  ```tsx
  import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
  
  // Inside Component:
  const scale = useSharedValue(1);
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }]
  }));
  
  const handlePressIn = () => { scale.value = withSpring(0.97, { damping: 15 }) };
  const handlePressOut = () => { scale.value = withSpring(1) };
  ```

### 5. Safe Area Integrity
- Ensure no elements intersect with top notch/Dynamic Island boundaries or bottom Home Indicator lines.
- For scroll lists, ensure content is rendered edge-to-edge behind the bottom home bar:
  ```tsx
  import { useSafeAreaInsets } from 'react-native-safe-area-context';
  
  const insets = useSafeAreaInsets();
  return (
    <ScrollView 
      contentContainerStyle={{ 
        paddingTop: 16, 
        paddingBottom: insets.bottom + 24 
      }}
    >
      {/* Content */}
    </ScrollView>
  );
  ```

---

## Phase 3: Hand-Off & Verification

After implementation is complete, run through this checklist before declaring success:
1. **Contrast Check**: Do all foreground labels pass color contrast checks against their containers?
2. **Double-Platform Check**: Will this layout render correctly on Android (handling safe areas and scroll bounces correctly)?
3. **No Web Clichés**: Did you accidentally include CSS Grid, web scroll styles, or hardcoded status bar heights?
4. **Haptics Bound**: Are all primary touch triggers backed by haptic cues?
