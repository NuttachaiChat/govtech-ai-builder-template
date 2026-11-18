# Living PRD - Day 2: Validate

Scenario: B · ICA Passport Applications (All data synthetic)

---

## 1. Product Overview

**What:** AI-powered validation system for ICA passport applications that checks for missing documents and compliance errors BEFORE submission to officers.

**Why:** 17% of passport applications have errors or missing documents. Each return adds 6 days. Only 38% of applicants read guidance before applying.

**For whom:** Parents renewing passports for children under 16 (primary user), but applicable to all passport application types.

**What it does:**
- Shows document requirements with visual examples before upload
- Validates each document immediately upon upload
- Flags missing documents (hard errors) and potential discrepancies (AI suggestions)
- Allows applicants to dismiss AI warnings or request human review
- Performs final consistency check before submission

**What it doesn't do:**
- Does NOT replace ICA officer final approval
- Does NOT automatically reject applications based solely on AI warnings
- Does NOT modify applicant information

---

## 2. Problem and Riskiest Assumption

### Problem
Applicants don't know what a complete application looks like for their situation, leading to 17% error rate and 6-day delays per return.

### Root Cause
- Only 38% read guidance before applying
- Requirements vary by circumstance (child renewal, foreign documents, etc.)
- Error messages come too late (after submission, days later)

### Riskiest Assumption
**"Applicants can understand and act on AI-generated error prompts to correct their submissions accurately, without increasing completion time or requiring additional support."**

**Risk type:** Usability

**Why it's risky:**
- AI may produce false positives (flag valid photos as non-compliant)
- Technical language ("85% confident") may confuse non-technical users
- Multiple warnings may overwhelm and cause abandonment
- If applicants dismiss ALL warnings (including valid ones), defeats the purpose

**Kill switch:**
- <70% of test participants can fix errors without calling support
- Completion time exceeds 22-minute baseline
- Call center volume increases >10%

**Success signal:**
- 70%+ fix all flagged errors correctly
- 90% success rate (up from 83%)
- No increase in completion time

---

## 3. User Flow and Screen Map

### Prototype Files (6 screens)

**1. upload-flow.html** (Progressive validation)
- Step 1: Show requirements BEFORE upload
- Step 2: Upload birth certificate → immediate validation
- Step 3: Upload passport photo → immediate validation (may flag warning)
- Step 4: User chooses: Replace / Keep Original / Request Human Review
- Step 5: Continue to final review

**2. index.html** (Final review screen)
- Summary of uploaded documents (2 of 4 verified)
- Alert box: "2 issues need your attention"
- Error cards with actionable guidance
- Submit button (disabled until errors fixed)

**3. test-scenario.html** (Usability test harness)
- Timer (measures completion time vs 22-minute baseline)
- Same as index.html but interactive
- Tracks: Fix actions, time elapsed, success/failure

**4. forced-failure-test.html** (False positive scenario)
- Ground truth: Valid photo uploaded
- AI incorrectly flags as "coloured background"
- User selects "My Photo is Correct"
- System accepts without blocking submission
- Tests transparency and user control

