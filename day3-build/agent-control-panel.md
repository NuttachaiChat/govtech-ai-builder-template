# Agent Control Panel - ICA Passport Application Validator

**Scenario:** B · ICA Passport Applications  
**Agent:** Validates passport applications before submission to officers  
**All data synthetic**

---

## What the Agent Is Doing

### Core Function
The agent validates ICA passport applications **before** submission to officers, checking for:
- Missing mandatory documents (birth certificate, passport photo, NRIC, existing passport)
- Photo compliance (white background, size, face centered, taken within 3 months)
- Name matching between application and supporting documents
- Document format and language requirements

### How It Works
1. **Applicant uploads documents** → Agent validates in real-time
2. **Agent generates error prompts** → Plain language with "What to do" steps
3. **Applicant fixes errors OR overrides warnings** → Three-option pattern (Replace/Keep/Review)
4. **Agent flags edge cases** → Escalates to officer queue when needed
5. **Application submitted** → Officer reviews only flagged items, not entire application

### Current Scope
- **In production:** NO (prototype only)
- **Test environment:** YES (day2-validate/prototype/)
- **Real AI model:** NO (hardcoded validation results)
- **Real applicant data:** NO (synthetic only - "PASS-2026-SYNTH-" case IDs)

---

## Opportunity Portfolio

**The agent addresses 5 steps of work in decreasing order of readiness:**

| Step | Agent Role | Ready? | Why/Why Not |
|------|-----------|--------|-------------|
| 1. Show requirements before upload | Chat assistant | ✅ YES | Static ICA rules, no AI needed, plain language validated |
| 5. Escalate edge cases to officers | Workflow routing | ✅ YES | Conservative stop conditions defined, over-escalation is safe |
| 3. Generate plain language prompts | Agent | ⚠️ PARTIAL | Templates written, but need user testing with diverse segments |
| 2. Validate documents in real-time | Agent | ⚠️ NO | AI model not trained, state management bug unfixed, user testing incomplete |
| 4. Accept user dismissals of AI warnings | Agent + workflow | ⚠️ NO | Dismissal/override rates unknown, officer workload impact unknown |

**Value if portfolio works:**
- Reduce error rate from 17% → ≤10% (goal: ≤8.5%)
- 90% of applications pass first submission
- Completion time stays ≤22 minutes
- No increase in support calls
- Officer workload reduced (focus on flagged items only)

**Risk if portfolio fails:**
- False positives frustrate applicants → call volume INCREASES
- Officers don't trust AI → check everything anyway, duplicate work
- Confusing prompts → elderly/low-literacy users give up, need phone support
- Too permissive dismissals → 30%+ officer override rate, efficiency lost

---

## Rules of Engagement

### Agent Decides Alone (No Human in Loop)

**When confidence is 100% (mandatory missing):**
- ❌ Missing birth certificate → Block submission
- ❌ Missing passport photo → Block submission
- ❌ Missing parent NRIC → Block submission
- ✅ Birth certificate uploaded → Mark as verified

