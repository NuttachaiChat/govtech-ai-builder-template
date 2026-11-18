# Handoff Note - Day 2: Validate

**Date:** 18 Nov 2026  
**Scenario:** B · ICA Passport Applications  
**Status:** Prototypes complete, ready for decision

---

## What It Does

### Core Function
Demonstrates AI-powered validation for ICA passport applications that checks for errors **before** submission to officers.

### User Flow (6 Screens)
1. **upload-flow.html** - Progressive validation
   - Shows document requirements with visual examples BEFORE upload
   - Validates immediately after upload (simulated 1.5s delay)
   - Birth certificate: mandatory check (always passes in prototype)
   - Passport photo: AI suggestion with 85% confidence (always warns)
   - Three options: Replace Photo / My Photo is Correct / Request Human Review

2. **index.html** - Final review screen
   - Summary of 2 flagged errors
   - Clear error cards with actionable guidance
   - Submit button disabled until errors addressed
   - Static mockup (no interactions)

3. **test-scenario.html** - Usability test harness
   - Interactive version of index.html
   - Timer tracks completion time vs 22-minute baseline
   - Click buttons to simulate fixing errors
   - Success message when both fixed

4. **forced-failure-test.html** - False positive demo
   - Ground truth: Valid photo uploaded
   - AI incorrectly flags as non-compliant (false positive)
   - User selects "My Photo is Correct"
   - System accepts without blocking submission
   - Tests transparency and user control

5. **styles.css** - ICA corporate identity (577 lines)
   - Blue #1C3F6E (header), Red #C4122E (alerts)
   - Government service visual polish
   - Longest file (26% of codebase)

6. **README.md** - Test instructions

### What It Proves
✅ Progressive validation reduces cognitive load  
✅ False positives can be handled with transparency  
✅ User control prevents infinite loops  
✅ ICA corporate identity can be applied  

### What It Doesn't Prove
❌ 70%+ of users will succeed (untested)  
❌ AI accuracy is >95% (no real AI)  
❌ Officer workload is manageable (unknown dismissal rate)  
❌ Completion time stays ≤22 minutes (not tested with real users)  

---

## What Is Synthetic

**All data in this prototype is made up and clearly labeled:**

### Test Data
- **Applicant name:** Tan Wei Lin (child, DOB: 15 Mar 2018)
- **Parent/Guardian:** Tan Ah Kow
- **Error scenarios:** Missing birth certificate, photo background issue
- **Metrics:** 17% error rate, 22-minute baseline, 15,000 weekly applications
- **User quote:** "The time to renew my passport is longer than my trip!"

### Simulated Behavior
- **File uploads:** No real files - clicks simulate uploads
- **AI validation:** Hardcoded results (`setTimeout(1500ms)`)
- **Birth certificate:** Always returns "Verified"
- **Passport photo:** Always returns "Warning - 85% Confident"
- **No backend:** No API calls, no database, no real ICA systems

### Labeling
Every screen has yellow banner: "🧪 All data on this page is synthetic and made up for testing purposes only."

---

## What Is Missing for Production

### Critical (Must Have)
1. **State management** - Dismissed warnings reappear on next page (BUG)
2. **Real file uploads** - No `<input type="file">`, no size/type validation
3. **Backend API** - Upload, validate, submit endpoints
4. **Real AI model** - Confidence thresholds, training data, serving infrastructure
5. **Error handling** - Network timeouts, validation failures, service down

### Important (Should Have)
6. **Accessibility** - ARIA labels, keyboard navigation, screen reader support
7. **Mobile responsive** - Media queries, touch targets, tested on devices
8. **Persistence** - Resume applications, save drafts, recover from interruptions
9. **Analytics** - Track dismissal rates, drop-off points, time-to-complete
10. **Authentication** - NRIC verification, session management

### Nice to Have
11. **Multilingual** - Chinese, Malay, Tamil (English only now)
12. **Feedback loop** - Track which dismissed warnings were correct/incorrect
13. **Progressive enhancement** - Works without JavaScript (form fallback)
14. **Email notifications** - Application status updates
15. **Payment integration** - If fees required

### Infrastructure
- CI/CD pipeline
- Staging environment
- Load testing (15,000 applications/week)
- Security audit (OWASP, penetration testing)
- Disaster recovery plan
- Monitoring and alerting

**Estimate to production-ready:** 6-8 weeks with full engineering team

---

## Decisions Engineers Must NOT Change Without Asking

### 1. Error Message Copy (Sacred)
**DO NOT change:**
- "My Photo is Correct" button label
- "This is an AI-generated suggestion" disclaimer
- ICA requirement references in error cards
- Birth certificate explanation: "required to verify parental consent"

**Why:** Exact wording tested for clarity. Changes invalidate user testing results.

