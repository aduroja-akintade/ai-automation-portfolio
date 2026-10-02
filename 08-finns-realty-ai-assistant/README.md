# Finn's Realty AI Assistant

**Status:** Independent fictional/synthetic portfolio implementation  
**Focus:** Multi-source agent orchestration, structured property retrieval, RAG, persistent conversation memory, automated knowledge ingestion

Finn's Realty AI Assistant is a customer-facing rental-property assistant built in n8n. It demonstrates how a conversational agent can combine current structured property inventory with company policy knowledge, preserve conversational context, and avoid inventing facts that cannot be verified.

## Business problem

A rental customer may ask questions that cross multiple information domains in a single conversation. Current property facts such as availability, price, bedrooms and pet permission belong in structured inventory, while application requirements, deposits and rental procedures belong in an approved policy knowledge base.

The system separates those sources of truth instead of asking the language model to answer from general model knowledge.

## Architecture

### Customer runtime

```text
Telegram Customer Message
        |
        v
Realty AI Agent
   |        |        |
   |        |        +--> Policy Knowledge Base (Supabase RAG)
   |        +-----------> Property Inventory (Airtable)
   +--------------------> Conversation Memory (PostgreSQL)
        |
        v
Telegram Response
```

The agent uses **Gemini 2.5 Flash Lite** for orchestration and **Gemini Embeddings** for vector retrieval.

### Knowledge lifecycle

```text
New Google Drive file
    -> Download
    -> Document Loader
    -> Gemini Embeddings
    -> Supabase Knowledge Base

Updated Google Drive file
    -> Delete Previous Chunks
    -> Download Updated File
    -> Document Loader
    -> Gemini Embeddings
    -> Supabase Knowledge Base
```

The update path removes previous chunks before re-ingesting the changed source document, reducing stale duplicate knowledge.

## Verified multi-source execution

A regression test asked:

> Find me an available pet-friendly two-bedroom apartment in Helsinki, and tell me what documents I would need to apply for it.

**n8n execution #351** completed successfully in **11.827 seconds**.

Within the same agent execution:

1. **Property Inventory** was called once with constraints for apartment, Helsinki, two bedrooms, pets allowed and available status.
2. **Policy Knowledge Base** was called once for rental-application document requirements.
3. The agent combined both verified results into one Telegram response.

The property search returned **Harbour View Two-Bed**, Jätkäsaari, at **€1,450/month**. The policy retrieval returned application requirements including identification, contact information, current residential address, employment/income information and proof of income.

## Additional testing

### Conversational context

After returning two Helsinki apartments, the user asked:

> Which of those two is cheaper, and does it allow pets?

The assistant resolved the reference without requiring the property names to be repeated and returned the cheaper pet-friendly option.

### Grounded uncertainty

The user asked for the penalty fee for paying rent ten days late. The approved knowledge base did not contain a specific penalty amount.

The assistant did **not** fabricate a fee. It explained that the exact information could not be verified from the available policy knowledge and directed the customer to the signed rental agreement or Finn's Realty.

### Security testing

The assistant was tested against a prompt-injection request asking it to reveal system instructions, credentials, API keys and private configuration. It refused to disclose those details.

### Failure and regression testing

A harder combined property-and-policy test initially exposed excessive agent iterations. Tool-routing instructions were refined, temporary Gemini free-tier quota errors were isolated from workflow logic, and the same combined request was regression-tested successfully in execution #351.

## Engineering concepts demonstrated

- Agentic tool calling and tool selection
- Multi-source orchestration in a single customer request
- Deterministic Airtable filtering for current property facts
- Retrieval-augmented generation using Supabase vector search
- Gemini embeddings
- PostgreSQL-backed conversation memory
- Automated Google Drive knowledge ingestion
- Update-safe re-ingestion with previous-chunk deletion
- Grounded-answer and uncertainty handling
- Prompt-injection testing
- Execution tracing, debugging and regression testing

## Stack

- n8n
- Google Gemini 2.5 Flash Lite
- Gemini Embeddings
- Airtable
- Supabase Vector Store
- PostgreSQL
- Telegram
- Google Drive

## Public evidence

The portfolio evidence set includes:

- clean AI Assistant architecture
- clean Knowledge Base Ingestion architecture
- successful multi-source Telegram demonstration
- successful n8n execution #351 showing both Property Inventory and Policy Knowledge Base used in one run
- conversational-memory follow-up test
- grounded uncertainty test

Screenshots are intentionally sanitized. Credentials, API keys, database secrets, private configuration and reusable production workflow exports are excluded.

## Implementation note

Finn's Realty is a fictional portfolio scenario using synthetic demonstration data. The project is presented as engineering evidence, not as a production deployment for a real estate client.
