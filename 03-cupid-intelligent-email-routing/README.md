# Cupid Errands Intelligent Email Routing

**Status:** Completed AI automation portfolio system, end-to-end tested  
**Stack:** n8n, Google Gemini, Gmail, Slack, n8n Data Table / processed state, Structured Output  
**Pattern:** AI intent classification, duplicate protection, deterministic department routing, fallback handling, retry handling, processed-state tracking

## Video portfolio

**5-minute walkthrough:** [Watch the Cupid Errands demo on Tella](https://www.tella.tv/video/ai-email-routing-system-dql7)

The walkthrough covers the controlled Gmail trigger, duplicate guard, Gemini classification, structured output, deterministic routing, Gmail and Slack delivery, fallback behavior, retry handling, and duplicate-prevention proof.

## Business problem

A shared inbox becomes a bottleneck when staff must manually read every message, determine which department owns it, forward it, notify the right team, and avoid processing the same email twice.

This workflow automates that routing problem while keeping operational control explicit and inspectable.

## Departments

The workflow routes messages to five operational destinations:

1. Sales
2. Logistics
3. Billing
4. Customer Support
5. Office / Admin

Office / Admin also provides the default operational path when the classification cannot be mapped safely to a specialist route.

## Final workflow architecture

```mermaid
flowchart LR
    A[Gmail Trigger: Cupid-Test] --> B[Edit Fields]
    B --> C[Duplicate Guard / Processed Email Check]
    C --> D[Google Gemini AI Classifier]
    D --> E[Structured Output Parser]
    E --> F{Department Router}
    F --> S[Sales]
    F --> L[Logistics]
    F --> BIL[Billing]
    F --> CS[Customer Support]
    F --> O[Office / Admin]
    S --> S1[Sales Gmail]
    S1 --> S2[Sales Slack]
    L --> L1[Logistics Gmail]
    L1 --> L2[Logistics Slack]
    BIL --> B1[Billing Gmail]
    B1 --> B2[Billing Slack]
    CS --> C1[Support Gmail]
    C1 --> C2[Support Slack]
    O --> O1[Office/Admin Gmail]
    O1 --> O2[Office/Admin Slack]
    S2 --> Z[Mark Processed]
    L2 --> Z
    B2 --> Z
    C2 --> Z
    O2 --> Z
```

Detailed control architecture: [`ARCHITECTURE.md`](./ARCHITECTURE.md)

## Controlled Gmail intake

The trigger is restricted using a dedicated `Cupid-Test` Gmail label. This was added after testing showed that a broad inbox trigger allowed unrelated messages into the workflow. The label acts as an explicit test gate so only intended messages enter the automation.

## Duplicate protection

Before classification, the workflow checks the incoming Gmail message identifier against the `processed_emails` tracking table.

If the message already exists, the Duplicate Guard returns no output and execution stops before another AI call, department email, Slack notification, or processed-state write occurs.

## AI classification contract

Google Gemini interprets the email and returns structured output for downstream execution. The classification layer produces:

- `department`
- `confidence`
- `reason`

The LLM is responsible for interpreting unstructured intent. Routing remains controlled by explicit n8n workflow logic.

> **Engineering principle:** AI handles interpretation. Deterministic workflow logic controls the action.

## Deterministic routing

The Department Router maps the structured `department` value to the corresponding branch. This keeps the execution path transparent even though AI is used upstream for interpretation.

Each branch performs department-specific Gmail delivery and Slack notification before the workflow updates its processed state.

## Verified end-to-end testing

The following tests were completed successfully:

| Test | Result |
| --- | --- |
| Sales request | Routed only to Sales email + Sales Slack, then marked processed |
| Logistics request | Routed only to Logistics email + Logistics Slack, then marked processed |
| Billing request | Routed only to Billing email + Billing Slack, then marked processed |
| Customer Support request | Routed only to Customer Support email + Support Slack, then marked processed |
| Office/Admin request | Routed only to Office/Admin email + Office/Admin Slack, then marked processed |
| Ambiguous request | Fell back to Office/Admin as designed |
| Duplicate message ID | Duplicate Guard returned no output and stopped downstream execution |

## Retry handling

During testing, Google Gemini returned a temporary `503 Service Unavailable` response caused by high demand. Retry-on-fail was added with three attempts and a delay between attempts so a transient provider failure does not immediately terminate the workflow.

The failed execution was not marked as processed, and a later run completed successfully.

## Delivery and state tracking

Each successful route triggers:

- department-specific Gmail delivery
- department-specific Slack notification
- final processed-state update

The processed table stores operational metadata such as message ID, department, processed time, sender, subject, and status.

## Engineering decisions

- AI is used for unstructured email interpretation, not uncontrolled execution.
- Controlled Gmail intake prevents unrelated inbox traffic from entering the workflow.
- Duplicate protection occurs before repeated downstream actions and before another model call.
- Structured output creates a machine-readable boundary between AI interpretation and workflow routing.
- Department execution is deterministic and inspectable.
- Office / Admin provides an operational fallback path.
- Retry handling addresses transient external-model failures.
- Processed-state tracking supports traceability and prevents repeat handling.

## Public evidence

Public evidence consists of the video walkthrough, documented architecture, verified test outcomes, and selected implementation details. Credentials, API keys, personal test addresses, Slack identifiers, and other private configuration are intentionally excluded.

## Skills demonstrated

- n8n workflow orchestration
- Google Gemini integration
- AI intent classification
- structured-output design
- JSON handling
- deduplication and idempotency
- deterministic routing
- Gmail and Slack integrations
- fallback design
- retry handling
- operational state tracking
- end-to-end test design
