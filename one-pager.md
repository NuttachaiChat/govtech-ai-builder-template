# One Pager

Use case: Scenario B · ICA Passport Applications (all data synthetic)

## Problem and user

**Problem:** Applicants do not know what a complete application looks like for their situation, leading to 17% of submissions having errors or missing documents.

**User:** Applicants submitting passport applications and renewals to ICA, especially parents renewing passports for children under 16.

**User quote (synthetic):** "The time to renew my passport is longer than my trip! It is unclear what do I need to submit."

**Status quo:** 
- 17% error rate for applications with errors or missing documents
- Each return adds approximately 6 days to processing time
- Only 38% of applicants read the guidance before they apply
- Applicants submit, get rejected, resubmit (frustrating for citizens, inefficient for ICA)

## Opportunity and evidence

**Opportunity:** Check submissions for errors and missing documents BEFORE applicants submit to ICA officers.

**Goal:** Reduce submission errors or missing documents by half from 17% to ~8.5%

**Hypothesis:** Applicants will edit their submission when they receive prompts about errors, and make correct edits.

**Evidence (synthetic):**
- Current error rate: 17%
- Only 38% read guidance before applying
- Each return adds ~6 days delay

**Success metrics:**
- 9 in 10 applications (90%) are submitted with complete info and documents
- Target: Reduce error rate from 17% to ≤10% within first month of pilot
- No increase in completion time (stay at or below 22-minute baseline)
- No increase in support call volume
## Experience and trust
## Risks (market, technical, team)

### Market Risk

**Risk:** Applicants don't understand AI-generated error prompts and require additional support, defeating the efficiency goal (increased call center volume instead of reduced workload).

**How we will test it:**
- Usability testing with 10+ real applicants across diverse segments (elderly parents, low digital literacy, foreign document holders)
- Success criteria: 70%+ can fix all flagged errors without calling support or asking for help
- Measure completion time (must not exceed current 22-minute baseline)
- Track support call reasons during pilot phase

### Technical Risk

**Risk:** AI incorrectly flags valid documents that meet ICA guidelines as errors (false positives), creating applicant frustration and infinite edit loops. Example: A passport photo with a proper white background gets flagged due to lighting/shadow detection issues.

**How we will test it:**
- Test AI model with 100+ known-valid documents (pre-verified by ICA officers as meeting all requirements)
- Measure precision rate: target >95% to keep false positives under 5% (750 wrong calls per 15,000 weekly applications is the maximum acceptable)
- Implement confidence thresholds: High (95%+) = block submission, Medium (80-95%) = soft warning with override option, Low (<80%) = silent flag for officer review
- Monitor override rates during pilot: if applicants override >30% of AI flags, the model is not accurate enough

### Team Risk

**Risk:** ICA officers don't trust AI decisions and re-check every application anyway, negating efficiency gains and creating duplicate work.

**How we will test it:**
- Pilot with 5% of applications, measure officer override rate
- If officers override AI decisions >20% of time, indicates lack of trust or insufficient AI accuracy
- Survey officers: do they feel confident accepting AI-passed applications without full manual review?
- Track time-per-application: if it doesn't decrease during pilot, officers aren't trusting the system

## Build plan and handoff
## Agent role and guardrails

### What the Agent Does

**Validates ICA passport applications BEFORE submission to officers.**

The agent checks for:
- Missing mandatory documents (birth certificate, passport photo, parent NRIC, existing passport)
- Photo compliance (white background, size, face centered, taken within 3 months)
- Name matching between application and documents
- Document format and language requirements (English/Chinese/Malay/Tamil)

**How it works:**
1. Applicant uploads documents → Agent validates in real-time
2. Agent generates plain language error prompts with "What to do" steps
3. Applicant fixes errors OR overrides warnings (three-option pattern)
4. Agent flags edge cases → Escalates to officer queue when needed
5. Application submitted → Officer reviews only flagged items

### Decision Authority

**Agent decides alone (no human in loop):**
- Missing mandatory documents → Block submission until uploaded
- Documents present → Mark as verified
- Real data detected → Refuse processing (safety check)
- **Volume:** ~15% of applications (2,250/week)

