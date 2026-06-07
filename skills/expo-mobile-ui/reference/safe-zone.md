# Safe-Zone Layout Flow

How to handle safe-area insets, notch heights, Dynamic Islands, the iOS Home Indicator, dynamic status bars, and keyboard-avoiding inputs across varying mobile devices.

---

## Core Philosophy of Safe-Zone Layouts

Mobile screens are no longer rectangular grids. They have rounded corners, physical camera notches, dynamic status indicators, and virtual home gestural bars.
1. **Never Hardcode pixel heights**: Device aspect ratios are highly fragmented. Never hardcode padding values like `pt-11` (for notch) or `pb-8` (for home bar) to clear safe zones. Always bind to dynamic safely-resolved bounds.
2. **Never Let Input Fields Slide Off-Screen**: Input views must automatically rise above the virtual software keyboard when it mounts.
3. **Scrollable Bleed**: The visual canvas (colors, images, maps) must flow completely to the physical edge of the device, while interactive controls (buttons, forms, links) are held safely within safe-area bounds.

---

## 1. Safe Area Insets (`react-native-safe-area-context`)

The standard library for resolving notches, dynamic islands, and home indicator bounds.

### Code Standards:
- Wrap your root app layout file (`app/_layout.tsx`) with `<SafeAreaProvider>`.
- Use the React hook `useSafeAreaInsets()` to resolve padding values dynamically:
  ```tsx
  import { useSafeAreaInsets } from 'react-native-safe-area-context';
  
  export default function MyScreen() {
    const insets = useSafeAreaInsets();
    
    return (
      <View 
        style={{ 
          flex: 1, 
          paddingTop: insets.top, // Dynamic Notch/Dynamic Island clear
          paddingBottom: insets.bottom, // Dynamic Home Indicator clear
        }}
      >
        {/* Interactive Content */}
      </View>
    );
  }
  ```

### Scroll bleeds:
- For scrolling pages, do NOT wrap the whole container with `<SafeAreaView>`. This clips the scrolling content at the notch or home bar, creating a harsh letterbox block.
- Instead, let the `<ScrollView>` bleed edge-to-edge, and apply the insets directly to the scroll view's internal spacing:
  ```tsx
  <ScrollView 
    contentContainerStyle={{ 
      paddingTop: insets.top + 16, 
      paddingBottom: insets.bottom + 24 
    }}
  >
    {/* Page Content scrolls completely behind safe elements */}
  </ScrollView>
  ```

---

## 2. Dynamic Status Bar Handling

The status bar contains the clock, battery meter, and network indices. It must adapt to the underlying screen background.

- **Contrast Matching**: On screens with light backgrounds, use a dark status bar. On screens with dark backgrounds, use a light status bar:
  ```tsx
  import { StatusBar } from 'expo-status-bar';
  
  // Inside light screen layout:
  <StatusBar style="dark" />
  
  // Inside dark screen layout:
  <StatusBar style="light" />
  ```
- **Modal Overlays**: Ensure sheet presentation modals set the status bar style to "auto" so the system adapts gracefully based on the light/dark scheme.

---

## 3. Keyboard-Avoiding Inputs

When the software keyboard slide-mounts from the bottom of the viewport, it must push active input cells safely into view.

- **Avoid standard container overlaps**: Wrap input-dense screens with `<KeyboardAvoidingView>` and configure the behavior appropriately for each target platform. Use `'padding'` on iOS; on Android prefer `undefined` (let the native `windowSoftInputMode="adjustResize"`, the default in Expo, handle it) — `behavior="height"` is a known footgun that frequently mis-sizes the container:
  ```tsx
  import { KeyboardAvoidingView, Platform, ScrollView } from 'react-native';
  
  return (
    <KeyboardAvoidingView
      behavior={Platform.OS === 'ios' ? 'padding' : undefined}
      className="flex-1"
    >
      <ScrollView>
        {/* Inputs */}
      </ScrollView>
    </KeyboardAvoidingView>
  );
  ```
- **Keyboard Dismiss Handling**: Wrap background non-action surfaces with `<TouchableWithoutFeedback onPress={Keyboard.dismiss}>` so that tapping empty space immediately clears the active keyboard focus.
- **For input-heavy screens, prefer `react-native-keyboard-controller`**: Its `<KeyboardAvoidingView>`/`<KeyboardAwareScrollView>` track the keyboard frame natively and animate in sync on both platforms, sidestepping the per-platform `behavior` quirks of RN's built-in component. Reach for it whenever a screen has more than a couple of inputs.

---

## 4. Landscape Orientations (Insets Change)

On devices with horizontal notches (or tablets in landscape mode), safe area margins must adapt instantly.
- Insets are non-static values. `useSafeAreaInsets()` automatically recalculates when the device orientation changes.
- Ensure left and right inset bounds are factored into side-margin scales when rendering wide-layout components:
  ```tsx
  const insets = useSafeAreaInsets();
  // Safe side padding on landscape rotation
  <View style={{ paddingLeft: insets.left + 16, paddingRight: insets.right + 16 }}>
  ```
- Avoid forcing landscape mode unless explicitly requested in the design brief (e.g., video playing or mapping surfaces).
