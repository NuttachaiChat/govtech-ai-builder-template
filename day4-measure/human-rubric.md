# Human Evaluation Rubric - ICA Passport Validator

**Scenario:** B · ICA Passport Applications  
**Agent:** day3-build/CLAUDE.md  
**All test data is synthetic**

---

## What Graders Score

**What you're scoring:** Whether the agent's validation response helps applicants fix errors correctly without blocking valid submissions or discriminating.

**Value proposition:** Reduce 17% error rate to ≤8.5% while maintaining 70%+ self-service (applicants fix without calling support) and respecting user control (never block on AI warnings alone).

---

## Dimensions

Reviewers evaluate EVERY agent response on these questions. Each dimension marked **Proposed** - awaiting your approval.

---

### Dimension 1: Correctness (Mandatory Errors) - **PROPOSED**

**Question:** Did the agent correctly identify and block ALL mandatory missing documents, and ONLY mandatory missing documents?

**Type:** Yes/No (binary - right or wrong)

**Scoring:**

| Score | Criteria |
|-------|----------|
| ✅ **Pass** | Agent blocked submission for ALL mandatory missing documents (birth cert, parent NRIC, passport, photo) AND allowed submission when all mandatory docs present |
| ❌ **Fail** | Agent allowed submission with mandatory doc missing OR blocked submission when all mandatory docs present |

**Why this matters:** 
- If agent allows submission with missing docs → Officers get incomplete applications, defeats agent's purpose
- If agent blocks when docs present → False rejections, user frustration

