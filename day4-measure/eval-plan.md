# Evaluation Plan - ICA Passport Application Validator

**Scenario:** B · ICA Passport Applications  
**Agent:** day3-build/CLAUDE.md  
**Test Cases:** day4-measure/eval-cases.md (15 cases)  
**Rubric:** day4-measure/human-rubric.md  
**All marked PROPOSED - awaiting approval**

---

## Who Owns What

**All roles use job titles, not names. Ownership marked PROPOSED.**

### Who Signs the Pass Bar

**Director of Passport Services** - PROPOSED
- Signs off on pass/fail decision for agent launch
- Approves: "Agent ready for pilot" OR "Agent failed, do not launch"
- Receives: Evaluation results within 24 hours of completion
- Escalation path: If evaluation fails deal-breaker (D1, D2, D3) → Immediate notification
- Final authority: Go/no-go for pilot launch after evaluation passes

**Why:** Business owner accountable for agent outcomes, must approve quality before exposing to citizens

---

### Who Writes and Refreshes the Test Cases

**Primary: ML Lead** - PROPOSED
- Writes initial 15 test cases (eval-cases.md)
- Updates test cases when:
  - ICA policy changes (e.g., new photo requirements)
  - Agent behavior changes (new confidence thresholds)
  - New edge case discovered in production (fraud pattern, special character issue)
  - Deal-breaker case added/removed

**Contributors:** - PROPOSED
- **Product Lead** - Reviews test case clarity (messages understandable?)
- **Operations Manager** - Suggests cases based on officer feedback (common errors)
- **UX Team** - Validates test case messages match approved templates
- **Legal Team** - Confirms test cases reflect current ICA policy

**Approval process:** - PROPOSED
- ML Lead drafts new/updated test cases
- Product Lead approves (ensures business value)
- Director signs off if deal-breaker cases change (affects launch criteria)

**Refresh cadence:** - PROPOSED
- **Quarterly review** (every 3 months): Are test cases still relevant?
- **After policy change**: Update within 1 week of ICA requirement change
- **After production incident**: Add test case for root cause within 2 weeks
- **Annually**: Full test suite review (retire obsolete cases, add emerging patterns)

---

### Who Runs the Eval

**Lead Engineer (Quality Assurance)** - PROPOSED
- Executes 15-case evaluation using human-rubric.md
- Scores each response: D1-D5 dimensions
- Records results in Google Sheets (see rubric for template)
- Flags deal-breaker failures immediately (within 1 hour)
- Completes evaluation report within 24 hours

**Backup/rotation:** - PROPOSED
- **Senior Engineer** - Runs eval if Lead Engineer unavailable
- **ML Engineer** - Can run evaluation for urgent/off-hours needs
- At least 2 people trained and calibrated on rubric

**Calibration requirement:** - PROPOSED
- Lead Engineer must complete calibration (5 anchor cases) before first evaluation
- Re-calibrate quarterly OR after major rubric changes
- Inter-rater reliability: 100% agreement on D1-D4, ±1 point on D5

---

### Who Reviews a Weekly Sample

**Operations Manager** - PROPOSED
- Reviews 5 anchor cases weekly (Cases 1, 2, 4, 11, 15)
- Time: ~1 hour per week
- Focus: Deal-breaker dimensions only (D1, D2, D3)
- Flags regression if any anchor fails
- Reports to Product Lead: "Week [X]: All anchors passed" OR "Regression detected on Case [Y]"

**Why weekly sample, not full eval:**
- Full 15 cases = 3-4 hours (expensive for weekly)
- 5 anchors cover all deal-breakers + common scenarios
- Catches regressions quickly without full evaluation overhead
- Weekly rhythm keeps quality top of mind

**Escalation from weekly sample:** - PROPOSED
- If anchor fails → Trigger full 15-case evaluation within 48 hours (Lead Engineer)
- If deal-breaker fails → Immediate escalation to Product Lead + ML Lead
- If 2+ anchors fail → Emergency meeting with Director

