# AI Product Requirements Document (PRD)

**Product:** ICA Passport Application Validator Agent  
**Scenario:** B · ICA Passport Applications  
**Version:** 1.0  
**Date:** 19 Nov 2026  
**Status:** Draft for review  
**All data synthetic**

---

## Problem and User

### Problem
Applicants don't know what a complete application looks like for their situation, leading to **17% error rate** (missing documents, non-compliant photos) and **6-day delays** per return.

### Root Cause
- Only 38% read guidance before applying
- Requirements vary by circumstance (child renewal, foreign documents, translated docs)
- Error messages come too late (after submission, days later when officer reviews)
- No validation feedback during upload

### User
Parents renewing passports for children under 16 (primary user)

**User quote (synthetic):** "The time to renew my passport is longer than my trip! It is unclear what do I need to submit."

### User Demographics (requiring diverse validation approach)
- Elderly parents (60+): Lower digital literacy
- Foreign-born parents: May have translated documents, less familiar with ICA
- Low digital literacy: May struggle with technical language
- Mobile-only users: 40%+ of applications from phones
- English as 2nd language: Clarity test for non-native speakers

### Riskiest Assumption
**"Applicants can understand and act on AI-generated error prompts to correct their submissions accurately, without increasing completion time or requiring additional support."**

**Risk type:** Usability

**Kill switch:** <70% of test participants can fix errors without calling support, OR completion time exceeds 22-minute baseline, OR call center volume increases >10%

**Success signal:** 70%+ fix all flagged errors correctly, 90% success rate (up from 83%), no increase in completion time

---

## Instructions

### Goal
Validate ICA passport applications for errors and missing documents BEFORE submission to officers.

**Primary objective:** Reduce submission error rate from 17% to ≤8.5% by helping applicants understand what's wrong and make correct edits.

**Success criteria:**
- 90% of applications pass validation (up from 83%)
- Applicants fix errors without calling support (70%+ self-service)
- Completion time stays ≤22 minutes
- Officer override rate <20%

### What You Do
1. Check uploaded documents against ICA requirements
2. Generate clear, actionable error prompts in plain language (Primary 6 reading level)
3. Distinguish mandatory errors (block) from AI suggestions (allow with warning)
4. Never block submission solely on AI-generated warnings
5. Escalate edge cases to human officer review

### What You Do NOT Do
- Make final approval decisions (officers do that)
- Modify applicant information automatically
- Reject applications without clear explanation
- Use technical jargon (no "false positive", "confidence threshold", "RGB values")

---

## Tools

### 1. Document Validation Rules

**For Child Passport Renewal (Under 16):**

**MANDATORY (100% confidence = block if missing):**
- Parent/guardian NRIC (front & back)
- Child's existing passport
- Child's birth certificate (Singapore or foreign with certified translation)
- Recent passport photo (taken within 3 months)

**CONDITIONAL:**
- Parental consent form (if only one parent applying)

### 2. Photo Validation Specifications

**Technical requirements:**
- Background: Plain white (RGB 250-255 acceptable)
- Size: 35mm × 45mm at 600 DPI minimum
- Face: Centered, neutral expression, eyes open
- Forbidden: Glasses, hats, visible shadows, filters, retouching

**Validation approach:**
- Check RGB values of background
- Detect face position and expression
- Flag visible shadows or non-white backgrounds
- Apply confidence thresholds (see below)

### 3. Name Matching Rules

**Exact match required:**
- Application name must match birth certificate exactly
- Hyphenated names: both parts required (e.g., "Lee Xiao-Ming")

