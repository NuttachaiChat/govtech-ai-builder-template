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

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Read uploaded document metadata | Read filename, size, upload timestamp | read | | |
| Read document contents | Read file bytes for validation (photo pixels, PDF text) | read | | |
| Validate document presence | Check if mandatory document exists | read | | |
| Validate photo background | Analyze RGB values of photo background | read | | |
| Validate photo dimensions | Check 35mm × 45mm at 600 DPI | read | | |
| Validate face detection | Check face centered, neutral expression, eyes open | read | | |
| Validate name matching | Compare application name to birth certificate name | read | | |
| Delete/modify uploaded document | Change or remove applicant's uploaded file | write | | |

### Error Message Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Generate missing document error | Create ❌ error card for missing mandatory doc | draft | | |
| Generate AI soft warning (75-89%) | Create ⚠️ warning card with 3-option pattern | draft | | |
| Generate AI strong warning (90%+) | Create ⚠️ warning card with acknowledgment checkboxes | draft | | |
| Display error message to applicant | Show error card in UI | send | | |
| Update error message template | Change approved error message wording | write | | |

### Submission Control Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Block submission (mandatory missing) | Disable submit button, show "Fix issues to continue" | write | | |
| Block submission (AI warning) | Disable submit button based solely on AI confidence | write | | |
| Allow submission (after dismissal) | Enable submit button after user dismisses ⚠️ warning | write | | |
| Allow submission (no errors) | Enable submit button when all checks pass | write | | |

### Officer Queue Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Flag item for officer review | Silently add to officer queue (confidence <75%) | write | | |
| Escalate to language verification queue | Route foreign language docs to officer | write | | |
| Escalate to fraud investigation queue | Route suspected altered docs to officer (>70% confidence fraud) | write | | |
| Escalate to name verification queue | Route legal name change cases to officer | write | | |
| Escalate to special cases queue | Route edge cases (diplomatic, refugee, stateless) to officer | write | | |
| Set officer queue priority | Mark as HIGH priority (90%+ dismissed warnings) vs MEDIUM | write | | |

### User Control Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Accept user photo replacement | User clicked "Replace Photo", allow new upload | write | | |
| Accept user dismissal (75-89%) | User clicked "My Photo is Correct", flag for officer, allow submission | write | | |
| Accept user dismissal (90%+) | User checked 3 acknowledgment boxes, flag as HIGH priority, allow submission | write | | |
| Accept user review request | User clicked "Request Human Review", route to queue | write | | |
| Override user dismissal | Reject user's "My Photo is Correct" and force replacement | write | | |

### Data Storage Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Log session validation results | Store anonymized case_id, error counts, timing | write | | |
| Log dismissal tracking | Store warning_id, dismissal reason, confidence level (for model tuning) | write | | |
| Log applicant PII | Store applicant name, NRIC, passport number in logs | write | | |
| Store uploaded document contents | Persist document files long-term | write | | |

### Model Tuning Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Track false positive rate | Count warnings dismissed that officer later validated as correct | read | | |
| Track false negative rate | Count errors agent missed that officer caught | read | | |
| Adjust confidence thresholds | Change 90% → 95%, or 75% → 80% based on performance | write | | |
| Retrain AI model | Update model weights based on officer feedback | write | | |

### System Control Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Refuse real data | Detect non-synthetic data, block processing before validation | write | | |
| Killswitch Level 1 (pause validations) | Stop validating new applications, route to manual flow | write | | |
| Killswitch Level 2 (block submissions) | Prevent all agent-validated submissions, show maintenance message | write | | |
| Killswitch Level 3 (rollback completely) | Disable agent, delete agent-generated validations, restart from scratch | write | | |

### External Communication Actions

| Action | Description | Tier | Setting | Proposed |
|--------|-------------|------|---------|----------|
| Send email to applicant | Notify about application status via email | send | | |
| Send SMS to applicant | Notify about issues via text message | send | | |
| Post to external API | Send data to third-party service (e.g., analytics) | send | | |
| Access real ICA database | Read from production ICA systems | read | | |

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

