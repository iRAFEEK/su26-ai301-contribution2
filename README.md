# Contribution #2: feat: Markdown Preview Toggle (Raw vs. Rendered)

**Contribution Number:** 2  
**Student:** Rafeek Hanna  
**Issue:** [JhaSourav07/commitpulse #7813](https://github.com/JhaSourav07/commitpulse/issues/7813)  
**Status:** Phase IV — Submitted (PR auto-closed; awaiting maintainer assignment)

---

## Why I Chose This Issue

The Export Panel's Markdown tab shows the badge snippet as raw code but gives no way to confirm how the badge image actually looks before copying it to a GitHub README. I chose this issue because it has a clearly bounded scope — one component file — and involves React state management and conditional rendering, which matches my frontend background. It also produces a visible, testable UI change that I could verify locally end-to-end.

This was my second issue on the commitpulse project, chosen because I was already familiar with the codebase and wanted to deepen that familiarity with a self-contained feature rather than switching to a new project entirely.

---

## Understanding the Issue

### Problem Description

When a user customizes their CommitPulse badge and switches to the Markdown export tab, they see only raw markdown code (e.g. `![badge](https://commitpulse.vercel.app/api/streak?user=...)`) with no way to preview whether the image URL renders correctly before copying it.

### Expected Behavior

The Markdown tab should include a Raw/Preview toggle so users can switch between:
- **Raw** — the markdown code snippet, ready to copy
- **Preview** — the badge image rendered exactly as GitHub would display it in a README

### Current Behavior

Only the raw markdown code block is shown. There is no toggle. Users must copy the snippet and paste it into a separate tool to verify the image renders.

### Affected Components

- **Repo:** JhaSourav07/commitpulse (forked to iRAFEEK/commitpulse)
- **File:** `app/customize/components/ExportPanel.tsx`

---

## Reproduction Process

### Environment Setup

1. Forked the repo on GitHub and cloned my fork: `git clone https://github.com/iRAFEEK/commitpulse.git`
2. Installed dependencies: `npm install`
3. Created `.env.local` — had to generate `AUTH_SECRET` and `ENCRYPTION_KEY`, and add a GitHub PAT as `GITHUB_TOKEN`. Without these, the dev server threw a 500 error on load.
4. Started the dev server: `npm run dev`

### Steps to Reproduce

1. Navigate to `localhost:3000/customize`
2. Enter a GitHub username
3. Scroll down to the Export Panel and click the **Markdown** tab
4. Observe: only a raw code block is shown — no toggle button exists

### Reproduction Evidence

- **Commit showing reproduction:** [iRAFEEK/commitpulse/tree/feat/markdown-preview-toggle](https://github.com/iRAFEEK/commitpulse/tree/feat/markdown-preview-toggle)
- **Screenshots/logs:** Confirmed on live site at commitpulse.vercel.app/customize — Markdown tab shows raw code only, no toggle
- **My findings:** The issue is purely a missing UI feature in `ExportPanel.tsx`. No backend changes required. The markdown snippet already contains the image URL, so it can be extracted and rendered client-side.

---

## Solution Approach

### Analysis

`ExportPanel.tsx` already tracks which format tab is active via a `format` prop. The Markdown tab renders a static `<code>` block. The fix is to add a local `viewMode` state that switches between showing that code block and rendering an `<img>` tag pointing to the URL embedded in the snippet string.

### Proposed Solution

Extract the image URL from the markdown snippet string using a regex match on the `(URL)` pattern, and render it as `<img src={url}>` in Preview mode. Fall back to a placeholder message if no username is set yet.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The markdown snippet is a string like `![badge](https://commitpulse.vercel.app/api/streak?user=iRAFEEK&theme=dark)`. The URL inside the parentheses is the badge image URL.

**Match:** The existing format tab toggle pattern in `ExportPanel.tsx` was used as a model. The emerald color scheme and glassmorphism styling used throughout the component were reused for the toggle buttons.

**Plan:**
1. Add `const [viewMode, setViewMode] = useState<'raw' | 'rendered'>('raw')` inside the component
2. Reset `viewMode` to `'raw'` whenever the user switches format tabs
3. Render a pill-style Raw/Preview button group — only visible when `format === 'markdown'`
4. Replace the static code block with a conditional: `<img>` in `'rendered'` mode, `<code>` in `'raw'` mode

**Implement:** [feat/markdown-preview-toggle branch](https://github.com/iRAFEEK/commitpulse/tree/feat/markdown-preview-toggle) — 65 additions, 4 deletions in `ExportPanel.tsx`

**Review:** Matched existing Tailwind class patterns, emerald active-state styling, and glassmorphism border/backdrop conventions. Added `aria-pressed` for accessibility.

**Evaluate:** Tested locally — toggle appears only on Markdown tab, Preview renders the badge image, Raw shows the code, switching tabs resets to Raw.

---

## Testing Strategy

### Unit Tests

- [x] No automated unit test harness exists for this UI component — verification was manual and behavioral, consistent with the project's approach

### Integration Tests

- [x] Existing build checks (linting, TypeScript compilation) pass on the branch

### Manual Testing

- Switched to the Markdown tab → toggle button group appears
- Clicked **Preview** → badge image loaded correctly from the production API URL
- Clicked **Raw** → raw code block restored
- Switched to HTML/React TSX tabs → toggle disappears and state resets to Raw on return
- Tested with no username entered → fallback message shown in Preview mode

---

## Implementation Notes

### Week 5 Progress (Phase I — Issue Selection & Phase II — Reproduce & Plan)

Selected issue #7813 on JhaSourav07/commitpulse and forked the repo. Set up the local dev environment — the main challenge was the missing `.env.local`: the app threw a 500 error until I generated the required secrets (`AUTH_SECRET`, `ENCRYPTION_KEY`) and added a GitHub PAT as `GITHUB_TOKEN`. Reproduced the issue on both localhost and the live site (commitpulse.vercel.app), confirming the Markdown tab shows only raw code with no toggle. Analyzed `ExportPanel.tsx` and planned the solution: add `viewMode` state, a pill-style toggle, and conditional rendering based on whether the URL can be extracted from the snippet string.

### Week 6 Progress (Phase III — Build & Phase IV — Submit)

Implemented the feature in `ExportPanel.tsx`: added `viewMode` useState, the pill-style Raw/Preview toggle (markdown tab only), and conditional rendering logic — `<img>` in Preview mode, `<code>` in Raw mode, with a fallback message if no username is set. Tested end-to-end locally; all scenarios passed. Opened PR [#8049](https://github.com/JhaSourav07/commitpulse/pull/8049) against `JhaSourav07/commitpulse:main`. The repo's GitHub Actions bot auto-closed it because the issue was not pre-assigned — the bot's `/claim` command is restricted to the issue author only. Left a comment on the issue requesting maintainer assignment; will reopen the PR once assigned.

### Code Changes

- **Files modified:** `app/customize/components/ExportPanel.tsx`
- **Key commits:** [feat/markdown-preview-toggle](https://github.com/iRAFEEK/commitpulse/tree/feat/markdown-preview-toggle) — 65 additions, 4 deletions
- **Approach decisions:** Used regex extraction of the URL from the markdown string to keep the change minimal and non-breaking. Reset to Raw on tab switch to avoid stale previews.

---

## Pull Request

**PR Link:** [JhaSourav07/commitpulse #8049](https://github.com/JhaSourav07/commitpulse/pull/8049)

**PR Description:** Adds a Raw/Preview toggle to the Markdown export tab. Includes a note clarifying the relationship to the existing Live Preview widget — the outputs are visually similar, but this toggle is scoped to the Export Panel and confirms the snippet URL renders as a plain image. Checklist and Pillar filled out per CONTRIBUTING.md.

**Maintainer Feedback:**
- 2026-07-13: GitHub Actions bot — PR auto-closed because issue #7813 was not assigned before opening. → Left comment on issue requesting assignment; will reopen once assigned.

**Status:** PR closed by bot; awaiting maintainer assignment on issue #7813.

---

## Learnings & Reflections

### Technical Skills Gained

Set up a full Next.js app locally from scratch, including generating secrets and configuring environment variables. Read and modified a 400+ line React component without breaking unrelated functionality. Learned how GitHub Actions bots enforce contribution workflows and what pre-assignment requirements exist before a PR can be accepted.

### Challenges Overcome

Dev server failed to start due to missing environment variables — resolved by generating required secrets and adding a GitHub PAT. PR was auto-closed by the bot because I hadn't been pre-assigned to the issue — a project-specific rule I wasn't aware of going in.

### What I'd Do Differently Next Time

Read the repo's `CONTRIBUTING.md` and check for bot workflows before opening a PR. In this project the bot enforces pre-assignment strictly — submitting without it results in an automatic close regardless of code quality.

---

## Resources Used

- [CommitPulse CONTRIBUTING.md](https://github.com/JhaSourav07/commitpulse/blob/main/CONTRIBUTING.md)
- [Issue #7813](https://github.com/JhaSourav07/commitpulse/issues/7813)
- [PR #8049](https://github.com/JhaSourav07/commitpulse/pull/8049)
- Next.js App Router docs — local environment setup
- React `useState` docs — conditional rendering patterns
