# Prototype - Day 2: Validate

Scenario: B · ICA Passport Applications

## Files

- **index.html** - Static preview mockup showing the application review screen with 2 flagged errors
- **test-scenario.html** - Interactive usability test scenario with timer and click tracking
- **styles.css** - Shared styles (ICA corporate identity)

## Testing the Riskiest Assumption

**Assumption:** Applicants can understand and act on AI-generated error prompts to correct their submissions accurately, without increasing completion time or requiring additional support.

### How to Test

1. Open `test-scenario.html` in a browser
2. Give the participant the scenario context (they are a parent renewing a child's passport)
3. Ask them to "fix the errors and submit the application"
4. Observe:
   - Do they understand what went wrong?
   - Do they know what action to take?
   - Can they complete it without asking for help?
   - How long does it take?

### Success Criteria

- ✅ Participant fixes both errors correctly
- ✅ Completes within 22 minutes (baseline)
- ✅ No questions or confusion about what to do

### Kill Switch

If 3+ out of 10 participants:
- Get stuck and don't know what to do
- Ask "what does this mean?" or "how do I fix this?"
- Take longer than 22 minutes
- Make incorrect fixes

→ The error prompts are not clear enough. Pivot to clearer language or earlier validation.

## Made-up Test Data

All data in these prototypes is synthetic:
- **Applicant:** Tan Wei Lin (child, DOB: 15 Mar 2018)
- **Parent/Guardian:** Tan Ah Kow
- **Errors:** Missing birth certificate, incorrect photo background
- **Baseline time:** 22 minutes (current average completion time)

No real citizen data is used in testing.