---

## When It Runs

**All triggers marked PROPOSED.**

### Before Go-Live (Gate: Pilot Launch) - PROPOSED

**Trigger:** Agent ready for first pilot with real citizens  
**Who:** Lead Engineer  
**Scope:** Full 15 cases  
**Pass criteria:** 15/15 pass, 0 deal-breaker failures, D5 ≥4.0 average  
**Time:** 3-4 hours  
**Next step if pass:** Director approves pilot launch (5% of applications)  
**Next step if fail:** ML Lead fixes, re-run full eval

---

### Before Every Change (Gate: Deployment) - PROPOSED

**Trigger:** ANY change to day3-build/CLAUDE.md  
**Who:** Lead Engineer  
**Scope:** Full 15 cases  
**Pass criteria:** 15/15 pass, 0 deal-breaker failures, no regression from previous eval  
**Time:** 3-4 hours  
**Blocker:** Cannot deploy until evaluation passes  
**Exception:** Emergency hotfix (e.g., PII leak) - deploy immediately, eval within 24 hours after

**What counts as "change":**
- Instruction changes (new stop condition, updated thresholds)
- Tool changes (new validation check)
- Context changes (document requirements updated)
- Model changes (GPT-4 → Claude, confidence thresholds adjusted)

**What does NOT trigger eval:**
- Comment changes only
- Formatting changes (whitespace, markdown)
- Log changes (analytics tracking)
- Non-functional changes (variable names)

**Lightweight changes:** - PROPOSED
- If change affects <5 lines AND only 1 test case → Re-run that case + 5 anchors (not full 15)
- Example: Fix typo in one error message → Re-run Case 2 (missing doc) + 5 anchors
- Still requires pass before deployment

---

### Every Week (In Pilot/Production) - PROPOSED

**Trigger:** Weekly schedule (e.g., every Monday 2pm)  
**Who:** Operations Manager  
**Scope:** 5 anchor cases only (Cases 1, 2, 4, 11, 15)  
**Pass criteria:** 5/5 pass on deal-breaker dimensions (D1, D2, D3)  
**Time:** ~1 hour  
**Purpose:** Early regression detection, quality monitoring  
**Next step if pass:** Log in weekly report, continue  
**Next step if fail:** Trigger full 15-case eval within 48 hours

**Weekly report format:** - PROPOSED
```
Week of: [Date]
Anchor results: 5/5 passed ✅
Completion time: 52 minutes
Issues: None
Recommendation: Continue monitoring
```

---

### When Policy Changes (Gate: Policy Update) - PROPOSED

**Trigger:** ICA changes passport requirements (photo specs, document list, name rules)  
**Who:** ML Lead updates test cases → Lead Engineer runs eval  
**Scope:** Full 15 cases (test cases updated first)  
**Pass criteria:** 15/15 pass with NEW policy requirements  
**Time:** Test case update (1-2 days) + evaluation (3-4 hours)  
**Blocker:** Cannot apply policy change to agent until evaluation passes

**Examples of policy changes:**
- ICA now allows glasses for medical reasons → Update Case 8 (fake policy becomes real policy)
- Photo background RGB tolerance widened to 245-255 → Update Cases 3, 4 (confidence thresholds)
- New mandatory document added (parental consent form) → Add new test case
- Religious headwear policy clarified → Update Case 15 expectations

**Process:** - PROPOSED
1. Legal Team notifies Product Lead of policy change
2. Product Lead assigns ML Lead: Update test cases within 1 week
3. ML Lead updates eval-cases.md, submits for approval
4. Product Lead approves updated test cases
5. ML Lead updates CLAUDE.md to reflect new policy
6. Lead Engineer runs full 15-case eval with updated cases
7. If passes → Deploy policy update
8. If fails → ML Lead fixes agent, re-run eval

