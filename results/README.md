# Evaluation results

## Checklist

| Check | Status | Notes |
|-------|--------|-------|
| Static `scripts/checks/check-*.sh` | pending re-run | After h3 review fixes |
| Implementation rubric (`harbor check`) | pending | |
| Docker build (env + verifier) | pending re-run | |
| Oracle reward 1.0 | pending re-run | Prior h3 was 1.0 before JSON/env-path fixes |
| Nop reward 0.0 | pending re-run | Prior h3 was 0.0 before JSON/env-path fixes |
| Cheat `cheat/solve.sh` reward 0.0 | pending | Deterministic partial patch |
| Codex exploratory | wait | Only after Harbor validates |
| Codex `/run` ×3 fail | pending | |
| Claude `/run` ×3 fail | pending | Needs Claude OAuth |
| Codex `/cheat` reward 0 | pending | |
| Claude `/cheat` reward 0 | pending | |

## Hardening note

Codex (`gpt-5.6-sol`, xhigh) solved v1 in ~12m with reward 1.0. That attempt is preserved under `results/codex/preserved/codex-run-1-PASSED-need-harden` and **does not count**.

An exploratory post-partial-harden Codex run was **intentionally aborted** (Codex had already identified defects from remaining BUG labels). Preserved under `results/codex/preserved/codex-h-run-1-EXPLORATORY-ABORTED` — **does not count**.

Hardened h3 contract: tenant vs shared group-collapse precedence, synonym BFS with cycles/stopwords, invalid embedding handling via standard JSON (strings / null / empty / wrong dim / `1e309`), NFKC citation offsets into original text, weighted RRF, seeded fixtures (168 public / 82 variant docs), independent oracle, no BUG/BUGGY markers, no verifier env-path overrides.

## Commands used

```bash
for c in scripts/checks/check-*.sh; do bash "$c" tasks/hybrid-rag-ledger; done
harbor run -p tasks/hybrid-rag-ledger --agent oracle --env docker --yes -o results/validate/oracle-h3
harbor run -p tasks/hybrid-rag-ledger --agent nop --env docker --yes -o results/validate/nop-h3
```

## Agent trial log

See `results/codex/` and (later) `results/claude/`.
