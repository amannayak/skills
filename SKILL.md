---
name: expo-mobile-ui
description: Use when the user wants to design, redesign, shape, critique, audit, polish, clarify, distill, harden, optimize, adapt, animate, colorize, extract, or otherwise improve an iOS or Android app built with Expo or React Native. Covers mobile screens, navigation headers, tab bars, sheet/modal flows, onboarding, gestural interactions, haptic design, safe area layout, mobile typography, and high-performance lists. Handles platform-aware visual consistency (Apple Human Interface Guidelines vs Material Design 3), mobile-first spacing (8-point grid), device-appropriate color contrast, fluid micro-interactions, haptic tactile response mapping, and emotional design (Peak-End rule). Also use when the user wants to audit UX usability, map user journeys, apply cognitive design laws (Fitts's, Hick's, Miller's), extract and convert a Figma design or screenshot into Expo-native styles, generate and enforce design system tokens, or apply industry-specific design conventions (fintech, wellness, crypto, education, fitness, media, productivity). Even if the user just says "design this screen" or "make this flow feel premium," use this skill.
version: 1.0.0
user-invocable: true
argument-hint: "[init|teach|craft|shape|polish|gesture|haptic|nav|safe-zone|performance|delight|platform|ux|figma|industry|tokens] [target]"
---

# Expo Mobile UI & UX Design Skill

Designs, refines, and implements production-grade, highly tactile mobile app interfaces for both iOS and Android simultaneously inside a single Expo/React Native codebase.

## Setup & Handshake

You MUST perform these setup steps on activation before writing code:

1. **Verify Project Context**: Look for `MOBILE.md` and `DESIGN.md` in the project root.
   - **If `MOBILE.md` is missing**: Stop immediately and run `/expo-mobile-ui init` (or the alias `teach`). Every other command relies on this context.
2. **Read Current Design Tokens**: Read `DESIGN.md` (if present) to align with existing colors, font stacks, and custom component sets.
3. **Load Register & Platform Context**: Identify the app's target register (`consumer`, `transactional`, or `media` from `MOBILE.md`) and load the matching register reference file from `reference/` to guide your design choices.
4. **Identify Platform Assumptions**: Expo builds for both iOS and Android from one codebase. Always design with platform adaptive branches (`process.env.EXPO_OS` or `Platform.select`) in mind. Load `reference/platform.md` when platform-specific UI controls or layout paradigms are needed.

---

## Shared Mobile Design Laws

These laws are non-negotiable. They override web design defaults and must be applied to all mobile screens:

### 1. Spacing & Layout (Flexbox Only)
- **Flexbox Rules Only**: React Native layout is Flexbox-only. Never write web CSS grid rules or generic web positioning hacks. Use `flex-row`, `items-center`, `justify-between`, etc.
- **The 8-Point Grid**: All spacing, sizes, margins, padding, and heights must be strictly divisible by **8 or 4** (4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 128, 160).
- **The Thumb Zone**: Place primary actionable elements (primary CTA buttons, pull handles, bottom tabs) in the bottom 40% of the screen. Keep secondary or destructive actions outside of the easy-reach zone to prevent fatigue or accidental triggers.
- **Touch Targets**: All pressable or actionable elements (buttons, icons, text links) MUST have a minimum tap target of **44×44pt** on iOS and **48×48dp** on Android. Add padding (`p-4` or similar) to thin components to increase hitboxes.

### 2. Native Typography
- **Scale Restraint**: Limit each screen to a maximum of **4 distinct font sizes** and **2 font weights** (e.g., Medium and Bold/Semibold).
- **Default System Fonts**: Use default platform system fonts (`SF Pro` on iOS, `Roboto` on Android) unless a custom brand font is loaded via `expo-font` with `useFonts()`.
- **Numeric Tabular Alignment**: Always apply `fontVariant: ['tabular-nums']` (or `font-mono` via NativeWind) for all running numeric lists, prices, metrics, or timers to prevent layout-jitter.
- **Line Heights**: Enforce exact line heights to prevent text truncation bugs: body text line-height = `fontSize * 1.4`; heading line-height = `fontSize * 1.15`.

### 3. Tactile Color & Shadows
- **60/30/10 Rule**: 60% neutral background (system background), 30% chrome/container surfaces, 10% high-contrast brand accent.
- **Semantic Platform Colors**: Prefer `PlatformColor` for standard elements (e.g., `systemBackground` / `label` on iOS; `@android:color/background_light` on Android) so that dark mode matches system behaviors effortlessly.
- **Modern Shadow CSS**: Always use the modern `boxShadow` prop (supported by current Expo SDKs as outlined in `building-native-ui`). Never use legacy platform-split properties (`shadowOffset`/`shadowOpacity` or `elevation` alone) unless compiling for older SDK versions.
- **Color Contrast**: Enforce WCAG 2.2 AA standards: body text ≥ 4.5:1 against container fill; headers ≥ 3:1.

### 4. Safe Areas & Insets
- **Dynamic Inset Wrapping**: Wrap all screen roots or floating elements with safe area hooks from `react-native-safe-area-context` (`useSafeAreaInsets()`).
- **Scroll Integration**: For scrolling screens, use `<ScrollView contentContainerStyle={{ paddingBottom: insets.bottom + 16 }}` instead of putting a fixed safe area at the bottom of the layout, so content scrolls edge-to-edge behind the Home Indicator.

---

## Absolute Mobile Bans

If you are about to write any of these, stop and refactor immediately:
1. **Web CSS Grid Layouts**: No `display: grid` or `grid-cols-*`. React Native layout is strictly Flexbox.
2. **Legacy Shadow Props**: Do not write split `shadowColor`, `shadowOffset`, `shadowOpacity`, `shadowRadius` (iOS) alongside `elevation` (Android). Use the single `boxShadow` prop for clean, unified styling.
3. **Hardcoded Device Insets**: Never write fixed pixel top/bottom padding (e.g., `pt-11` or `pb-8` to clear the notch). Device heights vary dynamically (Notch, Dynamic Island, Android hole-punch, Home Indicator) — always bind to `useSafeAreaInsets()`.
4. **General Scroll List Trashing**: Never render a dynamic list of arbitrary size using `<ScrollView>` + `.map()`. This completely bypasses virtualized viewport optimizations and trashes the JS thread. Always use `<FlatList>` or `@shopify/flash-list`'s `<FlashList>`.
5. **Inefficient JS-Thread Animations**: Never use standard `setInterval` or standard React `useState` to drive custom transition animations. This bottlenecks frame rendering. Always use Native-driven Reanimated 3 worklets (`useAnimatedStyle`, `useSharedValue`).

---

## Command Router

| Command | Category | Description | Reference |
|---|---|---|---|
| `init` / `teach` | Build | Setup mobile project context (MOBILE.md, DESIGN.md), registers, and platform rules | [reference/init.md](reference/init.md) |
| `craft [screen]` | Build | Shape, design, and implement a high-fidelity mobile screen/flow end-to-end | [reference/craft.md](reference/craft.md) |
| `shape [screen]` | Build | Plan UX/UI architecture, content taxonomy, and screen maps (no code) | [reference/shape.md](reference/shape.md) |
| `polish [target]` | Refine | Comprehensive visual, functional, and layout quality audit before shipping | [reference/polish.md](reference/polish.md) |
| `gesture [target]` | Enhance | Implement fluid native gestures, swipe actions, drawer drags, and bottom sheets | [reference/gesture.md](reference/gesture.md) |
| `haptic [target]` | Enhance | Map precise tactile haptic feedback triggers (success, impact, selection, warning) | [reference/haptic.md](reference/haptic.md) |
| `nav [target]` | Enhance | Architect navigation structures, headers, tab configurations, and deep-link maps | [reference/nav.md](reference/nav.md) |
| `safe-zone [target]`| Fix | Resolve notch, Dynamic Island, Home Indicator, landscape, and keyboard-avoiding bugs | [reference/safe-zone.md](reference/safe-zone.md) |
| `performance [target]`| Fix | Performance audits on lists (FlashList), re-render bottlenecks, and worklet execution | [reference/performance.md](reference/performance.md) |
| `delight [target]` | Enhance | Implement emotional design, Kahneman Peak-End moments, celebratory states, and animations | [reference/delight.md](reference/delight.md) |
| `platform [target]` | Fix | Optimize visual and functional adaptive parity between iOS HIG and Android MD3 | [reference/platform.md](reference/platform.md) |
| `ux [target]` | Design | UX usability audits, cognitive psychology, user journey mapping, accessibility | [reference/ux.md](reference/ux.md) |
| `figma [target]` | Design | Extract and convert Figma layouts or reference mockups into Expo-native code | [reference/figma.md](reference/figma.md) |
| `industry [type]` | Design | Apply industry-specific design guidelines and emotional business conventions | [reference/industry.md](reference/industry.md) |
| `tokens` | Design | Generate and enforce semantic mobile design tokens and WCAG 2.2 AA rules | [reference/tokens.md](reference/tokens.md) |

### Routing & Delegation Rules

1. **No Argument Provided**: Run a quick codebase analysis, check for signals of open/changed files, and suggest the **2-3 highest-value next steps** with precise command lines. Do not dump the entire command menu.
2. **First Word Matches Command**: Immediately load the corresponding file in `reference/<command>.md` and execute its instructions verbatim. Everything after the command name is treated as the target.
3. **Intent Maps to Command**: If the user asks for a feature that directly falls under a command (e.g., "This list is lagging" -> `/performance`, "Make the success screen feel rewarding" -> `/delight`), load that command's reference and run it as if called.
4. **No Clear Match**: Apply the general Shared Mobile Design Laws and register-specific guidelines (`reference/<register>.md`) directly to the user's task.

---

## Infrastructure Delegation

This skill focuses exclusively on **Visual Design, Tactile Polish, Layout Hierarchy, and Emotional UX**. For lower-level infrastructural and structural concerns, explicitly refer to and delegate to the existing `expo-skills` tools:

- For initial tailwind setup and CSS variable configuration, check: **`expo-tailwind-setup`**
- For physical folder layouts, route configuration files, and standard Expo Router syntax, check: **`building-native-ui`**
- For native module development or SwiftUI/Jetpack Compose wrappers, check: **`expo-ui-swift-ui`** or **`expo-ui-jetpack-compose`**
- For build pipeline, app credentials, and OTA deployment, check: **`expo-deployment`** or **`expo-cicd-workflows`**

Do not re-invent or re-implement configuration steps covered by these skills. Interlink with them cleanly.
