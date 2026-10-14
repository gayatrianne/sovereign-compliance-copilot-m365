# Sovereign Compliance Copilot

**Portfolio Project 5 | Declarative Agents | M365 Copilot Extensibility**

Gayatri Anne — AI & Cloud Architect | 18+ Years Enterprise IT

---

## Overview

This project demonstrates three different ways to build a declarative agent for Microsoft 365 Copilot, using the same scenario across all three build paths. The agent is the **Sovereign Compliance Copilot** — a scoped M365 Copilot experience for the compliance team at Sovereign Bank (fictional), grounded in FCA regulations and internal compliance policy documents.

A declarative agent is not a standalone bot. It is a customised version of the native M365 Copilot experience — same GPT-4 class engine, same Microsoft Graph grounding — but with a defined persona, scoped knowledge sources, and constrained scope. Compliance analysts open it in Teams or M365 Copilot Chat and get a specialist experience without leaving their existing tools.

---

## Scenario

**Organisation:** Sovereign Bank (fictional)
**User:** Compliance analyst
**Use case:** FCA regulatory guidance, internal compliance policy Q&A, suitability reporting requirements
**Knowledge source:** KB005-FCA-Suitability-Compliance.docx (SharePoint)
**Scope:** FCA regulations, internal compliance policies, mandatory reporting procedures only

---

## Architecture Decision: Why a Declarative Agent?

| Requirement | Declarative Agent | Copilot Studio Custom Agent |
|---|---|---|
| Users already have M365 Copilot seats | Included at no extra charge | Included at no extra charge |
| Need a scoped, personalised Copilot experience | Correct choice | Adds unnecessary complexity |
| Need API calls, workflow automation, actions | Not appropriate | Correct choice |
| Need source control and CI/CD pipeline | Agents Toolkit path | Not supported natively |

The compliance team at Sovereign Bank does not need a separate bot. They need their Copilot to behave differently — scoped to compliance knowledge, refusing out-of-scope questions, branded for their team. A declarative agent is the right tool. A Copilot Studio custom agent would add licensing cost and orchestration overhead with no benefit.

---

## Three Build Paths Compared

This project builds the same agent three ways to demonstrate the trade-offs between the no-code, low-code, and pro-code paths.

### Part A — Agent Builder (No-Code)

**Tool:** Agent Builder inside M365 Copilot Chat
**Who builds it:** Business user, team lead, no IT involvement required
**Time to build:** Under 10 minutes

**What was built:**
- Named the agent and wrote instructions in the configure panel
- Added KB005-FCA-Suitability-Compliance.docx as the sole knowledge source
- Disabled Outlook, Teams, and Web search to enforce knowledge boundary
- Tested in-scope (FCA suitability) and out-of-scope (UK base rate) queries

**Result:** Working agent with correct in-scope and out-of-scope behaviour, KB05 citations confirmed.

**Ceiling hit immediately:**
- No manifest JSON — no source control, no Git, no version history
- No ALM — agent lives only in the tenant, cannot be packaged or distributed
- No AppSource publishing
- Name field reverted on creation — UI reliability issues
- IT has no visibility — any Copilot-licensed user can create agents with no governance

**When to use this path:** Personal or small-team knowledge agents where speed matters more than governance.

---

### Part B — Copilot Studio Declarative (Low-Code)

**Tool:** Copilot Studio portal (classic experience, Agents > Copilot for Microsoft 365)
**Who builds it:** Developer, power user, architect

**Observation:** This path requires a separate Copilot Studio maker licence beyond the M365 Copilot Premium licence. The "Copilot for Microsoft 365" section is not visible in Copilot Studio without this entitlement. This is a meaningful enterprise architecture consideration — the low-code declarative path carries additional licensing cost that must be factored into the build-vs-buy decision.

**When to use this path:** When the organisation already has Copilot Studio licences, wants managed deployment via Power Platform Admin Center, and needs connectors or governance features that Agent Builder cannot provide — but does not need source control or AppSource publishing.

---

### Part C — Microsoft 365 Agents Toolkit (Pro-Code)

**Tool:** VS Code with Microsoft 365 Agents Toolkit extension (v6.18.0)
**Who builds it:** Developer, architect
**Schema version:** v1.8 (latest as of October 2026)

**What was built:**
- Scaffolded project with Agents Toolkit: `appPackage/`, `env/`, `evals/`, `m365agents.yml`
- Authored `declarativeAgent.json` with name, description, instructions reference, SharePoint knowledge source, and conversation starters
- Authored `instruction.txt` with full persona and scope constraints
- Authored `manifest.json` with Sovereign Bank developer details, short and full descriptions, version-controlled
- Provisioned to dev environment via Agents Toolkit Lifecycle panel
- Agent live in M365 Agent Store as "Sovereign Compliance Copilot — Created by Sovereign Bank"

