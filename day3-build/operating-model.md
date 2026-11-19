# Operating Model - ICA Passport Application Validator

**Scenario:** B · ICA Passport Applications  
**Agent:** Validates passport applications before submission to officers  
**All data synthetic**

---

## Roles

### Decision-Makers
- **Director of Passport Services** - Business owner, accountable for agent outcomes
- **Product Lead** - Day-to-day agent operations, error message approval
- **Engineering Lead** - Technical infrastructure, deployment, API changes
- **ML Lead** - AI model performance, confidence thresholds, retraining
- **Operations Manager** - Officer workload distribution, queue management
- **CISO (Chief Information Security Officer)** - Security, PII protection, fraud escalation

### Contributors
- **UX Team** - Error message templates, user testing, accessibility
- **ICA Officers** - Validate flagged applications, provide feedback on agent decisions
- **Legal Team** - Policy changes, name verification rules, document requirements
- **Customer Support** - Track citizen complaints, identify confusion patterns

### Informed
- **Applicants/Citizens** - Experience AI-generated prompts, make final submission decisions
- **Minister's Office** - High-level metrics (error rate reduction, citizen satisfaction)
- **Audit Team** - Compliance monitoring, PDPA adherence

---

## Decision Rights

| Decision | Harness Part | Who Decides | Who is Consulted | Who is Told |
|----------|--------------|-------------|------------------|-------------|
| **Scope** (Add/remove validation checks, change document requirements, new application types) | Instructions, Tools, Business rules | Director of Passport Services | Product Lead, Operations Manager, Legal Team | Engineering Lead, ML Lead, ICA Officers, Applicants (via release notes) |
| **Permissions** (Change auto/confirm/block settings, adjust confidence thresholds 95%/80%, dismissal kill switch 20%) | Permissions section in PRD | Product Lead | Director of Passport Services, Operations Manager, ML Lead | Engineering Lead, CISO, ICA Officers |
| **Vendor and Model** (Switch AI provider AWS→Google, change model Rekognition→Custom TensorFlow, retrain with new data) | Tools, AI model specification | ML Lead | Engineering Lead, CISO (security review), Product Lead (accuracy trade-offs) | Director of Passport Services, Operations Manager, Finance (cost impact) |
| **Go-Live** (Launch to production, enable for all citizens, remove yellow "synthetic" banner) | Deployment, System control | Director of Passport Services | Product Lead, Operations Manager, Engineering Lead, ML Lead, CISO, Minister's Office | All ICA staff, Applicants (via announcement), Customer Support |
| **Incident** (Kill switch activation, rollback to manual, investigate wrong answer to citizen) | Stop conditions, Killswitches, System control | Operations Manager (Level 1-2), Director (Level 3) | Product Lead (error analysis), ML Lead (model failure?), Engineering Lead (system down?) | CISO (if PII exposed), Customer Support (citizen impact), Minister's Office (if public/media) |
| **Retire** (Decommission agent, return to manual validation, archive model and data) | System lifecycle | Director of Passport Services | Product Lead (transition plan), Operations Manager (officer retraining), Engineering Lead (data retention) | All ICA staff, Applicants (via announcement), Finance, Audit Team |

---

## Incident Response Playbook

### Scenario: Agent Sends Wrong Answer to Applicant (Monday 08:00)

**What happened:** Applicant received incorrect validation prompt (e.g., "Missing birth certificate" when birth certificate was uploaded, or "Photo background incorrect" for a compliant photo with 99% confidence).

---

### Who Acts First

**Operations Manager (Duty Officer)**
- **First action:** Receive alert from monitoring dashboard (dismissal spike, support call spike, OR officer override spike)
- **Within 5 minutes:** Review case details
  - Case ID: PASS-2026-SYNTH-123456
  - Error shown: "❌ Birth Certificate not uploaded"
  - Ground truth: Birth certificate WAS uploaded (officer verified)
  - Agent log: File upload succeeded, but validation returned "missing" (bug)
- **Decision:** Is this isolated (1 case) or widespread (pattern)?
  - **Isolated:** Flag for Product Lead, continue monitoring
  - **Widespread (≥5 cases in 1 hour):** Activate Killswitch Level 1

---

### Who Switches Agent Off

**Killswitch Authority (Escalating Levels):**

#### Level 1: Feature Flag (Operations Manager)
- **Trigger:** ≥5 incorrect validations in 1 hour, OR dismissal rate >20%, OR officer override rate >20%
- **Action:** Disable AI validation via feature flag (takes effect in <5 minutes)
- **Impact:** Agent stops generating AI warnings, only checks mandatory documents (100% confidence)
- **Applicant experience:** Birth certificate/photo validation disabled, faster processing but lower error detection
- **Authority:** Operations Manager (no approval needed, notify Director within 30 minutes)

#### Level 2: Maintenance Mode (Product Lead)
- **Trigger:** Feature flag insufficient (bug in mandatory checks too), OR PII leak detected
- **Action:** Enable maintenance page, route all applications to officers
- **Impact:** Agent completely offline, 100% manual validation (15,000 cases/week to officers)
- **Applicant experience:** "System under maintenance. Applications will be reviewed manually."
- **Authority:** Product Lead (notify Director immediately, CISO if PII involved)

