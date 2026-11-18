# Build Log - Day 2: Validate

Scenario: B · ICA Passport Applications

## Three Leaders

### 1. Director of Passport Services (Frontline Operations)

**Objection:**

"This is backwards. You're showing applicants their mistakes AFTER they've already spent 20-30 minutes filling out the entire form and uploading documents. By that point, they're frustrated and exhausted. The prototype says 'Photo may not meet requirements' — but the applicant already paid $20 at the photo shop! Now what? They have to go back, get a new photo taken, come back here, and re-upload?

My team handles 15,000 applications a week. If we deploy this, we'll just move the errors from post-submission to mid-submission. People will still call our hotline asking 'what's a white background?' or 'can I crop my existing photo?' The error detection is impressive, but it's catching problems at the wrong moment."

**My Response:**

Isn't it better that these errors are caught before the applications are submitted to your team to review? As you mentioned your team handles 15k applications a week, complete submission will reduce their workloads significantly. Of course, we can improve upper the funnel to make the guideline clearer but we know no one read the guideline. If the applicant don't read the requirement and took a wrong photo then we shouldn't be responsible for that. We need to stick to our goal! In order to show those data, we need to run experiment.

**Director's Counter-Response:**

"Hold on. You're saying 'we shouldn't be responsible' when an applicant takes a wrong photo? That's exactly the wrong mindset for government service. These are our citizens — many are elderly, some have low digital literacy, some are doing this for the first time in 10 years. If 62% aren't reading the guidelines, that's OUR problem to solve, not theirs.

And yes, catching errors before submission sounds good on paper, but you're missing my point: this prototype catches errors at the WRONG step in the journey. You're validating at preview, when the applicant has already taken time off work, paid for photos, uploaded everything, and invested 20 minutes.

What I'm saying is: why not validate EARLIER? Show photo requirements with examples BEFORE they upload. Check the birth certificate WHEN they select 'child renewal.' Give them a checklist upfront, not error messages at the end."

**Requirements to Prove:**

- User testing with real applicants showing 70%+ can fix errors without calling support
- Average time to complete application doesn't increase beyond current 22 minutes
- Testing includes elderly parents, foreign birth certificates, home-taken photos
- Data proving applicants understand error prompts and take correct action

---

### 2. Head of Product

**Objection:**

"I'm looking at your hypothesis: 'applicants edit their submission when prompted about errors, and make correct edits.' That's two assumptions, not one. Your prototype only tests the first part — whether they'll try to fix it. It doesn't test whether they fix it CORRECTLY.

Look at your error card: 'Photo appears to have a coloured background.' What if the photo actually has a white background but there's a shadow? What if it's a lighting issue? The AI flagged it as 'may not meet requirements' — that's a probabilistic judgment, not a definite rule. But your UI treats it like a hard error.

I'm worried we're setting up a terrible loop:
- Applicant uploads photo
- AI says 'might be wrong'
- Applicant uploads a different photo
- AI says 'might be wrong' again
- Applicant calls support, frustrated

We're deploying AI uncertainty into a citizen-facing flow without a clear escape path. Where's the 'I believe this is correct' override? Where's the 'request human review' option?

Your kill switch says 'applicants understand the prompt and make correct edits' — but how do you measure 'correct' if the AI itself is uncertain?"

**My Response:**

The accuracy of the model is above 98%. We can update the prototype to show confidence levels shown to users, a human review option. We will also enable the submitting button after the first review in case the AI make mistake the user won't be stuck in the loop.

**Head of Product's Counter-Response:**

"Wait. You just said three things that contradict each other:

First, you claim 98% accuracy — but accuracy of WHAT? Is that 98% precision on detecting missing documents? Or 98% recall on catching photo errors? Those are completely different metrics. And 98% sounds high until you realize that's 300 wrong calls out of 15,000 weekly applications.

Second, you say you'll 'enable the submit button after the first review' — so if the AI flags an error, but the applicant doesn't fix it, they can just submit anyway? Then what's the point of the AI check? You're building a guard rail that users can ignore.

Third, 'we can add confidence levels and human review' — you're describing features you DON'T have yet. Your prototype doesn't show any of this. So you're asking for budget and executive buy-in based on a half-built idea."

**Requirements to Prove:**

- Precision and recall rates for each error type (missing doc, photo background, NRIC mismatch, etc.)
- A/B test data showing applicants who see prompts have LOWER re-submission rate than control
- Updated prototype with:
  - Confidence levels shown to users
  - Human review option
  - Override path for edge cases

---

### 3. Chief Executive Sponsor

**Objection:**

"I'm the one who has to answer to Parliament when something goes wrong. And I'm looking at this from 30,000 feet.

Here's what concerns me: you're solving for YOUR operational efficiency — the 17% error rate, the 6-day delays, your officer workload. But what about the applicant's experience? What about trust?

Right now, when someone's application gets returned, they blame themselves: 'I forgot the birth certificate' or 'I uploaded the wrong photo.' It's frustrating, but they understand it's a rule they missed.

But with your AI system, when it flags an error, who do they blame? The government. The system. 'The AI rejected my perfectly good photo.' 'The computer won't let me submit even though I've tried three times.'

You're inserting AI into a high-stakes government transaction. Passport renewal isn't buying shoes online. People need passports for time-sensitive travel — funerals, job offers, medical treatment overseas. If your AI blocks their application and they miss their flight, that's a STOMP article. That's a Parliamentary Question.

I'm also worried about equity. Your prototype is in English. What about elderly Chinese-educated applicants who struggle with digital services? What about foreign spouses with translated documents? Your 98% accuracy — is that evenly distributed, or does it perform worse on edge cases, which are often our most vulnerable users?

And finally: what's your rollback plan? If we deploy this and citizen complaints spike, how fast can we turn it off? What's the fallback?"

**My Response:**

Okay, we will conduct user research (i.e. prototype testing with real applicants). Yes, the staged rollout is already in the plan. We will come up with the guardrails and political risk assessment and get back to you.

**Requirements to Prove:**

- User research with real applicants across age groups, education levels, and document types
- Staged rollout plan starting with 5% of applications, monitoring call center volume and NPS
- Guardrails documented:
  - When AI defers to human judgment
  - Confidence thresholds
  - Clear SOP for edge cases
- Political risk assessment:
  - Worst-case scenarios
  - Response plan for STOMP articles and Parliamentary Questions
  - Rollback plan if citizen complaints spike

---

## Summary of Commitments

To move forward, the prototype must prove:

1. **Usability** - Real applicants can understand and fix errors without support (70%+ success rate)
2. **Accuracy** - Precision/recall documented for each error type, with plan for false positives/negatives
3. **Trust & Safety** - Staged rollout, guardrails, human review option, and political risk mitigation
