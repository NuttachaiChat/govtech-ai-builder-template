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
