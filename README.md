# AI Automation Portfolio

A curated collection of automation and AI workflow systems by **Aduroja Akintade**, focused on reliable business-process automation, integrations, AI-assisted workflows, and the infrastructure that supports them.

This repository complements my Cloud/DevOps work by making the automation side of my engineering portfolio inspectable through business context, architecture, testing evidence, routing logic, technical decisions, and selected sanitized implementation evidence.

## Portfolio principles

- **Business problem first.** Each project explains the operational problem before the tooling.
- **Evidence over claims.** Architecture, execution evidence, test records, routing logic, and technical decisions demonstrate the work.
- **Selective public evidence.** Credentials, production webhook URLs, private document IDs, customer data, secrets, and proprietary implementation details are excluded.
- **Clear implementation status.** Commercial, independent portfolio, synthetic-data, demo, and training implementations are labelled separately.
- **Reliability matters.** Fallbacks, duplicate protection, human review, auditability, testing, documentation, and handover are treated as part of system design.

## Featured automation systems

| Project | Focus | Stack | Public evidence |
|---|---|---|---|
| [AI Agency Lead Capture & Qualification](./01-ai-agency-lead-qualification/) | Lead capture, deterministic qualification, CRM logging, acknowledgement | n8n, Google Sheets, Gmail | Routing architecture and five tested outcomes |
| [MedFlow Intake OS](./02-medflow-intake-os/) | Structured intake, deduplication, linked records, routing, audit logging | Jotform, Make, Airtable, Google Sheets | Six-table relational model and implementation evidence |
| [Cupid Errands Intelligent Email Routing](./03-cupid-intelligent-email-routing/) | AI email classification, duplicate protection, deterministic department routing, fallback and retry handling | n8n, Google Gemini, Gmail, Slack | [5-minute video walkthrough](https://www.tella.tv/video/ai-email-routing-system-dql7), five-route end-to-end tests, fallback and duplicate-prevention proof |
| [RAG & AI Agent Workflows](./04-rag-ai-agent-workflows/) | Document ingestion, embeddings, vector retrieval, agent memory | n8n, OpenRouter, OpenAI Embeddings, Supabase | Training implementation architecture and security testing |
| [WhatsApp / Chatwoot CRM Automation](./05-whatsapp-chatwoot-crm/) | Messaging ingestion, webhook processing, CRM-style logging | n8n, Chatwoot, WhatsApp, Google Sheets, Docker | Development and architecture evidence |
| [AI-Assisted Support with Human Review](./06-ai-support-human-review/) | Context retrieval, AI drafting, approval boundary, CRM follow-up | AI workflow, Gmail, HubSpot | Human-in-the-loop workflow evidence |
| [Self-Hosted n8n Platform](./07-self-hosted-n8n-platform/) | Automation platform hosting and operational infrastructure | Docker, PostgreSQL, Redis, Traefik, HTTPS | Infrastructure and deployment evidence |

## Open demo repository

### WhatsApp AI Order Processor

A separate demo n8n project demonstrating WhatsApp order intake, AI-assisted parsing, payment-flow orchestration, order logging and fulfilment notification using clearly labelled mocked or simulated external integrations.

[Inspect the WhatsApp AI Order Processor repository](https://github.com/aduroja-akintade/whatsapp-order-processor)

## Commercial production case study

### YTRanker SaaS Platform Engineering

YTRanker is represented as a case study because the production source code is private.

Validated scope includes:

- React, Supabase and Vercel production platform work
- Gmail OAuth, Telegram, WhatsApp, OpenRouter, Anthropic and external integrations
- AI-provider migration to OpenRouter
- role-based permissions and operational workflow controls
- regression testing across **6 user roles**
- validation of **40+ feature branches** before handover
- full technical handover documentation

[Read the public YTRanker case study](./commercial-case-studies/ytranker.md)

## Cloud / DevOps foundation

The automation work is supported by hands-on engineering across Terraform, GCP, AWS, Azure, Docker, Kubernetes, GitHub Actions, Ansible, Linux, IAM and cloud networking.

[View my GitHub profile](https://github.com/aduroja-akintade)

## Contact

- Portfolio: https://aduroja-akintade.github.io/
- LinkedIn: https://www.linkedin.com/in/akintade-aduroja
- GitHub: https://github.com/aduroja-akintade
- Upwork: https://www.upwork.com/freelancers/~01c8ba1b4d2162d9f1?mp_source=share
- Location: Kokkola, Finland

---

Public evidence is intentionally sanitized and scoped. Complete reusable implementations are published only where they are explicitly intended as open demo material.