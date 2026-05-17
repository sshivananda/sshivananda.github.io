---
title: "The line I had to stay behind"
date: 2026-05-17
category: sieve
hook: "I asked one question I should have asked weeks ago — does Sieve, as designed, need to register with SEBI? — and spent the rest of the weekend redesigning around the answer."
reading_time: 7
---

It started with a question I should have asked weeks ago.

Sieve, as I'd designed it, rendered a verdict on every mutual fund a user owns. Keep this. Exit that. Stop the SIP on the other.

Each verdict came with the reasoning attached — the personal XIRR alongside the 3-year and 5-year annualised return, the post-tax math against the user's loan rate, the credit-risk flag if it applied. That was the whole pitch of [the first post](/blog/the-beginnings-of-sieve).

What I never asked, in the weeks of writing methodology and decision matrices, was whether shipping that would put me on the wrong side of [SEBI (Investment Advisers) Regulations, 2013](https://www.sebi.gov.in/legal/regulations/feb-2023/securities-and-exchange-board-of-india-investment-advisers-regulations-2013-last-amended-on-february-07-2023-_69215.html). The answer, after a few hours of reading, was: yes. Exactly. The per-fund verdict on a user's specific holdings, derived from their profile and risk window, is the textbook example of what those regulations cover. The rest of the weekend was about redesigning around that line — and discovering that the product underneath was the one I'd actually wanted to build.

## What the regulation actually says

The rule is short and direct. Under Regulation 2(m) of the SEBI Investment Advisers Regulations, an investment adviser is "any person who, for consideration, is engaged in the business of providing investment advice to clients." *Investment advice* in turn is defined as advice relating to investing in, purchasing, selling, or otherwise dealing in securities — and the definition explicitly includes financial planning. There are exemptions for newspapers and mass-media commentary, but those exemptions specifically exclude personalised advice.

A per-fund verdict — *exit this debt fund because its post-tax return trails your home loan rate; stop the SIP on this overlapping large-cap; hold this small-cap for another six months for LTCG* — is the canonical example of what the regulation covers. Personalised. On a security. Derived from the user's profile, holdings, and loan stack. Three for three.

Robo-advisors don't get a lighter regime. SEBI's stance, [reiterated in the December 2024 amendments](https://www.lexology.com/library/detail.aspx?g=666c5c80-23cb-4217-a261-ac19257cd5b2) and the January 2025 circulars that followed, is that algorithmic personalised advice is the same as human personalised advice — with the additional burden of algorithm transparency and bias disclosures bolted on top. There's no useful "it's free" loophole either. The "for consideration" trigger captures indirect benefits — paid tiers, referral fees, lead generation, data monetisation. Once anything resembling revenue touches the surface, the trigger fires.

The cost of crossing the line, once you do, is a real bar. A non-individual registration as a body corporate or LLP. A principal officer qualified in both NISM-Series-XA and -XB. A ₹50 lakh deposit lien-marked to the Investment Adviser Administration and Supervisory Body — a BSE subsidiary that took over RIA oversight in July 2024. An annual compliance audit. A fee-only revenue model with mandatory segregation from any distribution arm. Mandatory risk profiling before any advice is rendered. Five-year record retention. The lighter individual-RIA path doesn't help — an individual RIA cannot also operate any execution or distribution surface, which makes a fintech delivery model effectively impossible.

## The two paths

There were, in practice, two ways to ship Sieve from where I was.

The first was to set up as a non-individual RIA before the first milestone went out. ₹50 lakh in a lien-marked deposit before a line of code. NISM exams. An articled chartered accountant for the annual audit. A five-year retention obligation before there's a single user to retain records for. A real bar — and worth clearing, if there's a fee-bearing product on the other side. But Sieve as I've designed it isn't a fee-bearing product. There's no plan to monetise it for the foreseeable future. The infrastructure I'd be standing up exists to serve a revenue model that doesn't.

The second was to redesign so the trigger doesn't fire. Keep the read-only fund metadata. Keep the holdings parser. Keep the simulator. Keep the cash-flow ratios and the goal calculator. And *remove the verdict layer*. No KEEP. No EXIT NOW. No STOP SIP. No "ON TRACK / NEEDS ATTENTION". Render facts; let the user reach the conclusion. The line SEBI cares about is the personalised recommendation on a security. The math on either side of that line is the same. It's the framing of the output that crosses it.

**The choice was easy.** No revenue. No urgency to take on a regulatory and accounting load that exists for a product I'm not running. And — this is the part I didn't see at first — the redesigned version is the one I'd actually have preferred to build.

## What survives when you take the verdicts out

I expected the cut to gut the product. What survives is more than I thought.

The simulator is untouched. A year-by-year calculator that the user drives — pick your equity assumption, pick your inflation, slide the SIP and the prepayment, watch the loan-free age and the financial-independence age move — is a calculator, not advice. The user supplies the inputs; Sieve runs the math. None of the regulator's tests for "advice" apply to that. The hero metric stays an age; the trade-off stays visible in the unit that actually matters.

The mutual-fund observatory survives in altered form. Personal XIRR sits next to the fund's 3-year and 5-year annualised return next to the category median next to the holding period — four numbers, side by side, on a single card. The user can read them. The category overlap map lays out which of their large-caps overlap with their flexi-caps and by how much. The post-tax debt-fund return is rendered next to the user's highest loan rate as two adjacent numbers. None of that is a verdict. All of it is what the user needs to reach the verdict themselves.

The cash-flow ratios survive as numbers rendered alongside industry benchmark ranges, with no "healthy / stressed / critical" label attached. The goal calculator survives as a gap between projected corpus and future cost — green if positive, red if negative, no on-track/off-track verdict.

The pieces that go: the KEEP / EXIT NOW / REVIEW / EXIT-AFTER-1Y classification, the EXIT NOW verdict on debt funds whose post-tax return trails the loan rate, the tax-timing engine that named months to redeem in, the goal-aware surplus routing recommendation, and every ON TRACK / NEEDS ATTENTION label. They're listed in the redrafted methodology as removed. The math underneath them mostly stays — repurposed into facts the user reads.

## The product underneath

Here is what I didn't expect.

The verdict layer was a shortcut. It was outsourcing the user's thinking to me — to my decision matrix, to my thresholds, to my view of what counts as redundant. Convenient, but condescending in a way I hadn't noticed when I was writing it. *Here's your verdict. Trust me.*

What's left forces a different kind of product. If I can't tell you what to do, I have to show you what you need to make the call yourself. The personal XIRR sitting next to the 3-year annualised on the same card is more honest than a "this fund is fine" label, because it lets you *see* that the apparently terrible XIRR is six months of bad timing rather than four years of bad management. The debt-fund post-tax return rendered next to your loan rate is more useful than EXIT NOW, because you can see the actual delta and decide whether the 1.5% gap is worth the redemption load, the realised tax, the capital tied up.

**The verdict was the wrong unit.** The right unit is the data, presented in a way that makes the conclusion legible. The user gets to disagree with me — which they couldn't, when I was just rendering a verdict. They get to notice things I didn't, like that one of their "redundant" overlapping funds has a much better expense ratio than the other. They get to overrule the math when their circumstance calls for it.

There's a related shift in the scope section of the README that I added this weekend: a does/doesn't table that draws the line in plain English. Show holdings, returns, overlap. Don't tell you what to exit. Run projections. Don't issue a recommended strategy. Size your required term cover. Don't recommend a policy. Every row on the left is a calculator; every row on the right is a verdict. The shape Sieve takes is the shape on the left.

This is, I now think, the better product. The verdict version was *me, telling you*. This version is *the data, telling you both*.

## What changed in pixels

Three documents were rewritten end-to-end this weekend. The README lost the verdict-led tagline and gained a Scope section that names what Sieve doesn't do. The methodology doc lost three sections — Fund Exit/Keep Classification, Target Portfolio Construction, Common Pitfalls — and reframed the rest into fact-displays and calculators. The audit engine in the architecture doc became the *observatory engine*, with the same rename propagating through the privacy ADR and the workload sizing in the serverless ADR.

The landing-page tagline went from *A second opinion on every fund* to *Cause your financial health matters as much as your physical* — broader, less promissory, less verdict-shaped. The subhead under it dropped the "we tell you what to keep, exit, or stop adding to" framing and now reads: *One view of your money, and a simulator to compare scenarios. The hero metric is an age — when you're debt-free, when you're financially independent — and it moves as you do.* The onboarding panel on the right is smaller too — one step instead of two, the funds step pulled out because the engine isn't there yet. *Start here* is the new label.

The implementation doesn't get to renegotiate any of it — [same commitment-device argument as yesterday's post](/blog/a-face-before-the-engine). What's on the door now is what gets shipped, minus a verdict layer that wasn't going to ship anyway.

Next weekend, the first milestone starts. The cipher adapter wakes up. The CAS parser starts running in the browser. AMFI's NAV file gets pulled. The first real metrics get rendered against real funds — and not a single one of them tells you what to do with them.
