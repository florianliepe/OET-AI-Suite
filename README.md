# OET AI Suite

Public bilingual Eraneos marketing site. Five products: Intellectual Twin, OrgDesign Simulator (ORGVis, under construction), Storage Agent, AI Vision Studio and NoteTaker. SkillDesign and the other diagram products are future scope.

## Development

Node.js 22 or newer. No frontend dependencies.

```sh
npm run build
npm test
npm run dev
```

Open http://127.0.0.1:4173/de/. Local preview does not call the gateway. The Azure deployment provides `/api/chat` through managed Azure Functions.

## Content

Edit `content/products.json`; German and English content share stable product slugs. `draft` marks incomplete product descriptions, `construction` marks products in development. Storage Agent, AI Vision Studio and NoteTaker await product-owner input. ORGVis marketing copy is derived from the supplied October 2026 workshop/storyline documentation; this is not an implementation or validation of the OrgVis product itself. No source attachments, customer datasets or sample analysis numbers are published.

Future industry pages can be generated under `/{language}/products/{product}/{industry}/`. The existing insurance site is linked from Intellectual Twin.

## Branches and deployment

- `develop`: integration branch; pushes deploy an Azure branch preview.
- `main`: production; merge reviewed changes from `develop`.
- Pull requests to main run checks only. Pushes deploy after checks pass.
- Workflow: `.github/workflows/deploy.yml`.
- GitHub secret: `AZURE_STATIC_WEB_APPS_API_TOKEN` for this app only.
- Azure app: `oet-ai-suite`, resource group `rg-ai-intellectual-twin`, West Europe.
- Default production URL: https://lemon-beach-0f7f19b03.1.azurestaticapps.net

The initial resource uses Free SKU. Review service limits and the desired production support tier before expanding traffic. Deployment does not depend on GitHub Pages.

## Server configuration

Set through Azure Static Web App application settings, never through frontend variables or tracked files:

- `LLM_API_KEY`
- `LLM_BASE_URL` (default `https://ai-gateway.eraneos.com`; include `/v1` only if required by the gateway)
- `LLM_MODEL` (default `gpt-5.1`)

The API has bounded input, output and request time, same-origin browser validation, and best-effort per-instance burst limits. These in-memory limits are not a distributed quota; enforce a gateway spending/rate limit for public exposure. Chat questions are forwarded to the gateway; this app does not persist them. Gateway retention follows the gateway’s own configuration. Chat answers are grounded in the same product data as the website and do not use uploaded documents yet. History is not sent between questions.

## Forms

FormSubmit receives every inquiry at `oet-ai-suite@eraneos.com`. CC contains the product owner, Florian Liepe and Marvin Toellner, with duplicates removed. Overview inquiries copy Florian and Marvin. FormSubmit CAPTCHA remains enabled. The form has a honeypot, required privacy acknowledgement and a return URL on the originating host.

The mailbox owner may need to activate FormSubmit on first submission. A full delivery check requires a real submission and mailbox confirmation; a return page alone does not verify delivery. No email delivery is claimed without that check.

## Validation

`npm test` validates translated routes, recipients, missing configuration, origin enforcement, server-side knowledge and rate limiting. Browser QA covers desktop/mobile layout, switching language and chat interaction. No attachments or gateway keys belong in public assets, logs or commits.
