# One Pager

Use case: [your chosen scenario (A, B, or C), written with synthetic data only]

## Problem and user
## Opportunity and evidence
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
