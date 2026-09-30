---
name: analyze-company-stack
description: Show which SaaS products and business services a company uses, with estimated adoption and annual spend. Use when the user asks what tools, software or subscriptions a company uses, wants to research an account before a sales call, or wants to size a company's vendor spend.
---

# Analyze a company's services

Uses the anVendor connector's `analyze_company`. One analysis costs 1 credit and is refunded
when nothing is found. A fresh result this account already paid for is returned free.

## 1. Start the analysis

- Pass the company's website domain when you know it (`stripe.com`); a URL or a company name
  also works.
- Tell the user it costs up to 1 credit before calling, unless they already asked for it.
- Set `refresh: true` only when the user explicitly asks for a new live check of a company
  they already have; it is charged again.

## 2. Wait for the result

- `status: "done"` — the result is included.
- `status: "pending"` — keep the `id`. A full analysis usually takes five to ten minutes, and
  longer for a large company. Call `get_analysis` no sooner than `pollAfterSeconds`; if you
  cannot wait in this environment, give the user the id and offer to check back.
- `status: "failed"` — nothing was charged. Offer to try again later.

## 3. Present it

- Start with the company: name, domain, headcount and location.
- Group `services` by `category`. For each, give the name, `adoptionPercent` when present
  (an estimate of how much of the company uses it) and the estimated annual spend `label`.
  Order by spend, highest first; services without an estimate go last.
- Mention the data date (`dataDate`). If `stale` is true the data is 30 days old or more;
  offer a refresh. If `partial` is true, only some services were checked for this company.

## How to describe results

- Each service listed is one the company uses. It is not proof of a paid contract,
  renewal date or buying intent.
- A service missing from the list is not proof the company does not use it.
- Spend figures are estimated annual ranges in USD based on published list prices, not
  invoices; they tend to lean high.
