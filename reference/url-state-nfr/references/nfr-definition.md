# NFR Definition — URL State Compliance

## The Rule

All page view state must be controllable via the URL, and the URL must reflect the current view state. If a user shared the URL with a colleague, would the colleague expect to see the same view? If yes, that state belongs in the URL.

**Critically, this does NOT include form state.** Input values, checkbox states, radio button selections, form validation — none of these are in scope. The NFR targets only control logic that modifies the page view: which panels are open, which tab is active, whether a drawer or modal is showing.

## Always Flag — High Confidence Targets

These UI patterns nearly always represent view state that should be URL-controlled:

- **Modal / Dialog** open/close state
- **Drawer / Sheet / Side panel** open/close state
- **Sidebar / SideNav** visibility toggle
- **Tab selection** — the active tab in a tab group
- **Multi-step wizard / Stepper** — the current step
- **Route-like view switching** — content panels that swap based on state (not routing)
- **Expandable/collapsible sections** when they represent major content areas (not individual FAQ items or accordion rows)
- **Full-screen overlay** visibility

## Flag with Lower Confidence — Judgment Required

These patterns may or may not be view state depending on context. Flag them but note the ambiguity:

- **Accordion expansion** — flag if only one accordion in a group, or if the accordion controls a major content section. Skip if it's a list of many small expandable items (FAQ-style).
- **Toggle panels** that show/hide substantial content blocks
- **Sort/filter state** on data tables — borderline; flag at low confidence and note as debatable
- **Navigation menu open/close** on mobile — flag if the menu is a full-screen overlay, skip if it's a small hamburger dropdown

## Never Flag — Explicitly Excluded

These are NOT view state violations regardless of implementation:

- Form input values, checkbox states, radio button selections
- Form validation state (`isDirty`, `isValid`, `isSubmitting`)
- Loading / error / pending states (`isLoading`, `isError`, `isPending`, `isFetching`)
- Hover, focus, active, pressed states
- Tooltip visibility
- Dropdown menu open/close (unless it's a navigation mega-menu with substantial content)
- Popover open/close (unless it contains navigable content, not just a quick info bubble)
- Animation / transition states
- Scroll position
- Server cache state (React Query/SWR stale/refetching indicators)
- State that only toggles CSS classes for styling without conditional rendering
- Drag-and-drop state
- Selection state in lists (selected rows in a table) — unless selection changes what content panel is shown
- Toast / snackbar notification visibility
- Context menu (right-click menu) visibility
