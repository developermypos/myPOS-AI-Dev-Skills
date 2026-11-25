> Docs: https://developers.mypos.com/apis
> Identity: https://developers.mypos.com/apis/identity
> ePOS: https://developers.mypos.com/apis/epos
> eCommerce: https://developers.mypos.com/apis/ecommerce
> Banking: https://developers.mypos.com/apis/banking

## Reading Order

**Always read these two first** — every API call depends on them:
1. `references/setup.md` — partner credentials, integration prerequisites
2. `references/authentication.md` — 2-step OAuth2 flow and header construction

Then open the reference file for your sub-API:

3. `references/epos-payments.md` — trigger payments on a myPOS terminal
4. `references/epos-terminals.md` — list, activate, manage terminals
5. `references/ecommerce.md` — payment links, buttons, requests
6. `references/banking.md` — accounts, transactions, statements

Finally:

7. `references/testing.md` — demo environment setup

## All References

| File | Use when... |
|------|-------------|
| `setup.md` | Setting up credentials and understanding the two credential pairs |
| `authentication.md` | Obtaining Bearer token + session token; building the 4-header set |
| `epos-payments.md` | Creating a payment on a terminal, checking status, reversal, refund |
| `epos-terminals.md` | Listing terminals, activating/deactivating, getting receipts |
| `ecommerce.md` | Creating and managing payment links, buttons, and payment requests |
| `banking.md` | Listing accounts, fetching transaction history, downloading statements |
| `testing.md` | Demo environment URL, demo partner portal, test credentials |
