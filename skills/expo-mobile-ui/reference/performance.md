# Performance Design Flow

How to audit, optimize, and maintain peak rendering performance, 120Hz scrolling framerates, and rapid interface response times in Expo.

---

## Core Philosophy of Mobile Performance

Mobile performance is not an optimization pass to run before release — it is a foundational layout constraint. Stuttering framerates or heavy re-render cycles break the physical immersion of a tactile screen:
1. **The 60fps/120Hz Mandate**: All user-initiated animations, gestures, and list scrolls must target peak display framerates (60fps on standard devices, 120Hz on ProMotion/Pro devices).
2. **Strict Render Offloading**: Keep the JavaScript thread clear of heavy computation. All animation calculations must run on the UI thread via Reanimated worklets.
3. **Optimized Virtualization**: Never render dynamic, unstructured datasets inside simple Map loops. Enforce strict virtualized recycling lists.

---

## 1. High-Performance Lists (`FlashList`)

For all scrolling feeds or unstructured lists, use a virtualized list. `<FlatList>` is valid and fully maintained, but `@shopify/flash-list`'s `<FlashList>` recycles views and materially outperforms it on long or media-heavy feeds — prefer `<FlashList>` there. The non-negotiable rule (see `SKILL.md` Absolute Bans) is simply: **never** render a dynamic list via `<ScrollView>` + `.map()`.

### Design & Implementation Rules:
- **Set Explicit Item Sizes (v1)**: On FlashList **v1**, `estimatedItemSize` is required — it is the single most important parameter for preventing initial list layout-jitter and blank cell scroll artifacts. Set it to the average cell height (e.g., `estimatedItemSize={84}`).
  - **Version caveat**: FlashList **v2** auto-measures items and **deprecates** `estimatedItemSize` — omit it there. Check the installed `@shopify/flash-list` version before adding the prop so the guidance does not fight a v2 install.
- **Keep Item Components Lean**: Avoid heavy computational evaluations or heavy state trees inside list cells. Delegate item cells to standalone, memoized components:
  ```tsx
  import React, { memo } from 'react';
  import { FlashList } from '@shopify/flash-list';
  
  const ListItemCell = memo(({ item }) => (
    <View className="flex-row p-4 items-center border-b border-neutral-100">
      <Text className="font-semibold">{item.title}</Text>
    </View>
  ));
  
  // Inside Feed Parent:
  <FlashList
    data={dataList}
    renderItem={({ item }) => <ListItemCell item={item} />}
    estimatedItemSize={72}
    getItemType={(item) => item.kind} // see rule below
  />
  ```
- **Type Heterogeneous Lists with `getItemType`**: When a feed mixes cell shapes (section headers, ad slots, media vs text rows), supply `getItemType={(item) => item.kind}`. FlashList recycles views **within each type**, so without it the recycler reuses mismatched cells and produces layout jank and flicker on fast scroll. This is the highest-leverage fix for stuttering mixed-content feeds (common in the media/social register).

---

## 2. Low-Overhead Animations (Reanimated Worklets)

Animations must never bog down the main thread.

### Implementation Cues:
- **UI Thread Offloading**: All interpolation, translation, scale modifications, and opacity sweeps must reside in Reanimated worklets (`useAnimatedStyle`, `useSharedValue`).
- **Never trigger standard re-renders**: Avoid using standard React `useState` variables to drive continuous coordinate transitions (e.g., custom slider bars, custom progress indicators, or floating elements). Bind directly to a Shared Value:
  ```tsx
  import { useSharedValue, useAnimatedStyle } from 'react-native-reanimated';
  
  const opacity = useSharedValue(0);
  
  const animatedStyle = useAnimatedStyle(() => ({
    opacity: opacity.value, // Translates instantly on the UI thread
  }));
  ```

---

## 3. Re-Render Audits

Frequent, heavy layout thrashing is the primary cause of battery drain and sluggish keyboard inputs on mobile.

- **Check State Updates**: When implementing rich inputs, isolate input state updates from the wider parent layout container to prevent full-screen re-renders on every keystroke.
- **Memoization Guards**: Use `React.memo` for static cells, navigation headers, background layout illustrations, or stationary lists.
- **Callback & Dependency Cache**: Wrap complex functions passed to child inputs with `useCallback`. Wrap intensive dataset transforms with `useMemo`.

---

## 4. Image Caching & Assets (`expo-image`)

Raw, uncached image renders cause sudden flashes of white content, high memory consumption, and slow list load times.

- **Standardize on `expo-image`**: Never use the legacy, slow React Native `<Image>` component.
- **Implementation Guidelines**:
  - Use `contentFit="cover"` instead of legacy resize modes.
  - Set a blurhash placeholder (e.g., `placeholder={{ blurhash: 'L6PZ0VPh00j.9Fwp~q_3_3ofD%xu' }}`) to prevent empty white layout blocks during network image loads. (Current `expo-image` expects the object form; a bare string is the deprecated API.)
  - Configure optimal cache policies (`cachePolicy="disk"`) for recurrent user content.
  - Prefetch critical hero assets on app startup using `Image.prefetch()`.
