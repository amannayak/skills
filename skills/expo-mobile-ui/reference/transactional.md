# Transactional/Productivity App Register Flow

How to design and implement visual languages, typography scales, color schemes, motion rules, and information architectures for Transactional/Productivity apps (fintech, banking, crypto, developer tools, task managers, admin dashboards).

---

## Core Philosophy of Transactional/Productivity UI

Transactional apps succeed by conveying precision, safety, velocity, trust, and absolute control. The interface must feel highly organized, rapid, expert-grade, and crisp:
1. **Earned Familiarity**: Stick to predictable native navigation standards. Users of high-performance tools (like Revolut, Stripe, Linear, or Raycast) expect immediate responsiveness, predictable gesture sweeps, and zero decorative fluff.
2. **Dense but Structured Architecture**: Structure layouts to support dense data views without creating clutter. Enforce perfect visual alignment, clear tabular column headers, and cohesive grouping cards.
3. **Tactile Reassurance**: Every transactional commit (confirming a payment, archiving a task, executing a query) must feel physically completed using crisp, discrete haptic ticks and fast, linear-to-spring transitions (150ms to 250ms).

---

## 1. Visual Identity & Theme Guidelines

### Color Palette Strategy (Restrained/Precise):
- **Base Background**: High-contrast, clean neutral bases. Slate blacks, deep navy, or pure whites with ultra-thin, low-opacity container borders (`border-neutral-100`).
- **Brand Accent**: High-contrast, trustworthy colors (cool blue, royal purple, gold, or high-octane neon greens) representing security, status, and precise feedback.
- **Shadow System**: Use tiny, crisp, highly-controlled drop shadows to differentiate floating layers, cards, or sheets from the background.

### Spacing & Spacing Rhythm:
- **Compact Spacing**: Tighten margins to 16px (`p-4` or `gap-4`) or 12px (`p-3` or `gap-3`). Maximize the display space for information.
- **Tabular Alignment**: Use monospace font alignment (`fontVariant: ['tabular-nums']`) for all running ledgers, balance counts, and metric tables.
  ```tsx
  <View className="flex-row justify-between items-center p-4 bg-white rounded-xl border border-neutral-100">
    <Text className="text-neutral-500">Available Balance</Text>
    {/* Tabular numeric ensures digits align and do not jitter */}
    <Text className="text-xl font-bold font-mono text-neutral-800">$12,482.90</Text>
  </View>
  ```

---

## 2. Typical Interaction Patterns

- **Interactive Status Timelines**: Instead of static date lists, represent transaction processing, order tracking, or build sequences using dynamic, vertical linear visual timelines.
- **Biometric Handshake Prompts**: Secure high-stakes actions (money transfers, deleting records) behind a native TouchID/FaceID biometric check, immediately backed by a system-level success haptic notification.
- **Monospace Ledger Feeds**: Implement scrolling transaction logs using highly-scannable `<FlashList>` layouts with clean, color-coded status badges (green for credit, gray for debit).

---

## 3. Anti-Patterns to Avoid

- **Decorative Motion**: Never include slow, looping, or non-functional animations. Motion in a productivity tool must exist solely to convey state changes, taking under 200ms.
- **Modal Overuse**: Do not use modals as the default solution for minor configurations. Use inline expandable drawers, sliding context panels, or bottom sheets to keep the user inside their workflow.
- **Ambiguous Form Labels**: Never use cute, colloquial, or descriptive copy for high-stakes form actions. Button labels must state exactly what will happen on release: use *"Send $500.00"* instead of *"Go"* or *"Make it happen!"*.
- **Vague Empty States**: Avoid generic "Nothing here" empty states on ledgers or lists. Explain how to resolve the state and provide an immediate primary action: *"No accounts linked yet. Link a bank account to start transferring."*
