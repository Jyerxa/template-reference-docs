---
name: url-state-nfr
description: >
  Scans React codebases to find components that manage page view state (modals, drawers,
  sidebars, tabs, panels, steppers, accordions) using local or store-based state instead
  of URL parameters. Use this skill whenever someone asks to audit, scan, check, or evaluate
  a React codebase for URL state compliance, view state violations, or NFR conformance
  around URL-driven UI. Also trigger for phrases like "find components not using URL state,"
  "which components need URL refactoring," "audit view state," "scan for modal/drawer state
  violations," "are we URL-compliant," or "check if our state is in the URL." Use even for
  partial scans targeting a single directory or feature folder.
---

# URL State NFR Compliance Scanner

Scan a React codebase to identify components where page view state (modals, drawers, tabs, sidebars, steppers, etc.) is managed via local state or stores instead of URL parameters.

**The NFR:** All page view state must be controllable via the URL, and the URL must reflect the current view state. If a user shared the URL with a colleague, the colleague should see the same view. This does NOT include form state — input values, checkboxes, radio buttons, and form validation are explicitly out of scope.

## Architecture

You are the **orchestrator**. You do NOT read component files or analyze code yourself. You facilitate, delegate, and consolidate by spawning sub-agents for all heavy work:

| Phase | What You Do | What Sub-Agents Do |
|-------|------------|-------------------|
| 1. Discovery | Spawn discovery agent, provide target directory | Traverse codebase, build structural manifest |
| 2. Batch Planning | Read the manifest, group files into analysis batches | — (you do this, it's lightweight) |
| 3. Analysis | Spawn analyzer agents in parallel (one per batch) | Read component source, apply detection algorithm, output findings |
| 4. Consolidation | Spawn consolidator agent with all batch results | Deduplicate, cross-reference, generate final report |

This architecture exists because target codebases have hundreds or thousands of components. A single agent would exhaust its context window trying to read and analyze them all. Sub-agents each get a fresh context window scoped to their batch.

## Reference Files

These files contain the detailed detection rules, scoring criteria, and exclusion logic. They are embedded into sub-agent prompts — you don't need to read them yourself unless you want to understand the rules for batch planning decisions.

| File | Purpose |
|------|---------|
| `references/nfr-definition.md` | What counts as a violation, what doesn't |
| `references/detection-patterns.md` | The five-step analysis algorithm |
| `references/confidence-rubric.md` | How to score findings (0.3–1.0) |
| `references/exclusion-list.md` | False positive prevention |

## Sub-Agent Prompts

These are complete, self-contained prompts for each sub-agent type. Read the relevant prompt file, fill in the template variables, and pass it to the Agent tool.

| File | Used In | Purpose |
|------|---------|---------|
| `prompts/discovery-agent.md` | Phase 1 | Codebase traversal and manifest building |
| `prompts/analyzer-agent.md` | Phase 3 | Violation detection on a file batch |
| `prompts/consolidator-agent.md` | Phase 4 | Deduplication, cross-referencing, report generation |

---

## Phase 1 — Discovery

**Goal:** Get a structural manifest of the codebase without reading any component logic yourself.

### Step 1: Determine the Target

Ask the user for a target directory. If they don't specify one, use the project root. If there are uploaded files, check the uploads path.

### Step 2: Spawn the Discovery Agent

Read `prompts/discovery-agent.md`. Fill in `{{TARGET_DIRECTORY}}` with the target path. Spawn as a sub-agent using the Agent tool:

```
Agent(
  description: "Discover React components",
  prompt: <contents of discovery-agent.md with TARGET_DIRECTORY filled in>
)
```

The discovery agent will:
- Find all `.tsx` and `.jsx` files (excluding tests, stories, node_modules, type defs)
- Extract imports, exports, line counts, and classification signals
- Identify files that import view-state primitives (Modal, Drawer, Tab, etc.)
- Identify context providers
- Save a JSON manifest

### Step 3: Review the Manifest

When the discovery agent completes, read the manifest it produced. Report to the user:
- Total component files discovered
- How many import view-state primitives (high-priority targets)
- How many context providers were found
- Breakdown by file classification (pages, features, layouts, hooks, etc.)

---

## Phase 2 — Batch Planning

**Goal:** Divide the manifest into analysis batches optimized for context window efficiency and detection accuracy. This is the one phase you do yourself — it's lightweight metadata work.

### Batching Rules

1. **Line budget:** Each batch targets ~800 source lines maximum. This leaves room in the sub-agent's context for the system prompt, analysis instructions, and structured output. If a single file exceeds 800 lines, give it its own batch with a note to scan section by section.

2. **Grouping priority (highest to lowest):**

   - **Context Provider + consumers:** Batch a provider with its most-imported consumers. If consumers exceed the line budget, batch the provider with the top consumers and put the rest in separate batches with a cross-reference note pointing back to the provider.
   - **Parent + children:** Batch a parent component with the children it renders (from the import adjacency in the manifest). This lets the analyzer see state ownership and prop passing together.
   - **View-primitive importers are high priority:** Files importing Modal, Drawer, Tab, etc. should be analyzed first and never deprioritized by batching with many low-priority files.
   - **Hook + consumers:** Batch custom hook files with the components that use them when possible.
   - **UI primitives:** Batch aggressively for quick dismissal, or skip entirely if they don't import view-state primitives and have generic names (Button, Input, Card).

3. **Batch metadata:** Each batch should include:
   - File paths and total line count
   - Context notes (e.g., "File X is a Context Provider consumed by Y and Z")
   - External component references (components imported by batch files but not in this batch)

### Scope Filtering

If the user requested a subset (e.g., "just scan `src/features/orders/`"):
- Filter the manifest to that subset
- BUT include relevant context files (shared hooks, context providers) the subset imports from
- Note which context files were pulled in and why

### Small Codebases (< 50 component files)

For small codebases, skip formal batching. Just create one batch per high-priority file (or small natural group) and move to Phase 3. The overhead of optimizing batches isn't worth it.

### Confirm with the User

Before spawning analyzer agents, report:

```
Planned [B] analysis batches covering [N] component files.
- [X] files import view-state primitives (high priority)
- [Y] context providers identified
- Estimated: [B] parallel sub-agent runs
Proceed?
```

Wait for confirmation before Phase 3.

---

## Phase 3 — Analysis

**Goal:** Spawn parallel analyzer sub-agents to detect violations in each batch.

### Spawn Analyzer Agents

Read `prompts/analyzer-agent.md`. For each batch, fill in the template variables:

- `{{BATCH_ID}}` — sequential batch number
- `{{BATCH_FILE_LIST}}` — the file paths in this batch
- `{{BATCH_CONTEXT_NOTES}}` — relationship notes from batch planning
- `{{EXTERNAL_COMPONENT_LIST}}` — components imported but not in this batch
- `{{TARGET_DIRECTORY}}` — root of the scanned codebase (so the agent can resolve paths)

**Spawn all batch agents in a single turn** so they run in parallel:

```
Agent(description: "Analyze batch 1", prompt: <filled analyzer prompt for batch 1>)
Agent(description: "Analyze batch 2", prompt: <filled analyzer prompt for batch 2>)
Agent(description: "Analyze batch 3", prompt: <filled analyzer prompt for batch 3>)
...
```

Each analyzer agent will:
- Read the full source of each file in its batch
- Apply the five-step detection algorithm (identify state → trace to rendering → classify source → assess scope → assign confidence)
- Apply the exclusion list to filter out form state, loading state, CSS-only state, and other false positives
- Output a JSON array of findings with file paths, line numbers, confidence scores, and suggested fixes

### While Agents Run

Use this time to prepare for Phase 4. No action needed — just wait for all agents to complete and collect their results.

### Handling Large Codebases

For codebases with many batches (20+), consider spawning in waves of 8-10 agents at a time rather than all at once, to avoid overwhelming the system. Collect results from each wave before spawning the next.

---

## Phase 4 — Consolidation

**Goal:** Merge all batch results into a single deduplicated, prioritized report.

### Spawn the Consolidator Agent

Read `prompts/consolidator-agent.md`. Fill in:

- `{{BATCH_RESULTS}}` — the combined findings from all analyzer agents
- `{{TARGET_DIRECTORY}}` — what was scanned
- `{{TOTAL_FILES_SCANNED}}` — from the manifest
- `{{TOTAL_COMPONENTS_ANALYZED}}` — count of files that went through analysis

```
Agent(
  description: "Consolidate scan results",
  prompt: <contents of consolidator-agent.md with variables filled in>
)
```

The consolidator agent will:
- Cross-reference parent/child findings into violation groups
- Link context provider violations to their consumers
- Deduplicate overlapping findings from different batches
- Sort by confidence (descending) then fix scope (root-refactor → cross-cutting → isolated)
- Generate a markdown compliance report

### Present Results

When the consolidator finishes, read the report it produced. Present to the user:
- The full report (saved as a file they can access)
- A conversation summary highlighting:
  - Total violations by confidence tier (high/medium/low)
  - Top 3–5 highest-impact findings
  - Patterns observed (e.g., "most violations in the orders feature," "a shared SidebarContext distributes view state to 12 components")
  - Recommended fix priority

---

## Special Cases

### Monorepos

- Shared UI primitive packages (e.g., a shared `<Modal />` wrapper) are NOT violations themselves
- The violation is in the consuming app's state management
- Scan shared packages only to understand what primitives exist; flag violations in app code
- When batching, treat each app as its own scope but include shared imports as context

### Server Components (Next.js App Router / RSC)

- Files with `'use client'` directive → scan normally
- Files WITHOUT `'use client'` in a Next.js App Router project → server components, cannot have state, skip analysis
- Note server component count in the discovery summary

### Remix / React Router v6+

- Components using `useLoaderData`, `useActionData`, or URL-based routing state are compliant for those specific values
- Still check for local state controlling view primitives alongside the compliant patterns

### TanStack Router

- `useSearch`, `useParams`, `useNavigate` with search params → compliant
- Custom hooks wrapping TanStack Router search params → compliant

### Zustand Stores

- Zustand is very common in modern React. Look for `create(` from `zustand` and custom hooks generated by `create`.
- View state in a Zustand store is still a violation — it's not URL-sourced just because it's in a store.

### Dead Code

This skill cannot detect whether a component is actually rendered. If the user is concerned about scanning unused code, recommend a narrower target directory or separate tree-shaking analysis.

---

## Error Handling

- **Discovery agent fails or returns empty manifest:** Check if the target directory is correct. Ask the user to confirm the path.
- **Analyzer agent times out on a large batch:** Split the batch in half and re-run as two smaller batches.
- **No violations found:** This is a valid result. Report it clearly — the codebase (or scanned subset) appears compliant.
- **User wants to re-scan after fixes:** Re-run the full pipeline. The manifest will reflect current file state since discovery reads from disk.
