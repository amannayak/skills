# UX Usability & Cognitive Audit Flow

How to conduct mobile-first UX research, apply cognitive psychology laws, audit accessibility (WCAG 2.2 AA), map user journeys, and identify dark patterns inside Expo apps.

---

## 1. The Mobile UX Philosophy

Designing for mobile requires respecting the constraints of a small touch screen and a distracted user:
- **The 3-Filter Rule**: Before designing or auditing any screen, answer:
  1. *What is the user trying to accomplish?* (Singular primary goal).
  2. *How should this screen make them feel?* (Anxiety-free, confident, calm, or focused).
  3. *What is the one thing they should notice first?* (Clear visual hierarchy, avoiding the "AI slop" clutter).
- **Separate UX and UI Research**: Gather UX wireframe and flow inspiration separately from visual UI styling. Never mix the two phases.

---

## 2. Cognitive Laws of Mobile UX

Evaluate layouts against these verified psychological principles:

- **Fitts's Law**: The target acquisition time depends on target size and proximity.
  - *Audit*: Keep primary CTA buttons large (min 44pt iOS / 48dp Android) and located in the bottom 40% thumb zone.
- **Hick's Law**: Decision-making time increases with choices.
  - *Audit*: Limit action lists. Use accordion sections or progressive disclosure to hide secondary choices.
- **Miller's Law**: The average person can hold 7±2 items in working memory.
  - *Audit*: Group content chunks. Lists of selectable elements (e.g., country dropdowns or options) should be grouped into categorized clusters or broken into progressive steps.
- **Serial Position Effect**: Users remember the first and last items in a series.
  - *Audit*: Place critical actions at the far left and far right of bottom tab bars. Put high-contrast elements at the top or bottom of vertical feeds.
- **Zeigarnik Effect**: People remember uncompleted tasks better than completed ones.
  - *Audit*: Use profile completion percentages, streak markers, and saved-draft indicator badges to gently encourage users to return and finish workflows.
- **Peak-End Rule (Kahneman)**: Users judge an experience by its peak and ending moments. Refer to `reference/delight.md` for explicit implementation rules.

---

## 3. Mobile Accessibility (WCAG 2.2 AA)

Never build a mobile layout that excludes users with visual, motor, or cognitive impairments. Use Expo's native APIs:

- **Semantic Labeling**: Add descriptive, non-redundant accessibility tags to all interactive components:
  ```tsx
  <TouchableOpacity
    accessibilityLabel="Add to favorites"
    accessibilityHint="Toggles the favorite state of this item"
    accessibilityRole="button"
    accessibilityState={{ selected: isFav }}
    onPress={toggleFav}
  >
    <LucideIcon name={isFav ? "star-filled" : "star"} />
  </TouchableOpacity>
  ```
- **Text & Font Scaling**: Do not disable `allowFontScaling={true}` (React Native's default). Ensure text containers use flexbox wrapping (`flex-shrink`, flex wrapping) so scaled text does not overflow or clip inside container borders. For layout-critical numerics, **cap** scaling with `maxFontSizeMultiplier={1.3}` instead of disabling it (see `reference/tokens.md` §4).
- **Motion Restraint**: Before running intense animations or camera parallax effects, verify if the user has enabled system-level Reduced Motion:
  ```tsx
  import { AccessibilityInfo } from 'react-native';
  import { useSharedValue, useReducedMotion } from 'react-native-reanimated';
  
  // Inside Component:
  const isReduced = useReducedMotion(); // Hook respects system setting automatically
  ```
- **Transparency Restraint**: For any translucent/glass chrome (`expo-glass-effect`, `BlurView`), check `AccessibilityInfo.isReduceTransparencyEnabled()` and fall back to an opaque surface when enabled (see `reference/platform.md` §2).
- **High Contrast**: Verify a contrast ratio of at least **4.5:1** for body text and **3:1** for headings and functional icons.

---

## 4. Mobile User Journey Mapping

Use this layout to map user actions, feelings, and pain points across stages to isolate UX flaws:

```markdown
### Stage: [e.g., Onboarding Setup]
- **Doing**: User opens app, sees 3 options, selects one, enters email, receives verification code.
- **Thinking**: *"Is this email verification going to take long? I just want to see the workout plans."*
- **Feeling (Emotion Curve)**: Mild curiosity dropping into frustration during code entry.
- **Pain Point**: Mandatory email validation before exploring content increases initial abandonment.
- **Opportunity**: Enable a "Browse as Guest" option and defer verification until they save their first plan.
```

---

## 5. Dark Patterns & Ethical Auditing

Always reject deceptive design patterns. Ensure the app never contains:
1. **Confirmshaming**: Opt-out buttons with guilt-tripping labels (e.g., *"No, I don't want to get fit"* on premium close screens).
2. **Roach Motel**: Making it incredibly easy to sign up or subscribe but requiring a customer support call to cancel.
3. **Misdirection**: Using visual design to highlight a decoy action (e.g., a massive green "Yes, Subscribe" button alongside a microscopic gray close link).
4. **Faked Urgency**: Adding false countdown timers or fabricated stock indicators to force quick purchases.
