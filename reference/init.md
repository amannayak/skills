# Init & Teach Flow

The bootstrap command for an Expo mobile project. One comprehensive codebase crawl feeds everything this command writes:

- **MOBILE.md**: Root-level strategic document outlining the app's register (consumer/transactional/media), primary platform targets (iOS/Android/both), core target users, app purpose, brand/interface personality, anti-references, and key strategic design principles.
- **DESIGN.md**: Root-level visual system document containing colors, type scales, haptic mapping guides, gesture strategies, and custom component tokens.

Every other command in this skill reads `MOBILE.md` and `DESIGN.md` before performing design or implementation work.

---

## Step 1: Check Existing State

Scan the project root for existing context files. Look for `MOBILE.md` and `DESIGN.md` (case-insensitive).
- **If neither file exists**: Proceed with Step 2 (Codebase Crawl) and Step 3 (Structured Interview) to build `MOBILE.md` first.
- **If `MOBILE.md` exists but `DESIGN.md` is missing**: Skip to Step 5 (Document Design) and offer to extract existing styles.
- **If both exist**: Proactively ask the user which file they would like to refresh. Do not overwrite existing files without explicit user confirmation.

If `init` was triggered as a setup blocker by another command (e.g., running `/expo-mobile-ui craft screen` on an un-bootstrapped repository), complete `init` first, then resume the original command.

---

## Step 2: Codebase Crawl

Thoroughly scan the project to discover the existing technical structure. This feeds `MOBILE.md`, `DESIGN.md`, and references `expo-skills` correctly:

1. **Configurations (`app.json` / `app.config.ts` / `app.config.js`)**: Identify the Expo SDK version, target platforms (ios, android, or both), bundle identifiers, and standard screen orientations.
2. **Dependencies (`package.json`)**: Check for existing packages that affect UI/UX capabilities:
   - Styling: `nativewind`, `tailwindcss`, `@gorhom/bottom-sheet`
   - Animations: `react-native-reanimated`, `moti`
   - Lists: `@shopify/flash-list`
   - Gestures: `react-native-gesture-handler`
   - Feedback: `expo-haptics`
   - Native modules: `@expo/ui`, `expo-glass-effect`
3. **Register Hypothesis**: Look for signals of the app's nature:
   - *Consumer/Utility*: Health, sleep, fitness, educational elements, streak tracking, playful layouts.
   - *Transactional/Productivity*: Data tables, cards with dense numbers, form screens, banking/finance elements.
   - *Media/Social*: Scroll-driven grids, video/image frames, music players, full-bleed media layouts.

---

## Step 3: Structured Interview (Multi-Round)

Do not generate `MOBILE.md` using only inferred data or a single-sentence task description. Run a structured interview with **2-3 questions per round**, then wait for the user's responses.

### Round 1: Core Strategy & Platform Parity
1. **Confirm Register**: Propose your register hypothesis (e.g., *"This looks like a Transactional/Productivity app. Is that correct, or should we treat it as Consumer/Utility or Media/Social?"*) and explain why.
2. **Platform Parity**: Since we build for both iOS and Android simultaneously, do we want to stick to a highly unified shared visual style (like Airbnb), or do we want the app to adapt dynamically to feel 100% native on each platform (using SF Symbols/Liquid Glass on iOS and Material You/M3 ripple on Android)?
3. **Target Users & Context**: Who are the primary users, and under what conditions do they use this app? (e.g., on the move with one hand, in low-light environments, highly focused at a desk, etc.)

### Round 2: Brand Personality & Anti-References
1. **Brand Personality**: Describe the desired interface vibe in 3 distinct adjectives (e.g., "tactile, trustworthy, hyper-precise" vs "playful, encouraging, soft").
2. **Visual Reference Analogs**: Name 1-2 existing apps whose interface details (typography, motion, or overall feel) represent the target benchmark for this project.
3. **Anti-References**: What should this app explicitly **not** look like? (e.g., "not a cluttered web wrapper," "no sterile black-and-white forms").
4. **Accessibility Needs**: Are there specific user requirements we need to accommodate? (e.g., large font scaling, high contrast constraints, reduced motion support).

---

## Step 4: Write MOBILE.md

Once the user approves the interview details, write `MOBILE.md` to the project root:

```markdown
# Mobile Product Strategy

## Register
[consumer | transactional | media]

## Platforms
[ios | android | both]

## Target Users & Context
[Who they are, where they use the app, and how that shapes interaction costs]

## App Purpose & Goals
[What the app does, why it exists, and what a successful user journey looks like]

## Brand & Interface Personality
- **Tone & Voice**: [Adjectives representing the core vibe]
- **Visual References**: [App names and the specific UI/UX details to match]
- **Anti-References**: [Patterns, styles, and bad habits to avoid]

## Strategic UX Principles
- **Principle 1**: [Concrete strategic focus, e.g., "Instant Gratification: Reduce form field interactions to a single tap."]
- **Principle 2**: [e.g., "Tactile Confidence: Back every critical action with subtle, distinctive haptic feedback."]
- **Principle 3**: [e.g., "Contextual Density: Prioritize rapid scannability for returning transactional power-users."]

## Accessibility & Inclusion
[WCAG 2.2 target level, dark-mode-first needs, reduced motion handling, safe area layouts]
```

---

## Step 5: Document Design (DESIGN.md)

Offer the user to generate `DESIGN.md`. If they agree:
- **If codebase has existing UI code**: Perform a scan of styles, Tailwind configurations, or custom component layouts, then extract them into a structural design tokens layout.
- **If the project is empty / pre-implementation**: Seed a modern mobile-centric design token system based on the confirmed brand direction.

The file must follow a clean, accessible layout:

```markdown
# Design System Tokens

## Color Palette
- **60% Base**: [Hex codes / semantic system colors for Dark/Light backgrounds]
- **30% Chrome**: [Container, card, and layout border colors]
- **10% Brand Accent**: [High-fidelity focus/CTA color]

## Typography (Max 4-Step Scale)
- **Title (H1)**: `text-2xl font-bold` / SF Pro Rounded
- **Subtitle (H2)**: `text-lg font-semibold`
- **Body**: `text-base font-normal`
- **Caption/Numeric**: `text-xs font-mono tracking-wider`

## Tactile Micro-Interactions
- **Standard Pressed State**: scale down to `0.97` with `300ms` spring physics via Reanimated.
- **Primary Haptic**: `Haptics.ImpactFeedbackStyle.Medium` on tap release.

## Layout & Border Radii
- All spacing divisible by 8 or 4.
- Standard Rounded Corners: `rounded-2xl` / `rounded-3xl` with `{ borderCurve: 'continuous' }` for iOS.
```

---

## Step 6: Recommend Starting Points

Wrap up by reporting the exact outcomes concisely, summarizing the core strategic principles, and suggesting the next **2-3 commands** to type based on what the crawl found.

**Examples based on project state**:
- For empty projects: `/expo-mobile-ui craft <screen-name>` or `/expo-mobile-ui shape <screen-name>`
- For existing projects with screen components: `/expo-mobile-ui polish <component-path>` or `/expo-mobile-ui performance <list-component>`

Always specify the exact syntax for the user to copy-paste.