**Location:** `day2-validate/context-skill.md` contains approved error message patterns

---

### 2. ICA Corporate Identity Colors (Locked)
**DO NOT change:**
- Primary Blue: `#1C3F6E` (ICA Blue - header)
- Accent Red: `#C4122E` (Alerts, errors)
- Must match official ICA website

**Why:** Government branding compliance required. Changes need legal/comms approval.

**Location:** `day2-validate/prototype/styles.css` lines 1-6

---

### 3. Three-Option Pattern (Core UX)
**DO NOT change:**
- Replace Photo / My Photo is Correct / Request Human Review
- Must always offer all three options
- AI warnings never block submission

**Why:** This pattern solves false positive problem. Validated by three leaders in build-log.md.

**Location:** All error cards with AI suggestions

---

### 4. 70% Success Threshold (Business Rule)
**DO NOT change:**
- Kill switch: <70% of users can fix errors without support
- 22-minute completion time baseline
- 90% application success rate goal

**Why:** These numbers came from stakeholder negotiations. Changes need Director sign-off.

**Location:** `day1-discover-define/opportunity.md`, `day2-validate/build-log.md`

---

### 5. Progressive Validation Approach (Strategy)
**DO NOT change:**
- Validate one document at a time
- Show requirements BEFORE upload
- Immediate feedback AFTER upload
- Final consistency check before submission

**Why:** This addresses Director's objection ("validate at wrong step"). Switching back to validation-at-end needs new stakeholder approval.

**Location:** `day2-validate/PROMPTS.md` Expand prompt results

---

### 6. Confidence Threshold Display (Under Review)
**MAY NEED TO CHANGE:**
- "AI Suggestion (85% Confident)" badge
- Director of Passport Services may reject technical language
- Waiting on user testing feedback

**If users confused:** Replace with "AI may be uncertain" or "Not sure - your choice"

**Location:** `forced-failure-test.html`, `upload-flow.html` - search "85%"

---

## Handoff Decision

### My Recommendation: **KEEP PROTOTYPING**

**Do NOT move to production spec yet.**

---

## Why Keep Prototyping

### 1. Riskiest Assumption Is Untested (Critical)
**Assumption:** "70%+ of applicants can understand and act on AI prompts without support"

**Status:** UNVALIDATED

**Evidence needed:**
- User testing with 10+ real applicants
- Diverse segments: elderly, low digital literacy, foreign documents
- Observed completion rate, time, confusion points

**Risk:** If <70% succeed, prompts aren't clear enough. Building production before validating = wasted engineering time.

### 2. State Management Bug Must Fix First (Blocker)
**Bug:** Dismissed warnings in upload-flow.html reappear in index.html

**Impact:** User thinks they're in infinite loop. Test will fail immediately.

**Fix required:** 1-2 days for session storage OR 1 week for SPA refactor

**Status:** Cannot test with users until fixed.

### 3. Technical Language Not Resolved (Medium)
**Problem:** "85% Confident" badge too technical for citizens

**User testing needed:** Do they understand? Or does it confuse?

**If confused:** Must iterate on language before production

### 4. Mobile Layout Untested (Medium)
**Problem:** Side-by-side photo examples may break on small screens

**iPhone SE test needed:** Does layout work? Or does it need stacked version?

**Production can't launch mobile-broken:** 40%+ applications from phones

### 5. Officer Workload Unknown (Business Risk)
**Question:** What if 30% of users select "My Photo is Correct"?

**Impact:** 30% × 15,000 = 4,500 officer reviews per week

**Unknown:** Can ICA operations handle this? Need ops team input.

**Risk:** Launch system that creates MORE work instead of less.

---

## What Must Happen Before Production Spec

### Phase 1: Fix and Test (2 weeks)
1. **Fix state bug** (1-2 days)
   - Add session storage OR convert to SPA
   - Test across all screens
   
2. **Replace "85% Confident"** if too technical (1 day)
   - Option A: "AI may be uncertain"
   - Option B: "Not sure - your choice"
   - Option C: Remove confidence entirely

3. **Mobile responsive** (2-3 days)
   - Add media queries for photo examples
   - Test on iPhone SE, Android
   - Verify touch targets ≥44px

4. **User testing** (1 week)
   - 10+ participants (elderly, foreign docs, low literacy)
   - Observe completion, time, confusion
   - Ask: "What was unclear? What would you change?"

### Phase 2: Analyze Results (1 week)
5. **If 70%+ succeed:**
   - ✅ Concept validated
   - ✅ Move to production spec
   - ✅ Brief engineering team with Living PRD

6. **If <70% succeed:**
   - ❌ Prompts not clear enough
   - ❌ Iterate: Simplify language, add examples, earlier validation
   - ❌ Retest with revised prompts
   - ❌ Do NOT proceed to production until validated

