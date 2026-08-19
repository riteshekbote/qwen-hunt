# Qwen Hunt — 24/7 AI bug-hunting automation (Qwen3 abliterated)

Multi-model bug-hunting automation running on GitHub Actions, powered by
**local Qwen3 abliterated models via Ollama** (no cloud API keys, uncensored
reasoning for offensive-security analysis).

## Models

| Model | Tag | Size | Role |
|---|---|---|---|
| Qwen 14B abliterated | `richardyoung/Qwen3-14B-Abliterated-GGUF:Q4_K_M` | ~9.1GB | Analyst + Triager |
| Qwen 8B abliterated | `richardyoung/Qwen3-8B-Abliterated-GGUF:Q4_K_M` | ~5.0GB | Reposcan triage |

## Pipeline (10-min cadence)

1. **hunt.yml** — analyst job per model (8-step methodology: DELTA → PRIORITIZE →
   HYPOTHESES → SELF-CRITIQUE → NEXT → LEARNING → RISK), aggregator merges + ranks
   hypotheses, passive verifier probes (scope-bound), single-commit push.
2. **reposcan.yml** (15-min) — clones target org repos, 50-pattern secret/endpoint
   grep, Qwen 8B triage of hits.
3. **triage.yml** (23,53-min) — 7-Question Gate validation of leads by Qwen 14B,
   VALID/INVALID/HOLD verdicts, valid-bugs register, knowledge-base learnings.
4. **sync-issues.yml** (hourly) — creates GH issues for hypotheses (human review).

## Setup

1. Edit `scope.yml` with the target program (name, platform, domains, github_orgs,
   excluded, probe_allow).
2. Push to GitHub — workflows run automatically.
3. Review `reports/valid-bugs.md` + Issues for human-submittable findings.

## Discipline

- Passive-first: verifier probes only hosts matching `probe_allow`.
- Secrets are never written in full (hashed with sha256 in outputs).
- Findings in `reports/hypotheses.md` are ranked by confidence; triage applies the
  7-Question Gate; human decides on submission.