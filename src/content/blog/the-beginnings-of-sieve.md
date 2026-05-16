---
title: "Sieve — Filter through the (financial) noise"
date: 2026-05-16
category: sieve
hook: "Three loans, twenty-one mutual funds, and no app I could find would tell me the age at which I could stop working (for money)."
reading_time: 5
---

A few weeks ago I sat down with a stack of statements — three loan accounts, twenty-one mutual fund schemes across two platforms — the usual mess. I wasn't looking to do anything fancy. I just wanted a clean answer to one question: am I on track?

By the end of that weekend I had something I still can't find in any product I've tried.

- A per-fund verdict — keep, review, exit now, exit after a year — with the reasoning attached to each one.
- A year-by-year projection of my loans and my corpus, three strategies plotted against each other.
- And an answer to "when can I stop working (for money)?" that updated live as I slid the SIP and prepayment numbers around.

That weekend is the reason I'm building Sieve.

## What I noticed doing it by hand

A few insights jumped out that I now think every household audit should produce.

**Personal XIRR is the wrong metric to judge a fund by.** Every platform you log into puts your XIRR front and centre. The problem is that XIRR reflects *when you invested*, not *how good the fund is*. A great fund where you started a SIP six months ago during a correction shows a brutal negative number. A mediocre fund you've held for eight years shows a flattering one. Acting on XIRR alone exits good funds and rewards bad ones. You have to cross-reference with the 3-year and 5-year annualised return of the fund itself — and that lookup just isn't surfaced anywhere I've checked.

**If you have a loan above 7%, almost every debt fund you hold is a net loss.** Post-2023 the tax rules changed — debt funds are taxed at your income slab regardless of how long you hold them. So a debt fund earning 8% pre-tax, at a 30% slab, gives you 5.6% net. A home loan at 7.15% is a guaranteed, tax-free 7.15% if you prepay it. The maths says: stop the debt SIP, exit the debt fund, prepay the loan. But no app routes you to that conclusion. Every app I've seen treats debt funds and loans as separate things owned by separate planning logic.

**The "cascade effect" is the single biggest lever you have.** When you prepay one loan and close it early, its entire EMI — every month, every year, for years — redirects to the next loan in line. A ₹1.12 lakh/month EMI freed up two years earlier doesn't add a little; it compounds. Splitting your extra surplus across three loans defeats the whole thing. Concentrate on one. Close it. Then concentrate on the next.

**The right output metric isn't a corpus number — it's an age.** Most planning tools tell you "you need ₹5 crore at retirement." That's true and useless. The number that actually moves the needle is "with these inputs, you'll be loan-free at 41 and financially independent at 47." That's the metric that makes trade-offs visible — slide a parameter, watch the age change, decide if it's worth it.

## The product I want to build

Sieve is a financial health tool. It's read-only — never moves your money, never has write access to a single broker or bank. You give it your profile, your goals, your holdings, your loans. It tells you, in plain English, what to keep, what to exit, how fast you can be debt-free, and at what age you reach financial independence.

Two outputs do most of the work. The first is a per-fund verdict — keep, review, exit now, exit after a year — with the reasoning attached. Not a star rating, not a buy/sell oracle. Just the actual maths, in English, that you can read and disagree with.

The second is a simulator. Year-by-year, twenty-five years out, with the hero numbers — loan-free age, FI-ready age, net worth at FI, total interest paid — updating live as you slide the SIP, the prepayment, the return assumption. You see the trade-off in the only unit that actually matters.

More on each piece as it gets built.

## What makes this different

The honest answer is that most pieces of Sieve exist somewhere. Tickertape will tell you about a fund. Groww shows you XIRR. ET Money has a portfolio view. Most banks have an EMI calculator.

What I haven't found is something that joins them. Something that knows your loan rate when it's giving you a debt fund verdict. Something that treats SIP and loan prepayment as the same decision, because they're competing for the same surplus. Something that gives you an *age* output, not a corpus number. Something that reads your actual holdings rather than asking you to type fund names.

That's Sieve.

## What's next

The first weekend was deciding what I wanted the product to solve. Next is getting the bones up — a domain, a landing page that admits there's nothing behind it yet. After that, the mutual fund audit — the one feature I think earns its keep before goals or loans are even captured.

I'm going to write about each item as I build. Some of the decisions I've already made are going to look strange — I'll get to them. There's one in particular about privacy that I want to put on the table before any code lands, so it's clear it wasn't an afterthought. That's the next post.

For now: if you've ever stared at twenty-one funds across two platforms and wondered if there's a way to make sense of it without a six-hour spreadsheet, you're who I'm building this for.