**Current proposal:** 90%+ strong warning, 75-89% soft warning, <75% silent

**Tradeoffs:**
| Threshold | False Positives | False Negatives | User Friction | Officer Workload |
|-----------|-----------------|-----------------|---------------|-------------------|
| 95%/80% (stricter) | Low (good) | High (bad) | Low (good) | High (bad) - misses errors |
| 90%/75% (current) | Medium | Medium | Medium | Medium |
| 85%/70% (looser) | High (bad) - confuses users | Low (good) | High (bad) - too many warnings | Low (good) |

**Data needed:** User testing dismissal rates, pilot officer override patterns

**Decision owner:** Director of Passport Services

**Deadline:** Week 16 (end of pilot)

---

### Decision 2: Acknowledgment Checkbox Wording

**Question:** What exact text should the 3 checkboxes say for 90%+ warnings?

**Current proposal:**
- ☐ I understand this may not meet requirements
- ☐ I understand my application may be rejected if incorrect
- ☐ I choose to submit for officer review anyway

**Alternative A (stronger consequence framing):**
- ☐ I understand this will likely be rejected (not "may")
- ☐ I accept a 6-day delay if rejected
- ☐ I have read ICA photo requirements and believe my photo is correct

**Alternative B (softer, more supportive):**
- ☐ I've double-checked my photo against ICA requirements
- ☐ I understand an officer will review this during processing
- ☐ I'm ready to submit

**Tradeoffs:**
- Stronger = fewer dismissals, but feels intimidating (elderly may give up)
- Softer = more dismissals, but feels supportive (better UX)

**Data needed:** User testing feedback, cognitive walkthrough with elderly participants

**Decision owner:** Head of Product + UX Lead

**Deadline:** Week 4 (after user testing)

---

### Decision 3: Mobile Layout Strategy

**Question:** How should photo examples display on mobile (<768px)?

**Current state:** Side-by-side comparison (Correct ✓ | Incorrect ✗) may break on small screens

**Option A: Stack vertically**
- Pro: Simple, works on all devices
- Con: Requires scrolling, may lose comparison context

**Option B: Swipe carousel**
- Pro: Modern UX, preserves comparison
- Con: Not obvious to elderly users (needs tutorial)

**Option C: Tap to toggle**
- Pro: Minimal space, interactive
- Con: Only shows one at a time, loses comparison

**Data needed:** Mobile usability test on iPhone SE and Android

**Decision owner:** UX Lead

**Deadline:** Week 3 (before user testing)

---

### Decision 4: Foreign Language Escalation Message

**Question:** Should we auto-translate the escalation message into detected language?

**Current proposal:** Show in English only: "Document in [French] requires officer verification."

**Option A: Keep English only**
- Pro: Simple, no translation cost
- Con: French-speaking applicant may not understand

**Option B: Auto-translate to common languages**
- Pro: Better UX for non-English speakers
- Con: Google Translate errors may confuse more than help

**Option C: Offer language selector**
- Pro: User controls language, accurate translations
- Con: Adds UI complexity, translation cost $40k × 3 languages

**Data needed:** Volume of foreign language documents by language (from pilot)

**Decision owner:** Head of Product

**Deadline:** Week 20 (Month 1 of full launch, if volume is high)

---

### Decision 5: Officer Override Feedback Loop

**Question:** Should officers see agent's confidence score when they review?

**Current proposal:** Officer sees flagged items, but NOT the confidence percentage

**Option A: Hide confidence from officers**
- Pro: Officer makes independent judgment (no anchoring bias)
- Con: Officer doesn't know why agent flagged it

**Option B: Show confidence to officers**
- Pro: Officer understands agent's uncertainty, can provide better feedback
- Con: Officer may rubber-stamp 95% confidence (trust AI too much)

**Option C: Show confidence + agent's reasoning**
- Pro: Full transparency, officer learns from agent
- Con: Takes longer to review (defeats efficiency goal)

**Data needed:** Officer feedback during pilot ("Do you want to see confidence?")

**Decision owner:** Director of Passport Services

**Deadline:** Week 16 (end of pilot)

