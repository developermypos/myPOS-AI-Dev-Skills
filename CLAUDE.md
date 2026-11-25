# myPOS Integration Skills

When helping with a myPOS integration, identify which SDK or API the developer needs and load the relevant skill below.

## Available Skills

**API Gateway (TypeScript)** — server-side TypeScript/Node.js, banking/ecommerce/ePOS APIs, multi-merchant:
@skills/mypos-api-gateway-ts/SKILL.md

**API Gateway (REST)** — direct HTTP/REST integration in any language, OAuth2, ePOS terminal payments, payment links, banking:
@skills/mypos-api-gateway-rest/SKILL.md

**Checkout (TypeScript)** — web payments, hosted payment page, card tokenization, webhooks (JS/TS):
@skills/mypos-checkout-ts/SKILL.md

**Checkout API (REST)** — direct HTTP integration in any language, RSA signatures, redirect checkout, server-side payments, all Checkout API operations:
@skills/mypos-checkout-rest/SKILL.md

**Checkout (PHP)** — web payments in PHP, redirect checkout, server-side charging, IPN:
@skills/mypos-checkout-php/SKILL.md

**Checkout (Embedded)** — iFrame payment form embedded directly in your page, no redirect, Apple/Google Pay:
@skills/mypos-checkout-embedded/SKILL.md

**Mobile Checkout (Android)** — in-app card payments inside your own native Android app, no redirect:
@skills/mypos-checkout-mobile-android/SKILL.md

**Mobile Checkout (iOS)** — in-app card payments inside your own native iOS app, no redirect:
@skills/mypos-checkout-mobile-ios/SKILL.md

**Smart SDK (Android)** — Android app running directly on a myPOS Smart terminal device:
@skills/mypos-smart-android/SKILL.md

**Terminal SDK (Android)** — separate Android device controlling a myPOS terminal over Bluetooth/USB/TCP-IP:
@skills/mypos-terminal-android/SKILL.md

**Terminal SDK (iOS)** — iOS app controlling a myPOS terminal over Bluetooth (Objective-C / Swift):
@skills/mypos-terminal-ios/SKILL.md

**Terminal SDK (.NET)** — .NET/C# application controlling a myPOS terminal over USB/Serial or TCP-IP:
@skills/mypos-terminal-dotnet/SKILL.md

**Glass — SoftPOS (Android)** — soft POS on a standard NFC-enabled Android device, Visa/Mastercard only:
@skills/mypos-glass-android/SKILL.md

**IPP (Integrated Payments Protocol)** — binary wire protocol for direct serial/USB/Bluetooth/WiFi communication with myPOS terminals; all 31 methods, all stages, Cash Register and Slave mode; DCM log analysis and debugging:
@skills/mypos-ipp/SKILL.md

**myPOS Integration Guide** — starting point for all myPOS integrations; routes to the right SDK or API, compares options, answers cross-cutting questions (environments, auth, webhooks, card security); automatically detects and analyzes IPP protocol logs from any format:
@skills/mypos-info/SKILL.md

## Quick Selection Guide

| Developer says... | Load skill |
|-------------------|------------|
| "REST API", "backend", "Node.js", "TypeScript", "API Gateway" (TS SDK) | mypos-api-gateway-ts |
| "API Gateway", "REST", "HTTP", "no SDK", "any language", "direct integration", "OAuth2" | mypos-api-gateway-rest |
| "checkout page", "payment link", "online store", "IPN", "webhook" (JS/TS SDK) | mypos-checkout-ts |
| "Checkout API", "REST", "HTTP", "no SDK", "any language", "RSA signature", "direct integration" | mypos-checkout-rest |
| "PHP", "Laravel", "Symfony", "checkout", "IPN" | mypos-checkout-php |
| "embedded", "iFrame", "no redirect", "inline payment", "Apple Pay", "Google Pay" | mypos-checkout-embedded |
| "in-app payment", "native app checkout", "add payments to my Android app", "myPOS Mobile Checkout SDK Android" | mypos-checkout-mobile-android |
| "in-app payment", "native app checkout", "add payments to my iOS app", "myPOS Mobile Checkout SDK iOS" | mypos-checkout-mobile-ios |
| "Smart terminal", "myPOS mini", "POS app on device" | mypos-smart-android |
| "Bluetooth", "USB", "slave", "connected terminal", "Android controller" | mypos-terminal-android |
| "iOS", "Swift", "Objective-C", "iPhone", "iPad", "terminal" | mypos-terminal-ios |
| ".NET", "C#", "WinForms", "WPF", "COM port", "serial" | mypos-terminal-dotnet |
| "Glass", "SoftPOS", "soft POS", "NFC phone", "tap to pay" | mypos-glass-android |
| "IPP", "serial", "USB", "Bluetooth", "WiFi", "wire protocol", "binary protocol", "direct terminal", "slave mode", "cash register mode", "terminal SDK internals", "DCM log", "DCM trace", "IPP log", "debug protocol log", "analyze log" | mypos-ipp |
| "where do I start", "which SDK", "which API", "help me choose", "what can I do with myPOS", "getting started", "new project", "overview", "don't know which", "I have a log", "analyze this log", "what integration" | mypos-info |