**Agent proposes, user chooses (3-option pattern):**
- 75-89% confidence warnings → Soft suggestion (Replace / Keep / Request Review)
- 90%+ confidence warnings → Strong warning requiring 3-checkbox acknowledgment
- User always has control, agent never blocks on AI warnings alone
- **Volume:** ~40% of applications show warnings (6,000/week)

**Agent escalates, officer decides:**
- Foreign language documents → Language verification queue
- Suspected fraud (>70% confidence) → Fraud investigation queue
- Legal name changes → Name verification queue
- Edge cases (diplomatic, refugee, stateless) → Special cases queue
- Confidence <75% → Silently flagged for officer (not shown to applicant)
- User requests review → Honor request, route to queue
- **Volume:** ~15% escalated to officers (2,250/week vs 15,000/week baseline = 85% workload reduction)

### What Stays Human

**Officer decisions (agent never makes final call):**
- Final approval/rejection of application
- Interpretation of ambiguous documents (faded text, handwritten, translated)
- Policy exceptions (compassionate grounds, urgent travel, special accommodations)
- Fraud determination (agent flags, officer investigates)
- All dismissed AI warnings (user overrode at 92% confidence? officer reviews)

**User decisions (agent never overrides):**
- Whether to replace flagged photo or keep it
- When to request human review (button always available)
- Whether to continue or abandon application

