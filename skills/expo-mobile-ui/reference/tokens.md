# Design Tokens & Accessibility Flow

How to generate, maintain, and enforce semantic mobile design tokens, handle system-level dark mode adaptation, and enforce strict accessibility standards inside Expo apps.

---

## 1. Creating the Design Tokens File

Mobile apps must never use hardcoded, inline style values or unstructured CSS variables. Always compile design parameters into a single, importable TypeScript token file (e.g., `src/theme/tokens.ts`).

### The Standard Semantic Token Structure:
```ts
// src/theme/tokens.ts
import { PlatformColor } from 'react-native';

export const tokens = {
  colors: {
    // 60% Base backdrops
    background: {
      light: '#FFFFFF',
      dark: '#121212', // OLED friendly near-black, never pure #000
    },
    // 30% Chrome containers
    surface: {
      light: '#F8F9FA',
      dark: '#1E1E1E',
      elevatedLight: '#FFFFFF',
      elevatedDark: '#2D2D2D',
    },
    // 10% Brand Accents — EVERY token below is a {light, dark} pair (see §2).
    // Dark values are lightened + slightly desaturated so they read on #121212.
    brand: {
      // For fills, icons, and LARGE text only. #3B82F6 ≈ 3.7:1 on white →
      // passes WCAG AA for large text (≥18pt bold / ≥24pt) and UI components, but
      // FAILS the 4.5:1 body-text bar. Never use `accent` for <18pt text.
      accent: { light: '#3B82F6', dark: '#60A5FA' },
      // Use THIS for accent-colored body text. #2563EB ≈ 5.1:1 on white (AA body).
      accentText: { light: '#2563EB', dark: '#93C5FD' },
      accentMuted: { light: '#93C5FD', dark: '#1E3A8A' },
      accentContrast: '#FFFFFF', // text/icon color placed ON the accent fill
    },
    // Semantic States — fills are AA-large only on white; for inline semantic TEXT
    // use the *Text variants, which clear 4.5:1 on their respective backgrounds.
    semantic: {
      success: { light: '#10B981', dark: '#34D399' },
      successText: { light: '#047857', dark: '#6EE7B7' },
      warning: { light: '#F59E0B', dark: '#FBBF24' },
      warningText: { light: '#B45309', dark: '#FCD34D' },
      error: { light: '#EF4444', dark: '#F87171' },
      errorText: { light: '#B91C1C', dark: '#FCA5A5' },
      info: { light: '#3B82F6', dark: '#60A5FA' },
      infoText: { light: '#2563EB', dark: '#93C5FD' },
    },
  },
  
  typography: {
    sizes: {
      title: 28,      // H1 headings
      heading: 20,    // H2 subheadings
      body: 16,       // Body copy
      caption: 12,    // Subtitles & Micro-copy
    },
    // This is the available PALETTE, not a per-screen budget. The Shared Mobile
    // Design Laws cap any single screen at MAX 2 of these weights (e.g. medium + bold).
    weights: {
      regular: '400' as const,
      medium: '500' as const,
      semibold: '600' as const,
      bold: '700' as const,
    },
    // NOTE: React Native `lineHeight` is an ABSOLUTE pixel value, not a CSS-style
    // unitless multiplier. Store ratios here, then multiply by the font size at the
    // call site: lineHeight: sizes.body * lineHeightRatios.body  (e.g. 16 * 1.4 = 22.4).
    // Never pass these ratios directly to a `lineHeight` style prop.
    lineHeightRatios: {
      heading: 1.15,
      body: 1.4,
    },
  },

  radius: {
    sm: 8,            // Small widgets
    md: 12,           // Standard buttons / Cards
    lg: 16,           // Carousel items (rounded-2xl)
    xl: 24,           // Main sheets (rounded-3xl)
    full: 9999,
  },

  spacing: {
    xs: 4,
    sm: 8,
    md: 16,
    lg: 24,
    xl: 32,
    xxl: 48,
  },
};
```