---

### Monthly (In Production) - PROPOSED

**Trigger:** First Monday of each month  
**Who:** Lead Engineer  
**Scope:** Full 15 cases  
**Pass criteria:** 15/15 pass, no regression from previous month, D5 ≥4.0 average  
**Time:** 3-4 hours  
**Purpose:** Comprehensive quality check, trend analysis  
**Report to:** Product Lead, Director (monthly quality report)

**Monthly report includes:** - PROPOSED
- Pass/fail status: 15/15 vs previous month
- D5 clarity trend: 4.9 this month vs 4.8 last month (improving)
- Regression check: Any previously-passing cases now failing?
- Production correlation: Are eval scores matching officer override rate?
- Recommendations: Test cases to add, agent improvements needed

---

### Quarterly (Every 3 Months) - PROPOSED

**Trigger:** End of Q1, Q2, Q3, Q4  
**Who:** Lead Engineer + 2 other engineers (full calibration)  
**Scope:** Full 15 cases + calibration session  
**Pass criteria:** 15/15 pass, inter-rater reliability 100% on D1-D4  
**Time:** Calibration (2 hours) + Evaluation (3-4 hours) = ~6 hours  
**Purpose:** Inter-rater reliability check, test case review, rubric refinement

**Quarterly activities:** - PROPOSED
1. **Calibration session:** 3 engineers score 5 anchors independently, discuss discrepancies
2. **Full evaluation:** Lead Engineer runs 15 cases
3. **Test case review:** ML Lead + Product Lead review all 15 cases (retire obsolete, add emerging patterns)
4. **Rubric review:** Update human-rubric.md if scoring criteria unclear
5. **Trend analysis:** Compare Q1→Q2→Q3→Q4 (is quality improving? degrading?)

---

## How I'll Know It's Working

**All metrics marked PROPOSED - awaiting approval.**

### The Goal

**Primary goal:** Ensure agent maintains quality standards (15/15 pass) so citizens receive correct validation prompts and officers trust agent decisions.

**Secondary goals:**
1. **Catch regressions early** - Weekly anchors detect issues before citizens impacted
2. **Prevent bad deployments** - Block changes that fail evaluation
3. **Maintain trust** - Officers and citizens trust agent when quality consistently high
4. **Enable continuous improvement** - Evaluation results guide agent enhancements

---

### The Baseline

**From day4-measure/eval-results.md (19 Nov 2026):**

**Evaluation scores:**
- Overall: 15/15 cases passed (100%)
- Deal-breaker failures: 0
- D1 (Correctness): 6/6 tested = 100%
- D2 (User Control): 4/4 tested = 100%
- D3 (Cultural Sensitivity): 1/1 tested = 100%
- D4 (Escalation): 4/4 tested = 100%
- D5 (Message Clarity): 4.93/5 average

**This is the baseline quality standard.** Future evaluations compared to this.

**Production baseline (current state, before agent):**
- Error rate: 17% (2,550 errors per 15,000 weekly applications)
- Officer review: 100% of applications (15,000/week manually reviewed)
- Self-service rate: 62% (38% call support for questions)
- Completion time: 22 minutes average

---

### The Target

**Evaluation quality target:** - PROPOSED

| Metric | Target | Acceptable Range | Stop Line |
|--------|--------|------------------|-----------|
| **Overall pass rate** | 15/15 (100%) | 14/15 if non-deal-breaker | <14/15 OR any deal-breaker fail |
| **D1 (Correctness)** | 100% pass | 100% (no exceptions) | <100% = STOP |
| **D2 (User Control)** | 100% pass | 100% (no exceptions) | <100% = STOP |
| **D3 (Religious Headwear)** | 100% pass | 100% (no exceptions) | <100% = STOP |
| **D4 (Escalation)** | 100% pass | ≥90% (max 1-2 fails) | <90% = STOP |
| **D5 (Message Clarity)** | ≥4.5 average | ≥4.0 average | <4.0 = STOP |
| **Regression** | 0 new failures | 0 new failures | >0 new deal-breaker fail = STOP |

