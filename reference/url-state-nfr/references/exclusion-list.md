# Exclusion List — What NOT to Flag

This is the most critical reference for avoiding false positives. Every finding must be checked against this list before inclusion in the output.

---

## 1. Form State (Most Common False Positive)

Form state is the #1 source of false positives. This NFR explicitly excludes form state. Never flag these:

**State names that signal form state:**
`isChecked`, `selectedValue`, `inputValue`, `formData`, `errors`, `isValid`, `isDirty`, `isSubmitting`, `fieldValue`, `formState`, `touched`, `pristine`

**The test:** Does this state change what *page section or navigational view* the user is looking at, or does it change *content within a form*? Only the former is a violation.

**Examples — DO NOT flag:**
```tsx
// Validation error display — form state
const [errors, setErrors] = useState({});
{errors.email && <span className="error">{errors.email}</span>}

// Conditional form field — still form state
const [showOptionalFields, setShowOptionalFields] = useState(false);
{showOptionalFields && <input name="middleName" />}

// Form step within a form wizard — use judgment:
// If each step is a full-screen form page with its own URL, it's view state.
// If steps are sections within one scrollable form, it's form state.
```

---

## 2. Loading / Error / Pending States

Server synchronization states are never view state violations.

**State names to exclude:**
`isLoading`, `isError`, `isPending`, `isFetching`, `status`, `isRefreshing`, `isStale`

**Patterns to exclude:**
```tsx
{isLoading ? <Spinner /> : <Content />}
{error ? <ErrorMessage error={error} /> : <DataTable data={data} />}
const { data, isLoading, error } = useQuery(...)
```

**Exception:** If `isLoading` toggles between an entirely different page layout (not just a spinner overlay), note it with low confidence (0.3–0.4) and explain the ambiguity.

---

## 3. CSS-Only State

If state is used only to toggle CSS classes and does not gate conditional rendering of JSX elements, it's a styling concern, not view state.

**The test:** Does the state appear inside `className={}` or `style={}` and nowhere in a conditional rendering expression (`&&`, ternary, early return, switch)?

```tsx
// Pure CSS toggle — NOT a violation
const [isActive, setIsActive] = useState(false);
<div className={isActive ? 'active' : 'inactive'}>Always rendered content</div>

// Inline style toggle — NOT a violation
<div style={{ opacity: isVisible ? 1 : 0 }}>Content always in DOM</div>
```

**Caveat:** If the same state controls *both* a CSS class *and* conditional rendering, evaluate the conditional rendering part — that may be a violation.

---

## 4. Trivial Conditional Content

Not all conditional rendering is view state. The conditionally rendered content must represent a distinct navigable section of the page.

```tsx
// Small inline toggle — NOT a violation
{showMore ? <span>Full description here...</span> : <span>Short desc...</span>}

// Badge / indicator — NOT a violation
{hasNotifications && <Badge count={3} />}

// Empty state — NOT a violation
{items.length === 0 ? <EmptyState /> : <ItemList items={items} />}
```

---

## 5. Ephemeral UI Elements

Small, transient UI elements that don't represent navigable page sections:

- **Tooltips** — never flag
- **Popovers** — don't flag unless the popover contains navigable content (a mini-app, navigation links, substantial form)
- **Dropdown menus** — don't flag standard select-like dropdowns. Flag only navigation mega-menus with substantial content
- **Toast / Snackbar notifications** — never flag
- **Context menus** (right-click menus) — never flag

---

## 6. Server Cache & Data Fetching State

React Query, SWR, Apollo, and similar libraries manage server synchronization state. None of this is view state:

- `useQuery` return values (`data`, `isLoading`, `isError`, `isFetching`, `isStale`)
- `useMutation` return values
- Cache invalidation / refetching state
- Optimistic update state
- Pagination cursors (borderline — if flagged, use low confidence 0.3–0.4)

---

## 7. Animation & Transition State

State used purely for orchestrating animations:

- `isAnimating`, `isTransitioning`
- Spring/motion values (framer-motion, react-spring)
- CSS transition triggers
- `requestAnimationFrame` state

---

## 8. Interaction State

Transient user interaction state that doesn't represent a view:

- Hover state (`isHovered`)
- Focus state (`isFocused`, `hasFocus`)
- Pressed/active state (`isPressed`)
- Drag-and-drop state (`isDragging`, `dragOverIndex`)
- Scroll position
- Mouse/pointer coordinates
- Selection state in lists (selected rows) — unless selection changes what content panel is shown

---

## 9. File Types to Skip Entirely

If these appear in an analysis batch, skip them without analysis:

- Test files: `*.test.tsx`, `*.spec.tsx`, `__tests__/`
- Storybook files: `*.stories.tsx`, `*.story.tsx`
- Type definition files: `*.d.ts`
- Utility files with no JSX (pure functions, constants, config)
- Server components without `'use client'` directive (they can't have state)
