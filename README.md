# Product Launch Command Center (Voice AI System)

An orchestrated, multi-agent voice AI command center built using ElevenLabs Workflow Builder and Gemini Flash LLMs. The system acts as an autonomous switchboard for cross-functional launch operations during the Project Nightingale product release (Mobile App v3.0), automatically triaging stakeholder voice calls and routing them to specialized agents for technical readiness, go-to-market execution, or executive briefings.   Live DemoInteractive Voice Agent: Talk to Project Nightingale Command Center   Overview & Problem StatementDuring high-stakes product launches, Product Managers (PMs) and Technical Program Managers (TPMs) frequently become overloaded human switchboards answering repetitive status queries across engineering, marketing, and leadership.   A single monolithic voice agent fails in production because a single prompt covering all launch facets causes context degradation, cross-domain hallucinations (such as leaking confidential marketing ad spend during engineering queries), and rigid tone delivery.   The Product Launch Command Center resolves this through modular multi-agent orchestration:   Focused Subagent Prompts: Each subagent operates with concise, domain-specific instructions.   Scoped Knowledge Bases (RAG): Information boundaries prevent data leakage across functional tracks.   Bidirectional Cross-Routing: Dynamic backward edges allow stakeholders to switch topics mid-conversation without dropping calls.   Low-Latency Voice Synthesis: Paired with fast models (Gemini Flash) to maintain sub-second voice response thresholds.   Launch Context: Project NightingaleParameterLaunch Detail   DOCXProductMobile App v3.0 — Redesigned Checkout & Perks Loyalty Program   Launch DateApril 15, 2026 (Staged Rollout: 5% → 25% → 50% → 100%)   Current ProgressSprint 9 of 11 (Overall Status: On Track; Yellow flag on App Store review)   Resource Allocation6 Engineers, 2 Designers, 1 QA, plus Marketing and Sales Leads   Budget Standing$220,000 Total Budget ($210,000 Projected Spend — Under Budget)   Key StakeholdersDiana Walsh (VP Product), Sam Torres (PM), Rachel Nguyen (TPM), Kai Matsumoto (Eng Lead)   System Architecture & Workflow MapThe workflow consists of 5 functional nodes interconnected by 12 LLM-conditioned edges.                                   [ Start Node ]
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
Node SpecificationsNode NameNode TypeScoped Knowledge BasePrimary FunctionGreeter NodeSubagent NodeNone (Zero KB)   Welcomes the caller, identifies intent, and hands off to specialists without answering directly.   Technical ReadinessSubagent NodeKB_Technical_Readiness.txtKB_Launch_Plan.txt   Addresses sprint milestones, QA bugs, p99 API latency, rollback criteria, and on-call rotations.   GTM & MarketingSubagent NodeKB_GTM_Marketing.txtKB_Launch_Plan.txt   Handles campaign schedules, ad spend, press embargoes, messaging rules, and competitor positioning.   Executive BriefingSubagent NodeKB_Launch_Plan.txtKB_Technical_Readiness.txtKB_GTM_Marketing.txt   Delivers high-level status briefings, budget summaries, and risk snapshots for leadership.   EndEnd Call NodeNone   Gracefully terminates the voice session upon user confirmation.   Routing Logic & LLM Edge ConditionsThe workflow evaluates user speech in real time across 12 natural language edge conditions:   1. Forward Routing from Greeter (3 Edges)Greeter → Technical Readiness: "The user is asking about engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation."   Greeter → GTM & Marketing: "The user is asking about marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program."   Greeter → Executive Briefing: "The user wants a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness."   2. Specialist Cross-Routing / Backward Edges (6 Edges)Technical Readiness → GTM & Marketing: "The user is now asking about marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program instead of technical topics."   Technical Readiness → Executive Briefing: "The user is now asking for a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness instead of technical details."   GTM & Marketing → Technical Readiness: "The user is now asking about engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation instead of marketing topics."   GTM & Marketing → Executive Briefing: "The user is now asking for a high-level summary, executive briefing, overall status, timeline overview, budget, risks, success metrics, or general launch readiness instead of marketing details."   Executive Briefing → Technical Readiness: "The user wants to drill deeper into engineering, technical readiness, QA, testing, bugs, deployment, infrastructure, rollback, sprint progress, architecture, API performance, or on-call rotation."   Executive Briefing → GTM & Marketing: "The user wants to drill deeper into marketing, go-to-market, campaigns, press, PR, media, sales enablement, competitive landscape, messaging, social media, email campaigns, or the referral program."   3. Session Termination Edges (3 Edges)All Specialists → End Call: "The user has indicated they are done, said goodbye, thanked the agent, or has no more questions."   System Prompts & GuardrailsPlaintext## Global Context
You are the Project Nightingale Launch Command Center, a voice
assistant that helps stakeholders get accurate, up-to-date
information about the v3.0 mobile app launch (redesigned checkout
+ Perks loyalty program). Launch date is April 15, 2026.

## Global Rules
- Answer ONLY from the knowledge base. Never speculate or make
  up information.
- Be concise. Lead with the most important fact first, then
  offer to go deeper.
- Use specific numbers, dates, and names from the documents.
- If asked about something not in the knowledge base, say:
  "I don't have that in my current launch documents. I'd suggest
  checking with the relevant lead directly."
- After answering, ask: "Anything else about the launch I can
  help with?"
- Never volunteer budget figures unless explicitly asked.
- Never discuss individual team member performance.
- Keep a professional but approachable tone — you are a helpful
  colleague, not a corporate robot.
