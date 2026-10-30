# Product Launch Command Center (Voice AI System)

An orchestrated, multi-agent voice AI command center built using **ElevenLabs Workflow Builder** and **Gemini Flash LLMs**. The system acts as an autonomous switchboard for cross-functional launch operations during the **Project Nightingale** product release (Mobile App v3.0), automatically triaging stakeholder voice calls and routing them to specialized agents for technical readiness, go-to-market execution, or executive briefings.

---

## Live Demo

* **Interactive Voice Agent:** [Talk to Project Nightingale Command Center](https://elevenlabs.io/app/agents/agents/agent_1701m49t7989etesbc6hak7ac19s/workflow?branchId=agtbrch_4001m49t7bp3fk1rma1vgppf7r7z)



---

## Overview & Problem Statement

During high-stakes product launches, Product Managers (PMs) and Technical Program Managers (TPMs) frequently become overloaded human switchboards answering repetitive status queries across engineering, marketing, and leadership.

A single monolithic voice agent fails in production because a single prompt covering all launch facets causes context degradation, cross-domain hallucinations (such as leaking confidential marketing ad spend during engineering queries), and rigid tone delivery.

The **Product Launch Command Center** resolves this through modular multi-agent orchestration:

* **Focused Subagent Prompts:** Each subagent operates with concise, domain-specific instructions.


* **Scoped Knowledge Bases (RAG):** Information boundaries prevent data leakage across functional tracks.


* **Bidirectional Cross-Routing:** Dynamic backward edges allow stakeholders to switch topics mid-conversation without dropping calls.


* **Low-Latency Voice Synthesis:** Paired with fast models (Gemini Flash) to maintain sub-second voice response thresholds.



---

## Launch Context: Project Nightingale

| Parameter | Launch Detail |
| --- | --- |
| **Product** | Mobile App v3.0 — Redesigned Checkout & Perks Loyalty Program |
| **Launch Date** | April 15, 2026 (Staged Rollout: 5% → 25% → 50% → 100%) |
| **Current Progress** | Sprint 9 of 11 (Overall Status: On Track; Yellow flag on App Store review) |
| **Resource Allocation** | 6 Engineers, 2 Designers, 1 QA, plus Marketing and Sales Leads |
| **Budget Standing** | $220,000 Total Budget ($210,000 Projected Spend — Under Budget) |
| **Key Stakeholders** | Diana Walsh (VP Product), Sam Torres (PM), Rachel Nguyen (TPM), Kai Matsumoto (Eng Lead) |

---

## System Architecture & Workflow Map

The workflow consists of **5 functional nodes** interconnected by **12 LLM-conditioned edges**.

```
                                [ Start Node ]
                                      │
                                      ▼
                             [ 1. Greeter Node ]
                        (Intent Triaging & Intake)
                      /               │               \
         LLM Condition          LLM Condition          LLM Condition
                    /                 │                 \
                   ▼                  ▼                  ▼
     [ 2. Technical Readiness ] ◄──────────► [ 4. Executive Briefing ] ◄──────────► [ 3. GTM & Marketing ]
          (KB: Tech + Launch)    Cross-Routing    (KB: All 3 Documents)    Cross-Routing    (KB: GTM + Launch)
                   │                                     │                                  │
                   └─────────────────────────────────────┼──────────────────────────────────┘
                                                         │ LLM Condition (Call Complete)
                                                         ▼
                                                 [ 5. End Call Node ]

```

### Node Specifications

| Node Name | Node Type | Scoped Knowledge Base | Primary Function |
| --- | --- | --- | --- |
| **Greeter Node** | Subagent Node | *None (Zero KB)*| Welcomes the caller, identifies intent, and hands off to specialists without answering directly.|
| **Technical Readiness** | Subagent Node | `KB_Technical_Readiness.txt`<br> <br>`KB_Launch_Plan.txt`| Addresses sprint milestones, QA bugs, p99 API latency, rollback criteria, and on-call rotations.|
| **GTM & Marketing** | Subagent Node | `KB_GTM_Marketing.txt`<br> <br>`KB_Launch_Plan.txt` | Handles campaign schedules, ad spend, press embargoes, messaging rules, and competitor positioning.|
| **Executive Briefing** | Subagent Node | `KB_Launch_Plan.txt`<br> <br>`KB_Technical_Readiness.txt`<br> <br>`KB_GTM_Marketing.txt` | Delivers high-level status briefings, budget summaries, and risk snapshots for leadership. |
| **End** | End Call Node | *None* | Gracefully terminates the voice session upon user confirmation.|

---

## Routing Logic & LLM Edge Conditions

The workflow evaluates user speech in real time across **12 natural language edge conditions**:

### 1. Forward Routing from Greeter (3 Edges)

* **Greeter → Technical Readiness:** `"The user is asking about engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation."`


* **Greeter → GTM & Marketing:** `"The user is asking about marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program."`


* **Greeter → Executive Briefing:** `"The user wants a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness."`



### 2. Specialist Cross-Routing / Backward Edges (6 Edges)

* **Technical Readiness → GTM & Marketing:** `"The user is now asking about marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program instead of technical topics."`


* **Technical Readiness → Executive Briefing:** `"The user is now asking for a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness instead of technical details."`


* **GTM & Marketing → Technical Readiness:** `"The user is now asking about engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation instead of marketing topics."`


* **GTM & Marketing → Executive Briefing:** `"The user is now asking for a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness instead of marketing details."`


* **Executive Briefing → Technical Readiness:** `"The user wants to drill deeper into engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation."`


* **Executive Briefing → GTM & Marketing:** `"The user wants to drill deeper into marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program."`



### 3. Session Termination Edges (3 Edges)

* **All Specialists → End Call:** `"The user has indicated they are done, said goodbye, thanked the agent, or has no more questions."`



---



## Knowledge Base Scoping Reference

* `KB_Launch_Plan.txt` — Master plan covering feature specs, milestones, risk matrix, success metrics, stakeholder directory, budget allocations, and post-launch roadmap.


* `KB_Technical_Readiness.txt` — Sprint progress, system architecture, API benchmarks, QA defect tallies, staged rollout plan, automatic rollback thresholds, and launch week on-call shifts.


* `KB_GTM_Marketing.txt` — Channel-by-channel budgets ($65K total), PR embargo windows, messaging guardrails, competitive matrices (ShopFast, QuickCart, BuyNow), and sales objection playbooks.



---

## Verification & Testing Suite

| Test Scenario | Input Utterance | Expected System Response |
| --- | --- | --- |
| **1. Technical Routing** | *"Hey, are we going to be ready for launch from an engineering perspective?"* | Routes to Technical Specialist; reports Sprint 9/11 status, 0 critical defects, passed load testing, and staged rollout dates. |
| **2. Marketing Routing** | *"What’s our marketing plan for launch week?"* | Routes to GTM Specialist; outlines $45K paid social split, press embargo ending April 10, and April 22 referral push.|
| **3. Executive Summary** | *"Just give me the 30-second version — are we on track?"* | Routes to Executive Briefing; delivers a 4–5 sentence synthesis on April 15 readiness, App Store timing risk, and $210K spend against $220K budget.|
| **4. Boundary Enforcement** | User asks Technical Specialist: *"What’s our ad spend?"* | Subagent declines marketing budget disclosure and redirects caller to the GTM track.|
| **5. Dynamic Cross-Routing** | In Technical track, user says: *"Actually, can you tell me about the competitive landscape instead?"* | Seamlessly transitions caller to GTM Specialist to provide competitive comparisons against ShopFast, QuickCart, and BuyNow.|

---

## Troubleshooting Guide

| Issue Encountered | Root Cause | Remediation Step |
| --- | --- | --- |
| **Greeter answers directly instead of routing** | Greeter prompt lacks strict routing boundary.| Verify Greeter prompt includes: *"DO NOT try to answer their question yourself. Your ONLY job is to route."*|
| **Misrouting between specialists** | Overlapping or ambiguous LLM edge condition text.| Add explicit negative qualifiers to conditions (e.g., *"instead of technical topics"*).|
| **Hallucinated or generic replies** | Missing KB document bindings or incomplete vector indexing.| Re-upload missing `.txt` files in the specific subagent configuration and confirm indexing status.|
| **Stuck in specialist node** | Missing backward/cross-routing edges.| Ensure all 6 bidirectional cross-routing edges are connected between the three specialist nodes.|
| **High voice latency (>2s)** | Using high-overhead LLM models or heavy audio profiles.| Switch model configuration to Flash models (e.g., Gemini 1.5 Flash / Gemini 2.5 Flash).|