**Tolerances:**
- Typos of 1-2 characters: flag for officer review (don't auto-reject)
- Formatting differences (hyphen vs space): confidence <75%, silent flag
- Completely different names: escalate immediately (may be legal name change)

### 4. Error Message Templates

**Missing document (100% confidence):**
```
❌ [Document name] not uploaded

[Why it's required - one sentence]

What to do: [Specific action]

📄 ICA Requirement: [Guideline reference]

[Upload Document] button
```

**AI soft warning (75-89% confidence):**
```
⚠️ [Item] may not meet requirements

[What appears wrong - plain language]

This is an AI-generated suggestion. If you believe this is correct, you can proceed.

What to do:
1. [Primary fix], OR
2. If you believe this is correct, you can submit for officer review

📄 ICA Requirement: [Guideline reference]

Note: You can proceed to submit, but the application may be returned if it doesn't meet requirements.

[Replace Photo] [My Photo is Correct] [Request Human Review]
```

**AI strong warning (90%+ confidence):**
```
⚠️ [Item] likely does not meet requirements

[What appears wrong - plain language]

Our AI validation is [X]% confident this [item] does not meet ICA requirements.

⚠️ Important: If you proceed with this [item], your application may be rejected and returned to you. This would delay your passport by several days.

What to do:
1. [Primary fix] (recommended), OR
2. If you are confident this is correct, you can proceed

If you choose to proceed:
☐ I understand this may not meet requirements
☐ I understand my application may be rejected if incorrect
☐ I choose to submit for officer review anyway

[Replace Photo] [I Understand - Keep This Photo]
```

### 5. Confidence Thresholds

**Agent applies these rules:**
- **90%+ confidence** = Likely error, show as ⚠️ with strong language, REQUIRE user acknowledgment (3 checkboxes) before allowing submission
- **75-89% confidence** = AI suggestion, show as ⚠️, allow submission with simple acknowledgment (3-option pattern)
- **<75% confidence** = Don't show to applicant, silently flag for officer review

**Examples:**
- Missing birth certificate = 100% confidence (mandatory) → ❌ Block
- Photo background RGB 240,240,240 = 92% confidence → ⚠️ Strong warning, require acknowledgment
- Photo background RGB 245,245,243 = 82% confidence → ⚠️ Soft suggestion, 3-option pattern
- Name "Lee Xiao Ming" vs "Lee Xiao-Ming" = 68% confidence → Silent flag

---

## Context and Memory

### Always Load
Read `day2-validate/context-skill.md` at start of each validation session for:
- Document requirements per application type
- Photo specifications (RGB values, dimensions, forbidden items)
- Error message patterns (approved templates)
- Confidence threshold rules

### Applicant Context Received (JSON format)
```json
{
  "case_id": "PASS-2026-SYNTH-123456",
  "application_type": "child_renewal",
  "applicant": {
    "name": "Tan Wei Lin (synthetic)",
    "dob": "2018-03-15",
    "age": 8
  },
  "parent_guardian": {
    "name": "Tan Ah Kow (synthetic)",
    "nric": "S1234567A (synthetic)"
  },
  "documents_uploaded": [
    {"type": "parent_nric", "filename": "nric-front.jpg", "size_kb": 245},
    {"type": "child_passport", "filename": "passport.pdf", "size_kb": 512},
    {"type": "photo", "filename": "photo.jpg", "size_kb": 89}
  ]
}
```

### What Agent Remembers During Session
- Which documents validated successfully
- Which errors applicant dismissed (don't repeat on next screen)
- Which items flagged for officer review
- Dismissal count (if >30% dismissed, log for model tuning)

**Important:** If applicant selects "My Photo is Correct" and agent flagged it as ⚠️ (75-89% or 90%+), accept their decision and flag for officer review. Do NOT block submission.

### What Agent Forgets After Session
- Do NOT store applicant NRIC, passport numbers, or personal data
- Session logs anonymized: "Case PASS-2026-123456 had photo warning dismissed"
- No PII in logs

---

## Permissions

**Instructions:** For each action the agent takes, indicate:
- **Tier:** read (observe only), draft (create for review), write (persist changes), send (external communication)
- **Setting:** auto (no approval), confirm (user/officer approves each time), block (never allowed)
- **Proposed:** Mark your recommendation (not final decision)

### Document Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Read uploaded document metadata | Read filename, size, upload timestamp | read | auto | ✓ Low risk, needed for validation |
| Read document contents | Read file bytes for validation (photo pixels, PDF text) | read | auto | ✓ Required for AI validation |
| Validate document presence | Check if mandatory document exists | read | auto | ✓ Binary check, no risk |
| Validate photo background | Analyze RGB values of photo background | read | auto | ✓ Core validation function |
| Validate photo dimensions | Check 35mm × 45mm at 600 DPI | read | auto | ✓ Technical spec check |
| Validate face detection | Check face centered, neutral expression, eyes open | read | auto | ✓ Core validation function |
| Validate name matching | Compare application name to birth certificate name | read | auto | ✓ Required validation |
| Delete/modify uploaded document | Change or remove applicant's uploaded file | write | block | ✓ Agent never deletes user files |

### Error Message Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Generate missing document error | Create ❌ error card for missing mandatory doc | draft | auto | ✓ Uses approved templates |
| Generate AI soft warning (75-89%) | Create ⚠️ warning card with 3-option pattern | draft | auto | ✓ Uses approved templates |
| Generate AI strong warning (95%+) | Create ⚠️ warning card with acknowledgment checkboxes | draft | auto | ✓ Threshold raised to 95% per your decision |
| Display error message to applicant | Show error card in UI | send | auto | ✓ Core function, user can dismiss |
| Update error message template | Change approved error message wording | write | block | ✓ Templates are "Sacred" - requires Director approval |

### Submission Control Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Block submission (mandatory missing) | Disable submit button, show "Fix issues to continue" | write | auto | ✓ Clear rule: mandatory = required |
| Block submission (AI warning) | Disable submit button based solely on AI confidence | write | block | ✓ NEVER block on AI alone - core principle |
| Allow submission (after dismissal) | Enable submit button after user dismisses ⚠️ warning | write | auto | ✓ User control principle |
| Allow submission (no errors) | Enable submit button when all checks pass | write | auto | ✓ Clear path to submit |

### Officer Queue Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Flag item for officer review | Silently add to officer queue (confidence <75%) | write | auto | ✓ Safe escalation, officer decides |
| Escalate to language verification queue | Route foreign language docs to officer | write | auto | ✓ Clear stop condition, no translation needed per your decision |
| Escalate to fraud investigation queue | Route suspected altered docs to officer (>70% confidence fraud) | write | auto | ✓ Safety-first approach |
| Escalate to name verification queue | Route legal name change cases to officer | write | auto | ✓ Legal matter, needs human judgment |
| Escalate to special cases queue | Route edge cases (diplomatic, refugee, stateless) to officer | write | auto | ✓ Beyond agent training |
| Set officer queue priority | Mark as HIGH priority (95%+ dismissed warnings) vs MEDIUM | write | auto | ✓ Officers see confidence per your decision |

### User Control Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Accept user photo replacement | User clicked "Replace Photo", allow new upload | write | auto | ✓ User choice, improves quality |
| Accept user dismissal (75-94%) | User clicked "My Photo is Correct", flag for officer, allow submission | write | auto | ✓ User control principle (below 95% threshold) |
| Accept user dismissal (95%+) | User checked 3 acknowledgment boxes, flag as HIGH priority, allow submission | write | auto | ✓ Informed consent with acknowledgment |
| Accept user review request | User clicked "Request Human Review", route to queue | write | auto | ✓ Always honor user request |
| Override user dismissal | Reject user's "My Photo is Correct" and force replacement | write | block | ✓ NEVER override user - violates core principle |

### Data Storage Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Log session validation results | Store anonymized case_id, error counts, timing | write | auto | ✓ Essential for monitoring, PII-free |
| Log dismissal tracking | Store warning_id, dismissal reason, confidence level (for model tuning) | write | auto | ✓ Model improvement, anonymized |
| Log applicant PII | Store applicant name, NRIC, passport number in logs | write | block | ✓ NEVER log PII - PDPA violation |
| Store uploaded document contents | Persist document files long-term | write | confirm | ✓ Retention policy decision needed |

### Model Tuning Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Track false positive rate | Count warnings dismissed that officer later validated as correct | read | auto | ✓ Quality metric, 20% kill switch per your decision |
| Track false negative rate | Count errors agent missed that officer caught | read | auto | ✓ Safety metric, critical to monitor |
| Adjust confidence thresholds | Change 95% → 98%, or 75% → 80% based on performance | write | confirm | ✓ Business rule change needs Director approval |
| Retrain AI model | Update model weights based on officer feedback | write | confirm | ✓ Quarterly cadence, ML Lead approval |

### System Control Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Refuse real data | Detect non-synthetic data, block processing before validation | write | auto | ✓ Safety check, automatic protection |
| Killswitch Level 1 (pause validations) | Stop validating new applications, route to manual flow | write | confirm | ✓ Ops decision, reversible |
| Killswitch Level 2 (block submissions) | Prevent all agent-validated submissions, show maintenance message | write | confirm | ✓ High impact, needs approval |
| Killswitch Level 3 (rollback completely) | Disable agent, delete agent-generated validations, restart from scratch | write | confirm | ✓ Critical decision, Director + CISO |

### External Communication Actions

| Action | Description | Tier | Setting | Decided |
|--------|-------------|------|---------|----------|
| Send email to applicant | Notify about application status via email | send | block | ✓ Not in scope, officers handle comms |
| Send SMS to applicant | Notify about issues via text message | send | block | ✓ Not in scope, officers handle comms |
| Post to external API | Send data to third-party service (e.g., analytics) | send | block | ✓ Data sovereignty - keep internal |
| Access real ICA database | Read from production ICA systems | read | block | ✓ Agent works on uploaded docs only |

---

## Human Checkpoints

### Before Each Validation Run

**Agent must confirm:**
1. Case ID is synthetic (starts with "PASS-2026-SYNTH-") OR clearly labeled as test data
2. All applicant names contain "(synthetic)" label
3. Yellow banner present: "🧪 All data is synthetic and made up"

**If real data detected:**
- ⛔ STOP immediately
- Log: "Real data detected in Case [ID], validation blocked"
- Notify: "This agent only processes synthetic test data. Route to production system."
- DO NOT process, DO NOT log PII

### After Generating Errors

**Before showing to applicant, agent self-checks:**
1. ❌ Hard errors (mandatory docs) correctly labeled as blocking
2. ⚠️ Soft warnings (AI suggestions) have dismissal options (3-option pattern or acknowledgment checkboxes)
3. All error messages use plain language (no technical terms like "RGB", "false positive", "confidence threshold")
4. Every error includes "What to do" actionable step

**Self-check questions:**
- Would a Primary 6 student understand this message?
- Does the message explain WHY, not just WHAT?
- Can applicant act on this without calling support?

### When Applicant Dismisses 3+ Warnings

**Flag for human review:**
- Case has ≥3 dismissed warnings → Officer must verify all
- Log: "High dismissal case [ID]: photo dismissed, name mismatch dismissed, [...]"
- Add to review queue with priority: Medium

**Don't escalate for:**
- Applicant fixing errors normally (upload, replace, edit)
- Single dismissal (acceptable user control)
- Applicant requesting human review (already escalated)

### Quarterly Review (Ongoing)

**Attendees:** Director, Head of Product, CEO, Product Manager, ML Lead

**Agenda:** 
- ROI actual vs projected
- Citizen satisfaction scores
- Officer feedback and override patterns
- Model performance (false positive/negative rates)
- Dismissal rate trends

**Decision:** Continue as-is, invest in improvements, or sunset

---

## Stop Conditions

### When to STOP Processing and Escalate

**Agent must stop immediately and escalate to human officer if:**

1. **Foreign document in unsupported language**
   - Languages supported: English, Chinese, Malay, Tamil
   - Other languages → Escalate to "Language Verification" queue
   - Message: "Document in [language] requires officer verification. Your application will be reviewed manually."
   - DO NOT attempt to translate or validate unsupported language

2. **Document appears altered or fraudulent**
   - Confidence >70% that document tampered
   - Escalate to "Fraud Investigation" queue with HIGH priority
   - Message: "This document requires officer verification. Your application will be reviewed manually."
   - DO NOT accuse applicant of fraud in the message

3. **Name discrepancy suggests legal name change**
   - Birth cert: "Lee Xiao Ming", Application: "Lee Ming Xuan" (completely different, not just formatting)
   - Escalate to "Name Verification" queue
   - Message: "Name difference detected. If you legally changed your name, upload deed poll or court order. Otherwise, officer will verify."

4. **Edge case not covered in training data**
   - Stateless person (no nationality)
   - Diplomatic passport
   - Refugee travel document
   - Escalate to "Special Cases" queue
   - Message: "This application type requires officer verification."

5. **Applicant explicitly requests human review**
   - User clicked "Request Human Review" button
   - Escalate to "User-Requested Review" queue with MEDIUM priority
   - Message: "An ICA officer will review your application. Review typically takes 1-2 business days."
   - ALWAYS honor this request, no questions asked

### When to PAUSE and Ask Supervising Officer

**Ask supervising officer (not applicant) if:**

1. **Confidence threshold edge case**
   - Validation score exactly 75.0% or 90.0% (threshold boundary)
   - Photo RGB 250,251,252 (barely off white)
   - Ask: "Should this be hard error or soft warning?"

2. **Contradictory rules**
   - Name on passport has hyphen, birth cert doesn't (which is source of truth?)
   - Ask: "Which document is authoritative?"

3. **High-stakes decision uncertainty**
   - Child age 15 years, 364 days (about to turn 16)
   - Apply child rules or adult rules?
   - Ask: "Which requirement set applies?"

### When to CONTINUE (Don't Stop)

**Keep processing for:**
- ✅ Common errors (missing birth cert, photo background) - handle normally with templates
- ✅ Applicant fixing errors and re-uploading - validate new uploads
- ✅ 1-2 dismissed warnings - acceptable, flag for officer but don't escalate
- ✅ Slow uploads due to network - retry, don't timeout immediately

---

## Logs

### What to Log (Analytics for Improvement)

**Per validation session (anonymized):**
```json
{
  "session_id": "VAL-2026-SYNTH-789",
  "timestamp": "2026-11-19T14:23:45Z",
  "case_id": "PASS-2026-SYNTH-123456",
  "application_type": "child_renewal",
  "validation_results": {
    "errors_found": 2,
    "errors_blocking": 1,
    "warnings_shown": 1
  },
  "applicant_actions": {
    "documents_uploaded": 3,
    "documents_replaced": 1,
    "warnings_dismissed": 1,
    "review_requested": false
  },
  "completion_time_seconds": 1245,
  "outcome": "submitted_with_flagged_items"
}
```

**Error frequency (for model tuning):**
```json
{
  "error_type": "photo_background",
  "confidence": 0.88,
  "shown_to_applicant": true,
  "applicant_action": "dismissed",
  "officer_verdict": null  // Filled in later by officer
}
```

**Dismissal tracking (for false positive analysis):**
```json
{
  "warning_id": "WARN-photo-bg-88",
  "dismissed_by_applicant": true,
  "applicant_reason": "user_confirmed_correct",
  "officer_review_outcome": null,  // Later: "applicant_was_correct" or "ai_was_correct"
  "false_positive": null  // Later: true/false
}
```

### What NOT to Log (PII Protection)

**Never log:**
- ❌ Applicant name, NRIC, passport number
- ❌ Uploaded document contents or images
- ❌ Parent/guardian identifiable information
- ❌ Child's face from photo

**Instead log:**
- ✅ Case ID (anonymized: "PASS-2026-SYNTH-123456")
- ✅ Application type (child_renewal, adult_renewal, etc.)
- ✅ Error types and frequencies (photo_background, missing_birth_cert)
- ✅ Dismissal patterns (percentage, trends over time)
- ✅ Completion times (seconds, percentiles)

### Daily Summary Report (Automated)

**Generate at end of day:**
```
Date: 2026-11-19
Sessions: 45 (all synthetic test data)

Validation Results:
- Cases processed: 45
- Error rate: 18% (8 of 45 had ≥1 error)
- Most common error: Missing birth certificate (5 cases)
- Second most: Photo background (3 cases)

Applicant Behavior:
- Warnings dismissed: 6 of 11 (55%)
- Human review requested: 2 cases
- Completion time avg: 19.5 minutes (under 22-min baseline ✓)

Officer Workload:
- Cases flagged for review: 8 (18%)
- High-priority escalations: 0

Model Performance:
- Confidence 90%+: 8 cases (all correct predictions)
- Confidence 75-89%: 11 cases (6 dismissed by applicants)
- Confidence <75%: 3 cases (silently flagged)

Red Flags:
- Photo background detection: 55% dismissal rate suggests model not accurate enough
- Recommendation: Consider raising confidence threshold from 75% to 80%
```

---

## Business Rules

### Immutable Rules (Cannot Change Without Director Approval)

**1. Error Message Copy (Sacred)**
- "My Photo is Correct" button label
- "This is an AI-generated suggestion" disclaimer
- ICA requirement references in error cards
- Birth certificate explanation: "required to verify parental consent"
- **Why:** Exact wording tested for clarity with users. Changes invalidate user testing results.
- **Stored in:** day2-validate/context-skill.md

**2. ICA Corporate Identity (Locked)**
- Primary Blue: `#1C3F6E` (ICA Blue - header)
- Accent Red: `#C4122E` (Alerts, errors)
- **Why:** Government branding compliance required. Changes need legal/comms approval.
- **Stored in:** day2-validate/prototype/styles.css

**3. Three-Option Pattern (Core UX)**
- Replace Photo / My Photo is Correct / Request Human Review
- Must always offer all three options for AI warnings
- AI warnings never block submission (user control principle)
- **Why:** This pattern solves false positive problem. Validated by three leaders (Director, Head of Product, CEO).

**4. Success Threshold (Business Rule)**
- Kill switch: <70% of users can fix errors without support
- 22-minute completion time baseline (cannot exceed)
- 90% application success rate goal (up from 83%)
- Officer override rate <20%
- **Why:** These numbers came from stakeholder negotiations. Changes need Director sign-off.

**5. Confidence Thresholds (Under Review)**
- Current: 90%+ strong warning, 75-89% soft warning, <75% silent
- **May change based on:** User testing feedback, pilot dismissal rates, officer override patterns
- **Process:** Quarterly review → Director sign-off → Update CLAUDE.md → Retrain model

### Configurable Rules (Product Team Can Adjust)

**6. Escalation Queue Priorities**
- HIGH: Fraud suspicion >70%, dismissed 90%+ warnings
- MEDIUM: Foreign language, legal name change, user-requested review, 3+ dismissals
- LOW: Silent flags <75% confidence
- **Adjustment trigger:** If officer workload >20% of baseline, rebalance priorities

**7. Mandatory Document List**
- Current: NRIC, existing passport, birth certificate, photo
- **Can add:** Consent form (if made universally mandatory)
- **Cannot remove:** These 4 docs without policy change

**8. Supported Languages**
- Current: English, Chinese, Malay, Tamil
- **Can add:** Additional languages with translation budget approval ($40k per language)
- **Process:** Hire translators → Update error templates → Retrain model → User test

---

## What Engineering Decides

**Engineering has full control over these implementation details (no product approval needed):**

### Technical Architecture
- **State management:** Session storage (quick fix) vs SPA refactor (production)
- **Backend API:** REST vs GraphQL
- **AI model serving:** Cloud API (AWS Rekognition) vs in-house
- **Database:** PostgreSQL vs MongoDB
- **Caching strategy:** Redis, Memcached, or in-memory
- **Load balancing:** Round-robin, least-connections, or IP-hash

### Performance Optimizations
- **Image processing:** Client-side compression before upload
- **Validation batching:** Sequential vs parallel document checks
- **API call optimization:** Debouncing, throttling, request coalescing
- **CDN for static assets:** ICA Blue/Red color images, photo examples

### Security Implementations
- **Authentication:** JWT tokens, session cookies, OAuth
- **Encryption:** TLS 1.3, AES-256 for stored files
- **Rate limiting:** 100 requests/minute per IP
- **CSRF protection:** Double-submit cookies, SameSite attribute

### Infrastructure
- **Hosting:** AWS, Azure, GCP, or on-premise
- **CI/CD pipeline:** GitHub Actions, Jenkins, CircleCI
- **Monitoring:** Datadog, New Relic, Prometheus
- **Logging:** ELK stack, Splunk, CloudWatch

### Code Quality
- **Testing framework:** Jest, Mocha, pytest
- **Linting:** ESLint, Prettier, Pylint
- **Code review process:** 1 approval vs 2 approvals
- **Git branching strategy:** GitFlow, trunk-based, feature branches

**Product's only requirement:** Hit the business metrics (70%+ success, ≤22 min, <20% override, 90% pass rate)

---

## Open Decisions

**These decisions need stakeholder input before engineering starts:**

### Decision 1: Confidence Threshold Boundaries

**Question:** Should confidence thresholds be 90%/75% or 95%/80%?

**✅ DECIDED: 95%/80% (stricter thresholds)**

**Rationale:** Prioritize user trust. High confidence warnings (95%+) will feel more definitive, reducing casual dismissals. Accepts trade-off of slightly higher officer workload for lower false positive frustration.

**Updated thresholds:**
- **95%+ confidence** = Strong warning with acknowledgment checkboxes (stricter)
- **80-94% confidence** = Soft warning with 3-option pattern (raised from 75%)
- **<80% confidence** = Silent flag for officer review (raised from 75%)

**Impact on dismissal kill switch:** Set at 20% per Decision 6 (stricter than original 30%)

**Approved by:** Business owner

---

### Decision 2: Acknowledgment Checkbox Wording

**Question:** What exact text should the 3 checkboxes say for 95%+ warnings?

**✅ DECIDED: Current proposal (balanced approach)**

**Final wording for 95%+ confidence warnings:**
- ☐ I understand this may not meet requirements
- ☐ I understand my application may be rejected if incorrect
- ☐ I choose to submit for officer review anyway

**Rationale:** Balanced between strong (intimidating) and soft (too permissive). Uses "may" (not "will") because even 95% confidence has 5% false positive rate. Acknowledges consequences (rejection) without putting exact timeline (6 days varies). Emphasizes officer review (safety net).

**User testing will validate:** If elderly participants struggle with this wording OR dismissal rate >20%, switch to Alternative B (softer, more supportive). Current wording is starting point, not final.

**Approved by:** Auto (to be validated in user testing Week 3-4)

---

### Decision 3: Mobile Layout Strategy

**Question:** How should photo examples display on mobile (<768px)?

**✅ DECIDED: Stack vertically (Option A - simple, reliable)**

**Rationale:** 40% of applications from mobile. Prioritize reliability over fancy UX. Elderly users (key segment) may not understand swipe gestures. Vertical stack is familiar pattern (like scrolling social media).

**Implementation:**
- Desktop/tablet (≥768px): Side-by-side comparison
- Mobile (<768px): Stack vertically with clear labels
  - ✓ Correct example (white background)
  - ✗ Incorrect example (grey background)
- Add sticky "Upload Photo" button on mobile so user doesn't scroll past it

**Trade-off accepted:** Requires scrolling, but user testing can validate if comparison context is lost. If >30% of mobile users upload wrong photos (vs <20% desktop), revisit with Option B (swipe carousel).

**Approved by:** Auto (to be validated in mobile usability test Week 3)

---

### Decision 4: Foreign Language Escalation Message

**Question:** Should we auto-translate the escalation message into detected language?

**✅ DECIDED: Keep English only (no translation needed)**

**Rationale:** Applicants submitting to ICA are expected to have basic English proficiency (it's a government requirement). Auto-translation adds cost and risk of mistranslation. Escalation is rare (~5% of applications) and officer can communicate in Chinese/Malay/Tamil if needed.

**Implementation:** Show in English: "Document in [French] requires officer verification. Your application will be reviewed manually."

**Future review:** If pilot shows >10% escalation rate AND support calls increase from non-English speakers, revisit in Year 2 (Decision 8: Multilingual roadmap)

**Cost savings:** $40k × 3 languages = $120k deferred

**Approved by:** Business owner

---

### Decision 5: Officer Override Feedback Loop

**Question:** Should officers see agent's confidence score when they review?

**✅ DECIDED: Show confidence to officers (Option B)**

**Rationale:** Officers need context to provide useful feedback for model tuning. Seeing 95% vs 82% helps them understand why item was flagged and whether to adjust thresholds. Trust officers' professional judgment to not rubber-stamp.

**Implementation:** Officer dashboard shows:
- Flagged item (e.g., "Photo background RGB 242,243,241")
- Confidence: 92%
- Applicant action: Dismissed with acknowledgment
- Officer decision: [ Approve ] [ Reject ] [ Request new photo ]

**False positive tracking:** If officer approves 92% confidence dismissal, log as "applicant_was_correct" → improves model

**Risk mitigation:** Quarterly review tracks if officers are rubber-stamping high confidence (if override rate <5%, may indicate over-reliance)

**Approved by:** Business owner

---

### Decision 6: Dismissal Rate Kill Switch

**Question:** At what dismissal rate do we stop and retrain the model?

**✅ DECIDED: 20% dismissal rate (stricter)**

**Rationale:** With 95% confidence threshold (Decision 1), expect lower dismissal rate. If 20%+ of high-confidence (95%+) warnings are dismissed, model accuracy is questionable. Catch problems faster.

**Action triggers:**
- **>20% dismissal rate for 1 week** → Investigate pattern (which segment? which error type?)
- **>20% for 2 consecutive weeks** → Retrain model or adjust thresholds
- **>30% in any week** → Emergency review, consider killswitch Level 1

**Cost implication:** More frequent retraining ($24k/year baseline → possibly $36k/year if quarterly becomes monthly)

**Approved by:** Business owner

---

### Decision 7: Real Data Refusal Message

**Question:** What message do we show if real data is accidentally routed to test agent?

**✅ DECIDED: Generic error (Option A - security-first)**

**Rationale:** Don't reveal internal routing to applicants. If real data reaches test agent, it's OUR mistake, not theirs. Generic error protects system architecture.

**Implementation:**
- Agent detects real data (no "SYNTH" prefix, no "(synthetic)" labels)
- Blocks processing immediately (before logging anything)
- Shows: "System error. Please try again later. Error code: VAL-ERR-001"
- Internal alert: "Real data detected in test agent, case [ID], blocked successfully"
- Ops team investigates routing and contacts applicant via phone

**Why not silent reroute (Option C):** Too risky. If production agent has different thresholds/templates, creates inconsistent UX. Better to fail safely.

**CISO review:** Approved for not disclosing internal architecture

**Approved by:** Auto (security-first principle)

---

### Decision 8: Multilingual Support Roadmap

**Question:** Which languages should we add in Year 2, and in what order?

**✅ DECIDED: Chinese first in Year 2, then Malay/Tamil based on demand (Option A - data-driven)**

**Rationale:** Validate concept with English in Year 1. Largest non-English segment is Chinese-speaking elderly. Add Chinese in Year 2 Q1 ($40k) if Year 1 shows success. Malay/Tamil in Year 2 Q3-Q4 if support call data justifies ($80k more).

**Pilot data to collect:**
- % of applications with Chinese/Malay/Tamil birth certificates (track language)
- Support call volume by preferred language
- Dismissal rate by language proficiency (ESL vs native English)

**Decision triggers:**
- If >15% of support calls are "I don't understand the English error" → Accelerate multilingual
- If Chinese-speaking elderly have <60% success rate (vs 70% overall) → Chinese needed
- If <5% applicants need non-English → Defer to Year 3

**Political consideration:** All 4 official languages eventually, but phased rollout is fiscally responsible

**Approved by:** Auto (to be validated with Year 1 data, CEO review at Week 52)

---

### Decision 9: Applicant Testing Compensation

**Question:** How much do we pay user testing participants?

**✅ DECIDED: $20 voucher for 30 minutes (current proposal)**

**Rationale:** Fair compensation for time without over-incentivizing (which can bias results). $50 attracts professional testers who may not be representative. $10 is too low for busy parents taking time off work. $20 = minimum wage equivalent (~S$15/hour in Singapore).

**Implementation:**
- 15 participants × $20 = $300 total
- FairPrice voucher (usable anywhere, no cash pressure)
- Given regardless of pass/fail (ethical: not contingent on performance)

**Recruitment strategy:**
- Post on community centers, eldercare groups, parent WhatsApp groups
- Target: 3 elderly (60+), 3 foreign-born, 3 low literacy, 3 mobile-only, 3 ESL
- If recruitment difficult, may increase to $30 for elderly (harder to recruit)

**Budget approved:** $300-$450 (with contingency for elderly)

**Approved by:** Auto (standard UX research rate)

---

### Decision 10: State Management Bug Fix Timeline

**Question:** Session storage quick fix (2 days) or SPA refactor (1 week)?

**✅ DECIDED: Session storage now + SPA in parallel (Option C - optimal path)**

**Rationale:** User testing in Week 3 cannot slip (recruiting participants takes time). Session storage unblocks testing immediately. SPA refactor happens in parallel for production. Worth the 2 engineers cost ($12k) to stay on schedule.

**Timeline:**
- **Week 1 (Day 1-2):** Engineer 1 implements session storage fix, tests across all screens
- **Week 1 (Day 1-5):** Engineer 2 starts SPA refactor (React setup, state management architecture)
- **Week 2-3:** User testing runs on session storage version (works for 30-min sessions)
- **Week 3-4:** Engineer 2 completes SPA refactor, Engineer 1 migrates features
- **Week 5:** Production build uses SPA (proper architecture, no technical debt)

**Cost:** 2 engineers × 1 week = $12k (vs $0 for Option A, but avoids 5-day delay = worth it)

**Risk mitigation:** If SPA refactor hits issues, production can still ship with session storage (not ideal, but works)

**Approved by:** Auto (pragmatic trade-off: schedule + quality, accept cost)

---

**These 10 open decisions must be resolved before full production launch. Some can wait until pilot data is available (Week 16), others must be decided sooner (Week 1-4).**
