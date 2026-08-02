---
title: "Netlify's Hot AR Summer Challenge: I Wasn't Going to Enter. 33 Apps Later, I'm So Glad I Did"
published: true
tags: showdev, ai, webdev, buildinpublic
cover_image:
canonical_url: https://dev.to/earlgreyhot1701d/netlifys-hot-ar-summer-i-wasnt-going-to-enter-33-apps-later-heres-the-bill-1d25
series: Hot AR Summer
---

On July 31 I was at date night dinner with my wife in LA. We sat down at 8. The plan was simple: enjoy dinner, drive the hour home by 11, post my last app, close out the month.

It was a five course tasting menu. At 9:15 we had finished exactly one course.

I did the math at the table. Four courses to go, an hour of driving after that, and a deadline that did not care about either. So I picked up my phone. Not my laptop, which was at home. My phone.

I built the last one right there, sitting across from my wife, between courses. Claude for the build. Then I deployed it from the same phone through Netlify Agent Runners running Gemini. A confetti canvas, a terminal card, a certified stamp, and a deterministic serial number, because the closing piece had to prove its own work too.

It went live. On the first try. From a restaurant table.

Then I went to submit it and found there was nowhere to put it. The window had closed. My stomach dropped somewhere around the second course.

We left at 11:30 and got home at 12:30 in the morning. Thirty-three apps made the wall. The thirty-fourth exists, works, and was never counted.

That is the ending, so I am going to start there and work backward.

## I wasn't going to enter

