# Polish Flow

The pre-flight quality and visual craft audit. This command is executed before shipping any screen, component, or flow to production. 

A layout is never just "working"; it must be polished to a high-fidelity standard. Run through these **10 systematic dimensions** to evaluate and refine the implementation:

---

## The 10 Dimensions of Mobile Polish

### 1. Spacing & Spacing Consistencies (Visual Rhythm)
- **Grid Compliance**: Verify that all `gap`, `p-*`, `m-*`, `h-*`, and `w-*` values are strictly divisible by 8 or 4.
- **Relational Gaps**: Ensure items that are related (e.g., a heading and its body caption) are closer together (`gap-1` (4px) or `gap-2` (8px)) than unrelated groups (`gap-6` (24px) or `gap-8` (32px)).

### 2. Typographic Polish & Scale Hierarchy
- **Scaling Limit**: Ensure no single screen contains more than 4 distinct font sizes. If it does, compress them.
- **Header Weights**: Do not bold everything. Create hierarchy using color opacity (e.g., use an 80% opacity text color like `text-neutral-500` for subtitles) instead of massive weight leaps.
- **Tabular Mono Numbers**: Ensure all dynamic timers, statistics, and financial numbers are backed by monospace alignment to prevent characters from shifting layout positions.

### 3. Tap Targets & Accessibility
- **Target Sizes**: Verify that all interactable elements have a tap target of at least **44×44pt** (iOS) / **48×48dp** (Android).
- **Target Spacing**: Ensure tap targets are not crowded together. Space adjacent button elements by at least 8px to prevent accidental taps.
- **HitSlop Boundaries**: For small controls (close icons, chevron buttons), add `hitSlop` overrides:
  ```tsx
  <TouchableOpacity hitSlop={{ top: 16, bottom: 16, left: 16, right: 16 }}>
  ```

### 4. Interactive Feel (Pressed States)
- **Compression Feedback**: Ensure all pressable list items or cards scale down slightly on press (e.g., `scale: 0.98`) using Reanimated spring physics.
- **Ripple Effect (Android)**: Ensure all pressables on Android have native ripple backgrounds (`android_ripple={{ color: 'rgba(0,0,0,0.1)' }}`).

### 5. Tactile Feedback (Haptic Responses)
- **Cues Present**: Verify that all primary actions (button releases, toggling switches, form submission) trigger a medium haptic impact or success notification.
- **Haptic Restraint**: Do not trigger haptics on passive events (like simple item selections or navigation changes) to avoid overwhelming the user.

### 6. Safe-Zone Bounds
- **Top Insets**: Verify that navigation bars, close buttons, or headers do not intersect with notch boundaries or Dynamic Islands.
- **Bottom Insets**: Ensure buttons or tabs do not overlap with the iOS Home Indicator. Ensure they sit safely above `insets.bottom` while backgrounds flow edge-to-edge.

### 7. Completeness of App States
- **Empty States**: Verify that empty feeds do not just show blank screens. Ensure there is a friendly description and a primary action.
- **Skeleton Pulse**: Verify that loading states use pulse animations matching the aspect ratios of the cards they represent.
- **Error Cues**: Verify that inline error states are graceful, non-blocking, and offer an immediate retry path.

### 8. Navigation & Transition Cleanliness
- **Header Parity**: Verify that headers match system defaults. On iOS, large titles should collapse smoothly to inline headers as the list scrolls.
- **Dismiss Gestures**: For sheet modals, verify that swipe-down-to-dismiss is enabled and behaves naturally.

### 9. Component Consistency
- **Design System Alignment**: Double-check that colors, text classes, and borders match the patterns in `DESIGN.md`. 
- **No Web Monocultures**: Ensure we are not leaking web layout techniques (such as CSS Grid or absolute offsets mapped to web viewports) into the native environment.

### 10. Performance & Framerates
- **List Virtualization**: Ensure long lists are implemented via `<FlashList>` with a correct `estimatedItemSize` instead of mapping array components.
- **Render Auditing**: Check for unnecessary component re-renders on active input states. Memoize components where needed.

---

## Execution: The Polish Pass

When running `/expo-mobile-ui polish [target]`:
1. Read the target component file(s).
2. Evaluate them line-by-line against the 10 Dimensions.
3. Identify exactly which rules are violated.
4. **Draft and apply the exact code refactors** to resolve the issues.
5. Provide a summary checklist of what was polished.
