# What's Unclear or Missing for Independent Evaluation

**Perspective:** Lead Engineer running eval next month without original author

---

## CRITICAL MISSING: How to Actually Run the Agent

**Gap 1: No technical setup instructions**
- How do I invoke the agent? (Command line? API call? Web interface?)
- What environment is needed? (Python? Node? Claude API?)
- What dependencies must be installed?
- Where do I run it from? (local machine? server?)

**Gap 2: Input format not specified**
- Test cases show JSON, but how do I feed this to the agent?
- Do I paste JSON into a prompt?
- Is there a script that runs all 15 cases?
- How do I simulate "user selects option" (Case 9: user clicks "My Photo is Correct")?

**Gap 3: How to interpret agent output**
- Agent outputs shown in eval-results.md, but how do I get this output?
- Is it text response? JSON? UI mockup?
- How do I know if "submission button" is enabled/disabled?
- How do I see "behind the scenes" (officer queue, silent flags)?

---

## MISSING: Evaluation Process Details

**Gap 4: Agent invocation not documented**
- Rubric says "Run agent on that input" - but HOW?
- Do I use day3-build/CLAUDE.md as a prompt?
- Do I copy the JSON input into Claude chat?
- Is there an evaluation harness/script?

**Gap 5: Test case execution unclear**
- Case 9 requires user interaction ("User selects: My Photo is Correct")
- How do I simulate this? Type it as follow-up message?
- Multi-step cases not documented (upload → fix → re-validate)

**Gap 6: "Behind the scenes" not observable**
- Rubric checks for: silently flagged, officer queue, no PII logged
- How do I verify these if they're not shown to user?
- Do I check logs? Database? System output?

---

## MISSING: Calibration Process

**Gap 7: Number of reviewers not specified**
- Calibration says "3+ reviewers" but is this required every time?
- For weekly/monthly eval, do I need 3 people or can 1 do it?
- How to handle if only 1 reviewer available?

**Gap 8: Inter-rater reliability target missing**
- Calibration says "discuss discrepancies until agreement"
- What if reviewers still disagree? Majority vote? Subject matter expert decides?
- No quantitative target (e.g., "Cohen's Kappa > 0.8")

**Gap 9: When to re-calibrate not defined**
- Do anchor cases need to be scored every evaluation?
- Or only when new reviewers join?
- After agent updates?

---

## MISSING: Results Management

**Gap 10: Where to record results**
- Rubric mentions "spreadsheet" but no template provided
- What columns? What format?
- Where is it stored? (Google Sheets? Excel? Database?)

**Gap 11: Results comparison not specified**
- How do I compare this month's results to last month's?
- Is regression a fail? (If agent scored 15/15 last time, 14/15 this time)
- Who decides if changes are acceptable?

**Gap 12: Reporting not defined**
- Who gets the results? (Director? Product Lead?)
- What format? (Email? Report? Dashboard?)
- What's the SLA? (Results within 24 hours? 1 week?)

---

## MISSING: Edge Case Handling

**Gap 13: Partial failures not addressed**
- What if agent crashes mid-evaluation?
- What if agent gives ambiguous output (not clear if blocked or not)?
- What if agent output doesn't match any example in rubric?

**Gap 14: Scoring disagreement resolution**
- D5 is subjective (1-5 scale) - what if I score 3 and another reviewer scores 5?
- Who decides final score?
- Is there a tiebreaker process?

**Gap 15: Version control not documented**
- What if CLAUDE.md changes between evaluations?
- Do I use latest version or lock to specific version?
- How do I know which version was used last time?

---

## MISSING: Practical Logistics

**Gap 16: Time estimate missing**
- How long does full 15-case evaluation take?
- 1 hour? 4 hours? Full day?
- Need to plan capacity

**Gap 17: Prerequisites not listed**
- What knowledge/skills does reviewer need?
- Domain knowledge (ICA passport process)?
- Technical skills (read JSON, run Python)?
- Language proficiency (Primary 6 English level understanding)?

**Gap 18: Failure escalation not defined**
- Deal-breaker failed → "DO NOT LAUNCH" → but who do I tell?
- Is there an incident process?
- Do I stop immediately or finish all 15 cases?

---

## MISSING: Maintenance

**Gap 19: When to update test cases**
- If ICA policy changes (e.g., now allows glasses for medical reasons)
- Who updates eval-cases.md?
- How often to review test cases?

**Gap 20: Agent update triggers**
- After fixing bug, do I re-run full 15 cases or just failed case?
- What's the regression testing protocol?
- When is partial re-eval OK vs full re-eval required?

---

## Summary: Top 5 Blockers for Independent Execution

1. **HOW TO RUN THE AGENT** - No technical setup or invocation instructions
2. **INPUT FORMAT** - How to feed JSON test cases to agent
3. **OUTPUT INTERPRETATION** - How to extract Pass/Fail from agent response
4. **BEHIND THE SCENES VERIFICATION** - How to check silent flags, PII logging
5. **RESULTS RECORDING** - Where to put scores, no template provided

Without fixing these 5, engineer cannot run evaluation next month.
