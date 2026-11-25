---
name: mypos-info
description: Starting point for all myPOS integrations. Use when choosing between available myPOS SDKs and APIs, comparing integration options, answering cross-cutting questions about environments, authentication, webhooks, or card security, or when a user provides an IPP protocol log to analyze.
license: Apache-2.0
---

# myPOS Integration Guide

Use this skill when you are starting a new myPOS integration, choosing between available SDKs and APIs, answering cross-cutting questions about environments or authentication, or when a user provides a log to analyze.

---

## Log Detection — Act First

**Before doing anything else: scan what the user provided.**

If the input contains any of the following → it is an **IPP protocol log**:
- Lines starting with `DCM:(` or containing `MYPOS_RQ:` / `MYPOS_ANSW,LEN=`
- Lines labeled `Message:` followed by `PROTOCOL=IPP`
- Any content containing `PROTOCOL=IPP` and `METHOD=`

**Action:** Immediately load `@skills/mypos-ipp/references/debugging.md` and `@skills/mypos-ipp/references/appendices.md`, then apply the analysis methodology from `references/log-detection.md` to identify the log format and extract messages. Do not ask the user to switch to a different skill — analyze the log inline.

---

## Quick Decision Tree

```
What are you trying to do?

TAKE PAYMENTS IN PERSON (physical card terminal):
│
├── App running ON the myPOS Smart terminal itself
│   └── → mypos-smart-android   @skills/mypos-smart-android/SKILL.md
│
├── App on a separate device driving a terminal:
│   ├── Android phone / tablet
│   │   └── → mypos-terminal-android   @skills/mypos-terminal-android/SKILL.md
│   ├── iOS iPhone / iPad (Bluetooth)
│   │   └── → mypos-terminal-ios   @skills/mypos-terminal-ios/SKILL.md
│   └── Windows / .NET / C# (USB, Serial, TCP)
│       └── → mypos-terminal-dotnet   @skills/mypos-terminal-dotnet/SKILL.md
│
├── NFC payments on any standard Android phone (no dedicated terminal)
│   └── → mypos-glass-android   @skills/mypos-glass-android/SKILL.md
│
├── Kiosk / unattended / vending machine (no touch UI)
│   └── → mypos-ipp (VENDING_PURCHASE)   @skills/mypos-ipp/SKILL.md
│
└── Custom low-level protocol (serial/USB/Bluetooth, no Android SDK available)
    └── → mypos-ipp   @skills/mypos-ipp/SKILL.md

ADD PAYMENTS INSIDE YOUR OWN NATIVE MOBILE APP (no redirect, no dedicated terminal):
│
├── Native Android app (Kotlin/Java)
│   └── → mypos-checkout-mobile-android   @skills/mypos-checkout-mobile-android/SKILL.md
│
└── Native iOS app (Swift/Objective-C)
    └── → mypos-checkout-mobile-ios   @skills/mypos-checkout-mobile-ios/SKILL.md

TAKE PAYMENTS ONLINE:
│
├── Redirect customer to a myPOS-hosted checkout page:
│   ├── TypeScript / Node.js → mypos-checkout-ts   @skills/mypos-checkout-ts/SKILL.md
│   ├── PHP                  → mypos-checkout-php   @skills/mypos-checkout-php/SKILL.md
│   └── Any other language   → mypos-checkout-rest   @skills/mypos-checkout-rest/SKILL.md
│
├── Embedded payment form in your page (no redirect, iFrame, Apple/Google Pay)
│   └── → mypos-checkout-embedded   @skills/mypos-checkout-embedded/SKILL.md
│
└── Server-to-server charge (no user interaction, stored card token)
    └── → mypos-checkout-rest   @skills/mypos-checkout-rest/SKILL.md

CALL myPOS BACKEND APIs (balances, payment links, ePOS, banking):
│
├── TypeScript / Node.js (managed SDK)
│   └── → mypos-api-gateway-ts   @skills/mypos-api-gateway-ts/SKILL.md
│
└── Direct HTTP / any other language
    └── → mypos-api-gateway-rest   @skills/mypos-api-gateway-rest/SKILL.md

ANALYZE A LOG / DEBUG AN INTEGRATION:
└── Paste the log → see Log Detection above

NOT SURE WHERE TO START:
├── Full product landscape → references/integration-map.md
├── Comparing two options → references/comparison.md
├── Environment or auth questions → references/cross-skill.md
└── Quick specific question → references/faq.md
```

---

## Non-Negotiable Rules

- **IPP log in the input → analyze it immediately.** Do not route the user to another skill. Load `@skills/mypos-ipp/references/debugging.md` and handle it here.
- **Establish the developer's platform before recommending a specific skill.** "In-person payment" alone is not enough — a Windows POS app and an Android phone both take payments in person but need completely different skills.
- **Never assume the environment.** Always ask or confirm whether the developer is working in demo/sandbox or production before suggesting setup steps.
- **Terminal SDKs are built on top of IPP.** Recommend a Terminal SDK whenever one exists for the developer's platform. Recommend IPP directly only when no SDK exists for the platform, or when the developer needs protocol-level control.
- **The Partner Portal is always the first step.** No skill can be tested or deployed without credentials or an app registration from `demo-partners.mypos.com` (sandbox) or `partners.mypos.com` (production).

---

## Guidance by Scenario

**Developer doesn't know where to start:**
1. Ask what they want to build (in-person payments, online payments, API integration, or something else).
2. Ask what platform/language they're using.
3. Use the decision tree above to identify the skill, then load it.
4. Point them to `references/cross-skill.md` if they have setup questions first.

**Developer is choosing between two options:**
1. Load `references/comparison.md` for the relevant comparison section.
2. Give a direct recommendation, not just a list of trade-offs.
3. Load the recommended skill once the choice is made.

**Developer asks a general myPOS question:**
1. Check `references/faq.md` first — most common cross-skill questions are answered there.
2. If the question is skill-specific, load the relevant skill.

**Developer provides a log:**
1. Follow the Log Detection rule above.
2. Load `references/log-detection.md` to identify the format.
3. Load `@skills/mypos-ipp/references/debugging.md` to run the analysis.

---

## Required Response Contract

When routing a developer to a skill:
- State the recommended skill and why it fits their scenario.
- Mention one or two non-obvious things they'll need (e.g., "you'll need a Demo Debug POS device from myPOS sales for testing").
- Load the skill's `SKILL.md` via `@reference` so the full context is available.

When analyzing a log:
- Identify the log format before parsing.
- Report what is in the log — no inferences about fields that aren't present.
- Flag every STATUS≠0 and APPROVAL≠00 with context (METHOD, STAGE, timestamp).
- Give a root cause and recommended fix for every identified issue.

---

## References

- `references/integration-map.md` — all 14 integrations: platform, use case, device model lookup
- `references/comparison.md` — SDK vs REST, terminal SDK variants, checkout variants
- `references/cross-skill.md` — demo/production environments, Partner Portal, OAuth, webhooks, card security
- `references/faq.md` — quick answers to common cross-skill questions
- `references/log-detection.md` — IPP log detection, format parsing (DCM and partner logger), dispatch rules

On-demand (loaded when a log is detected):
- `@skills/mypos-ipp/references/debugging.md`
- `@skills/mypos-ipp/references/appendices.md`