**Why agent decides alone:**
- Binary check: document present or not (no judgment)
- ICA policy is clear: these documents are MANDATORY
- No false positive risk (if file exists, it's present)
- Applicant controls upload (can fix immediately)

**Volume:** ~15% of 15,000 applications/week = 2,250 cases/week

---

### Agent Proposes, User Chooses (3-Option Pattern)

**When confidence is 75-89% (soft warning):**
- ⚠️ Photo background slightly off-white (RGB 245,245,243) → 82% confidence
- ⚠️ Name formatting difference ("Lee Xiao-Ming" vs "Lee Xiao Ming") → 78% confidence

**Agent shows:**
```
⚠️ Photo may not meet requirements

The uploaded photo appears to have a slightly coloured background. 
Passport photos must have a plain white background.

This is an AI-generated suggestion. If you believe your photo meets 
ICA requirements, you can proceed.

What to do:
1. Upload a new photo with a plain white background, OR
2. If you believe this photo is correct, you can submit for officer review

[Replace Photo] [My Photo is Correct] [Request Human Review]
```

**Why user chooses:**
- Agent confidence is medium (not definitive)
- False positive risk exists (lighting/shadows can fool AI)
- User knows ground truth (they took/received the photo)
- Three options prevent blind override

**Volume:** ~30% of 15,000 applications/week = 4,500 warnings/week (estimated)

---

### Agent Proposes, User Must Acknowledge (90%+ Confidence)

**When confidence is 90-94% (strong warning):**
- ⚠️ Photo background RGB 240,240,240 → 92% confidence (clearly not white)
- ⚠️ Photo has visible shadows → 91% confidence

**Agent shows:**
```
⚠️ Photo likely does not meet requirements

The uploaded photo appears to have a coloured background. 
Passport photos must have a plain white background.

Our AI validation is 92% confident this photo does not meet ICA requirements.

⚠️ Important: If you proceed with this photo, your application may be 
rejected and returned to you. This would delay your passport by several days.

What to do:
1. Upload a new photo with a plain white background (recommended), OR
2. If you are confident this photo meets requirements, you can proceed

If you choose to proceed:
☐ I understand this photo may not meet requirements
☐ I understand my application may be rejected if the photo is incorrect
☐ I choose to submit this photo for officer review anyway

[Replace Photo] [I Understand - Keep This Photo]
```

**Why acknowledgment required:**
- Agent is VERY confident (almost certain)
- User needs to understand consequences (rejection, delay, resubmit)
- Three checkboxes create appropriate friction
- Still allows submission (never block on AI alone)
- Officer review flagged as HIGH priority (not medium)

**Volume:** ~10% of 15,000 applications/week = 1,500 strong warnings/week (estimated)

---

### A Person Reviews (Human Officer Queue)

**When agent encounters stop conditions:**

1. **Foreign language document**
   - French, German, Spanish birth certificate uploaded
   - Agent stops immediately, routes to "Language Verification" queue
   - **Why:** Supported languages are English, Chinese, Malay, Tamil only
   - **Volume:** ~5% of 15,000 = 750 cases/week (estimated)

2. **Suspected document alteration/fraud**
   - Agent confidence >70% that document tampered
   - Routes to "Fraud Investigation" queue with HIGH priority
   - **Why:** Agent cannot make fraud accusations, needs human judgment
   - **Volume:** <1% of 15,000 = <150 cases/week (estimated)

3. **Legal name change detected**
   - Birth cert: "Lee Xiao Ming", Application: "Lee Ming Xuan" (completely different)
   - Routes to "Name Verification" queue
   - **Why:** May be legal name change (need deed poll), may be error
   - **Volume:** <1% of 15,000 = <150 cases/week (estimated)

4. **Edge case (stateless, diplomatic, refugee documents)**
   - Stateless person, diplomatic passport, refugee travel document
   - Routes to "Special Cases" queue
   - **Why:** Not covered in agent's training data
   - **Volume:** <1% of 15,000 = <150 cases/week (estimated)

5. **User explicitly requests human review**
   - User clicked "Request Human Review" button
   - Routes to "User-Requested Review" queue with MEDIUM priority
   - **Why:** User choice must be honored
   - **Volume:** ~3% of 15,000 = 450 cases/week (estimated)

6. **Agent confidence <75% (silent escalation)**
   - Name hyphen difference: 68% confidence
   - Photo lighting: 72% confidence
   - NOT shown to applicant, silently flagged for officer
   - **Why:** Too uncertain to show, but not confident enough to ignore
   - **Volume:** ~5% of 15,000 = 750 cases/week (estimated)

**Total escalated to officers:** ~15% of applications (2,250/week)
- **Current baseline:** Officers review 100% = 15,000/week
- **With agent:** Officers review 15% = 2,250/week
- **Efficiency gain:** 85% reduction in officer workload

---

## Stop Conditions

### Agent Must Stop and Escalate Immediately

**Category 1: Out of Scope**
- Foreign language document (not English/Chinese/Malay/Tamil)
- Edge case application type (diplomatic, refugee, stateless)
- **Action:** Escalate with message: "requires officer verification"

**Category 2: High Risk**
- Document appears altered (>70% confidence fraud detection)
- Name completely different (not just formatting)
- **Action:** Escalate to priority queue, DO NOT accuse applicant

**Category 3: Uncertainty**
- Confidence threshold boundary (exactly 75.0%)
- Contradictory rules (passport has hyphen, birth cert doesn't - which is source of truth?)
- High-stakes edge case (child age 15 years, 364 days - apply child or adult rules?)
- **Action:** Ask supervising officer (not applicant), document decision for future

**Category 4: User Request**
- User clicked "Request Human Review"
- **Action:** ALWAYS honor, no questions asked

**Category 5: Safety Check Failure**
- Real data detected (no "PASS-2026-SYNTH-" prefix, no "(synthetic)" labels)
- **Action:** Block before processing, protect PII, alert ops team

### Agent Can Continue (No Escalation)

- Common errors: missing birth certificate, missing photo (mandatory, clear rule)
- Applicant fixing errors: re-uploading documents, replacing photos
- 1-2 dismissed warnings: acceptable user control, flag but don't escalate
- Slow network: retry uploads, don't timeout immediately

---

## What Stays Human

### Officer Decisions (Agent Never Makes Final Call)

1. **Final approval/rejection of application**
   - Agent validates BEFORE submission
   - Officer approves AFTER submission
   - Agent is advisory, not authoritative

2. **Interpretation of ambiguous documents**
   - Faded text on old passport
   - Handwritten birth certificate from 1980s
   - Translated foreign document with missing seal

3. **Policy exceptions**
   - Compassionate grounds (urgent travel for medical emergency)
   - Special accommodations (disability, elderly, illiterate)
   - VIP/diplomatic considerations

4. **Fraud determination**
   - Agent flags suspicion (>70% confidence)
   - Officer investigates, makes final determination
   - Officer contacts applicant, police if needed

5. **All dismissed AI warnings**
   - User selected "My Photo is Correct" at 92% confidence
   - Officer reviews photo during processing
   - Officer decides: accept or reject

### User Decisions (Agent Never Overrides)

1. **Whether to replace flagged photo**
   - Agent shows warning, user chooses
   - Three options always available
   - Even 92% confidence warnings allow user control

2. **When to request human review**
   - "Request Human Review" button always available
   - No explanation required from user
   - Agent cannot deny this request

3. **Whether to continue application**
   - User can abandon at any time (not tracked as failure)
   - Agent never forces user to continue
   - No penalty for starting over

### Strategic Decisions (Product Team)

1. **Confidence threshold changes**
   - Current: 90%+ strong warning, 75-89% soft warning, <75% silent
   - Requires Director sign-off to change
   - Cannot be adjusted by agent or individual officers

2. **Error message wording**
   - Templates are "Sacred" (user-tested and approved)
   - Changes invalidate testing results
   - Requires UX team + Director approval

3. **ICA corporate identity**
   - Blue #1C3F6E, Red #C4122E (locked)
   - Government branding compliance
   - Requires legal/comms approval to change

4. **Three-option pattern**
   - Replace / My Photo is Correct / Request Human Review
   - Core UX validated by three leaders
   - Cannot be removed without stakeholder approval

---

## Monitoring and Control

### Real-Time Dashboards (If Agent Goes Wrong)

**Red flags that trigger manual review of agent:**
1. **Dismissal rate >30%** → Model accuracy too low, retrain or tighten thresholds
2. **Officer override rate >20%** → Officers don't trust agent, investigate discrepancies
3. **Support call volume increases** → Prompts confusing, rewrite and retest
4. **Completion time >22 minutes** → Too much friction, simplify flow
5. **Escalation rate >20%** → Agent over-escalating, refine stop conditions

### Kill Switches (Emergency Stop)

**Level 1: Pause new validations**
- Stop validating new applications
- Route all traffic to old manual flow
- Existing validated applications can still submit
- **Use when:** Single high-severity bug detected

**Level 2: Block all submissions**
- Prevent all agent-validated submissions
- Show maintenance message
- Route 100% to manual flow
- **Use when:** Critical security/compliance incident

**Level 3: Rollback completely**
- Disable agent, remove from production
- Delete all agent-generated validations
- Applicants restart from scratch
- **Use when:** Fundamental design flaw discovered

### Review Cadence

**Daily:** Dismissal rate, escalation volume, completion time  
**Weekly:** Officer override patterns, support call themes, false positive analysis  
**Monthly:** Model accuracy, user satisfaction scores, efficiency gains vs baseline  
**Quarterly:** Strategic review with three leaders (Director, Head of Product, CEO)

---

## Decision Authority Matrix

| Decision | Agent Alone | Agent + User | Agent + Officer | Officer Alone |
|----------|-------------|--------------|-----------------|---------------|
| Missing mandatory doc (binary) | ✅ Block | | | |
| Soft warning 75-89% | | ✅ 3 options | | |
| Strong warning 90%+ | | ✅ Acknowledge | | |
| Foreign language doc | | | ✅ Escalate | |
| Fraud suspicion >70% | | | ✅ Escalate | |
| Real data detected | ✅ Refuse | | | |
| Confidence <75% | ✅ Silent flag | | ✅ Review | |
| User requests review | | ✅ Honor | ✅ Route | |
| Final approval/rejection | | | | ✅ Officer |
| Policy exception | | | | ✅ Officer |
| Change thresholds | | | | ✅ Director |
| Change error wording | | | | ✅ UX + Director |

---

**This control panel defines the boundaries of agent autonomy. The agent operates within these rails; everything outside requires human judgment.**
