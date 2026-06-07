# Media/Social App Register Flow

How to design and implement visual languages, typography scales, color schemes, motion rules, and immersive layouts for Media/Social apps (music players, video streaming, image feeds, travel marketplaces, messaging networks).

---

## Core Philosophy of Media/Social UI

Media and social apps succeed by keeping the user immersed in content, highlighting rich photography and video assets, establishing a distinct brand identity, and offering fluid, continuous gesture paths:
1. **Content is the Chrome**: Isolate and minimize UI borders and background containers. Let edge-to-edge photography, video frames, and album art act as the primary visual layers of the app.
2. **Immersive Scroll-Driven Choreography**: Use smooth, scroll-driven header fades, sticky media containers, and parallax image scales to make browsing feel like a continuous, premium movie reel.
3. **Identity-Driven Delight**: These surfaces are allowed to use bolder typography, custom font weight axes, organic colors, and playful, expressive transitions (such as full-screen sliding player transitions).

---

## 1. Visual Identity & Theme Guidelines

### Color Palette Strategy (Immersive/Dynamic):
- **Base Background**: Immersive dark modes (slate blacks, dark charcoal) are the industry standard for media-dominant platforms, letting vibrant photos and videos stand out. Light modes must use clean, crisp backgrounds with zero grey-borders to avoid visual noise.
- **Dynamic Accent Colors**: Query the color palette of the active asset (e.g., extracting the dominant hue from the playing album cover) and dynamically tint background gradients, text headers, or play buttons to match the content.
- **Continuous Glassmorphism**: Use translucent glass containers (`expo-glass-effect`) with fine white lines (`border border-white/10`) to layer controls over media layers without muddying backgrounds. Always provide an opaque fallback when Reduce Transparency is enabled (see `reference/platform.md` §2).

### Spacing & Layout Rhythm:
- **Full-Bleed Layouts**: Let images flow edge-to-edge (0px margin). Place text overlays inside bottom-anchored, translucent glass rows.
- **Spacing Flexibility**: Use larger, rhythm-based spacing gaps (`gap-8` (32px) or `gap-10` (40px)) between major content stories, combined with tight internal detail blocks.

---

## 2. Typical Interaction Patterns

- **Edge-to-Edge Media Feeds**: Implement lists using `<FlashList>` with full-bleed aspect ratios (e.g., aspect-square or 4:5 for feed cards, 16:9 for videos).
- **Persistent Mini-Players**: For music or video players, allow the full-screen detailed view to drag-dismiss down into a persistent, floating mini-player bar above the bottom tabs.
- **Scroll-Driven Parallax Headers**: Bind the height and scale of a detailed image header directly to the ScrollView's vertical offset using Reanimated. The shared value must be driven by `useAnimatedScrollHandler` and the handler attached to an **`Animated.ScrollView`** (a plain `<ScrollView>` will not update the shared value, leaving the header static):
  ```tsx
  import Animated, {
    useAnimatedStyle,
    useAnimatedScrollHandler,
    useSharedValue,
  } from 'react-native-reanimated';

  const scrollY = useSharedValue(0);
  const scrollHandler = useAnimatedScrollHandler((e) => {
    scrollY.value = e.contentOffset.y;
  });

  const headerStyle = useAnimatedStyle(() => {
    // Negative offset = overscroll pull-down → scale the hero up.
    const scale = scrollY.value < 0 ? 1 + Math.abs(scrollY.value) / 200 : 1;
    return { transform: [{ scale }] };
  });

  return (
    <Animated.ScrollView onScroll={scrollHandler} scrollEventThrottle={16}>
      <Animated.Image style={headerStyle} /* ... */ />
      {/* content */}
    </Animated.ScrollView>
  );
  ```

---

## 3. Anti-Patterns to Avoid

- **Heavy Grid Borders**: Never place thick, grey borders or double lines around image list items. Use margin-less tile offsets or soft shadow separations.
- **Static Loading Boxes**: Avoid plain grey or white boxes during media load states. Use glowing pulse skeletons tinted with the anticipated asset's color spectrum.
- **Clipped Text Overlays**: Never place raw white text directly over light-colored photo backgrounds. Always wrap text overlays in soft, vertical linear gradient scrims (flowing from transparent to solid black `rgba(0,0,0,0.6)` at the bottom).
- **Stiff, Linear Sheet Dismissals**: Full-screen players must drag down smoothly, snapping to a mini-player or dismissing entirely with bouncy, elastic spring physics. Never close with sudden, linear sliding timings.