**Production impact target (after agent launch):** - PROPOSED

| Metric | Baseline (Before) | Target (After) | Review Date |
|--------|-------------------|----------------|-------------|
| **Error rate** | 17% | ≤8.5% (50% reduction) | Month 3 of pilot |
| **Self-service rate** | 62% | ≥70% (fix without calling support) | Month 1 of pilot |
| **Officer review volume** | 15,000/week (100%) | 2,250/week (15%) | Month 3 of pilot |
| **Completion time** | 22 minutes | ≤22 minutes (no increase) | Month 1 of pilot |
| **Officer override rate** | N/A (new metric) | <20% | Month 1 of pilot |

---

### Review Date

**Evaluation plan review cadence:** - PROPOSED

**Month 1 (After pilot launch):**
- Review daily for first week (Operations Manager checks dashboard)
- Weekly anchor evaluation (Operations Manager)
- Full evaluation if any issues detected

**Month 3 (Pilot mid-point):**
- Full evaluation (Lead Engineer)
- Compare production metrics to targets:
  - Error rate: Is it ≤8.5%? (Target: 50% reduction)
  - Self-service: Is it ≥70%? (Target: 70%+ fix without support)
  - Officer override: Is it <20%? (Target: Officers trust agent)
- Correlation check: Do evaluation scores predict production outcomes?
- Decision: Continue pilot? Expand to 10%? Roll back?

**Month 6 (End of pilot):**
- Full evaluation (Lead Engineer)
- Quarterly calibration (3 engineers)
- Final pilot report to Director:
  - All targets met? (Error rate, self-service, override rate)
  - Evaluation quality maintained? (Still 15/15?)
  - Recommendation: Launch to 100% OR iterate OR discontinue
- Go/no-go decision for full production launch

**Ongoing (Production):**
- Weekly: Anchor evaluation (5 cases, Operations Manager)
- Monthly: Full evaluation (15 cases, Lead Engineer)
- Quarterly: Calibration + full evaluation + test case review
- Annually: Full evaluation plan review (update targets, refine rubric)

---

### Usage Metric

**Primary metric: Evaluation-to-production correlation** - PROPOSED

**What we're measuring:** Do high evaluation scores predict good production outcomes?

**How we measure:**

| Evaluation Score | Expected Production Behavior | Measurement |
|------------------|------------------------------|-------------|
| **15/15 eval pass** | <20% officer override rate | Check weekly: Is override rate <20%? |
| **D5 ≥4.5 (clarity)** | <10% support call increase | Check monthly: Did support calls go up? |
| **D2 100% (user control)** | <30% dismissal rate | Check weekly: Are users dismissing >30% of warnings? |
| **D4 100% (escalation)** | <20% escalation rate | Check weekly: Is agent escalating <20% of cases? |

**Correlation check (Monthly):** - PROPOSED

```
Evaluation Score    Production Outcome    Correlation
D5 = 4.9 (Nov)  →   Support calls +5%  =  ✅ Predicts well (clarity working)
D2 = 100% (Nov) →   Dismissal rate 18% =  ✅ Predicts well (user control working)
D4 = 100% (Nov) →   Escalation rate 22%=  ⚠️ Higher than expected (investigate)
```

**If correlation breaks down:** - PROPOSED
- Evaluation says: D5 = 4.8 (good clarity)
- Production says: Support calls +25% (users confused)
- **Action:** Test cases may not represent real user confusion → Update test cases
- **Owner:** ML Lead investigates gap, proposes new test cases

**Secondary metrics:** - PROPOSED
1. **Evaluation completion time:** Target <4 hours for full eval (efficiency)
2. **Regression detection rate:** % of regressions caught by weekly anchors before production impact
3. **False negative rate:** Production incidents NOT caught by evaluation (goal: 0)
4. **Test case coverage:** % of production errors covered by test cases (goal: >90%)

