# Confidence Rubric

Assign a confidence score to every potential violation. This score reflects how certain the analysis is that the finding is a real NFR violation, not a false positive.

## Scoring Table

| Score | Criteria | Examples |
|-------|----------|----------|
| **0.9–1.0** | State directly controls visibility of a component whose name unambiguously matches a view primitive (Modal, Dialog, Drawer, Sheet, Sidebar). State naming follows conventional patterns (`isOpen`, `isVisible`, `showModal`, `drawerOpen`). | `const [isModalOpen, setIsModalOpen] = useState(false)` controlling `<Modal open={isModalOpen}>` |
| **0.7–0.89** | State controls conditional rendering of a substantial UI section. Component name suggests a view element but isn't a perfect keyword match, or state naming is clear but the component is custom/unfamiliar. | `const [showDetails, setShowDetails] = useState(false)` controlling `<DetailPanel visible={showDetails}>` where `DetailPanel` is a custom component that behaves like a drawer |
| **0.5–0.69** | State controls conditional rendering but: the component name is generic (e.g., `<Panel />`), the state naming is ambiguous (e.g., `isActive` which could be a CSS toggle), or the conditionally rendered block is moderate in size. | `const [isActive, setIsActive] = useState(false)` controlling `{isActive && <SideContent />}` where `SideContent` might be a sidebar or might be a small info section |
| **0.3–0.49** | Weak signal — state might control view rendering but the analysis isn't confident. Generic boolean state with unclear usage, or the component could be ephemeral (popover, tooltip-like). | `const [expanded, setExpanded] = useState(false)` controlling a small expandable section that might or might not be major content |
| **Below 0.3** | **Do not include in output.** If the signal is this weak, it's noise. | State that toggles a CSS class, controls a tooltip, or manages a tiny inline expansion |

## Guidance

- **When in doubt, err toward flagging at lower confidence.** False positives are cheap — developers dismiss them in seconds. False negatives require another full scan of the codebase.
- **Component name is the strongest signal.** A state controlling `<Drawer>` is almost certainly a violation (0.9+). A state controlling `<ContentArea>` needs more investigation (0.5–0.7).
- **State naming is a secondary signal.** `isDrawerOpen` is unambiguous. `isActive` is not.
- **Rendered content size matters.** A ternary rendering `<span>Yes</span>` vs `<span>No</span>` is not a violation regardless of state source. The conditional content must represent a distinct navigable section.
- **Custom hooks that abstract view state don't reduce confidence.** `useModal()` returning `{ isOpen, open, close }` is just as much a violation as inline `useState` — the hook hides the state but doesn't make it URL-sourced.
- **UI library imports boost confidence.** If the controlled component is imported from MUI, Radix, Chakra, Ant Design, shadcn, or similar, and its name contains a view primitive keyword, treat it as 0.9+.
