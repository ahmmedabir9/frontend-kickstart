---
name: frontend-kickstart
description: Start a new React front-end app. Interviews the user in plain language (brand, tooling, conventions), then scaffolds a production-grade project with ESLint, Prettier, Git hooks, a design system, custom scrollbar, and docs for both humans and AI. Use whenever the user wants to create, start, bootstrap, or scaffold a new front-end or React application.
---

# Frontend Kickstart

Turn "create a React app" into a production-ready project with the user's own brand, rules, and documentation, so nobody repeats the same setup by hand.

## Principles

1. **Interview first, build second.** Never scaffold before the interview is done and the user has confirmed the summary.
2. **Plain language.** The user may be non-technical. Ask about outcomes ("Do you want automatic checks before code is saved to Git?"), not tools. Add a one-line explanation to each question.
3. **Recommend, don't interrogate.** Every question has a recommended default marked "(Recommended)". The user can accept all defaults.
4. **Enforce rules with tooling, document the reasons.** A rule that can be a lint rule, type check, or hook must be one. Docs explain the why; tooling guarantees the how.
5. **Docs are for humans and AI.** Keep `CLAUDE.md` short and point to `docs/` for detail.
6. **Save the answers** to `kickstart.config.json` so the project can be regenerated or re-themed later.

## Phase 0: Check the ground

- Run `pwd` and list the folder. If it already contains a project (`package.json`), stop and ask whether to add the kickstart to the existing app or use a new folder. Never overwrite existing files silently.
- Check for Node (`node -v`, require 20+) and the package manager the user prefers (default `pnpm`, fall back to `npm`).
- Check `kickstart.config.json`. If it exists, offer: re-run with saved answers, or change specific answers only.

## Phase 1: Interview

Use AskUserQuestion, 2 to 4 questions per round. Start by asking **Quick** (about 6 questions, defaults for the rest) or **Detailed**. Skip rounds that don't apply.

### Round 1: The project (always)
- App name (also used for the folder and package name).
- What is it? (Admin dashboard / SaaS product / Marketing site / Internal tool / Other.) This shapes the starter layout.
- Who uses it? (Public customers / Company staff / Both.) This decides how much accessibility and polish effort to document.

### Round 2: The foundation
- Framework: Vite + React + TypeScript (Recommended) / Next.js. Explain: "Vite is simpler and fastest for apps behind a login. Next.js is better when search engines must find your pages."
- Styling: Tailwind CSS (Recommended) / CSS Modules / Other.
- Component library on top? None, own components (Recommended) / shadcn/ui / Other.
- Routing and data fetching: React Router + TanStack Query (Recommended) / None yet.

### Round 3: Quality guardrails
- Automatic code checks (ESLint)? Yes (Recommended).
- Automatic formatting (Prettier)? Yes (Recommended).
- Checks before every commit and before every push (Git hooks)? Yes (Recommended). Explain: "Mistakes get caught on your computer before they reach teammates."
- Strictness: Strict (Recommended) / Relaxed.
- Maximum lines per file: 250 (Recommended) / 150 / 400 / No limit.
- Testing setup? Vitest + Testing Library (Recommended) / Later.

### Round 4: Brand and look (only if the user wants a brand guideline; offer it as Yes (Recommended))
Ask in this order and keep the wording simple:
- Do you already have brand colors? If yes, ask for the main color (hex or a description like "deep forest green"). If no, offer 3 to 4 mood choices (Trustworthy and calm / Bold and energetic / Warm and friendly / Minimal and elegant) and derive a palette from the pick.
- Secondary or accent color (optional).
- Light mode only / Dark mode only / Both (Recommended).
- Font feel: Modern sans / Friendly rounded / Classic serif / Technical mono. Map to real fonts, and confirm fonts are free to use (for example Inter, Plus Jakarta Sans, Source Serif, JetBrains Mono).
- Corners: Sharp / Slightly rounded (Recommended) / Very rounded.
- Density: Compact (data-heavy) / Comfortable (Recommended) / Spacious.
- Any website or app whose look you like? (Optional. Use as a reference for tone, never to copy.)
- Scrollbars: Slim custom scrollbar (Recommended) / Auto-hide until hover / Browser default.