#### Level 3: Emergency Shutdown (Director of Passport Services)
- **Trigger:** Public incident (media coverage), Minister escalation, legal violation (PDPA breach)
- **Action:** Shut down agent infrastructure, quarantine data, freeze all changes
- **Impact:** Agent offline indefinitely, manual validation, investigation launched
- **Authority:** Director of Passport Services ONLY
- **Who is told:** Minister's Office, CISO, Legal Team, all ICA staff

---

### Who Tells the Business Owner

**Chain of Notification:**

#### Isolated Incident (1-4 cases):
1. **Operations Manager** → **Product Lead** (within 30 minutes)
   - Slack message: "Agent validation error detected. Case #123456. Investigating."
2. **Product Lead** → **Director of Passport Services** (end of day)
   - Daily summary: "1 false positive today (photo validation). Root cause: lighting edge case. Officer corrected. No citizen impact."

#### Widespread Incident (≥5 cases, killswitch activated):
1. **Operations Manager** → **Director of Passport Services** (immediately, within 15 minutes)
   - Call first, then Slack: "Level 1 killswitch activated. 8 birth cert false positives since 08:00. AI validation disabled. Officers handling manually."
2. **Director** → **Minister's Office** (if >50 cases affected, within 1 hour)
   - Email summary: "Passport validation agent paused due to technical issue. Manual processing in place. No data breach. Expected resolution: 24 hours."

#### Public/Legal Incident (media, PDPA breach, Minister escalation):
1. **Operations Manager** → **Director** → **Minister's Office** (immediately, <15 minutes)
2. **CISO** (if PII breach) → **Legal Team** → **PDPA Commission** (within 72 hours by law)
3. **Product Lead** → **Customer Support** → **Affected Applicants** (apology, compensation if delay caused)

---

## What is Missing (Filled In)

### 1. Monitoring and Alerting (Who Watches the Dashboard)

**Primary:** Operations Manager (duty rotation, 24/7)
- Monitors dashboard every 30 minutes during business hours (Mon-Fri 08:00-18:00)
- Receives automated alerts:
  - Dismissal rate >20% (SMS alert)
  - Officer override rate >20% (SMS alert)
  - Support call spike (>10 calls in 1 hour mentioning "passport photo error")
  - False positive detected (officer marks agent wrong)

**Secondary:** Product Lead (business hours only)
- Reviews daily summary report at 09:00
- Analyzes trends: Are false positives increasing? Which validation failing?

**On-Call (After Hours):** Engineering Lead (weekends, public holidays, after 18:00)
- Receives critical alerts only (Level 2-3 killswitch, system down, PII breach)

---

### 2. Root Cause Analysis (Who Investigates)

**For Isolated Incidents (1-4 cases):**
- **Product Lead** investigates within 24 hours
- Questions:
  - Which validation failed? (photo, name matching, document presence)
  - Agent confidence level? (95%+ strong warning, 80-94% soft, <80% silent)
  - Officer verdict? (Agent was wrong, or applicant was wrong?)
  - Pattern? (Same photo type? Same file format?)
- Output: Bug report to Engineering Lead OR retraining recommendation to ML Lead

**For Widespread Incidents (≥5 cases, killswitch activated):**
- **Engineering Lead** + **ML Lead** + **Product Lead** investigate within 4 hours
- War room (Zoom call):
  - Check logs: API errors? Model latency spike? Database timeout?
  - Check model: Accuracy dropped? Training data drift?
  - Check edge case: New photo type? iOS 18 camera format?
- Output: Fix deployed within 24 hours OR escalate to Director to keep agent off longer

---

### 3. Post-Incident Review (Who Learns from Mistakes)

**Within 3 Days of Resolution:**

**Meeting:** Product Lead chairs, invites Engineering Lead, ML Lead, Operations Manager
**Agenda:**
1. What happened? (timeline, root cause)
2. How many applicants affected? (1 case vs 50 cases vs 500 cases)
3. How fast did we detect? (5 minutes vs 2 hours vs next day)
4. How fast did we respond? (killswitch in 5 min vs 2 hours)
5. What prevented faster detection? (alert threshold too high? dashboard unclear?)
6. What prevented faster fix? (no rollback plan? engineer unavailable? model too slow to retrain?)
7. What changes to prevent recurrence?
   - Code fix (deploy within 1 week)
   - Alert tuning (lower threshold from 20% to 15%?)
   - Process change (add secondary on-call? faster escalation path?)
   - Documentation update (add this incident to runbook)

**Output:** Post-incident report shared with Director, filed in `incidents/` folder

