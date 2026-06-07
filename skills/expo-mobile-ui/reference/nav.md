# Navigation Design Flow

How to design and implement slick, intuitive navigation frameworks, headers, tab configurations, and deep-link structures in Expo.

---

## Core Philosophy of Mobile Navigation

Navigation is the skeletal structure of the user experience. On mobile, it must remain lightweight, predictable, and visually consistent:
1. **The 5-Tab Rule**: Bottom tab navigation bars must hold between **2 and 5 items max**. Anything more overflows target boundaries and increases interaction cost.
2. **Predictable Back-Stack Tracing**: Swipe-back gestures on iOS and predictive back gestures on Android must always behave predictably. Never intercept back gestures unless data loss is imminent (e.g., exiting a mid-transaction screen).
3. **Modal Staging (Context Focus)**: Use Stacks for general progressive linear detail pushes. Use Modals (`presentation: "modal"`) for branching non-linear context tasks (e.g., creating a new item, configuring a setting).

---

## 1. Native Tab Bar Design (Root Layout)

Tab bars are the core home layout. They represent the primary functional categories of the app.

### Design Standards:
- **Visual Feedback**: Active tabs must have a clear visual shift (high-contrast brand accent color, bold text weights, or an filled-icon state) compared to inactive tabs (muted-gray, hollow icon).
- **Tab Badges**: Badges (e.g., unread notification dots) should be placed strictly at the top-right corner of the tab icon, sized at **16×16px** with monospace, vertically-centered numbers.
- **Spring-Scale Indicator**: Apply a subtle scaling transition (bounce to `1.1` and return) to the tab icon the moment it is active to represent mechanical completion.

### Directory Structure (Expo Router):
Ensure the directory structure matches the file-based route conventions of Expo Router as defined in `building-native-ui`:
```
app/
├── (tabs)/
│   ├── _layout.tsx       <-- Tabs Navigator Configuration
│   ├── index.tsx         <-- Home Tab
│   ├── search.tsx        <-- Search Tab
│   └── settings.tsx      <-- Settings Tab
├── _layout.tsx           <-- Main Root Navigator (Stack with Modal targets)
└── modal.tsx             <-- Settings or Compose branching target
```

---

## 2. Interactive Navigation Headers

The navigation header is the visual crown of the screen.

### iOS Large Titles:
- Use large, bold titles (e.g., `text-3xl font-bold` or `fontWeight: '700'`) for the primary landing screens.
- **Scroll Synchronization**: On scroll, the large title must transition smoothly to a smaller, centered title in the header bar. Drive the transition from the ScrollView offset. The handler only updates the shared value if it is attached to an **`Animated.ScrollView`** with `scrollEventThrottle={16}`:
  ```tsx
  import Animated, {
    useAnimatedScrollHandler,
    useAnimatedStyle,
    useSharedValue,
    interpolate,
    Extrapolation,
  } from 'react-native-reanimated';

  const scrollY = useSharedValue(0);
  const scrollHandler = useAnimatedScrollHandler((event) => {
    scrollY.value = event.contentOffset.y;
  });

  // Collapse the large title into the inline header bar over the first 60pt of scroll.
  const inlineTitleStyle = useAnimatedStyle(() => ({
    opacity: interpolate(scrollY.value, [0, 60], [0, 1], Extrapolation.CLAMP),
  }));

  return (
    <Animated.ScrollView onScroll={scrollHandler} scrollEventThrottle={16}>
      {/* content */}
    </Animated.ScrollView>
  );
  ```
- **Automatic Handling**: Refer to `building-native-ui` for setting up Native Tabs with `expo-router/unstable-native-tabs` to let the system handle this transition natively.

### Header Components:
- **Action Slots**: Keep left/right header action slots single-purpose. The left slot is reserved for back/dismiss actions; the right slot is reserved for settings, edit, or primary positive completions (e.g., "Save" or "Compose").
- **Contrast**: The header background must match the underlying container background on scroll to prevent harsh, floating line breaks.

---

## 3. Modal & Sheet Layout Presentation

Modals are branching interactions that overlay the root stack.

- **iOS Sheet Presentation**: On iOS, prefer the native Sheet modal presentation (`presentation: "formSheet"`):
  ```tsx
  // app/_layout.tsx Stack config:
  <Stack.Screen 
    name="compose-modal" 
    options={{ 
      presentation: 'formSheet',
      sheetAllowedDetents: [0.5, 0.9], // Half and full-screen detents
      sheetGrabberVisible: true,      // Grab handle visible at top
    }} 
  />
  ```
- **Android Fallback**: On Android, formSheets are simulated by full-screen modal sheets that slide up from the bottom with spring transitions.
- **Close Button Affordances**: For all modals, place an obvious close action (a text button "Cancel" or an icon "X") in the top-left or top-right header slot to ensure users are never trapped.

---

## 4. Deep-Link Mapping (Expo Router Compatibility)

Ensure navigation layouts are designed with semantic URLs in mind.
- Name screens with simple, kebab-case directory paths.
- Design modals or tabs with clear parameters to make deep links clean and sharable (e.g., `/item/[id]` or `/settings/profile?edit=true`).
- Keep sub-routes organized under layout-groups (e.g., `(tabs)` or `(auth)`) to cleanly isolate access scopes.
