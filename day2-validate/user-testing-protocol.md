# User Testing Protocol

**Goal:** Validate that 70%+ of applicants can understand and fix AI-generated error prompts without support

**Duration:** 1 week (2-3 participants per day)  
**Target:** 10-15 participants minimum  

---

## Participant Recruitment

### Inclusion Criteria (Must have ALL)
- Currently has or will soon need a passport
- Parent or guardian of child under 16 (primary scenario)
- Willing to participate in 30-minute session
- Can attend in person or via video call

### Diversity Requirements
Recruit across all segments to represent real ICA applicants:

| Segment | Count | Why |
|---------|-------|-----|
| Elderly parents (60+) | 3 | Lower digital literacy, may not understand AI |
| Foreign-born parents | 2-3 | May have translated documents, less familiar with ICA |
| Low digital literacy | 2-3 | May struggle with technical language |
| Mobile-only users | 2-3 | 40%+ of applications from phones |
| English as 2nd language | 2-3 | Clarity test for non-native speakers |

**Total: 10-15 participants**

---

## Test Setup

### Materials Needed
- Laptop with test-scenario.html loaded
- Screen recording software (OBS, QuickTime)
- Note-taking template (see below)
- Consent form
- $20 voucher per participant (incentive)

### Test Environment
- Quiet room
- Participant sits in front of laptop
- Facilitator sits to the side (not behind - intimidating)
- Camera records screen + participant's face

---

## Test Script

### Introduction (3 minutes)

**Say to participant:**

> "Thank you for helping us test a new system for ICA passport applications. This is a prototype - it's not real, and nothing you do here will affect your actual passport.
>
> I'm going to show you a screen with some errors, and I want to see if you can fix them. There are no wrong answers - if something is confusing, that's OUR problem, not yours. Please think out loud as you work, and tell me if anything is unclear.
>
> I'm here to observe, not to help. If you get stuck, tell me what you're confused about, but I won't give hints. The test is to see if the system itself is clear enough.
>
> Do you have any questions before we start?"

---

### Scenario Setup (2 minutes)

**Give participant the context:**

> "Imagine you're renewing your child's passport. You've filled out the application form and uploaded documents. Now you're at the review screen, and the system has found 2 errors.
>
> Your goal is to fix the errors and submit the application. I'll start the timer when you begin. There's no rush - work at your own pace and tell me what you're thinking."

**Load test-scenario.html on screen**

---

### Observation (15-20 minutes)

**While participant works, note:**

1. **Time stamps**
   - When they start
   - When they first read each error
   - When they click each button
   - When they finish

2. **Verbal cues**
   - "I don't understand..."
   - "What does this mean?"
   - "Where do I...?"
   - Pauses >10 seconds
   - Frustration expressions

3. **Actions**
   - Do they read the full error message?
   - Do they skip straight to buttons?
   - Do they understand what "Upload Document" will do?
   - Do they hesitate before clicking?

4. **Confusion signals**
   - Re-reading error multiple times
   - Looking at facilitator for help
   - Clicking wrong button then correcting
   - Giving up ("I'd call someone")

---

### Probing Questions (5 minutes)

**After they finish (or give up), ask:**

1. **Comprehension**
   - "What did the first error mean?"
   - "What did 'AI Suggestion 85% Confident' mean to you?"
   - "What would happen if you clicked 'My Photo is Correct'?"

2. **Clarity**
   - "Was anything confusing?"
   - "If you could change the wording, what would you say instead?"
   - "Did you feel confident you were doing the right thing?"

3. **Expectations**
   - "What did you expect to see on this screen?"
   - "Was anything missing that would have helped?"
   - "Would you use this if it were real?"

4. **Technical language test**
   - "What does '85% Confident' mean to you?"
   - If confused: "How would you say it in your own words?"

---

## Success Criteria (Per Participant)

### ✅ PASS (counts toward 70%)
Participant must achieve ALL of these:
- Fixes both errors correctly
- Completes within 22 minutes
- Does NOT ask "What does this mean?" or "How do I fix this?"
- Successfully submits application

### ❌ FAIL (does not count toward 70%)
If ANY of these happen:
- Gets stuck for >2 minutes without progress
- Asks for clarification on what to do
- Makes incorrect fix (e.g., clicks wrong button)
- Takes >22 minutes
- Gives up ("I'd just call the helpline")

### 🤷 PARTIAL (investigate further)
- Completes but expresses confusion
- Fixes errors but takes 20-25 minutes (near limit)
- Understands birth certificate error but confused by photo error
- Completes but says "I'm not sure if I did it right"

