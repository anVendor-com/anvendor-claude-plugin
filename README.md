# anVendor for Claude

[anVendor](https://anvendor.com) is live SaaS intelligence for competitor prospecting. This
plugin connects Claude to your anVendor account so you can find companies that use a
competitor's SaaS product or business service, check your own account list for a vendor on
demand, and review a company's services with estimated adoption and annual spend. It
includes usage that website scanners cannot see, and live checks when you need a current
answer.

## What's included

- **anVendor connector** (`https://api.anvendor.com/mcp`): the tools Claude calls. You sign
  in with your anVendor account when you connect it.
- **Skills** that teach Claude the workflows:
  - `find-competitor-customers` — "Who uses Pipedrive among software companies in Germany?"
    Discovers companies for a service with location, industry and size filters, then confirms
    the ones you pick.
  - `check-accounts-for-vendor` — "Which of these 200 accounts use Zendesk?" Quotes the cost,
    runs the check after you approve it, and reports detected, not detected and failed
    separately.
  - `analyze-company-stack` — "What does acme.com pay for?" Lists a company's services,
    grouped by category, with estimated adoption and annual spend.

## Getting started

1. Create an anVendor account at [anvendor.com](https://anvendor.com/get-started). The Free
   plan includes 20 credits every 30 days.
2. Add this plugin, then open its **Connectors** tab and connect **anVendor**. On the
   anVendor consent screen, choose which methods Claude may use (company analysis, scans,
   lead discovery) and set a credit budget for the connection. The default budget is 50
   credits a month.
3. Ask Claude a question like the examples above.

## Credits and costs

Discovering companies is free. You pay only for confirmed results:

- Confirming whether a company uses a service costs 1 credit, and is refunded when the
  service is not found. A company checked within the last 30 days and not found is answered
  free, and so is an answer your account already holds.
- Analyzing a company costs 1 credit and is refunded when nothing is found.
- Before a check of more than one company, Claude shows a quote and waits for your approval.

Every charge appears on your [billing page](https://anvendor.com/account/billing), labelled
with the connection that made it. You can change the budget or disconnect Claude at any time
under [API access](https://anvendor.com/account/api).

## What results mean

A detection shows that a company uses the vendor. It is not proof of a paid contract, buying
intent or willingness to switch, and "not detected" does not prove that a company does not
use a service. Spend figures are estimated annual ranges in USD based on published list
prices. anVendor is not a contact database; results contain company-level facts only.

## Data and privacy

The plugin runs nothing on your computer and stores nothing itself. When Claude calls a tool,
it sends the tool's inputs to anVendor at `api.anvendor.com`: service names or domains,
company names or domains you ask about, and any location, industry or size filters. anVendor
keeps these requests and their results in your account history, as it does for searches made
on the website. Sign-in uses OAuth with anvendor.com as the authorization server; Claude
receives an access token scoped to the methods you approve, never your password.

See the [Privacy Policy](https://anvendor.com/privacy) and
[Terms of Service](https://anvendor.com/terms). The REST API and MCP server are documented at
[anvendor.com/docs/api](https://anvendor.com/docs/api).

## Support

Email [support@anvendor.com](mailto:support@anvendor.com).

## License

MIT. See [LICENSE](LICENSE).
