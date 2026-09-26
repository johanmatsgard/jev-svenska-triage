# 🇸🇪 jev-svenska-triage

**Can a decision model moderate Swedish social comments well enough to act on its own?**

I run marketing at [Elvy](https://www.elvyenergy.com/). Our paid social ads collect hundreds of Swedish comments: real questions, skeptics, angry customers, trolls and scam bots. Each one needs the right move, and the wrong move is expensive. Hiding a genuine complaint is a brand problem. Letting a phishing comment sit under an ad is worse.

This repo tests [Jev](https://typesafe.ai), TypeSafe's System One model, on that job. It uses 100 hand-labeled Swedish comments and real house rules, and it measures one thing above accuracy: **does Jev's confidence tell you when it's safe to act without a human?**

## The decision

Every comment gets five typed questions in one call ([`questions.json`](./questions.json)):

| Question | Type | Values |
|---|---|---|
| `action` | Choice | reply_cta · reply_no_cta · kundservice · hide · ignore |
| `topic` | Choice | price · contract · installation · performance · service · trust · other |
| `genuine_complaint` | Noul | yes / no |
| `existing_customer` | Noul | yes / no |
| `purchase_intent` | Score | none → ready |

The labels encode Elvy's actual moderation rules:
- Hide substanceless hostility, never block.
- **Never hide a genuine complaint, however rude.**
- Skeptics and mockers still get a reply with a call to action, because the reply is written for everyone reading the thread.
- Existing-customer praise and Gotland get a reply without one.

## The eval set

[`eval/comments.jsonl`](./eval/comments.jsonl): 100 synthetic comments written to mirror real traffic. No customer data. A third are tagged `hard`. These include rude complaints that must *not* be hidden, sarcasm with a real question inside, a phishing comment impersonating Elvy support, and Gotland place names that never say "Gotland".

| action | n |
|---|---|
| reply_cta | 38 |
| hide | 23 |
| reply_no_cta | 15 |
| kundservice | 14 |
| ignore | 10 |

## Status

The question set and the labeled eval are done. The eval harness is in progress, and results will land here with the exact model version they were run against.

What will be measured:
- Action accuracy, overall and on the hard third
- **Genuine complaints wrongly hidden** (the safety metric)
- Calibration: does accuracy rise with Jev's confidence?
- The threshold *t* above which Jev can act without a human, chosen as the lowest *t* with zero wrongly-hidden complaints
- Latency and cost for the full set

## Why this matters beyond Elvy

Most published Jev evals are in English. Swedish is a small language full of sarcasm, dialect and compound words, so it's a useful stress test for any team outside the English-speaking world deciding whether a typed decision model can sit in production.

---
Built by [Johan Matsgård](https://github.com/johanmatsgard), CMO at Elvy.
