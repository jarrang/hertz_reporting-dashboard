# Hertz EMEA reporting concept

Five separate static preview pages with shared CSS and JavaScript. All figures are sample data. No SFMC API, remote data requests or external assets are used.

## Preview and GitHub Pages
Copy `docs` into the repository. In Settings > Pages choose Deploy from a branch, `main`, `/docs`, then Save. Open `docs/index.html` locally to preview. Navigation retains period, market and audience. Each HTML page runs only its own reporting calculation. Browser Back and direct links work normally.

`docs/assets/reporting.js` owns configuration, sample data and renderers. `docs/assets/reporting.css` owns shared styling. The five HTML files own page layout. Do not paste the sample data provider into production. Synthetic row generation is not a live retrieval strategy or latency benchmark.

## Production boundary: native lookup only
The future SFMC build must use native AMPscript Lookup, LookupRows or LookupOrderedRows against prepared DE outputs. No WSProxy, REST requests, SOAP InvokeRetrieve, API authentication or API-triggered preparation. SSJS is optional for control/formatting, not an API access wrapper. The preview contains no live adapter and is not deployment-ready SFMC code.

Create a separate authenticated CloudPage for each view, using shared Content Blocks for navigation, styles and common rendering. Each page performs only the lookups for that page, then renders its HTML. Do not execute other pages' lookups and hide their output. Full-page navigation is intentional; no iframe, JSON endpoint or background prefetch is required. Server-side lookup time occurs before that page's HTML is available.

Preparation runs on a schedule in Automation Studio. Publish only completed runs and include coverage, as-of date, DefinitionVersion and a stable RunID. Each page first selects the completed run and uses it for all subsequent lookups. Never rebuild a missing summary during a page request.

Suggested prepared output families (schemas still to be designed):
- Overview: period/filter totals and a small daily/monthly trend.
- Campaigns: prepared page buckets and a separate total/page count; individual JobID detail.
- Automations: period journey/email aggregates with validated send-cohort attribution.
- Database: closing/opening snapshots, tier/market breakdowns and monthly counts.
- Audience health: marketable/non-marketable totals, gross acquisition and raw source totals.

Use exact allowlisted period and dimension keys. Keep each lookup comfortably below the documented 2,000-row LookupRows ceiling. Campaign pagination should use prepared page keys, not retrieve every campaign then slice in the browser. Summary totals must be prepared independently of those pages. Overflow, missing preparation or incomplete coverage must never silently produce complete-looking totals. Search, ordering and pagination must have a bounded production design; the preview's local search/export operate on sample rows. Do not promise unlimited full-history exports from the CloudPage. Prefer current-result/page exports unless a bounded complete export is separately validated.

No arbitrary SQL, raw event scans, subscriber-level joins, unbounded loops or fallback API retrievals during page visits. Validate access on every CloudPage, not only the landing page. Validate input and escape output. Keep real data and secrets out of GitHub Pages.

Native lookup functions avoid making REST/SOAP API requests. This is not a blanket statement about CloudPages commercial entitlements or all platform usage. Live execution time, exact retrieval sizes, authentication and account usage must be verified in the published SFMC environment before release. Splitting pages limits work per request; it does not eliminate timeout risk from an expensive lookup.

## Metric limits
Acquisition is new stored S.Check records, not new opt-ins. Raw sources retain their original meanings. Tier applies only to database reporting. Campaign audience/market attribution requires validated naming/mapping; mixed sends must remain unclassified/combined. Automations need prepared send-cohort facts, not ongoing-job lifetime totals. Missing history is not zero. No verified deletion metric is displayed.

## Official references
- https://developer.salesforce.com/docs/marketing/marketing-cloud-ampscript/references/mc-ampscript-data-extension/mc-ampscript-reference-data-extension-lookup-rows.html
- https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/ssjs_WSProxy_useSSJS.html
