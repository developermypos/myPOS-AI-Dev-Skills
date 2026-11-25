# myPOS Skills for Gemini CLI

This extension provides guidance for building myPOS payment integrations.

Use the appropriate skill file when a request involves myPOS payment integration:

- **API Gateway (TypeScript)** (Node.js/TypeScript SDK): [skills/mypos-api-gateway-ts/SKILL.md](skills/mypos-api-gateway-ts/SKILL.md)
- **API Gateway (REST)** (direct HTTP, any language — OAuth2, ePOS, eCommerce, Banking): [skills/mypos-api-gateway-rest/SKILL.md](skills/mypos-api-gateway-rest/SKILL.md)
- **Checkout (TypeScript)** (web payment forms, TypeScript SDK): [skills/mypos-checkout-ts/SKILL.md](skills/mypos-checkout-ts/SKILL.md)
- **Checkout API (REST)** (direct HTTP, any language — RSA signatures, all checkout operations): [skills/mypos-checkout-rest/SKILL.md](skills/mypos-checkout-rest/SKILL.md)
- **Checkout (PHP)** (web payment forms, PHP): [skills/mypos-checkout-php/SKILL.md](skills/mypos-checkout-php/SKILL.md)
- **Checkout (Embedded)** (iFrame form, no redirect, Apple/Google Pay): [skills/mypos-checkout-embedded/SKILL.md](skills/mypos-checkout-embedded/SKILL.md)
- **Mobile Checkout (Android)** (in-app card payments inside your own native Android app): [skills/mypos-checkout-mobile-android/SKILL.md](skills/mypos-checkout-mobile-android/SKILL.md)
- **Mobile Checkout (iOS)** (in-app card payments inside your own native iOS app): [skills/mypos-checkout-mobile-ios/SKILL.md](skills/mypos-checkout-mobile-ios/SKILL.md)
- **Smart SDK (Android)** (Android app on myPOS Smart terminal): [skills/mypos-smart-android/SKILL.md](skills/mypos-smart-android/SKILL.md)
- **Terminal SDK (Android)** (Android app controlling myPOS terminal remotely): [skills/mypos-terminal-android/SKILL.md](skills/mypos-terminal-android/SKILL.md)
- **Terminal SDK (iOS)** (iOS app controlling myPOS terminal over Bluetooth): [skills/mypos-terminal-ios/SKILL.md](skills/mypos-terminal-ios/SKILL.md)
- **Terminal SDK (.NET)** (.NET/C# app controlling myPOS terminal over USB or TCP-IP): [skills/mypos-terminal-dotnet/SKILL.md](skills/mypos-terminal-dotnet/SKILL.md)
- **Glass — SoftPOS (Android)** (Soft POS on standard NFC-enabled Android device): [skills/mypos-glass-android/SKILL.md](skills/mypos-glass-android/SKILL.md)
- **IPP — Integrated Payments Protocol** (binary wire protocol for serial/USB/Bluetooth/WiFi terminal communication, all 31 methods; DCM log analysis and debugging): [skills/mypos-ipp/SKILL.md](skills/mypos-ipp/SKILL.md)
- **myPOS Integration Guide** (starting point — routes to the right SDK or API, compares options, answers cross-skill questions, detects and analyzes IPP logs from any format): [skills/mypos-info/SKILL.md](skills/mypos-info/SKILL.md)

Use the references under each skill's `references/` directory for detailed feature guidance.

Developer documentation: https://developers.mypos.com/