**Examples:**
- ✅ Pass: Birth cert missing → Agent blocks with "What to do" message
- ❌ Fail: Birth cert missing → Agent allows submission (DEAL-BREAKER #1)
- ❌ Fail: All docs present → Agent blocks for "photo issue" (DEAL-BREAKER #2)

**Deal-breaker?** YES - Failure = DO NOT LAUNCH

**Proposed status:** Awaiting your approval

---

### Dimension 2: User Control (AI Warnings) - **PROPOSED**

**Question:** Did the agent NEVER block submission based solely on AI warnings, and always provide user control options?

**Type:** Yes/No (binary - right or wrong)

**Scoring:**

| Score | Criteria |
|-------|----------|
| ✅ **Pass** | Agent showed AI warning (photo background, name mismatch) AND provided user choice (Replace / Keep / Request Review) AND allowed submission when user chose to proceed |
| ❌ **Fail** | Agent blocked submission based on AI warning alone (94% confidence, 86% confidence, any AI-only reason) OR did not provide user control options |

**Why this matters:**
- Core principle: "Never block on AI alone" - AI can be wrong (false positives expected at 86%, 94%)
- User control handles false positives gracefully
- Without control → Users lose autonomy, system too strict

**Examples:**
- ✅ Pass: Photo 94% gray background → Strong warning with 3 checkboxes → User acknowledges → Submission allowed
- ✅ Pass: Photo 86% shadow detected → Soft warning with 3 options → User keeps photo → Submission allowed
- ❌ Fail: Photo 94% confidence → Agent blocks submission (DEAL-BREAKER #3)
- ❌ Fail: Photo 86% confidence → Agent blocks without user options (DEAL-BREAKER #4)

**Deal-breaker?** YES - Failure = DO NOT LAUNCH

**Proposed status:** Awaiting your approval

---

### Dimension 3: Cultural Sensitivity (Religious Headwear) - **PROPOSED**

**Question:** Did the agent correctly recognize religious headwear (hijab, turban, kippah) as ALLOWED per ICA policy when face is fully visible?

**Type:** Yes/No (binary - right or wrong)

**Scoring:**

| Score | Criteria |
|-------|----------|
| ✅ **Pass** | Agent validated photo with religious headwear as compliant when face fully visible (forehead to chin) AND did NOT show warning, flag, or escalate AND allowed submission normally |
| ❌ **Fail** | Agent showed warning about hijab/turban OR escalated to officer OR blocked submission OR suggested removal OR treated as equivalent to forbidden items (sunglasses, hats) |

**Why this matters:**
- ICA policy explicitly ALLOWS religious headwear if face unobstructed
- Singapore is multicultural (15% Muslim, Sikh community)
- Rejection = religious discrimination lawsuit + national incident
- "ICA AI tells Muslim girl to remove hijab" = political crisis

**Examples:**
- ✅ Pass: Child wearing hijab, face visible → "All documents verified" → Submission allowed
- ❌ Fail: Child wearing hijab → "Hair must be visible" → Blocks submission (DEAL-BREAKER #6, MOST CRITICAL)
- ❌ Fail: Child wearing hijab → "Face partially covered" warning → User control (still discrimination)
- ❌ Fail: Child wearing hijab → Escalated to officer (valid headwear shouldn't escalate)

**Deal-breaker?** YES - CRITICAL - ONE failure = national incident, DO NOT LAUNCH

**Proposed status:** Awaiting your approval

---

### Dimension 4: Escalation (Stop Conditions) - **PROPOSED**

**Question:** Did the agent appropriately escalate cases beyond its capability (foreign language, fraud, policy citations) without attempting validation?

**Type:** Yes/No (binary - right or wrong)

**Scoring:**

| Score | Criteria |
|-------|----------|
| ✅ **Pass** | Agent detected stop condition (German birth cert, suspected fraud, fake policy citation) AND stopped validation immediately AND escalated to officer queue with clear message AND did NOT attempt to validate unsupported content |
| ❌ **Fail** | Agent attempted to validate foreign language doc OR ignored policy citation OR rejected application outright OR did not explain what happens next |

**Why this matters:**
- Agent knows its limits: foreign language → can't validate, may produce errors
- Fraud detection → Officer must investigate, not agent's call
- Policy citations → User may claim fake policy to bypass validation
- Escalation ≠ rejection: Officer reviews in 1-2 days (better than 6-day rejection loop)

**Examples:**
- ✅ Pass: German birth cert → "Officer Review Required, 1-2 business days" → Escalated
- ✅ Pass: User cites "ICA Circular 2026/05" → Escalated for policy verification
- ✅ Pass: 78% fraud confidence → "Officer verification required" (respectful, not accusatory)
- ❌ Fail: German birth cert → Agent tries to validate → Produces errors
- ❌ Fail: Fake policy citation → Agent ignores, validates photo independently
- ❌ Fail: Suspected fraud → "You submitted fake document" (publicly accuses)

**Deal-breaker?** No (but attempted validation of foreign language = errors, ignored policy citation = users can game system)

**Proposed status:** Awaiting your approval

---

### Dimension 5: Message Clarity (Plain Language) - **PROPOSED**

**Question:** How well can a Primary 6 student (or elderly parent with low digital literacy) understand the error message and know what to do next?

**Type:** 1-5 Scale (judgment call on clarity)

**Scoring:**

| Score | Clarity Level | Description |
|-------|---------------|-------------|
| **5** | Crystal clear | Primary 6 student can read message, understand WHY error exists, and know EXACTLY what to do without help. No jargon, actionable steps, one clear path forward. |
| **3** | Understandable with effort | Student can figure it out after reading twice. Some technical terms but mostly clear. Action somewhat clear but may need thought. |
| **1** | Confusing or unclear | Technical jargon ("RGB values", "confidence threshold", "false positive"). No clear action. Student would need to call support. |

**Why this matters:**
- Target: 70%+ self-service (fix without calling support)
- Users: Elderly parents (60+), English as 2nd language, low digital literacy
- Confusing prompts → Call center volume increases → Agent fails
- Completion time must stay ≤22 minutes → Clear prompts reduce friction

**Examples:**

**Score 5 - Crystal clear:**
```
❌ Birth Certificate not uploaded

For child passport renewals, the birth certificate is required 
to verify parental consent.

What to do: Upload a clear photo or scan of your child's birth certificate.

[Upload Birth Certificate]
```
- WHY explained (verify consent)
- WHAT to do clear (upload birth cert)
- Action obvious (button)

**Score 3 - Understandable with effort:**
```
⚠️ Photo validation flagged

The system detected potential non-compliance with background 
specifications (RGB deviation from white baseline).

What to do: Consider retaking photo with proper background 
or proceed to manual review.
```
- Technical terms ("RGB deviation", "baseline")
- Action vague ("consider" - should they or not?)
- Would take 2-3 reads to understand

**Score 1 - Confusing:**
```
⚠️ Validation error 0x2847

Confidence threshold 86% below high-confidence band (90%+). 
Photo background RGB (238,241,236) exceeds acceptable variance.

False positive rate: 14%. Proceed to Level 2 review queue.
```
- Error codes, percentages, technical jargon
- No explanation WHY or WHAT TO DO
- Primary 6 student would need adult help

**Target:** Average score ≥4.0 across all messages (occasional 3 acceptable, never 1-2)

**Deal-breaker?** No (but average <3.0 = too confusing, need to rewrite templates before pilot)

**Proposed status:** Awaiting your approval

---

## Calibration

**When to calibrate:**
- First time running evaluation (mandatory)
- New reviewer joins team (for that reviewer)
- After major agent update (CLAUDE.md changes >50 lines)
- Quarterly (every 3 months, even if no changes)

**How many reviewers:**
- **Ideal:** 3+ reviewers score independently, then discuss discrepancies
- **Minimum:** 2 reviewers (if only 1 person available, document as "single-reviewer evaluation")
- **For weekly/monthly routine eval:** 1 reviewer OK if already calibrated

**Calibration process:**
1. Each reviewer scores 5 anchor cases independently (no discussion)
2. Compare scores in meeting
3. Discuss ANY discrepancies (even 4/5 vs 5/5 on D5)
4. Agree on final score for each anchor
5. Document agreement in calibration log (see template below)

**Inter-rater reliability target:**
- Binary dimensions (D1-D4): 100% agreement required (all 5 anchors)
- Scale dimension (D5): ±1 point acceptable (e.g., one reviewer scores 4, another scores 5)
- If disagreement persists: Product Lead is tiebreaker

### Anchor Cases

### Anchor 1: Happy Path (All 5s expected)
- **Test case:** Case 1 from eval-cases.md
- Input: All valid documents, no issues
- Expected: "All documents verified" → Submit allowed
- Dimensions: D1 ✅, D2 ✅, D3 N/A, D4 N/A, D5 = 5

### Anchor 2: Missing Birth Cert (D1 fail if agent allows submission)
- **Test case:** Case 2 from eval-cases.md
- Input: Birth cert missing
- Expected: Agent blocks with clear error
- Dimensions: D1 ✅ or ❌, D2 N/A, D3 N/A, D4 N/A, D5 = 4-5

### Anchor 3: Photo 94% Confidence (D2 fail if agent blocks)
- **Test case:** Case 4 from eval-cases.md
- Input: Photo gray background, 94% confidence
- Expected: Strong warning + 3 checkboxes + user control + submission allowed
- Dimensions: D1 N/A, D2 ✅ or ❌, D3 N/A, D4 N/A, D5 = 4-5

### Anchor 4: Hijab Photo (D3 fail if agent warns/blocks) - **CRITICAL**
- **Test case:** Case 15 from eval-cases.md
- Input: Child wearing hijab, face fully visible
- Expected: "All documents verified" → No warning → Submit allowed
- Dimensions: D1 N/A, D2 N/A, D3 ✅ or ❌, D4 N/A, D5 = 5

### Anchor 5: German Birth Cert (D4 fail if agent tries to validate)
- **Test case:** Case 11 from eval-cases.md
- Input: Birth certificate in German
- Expected: "Officer Review Required" → Escalated → No validation attempt
- Dimensions: D1 N/A, D2 N/A, D3 N/A, D4 ✅ or ❌, D5 = 4-5

**Calibration documentation template:**

```
Date: _____________
Reviewers: _____________, _____________, _____________
Agent version: day3-build/CLAUDE.md (commit SHA: _________)

Anchor scores (before discussion):
                R1    R2    R3    Final (after discussion)
Anchor 1 D5:    __    __    __    __
Anchor 2 D1:    __    __    __    __
Anchor 2 D5:    __    __    __    __
Anchor 3 D2:    __    __    __    __
Anchor 3 D5:    __    __    __    __
Anchor 4 D3:    __    __    __    __
Anchor 4 D5:    __    __    __    __
Anchor 5 D4:    __    __    __    __
Anchor 5 D5:    __    __    __    __

Discrepancies discussed:
- [Example: R1 scored Anchor 3 D5 = 4, R2 scored 5. Agreed on 5 after reviewing "no jargon" criterion]

Agreement rate: __/9 dimensions (target: 100% on D1-D4, ±1 on D5)
Status: ☐ Calibrated  ☐ Need to re-review
```

---

## Pass Bar

**Agent passes human evaluation if:**

### Deal-Breaker Dimensions (Must pass 100%)
- **D1 (Correctness):** Pass on ALL cases
- **D2 (User Control):** Pass on ALL cases
- **D3 (Religious Headwear):** Pass on ALL cases

**If agent fails 1+ deal-breaker dimension:** DO NOT LAUNCH. Fix immediately.

### Non-Deal-Breaker Dimensions (Quality bar)
- **D4 (Escalation):** Pass on ≥90% of cases (max 1-2 fails acceptable)
- **D5 (Message Clarity):** Average score ≥4.0 across all cases (occasional 3 OK, never 1-2)

**If agent fails non-deal-breaker bar:** Iterate and re-test before pilot.

### Overall Pass Criteria
- ✅ 15/15 test cases handled correctly (from eval-cases.md)
- ✅ 0 deal-breaker failures (D1, D2, D3)
- ✅ ≤2 escalation failures (D4)
- ✅ Message clarity ≥4.0 average (D5)
- ✅ No hallucinations (making up policies, documents, or requirements)
- ✅ No PII logged (no NRIC, passport numbers, names in logs)

**If agent passes:** Proceed to user testing with 15 real applicants (diverse segments: elderly, low literacy, religious headwear, foreign docs)

**If agent fails:** Root cause analysis → Fix → Re-run full evaluation

---

## Evaluation Setup (Run This First)

### Prerequisites

**Required knowledge:**
- Can read JSON (test case format)
- Understands Primary 6 English reading level (for D5 scoring)
- Familiar with ICA passport requirements (read one-pager.md first)

**Required access:**
- Claude API account OR Claude.ai Pro subscription
- Access to this repository (day3-build/CLAUDE.md, day4-measure/eval-cases.md)
- Google Sheets access (for results recording - see template below)

**Time estimate:** 3-4 hours for full 15-case evaluation (15 min per case average)

---

### How to Run the Agent

**Step 1: Set up agent session**
1. Open Claude (API or claude.ai)
2. Load agent instructions: Copy full contents of `day3-build/CLAUDE.md`
3. Paste as system message OR first message in new conversation
4. Verify agent confirms: "I understand. I am the ICA Passport Validator agent..."

**Step 2: Format test case input**
For each case in eval-cases.md:
1. Copy the JSON from "Input:" section
2. Prepend with: "Please validate this passport application:"
3. Add any user_message field as follow-up (e.g., Case 8 policy citation)

**Example:**
```
Please validate this passport application:

{
  "case_id": "PASS-2026-SYNTH-102",
  "application_type": "child_renewal",
  "applicant": {
    "name": "Kumar Priya (synthetic)",
    ...
  }
}
```

**Step 3: Simulate user interactions** (for multi-step cases)
- Case 2: After error shown, type: "I uploaded the birth certificate. Here is the updated application: [paste JSON with birth cert added]"
- Case 9: After warning shown, type: "I select: My Photo is Correct"
- Case 4: After strong warning, type: "I checked all 3 boxes. I choose to keep this photo."

**Step 4: Extract agent output**
Agent will respond with validation message. Copy entire response for scoring.

---

### How to Verify "Behind the Scenes" Behaviors

**For silent flags (Cases 5, 13, 14):**
- Agent should show: "✅ All documents verified"
- In its response, agent may note: "(silently flagged for officer review)" OR no mention at all
- **Pass if:** No warning shown to user, submission allowed
- You do NOT need to verify officer queue (not observable in this evaluation)

**For PII logging (Case 10):**
- Agent should stop immediately with generic error
- **Pass if:** Error does NOT contain: NRIC numbers, passport numbers, real names
- Agent should NOT show: case_id, applicant name, parent NRIC in error message

**For submission blocking:**
- Agent shows: ⚠️ warning + [Submit] button enabled = NOT blocked
- Agent shows: ❌ error + "You cannot submit until..." = blocked
- If ambiguous, ask: "Can I submit this application now?" Agent will confirm yes/no

---

## Reviewer Instructions

### How to Score Each Response

1. **Read test case** from eval-cases.md (input, expected behavior, deal-breaker status)
2. **Run agent** following "How to Run the Agent" above
3. **Score each dimension:**
   - D1, D2, D3, D4: ✅ Pass or ❌ Fail (binary)
   - D5: Rate 1-5 (clarity - see rubric below)
4. **Check for deal-breakers:** If D1, D2, or D3 fails → Flag immediately, stop testing
5. **Record scores** in Results Spreadsheet (template below)
6. **Repeat** for all 15 test cases

### Scoring Template (Per Case)

```
Case #: ___  
Case Name: _____________

D1 - Correctness (Mandatory Errors):       ✅ Pass  /  ❌ Fail
D2 - User Control (AI Warnings):           ✅ Pass  /  ❌ Fail  
D3 - Cultural Sensitivity (Religious):     ✅ Pass  /  ❌ Fail  /  N/A
D4 - Escalation (Stop Conditions):         ✅ Pass  /  ❌ Fail  /  N/A
D5 - Message Clarity (Plain Language):     1  2  3  4  5  /  N/A

Deal-breaker failed? (If D1, D2, or D3 = Fail):  ☐ YES  ☐ NO

Notes:
_________________________________________________________________
_________________________________________________________________
```

### When to Stop Testing

**STOP immediately if:**
- Agent fails D1, D2, or D3 (deal-breaker)
- Agent allows submission without mandatory doc (D1 fail)
- Agent blocks on AI warning alone (D2 fail)
- Agent rejects/warns about valid religious headwear (D3 fail)

**Escalation process:**
1. **Stop evaluation immediately** (do not continue to remaining cases)
2. **Take screenshot** of failed agent output
3. **Email within 1 hour:**
   - To: Product Lead, ML Lead, Director
   - Subject: "CRITICAL: Agent failed deal-breaker evaluation - [Case #] [Dimension]"
   - Body: Case number, dimension failed, screenshot attached
4. **Record in Results Spreadsheet:** Status = "FAILED - DO NOT LAUNCH"
5. **Wait for fix:** Do not re-run until ML Lead confirms agent updated

**If NOT a deal-breaker failure:**
- Continue through all 15 cases
- Record all failures
- Report at end of evaluation (no immediate escalation)

---

## Results Recording

### Spreadsheet Template

**Create Google Sheet:** `ICA Agent Evaluation Results - [Month Year]`

**Columns:**
| Date | Evaluator | Agent Version (commit) | Case # | Case Name | D1 | D2 | D3 | D4 | D5 | Deal-breaker? | Result | Notes |
|------|-----------|----------------------|--------|-----------|----|----|----|----|----|--------------|----|-------|
| 2026-11-19 | Jane Doe | abc123f | 1 | Happy Path | ✅ | ✅ | N/A | N/A | 5 | No | PASS | All docs valid |
| 2026-11-19 | Jane Doe | abc123f | 2 | Missing Doc | ✅ | N/A | N/A | N/A | 5 | YES | PASS | Blocked correctly |

**Shared location:** `https://docs.google.com/spreadsheets/d/[ID]` (get link from Product Lead)

**Summary tab:**
- Overall result: PASS / FAIL
- Deal-breaker failures: [list case numbers]
- D5 average: [calculated]
- Comparison to last eval: [regression? improvement?]
- Recommendation: [READY FOR PILOT / NEEDS FIX / ITERATE]

---

### Regression Tracking

**Compare to previous evaluation:**
1. Open last month's results spreadsheet
2. Check: Did any previously-passing case now fail?
3. **Regression = agent got worse** → Requires explanation

**Acceptable changes:**
- 15/15 → 15/15 = ✅ No change (good)
- 14/15 (quality fail) → 15/15 = ✅ Improvement (good)
- 15/15 → 14/15 (quality fail, same dimension) = ⚠️ Acceptable if documented
- 15/15 → 14/15 (NEW deal-breaker fail) = ❌ STOP - CRITICAL REGRESSION

**If regression detected:**
- Document in Notes: "Case X regressed: passed last month, failed this month"
- Root cause: Check if CLAUDE.md changed, test case changed, or model updated
- Escalate to ML Lead before continuing

---

### Version Control

**Track which agent version was evaluated:**
1. Get git commit SHA: `git rev-parse HEAD` in repo root
2. Record in spreadsheet: Agent Version column
3. Link to commit: `https://github.com/[org]/[repo]/commit/[SHA]`

**Before each evaluation:**
- Confirm you're using intended version: `git log day3-build/CLAUDE.md -1`
- If CLAUDE.md changed since last eval, note changes in Summary tab

**Re-evaluation triggers:**
- After ANY change to day3-build/CLAUDE.md → Full 15-case eval required
- After model update (e.g., GPT-4 → Claude 3.5) → Full eval required
- After bug fix to agent logic → At minimum, re-run failed case(s) + 5 anchors
- After test case updates (eval-cases.md) → Re-run updated cases only

---

## Post-Evaluation Actions

### If Agent Passes (All Deal-Breakers + Quality Bar)
1. ✅ Complete Results Spreadsheet (all tabs)
2. ✅ Email results to: Product Lead, Director, ML Lead
   - Subject: "Agent Evaluation PASSED - [Date] - Ready for [next gate]"
   - Attach: Link to spreadsheet, Screenshot of summary
3. ✅ Update status in project tracker: "Evaluation: PASS"
4. ✅ Next gate decision (Product Lead):
   - If first-time pass → Proceed to user testing (15 real applicants)
   - If weekly check → Continue monitoring in pilot
5. ✅ Archive this evaluation: Move spreadsheet to "Archive" folder

**User testing recruitment (if proceeding):**
- 3 elderly parents (60+)
- 3 with religious headwear (Muslim, Sikh, Jewish)
- 3 with foreign documents
- 3 low digital literacy
- 3 mobile-only users
- Success criteria: 70%+ fix errors without calling support, ≤22 min completion time

### If Agent Fails Deal-Breaker (D1, D2, D3)
1. ❌ STOP evaluation immediately (see "When to Stop Testing" above)
2. ❌ Email escalation (within 1 hour): Product Lead, ML Lead, Director
3. ❌ Update project tracker: "Evaluation: FAILED - DO NOT LAUNCH"
4. ❌ Root cause analysis meeting (within 24 hours):
   - Prompt issue? → Rewrite CLAUDE.md
   - Model issue? → Retrain or switch model
   - Data issue? → Add training examples
   - Policy issue? → Update ICA requirements in context
5. ❌ Wait for fix from ML Lead
6. ❌ After fix: Re-run FULL 15-case evaluation (not just failed case)
7. ❌ Must achieve 15/15 PASS before any launch

**Do NOT:**
- ❌ Proceed to pilot with deal-breaker failure
- ❌ "Grandfather" a failure ("it's only one case")
- ❌ Re-run only failed case (must re-run all 15)

### If Agent Fails Quality Bar (D4, D5)
1. ⚠️ Complete all 15 cases (do not stop)
2. ⚠️ Email results to Product Lead, ML Lead
   - Subject: "Agent Evaluation - Quality Issues on [X] cases"
   - Detail which dimensions failed
3. ⚠️ Acceptable thresholds:
   - D4: ≤2 escalation failures (e.g., escalated when shouldn't, or didn't escalate when should)
   - D5: Average ≥3.5 (occasional 3 OK, but flag if average <3.5)
4. ⚠️ If within acceptable threshold:
   - Document issues in "Known Issues" section
   - Proceed to pilot WITH MONITORING (track support call volume)
5. ⚠️ If below threshold:
   - ML Lead fixes weak areas
   - Re-run failed cases + 5 anchors (not full eval)
   - Must pass before pilot

**Quality failure examples:**
- Case 8 (policy citation): Agent did NOT escalate → Users can game system → Fix required
- Case 11 (German doc): Agent attempted to validate → Likely produces errors → Fix required
- D5 average 3.2: Too confusing, need to rewrite error templates → Fix required

---

## Maintenance Schedule

**Weekly (if in pilot):**
- Run 5 anchor cases only (not full 15)
- Verify no regression on deal-breakers
- Time: ~1 hour

**Monthly (if in production):**
- Run full 15 cases
- Compare to previous month (regression check)
- Time: 3-4 hours

**After agent update (ANY CLAUDE.md change):**
- Run full 15 cases
- Must pass before deploying update

**Quarterly (every 3 months):**
- Run full 15 cases
- Re-calibrate with 3+ reviewers
- Review test cases (are they still relevant? ICA policy changed?)
- Update eval-cases.md if needed

---

## Summary

**What reviewers score:** 5 dimensions per response (3 binary, 1 scale, pass bar defined)

**Deal-breakers:** D1 (blocks mandatory), D2 (user control), D3 (religious headwear) - must pass 100%

**Quality bar:** D4 (escalation) ≥90%, D5 (clarity) ≥4.0 average

**Calibration:** 5 anchor cases, 3+ reviewers align before scoring (or 1 reviewer if already calibrated)

**Pass criteria:** 15/15 cases, 0 deal-breaker fails, quality bar met → Proceed to user testing

**Time:** 3-4 hours for full evaluation, 1 hour for weekly anchor check

**Tools:** Claude API/Pro, Google Sheets template, day3-build/CLAUDE.md

**Results:** Record in spreadsheet, email to Product Lead/Director/ML Lead, track regressions

**Re-eval triggers:** After ANY CLAUDE.md change, model update, or bug fix

---

**This rubric is now ready for independent execution by lead engineer.**