7. **Analyze failure patterns:**
   - Which step confused users?
   - Which error card unclear?
   - What did they expect to see?
   - What words did they use?

8. **Stakeholder briefing:**
   - Present results to three leaders (Director, Head of Product, CEO)
   - If failed: Explain what needs to change
   - If passed: Get approval to proceed

### Phase 3: Production Spec (only if Phase 2 passes)
9. **Brief engineering team:**
   - Hand over Living PRD
   - Walk through prototype
   - Explain what to keep (design, copy) vs rebuild (everything technical)

10. **Define production requirements:**
    - API contracts
    - AI model requirements (precision, recall, latency)
    - State management architecture
    - Accessibility standards (WCAG 2.1 AA)

---

## Why NOT to Move to Production Spec Now

### Red Flags
🚩 **State bug unfixed** - Test will fail instantly  
🚩 **Zero user validation** - Don't know if it works  
🚩 **Technical language** - May confuse citizens  
🚩 **Mobile untested** - May be unusable on phones  
🚩 **Officer workload unknown** - May create more work  

### What Happens If You Proceed Anyway
- Engineering team spends 6-8 weeks building
- User testing in Week 9 reveals <70% success
- Prompts need rewrite
- Engineering work wasted
- Timeline reset to zero
- Stakeholder confidence damaged

### Better Path
- Fix bug (2 days)
- Test with users (1 week)
- Iterate if needed (1-2 weeks)
- THEN brief engineering (Week 4-5)
- Total time saved: 3-4 weeks
- Confidence level: High

---

## Alternative: If You Must Build Now

**If stakeholder pressure requires starting engineering before user testing:**

### Build These First (Low Risk)
1. ✅ File upload handling - reusable regardless of prompt changes
2. ✅ ICA corporate identity styling - locked and approved
3. ✅ Backend API skeleton - won't change based on user testing
4. ✅ Authentication/NRIC validation - independent of validation flow

### Do NOT Build Yet (High Risk of Rework)
5. ❌ Error message rendering - copy may change after user testing
6. ❌ AI integration - confidence thresholds may need tuning
7. ❌ State management - architecture decision pending (SPA vs MPA)
8. ❌ Final review screen - layout may change based on feedback

### Parallel Track
- Engineering builds low-risk items (1-4 above)
- Product team runs user testing
- Converge in Week 3-4 with validated prompts + working infrastructure

---

## Success Criteria for "Go to Production Spec"

**All of these must be TRUE:**

✅ State management bug fixed and tested  
✅ User testing completed with 10+ participants  
✅ 70%+ success rate achieved  
✅ Completion time ≤22 minutes  
✅ Mobile layout tested on devices  
✅ Technical language resolved (or validated as clear)  
✅ Stakeholders briefed on results  
✅ Officer workload risk assessed with ops team  
✅ Three leaders' requirements documented as production specs  

**If any are FALSE:** Keep prototyping.

---

## My Final Recommendation

**KEEP PROTOTYPING for 2-3 more weeks.**

**The prototype is excellent for what it is** - a research artifact that demonstrates the concept. But the riskiest assumption (70%+ understand prompts) is unvalidated, the state bug will fail testing immediately, and several design decisions (confidence display, mobile layout) are untested.

**Fix the bug, test with users, iterate if needed, THEN move to production spec.**

**Building production before validation is backwards.** The whole point of Day 2 (Validate) is to validate assumptions before Day 3 (Build). Don't skip the validation.

**If user testing passes (70%+):** This is production-ready as a specification. Hand it to engineering with confidence.

**If user testing fails (<70%):** You just saved 6-8 weeks of engineering time that would have been wasted building the wrong thing.

**The 2-3 week delay to validate is cheap insurance against 6-8 weeks of rework.**

---

## Next Owner Actions

### Immediate (This Week)
- [ ] Fix state management bug (session storage quick fix)
- [ ] Replace "85% Confident" with plain language
- [ ] Test mobile layout on iPhone SE and Android

### Next Week
- [ ] Schedule user testing with 10+ participants
- [ ] Prepare test scenarios and observation protocol
- [ ] Run testing sessions, record results

### Week After
- [ ] Analyze results: success rate, time, confusion patterns
- [ ] If passed: Brief stakeholders, proceed to production spec
- [ ] If failed: Document what needs to change, iterate, retest

### When Ready for Production
- [ ] Hand over Living PRD to engineering
- [ ] Walk through prototype (keep design/copy, rebuild technical)
- [ ] Define API contracts, AI requirements, architecture
- [ ] Protect the 6 decisions engineers must not change

---

**This prototype proves the concept CAN work. User testing will prove it DOES work. Only then should engineering build it.**

Good luck! 🚀
