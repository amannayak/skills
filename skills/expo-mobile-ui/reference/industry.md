# Industry Conventions & Emotional Design Flow

How to leverage industry-specific visual vocabularies, design emotional feedback loops, apply Daniel Kahneman's Peak-End rule, and coordinate the 4-Phase Product Design process.

---

## 1. The Separation Rule

Never mix visual research (UI) and structural research (UX) during the discovery phase.
- **UI Lens (Inspiration)**: Look up color harmonies, typography weights, tactile micro-transitions, backgrounds, and aesthetic trends on visual platforms (Mobbin, Dribbble, Twitter).
- **UX Lens (Structure)**: Map the wireframes, information architecture, click costs, and navigation hierarchies.
- **Action**: Research these two lenses in completely separate passes to prevent a pretty visual style from masking a flawed, high-friction user flow.

---

## 2. 9 Industry Design Languages & Lessons

Align screen layouts with the established visual conventions users expect:

### AI / Tech Products
- **Visuals**: Soft background gradients, glowing mesh details, floating sheets with thin light borders, and subtle opacity transitions communicating "intelligence."
- **Focus**: Calm, futuristic accents (vibrant blues, ethereal violets) over dark themes.

### Crypto / Web3
- **Visuals**: Neon accent strikes, geometric grids, deep dark-mode backgrounds.
- **Lesson (Phantom)**: **Visual polish IS the trust signal.** In high-stakes decentralized environments, perfect spacing, premium transitions, and tactical animations are what build security and confidence.

### Finance / Banking
- **Visuals**: Blue-dominant palettes (stability), conservative typography, generous whitespace, clear data rows.
- **Lesson (Revolut)**: **Tactile interactions build premium value.** Draggable charts, elastic currency flips, and immediate haptic ticks elevate sterile financial transactions into engaging physical sensations.

### Health / Wellness
- **Visuals**: Calm, desaturated base tones (creams, warm off-whites, soft sages), organic shapes, and friendly illustrations.
- **Lesson (Ahead)**: Guide users kindly. Do not overwhelm with data inputs. Onboard them through structured choices and celebrate their goals.

### Sleep / Meditation
- **Visuals**: Purples and midnight blues, low-contrast text scales, minimal headers, and slow cross-fading transitions.
- **Focus**: Reduce cognitive load and sensory excitement completely.

### Education / Learning
- **Visuals**: High-saturation, playful color blocks, chunky border lines, and custom character icons.
- **Lesson (Duolingo)**: **Character animations and emotional feedback loops drive engagement.** Tying user progress and mistakes to playful visual rewards and haptic clicks maintains long-term habits.

### Fitness
- **Visuals**: High-energy, high-saturation colors (lime greens, warm oranges), heavy athletic typography, and progressive visual status timelines.
- **Focus**: Fuel user momentum and celebrate routine streaks.

### Productivity
- **Visuals**: Ultra-clean, modular, structured columns, thin dividers, monospace statistics, and keyboard-friendly action shortcuts.
- **Focus**: Reduce click costs and clear extraneous visual noise to maximize speed.

### E-commerce / Food
- **Visuals**: High-quality, color-rich photography, prominent floating checkout CTAs, and immediate trust indicators (delivery times, progress maps).

---

## 3. Emotional UX Principles

### Kahneman's Peak-End Rule:
The human brain compresses memories. A user will judge an app journey based on the **Peak** (most intense moment) and the **End** (final screen state).
- *Implementation*: Design one memorable "celebration" event per flow (e.g., confirmatory spring badge bounce, confetti explosion, distinct dual haptic clicks) and craft a warm, personalized closing card (see `reference/delight.md`).

### Spotify's 3 Playbook Principles:
1. **The Trojan Horse**: Wrap complex technology in simple, familiar mobile controls. Do not expose complicated dials or database structures directly.
2. **The Vanity Mirror**: Frame feedback as personal self-expression, not cold statistics. Use copy like: *"You do your best work on Wednesday evenings"* rather than *"14 tasks completed."*
3. **The Comfort Trap**: High design consistency (buttons compressing at the same spring rate, identical tab structures) builds deep user habits, raising switching costs naturally.

---

## 4. The 4-Phase Client/Product Design Process

Follow this sequence for all major mobile app builds:

### Phase 1: Discovery & Mood Boards
Gather visual references and compile distinct visual directions (e.g., minimalist-clinical vs playful-approachable). Select the target register.

### Phase 2: UI Direction Mockups
Design 2 or 3 high-fidelity visual cards or screens. Confirm color palettes, typographic fonts, and spacing ratios before mapping complex flows.

### Phase 3: UX Structure & Wireframes
Map the step-by-step screen flows using grayscale wireframes. Minimize clicks and position main CTAs in the bottom thumb zone.

### Phase 4: Combined Implementation
Apply the approved visual direction directly to the wireframed layouts. Polish all edge cases (empty states, errors, skeleton loaders, and haptics).