---

## Note-Taking Template

```
PARTICIPANT #___
Date: __________
Segment: [ ] Elderly [ ] Foreign-born [ ] Low literacy [ ] Mobile [ ] ESL

START TIME: _____
END TIME: _____
TOTAL: _____ minutes

BIRTH CERTIFICATE ERROR:
- Understood immediately? [ ] Yes [ ] No
- Clicked correct button? [ ] Yes [ ] No
- Time to fix: _____ minutes
- Verbatim confusion quotes: "_________________"

PASSPORT PHOTO ERROR:
- Understood "85% Confident"? [ ] Yes [ ] No [ ] Ignored it
- Understood three options? [ ] Yes [ ] No
- Which button clicked? [ ] Replace [ ] Keep [ ] Review
- Time to decide: _____ minutes
- Verbatim confusion quotes: "_________________"

OVERALL:
Result: [ ] PASS [ ] FAIL [ ] PARTIAL
If FAIL, why: _________________________________
Most confusing element: _______________________
Suggested wording changes: ____________________

POST-TEST QUOTES:
"_____________________________________________"
"_____________________________________________"
"_____________________________________________"
```

---

## Analysis (After All Tests)

### Calculate Success Rate
```
Pass Count: ___ / 10-15 total = ____%

✅ If ≥70%: Concept validated, proceed to production spec
❌ If <70%: Iterate prompts, retest
```

### Pattern Analysis

**Common confusion points:**
- [ ] "85% Confident" - rewrite as "AI may be uncertain"
- [ ] Three-option buttons - users expected only one action
- [ ] Birth certificate explanation - too technical
- [ ] Photo example - not clear what "white background" means
- [ ] Upload button label - didn't know what would happen

**Failure patterns by segment:**
- Elderly: ___% pass (target: 60%+)
- Foreign-born: ___% pass (target: 60%+)
- Low literacy: ___% pass (target: 60%+)
- Mobile: ___% pass (target: 70%+)
- ESL: ___% pass (target: 65%+)

**If one segment fails disproportionately:** Investigate why and adapt for that audience

---

## Iteration Guidelines

### If 70%+ Pass
✅ Move to production spec  
✅ Document learnings for engineering  
✅ Brief three leaders with results  

### If 50-69% Pass (Close)
🔧 Minor iterations needed:
- Simplify specific confusing phrases
- Add one example or visual
- Clarify button labels
- Retest with 5 more participants

### If <50% Pass (Major Issues)
🚨 Major redesign needed:
- Prompts fundamentally unclear
- Wrong validation timing (too late?)
- Need earlier guidance (before upload?)
- Consider alternative approach

---

## Red Flags That Should Stop Testing

**Stop immediately and redesign if:**
1. First 3 participants ALL fail (0% success rate)
2. Participants express frustration or anger
3. Multiple participants say "I'd just call the helpline"
4. Safety concern: Participants uploading wrong documents (e.g., photo ID instead of birth cert)

**Don't waste 10 participants on a broken concept.**

---

## Deliverables

### After Testing Complete

1. **Summary deck (5 slides)**
   - Success rate: X% passed
   - Pass/fail breakdown by segment
   - Top 3 confusion points
   - Recommendation: Go / Iterate / Stop

2. **Video clips (3-5 minutes)**
   - Best success example
   - Worst failure example
   - Quote montage of confusion

3. **Prompt changes doc**
   - Before/after wording
   - Rationale for each change

4. **Stakeholder brief**
   - Present to three leaders
   - Get approval to proceed (if passed)
   - Get budget for iteration (if failed)

---

## Ethical Considerations

### Informed Consent
- Participants know this is a test, not real
- Screen recorded with permission
- Can stop anytime
- Data anonymized in reports

### Data Protection
- No real NRIC or passport data used
- Synthetic names only (Tan Wei Lin, etc.)
- Videos stored securely, deleted after 6 months
- Quotes anonymized (no names in reports)

### Compensation
- $20 voucher for 30 minutes
- Given regardless of pass/fail
- Not contingent on "good" performance

---

## Timeline

| Day | Activity | Notes |
|-----|----------|-------|
| Mon | Recruit 2 elderly participants | Test in afternoon |
| Tue | Recruit 2 foreign-born | Test in afternoon |
| Wed | Recruit 3 low literacy / ESL | Test throughout day |
| Thu | Recruit 3 mobile users | Test on phones |
| Fri | Analyze results, write summary | Present Monday |

**Week 2:** Iterate and retest if <70% passed

---

**This protocol ensures we validate the riskiest assumption before wasting engineering time.**
