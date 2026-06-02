# Figma-to-Expo Conversion Flow

How to extract visual layouts, spacing vectors, typography metrics, and component states from Figma designs or reference screenshots and translate them into clean, high-performance Expo code.

---

## 1. The 4-Phase Conversion Workflow

### Phase 1: Context & Metadata Extraction
- **Cache Local Designs**: Store Figma metadata, screenshots, and visual components in a `.figma-mcp/` workspace directory:
  ```
  .figma-mcp/
  ├── info.json              <-- Frame dimensions, layer tree, and constraints
  ├── design-context.tsx     <-- Initial token assumptions and themes
  └── screenshot.png         <-- Reference visual layout image
  ```
- **Analyze Before Code**: Read all component layers and understand constraints before writing a single line of React Native style code.

### Phase 2: Design Token Translation
Map Figma values directly to native Expo equivalent styles (see Section 2). Do not hardcode arbitrary pixel coordinates.

### Phase 3: Component Generation
Build highly structural React Native UI layouts utilizing NativeWind utility classes (Tailwind v4) or clean, semantic StyleSheet objects.

### Phase 4: Verification Loop
Compare the rendered Expo layout directly against the `.figma-mcp/screenshot.png`. Audit using the `/expo-mobile-ui polish` command.

---

## 2. Figma to Expo Token Mapping Matrix

| Figma Layer Property | Web CSS Syntax (Web-only) | Expo / NativeWind Equivalent |
|---|---|---|
| **Border Radius** | `border-radius: 16px` | `rounded-2xl` / `borderRadius: 16` + `{ borderCurve: 'continuous' }` (iOS only) |
| **Material Effect** | `backdrop-filter: blur(10px)` | `<BlurView intensity={20} />` from `expo-blur` |
| **Main Typography** | `font-size: 16px; line-height: 24px` | `text-base` / `fontSize: 16, lineHeight: 22` |
| **Drop Shadow** | `box-shadow: 0px 4px 8px ...` | `boxShadow` prop (modern Expo/RN SDK) |
| **External Fonts** | Google CDN link | Load via `expo-font` with `useFonts()` |
| **Frame Overflows**| `overflow: hidden` | Avoid on ScrollViews. Use only on static containers. |
| **Absolute Layers**| `position: absolute; top: X; left: Y` | Verify parent is relative; calculate absolute coordinates relative to the nearest parent frame. |

---

## 3. Critical Layout Conversion Laws

- **Rule 3.1: Never Hardcode Frame Dimensions**: Screen heights are dynamic. Never translate a full 390×844px iPhone canvas into hardcoded width/height properties in StyleSheet.
  - *Expo Solution*: Use percentage strings (`w-full`), flex constraints (`flex-1`), or use the `useWindowDimensions()` hook for responsive calculations.
- **Rule 3.2: Eliminate Fixed Text Container Heights**: Figma text layers frequently export with static height boxes. Translating these to React Native will clip characters (especially tails on letters g, j, q, y) on Android due to rendering engine differences.
  - *Expo Solution*: Omit `height` from text style containers completely. Use `flexShrink: 1` and let line heights determine vertical bounds naturally.
- **Rule 3.3: Flatten Relative Frame Offsets**: Figma frames within frames often yield nested absolute offsets.
  - *Expo Solution*: Do not add cumulative coordinate offsets. Flatten absolute elements so they align directly with their parent container, setting correct `top`/`left`/`right`/`bottom` bounds.
- **Rule 3.4: Align Font Weight Ranges**: Figma font weights do not always map 1:1 with React Native font styling.
  - *Figma weight → React Native equivalent*:
    - Thin / Light → `'300'`
    - Regular / Normal → `'400'`
    - Medium → `'500'` (often omitted in web, but critical for mobile hierarchy)
    - SemiBold / Demibold → `'600'`
    - Bold → `'700'`

---

## 4. Visual Extraction from Screenshots

When the user provides a reference mockup or screenshot (instead of Figma metadata):
1. **Analyze Swatch Colors**: Extract the hexadecimal color values. Identify the 60% base backdrop, 30% container chrome, and 10% high-contrast brand accent.
2. **Decompose Typography**: Identify the display headings, body texts, and labels. Draft a modular type-scale constants schema.
3. **Isolate Margins**: Measure spacing grids. Align values to the nearest divisible number of 8 or 4 (e.g., a 15px gap becomes an 8-point compliant `gap-4` (16px)).
4. **Draft the Tokens File**: Output the extracted visual system as a `src/tokens.ts` TypeScript file (see `reference/tokens.md` for format).