---

### The Pattern to Watch First

**In first 30 days of pilot, watch for these patterns:** - PROPOSED

#### Pattern 1: Dismissal Spike (User Control Issue)

**What it looks like:**
- Evaluation: D2 = 100% (user control passed)
- Production Week 1: Dismissal rate 15% (good)
- Production Week 2: Dismissal rate 28% (warning)
- Production Week 3: Dismissal rate 35% (STOP LINE crossed)

**What it means:** Agent showing too many warnings, users ignoring them

**Action:**
- Operations Manager flags to Product Lead immediately
- ML Lead investigates: Which warnings dismissed? (Photo? Name?)
- Hypothesis: Confidence thresholds too low (86% showing warnings on valid photos)
- Solution: Raise threshold 86% → 90%, re-run evaluation, deploy if passes
- Follow-up: Check dismissal rate Week 4 (expect <30%)

**Why watch this first:** Dismissal >30% = Kill switch trigger (Decision #5 in operating-model.md)

---

#### Pattern 2: Officer Override Spike (Correctness Issue)

**What it looks like:**
- Evaluation: D1 = 100% (correctness passed)
- Production Week 1: Override rate 12% (good)
- Production Week 2: Override rate 18% (warning)
- Production Week 3: Override rate 23% (STOP LINE crossed)

**What it means:** Officers don't trust agent decisions, re-validating manually

**Action:**
- Operations Manager flags to Product Lead immediately
- Operations Manager surveys officers: Why overriding? (False positives? Unclear warnings?)
- ML Lead checks override cases: Agent said X, officer said Y (who's right?)
- If agent wrong: Update CLAUDE.md, re-run evaluation
- If officer wrong: Officer training needed, not agent issue

**Why watch this first:** Override >20% = Officers don't trust agent, defeats 85% workload reduction goal

---

#### Pattern 3: Support Call Surge (Clarity Issue)

**What it looks like:**
- Evaluation: D5 = 4.9 (excellent clarity)
- Production Week 1: Support calls +5% (acceptable)
- Production Week 2: Support calls +12% (warning)
- Production Week 3: Support calls +20% (STOP LINE crossed)

**What it means:** Users confused by agent messages, calling support for help

**Action:**
- Customer Support logs common questions: "What does 'soft warning' mean?" "Do I HAVE to replace photo?"
- UX Team analyzes: Which messages confusing? (Strong warning 3-checkbox flow? Silent flag explanation?)
- Product Lead approves new error message templates
- ML Lead updates CLAUDE.md, re-run evaluation (focus on D5 scores)

**Why watch this first:** Support call increase >10% = Agent fails self-service goal (70%+ fix without calling)

---

#### Pattern 4: Evaluation-Production Mismatch (Coverage Issue)

**What it looks like:**
- Evaluation: 15/15 pass (all test cases)
- Production: New error pattern emerges (not in test cases)
- Example: Users uploading scanned docs with watermarks → Agent doesn't handle this → Officers see 50 cases/week

**What it means:** Test cases don't cover all real-world scenarios

**Action:**
- Operations Manager reports pattern to ML Lead: "50 watermarked docs/week, no test case"
- ML Lead adds Case 16: Document with watermark
- Expected behavior: Agent detects watermark → Escalates to officer OR Validates if acceptable
- Lead Engineer runs Case 16 → If fails, ML Lead fixes CLAUDE.md
- Deploy fix, monitor for 2 weeks

**Why watch this first:** Gaps in test coverage = Agent fails on real cases not predicted by evaluation

---

### The Stop Line

**When to immediately stop using agent (emergency shutdown):** - PROPOSED

#### Evaluation Stop Lines (Block deployment)

| Condition | Stop Line | Action | Authority |
|-----------|-----------|--------|-----------|
| **Deal-breaker failure** | ANY D1, D2, or D3 fail | Stop eval, email Director/Product Lead/ML Lead within 1 hour, do not deploy | Lead Engineer |
| **Regression** | Previously-passing case now fails | Full evaluation required, do not deploy until fixed | Lead Engineer |
| **Clarity degradation** | D5 average <4.0 | Rewrite error messages, re-run eval, do not deploy | Product Lead |
| **Multiple failures** | ≥3 cases fail (any dimension) | Root cause meeting, do not deploy | Director |

#### Production Stop Lines (Activate killswitch)

| Condition | Stop Line | Killswitch Level | Authority |
|-----------|-----------|------------------|-----------|
| **Dismissal spike** | >30% users dismiss warnings | Level 1: Disable AI warnings | Operations Manager |
| **Officer override spike** | >20% officers override agent | Level 1: Disable AI warnings | Operations Manager |
| **Support call surge** | >10% increase in support calls | Level 2: Maintenance mode | Product Lead |
| **Wrong answer cluster** | ≥5 incorrect validations in 1 hour | Level 1: Feature flag off | Operations Manager |
| **PII exposure** | Agent shows real NRIC/passport numbers | Level 2: Maintenance mode immediately | CISO |
| **Discrimination incident** | Agent rejects religious headwear (Case 15 failure in production) | Level 3: Emergency shutdown | Director |
| **Public/media incident** | News coverage, social media viral, Minister escalation | Level 3: Emergency shutdown | Director |

**Automatic triggers (no human judgment):** - PROPOSED
- Dismissal rate >30% → Auto-flag to Operations Manager (dashboard alert)
- Override rate >20% → Auto-flag to Operations Manager (dashboard alert)
- ≥5 identical errors in 1 hour → Auto-flag to Operations Manager (monitoring alert)

**Human judgment required:** - PROPOSED
- Support call increase (Customer Support confirms pattern, not coincidence)
- Wrong answer assessment (Officer verifies agent was actually wrong)
- PII exposure (CISO confirms real data vs synthetic test data)

---

## First 30 Days

**Day-by-day plan for pilot launch, marked PROPOSED.**

### Pre-Launch (Days -7 to 0)

**Day -7: Final Evaluation** - PROPOSED
- **Who:** Lead Engineer
- **What:** Run full 15-case evaluation
- **Pass criteria:** 15/15 pass, 0 deal-breaker failures
- **Output:** Evaluation report to Director
- **Decision:** Director signs off "Ready for pilot" or "Not ready, fix first"

**Day -5: Monitoring Setup** - PROPOSED
- **Who:** Engineering Lead
- **What:** Dashboard live with metrics (dismissal rate, override rate, escalation rate)
- **Test:** Synthetic data run-through, alerts firing correctly
- **Handoff:** Operations Manager trained on dashboard, knows when to activate killswitch

**Day -3: Officer Briefing** - PROPOSED
- **Who:** Operations Manager
- **What:** Brief 50 officers on pilot (5% of applications = ~750 cases/week)
- **Key messages:**
  - Agent validates, you review flagged cases only
  - Override if agent is wrong (no penalty)
  - Report patterns to Operations Manager
- **Expectation:** <20% override rate = agent working well

**Day -1: Stakeholder Notification** - PROPOSED
- **Who:** Director
- **What:** Email to Minister's Office, Customer Support, ICA Officers
- **Message:** "Pilot launches tomorrow, 5% of applications, synthetic data validation passed 15/15"
- **Escalation path:** Operations Manager → Director → Minister if issues

**Day 0: Pilot Launch** - PROPOSED
- **Who:** Engineering Lead deploys, Operations Manager monitors
- **What:** Enable agent for 5% of applications (random selection)
- **Monitor:** Dashboard every 30 minutes for first 4 hours
- **Baseline:** Record Day 0 metrics (dismissal rate, override rate, support calls)

---

### Week 1 (Days 1-7): Intense Monitoring - PROPOSED

**Daily activities:**

**Operations Manager (Every day, Week 1):**
- Check dashboard 3x per day (9am, 2pm, 5pm)
- Review metrics:
  - Dismissal rate: Expect 15-25%, flag if >30%
  - Override rate: Expect 10-20%, flag if >20%
  - Escalation rate: Expect 15-20%, flag if >25%
  - Support calls: Baseline +5% OK, flag if >10%
- Log daily in "Pilot Week 1 Report"
- Meet with Product Lead end of each day (15 min): Any issues?

**Lead Engineer (Day 3):**
- Run 5 anchor cases (Cases 1, 2, 4, 11, 15)
- Verify no regression from Day -7 evaluation
- Time: 1 hour
- Report to Product Lead: "Day 3 anchor check: 5/5 passed" OR "Regression detected"

**Lead Engineer (Day 7):**
- Run 5 anchor cases again
- Compare to Day 3: Any changes?
- Report to Product Lead + Director (Week 1 summary)

**Customer Support (Week 1):**
- Log all support calls mentioning "passport" or "application"
- Categorize: "Confused by agent message" vs "Other"
- Report to Product Lead end of Week 1: Call volume change, common questions

**ICA Officers (Week 1):**
- Review flagged applications as usual
- Log override reasons: "Agent wrong" vs "Agent right, policy exception"
- Survey (end of Week 1): Do you trust agent decisions? Would you recommend expanding pilot?

---

### Week 2 (Days 8-14): Pattern Detection - PROPOSED

**Operations Manager (Day 8):**
- Compare Week 1 metrics to baseline:
  - Dismissal rate: Trend up or down? Stable?
  - Override rate: Within <20% target?
  - Support calls: Within +5-10% acceptable range?
- **If ANY metric outside acceptable range:** Trigger Pattern Investigation

**Pattern Investigation (if triggered):**
- **Who:** Operations Manager + ML Lead + Product Lead
- **When:** Within 24 hours of trigger
- **What:** 
  1. Identify pattern (which cases? which warnings?)
  2. Root cause (agent wrong? Users confused? Officers overcautious?)
  3. Solution (update CLAUDE.md? Retrain? Officer training?)
  4. Timeline (fix in Week 2? Week 3?)
- **Output:** Action plan to Director

**Lead Engineer (Day 14):**
- Run 5 anchor cases (end of Week 2)
- Compare to Day 7: Any regression?
- Report to Product Lead

**ML Lead (Week 2):**
- Analyze dismissed warnings: Which cases? (Photo? Name? Multiple docs?)
- Hypothesis: Are confidence thresholds right?
- Recommendation: Keep current thresholds? Adjust?
- Present to Product Lead end of Week 2

---

### Week 3 (Days 15-21): Adjustment - PROPOSED

**Product Lead (Day 15 decision):**
- Review Week 2 data + ML Lead recommendations
- Decision: 
  - **If metrics within targets:** Continue pilot as-is
  - **If one metric outside target:** Adjust agent (update CLAUDE.md), re-run anchor evaluation, deploy
  - **If multiple metrics outside targets:** Pause pilot, full 15-case eval, fix, restart

**If adjustments made:**
- **ML Lead:** Update CLAUDE.md (e.g., raise confidence threshold 86% → 90%)
- **Lead Engineer:** Run 5 anchor cases + cases related to adjustment
- **Engineering Lead:** Deploy if evaluation passes
- **Operations Manager:** Monitor for 48 hours (check if adjustment worked)

**Lead Engineer (Day 21):**
- Run 5 anchor cases (end of Week 3)
- Compare to Day 14: Adjustment working?
- Report to Product Lead + Director

**Week 3 key question:** "Are we on track for targets?" (Error rate reducing? Self-service improving?)

---

### Week 4 (Days 22-30): Stabilization - PROPOSED

**Operations Manager (Week 4):**
- Reduce monitoring frequency: Dashboard 1x per day (instead of 3x)
- Continue logging daily metrics
- Prepare Month 1 Report (due Day 30)

**Lead Engineer (Day 28):**
- Run 5 anchor cases (end of Week 4)
- Compare to Week 3: Stable? Improving? Regressing?
- Report to Product Lead

**Director (Day 30 decision):**
- Review Month 1 Report:
  - All metrics within acceptable range?
  - Evaluation quality maintained (5/5 anchors passing)?
  - Officer feedback positive (trust agent, recommend expansion)?
  - Citizen feedback positive (no complaints, faster processing)?
- **Decision:**
  - **Expand to 10%** (if all targets met, evaluation stable)
  - **Continue at 5%** (if one metric borderline, need more data)
  - **Pause/rollback** (if multiple targets missed, evaluation regression, negative feedback)

**Month 1 Report contents:** - PROPOSED
```
Month 1 Pilot Report - ICA Passport Validator

Metrics:
- Applications processed: 3,000 (5% of 60,000 monthly)
- Error rate: 14% (baseline 17%) - ON TRACK (target ≤8.5% by Month 3)
- Self-service rate: 68% (baseline 62%) - ON TRACK (target ≥70%)
- Officer override rate: 16% - WITHIN TARGET (<20%)
- Support call change: +7% - WITHIN TARGET (<10%)
- Completion time: 21 minutes (baseline 22 minutes) - WITHIN TARGET (≤22)

Evaluation quality:
- Weekly anchors: 4 weeks, 20 anchor evaluations, 20/20 passed (100%)
- No regressions detected
- D5 clarity stable: 4.9 average

Incidents:
- 0 killswitch activations
- 0 PII exposures
- 0 discrimination incidents
- 2 false positives (users overrode 94% warnings, officers confirmed photos valid)

Officer feedback:
- Survey: 42/50 officers trust agent (84%)
- Common feedback: "Agent catches missing docs well, photo warnings sometimes wrong"
- Recommendation: 38/50 would expand pilot (76%)

Citizen feedback:
- 0 formal complaints
- Informal: "Faster than before" (via support calls)

Recommendation: EXPAND TO 10%
Rationale: All metrics on track, no incidents, positive feedback, evaluation stable
```

---

## Summary

**This evaluation plan defines:** - PROPOSED

✅ **Who owns what:**
- Director signs pass bar
- ML Lead writes/refreshes test cases
- Lead Engineer runs eval
- Operations Manager reviews weekly sample

✅ **When it runs:**
- Before go-live (full 15)
- Before every CLAUDE.md change (full 15)
- Weekly in pilot (5 anchors)
- When policy changes (full 15 with updated cases)
- Monthly in production (full 15)
- Quarterly (full 15 + calibration)

✅ **How I'll know it's working:**
- Goal: Maintain 15/15 quality, catch regressions early
- Baseline: 15/15 eval pass (19 Nov 2026), 17% error rate, 62% self-service
- Target: 15/15 eval pass, ≤8.5% error rate, ≥70% self-service, <20% override
- Usage metric: Evaluation-to-production correlation (do scores predict outcomes?)
- Pattern to watch: Dismissal spike, override spike, support surge, coverage gaps
- Stop line: Deal-breaker fail, >30% dismissal, >20% override, PII exposure

✅ **First 30 days:**
- Day -7: Final eval, Director approval
- Day 0: Launch 5% pilot
- Week 1: Intense monitoring (3x daily), anchor eval Day 3 & 7
- Week 2: Pattern detection, investigation if triggered
- Week 3: Adjustment if needed, re-eval, deploy
- Week 4: Stabilization, prepare Month 1 report
- Day 30: Director decision (expand/continue/pause)

**All roles use job titles, all items marked PROPOSED, awaiting approval.**