### Round 5: Extras (Detailed mode only)
- Folder structure: Feature-based (Recommended) / Layer-based.
- Absolute imports with `@/` alias (Recommended).
- Environment variable handling with validation (Recommended).
- Error boundary and 404 page starter (Recommended).
- CI workflow for GitHub Actions (lint, type-check, test, build)? Yes / No.
- Commit message rules (Conventional Commits)? Yes / No.

### Confirm
Show a short plain-language summary of all answers. Ask "Ready to build?". Only continue on yes. Write the answers to `kickstart.config.json` at the project root.

## Phase 2: Scaffold

- Create the project with the official generator for the chosen framework. Check current docs for the latest commands and versions rather than relying on memory.
- Install the chosen dependencies. Remove demo boilerplate (default logo, counter, sample CSS).
- Enable TypeScript strict mode (`strict`, `noUncheckedIndexedAccess`, `noImplicitOverride`) when Strict is chosen. Set up the `@/` alias in both the bundler and `tsconfig`.
- Create the folder structure (see Architecture below).

## Phase 3: Guardrails

### ESLint (flat config)
Include: typescript-eslint (strict), react-hooks, react-refresh, jsx-a11y, import ordering, and these project rules (adjust numbers to the interview):

```js
rules: {
  'max-lines': ['error', { max: 250, skipBlankLines: true, skipComments: true }],
  'max-lines-per-function': ['warn', { max: 80, skipBlankLines: true, skipComments: true }],
  'no-console': ['warn', { allow: ['warn', 'error'] }],
  '@typescript-eslint/no-explicit-any': 'error',
  '@typescript-eslint/consistent-type-imports': 'error',
  'no-restricted-syntax': ['error',
    { selector: "Literal[value=/^#[0-9a-fA-F]{3,8}$/]", message: 'Use design tokens, not hard-coded colors.' }
  ],
}
```
Also add a `no-restricted-imports` rule that blocks deep cross-feature imports (features may only import from another feature's `index.ts`). Add naming rules via `@typescript-eslint/naming-convention` (camelCase variables, PascalCase components and types, UPPER_SNAKE_CASE constants).

### Prettier
`.prettierrc`: single quotes, semicolons, trailing commas `all`, print width 100. Add `prettier-plugin-tailwindcss` when Tailwind is chosen. Add `.editorconfig`.

### Git hooks (Husky + lint-staged)
- `pre-commit`: `pnpm lint-staged` (ESLint `--fix` and Prettier on staged files only).
- `pre-push`: `pnpm typecheck && pnpm test --run && pnpm build`.
- `commit-msg` (if chosen): commitlint with Conventional Commits.
- Add scripts: `lint`, `lint:fix`, `format`, `typecheck`, `test`, `build`, `prepare` (`husky`).
- Keep hooks fast. If pre-commit would take more than about 10 seconds, only check staged files.

### Testing and CI (if chosen)
Vitest with jsdom and Testing Library, one example test next to a real component. GitHub Actions workflow running lint, typecheck, test, build.

## Phase 4: Design system

Single source of truth: **design tokens**. Nothing in components may use raw colors, pixel spacing, or font names.

### Tokens (`src/styles/tokens.css`, or Tailwind `@theme` for Tailwind v4)
- **Color scale** for the brand color (50 to 950), generated from the user's main color. Also a neutral scale and status colors (success, warning, danger, info).
- **Semantic tokens** that components use: `--color-bg`, `--color-surface`, `--color-surface-raised`, `--color-border`, `--color-text`, `--color-text-muted`, `--color-primary`, `--color-primary-hover`, `--color-on-primary`, `--color-danger`, and so on. Provide light and dark values when both modes are chosen.
- **Check contrast** for text on every background pair (WCAG AA: 4.5:1 for body text). Adjust the palette if it fails and tell the user what changed.
- Spacing scale (4px base), type scale, line heights, radius (from the corner choice), shadows, z-index layers, motion durations, breakpoints.

### Scrollbar (always included, style from the interview)
```css
/* Slim custom scrollbar. For auto-hide, make the thumb transparent until hover. */
* {
  scrollbar-width: thin;
  scrollbar-color: var(--color-scrollbar-thumb) transparent;
}
*::-webkit-scrollbar { width: 8px; height: 8px; }
*::-webkit-scrollbar-track { background: transparent; }
*::-webkit-scrollbar-thumb {
  background-color: var(--color-scrollbar-thumb);
  border-radius: var(--radius-full);
}
*::-webkit-scrollbar-thumb:hover { background-color: var(--color-scrollbar-thumb-hover); }

/* Auto-hide variant */
.scroll-autohide { --color-scrollbar-thumb: transparent; }
.scroll-autohide:hover, .scroll-autohide:focus-within {
  --color-scrollbar-thumb: var(--color-scrollbar-thumb-visible);
}
```
Add `scrollbar-gutter: stable` on scroll containers to avoid layout shift. Never hide scrolling ability, only the visuals.

### Base styles and starter components
- Global reset, font loading (self-hosted or `font-display: swap`), visible focus ring, `prefers-reduced-motion` support, selection color.
- Starter primitives built on tokens only: Button, Input, Card, Modal, Spinner, EmptyState. Each has variants and all states (hover, focus, disabled, loading, error).
- A `/design-system` dev-only route (or Storybook if the user asks) that shows tokens and components so humans can see the system.

## Phase 5: Architecture and docs

### Folder structure (feature-based default)
```
src/
  app/            # app shell, providers, router
  features/
    <feature>/
      components/ hooks/ api/ types/ index.ts
  components/     # shared UI primitives (design system)
  hooks/          # shared hooks
  lib/            # utilities, API client, config
  styles/         # tokens.css, base.css
  types/          # shared types
  assets/
docs/
```
Naming: folders `kebab-case`; components `PascalCase.tsx`; hooks `useThing.ts`; utilities `camelCase.ts`; types `PascalCase`; constants `UPPER_SNAKE_CASE`; one component per file; test files next to the source (`Thing.test.tsx`).

### Files to write
1. **`CLAUDE.md`** (under 100 lines). Contains: what the app is, commands (dev, lint, typecheck, test, build), the 10 to 15 most important rules, and pointers to `docs/`. Include "Before finishing any task: run lint, typecheck, and tests." and "Never hard-code colors, spacing, or fonts; use tokens." and "Read `docs/design-system.md` before creating or changing UI."
2. **`docs/architecture.md`**: folder structure, dependency direction (app, features, shared, never the reverse), data flow, state management guidance, where new code goes.
3. **`docs/design-system.md`**: brand summary from the interview, token reference, component list with usage, do and don't examples, accessibility rules, how to add a new component.
4. **`docs/coding-standards.md`**: naming conventions, file size limit and what to do when exceeded, TypeScript rules, React patterns (composition over props explosion, custom hooks for logic, no business logic in JSX), error and loading state rules, import order, comment policy.
5. **`docs/workflow.md`**: branching, commit conventions, what the hooks do and why, how to run checks, definition of done, PR checklist.
6. **`README.md`**: quick start for a new developer in under 5 steps.

Write docs in plain, scannable language. Tell the reader the reason behind each rule in one short sentence.

## Phase 6: Verify

Run in order and fix failures before reporting: `install`, `lint`, `typecheck`, `test`, `build`. Then make a test commit attempt to confirm the hooks run (use a throwaway change and revert it). Start the dev server and confirm the app loads with the chosen brand colors and scrollbar.

## Final report (keep it short)
- What was created, in 4 to 6 plain bullets.
- How to start (`pnpm dev`) and where the docs are.
- Anything adjusted (for example a color changed for contrast) and why.
- One suggested next step (for example "build your first feature using `docs/architecture.md`").

## Rules for this skill
- Do not install or configure anything the user declined.
- Do not invent brand choices. If the user skipped brand questions, use the neutral defaults and say so.
- Prefer the latest stable versions, verified against current docs, not memory.
- If a step fails twice, stop and explain in plain language instead of retrying endlessly.
- Never commit or push on the user's behalf unless asked.
