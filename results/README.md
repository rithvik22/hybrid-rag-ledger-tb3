# Evaluation results

## Checklist

| Check | Status | Notes |
|-------|--------|-------|
| Static `scripts/checks/check-*.sh` | pass | All checks green locally |
| Docker build | pass | Via Colima + Docker CLI |
| Oracle reward 1.0 | pass | `jobs/2026-09-22__13-56-10` mean=1.0 |
| Nop reward 0.0 | pass | `jobs/2026-09-22__13-56-37` mean=0.0 |
| Codex `/run` ×3 fail | pending | Needs ChatGPT Plus / Codex auth |
| Claude `/run` ×3 fail | pending | Needs Claude Code OAuth |
| Codex `/cheat` reward 0 | pending | |
| Claude `/cheat` reward 0 | pending | |

## Commands used

```bash
for c in scripts/checks/check-*.sh; do bash "$c" tasks/hybrid-rag-ledger; done
harbor run -p tasks/hybrid-rag-ledger --agent oracle --env docker --yes
harbor run -p tasks/hybrid-rag-ledger --agent nop --env docker --yes
```

## Failure analysis (draft)

_To be completed after agent trials._

Expected failure modes for strong agents on this task:

1. Fixing eligibility but scoring on the full corpus first (wrong ranks)
2. Correct cosine but wrong BM25 DF/IDF (or vice versa)
3. RRF with `1/rank` or 0-based ranks instead of `1/(rrf_k+rank)`
4. Deduping on raw text so whitespace near-duplicates both survive
5. Hardcoding public `results.json` without repairing modules (fails hidden variant + unit contracts)
