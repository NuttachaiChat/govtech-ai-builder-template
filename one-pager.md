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
## Decision: continue, pivot or stop
## Open issues