---

### Decision 6: Dismissal Rate Kill Switch

**Question:** At what dismissal rate do we stop and retrain the model?

**Current proposal:** >30% dismissal rate triggers model retraining

**Alternatives:**
- 20% (stricter, faster response, more expensive retraining)
- 40% (looser, fewer false alarms, more false positives reach officers)
- Sliding scale: >30% for 1 week = investigate, >30% for 4 weeks = retrain

**Tradeoffs:**
- Lower threshold = catch problems faster, but may retrain unnecessarily
- Higher threshold = tolerate more false positives, but save retraining cost

**Data needed:** Pilot dismissal rate by segment (elderly vs young, English vs ESL)

**Decision owner:** ML Engineering Lead + Director

**Deadline:** Week 16 (end of pilot)

---

### Decision 7: Real Data Refusal Message

**Question:** What message do we show if real data is accidentally routed to test agent?

**Current proposal:** Generic error: "System error. Please try again later. Error code: SYS-ERR-001"

**Option A: Generic error (current)**
- Pro: Doesn't reveal routing mistake to applicant
- Con: Applicant gets stuck, no resolution

**Option B: Transparent error**
- "This system is for testing only. Your application has been routed to the live system."
- Pro: Honest, tells applicant what happened
- Con: Reveals internal routing (security concern)

**Option C: Silent reroute**
- Detect real data → automatically reroute to production agent → no error shown
- Pro: Seamless UX, applicant never knows
- Con: Risky if production agent isn't ready, logging becomes complex

**Data needed:** IT Security team input on acceptable message disclosure

**Decision owner:** CISO + Head of Product

**Deadline:** Week 8 (before production build starts)

---

### Decision 8: Multilingual Support Roadmap

**Question:** Which languages should we add in Year 2, and in what order?

**Current:** English only (Chinese/Malay/Tamil for documents, but UI/errors in English)

**Demand data needed:**
- % of applications with Chinese/Malay/Tamil birth certificates
- Support call volume by language
- Demographic data (which languages do applicants prefer?)

**Budget:** $40k per language × 3 languages = $120k

**Priority options:**
- **Option A:** Chinese first (largest non-English speaking population)
- **Option B:** All 3 simultaneously (official languages parity)
- **Option C:** Defer until Year 3 (English-first validates concept)

**Decision owner:** CEO (political decision on language equity)

**Deadline:** Week 52 (Year 1 review, before Year 2 budget)

---

### Decision 9: Applicant Testing Compensation

**Question:** How much do we pay user testing participants?

**Current proposal:** $20 voucher for 30 minutes

**Alternatives:**
- $10 (budget-friendly, may get less diverse participants)
- $50 (attracts elderly/busy parents, higher quality feedback)
- No compensation (community service framing, authenticity)

**Budget impact:**
- 15 participants × $20 = $300 (current)
- 15 participants × $50 = $750 (premium)

**Data needed:** Recruitment success rate at each compensation level

**Decision owner:** Product Manager

**Deadline:** Week 2 (before recruitment starts)

---

### Decision 10: State Management Bug Fix Timeline

**Question:** Session storage quick fix (2 days) or SPA refactor (1 week)?

**Current blocker:** Dismissed warnings reappear on next screen (infinite loop UX)

**Option A: Session storage quick fix**
- Pro: Fast (2 days), unblocks user testing Week 3
- Con: Technical debt, must refactor before production

**Option B: SPA refactor immediately**
- Pro: Proper fix, production-ready architecture
- Con: Delays user testing by 5 days (Week 3 → Week 4)

**Option C: Session storage now, SPA in parallel during user testing**
- Pro: User testing on time, production-ready by Week 4
- Con: Duplicate work, 2 engineers needed

**Tradeoffs:**
- Speed vs quality vs budget

**Decision owner:** Engineering Lead + Product Manager

**Deadline:** Week 1 (immediately, blocks all other work)

---

**These 10 open decisions must be resolved before full production launch. Some can wait until pilot data is available (Week 16), others must be decided sooner (Week 1-4).**
