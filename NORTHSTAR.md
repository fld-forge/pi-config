# NORTHSTAR - pi-config

Steering KPIs for this repository (personal backup/restore tool for the Pi configuration).
One North Star KPI per axis, plus supporting indicators. Every value is measured;
an unmeasured value is written as unmeasured, never invented. Updated whenever a
measurement changes category.

A `Current` value that a gate verifies on every run is written plain: it cannot
drift without turning something red. A value read by hand carries the date it
was read, because nothing keeps it current afterwards - the `Measurement`
column is how to refresh it, and a dated reading stays true even once stale.

## Speed

North Star KPI:

| KPI | Current | Target | Measurement |
| --- | --- | --- | --- |
| Fresh-machine restore time | not yet measured | < 30 min wall clock | Time the `README.md` "Restoring on a fresh machine" procedure end to end |

Supporting indicators:

| Indicator | Current | Target | Measurement |
| --- | --- | --- | --- |
| Test suite duration | 6.25 s (114 tests), read 2026-08-22 | < 5 s | `uv run pytest -q` (CI gate) |

Measurement cadence: CI runs on every push/PR to `main` and every Monday at
06:00 UTC - the weekly run catches bit-rot without anyone pushing.

## Security

North Star KPI:

| KPI | Current | Target | Measurement |
| --- | --- | --- | --- |
| Secrets in the repo | 0 (history audited) | 0, always | `sync.py` audit at each sync + gitleaks job in CI |

Supporting indicators:

| Indicator | Current | Target | Measurement |
| --- | --- | --- | --- |
| `auth.json` tracked by git | never | never | `sync.py` exclusion + `.gitignore` + e2e test |
| Release integrity (SBOM + provenance attestation) | v0.6.7: 6 assets, attestation and checksums pass, read 2026-08-22 | every release verified | `gh attestation verify <asset> --repo fld-forge/pi-config` for every release asset (see `SECURITY.md`) |
| Semgrep CE findings | 0 at adoption | 0 blocking | `uvx semgrep==1.173.0 scan --config p/python --metrics=off --error src scripts` in CI |
| Open vulnerability alerts / time-to-patch | baseline not yet recorded | record baseline, then 0 critical open | GitHub Security tab (CodeQL, `uv audit --locked`, pip-audit, Dependency Review, Dependabot, secret scanning) |

## Maintainability

North Star KPI:

| KPI | Current | Target | Measurement |
| --- | --- | --- | --- |
| Branch coverage | 95.30%, read 2026-08-22 | >= 90% (enforced floor) | every full `uv run pytest` run (pre-commit framework + CI + `just check`) |

Supporting indicators:

| Indicator | Current | Target | Measurement |
| --- | --- | --- | --- |
| Ruff selected-rule violations | 0 | 0 | `uv run ruff check .` (`just check`, pre-commit framework, and CI `quality`) |
| Static type diagnostics | 0 in ty and mypy at adoption | 0 blocking | both checkers in pre-commit, `just check`, and CI `quality` |
| Import architecture | 2/2 contracts kept; 0 TID251 violations | 0 violations | `uv run lint-imports` plus `uv run ruff check .` in every quality surface |
| src module / script size | max 199 / 8 lines, read 2026-08-22 | <= 200 / <= 20 | `tests/unit/test_standards.py` (the limit is a test) |
| Green tests | 114 (78 unit / 34 integration / 2 e2e) | 100% green, 3 levels | `uv run pytest` (pre-commit framework + CI) |

## Scalability

(For this tool, scalability means the sync/restore workflow keeps holding as the config grows.)

North Star KPI:

| KPI | Current | Target | Measurement |
| --- | --- | --- | --- |
| sync -> restore fresh-machine parity | proven (e2e, identical files) | stays proven at every commit | `tests/e2e/test_full_cycle.py` in CI |

Supporting indicators:

| Indicator | Current | Target | Measurement |
| --- | --- | --- | --- |
| `config/` snapshot freshness | latest tracked update on 2026-08-21, read 2026-08-22 | sync before every Pi update | `git log -1 --format=%as -- config/` after the README sync -> PR workflow |

A KPI that is always green effortlessly should be tightened; a KPI that is always red should be fixed or dropped.
