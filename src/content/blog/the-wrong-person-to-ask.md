---
title: "The wrong person to ask"
date: 2026-05-10
category: sieve
hook: "Sieve will hold your income, your expenses, every fund you own — and I want to be the wrong person to ask for any of it."
reading_time: 5
---

I said previously that I'd put the privacy decision on the table before any code lands. This is that post.

When I thought about showing Sieve to a friend the other day, my first thought was — wait, that means I'll see all their money. Then I went home and spent the weekend trying to figure out how to make that answer be no — at least for anyone who'd rather not share all of their data.

There's no deployed code yet. Not a single line. The architecture exists only as decisions in markdown files. I'm writing this post before I've deployed anything because privacy retrofitted is privacy as a concession; privacy designed-in is privacy as a commitment. The first kind happens after a breach or a launch or a regulatory letter. The second kind happens in week one, before there's anything to defend.

## The three tiers

There's a spectrum of things you can claim about user data. I had to figure out where to sit on it.

**Tier 1: encryption at rest.** The disk is encrypted. The keys are held by the operator — by me. If someone steals the database, they see ciphertext. But if someone shows up with a subpoena, or if my own credentials get compromised, the plaintext is reachable. This is what most apps do. It satisfies compliance auditors and not much else. The bar is low; the value is low.

**Tier 2: per-user keys, with the operator unable to decrypt at rest.** Each user has a key derived from their password. The server can only see their data while they're logged in — for the duration of a single request, in memory, never persisted. A snapshot of the database is ciphertext. An operator with full console access still can't decrypt without intercepting an active session, which is detectable and auditable. The audit logic still runs server-side, where the engine actually lives. The cost: if a user forgets their password and loses their recovery code, the data is gone. There is no help line that can recover it. That's the price.

**Tier 3: full end-to-end encryption.** The audit engine moves to the browser. The server stores only encrypted blobs and acts as a sync gateway. Even with a warrant, even with full access, an operator mathematically cannot decrypt. This is what Bitwarden and ProtonMail do. The cost is steep: four to six weeks of additional engineering to port the engine, a heavier browser bundle, no path to server-side improvements that depend on plaintext, and a multi-device sync problem that has to be solved with key sharing.

None of the three is right alone. Tier 1 doesn't meet my bar — I don't want to be reachable for the data. Tier 3 is the strongest claim but adds a month of work and forecloses a real product direction. Tier 2 is in the middle, with one important catch: it removes operator recovery as a service. For some users that's a feature. For others it's a deal-breaker.

## What I picked: a dual-mode product

The user gets to choose. At signup, two modes:

**Standard mode.** Your data is encrypted at rest with a server-managed key. If you forget your password, I can help you back in. The default for users who want a financial tool that behaves like every other one they've used.

**Private mode.** Your data is encrypted with a key derived from your password. I cannot decrypt it. Not for support, not for recovery, not under any circumstance short of breaking AES-256. If you forget your password and lose your recovery code, your data is gone forever.

Same code paths. Same audit engine. Same database tables — every row is encrypted ciphertext regardless of mode. The only thing that differs is where the key comes from: a server secret, or your password processed through a key-derivation function.

I considered offering only Private mode and forcing the recovery-code discipline on everyone. It would be the bolder claim. But most users want their financial app to behave like their email — forgettable, recoverable, low-stakes. Forcing them into "save this 24-word phrase or lose your data" at signup is the right move for users who care, and the wrong move for users who don't. I'd rather have the option than the imposition.

## What it costs to choose Private

The recovery code is non-negotiable in Private mode. At signup, the user sees a generated string once, has to copy it somewhere offline, and checks a box confirming they've stored it. After that screen, the code is gone from my view.

Lose your password and you can use the recovery code to unwrap your data and reset.

Lose both, and you lose the data. There is no path back. I've thought hard about this and there is no honest workaround. Any "help me recover" service requires the operator to hold something that can decrypt — which collapses Private mode back into Tier 1. The same applies to escrow services or "trusted contacts" schemes; the moment a third party can recover your data, so can a court compelling that third party.

If you pick Private, this is the cost. It's a deliberate cost. The strong claim is only strong because there's no back door.

## What this still doesn't protect against

I want to be honest about the parts that Tier 2 doesn't solve.

If your own device is compromised — keylogger, screen sharer, browser exploit — your data is reachable. Sieve runs on it; whoever else is on it does too. This is true of every product, not just mine.

If I, the operator, were to serve malicious JavaScript from my own origin — say, an update that captured passwords as users typed them — Tier 2 wouldn't catch it. The crypto runs in code I wrote. A reader who doesn't trust the code can't audit it without reading every release. This is the gap that Tier 3 closes, by moving the audit logic into open-source client-side bundles that get inspected and reproduced. It's a real gap.

It's a known upgrade path. It's not in the plan for the first version, but I'm naming it so it doesn't get sold as something it isn't.

## What's next

Next weekend, the scaffolding — a domain, a deploy pipeline, a landing page I can see actually live on the open internet. The privacy architecture turns into code shortly after — but with the decisions made before the keyboard came out, I get to spend the implementation time on getting the maths right, not on negotiating the principle.

If you've used a financial app and thought "I want a version of this where the company isn't holding the master key," that's the version I'm building you. Either mode is honest about what it gives and what it costs. Pick whichever fits.