Netlify ran a thing called [Hot AR Summer](https://hot-ar-summer.netlify.app/) through July. Build with Agent Runners, ship a small app, put it on the wall. Two apps a day allowed. I saw it and thought, that is a nice idea for people with more time than me.

Then I built one. Then the next day I built another one. Then it stopped being a decision.

By the end I had [33 apps on my builder page](https://hot-ar-summer.netlify.app/showcase/builder/la-shara-cordero), out of a showcase somewhere north of 200 total from everyone who played. A worry filing system with real Dewey Decimal call numbers. A diner wheel that decides dinner by how long you hold the button. A quiz that tells you which dead language you would think in. A goth subtype sorter I built for my wife. A basketball dashboard about Luka. A coin flip that shows its arithmetic.

None of them are big. That was the point. One screen, one idea, one self-contained `index.html`, one deploy.

What I did not expect was that the constraint I picked for aesthetic reasons would turn out to be a budget decision.

## The insides are deterministic, and that started as a cost decision

Here is the rule I ran all month: **no `Math.random` in any selection logic.**

Every app decides its output from the input. Type the same worry into Dewey, get the same call number. Hold the dinner wheel for the same 1.8 seconds, get the same supper. Press the Honest Coin at the same millisecond, get the same face and the same hash to prove it. The apps hash their inputs, run a fixed rule table, and show you the derivation.

I like this for philosophical reasons. I came to code from the courtroom, and in that world a confident wrong answer is worse than no answer. A receipt you can check beats a result you have to trust.

But the reason it survived contact with a credit meter is plainer than that.

**A deterministic app calls a model zero times after it is built.**

All of the AI cost is in construction. Once the thing is on Netlify it is a static file doing arithmetic in the visitor's browser. There is no inference at runtime. There is no API key. There is nothing to rate limit, nothing to cache, nothing that gets more expensive when more people show up.

I did not fully appreciate what that meant until I opened the billing page.

## The bill: $44, and 73 percent of it never touched a user

Across July I paid $44 in top-ups. My current billing period shows 3,990 credits consumed. Here is where every one of them went.

| Line item | Credits | Share |
|---|---:|---:|
| AI inference (Agent Runners) | 2,899.6 | 72.7% |
| Production deploys (41) | 615 | 15.4% |
| Agent Runners compute (47.41 GB-hrs) | 474.1 | 11.9% |
| Web requests (3,160) | 0.6 | 0.02% |
| Bandwidth (0.03 GB) | 0.7 | 0.02% |

That works out to about **121 credits per app**, or roughly **$1.33 an app** if you divide the top-ups by 33.

Three quarters of my spend was the model reading and writing code. Another quarter was deploying and the machine time to do it. And then there is that fourth row, which I want to sit on for a second.

## The cheapest thing I did all month was actually serve the apps

**3,160 web requests. Six tenths of one credit.**

Sixty percent of a single credit, to serve every visit to every app I shipped, for a month. That is not a rounding error in my bill, it *is* the rounding error. Serving the apps cost less than one twentieth of what a single deploy cost.

Here is the counterfactual that made me put my coffee down. Suppose I had built these the way a lot of "AI app" demos get built, with a model call on every visit. Some little inference to generate the flavor text instead of computing it.

Using my own measured Gemini rate from this month (2,254.97 credits for 16M tokens) and a conservative small call of about 1,200 tokens per visit, those same 3,160 visits would have run roughly **534 credits**. Against the 0.6 I actually paid.

That is about **890 times more expensive**, and it would have cost nearly as much as all 41 of my deploys combined. At my actual traffic. Which is small. That number does not stay still if anything ever catches on.

The 0.6 credits is measured. The 534 is my estimate, using my own token pricing and an assumed call size, so argue with it if you want.

Either way the difference is not really about my traffic, it is about what happens next. **My apps cost the same to run whether ten people visit or ten thousand. An app that calls a model on every visit gets more expensive with every single visitor.** One is a flat line. The other climbs forever. I got the flat line for free by being stubborn about `Math.random`.

## Gemini did the work. The expensive model did not do more work, it just cost more.

I ran two providers through Agent Runners this month, and the split is instructive.

| Provider | Tokens | Cost | Effective rate |
|---|---:|---:|---:|
| Google (Gemini) | 16M | $12.53 | ~$0.78 per M |
| OpenAI | 2.6M | $3.76 | ~$1.45 per M |

OpenAI cost me about **1.8x per token** for the same category of work: generate a single-file HTML toy. It is not that it did a bad job. Scrivener, my DOS-terminal typing tutor, came out of Codex and it is one of the better-built things on the wall. It just is not 1.8x better at making a small deterministic app, and when you are shipping one a day that multiple compounds.

So the rule I landed on mid-month, and wrote into my tracker so I would stop relitigating it: **default to Gemini, sketch it out by hand first, one deploy, do not re-prompt a working app.**

That last clause is the one that actually saved money. Costs are per run *and* per deploy at 15 credits each. Every "hmm let me just tweak that" is a triple charge: new run, new tokens, new deploy. I finished the month at **41 deploys for 33 apps**, which is 1.24 deploys per app. That number is the whole discipline in one statistic.

One more thing buried in the token data that surprised me. Of the 18.6M tokens I burned, only **323K were output**. That is **1.7 percent**. Ninety-eight percent of what I paid for was the model *reading*: my spec, the existing file, the context. The code it wrote was almost free.

If you want to spend less, write a tighter spec. That is the lever. Not a shorter app.

## Three tools, three jobs, and none of them doing the other one's work

The workflow that emerged, mostly by accident:

**Claude for brainstorm and architecture.** What is the idea, what is the rule table, what is the deterministic engine, what is the voice. This is where I argued about whether the app was any good before spending a credit on it.

**ChatGPT for the HTML.** Hand it a tight spec with the guardrails baked in (no `Math.random`, `textContent` not `innerHTML`, no storage, no fetch, must fit one screen) and let it produce the file.

**[Netlify](https://www.netlify.com/) Agent Runners for deploy.** One runner, one deploy, done.

I want to be specific about this part, because it is the reason a daily streak was even possible for someone with a full time job. Deploying was not a project. There was no pipeline to babysit, no build config to fight, no ceremony. Point a runner at the file and it is live on a URL you can send to your wife thirty seconds later. Thirty-three times in a row, including one time from my phone at a restaurant table between courses.

If you are new, this is the on-ramp I would hand you. If you are not new but you move fast and you resent tooling that slows you down, same recommendation for the opposite reason. The whole reason I shipped 33 small things instead of overthinking three big ones is that deploying cost me nothing emotionally. Fifteen credits and no stress.

The seam that worked: I figured out the whole thing on paper before running anything, because thinking is free and inference is not. The seam that cost me: any time I let a second pass happen because I did not specify well enough the first time.

I have started sorting those second passes into a taxonomy, because they are not all the same failure. Agent-miss (it ignored the spec), spec-miss (I never said it), scope-change (I changed my mind), operator-error (I ran the wrong thing). Only one of those four is the model's fault, and it is not the most common one.

## The apps I would show you first

These are the ones I would hand you first. All free, all instant, none of them want your email.

**[What's for Dinner?](https://whats-for-dinner-tonight.netlify.app/)**
A 1950s chrome diner wheel. Press and hold to wind it up, release, and it clatters to a stop on tonight's supper. The longer you hold, the further it spins, and the same hold always lands the same meal. There is a bell, an order ticket, and the button afterward says "Spin again (we both know why)." Cereal is one of the eight outcomes and I refuse to apologize for it.

**[Crypt Type](https://crypt-type.netlify.app/)**
Which goth subtype are you. Eleven results, each with a soundtrack and a receipt showing what you scored highest on. I built this one for my wife, who is the household authority on the subject and who tested it before anyone else got to.

**[Saint of Small Things](https://saint-of-small-things.netlify.app/)**
Name something small you are struggling with and get canonized as its patron saint, feast day included. Patron saint of the unanswered email. It is silly and then it is somehow not.

**[Vivant Linguae Mortuae](https://vivant-linguae-mortuae.netlify.app/)**
Eight questions, and it tells you which dead language you would think in. The most-shared one of the whole set, and the one people argue with me about.

**[Mood Ring](https://mood-ring-index.netlify.app/)**
Press the stone and hold it. Your mood comes from how long you held, not from a dice roll, so if you sit with it you get a different answer than if you tap. Turns out that is the honest mechanic for a mood ring anyway.

**[How Long Ago Was That](https://how-long-ago.netlify.app/)**
Type a year and it tells you the distance in something your body can feel. Type 1994 and learn it is closer to the moon landing than to today. This is the one Netlify demoed on their livestream, where it repeated itself on air and taught me something I had to write up separately.

**[Same Sky](https://same-sky.netlify.app/)**
Where the sun is right now, wherever you are, from real solar geometry instead of a weather API. Pick Svalbard in July and it will tell you the sun does not set there today, which is correct and still feels like a bug.

**[With Appreciation](https://with-appreaciation.netlify.app/)**
Name three things you are grateful for and receive a curated, properly sourced quote. It also tells you that others are here in quiet company, without a fake live counter, because I was not going to invent traffic.

**[Luka Fit Index](https://luka-fit-index.netlify.app/)**
A sports dashboard that shows you its own bias instead of hiding it. This was the first thing I shipped for Hot AR Summer, before I knew there would be thirty-two more.

**[Messi vs. Yamal](https://messi-or-yamal.netlify.app/)** and **[The Decision 2026](https://the-decision.netlify.app/)**
Two generations meeting in a final, and LeBron's career treated as a unit of measurement. The second one is funnier than it has any right to be.

**[Is it Friday?](https://is-it-friday-hmmm.netlify.app/)**
The entire app is one word. That is the whole product and I stand by it.

The full wall of all 33 is here: [my Hot AR Summer builder page](https://hot-ar-summer.netlify.app/showcase/builder/la-shara-cordero).

## The last one shipped. It just never got counted.

The final thing I made was not an app. It was a receipt for the month: [an attestation page](https://hot-ar-summer-attestation.netlify.app/) with confetti, a terminal window, and a certified stamp. The serial on it is **5307-062B**, and it is not decorative. It is FNV-1a run over the string `la-shara-cordero|hot-ar-summer|2026-07-31`. Same name, same event, same date, same serial, forever. You can check my arithmetic.

There is one honest asterisk on that page and I am going to point at it myself. The confetti uses `Math.random`. I left a comment in the source saying so:

```js
// Deterministic-ish spread without Math.random dependence for the pattern,
// but confetti is decorative so a little jitter is fine here.
```

That is the line I drew all month, and it took thirty-three apps to find it. The rule was never "randomness is bad." The rule was **nothing you are asked to believe may be random.** The serial is a claim, so it is computed. The confetti is a feeling, so it can scatter. If I had made the serial random it would have been a sticker. If I had made the confetti deterministic it would have been a chore.

Here is the context I am not going to dress up. That Friday I left the house at 6 in the morning and got home at 6:30 at night. Then we drove to LA for dinner. I was not running on a full tank when I sat down at that table, and I was still planning to ship something before midnight.

I am not telling you that to flex. I am telling you because it is the actual reason the last one missed. I tried to overachieve on an empty day and it did not work. Sometimes pushing produces a good story. This time it produced a live URL nobody was allowed to count.

Thirty-three apps counted. The thirty-fourth is real, shipped, and uncounted.

It stung. It also might be the most accurate possible ending for a month spent learning that the deploy is a cost, the second pass is a cost, and time is the one budget with no top-up button. The work existing and the work being counted are two different things, and only one of them was ever up to me.

Would I do it again? You know it. It was so much fun.

Coming up with the ideas was the best part. Sitting there working out what would actually make somebody smile, what is worth one screen, what has a joke in it and a real mechanism underneath. That is a harder puzzle than the code and I loved every round of it.

The wins I am keeping:

- **Leaning all the way into deterministic building.** I went in with it as a preference and came out with it as a practice. That is a real shift and it is mine now.
- **Learning Netlify from scratch.** New tool, thirty-three deploys, zero drama. Add it to the kit.
- **Getting demoed by a company for the first time.** Netlify put How Long Ago Was That on their livestream. That had never happened to anything I built.
- **Failing on that livestream, publicly, in real time.** [Here is the moment](https://www.youtube.com/watch?v=2JlOsW43muk). My app repeated itself on air and I got the lesson handed to me in front of an audience. I would not trade it. You do not learn that one from a blog post.

And a huge thank you to [Netlify](https://www.netlify.com/) for running this. A whole month of low-stakes, high-joy building with a wall to put things on. That is a gift to anyone learning, and I got a lot more out of it than 33 URLs.

## What I am taking with me

**Determinism is not a purity thing, it is a pricing model.** Building it into the app moves the entire cost to construction and gives you a flat line forever. That is worth more than the variety you give up.

**The fix for repetitive output in a deterministic tool is never randomness. It is more knowledge.** More anchors, more rules, a wider table. Randomness would make the output unrepeatable, and the receipt is the product.

**Your spend is mostly input tokens.** Ninety-eight percent of mine was. Tighten the spec, not the app.

**One deploy.** Say it out loud before you press the button.

I did not plan on doing this. I am so glad I did. Thirty-three small honest things exist now that did not exist on June 30, and the whole month cost me $44.

Which, and I promise I did not plan this either, is less than the dinner.

Quick context if you are new here: I came to code from the courtroom. Jury services to AI builder, starting July 2025 and still going, self-taught, learning in public. I direct, the agents generate, I validate and decide. I build the Clew Suite and a handful of civic tech tools, and you can see the rest of it at [Clew Labs](https://earlgreyhot1701d.github.io/Clew-Labs/). That is the lens I am writing from.

AI Assisted. Human Approved. Powered by NLP.
