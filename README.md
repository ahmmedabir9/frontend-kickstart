# Frontend Kickstart

A Claude Code plugin that starts a new React front-end app the right way. It asks a few plain-language questions, one at a time, about your tools, brand, and conventions, then builds a production-ready project with your own look and feel.

## What you get

- A React + TypeScript app (Vite or Next.js) with Tailwind or your chosen styling
- ESLint, Prettier, and Git hooks (Husky + lint-staged) set up and working
- A design system built from your brand answers: color scales, tokens, custom scrollbar, starter components
- Documentation for humans and AI: `CLAUDE.md`, architecture, design system, coding standards, workflow
- Your answers saved in `kickstart.config.json` so you can re-run or re-theme later

## Install

In Claude Code:

```
/plugin marketplace add ahmmedabir9/frontend-kickstart
/plugin install frontend-kickstart@frontend-kickstart
```

Restart Claude Code if the skill doesn't show up right away.

## Update

```
/plugin marketplace update frontend-kickstart
```

Then update the plugin from the `/plugin` menu, and restart Claude Code.

## Use

In an empty folder, open Claude Code and say:

> Start a new React app with frontend-kickstart.

Or run `/frontend-kickstart:frontend-kickstart`. Claude asks whether you want a Quick or Detailed interview, then builds the project.

## Develop

The skill lives in `skills/frontend-kickstart/SKILL.md`. To test local changes without publishing:

```
claude --plugin-dir /path/to/frontend-kickstart
```

## Status

First draft. Feedback and changes are welcome.