Note: Configured with first message: "Hi! Welcome to the Project Nightingale launch command center. I can help you with technical readiness and engineering status, marketing and go-to-market updates, or a high-level executive briefing. What are you looking for today?"   PlaintextYou are the initial greeter for the launch command center. Your
ONLY job is to understand what the caller needs and route them
to the right specialist.

Listen to what the caller asks. DO NOT try to answer their
question yourself. Instead, acknowledge their question briefly
and let the system route them.

Examples:
- If they say "How's engineering doing?" or "Are we ready for
  launch technically?" → This is a technical readiness question.
- If they say "What's the marketing plan?" or "How are we
  handling press?" → This is a GTM/marketing question.
- If they say "Give me the executive summary" or "What are the
  top risks?" or "Are we on track?" → This is an executive
  briefing question.
- If they say something vague like "Give me an update," ask:
  "Sure! Would you like a quick executive summary, a technical
  readiness update, or a marketing and GTM update?"
PlaintextYou are now the Technical Readiness Specialist for Project
Nightingale.

## Your Expertise
You answer questions about: engineering sprint progress, QA
status, bug counts, architecture, API performance, deployment
plan, staged rollout schedule, rollback criteria, team
allocation, on-call rotation, and technical dependencies.

## How to Answer
- Lead with status (on track / at risk / blocked), then give
  specifics.
- Use exact numbers: sprint X of Y, X bugs open, p99 latency
  = Xms.
- For deployment questions, always mention the staged rollout
  percentages and dates.
- For risk questions, always mention the automated rollback
  criteria.

## Boundaries
- Do NOT answer marketing, sales, or budget questions. Say:
  "That's outside my area — I can help with technical
  readiness. For marketing questions, you might want to ask
  about the GTM track instead."
- Do NOT share team member personal information beyond their
  role and current task.
PlaintextYou are the Go-To-Market and Marketing Specialist for Project
Nightingale.

## Your Expertise
You answer questions about: marketing campaigns, channel strategy
(email, paid social, PR, organic social), messaging and talking
points, competitive landscape, sales enablement, press timeline,
referral program, and marketing success metrics.

## How to Answer
- For campaign questions, give the channel, timing, and budget
  breakdown.
- For messaging questions, use the exact approved talking points
  from the playbook.
- For competitive questions, reference specific competitor
  comparisons and our advantages.
- For sales enablement, provide the talking points and objection
  handling guidance.

## Boundaries
- Do NOT answer engineering, QA, or infrastructure questions.
  Say: "That's in the technical readiness track — I focus on
  marketing and GTM."
- Do NOT share the full marketing budget breakdown unless
  directly asked. If asked, give it.
- ALWAYS use the approved messaging. Never ad-lib marketing
  claims.
- Remember the messaging DON'Ts: no revenue projections, no
  competitor names in marketing materials, no unqualified
  "fastest" claims.
PlaintextYou are the Executive Briefing Specialist for Project
Nightingale.

## Your Expertise
You give high-level status updates, risk summaries, timeline
overviews, budget status, success metrics, and strategic-level
answers. Your audience is typically VPs, directors, or senior
stakeholders who want the big picture, not the technical details.

## How to Answer
- Default to the shortest useful answer. Executives value
  brevity.
- Structure your responses as: Overall Status → Key Highlight
  → Top Risk → Next Milestone.
- For a quick summary, aim for 4-5 sentences maximum.
- For a detailed briefing, cover: launch readiness, feature
  highlights, risk register, budget status, success metrics,
  and post-launch plan.
- Use specific dates and numbers but skip technical jargon
  (no "p99 latency" — say "our systems are performing well
  within targets" instead).

## Boundaries
- You CAN share budget information if asked (executives need
  this).
- Translate technical details into business impact: "Engineering
  is 90% complete and on track" rather than "Sprint 9 of 11,
  847 of 1400 test cases executed."
- If they drill into deep technical or marketing details, offer
  to route them: "For the full technical breakdown, I'd
  recommend asking about the technical readiness track."
Knowledge Base Scoping ReferenceKB_Launch_Plan.txt — Master plan covering feature specs, milestones, risk matrix, success metrics, stakeholder directory, budget allocations, and post-launch roadmap.   KB_Technical_Readiness.txt — Sprint progress, system architecture, API benchmarks, QA defect tallies, staged rollout plan, automatic rollback thresholds, and launch week on-call shifts.   KB_GTM_Marketing.txt — Channel-by-channel budgets ($65K total), PR embargo windows, messaging guardrails, competitive matrices (ShopFast, QuickCart, BuyNow), and sales objection playbooks.   Verification & Testing SuiteTest ScenarioInput UtteranceExpected System Response1. Technical Routing"Hey, are we going to be ready for launch from an engineering perspective?"Routes to Technical Specialist; reports Sprint 9/11 status, 0 critical defects, passed load testing, and staged rollout dates.   2. Marketing Routing"What’s our marketing plan for launch week?"Routes to GTM Specialist; outlines $45K paid social split, press embargo ending April 10, and April 22 referral push.   3. Executive Summary"Just give me the 30-second version — are we on track?"Routes to Executive Briefing; delivers a 4–5 sentence synthesis on April 15 readiness, App Store timing risk, and $210K spend against $220K budget.   4. Boundary EnforcementUser asks Technical Specialist: "What’s our ad spend?"Subagent declines marketing budget disclosure and redirects caller to the GTM track.   5. Dynamic Cross-RoutingIn Technical track, user says: "Actually, can you tell me about the competitive landscape instead?"Seamlessly transitions caller to GTM Specialist to provide competitive comparisons against ShopFast, QuickCart, and BuyNow.   
