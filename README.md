# jev-svenska-triage

Testing whether TypeSafe's [Jev](https://typesafe.ai) can handle comments on our Swedish social media ads well enough to act without a person checking every one.

At Elvy our ads get a steady stream of comments: questions, skeptics, unhappy customers, trolls and the occasional scam bot. Each needs a different response. The mistake I care most about is hiding a real complaint, so that's what I'm measuring hardest. The other question is whether Jev's confidence can tell us which comments are safe to handle automatically.

## Questions

Every comment gets five questions in one call ([questions.json](./questions.json)):

| Question | Type | Answers |
|---|---|---|
| `action` | Choice | reply_cta, reply_no_cta, kundservice, hide, ignore |
| `topic` | Choice | price, contract, installation, performance, service, trust, other |
| `genuine_complaint` | Noul | yes / no |
| `existing_customer` | Noul | yes / no |
| `purchase_intent` | Score | none to ready |

The labels follow the rules our team already uses:

- Hide pure hostility, don't block
- Never hide a real complaint, however rude
- Skeptics still get a reply with a call to action, since the reply is really for everyone else reading
- Existing customers praising us, and people on Gotland, get a reply without one

## Test set

[eval/comments.jsonl](./eval/comments.jsonl) has 100 comments I wrote to resemble what we actually get. No real customer data. About a third are marked hard: rude complaints that shouldn't be hidden, sarcasm with a real question in it, a phishing comment pretending to be Elvy support, and Gotland towns that never mention Gotland.

| action | count |
|---|---|
| reply_cta | 38 |
| hide | 23 |
| reply_no_cta | 15 |
| kundservice | 14 |
| ignore | 10 |

## Status

Questions and test set are done. The test harness is next, and results will go here along with the model version they were run against. I'll be looking at accuracy overall and on the hard cases, how many real complaints end up hidden, whether accuracy rises with Jev's confidence, and at what confidence level it's safe to act automatically.

## After the test

If Jev holds up here, I want to use it inside Apollo, our marketing platform: to decide which model handles a task, to check generated copy against our house rules before it goes out, and later to pre-sort incoming leads. I'm starting with public comments and generated copy, so no customer data leaves Elvy before we have a data processing agreement in place.

## Why Swedish

Most published Jev tests are in English. Swedish comments are short, often sarcastic and full of compound words, which makes them a decent test for anyone outside English wondering whether this works for them.
