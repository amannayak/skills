# Haptic Design Flow

How to design and integrate a cohesive tactile haptic feedback language into your Expo app using `expo-haptics`.

---

## Core Philosophy of Tactile Feedback

Haptics are the "touch" of the interface. When designed correctly, they bridge the gap between virtual screens and physical things:
1. **Never Spurious**: Only trigger haptics when an action creates a physical state change (buttons compressing, switches flipping, items snapping, tasks completing). Do not trigger haptics on passive events like scrolling lists or loading pages.
2. **Cross-Platform by Default**: `expo-haptics` works on **both iOS and Android** — its functions safely no-op on devices or simulators without a haptic engine. Do **not** gate haptic calls behind `process.env.EXPO_OS === 'ios'`; doing so silences Android entirely and breaks the dual-platform mandate. Call `expo-haptics` directly on both platforms. Only drop to the raw `Vibration` API (Section 2) when you need custom vibration timings that `expo-haptics` does not express.
3. **Discrete Timing**: A haptic cue must fire the exact millisecond the touch threshold is met, never during press-in or after loading delays.

---

## 1. The Mobile Tactile Taxonomy

We define 5 core haptic states and their corresponding system-level triggers:

### State A: Selection Change (Subtle)
- **Vibe**: A tiny, almost imperceptible click representing a soft toggle.
- **Triggers**: Scrolling past items in a wheels list, changing an active Segmented Control tab, expanding/collapsing accordion cells.
- **Implementation**:
  ```tsx
  import * as Haptics from 'expo-haptics';
  
  // Works on both iOS and Android; no-ops safely where unsupported.
  Haptics.selectionAsync();
  ```

### State B: Medium Impact (Tactile Button)
- **Vibe**: A quick, solid compression feeling. Like pressing a mechanical button.
- **Triggers**: Releasing a primary CTA, completing an input field, opening a bottom sheet.
- **Implementation**:
  ```tsx
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  ```

### State C: Light Impact (Quiet/Secondary Action)
- **Vibe**: A very sharp but quiet click.
- **Triggers**: Tapping secondary navigation chevrons, clicking checkboxes, sliding past minor list dividers.
- **Implementation**:
  ```tsx
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  ```

### State D: Heavy Impact (Significant Action/Limit Met)
- **Vibe**: A deep, heavy thud.
- **Triggers**: Pulling a swipe item to its absolute edge limit, holding down a major action item, a deletion confirmation.
- **Implementation**:
  ```tsx
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
  ```

### State E: Notification Patterns (Event Success / Fail)
- **Vibe**: Rhythmic multi-clicks communicating a clean outcome status.
- **Triggers**: Form successfully submitted (Success), biometric scan failed (Error), password field check failed (Warning).
- **Implementation**:
  ```tsx
  // Success (double rapid tap)
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  
  // Warning (slow double tap)
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);
  
  // Error (heavy rapid three-tap)
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error);
  ```

---

## 2. A Unified Cross-Platform Helper

Centralize the taxonomy into one helper that runs on **both** platforms. `expo-haptics` covers iOS and Android natively — there is no need for an iOS-only branch. Reach for the raw `Vibration` API only when you need a custom timing pattern that the standard taxonomy above does not express:

```tsx
import * as Haptics from 'expo-haptics';

export const triggerHaptic = async (type: 'light' | 'medium' | 'success' | 'error') => {
  switch (type) {
    case 'light':
      await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      break;
    case 'medium':
      await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
      break;
    case 'success':
      await Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      break;
    case 'error':
      await Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error);
      break;
  }
};
```

### Custom Vibration Timings (Edge Case Only)
If a design calls for a bespoke rhythmic pattern beyond the standard impact/notification styles, drop to the `Vibration` API directly. This is rare — prefer the standard taxonomy for consistency:

```tsx
import { Vibration } from 'react-native';

// Timing array in ms: [wait, vibrate, wait, vibrate, ...]
Vibration.vibrate([0, 30, 40, 30, 40, 30]); // Heavy rapid triple-click
```

---

## 3. The Interactive Haptic Rules

- **Link to Tap-Release**: For CTA buttons, fire haptics on the `onPress` trigger (which corresponds to touch release), never on `onPressIn`. This ensures feedback aligns precisely with the execution of the user's action.
- **Never on Page-Load**: Do not fire haptics when a screen mounts. Haptics belong exclusively to interactive actions initiated by the user.
- **Disable with Accessibility**: If the user has disabled tactile cues or system vibrations, respect their preference. Expo handles this natively, but do not override or loop vibrations if a command fails to compile first-time.