**Example Report Structure:**
```markdown
# Incident Report: Birth Certificate False Positives (2026-11-19)

**Severity:** Medium (8 cases, Level 1 killswitch, no PII breach)
**Duration:** 2 hours (08:00-10:00)
**Impact:** 8 applicants received "Missing birth certificate" error for uploaded files

**Root Cause:** Bug in file upload validation - PDF files >5MB incorrectly marked as "not received" by backend API

**Detection:** 08:25 - Operations Manager noticed dismissal rate spike (3% → 8%)
**Response:** 08:30 - Level 1 killswitch activated, AI validation disabled
**Fix:** 10:00 - Engineering deployed hotfix, increased PDF size limit to 10MB
**Resolution:** 10:15 - Killswitch disabled, agent resumed, 8 applicants contacted and re-validated

**Lessons:**
1. File size limit (5MB) too small for some government-issued documents
2. Error message unclear - applicants thought they forgot to upload, not file too large
3. Alert threshold (20% dismissal) detected incident, but 25 minutes is long
   - Action: Lower threshold to 15% for faster detection

**Prevented Recurrence:**
- Increased file size limit to 10MB
- Added file size validation BEFORE upload (show warning: "File too large, max 10MB")
- Tuned alert: dismissal spike from >20% to >15%
- Updated error message: "❌ File upload failed (too large)" instead of "not uploaded"
```

---

### 4. Applicant Remediation (Who Fixes the User Experience)

**If Agent Gave Wrong Answer:**

**Customer Support (within 24 hours):**
1. Identify affected applicants from case logs
2. Email apology:
   ```
   Subject: Update on Your Passport Application

   Dear [Name],

   We identified a technical issue with our validation system on [date] that 
   may have incorrectly flagged your [document type].

   Your application has been reviewed by an ICA officer and is now processing normally.

   We apologize for any confusion. Your application has not been delayed.

   If you have questions, contact us at 6391-6100.

   Immigration & Checkpoints Authority
   ```

**If Applicant Delayed Due to Agent Error:**
- Priority processing (move to front of officer queue)
- Waive any late fees if passport needed urgently
- Director's Office: Assess compensation (S$20 voucher for significant delay)

---

### 5. Communication Plan (Who Tells Whom, When)

| Scenario | Internal Communication | External Communication |
|----------|----------------------|------------------------|
| **Isolated (1-4 cases)** | Slack #passport-agent channel, daily summary | None (handled quietly) |
| **Widespread (5-49 cases)** | Email to Director, Minister's Office (heads-up), ICA staff (all-hands) | None unless media asks |
| **Large-scale (50-499 cases)** | Email to Minister's Office, Legal Team, all ICA staff | Press release if media coverage, apology to affected applicants |
| **Critical (≥500 cases OR PII breach)** | Immediate call to Minister, PDPA Commission (if breach), Board | Public statement, media briefing, PDPA notification (72 hours) |

---

## Decision Authority Matrix (Reference)

| Decision Type | Approver | Veto Authority | Implementation |
|---------------|----------|----------------|----------------|
| **Daily Operations** (accept dismissals, flag for officer review, log anonymized data) | Agent (autonomous) | Operations Manager (Level 1 killswitch) | Automatic |
| **Tactical Changes** (adjust confidence from 95% to 97%, change error message wording, tune alert threshold) | Product Lead | Director of Passport Services | Engineering Lead deploys within 1 week |
| **Strategic Changes** (add new document type, change validation approach, switch AI vendor) | Director of Passport Services | Minister's Office (budget >$100k) | Product Lead writes PRD, Engineering Lead estimates, Director approves |
| **Emergency Actions** (Level 1-2 killswitch, rollback, maintenance mode) | Operations Manager (L1), Product Lead (L2) | Director (can override to keep running) | Immediate (5-30 minutes) |
| **Crisis Actions** (Level 3 shutdown, data quarantine, public apology) | Director of Passport Services | Minister's Office | Immediate (15 minutes) |

---

## Quarterly Review Cycle

**Every 3 Months:**

**Attendees:** Director of Passport Services, Product Lead, Engineering Lead, ML Lead, Operations Manager, UX Team

**Agenda:**
1. **Performance Metrics** (ML Lead presents)
   - Error detection rate: 17% → X%
   - False positive rate: Target <5%, Actual X%
   - Officer override rate: Target <20%, Actual X%
   - Dismissal rate: Target <20%, Actual X%
   - Completion time: Target ≤22 min, Actual X min
   
2. **Incident Review** (Operations Manager presents)
   - Incidents this quarter: X total, Y minor, Z major
   - Mean time to detect: X minutes
   - Mean time to resolve: Y hours
   - Lessons learned

3. **Citizen Feedback** (Customer Support presents)
   - Support call volume: Up/Down X%
   - Top 3 confusion points
   - User testing results (if any)

4. **Strategic Decisions** (Director leads)
   - Should we expand scope? (add adult renewals, new passports)
   - Should we tighten thresholds? (95% → 98%?)
   - Should we add multilingual? (Chinese first, Year 2)
   - Should we retire? (if not delivering value)

**Output:** Roadmap for next quarter, budget allocation, approval to proceed

---

**This operating model defines who decides, who is told, and who acts when things go wrong. Every role knows their authority.**
