# Shape Flow

The design planning command. This command is dedicated strictly to mapping user flows, content taxonomies, and screen layouts **before any React Native code is written.**

---

## The Philosophy of Shaping

A great mobile layout is shaped by cognitive efficiency and physical reality. We shape screens around three fundamental laws of mobile psychology:
1. **Fitts's Law**: The time to acquire a target is a function of the distance to and size of the target. Mobile screens require putting critical targets (CTAs, tabs, main actions) right beneath the user's thumb (bottom-middle of the screen), making them large and easy to tap.
2. **Hick's Law**: The time it takes to make a decision increases with the number and complexity of choices. Mobile interfaces must present content progressively rather than throwing all actions onto a single screen.
3. **Serial Position Effect**: Users tend to remember the first and last items in a series. Navigation and content feeds must place critical items at the far left/right of tab bars, or top/bottom of landing rows.

---

## Step 1: Discovery & Taxonomy Interview

To shape a screen, run a short interview to understand the anatomy of the data:
- **Core Intent**: What is the singular goal a user wants to achieve on this screen?
- **Information Hierarchy**: What are the top 3 pieces of data the user needs to see first?
- **Action Hierarchy**: What is the primary action? What are the secondary/safety actions?
- **Anti-Goals**: What features or data elements are we **explicitly excluding** from this screen to prevent clutter?

---

## Step 2: Formulate the Design Brief

Synthesize the interview answers into a detailed **Design Brief**. Do not write code. Present this brief to the user for structural sign-off:

```markdown
# Design Brief: [Screen Name]

## 1. Interaction Model (UX Lens)
- **Primary Goal**: [Singular task, e.g., Transferring money to a contact]
- **Hick's Law Optimization**: [How choices are limited/staged, e.g., Hide banking routing numbers behind an "Advanced" drawer]
- **Thumb Zone Layout**: [What goes in the bottom 40%, e.g., Floating "Confirm Transfer" button at bottom of screen]

## 2. Screen Anatomy & Content Taxonomy
- **The Lede (Top 10% Visual Focus)**: [e.g., Dynamic currency counter with tabular monospace numbers]
- **Primary List (Middle 50%)**: [e.g., FlashList of recent transactions with contact avatars]
- **Secondary Safe Actions (Bottom 40%)**: [e.g., "Add Message" or "Set Reminder" options]

## 3. Platform Parity Alignment
- **iOS Parity Strategy**: [e.g., Use native Sheet modal Presentation (`formSheet`), SF Symbols for action rows]
- **Android Parity Strategy**: [e.g., Springy transition animation, Material 3 ripple backgrounds for action tiles]

## 4. Emotional Journey States
- **The Peak Moment**: [e.g., Success animation on transfer complete]
- **The End Impression**: [e.g., Clean summary card showing contact's updated profile and a cheerful progress note]
- **Loading State Strategy**: [e.g., Pulse-opacity skeleton placeholders matching the exact card aspect ratios]
```

---

## Step 3: Visual Probes & Layout Mockups

If requested by the user, provide 1 or 2 **ASCII Mockups** of the screen layout to visually represent the hierarchy before building it.

### Example ASCII Mockup (375px Mobile Viewport Layout):
```
+-----------------------------------+
|  [X] Close           (iOS Sheet)  |
|                                   |
|       TRANSFER IN PROGRESS        |
|                                   |
|             $1,240.00             |  <-- Large Tabular Numbers
|             To: Sarah             |
|                                   |
|  +-----------------------------+  |
|  | [Avatar]  Sarah Jenkins     |  |  <-- Primary Content Card
|  |           s.jenkins@mail    |  |
|  +-----------------------------+  |
|                                   |
|  +-----------------------------+  |
|  | Note: "For dinner last night"|  |  <-- Secondary Input Field
|  +-----------------------------+  |
|                                   |
|                                   |
|  +-----------------------------+  |
|  |      CONFIRM & SEND         |  |  <-- Primary CTA in Thumb Zone
|  +-----------------------------+  |
|                                   |
+-----------------------------------+
```

---

## Step 4: Secure Approval

Wait for the user to reply and sign off on the design brief or mockups. 
Once approved, the structure is frozen and you are ready to transition to the implementation phase (`/expo-mobile-ui craft [screen]`).
