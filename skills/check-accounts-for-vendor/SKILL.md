---
name: check-accounts-for-vendor
description: Check a list of companies (domains or names, for example a CRM or spreadsheet export) for whether each one uses a specific SaaS product or business service, with a cost quote first. Use when the user pastes or attaches accounts and asks which of them use a vendor, a competitor, or a tool they integrate with.
---

# Check an account list for one vendor

Uses the anVendor connector's `scan_service`, which checks one service across up to 10,000
companies. Each company found to use the service costs 1 credit; a company where it is not
found is refunded, and an answer the account already holds is free.

## 1. Prepare the list

- Collect the companies from the user's message or file. Prefer website domains
  (`acme.com`); company names are accepted and matched to at most one domain each. A name
  that matches nothing is skipped and never charged.
- Remove obvious duplicates. Do not add companies the user did not give you.
- Identify the one service to check. For several services, run one scan per service and say
  so before quoting.

## 2. Quote before spending

1. Call `scan_service` with `service` and `companies`, **without** `confirm`. With more than
   one company it returns a quote and starts nothing.
2. Show the user:
   - `companies` in the run and `freeAnswers` (already paid for, or checked recently and not
     found);
   - `maxCredits`, the most the run can charge;
   - `affordableCompanies`. With `mode: "up_to_balance"` the run shortens to what the balance
     covers; with `mode: "all_or_nothing"` it is refused unless every chargeable company is
     covered.
3. If `activeScanId` is set, the same scan is already running. Use `get_scan` on that id.
4. Call again with `confirm: true` only after the user agrees.

A single company starts immediately, so state its cost (up to 1 credit) before calling.

## 3. Follow the run

- The start returns a scan with an `id`, and may already include answers in `results`.
- Call `get_scan` no sooner than `pollAfterSeconds`. If you cannot wait in this environment,
  give the user the scan id and offer to check back. `list_scans` finds recent scans later.
- Page long result lists with `offset` and `limit` (up to 500).
- The run is finished when `status` is `done` or `failed`.

## 4. Report

Summarise the counts first: detected, not detected, failed, credits charged
(`credits.charged`) and, while the run is going, credits still held (`credits.stillHeld`). Then list the detected companies with
location, headcount, adoption percent and the estimated annual spend `label`.

- `detected: false` means checked and not found; it does not prove non-use.
- `detected: null` or `status` failed means there is no verdict. Never count it as a negative;
  offer to retry those companies.
- A detection shows the company uses the vendor, not that it holds a paid contract or plans to
  switch. Spend figures are estimates.
