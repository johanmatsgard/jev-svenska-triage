# Build brief: jev-svenska-triage

## Goal
A small, clean TypeScript repo that evaluates TypeSafe's Jev on `eval/comments.jsonl` using the questions in `questions.json`, then writes results into README.md. Anyone reading it should be able to trust the numbers and rerun them in two minutes.

## Setup
- Install the TypeSafe skill first: `claude plugin marketplace add typesafe-ai/skills`, then read it before writing any API code. Do not guess request or response shapes.
- Node + TypeScript, minimal dependencies.
- Two providers behind one interface, selected by env:
  - TYPESAFE_API_KEY → direct TypeSafe API via the official SDK
  - AI_GATEWAY_API_KEY → Vercel AI Gateway, model `typesafe-ai/jev`, via AI SDK evaluate
- `questions.json` is the spec. Map it onto the SDK's exact question schema rather than assuming its field names match.
- Pin the model version you actually ran (e.g. jev-1.13) and record it in results.

## Scripts
- `npm run eval` does four things:
  1. runs all 100 comments, with concurrency limited and retries on 429/5xx
  2. saves raw responses to `results/raw.jsonl` so results can be re-scored without new calls
  3. writes `results/summary.json` and `results/calibration.svg` (10 confidence bins, accuracy per bin, bin counts shown)
  4. replaces the Status section in README.md with a Results table and a "Where it fails" section
- `npm run score` re-scores from raw.jsonl with no API calls.

## Metrics
- Accuracy for action, topic, and both nouls; all rows and hard-only
- Confusion matrix for action
- **Wrongly hidden complaints:** label kundservice, predicted hide. This is the safety metric.
- Threshold sweep for t from 0.5 to 0.99: accuracy above t, share above t, wrongly-hidden count above t
- Recommended t = the lowest t with zero wrongly-hidden complaints above it
- Latency p50/p95, total tokens, total cost at list price

## Rules
- Never change a label to make the numbers look better. If a label is wrong, fix it in a separate commit that explains why.
- No customer data, API keys or Elvy internal URLs in the repo.
- `.env.example` only. `.env` stays gitignored.
- Keep code readable. Reviewers will open it.

## Definition of done
`npm run eval` works from a fresh clone, the README has real results, the "Where it fails" section has 5 real misses written up, and CI runs `npm run score` against a committed `results/raw.jsonl`.