**Key advantages over Agent Builder:**

| Dimension | Agent Builder | Agents Toolkit |
|---|---|---|
| Source control | No | Yes — full Git history |
| CI/CD pipeline | No | Yes — m365agents.yml |
| Manifest control | No | Full JSON control |
| AppSource publishing | No | Yes |
| Features land here first | No | Yes — schema v1.8 |
| SharePoint docs returned | Up to 10 | Up to 10 |
| Orchestrator | Native Sydney | Native Sydney |

**When to use this path:** Enterprise-grade declarative agents requiring source control, deployment pipelines, AppSource distribution, or access to the latest manifest schema features before they reach Copilot Studio.

---

## Project Structure

```
sovereign-compliance-copilot/
├── appPackage/
│   ├── declarativeAgent.json     # Agent manifest — name, instructions, knowledge, conversation starters
│   ├── instruction.txt           # Agent persona and scope constraints
│   ├── manifest.json             # M365 app manifest — developer, name, description, version
│   ├── color.png                 # Agent icon (colour)
│   └── outline.png               # Agent icon (outline)
├── env/
│   ├── .env.dev                  # Dev environment variables (TEAMS_APP_ID etc.)
│   └── .env.local                # Local environment variables
├── evals/                        # Evaluation framework (Agents Toolkit scaffolded)
├── Screenshots/                  # Portfolio screenshots
├── .gitignore
├── m365agents.yml                # Deployment pipeline configuration
├── m365agents.local.yml          # Local deployment configuration
└── README.md
```

---

## Key Files

### declarativeAgent.json

```json
{
    "$schema": "https://developer.microsoft.com/json-schemas/copilot/declarative-agent/v1.8/schema.json",
    "version": "v1.8",
    "name": "Sovereign Compliance Copilot",
    "description": "FCA compliance specialist for Sovereign Bank compliance team",
    "instructions": "$[file('instruction.txt')]",
    "capabilities": [
        {
            "name": "OneDriveAndSharePoint",
            "items_by_url": [
                {
                    "url": "https://copilot054.sharepoint.com/Shared%20Documents/AgentDocs/Advisor%20Knowledge%20Base/KB005-FCA-Suitability-Compliance.docx"
                }
            ]
        }
    ],
    "conversation_starters": [
        {
            "text": "What are the FCA suitability requirements for retail investment recommendations?"
        },
        {
            "text": "What must be included in a suitability report?"
        },
        {
            "text": "What KYC information must be recorded before making a recommendation?"
        }
    ]
}
```

### instruction.txt

```
You are Sovereign Compliance Copilot, a specialist assistant for the compliance team at Sovereign Bank.

You only answer questions about FCA regulations, internal compliance policies, and mandatory reporting procedures.

Always respond in a professional, precise tone suitable for a regulated financial institution.

If a question falls outside compliance topics, politely decline and remind the user of your scope.

Do not answer general questions, provide personal financial advice, or discuss topics unrelated to Sovereign Bank compliance.
```

---

## Test Results

### In-Scope Test

**Query:** What are the three FCA suitability conditions an adviser must meet before recommending a product?

**Result:** Correct structured answer citing COBS 9, three numbered conditions (financial needs, affordability, risk tolerance), with KB005 citation links. Response stayed fully within compliance scope.

### Out-of-Scope Test

**Query:** What is the current base interest rate in the UK?

**Result:** Agent correctly declined, reminded the user of its compliance scope, and suggested compliance-related alternatives (FCA suitability, Consumer Duty, SMCR, financial promotions). No hallucination, no drift.

---

## Skills Demonstrated

- M365 Copilot extensibility architecture — declarative agents vs custom agents
- Microsoft 365 Agents Toolkit (VS Code) — scaffolding, manifest authoring, provisioning
- Declarative agent manifest schema v1.8 — capabilities, conversation starters, knowledge sources
- SharePoint knowledge grounding via OneDriveAndSharePoint capability
- Enterprise deployment pipeline via m365agents.yml
- Architecture decision-making: when to use Agent Builder vs Copilot Studio vs Agents Toolkit
- Licensing and governance considerations for enterprise M365 Copilot extensibility

---

## Author

Gayatri Anne — AI & Cloud Architect | 18+ Years Enterprise IT

I build AI-powered automation systems that eliminate manual work from business processes. This project is part of a six-project portfolio demonstrating Microsoft Copilot Studio, Azure AI Foundry, and M365 Copilot extensibility skills aligned to UK AI/Cloud Architect job requirements.

GitHub: [github.com/gayatrianne](https://github.com/gayatrianne)
