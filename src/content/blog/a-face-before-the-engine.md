---
title: "A face before the engine"
date: 2026-05-16
category: sieve
hook: "Two buttons that won't click, six adapter folders that hold no code, and a landing page that already promises everything the engine has to deliver."
reading_time: 5
---

The design has been sitting in `design/Onboarding_1.png` for days. A serif headline on a dark background, a lime accent, two buttons, a small sieve glyph in the corner. I've stared at it dozens of times. This weekend I built it.

Most of the visual was an afternoon. Tailwind classes, Google Fonts for Instrument Serif on the headline and Inter on the body, a lime hex value eyeballed from the mock. I rendered the page in a headless browser at the end and put the screenshot next to the PNG — the proportions were within a few pixels. Easy.

The harder, less visible half of the weekend was everything else.

## The cheapest commitment device is a URL

Documents change quietly. A README can be rewritten in a commit nobody sees. An ADR can be marked "superseded." A design PNG can be swapped for a new one. None of those changes have witnesses outside the repo.

A deployed page is different. Once Sieve loads at a URL, the headline, the tagline, the two-step flow, and the promise in the footer strip all sit in front of any visitor who lands there. The change has a witness. Going back on any of it costs more than going forward.

What got committed in pixels this weekend: the name *Sieve*. The tagline *Cause your financial health matters as much as your physical*. The promise, in a smaller font under the buttons: *~ read-only access · we never move your money*. That last phrase is what [last weekend's post](/blog/the-wrong-person-to-ask) was about. It's not a sentence on a blog anymore. It's a sentence on a landing page.

Pulling it down would cost me a screenshot in the Wayback Machine and a post explaining the climbdown. A small cost, but enough — the whole point of writing things down somewhere visible is that the cost of going back exceeds the cost of going forward. The implementation in M1 doesn't get to renegotiate any of it.

## Doing nothing, on purpose

The two CTAs — *Begin* and *Try a sample portfolio* — are disabled. Click them; nothing happens. The element is there, the styling is there, the focus ring works, but each button has a `disabled` attribute and no `onClick`.

That's the milestone. M0 is the spine: everything wired up and a landing page that visually matches the mock. The page does nothing on purpose. The first thing M0 has to prove is that the spine works — not that there's a product behind it.

A version of me wanted to make the buttons do *something*. Add a coming-soon page. Wire up an email capture. Show a confetti animation when someone clicks. None of those would have been the work this weekend was for. The point of M0 is the smallest possible thing that exercises the deploy pipeline end-to-end. Adding a feature would have been the wrong currency.

So the page renders, looks like the mock, and waits.

## The boundaries before there are files in them

Half the weekend went into things you can't see in a browser.

Inside `web/src/`, there's a folder called `adapters/`. Inside it: `auth/`, `email/`, `fund-meta/`, `cas-parser/`, `cipher/`, `storage/`. Each one contains a README and nothing else. No code. No `interface.ts`. Six rooms with the doors marked, the furniture unbought, the walls unpainted.

There's also an ESLint rule that does this: if I write `import _ from 'lodash'` anywhere in `src/app/`, the build fails with an error message linking to ADR-0001. If I write the same line in `src/adapters/auth/`, it passes. I tested both. The rule fires. The exemption works.

The rule has nothing to police yet. There's exactly one external dependency in `src/app/page.tsx` — the framework — and the rule's allowlist permits framework imports outright. But that's the wrong way to think about it. The rule isn't there to stop me from writing bad code today. It's there to stop me from writing bad code six weeks from now, in a hurry, at one in the morning, when I want to ship the CAS parser and the path of least resistance is to `pnpm add pdfjs-dist` and import it from a route handler. With the rule in place, that import fails CI; without it, the failure shows up six weeks later when I want to swap PDF parsers and find the library spread across forty files.

The cheapest moment to install that friction is now, while there are no imports for it to police. Twenty lines of config and a comment pointing at the ADR.

Same logic everywhere else. The repo is a pnpm monorepo with `web/` and a stubbed `backend/` — empty, README only, reserved for future standalone services. No mobile workspace yet, but the layout doesn't fight the day there is one. The pre-commit hook runs through Lefthook, chosen specifically because it isn't tied to Node — so when Swift or Kotlin or Go shows up, the same config picks them up. Every one of those calls is the same call: install the structure before there's content inside it, because the structure is approximately free now and never that cheap again.

## What's next

M1 is where the engine lands. The privacy architecture from last week becomes code — the cipher adapter wakes up, the dual-mode picker shows up at signup, the recovery-code flow gets built. The CAS parser starts running in the browser. The fund-metadata pipeline pulls AMFI's daily NAV file. The first real verdicts get rendered against real funds.

The landing page has been making the promise for a week by the time M1 opens what's behind it. Read-only. Never moves your money. The face has been on the door long enough that the engine has to be worth the wait.
