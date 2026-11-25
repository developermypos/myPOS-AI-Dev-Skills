# myPOS AI Developer Skills

A collection of AI coding skills for building myPOS payment integrations across server-side APIs, hosted web checkout, native in-app mobile checkout, and terminal SDKs.

myPOS exposes payment integration through more than a dozen different SDKs and APIs — hosted checkout, embedded checkout, native mobile SDKs, physical terminal SDKs across several platforms, a low-level binary wire protocol, and REST/TypeScript API gateways for banking, ecommerce, and ePOS. Picking the right one for your platform, wiring up authentication correctly, and not missing easy-to-overlook security requirements (PCI scope, signature verification, webhook validation, key storage) is easy to get wrong — and general-purpose AI coding assistants have no built-in knowledge of myPOS's specific APIs, naming conventions, or non-negotiable rules.

This repo packages that knowledge as **Agent Skills**: structured guides that an AI coding assistant (Claude Code, Cursor, Gemini CLI, or any tool supporting the Agent Skills standard) loads automatically when it detects you're working on a myPOS integration. Each skill contains a decision tree for choosing the right approach, the non-negotiable security and correctness rules for that integration, a step-by-step implementation workflow, and links to the canonical developer docs — so your assistant produces working, secure code instead of guessing.

### When to use this

- You're adding myPOS payments to a website, mobile app, POS device, or backend service, and want your AI assistant to generate code that matches myPOS's actual APIs and security requirements instead of inventing plausible-looking but wrong integrations.
- You're not sure which myPOS SDK or API fits your platform — start with `mypos-info`, which asks a few clarifying questions and routes you to the right skill.
- You have a raw protocol log (a DCM trace or IPP log) from a terminal integration and want it decoded and diagnosed — `mypos-info` detects and analyzes these automatically, no manual setup required.
- You're extending or debugging an existing myPOS integration and want your assistant to follow the same conventions the official docs require (auth flow, error handling, webhook/signature validation, unique order IDs, etc.).

## Skills

| Skill                             | Use when...                                                                                                                                                                                                                |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `mypos-api-gateway-ts`          | Building a Node.js/TypeScript server that calls myPOS Banking, Ecommerce, or ePOS APIs (TypeScript SDK)                                                                                                                    |
| `mypos-api-gateway-rest`        | Calling the myPOS API Gateway directly over HTTP in any language — OAuth2, ePOS payments, payment links, banking                                                                                                          |
| `mypos-checkout-ts`             | Accepting card payments on a website using myPOS hosted checkout forms (TypeScript/Node.js SDK)                                                                                                                            |
| `mypos-checkout-rest`           | Integrating with the myPOS Checkout API directly over HTTP in any language — RSA signatures, all checkout operations                                                                                                      |
| `mypos-checkout-php`            | Accepting card payments on a website using myPOS hosted checkout forms (PHP)                                                                                                                                               |
| `mypos-checkout-embedded`       | Embedding a myPOS payment form directly in your page via iFrame — no redirect, supports Apple Pay and Google Pay                                                                                                          |
| `mypos-checkout-mobile-android` | Accepting card payments inside your own native Android app using the myPOS Mobile Checkout SDK                                                                                                                             |
| `mypos-checkout-mobile-ios`     | Accepting card payments inside your own native iOS app using the myPOS Mobile Checkout SDK                                                                                                                                 |
| `mypos-smart-android`           | Building an Android app that runs directly on myPOS Smart terminal devices                                                                                                                                                 |
| `mypos-terminal-android`        | Building an Android app on a separate device that controls myPOS terminals via Bluetooth, USB, or TCP/IP                                                                                                                   |
| `mypos-terminal-ios`            | Building an iOS app that controls a myPOS terminal over Bluetooth (Objective-C or Swift)                                                                                                                                   |
| `mypos-terminal-dotnet`         | Building a .NET/C# application that controls a myPOS terminal over USB/Serial or TCP-IP                                                                                                                                    |
| `mypos-glass-android`           | Accepting NFC card payments through the myPOS Glass SoftPOS app on standard Android devices                                                                                                                                |
| `mypos-ipp`                     | Implementing direct serial/USB/Bluetooth/WiFi communication with myPOS terminals using the binary Integrated Payments Protocol — all 31 methods, all fields, Cash Register and Slave mode; DCM log analysis and debugging |
| `mypos-info`                    | Starting point for all myPOS integrations — routes to the right SDK or API, compares options, answers cross-skill questions, and automatically detects and analyzes IPP protocol logs from any format                     |

## Installing

These skills work with AI coding assistants that support the Agent Skills standard, including Claude Code, Cursor, and Gemini CLI.

### Claude Code

```bash
/plugin marketplace add developermypos/myPOS-AI-Dev-Skills
/plugin install mypos@mypos
```

### Cursor

Add via **Settings > Rules > Add Rule > Remote Rule (GitHub)** using `developermypos/myPOS-AI-Dev-Skills`.

### Gemini CLI

```bash
gemini extensions install https://github.com/developermypos/myPOS-AI-Dev-Skills --ref=main
```

### Clone / Copy

The repo root is the plugin, and the skills live in `skills/`. Clone it and point your tool at it, or copy the skill folders you need:

```bash
git clone https://github.com/developermypos/myPOS-AI-Dev-Skills
```

| Agent       | What to use                                 |
| ----------- | ------------------------------------------- |
| Claude Code | Repo root (`.claude-plugin/`) or `skills/*` |
| Cursor      | Repo root (`.cursor-plugin/`) or `skills/*` |
| Gemini CLI  | Repo root (`GEMINI.md`)                     |

## Developer Documentation

All skills link to the official myPOS developer documentation at **https://developers.mypos.com/**.

## Resources

- [myPOS Developer Documentation](https://developers.mypos.com)
- [myPOS Partner Portal](https://partners.mypos.com/en)
- [myPOS Demo Partner Portal](https://demo-partners.mypos.com/en)
- [Demo Merchant Account](https://merchant-demo.mypos.eu/en/login)

## Contributing and support

See [CONTRIBUTING.md](CONTRIBUTING.md). Report security concerns privately to integrations@mypos.com. The [developer documentation](https://developers.mypos.com/) is authoritative if it ever differs from these skills.

## License

Licensed under the [Apache License 2.0](LICENSE). The Apache license does not grant rights to use the myPOS name or trademarks.
