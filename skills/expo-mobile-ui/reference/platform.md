# Platform Parity Flow

How to handle visual, functional, and layout divergence between Apple Human Interface Guidelines (HIG) and Android Material Design 3 (MD3) inside a single, unified Expo codebase.

---

## Core Philosophy of Platform Parity

Expo builds one app for both platforms simultaneously. A truly professional app feels native to both iOS and Android users by respecting the layout conventions, typography, gestures, and UI vocabularies of each operating system:
1. **Never Compromise with Lowest Common Denominator**: Do not strip away native premium features (like SF Symbols or Material ripples) to make the code identical. Build platform-aware branches.
2. **Standardize on `process.env.EXPO_OS`**: Use static compilation flags (`process.env.EXPO_OS === 'ios'`) where possible to strip platform-specific branches at build time. Use runtime checks (`Platform.OS === 'android'`) for dynamic theme calculations.
3. **Adaptive Component Maps**: Use a clear decision matrix to decide when to write shared React Native views vs wrapping fully native UI controls.

---

## 1. Platform Divergence Decision Matrix

Use this matrix to guide your structural choices during screen development:

| Interaction Goal | iOS (HIG) Paradigm | Android (MD3) Paradigm |
|---|---|---|
| **Root Layout** | Bottom Tab Navigation | Bottom Navigation Bar / Nav Rail |
| **Icons** | SF Symbols (`source="sf:name"`) | Material Symbols (XML Vectors) |
| **Material Effect** | Liquid Glass (`expo-glass-effect`) | Material You Dynamic Surface tints |
| **Rounding** | Continuous Curves (`borderCurve: 'continuous'`) | Standard Geometric Corners (`rounded-2xl`) |
| **Modal Container** | Form Sheets / Detented Sheets | Floating/Full-Screen Bottom Sheets |
| **Header Styling** | Large Collapsible Title | Top App Bar with Flat/Centred Title |
| **Touch Response** | Scale Compression (`scale: 0.98`) | Interactive Ink Ripple (`android_ripple`) |
| **Primary CTA** | Floating Pill or Rounded Bottom Button | Floating Action Button (FAB) or Pill |

---

## 2. iOS HIG Design Alignment

### Apple Liquid Glass (iOS 26)
- Apply translucent, glassy backgrounds using `expo-glass-effect` for navigation headers, bottom drawers, and floating panels.
- Never use glass effects for content text cards or busy lists, as this degrades readability.
- **Respect Reduce Transparency**: Translucent chrome becomes unreadable for users who enable Reduce Transparency (and the equivalent high-contrast modes). Query the setting and fall back to an opaque surface token when it is on — mirror the same diligence applied to Reduce Motion:
  ```tsx
  import { AccessibilityInfo } from 'react-native';
  import { useEffect, useState } from 'react';

  const [reduceTransparency, setReduceTransparency] = useState(false);
  useEffect(() => {
    AccessibilityInfo.isReduceTransparencyEnabled().then(setReduceTransparency);
    const sub = AccessibilityInfo.addEventListener(
      'reduceTransparencyChanged',
      setReduceTransparency,
    );
    return () => sub.remove();
  }, []);
  // reduceTransparency → render an opaque `surface` token instead of <GlassView />.
  ```

### continuous border curves:
- All rounded corners on iOS cards and panels must apply continuous rounding to match Apple's hardware aesthetic:
  ```tsx
  <View style={{ borderRadius: 16, borderCurve: 'continuous' }}>
  ```

### SF Symbols & Semantic Tints:
- Render high-fidelity system icons with SF Symbols. The canonical Expo API is **`expo-symbols`' `<SymbolView />`**, which exposes weight, scale, rendering mode, and `animationSpec`:
  ```tsx
  import { SymbolView } from 'expo-symbols';
  <SymbolView name="star.fill" size={24} tintColor={PlatformColor('systemBlue')} />
  ```
  - `expo-image` also accepts an `sf:` source string (`<Image source="sf:star.fill" />`) — use that form only if your project already standardizes on it per `building-native-ui`; otherwise prefer `expo-symbols`.
- Use `PlatformColor` to access system-managed semantic brushes (`label`, `secondaryLabel`, `systemBackground`) so dark mode transition is automatic.

---

## 3. Android Material Design 3 Expressive

### Material You Dynamic Colors:
- Query Android dynamic wallpaper themes using native hooks. Apply wallpaper-tinted colors (primary, secondary, surface) to container tiles to make the app feel deeply integrated with the user's OS theme.

### Expressive Motion (1000ms Springs):
- MD3 uses springy, expressive animations. For entry animations on Android, use bouncier springs (damping `12`) with slightly longer durations compared to iOS's tighter, subtle transitions.

### Ink Ripple Touch Responses:
- Every pressable tile or button on Android must display an interactive ripple feedback that radiates outward from the exact coordinate of the tap. On iOS, `android_ripple` is ignored, so pair it with an opacity (or scale) press state so both platforms get tactile feedback:
  ```tsx
  import { Platform, Pressable } from 'react-native';

  <Pressable
    android_ripple={{ color: 'rgba(0,0,0,0.1)', borderless: false }}
    style={({ pressed }) => [
      // iOS has no ripple — dim slightly on press instead.
      { opacity: pressed && Platform.OS === 'ios' ? 0.9 : 1 },
    ]}
  >
  ```

---

## 4. Typography, Back Gestures, and Shadows

### Font Divergence:
- Default to the system's preferred typefaces: `SF Pro` on iOS; `Roboto` / `Google Sans` on Android.
- If using a custom brand font, load it via `expo-font` and apply standard CSS-equivalent weights. Keep the line heights exact to prevent tail letters (g, j, y) from clipping in tight native text containers.

### Back Gesture Parity:
- **iOS Swipe-Back**: Always keep iOS horizontal swipe-back navigation enabled. Never implement full-bleed gestures that block this edge sweep.
- **Android Predictive Back**: Ensure custom screen Stacks respond correctly to the Android predictive back gesture, revealing the home tab state underneath during the sweep.

### Unified Shadows (`boxShadow`):
- Never use legacy split `shadowOffset` or `elevation` parameters. Enforce clean, unified, platform-agnostic shadows using the modern `boxShadow` style prop supported in current Expo SDKs:
  ```tsx
  // GOOD: Unified shadow that renders beautifully on both iOS and Android
  <View style={{ boxShadow: '0px 4px 12px rgba(0, 0, 0, 0.08)' }}>
  ```
- Match the shadow tint to the underlying background color hue to keep layouts feeling rich and three-dimensional.
