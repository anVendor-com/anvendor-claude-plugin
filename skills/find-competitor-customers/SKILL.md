---
name: find-competitor-customers
description: Find companies that use a competitor's SaaS product or business service, optionally narrowed by location, industry or company size, and confirm the promising ones. Use when the user asks who uses a vendor (for example "who uses HubSpot in Germany"), wants a prospect list of a competitor's customers, or wants to target accounts on a rival tool.
---

# Find a competitor's customers

Uses the anVendor connector. Discovering companies is free; credits are spent only when you
confirm whether a company uses the service.

## 1. Build the search

- **Service**: pass the competitor's domain when you know it (`hubspot.com`), otherwise its name.
- **Location**: never invent location values. Call `suggest_locations` with what the user said
  ("Germany", "Texas", "London") and pass the returned objects unchanged in `locations`.
  Several locations are OR-ed.
- **Industry**: `search_leads` accepts one industry, spelled exactly as in the `industries`
  list from `get_lead_filters`. Pick the closest label; if two fit equally, ask the user.
- **Size**: pass `headcount` buckets exactly as `get_lead_filters` lists them.
- **An account list**: if the user gives companies, pass them in `companies` instead of
  filters. For a list the user wants checked, use the check-accounts-for-vendor skill.

## 2. Run `search_leads` and read the result

- `serviceUnknown: true` means anVendor does not track that service yet. Say so; do not
  retry with other spellings more than once.
- Each company has a `match`:
  - `purchased` — this account already paid for it; figures are included.
  - `detected` — found using the service within the last 30 days.
  - `previously_detected` — found using it earlier.
  - `unconfirmed` — matches the filters but has not been checked for this service.
- `locked: true` means adoption, headcount and spend stay hidden until the company is
  confirmed for this service. Never guess them.
- `recentlyNotDetected: true` means it was checked within 30 days and the service was not
  found. Do not suggest confirming it again.
- `matches` is the total the filters matched; `reachable` is how many can be paged (up to
  10,000). Fetch the next page only when the user asks, by passing `next.offset` (or
  `next.companyCursor`) back with the same filters.

Show the results as a table: company, domain, location, industry, match and last detected
date. Lead with `detected` and `previously_detected` rows.

## 3. Confirm companies (costs credits)

When the user wants figures or confirmation for specific companies:

1. Call `scan_service` with the service and the chosen domains, **without** `confirm`. For more
   than one company this returns a quote and starts nothing.
2. Show the quote: `companies`, `freeAnswers`, `maxCredits` (the most it can charge) and
   `affordableCompanies`. Explain that each company found to use the service costs 1 credit,
   and a company where it is not found is refunded. If `activeScanId` is set, the same scan is
   already running: use `get_scan` on that id instead.
3. Start it with `confirm: true` only after the user agrees to the cost.
4. Poll `get_scan` with the returned id no sooner than `pollAfterSeconds`. If you cannot wait
   in this environment, give the user the scan id and offer to check back.
5. Report `detected`, `notDetected` and `failed` separately. A failed check is not a negative.

`get_balance` shows credits left and the connection's budget; call it when the user asks or
when a quote says the balance does not cover the list.

## How to describe results

- A detection shows that a company uses the vendor. It is not proof of a paid contract, a
  renewal date, dissatisfaction or intention to switch.
- "Not detected" does not prove a company does not use the service.
- Spend figures are estimated annual ranges in USD, not invoices.
