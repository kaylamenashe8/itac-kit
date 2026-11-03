# <!-- FILL: project name --> — stable Playwright suite

<!-- FILL: one-line summary, e.g. "An inherited flaky Playwright/TS suite for <product>, made stable: 5/5 green gate, retries 0, 0 heals." -->

[![CI](<!-- FILL: ci badge -->)](<!-- FILL: ci link -->) [![Stability Gate](<!-- FILL: gate badge -->)](<!-- FILL: gate run -->) [![Report](https://img.shields.io/badge/report-GitHub%20Pages-blue)](<!-- FILL: pages -->)

## Results at a glance
| Metric | Before (`baseline`) | After (`main`) |
|---|---|---|
| Tests | <!-- FILL --> | <!-- FILL --> |
| Passing | | |
| Flaky | | 0 |
| Lint errors | | 0 |
| Hard waits | | 0 |
| Suite run time | | |
| Stability Gate | — | **5/5 green, retries 0, 0 heals** |

## Links
| What | Link |
|---|---|
| Stability Gate run (5 parallel jobs) | <!-- FILL --> |
| HTML report for that run | <!-- FILL --> |
| Report history index | <!-- FILL --> |
| Test plan (STP) | [docs/STP.md](docs/STP.md) |
| Product bugs (SpiraTest) | [docs/BUGS.md](docs/BUGS.md) |
| Automation fixes | [docs/FIXES.md](docs/FIXES.md) |
| Decision log | [docs/DECISIONS.md](docs/DECISIONS.md) |
| Summary video | <!-- FILL --> |

## How to run
**Local**
```bash
nvm use --lts && npm ci && npx playwright install --with-deps chromium
cp .env.example .env          # set BASE_URL (+ TEST_USER / TEST_PASSWORD)
npm run verify                # lint + typecheck + @smoke
npm test                      # full suite
npm run test:10x -- tests/e2e/<file>.spec.ts   # promotion check: 10/10, retries 0
```
**Docker** (same image as CI)
```bash
docker compose run --rm test
docker compose run --rm update-snapshots      # visual baselines, Linux only
```
**CI** — every push/PR runs `ci.yml`: lint, typecheck, 4 shards, merged report on Pages, Slack, triage on failure.

**Stability Gate**
```bash
gh workflow run stability-gate.yml -f mode=parallel     # 5 full runs at once (default evidence)
gh workflow run stability-gate.yml -f mode=sequential   # 5 chained runs (backup)
scripts/gate-5x.sh                                       # 5 separate ci.yml runs (backup evidence)
```

## Architecture
<!-- FILL: tree summary --> One paragraph per layer and the full tree: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).
- **Config** — zod-validated env, typed; no secrets in code.
- **Pages + components** — intent-level page objects; each element declared once with healer alternates.
- **Fixtures** — pages, `data` (RUN_ID factories), `healer`, `api`, auto page-error capture.
- **Tests** — projects `setup`, `chromium`, `api`, `mobile`; tags @smoke @regression @visual @a11y @api @known-bug.

## Stability approach
- **Root causes found:** <!-- FILL from FIXES summary --> 
- **Triage ladder:** isolated rerun -> rule classifier -> self-heal -> crawler diagnose -> Claude; every failure gets one verdict (flaky / automation / product / environment).
- **Self-healing:** allowed and logged in CI; any heal fails the gate (`HEAL_MODE=strict`); every heal became a committed locator fix.
- **Gate:** 5 full-suite runs of one commit, retries 0, 0 heals ([D2](docs/DECISIONS.md)).

## Known product bugs
Skipped tests show as **skipped**, not passed, in the gate. Each is filed in SpiraTest.
| Spira ID | Bug | Skipped test | Jam |
|---|---|---|---|
| <!-- FILL --> | | | |

## Bonus features
| Feature | Where | Evidence |
|---|---|---|
| Docker (pinned Playwright image, CI + local) | `Dockerfile`, `docker-compose.yml` | CI jobs run in the container |
| Visual tests (Docker baselines, masks) | `tests/visual/` | <!-- FILL --> |
| Accessibility (axe, critical + serious) | `tests/a11y/` | |
| Slack notifications (run, gate, triage) | `ci.yml`, `stability-gate.yml` | <!-- FILL: 2-3 screenshots --> |
| Self-healing locators | `src/utils/healing.ts` | `heal-report.json` in CI artifacts |
| Claude triage in CI | `triage.yml`, `triage/` | <!-- FILL: verdict example --> |
| Crawler test generator | `crawler/` | [promotion report](crawler/out/promotion-report.md) |
| API layer | `src/api/`, `tests/api/` | [API map](docs/API_MAP.md) |

## AI usage
- **Tools:** Claude Code with subagents (`.claude/agents/`), Playwright MCP and Playwright test agents, `anthropics/claude-code-action` in triage, Messages API fallback.
- **What AI created or changed:** <!-- FILL per folder, from docs/AI_LOG.md -->
- **Key prompts:** see [docs/AI_LOG.md](docs/AI_LOG.md#key-prompts) (every prompt is logged automatically, secrets redacted).
- **Human review:** no AI-generated code merged without Kayla's review, after the agent's 2-3 self-reviews and its own e2e run. Reviewed and rejected: <!-- FILL -->

## Prepared before the competition (disclosure)
This repo started from the competition project (tag `baseline`). The tooling kit — agent definitions, CI workflows, Docker setup, framework skeleton (config, fixtures, BasePage, healer, data factory), triage ladder, crawler, Spira filing script, docs templates and the Control Center dashboard — was **prepared before the competition** in a separate repo (`itac-kit`) and applied with `scripts/apply-kit.sh` at the start (D12). All product-specific tests, page objects, fixes, bugs and documentation content were made during the competition; see `git log baseline..main`.

## Decision log
[docs/DECISIONS.md](docs/DECISIONS.md) — D1-D13 plus the judges' answers and decisions made on the day.
