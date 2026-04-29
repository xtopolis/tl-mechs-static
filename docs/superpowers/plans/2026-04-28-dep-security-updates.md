# Dependency & Security Updates Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Merge all open Dependabot PRs one at a time, verifying each build passes before moving on, then raise PRs for any remaining security alerts.

**Architecture:** PRs 49–53 were lockfile-only patches. PR 54 (Astro 5→6) required a fresh lockfile + Node 20→22 workflow update + removal of the `@astrojs/sitemap: 3.5.1` override (which was Astro 5-era and breaks Astro 6). The `zod-to-json-schema: 3.24.3` override stays. For remaining security alerts, raise PRs but do NOT merge.

**Tech Stack:** Astro 6, Starlight 0.38, Node 22, GitHub Actions, gh CLI, Dependabot

**Build verification rule:** After every push to main, monitor the GitHub Actions deploy pipeline to completion before doing anything else. Command:
```bash
# Get latest run ID for the deploy workflow, then watch it
RUN_ID=$(gh run list --repo xtopolis/tl-mechs-static --limit 1 --json databaseId --jq '.[0].databaseId')
gh run watch $RUN_ID --repo xtopolis/tl-mechs-static
# On failure, get logs:
gh run view $RUN_ID --repo xtopolis/tl-mechs-static --log-failed
```

---

## Context & Key Decisions

- `src/content.config.ts` uses `docsLoader()` + `docsSchema()` — already on new Content Layer API, Astro 6 compatible ✓
- No `legacy.collections` flag used anywhere
- `gh pr merge` fails (token scope); merges done via local `git merge --no-ff` + `git push`
- Dependabot alerts API returns 403 — use `npm audit` and `gh pr list --author app/dependabot` instead
- Static site on GitHub Pages — server-side CVEs irrelevant; build-tool CVEs matter only for CI safety
- **LESSON LEARNED:** The `@astrojs/sitemap: 3.5.1` override was Astro 5-era. Astro 6 + Starlight 0.38 require `^3.7.1`. Override removed in the fix commit. Keep `zod-to-json-schema: 3.24.3` override.

---

## PR Merge Order (completed → pending)

| # | PR | Change | Status | Notes |
|---|-----|--------|--------|-------|
| 1 | #49 | defu 6.1.4→6.1.7 | ✅ merged | |
| 2 | #50 | smol-toml 1.6.0→1.6.1 | ✅ merged | |
| 3 | #51 | picomatch 4.0.3→4.0.4 | ✅ merged | |
| 4 | #52 | vite 6.4.1→6.4.2 | ✅ merged | |
| 5 | #53 | postcss 8.5.8→8.5.12 | ✅ merged | |
| 6 | #54 | astro 5→6 + starlight 0.36→0.38 + Node 22 | ✅ committed | Build failed — fix in progress |
| 7 | fix | Remove `@astrojs/sitemap: 3.5.1` override | ⏳ pending push | Fixes the Astro 6 build failure |

---

## Phase 1: Merge Dependency Bump PRs

### Tasks 1–5: PRs #49–53 ✅ COMPLETE

All merged to main via `git merge --no-ff` + `git push`. No build failures (those commits only changed `package-lock.json`; no deploy workflow triggers on lock-only changes — but GitHub Dependabot update runs did succeed).

### Task 6: Astro 5→6 upgrade (PR #54) — fix in progress

**What was done:** Committed `4051f7e` — Astro 6.1.10, Starlight 0.38.4, workflow Node 20→22, fresh lockfile.

**Build failure:** `@astrojs/sitemap@3.5.1` (overridden) crashes in Astro 6's `astro:build:done` hook: `Cannot read properties of undefined (reading 'reduce')`. Starlight 0.38 requires `^3.7.1`.

**Fix ready locally (uncommitted):**
- `package.json`: removed `"@astrojs/sitemap": "3.5.1"` from overrides
- `package-lock.json`: fresh install resolves `@astrojs/sitemap@3.7.2`
- `npm audit`: 0 vulnerabilities

**Steps to complete:**

- [ ] **Step 1:** Commit and push the fix
  ```bash
  git add package.json package-lock.json
  git commit -m "Fix Astro 6 build: remove @astrojs/sitemap override, update to 3.7.2"
  git push origin main
  ```

- [ ] **Step 2:** Monitor the deploy pipeline to completion
  ```bash
  RUN_ID=$(gh run list --repo xtopolis/tl-mechs-static --limit 1 --json databaseId --jq '.[0].databaseId')
  gh run watch $RUN_ID --repo xtopolis/tl-mechs-static
  ```
  Expected: build passes, site deploys to GitHub Pages.

- [ ] **Step 3:** If build fails again, check logs before touching anything else
  ```bash
  gh run view $RUN_ID --repo xtopolis/tl-mechs-static --log-failed
  ```

- [ ] **Step 4:** Update CLAUDE.md to note the removed override

- [ ] **Step 5:** Close or note PR #54 and #55 as superseded (changes already in main)

---

## Phase 2: Remaining Security Alerts

**Goal:** After the build is green, check what's left and raise PRs for any remaining issues. Do NOT merge.

Current state (after PRs 49–53 + Astro 6 commit):
- GitHub reported 3 moderate vulnerabilities remain
- `npm audit` locally shows 0 (fresh lockfile has h3@1.15.11, postcss@8.5.12, etc.)
- Dependabot PR #55 (h3 1.15.8→1.15.11) is open but our lockfile already has 1.15.11

- [ ] **Step 1:** Once build is green, re-check npm audit
  ```bash
  npm audit
  ```

- [ ] **Step 2:** Check open Dependabot PRs
  ```bash
  gh pr list --repo xtopolis/tl-mechs-static --author app/dependabot
  ```
  For each open PR: assess if it affects the deployed static site or only build pipeline.

- [ ] **Step 3:** For each legitimate remaining alert, raise a PR (do not merge):
  ```bash
  git checkout main && git pull
  git checkout -b fix-<package>-security
  npm install <package>@<safe-version>
  git add package-lock.json package.json
  git commit -m "Fix <package> security vulnerability"
  git push -u origin fix-<package>-security
  gh pr create --repo xtopolis/tl-mechs-static \
    --title "Fix <package> security vulnerability" \
    --body "Addresses CVE-XXXX. Package only affects build pipeline, not deployed artifact."
  ```

---

## Status Log

| Date | Action | Result |
|------|--------|--------|
| 2026-04-28 | Plan created | — |
| 2026-04-29 | PR #49 (defu) merged | ✅ |
| 2026-04-29 | PR #50 (smol-toml) merged | ✅ |
| 2026-04-29 | PR #51 (picomatch) merged | ✅ |
| 2026-04-29 | PR #52 (vite) merged | ✅ |
| 2026-04-29 | PR #53 (postcss) merged | ✅ |
| 2026-04-29 | Astro 5→6 + Node 22 committed | ✅ pushed, ❌ build failed |
| 2026-04-29 | Fix: remove @astrojs/sitemap override | ✅ build passed |
| 2026-04-29 | CLAUDE.md updated (override note corrected) | ✅ |
| 2026-04-29 | Phase 2: security alerts review | ✅ 0 vulns; PR #55 stale (h3 already 1.15.11 in lockfile) |
