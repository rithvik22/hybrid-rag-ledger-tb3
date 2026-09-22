# Klavis AI — Terminal-Bench 3 Take-Home

**Author:** Rithvik Reddy Velapati ([rithvik22](https://github.com/rithvik22))  
**Task:** `tasks/hybrid-rag-citation-ledger`  
**Purpose:** Original Terminal-Bench 3 task for the Klavis Founding Engineer evaluation.

This repository is a standalone submission (not a PR to Terminal-Bench). It contains the task package, evaluation commands, and (after runs) trial results plus failure analysis.

## Task summary

Repair a buggy multi-tenant hybrid RAG toolkit (`BM25` + dense cosine + RRF + citation spans) so batch retrieval matches a strict contract. The verifier uses an independent oracle and a hidden variant corpus to prevent hardcoding.

## Layout

```
tasks/hybrid-rag-citation-ledger/
  instruction.md
  task.toml
  README.md
  environment/     # agent-visible broken toolkit + public data
  solution/        # oracle repair
  tests/           # separate verifier image + independent oracle
  cheat/           # incomplete adversarial attempt (should score 0)
results/           # trial logs / summaries (filled after Harbor runs)
```

## Prerequisites

- Docker running
- Harbor (`uv tool install harbor`)
- For agent trials: Codex (ChatGPT Plus) and Claude Code OAuth as specified by Klavis

## Validation commands

```bash
# Static checks (from this repo root; scripts vendored from TB3)
for c in scripts/checks/check-*.sh; do
  bash "$c" tasks/hybrid-rag-citation-ledger || exit 1
done

# Oracle (must reward 1.0)
harbor run -p tasks/hybrid-rag-citation-ledger --agent oracle --env docker --yes

# Nop (must reward 0.0)
harbor run -p tasks/hybrid-rag-citation-ledger --agent nop --env docker --yes
```

## Required agent trials (Klavis / TB3 CI defaults)

Standard `/run` — each config **3 times**, all must **genuinely fail**:

```bash
# Codex x3
harbor run -p tasks/hybrid-rag-citation-ledger \
  --agent codex --model openai/gpt-5.6-sol \
  --env docker --yes --ae CODEX_FORCE_AUTH_JSON=1 --ak reasoning_effort=xhigh

# Claude Code x3
harbor run -p tasks/hybrid-rag-citation-ledger \
  --agent claude-code --model anthropic/claude-opus-5 \
  --env docker --yes --ae CLAUDE_FORCE_OAUTH=1 \
  --ae CLAUDE_CODE_OAUTH_TOKEN=<YOUR_OAUTH_TOKEN> --ak reasoning_effort=max
```

Adversarial `/cheat` — each once, **zero reward**:

```bash
harbor run -p tasks/hybrid-rag-citation-ledger \
  --agent codex --model openai/gpt-5.6-sol \
  --env docker --yes --ae CODEX_FORCE_AUTH_JSON=1 --ak reasoning_effort=xhigh
  # plus whatever --cheat / adversarial flag your Harbor/TB3 version exposes

harbor run -p tasks/hybrid-rag-citation-ledger \
  --agent claude-code --model anthropic/claude-opus-5 \
  --env docker --yes --ae CLAUDE_FORCE_OAUTH=1 \
  --ae CLAUDE_CODE_OAUTH_TOKEN=<YOUR_OAUTH_TOKEN> --ak reasoning_effort=max
```

> Note: Exact `/cheat` CLI flags follow current Harbor/TB3 CI. Document the precise commands used in `results/`.

## Results & failure analysis

See [`results/README.md`](results/README.md) after trials are executed.

## License

You may use/review this work for the Klavis hiring evaluation only as stated in their assignment terms. Copyright remains with the author.