**Strategic decisions (product team, requires Director sign-off):**
- Confidence threshold changes (currently 90%+ strong, 75-89% soft, <75% silent)
- Error message wording (templates are "Sacred" - user-tested and locked)
- ICA corporate identity (Blue #1C3F6E, Red #C4122E - government branding)
- Three-option pattern (Replace/Keep/Review - core UX validated by stakeholders)

### Guardrails

**Agent must stop and escalate if:**
- Foreign language document (not English/Chinese/Malay/Tamil)
- Document appears altered (>70% confidence fraud)
- Name completely different between documents (may be legal change)
- Edge case application type (diplomatic, refugee, stateless)
- Confidence at threshold boundary (exactly 75.0%)
- User explicitly requests human review

**Kill switches (emergency stop):**
- Level 1: Pause new validations, route to manual flow
- Level 2: Block all agent-validated submissions
- Level 3: Rollback completely, disable agent

**Red flags that trigger manual review:**
- Dismissal rate >30% → Model inaccurate, needs retraining
- Officer override rate >20% → Officers don't trust agent
- Support calls increase → Prompts confusing
- Completion time >22 min → Too much friction
- Escalation rate >20% → Agent over-escalating

**Details:** See day3-build/agent-control-panel.md for full decision authority matrix

## Roles and operating model

### Roles

**Decision-Makers:**
- **Director of Passport Services** - Business owner, accountable for agent outcomes, approves scope/go-live/retire
- **Product Lead** - Day-to-day agent operations, approves permissions/error messages/tactical changes
- **Engineering Lead** - Technical infrastructure, deployment, API changes
- **ML Lead** - AI model performance, approves vendor/model selection, confidence thresholds, retraining
- **Operations Manager** - Officer workload distribution, queue management, incident response (Level 1-2 killswitch)
- **CISO** - Security, PII protection, fraud escalation approval

**Contributors:** UX Team (error templates, user testing), ICA Officers (validate flagged applications, feedback), Legal Team (policy changes, name verification rules), Customer Support (track citizen complaints, confusion patterns)

**Informed:** Applicants/Citizens (experience agent prompts), Minister's Office (high-level metrics), Audit Team (compliance, PDPA)

### Decision Rights

| Decision | Who Decides | Who is Consulted |
|----------|-------------|------------------|
| **Scope** (Add/remove validation checks, new document types) | Director of Passport Services | Product Lead, Operations Manager, Legal Team |
| **Permissions** (Change auto/confirm/block, adjust 95%/80% thresholds, 20% kill switch) | Product Lead | Director, Operations Manager, ML Lead |
| **Vendor/Model** (Switch AI provider, retrain with new data) | ML Lead | Engineering Lead, CISO, Product Lead |
| **Go-Live** (Launch to production, enable for all citizens) | Director of Passport Services | Product Lead, Ops Manager, Engineering, ML, CISO, Minister |
| **Incident** (Killswitch activation, rollback, investigate wrong answer) | Operations Manager (L1-2), Director (L3) | Product Lead, ML Lead, Engineering Lead |
| **Retire** (Decommission agent, return to manual validation) | Director of Passport Services | Product Lead, Operations Manager, Engineering Lead |

### Incident Response (If Agent Gives Wrong Answer)

**Who acts first:** Operations Manager (duty officer on 24/7 rotation)
- Reviews case within 5 minutes
- Isolated (1-4 cases) → Flag for Product Lead, continue monitoring
- Widespread (≥5 cases in 1 hour) → Activate Level 1 killswitch

**Who switches off (3 escalating levels):**
1. **Level 1 (Operations Manager):** Feature flag, AI validation disabled, <5 min response
2. **Level 2 (Product Lead):** Maintenance mode, agent offline, manual processing
3. **Level 3 (Director):** Emergency shutdown, Minister notified, investigation launched

**Who tells business owner:**
- Isolated: Ops Manager → Product Lead (30 min) → Director (end of day)
- Widespread: Ops Manager → Director (immediate, 15 min) → Minister (if >50 cases)
- Public/Legal: Director → Minister (<15 min), CISO → Legal → PDPA (72 hrs if breach)

**Post-incident:** Root cause analysis within 3 days, lessons learned, prevent recurrence, quarterly review of all incidents

**Details:** See day3-build/operating-model.md for full incident response playbook
## Strategy and roadmap

### Strategy Kernel

**Diagnosis:** 17% of submissions have errors or missing documents. Each return adds about six days. Only 38% of applicants read the guidance before they apply.

**Guiding policy:** Applicants more aware of what a complete application looked like for their situation, especially parents renewing for a child.

**Coherent actions:**
1. Applicant aware of what they need to submit
2. Applicant understand what went wrong with their application when error prompted
3. Applicant able to make correct edits
## Metrics plan and stop line
## Evals and gates
## Open issues

**From evaluation (19 Nov 2026):**

1. **User control validation incomplete** - Only 4 cases tested D2 (user control on AI warnings). Need to verify 3-option pattern (Replace/Keep/Review) and 3-checkbox strong warning flow work in real application UI, not just agent logic.

2. **Production correlation unknown** - Evaluation shows 15/15 pass with synthetic data, but no evidence yet that eval scores predict production outcomes. Need to track: Do D5=4.9 clarity scores actually correlate with <10% support call increase?

3. **Test case coverage gaps** - Evaluation found agent handles 15 scenarios correctly, but production Week 1-2 may surface new patterns not in test cases (e.g., watermarked documents, low-quality scans). Plan to add Case 16+ as patterns emerge.

4. **Calibration not yet proven** - Only 1 reviewer (Claude) scored all 15 cases. Inter-rater reliability unknown until 3 human reviewers complete calibration on 5 anchor cases. Target: 100% agreement on D1-D4, need to verify achievable.

5. **Weekly sample sustainability** - Operations Manager assigned to run 5 anchor cases weekly (1 hour). Risk: If weekly sample becomes "check the box" without real scrutiny, regressions slip through. Need accountability mechanism.

## Decision: continue, pivot or stop

**Decision:** CONTINUE to pilot (5% of applications, ~750 cases/week)

**Why:** Agent passed 15/15 evaluation cases with 0 deal-breaker failures, demonstrating correct behavior on mandatory errors (D1), user control (D2), cultural sensitivity (D3), escalation (D4), and message clarity (D5=4.9/5). Risk is bounded: 5% pilot limits blast radius, weekly anchor evaluations catch regressions, and 4 killswitch triggers (dismissal >30%, override >20%, support calls +10%, PII exposure) provide stop lines if production behavior diverges from evaluation predictions.