**5. styles.css** (549 lines - longest file)
- ICA corporate identity (Blue #1C3F6E, Red #C4122E)
- Error states, progress bars, badges, buttons
- Government service visual polish

**6. README.md** (Test instructions)

### Screen Flow
```
Details Form → Upload Documents → Validate → Review → Submit
                     ↓                ↓
              [Immediate        [Dismissed
               feedback]         warnings
                                 NOT carried
                                 forward - BUG]
```

---

## 4. Success Metrics

### Primary Metrics
- **Error rate:** Reduce from 17% to ≤8.5% (half)
- **Success rate:** Increase from 83% to 90% (9 in 10 pass)
- **Completion time:** Stay at or below 22-minute baseline

### Leading Indicators
- **Usability:** 70%+ fix errors without support
- **AI accuracy:** >95% precision (false positives <5%)
- **Trust:** Officer override rate <20%

### Guardrails
- Call center volume: No increase >10%
- Dismissal rate: If >30% select "Keep Original", AI not accurate enough
- Time to process: 1-2 days for human review requests (acceptable delay)

### Current Prototype Metrics
- **Files:** 6 files, 2,119 lines
- **Interactions:** 14 onclick handlers
- **Validation:** Simulated (1.5s delay, hardcoded results)
- **Test coverage:** 2 error scenarios (missing doc, photo background)

---

## 5. Technical Reality

### What's Actually Built

**Static HTML mockups with inline JavaScript**
- No real file uploads (`<input type="file">` missing)
- No backend API
- No real AI validation
- Clicks simulate uploads: `onclick="simulateUpload('photo', 'warning')"`
- Results hardcoded: `setTimeout(1500ms)` fake delay
- State lives in page-scoped variables (lost on navigation)

**Code Structure**
```
day2-validate/prototype/
├── index.html (157 lines) - Final review
├── test-scenario.html (386 lines) - Usability test
├── upload-flow.html (512 lines) - Progressive flow
├── forced-failure-test.html (515 lines) - False positive demo
├── styles.css (549 lines) - ICA styling [LONGEST FILE]
└── README.md
```

**Key Technical Patterns**
- Inline onclick handlers: 14 across files
- Inline styles: 8+ mixed with CSS file
- CSS duplication: `.error-card` defined in 3 files
- No state management: localStorage, sessionStorage, URL params all absent
- No error handling: Network timeouts, validation failures, service down

**What Works**
✅ Visual design (ICA corporate identity)  
✅ User flow demonstrated  
✅ Copy is clear ("My Photo is Correct" not "Override AI")  
✅ False positive scenario handled transparently  

**What's Missing**
❌ File upload handling  
❌ State persistence (dismissed warnings reappear)  
❌ Real AI integration  
❌ Accessibility (ARIA labels, keyboard nav)  
❌ Mobile optimization (claimed, not tested)  
❌ Error boundaries  

---

## 6. Assumptions and Risks

### Assumptions Being Tested
1. **Applicants understand AI prompts** - Test with 10+ diverse users
2. **Prompts are actionable** - 70%+ fix correctly without help
3. **AI accuracy acceptable** - >95% precision on valid documents
4. **Officers trust AI** - Override rate <20%
5. **No time penalty** - Completion stays ≤22 minutes

### Risks

**Market Risk: Applicants don't understand prompts**
- Evidence: "85% Confident" badge - technical language
- Mitigation: User testing with elderly, low digital literacy, foreign doc holders
- Kill switch: <70% success rate or >22 min completion

**Technical Risk: False positives frustrate users**
- Evidence: AI flags valid photo → infinite upload loop risk
- Mitigation: "My Photo is Correct" escape hatch, human review option
- Kill switch: >30% dismissal rate (indicates AI not accurate enough)

**Team Risk: Officer workload increases**
- Evidence: If 30% request human review → 4,500 weekly reviews
- Mitigation: Track dismissal accuracy, tune confidence thresholds
- Kill switch: Officer override rate >20%

**Implementation Risk: State management**
- Evidence: Dismissed warnings in upload-flow.html reappear in index.html
- Impact: User frustration, infinite loop perception
- Fix required: localStorage or SPA architecture

**Accessibility Risk: Excludes users**
- Evidence: No ARIA labels, keyboard nav, screen reader testing
- Impact: Violates government service accessibility standards
- Fix required: WCAG 2.1 AA compliance before production

---

## 7. In and Out of Scope

### ✅ In Scope (Prototype Demonstrates)
- Progressive validation (one document at a time)
- Immediate feedback after upload
- Clear error messages with references to ICA guidelines
- Mandatory errors vs AI suggestions distinction
- User control (Replace / Keep Original / Request Human Review)
- False positive handling (transparent AI uncertainty)
- ICA corporate visual identity
- Usability testing harness (timer, success tracking)

### ❌ Out of Scope (Not Built)
- Real file upload handling
- Backend API integration
- Real AI model (confidence thresholds, training data)
- Multi-language support (English only)
- Accessibility features (ARIA, keyboard nav, screen readers)
- Mobile responsive design (claimed, not implemented)
- State persistence across pages
- Error recovery (network failures, validation timeouts)
- Analytics (track dismissal rates, drop-off points)
- Integration with existing ICA systems
- Authentication / NRIC validation
- Payment processing
- Email notifications
- Application draft saving

### 🤷 Unclear / Need Decision
- State management approach (SPA vs MPA with session storage)
- How many warnings before user loses trust?
- What happens if AI unavailable? (Fallback messaging)
- Officer review SLA (1-2 days acceptable?)
- Dismissal feedback loop (track accuracy to improve AI)

---

## 8. Engineering Recommendation

### Assessment: High-Fidelity Mockup, Not a Codebase

**What This Is:**
- Clickable prototype for user testing and stakeholder alignment
- Validates UX concept and interaction patterns
- Demonstrates edge cases (false positives, multiple errors)
- Perfect for its intended purpose

**What This Is Not:**
- Production-ready code
- Starting point for implementation
- System with real validation logic

### Conversion to Production: 6-8 Week Estimate

**Phase 1: Foundation (2-3 weeks)**
1. Real file uploads - `<input type="file">`, validation, preview
2. State management - React Context / Redux or session storage
3. API integration - Upload, validate, submit endpoints
4. Error boundaries - Network failures, timeouts, service down

**Phase 2: Polish (1-2 weeks)**
5. Accessibility - ARIA labels, keyboard nav, screen reader testing
6. Mobile optimization - Media queries, responsive grid, touch targets
7. Componentization - Extract reusable UploadArea, ErrorCard, ValidationResult

**Phase 3: Hardening (2-3 weeks)**
8. Real AI integration - Confidence thresholds, model serving, fallback
9. Persistence - Resume applications, save drafts, cross-page state
10. Analytics - Track dismissal rates, time-to-complete, drop-off, officer override

**Keep from Prototype:**
- Visual design (ICA identity, colors, layout)
- UX flows (progressive validation, immediate feedback)
- Copy (error messages, button labels, guidance text)
- Test scenarios (false positive handling, usability test structure)

**Rebuild from Scratch:**
- File handling
- State management
- API integration
- Validation logic
- Accessibility
- Mobile responsiveness

### Critical Fixes Before User Testing

**Must Fix:**
1. **State persistence** - Dismissed warnings must NOT reappear on next screen
2. **"85% Confident" language** - Replace with "AI may be uncertain" (plain language)
3. **Mobile layout** - Test side-by-side photo examples on iPhone SE

**Should Fix:**
4. Add error state for slow/failed validation
5. Keyboard navigation for all interactive elements
6. Loading indicators during validation

### Risk Mitigation

**Biggest Risk: Officer Workload**
- If 30% of 15,000 weekly applications request human review = 4,500 reviews
- Monitor dismissal rate in pilot (start with 5% of applications)
- Tune AI confidence thresholds based on dismissal accuracy feedback

**Second Risk: Trust Erosion**
- If AI frequently wrong, users dismiss ALL warnings (including valid ones)
- Track which dismissed warnings were actually correct
- Show users accuracy stats: "9 out of 10 users who fixed this issue passed review"

**Third Risk: State Management**
- Current bug (warnings reappear) will fail user testing immediately
- Must fix before any external testing
- Recommendation: Convert to SPA (React/Vue) OR implement session storage

### Go/No-Go Decision

**Green Light Criteria:**
- ✅ State management fixed (dismissed warnings don't reappear)
- ✅ 70%+ usability test success rate
- ✅ Completion time ≤22 minutes
- ✅ Accessibility basics in place (keyboard nav, ARIA labels)

**Red Flags:**
- ❌ <70% usability success (prompts not clear enough)
- ❌ Completion time >25 minutes (too much friction)
- ❌ Dismissal rate >30% (AI not accurate enough)
- ❌ Call center volume spikes (confusion)

### Recommendation

**Proceed with user testing** - Prototype is fit for purpose (validate assumptions)

**Do NOT proceed to build** until:
1. User testing confirms 70%+ success rate
2. State management approach decided and architected
3. AI model accuracy benchmarked (>95% precision requirement)
4. Engineering team allocated (6-8 weeks full-time)

**This is a research artifact, not a development artifact.** Treat it as specification for what to build, not as code to extend.
