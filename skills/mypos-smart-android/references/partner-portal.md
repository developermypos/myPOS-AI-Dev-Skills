> Partner Portal: https://developers.mypos.com/guides/onboarding-in-partners-portal
> Create integration: https://developers.mypos.com/guides/mypos-partner-create-integration
> AppMarket guide: https://developers.mypos.com/guides/appmarket-guide

## Deployment Lifecycle (Plan for This from Day One)

Apps for myPOS Smart devices are distributed through the **myPOS AppMarket** — the app store built into every Smart terminal. The deployment pipeline is:

**Develop → Test → Upload APK → App Review → Distribute via AppMarket**

This process takes time. Start the Partner Portal registration early.

## Step 1: Register in the Partner Portal

- Production: https://partners.mypos.com/en
- Register your organization and create an integration

## Step 2: Develop and Test

- Develop against a **Demo Debug POS device** (see `references/testing.md`)
- The Demo device allows running unsigned/development APKs

## Step 3: Upload Your APK

- Sign your release APK
- Upload it to the Partner Portal: https://developers.mypos.com/guides/appmarket-guide/application-upload
- Complete the app metadata (name, description, screenshots)

## Step 4: App Review

- myPOS reviews the APK for compliance and security
- Guide: https://developers.mypos.com/guides/appmarket-guide/app-review

## Step 5: Distribute

- Once approved, the app is available for merchants to install via the AppMarket app on their Smart device
- Guide: https://developers.mypos.com/guides/appmarket-guide/distribute-an-app

## AppMarket Guide Links

| Topic | URL |
|-------|-----|
| Develop an app | https://developers.mypos.com/guides/appmarket-guide/develop-an-app |
| Test your app | https://developers.mypos.com/guides/appmarket-guide/test-your-app |
| Upload APK | https://developers.mypos.com/guides/appmarket-guide/application-upload |
| App review | https://developers.mypos.com/guides/appmarket-guide/app-review |
| Distribute | https://developers.mypos.com/guides/appmarket-guide/distribute-an-app |

## Reading Order

1. This file — understand the deployment pipeline.
2. `references/setup.md` — SDK setup.
3. `references/testing.md` — get a Demo Debug POS device.

## See Also

- references/setup.md
- references/testing.md
