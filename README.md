# Frontend Kickstart

A Claude Code skill that starts a new React front-end app the right way. It asks a few plain-language questions about your tools, brand, and conventions, then builds a production-ready project with your own look and feel.

## What you get

- A React + TypeScript app (Vite or Next.js) with Tailwind or your chosen styling
- ESLint, Prettier, and Git hooks (Husky + lint-staged) set up and working
- A design system built from your brand answers: color scales, tokens, custom scrollbar, starter components
- Documentation for humans and AI: `CLAUDE.md`, architecture, design system, coding standards, workflow
- Your answers saved in `kickstart.config.json` so you can re-run or re-theme later

## Install

Clone this repo into your Claude Code skills folder.

Personal (all projects):

```bash
git clone https://github.com/ahmmedabir9/frontend-kickstart.git ~/.claude/skills/frontend-kickstart
```

Single project:

```bash
git clone https://github.com/ahmmedabir9/frontend-kickstart.git .claude/skills/frontend-kickstart
```

## Use

In an empty folder, open Claude Code and say something like:

> Start a new React app with frontend-kickstart.

Claude will ask whether you want a Quick or Detailed interview, then build the project.

## Status

First draft. Feedback and changes are welcome.