### Resolving the Active Theme

Every `{light, dark}` pair above is inert until something selects the active value. Resolve it once with React Native's `useColorScheme()` and expose a typed hook so screens never read raw hex or branch on the scheme themselves:

```ts
// src/theme/useTheme.ts
import { useColorScheme } from 'react-native';
import { tokens } from './tokens';

export function useTheme() {
  const scheme = useColorScheme() ?? 'light'; // 'light' | 'dark'
  const { colors, typography, radius, spacing } = tokens;
  return {
    scheme,
    bg: colors.background[scheme],
    surface: colors.surface[scheme],
    accent: colors.brand.accent[scheme],
    accentText: colors.brand.accentText[scheme],
    typography,
    radius,
    spacing,
  };
}
```

Components consume `const t = useTheme()` and read `t.surface`, `t.accentText`, etc. — the light/dark switch then happens automatically and consistently across the whole app when the system appearance changes.

---

## 2. System-Level Dark Mode Rules

Do not perform raw color-inversion for dark mode. Colors must be tuned deliberately:
1. **Never Pure Black backgrounds**: Pure black causes visual ghosting on OLED panels during scroll. Default to near-black (`#121212` or `#0A0A0A`).
2. **Reduce Saturation**: Bright primary colors (like rich golds or neon greens) glow too harshly on dark backdrops, causing eye fatigue. Mute the saturation of accents by **15-20%** for the dark token pair.
3. **Tonal Elevation overlays**: Represent visual depth in dark mode using light-emitting overlays instead of physical shadows (which are invisible on black):
  - *Base Surface*: `#121212` (Elevation 0)
  - *Card Container*: `#1E1E1E` (Elevation 1)
  - *Floating Dialog*: `#2D2D2D` (Elevation 2)

---

## 3. iOS and Android System-Native Colors

Where appropriate, use `PlatformColor` to access system-managed semantic colors. This lets the operating system handle light/dark transitions and high-contrast settings natively:

```tsx
import { Platform, PlatformColor, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    backgroundColor: Platform.select({
      ios: PlatformColor('systemBackground'),
      android: PlatformColor('@android:color/background_light'),
    }),
  },
  text: {
    color: Platform.select({
      ios: PlatformColor('label'),
      android: PlatformColor('@android:color/primary_text_light'),
    }),
  },
});
```

---

## 4. Accessibility (a11y) Verification

Verify and enforce these strict accessibility standards on every layout audit:

### Double-Platform Contrast Audit:
- Ensure all text-to-background contrast configurations pass WCAG 2.2 AA.
- Use contrast checking tools or mathematical formulas. Never ship body text utilizing low-contrast light grey on white.

### Screen Reader Semantic Tags:
- Ensure every image, illustration, and icon-only touchable is backed by semantic attributes:
  ```tsx
  <TouchableOpacity
    accessibilityLabel="Close sheet"
    accessibilityRole="button"
    onPress={onClose}
  >
    <LucideIcon name="x" />
  </TouchableOpacity>
  ```

### Respect Font Scaling:
- Never block font scaling with `allowFontScaling={false}` on body text or form inputs.
- For layout-critical numerics (countdown timers, tabular ledgers, fixed-width badges) where unbounded scaling would break the layout, **cap** rather than disable: `maxFontSizeMultiplier={1.3}`. This still honors the user's Dynamic Type / font-size preference up to a safe ceiling instead of ignoring it entirely.
- Ensure parent containers utilize flexible spacing (`flex-shrink` or auto wrapping) so that if a user scales text up to 200%, the labels wrap correctly and do not clip inside fixed borders.

### Motion Checking:
- Query system-level Reduced Motion requests before animating transitions:
  ```tsx
  import { AccessibilityInfo } from 'react-native';
  
  AccessibilityInfo.isReduceMotionEnabled().then((isEnabled) => {
    // If enabled, replace spring scale transitions with instant fades
  });
  ```
