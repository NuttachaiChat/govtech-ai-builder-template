AI user flow: An applicant previews a submission and sees prompts for additional documents / errors to edit
Scenario: B · ICA passport applications
All data in this file is synthetic.
The moment
An applicant previews their passport application and receives prompts to correct errors or upload missing documents before submission.
The iceberg
Above the line: what the user sees
Missing documents, discrepancies in submitted information, and clear guidance on how to correct them.
Below the line

* Data: User inputs, uploaded documents, ICA guidelines and application requirements.
* Model: A language model that identifies potential discrepancies and generates contextual guidance based on ICA requirements.
* Rules: Mandatory fields and document requirements are validated against official ICA rules. AI cannot approve or reject applications.
* Handoffs: Applicants review and correct flagged issues. Uncertain cases are referred to ICA officers. Final decisions remain with ICA officers.

The flow

1. Trigger: Applicant clicks to preview submission.
   * Trust gap: Does the applicant know their personal information will be processed by AI?
2. AI step: Check for discrepancies and missing documents, and suggest corrections.
   * Trust gap: Are the identified errors accurate? Could AI miss errors or flag valid information?
3. Human check: Applicant reviews the prompts, verifies their accuracy, and makes corrections accordingly.
   * Trust gap: Can applicants understand and challenge AI suggestions rather than blindly trusting them?
4. Outcome: Applicant submits an application with fewer errors and missing documents.
   * Trust gap: Will applicants mistakenly assume that passing the AI check guarantees approval?

Fallback when the AI is unsure (Advanced)

* Clearly indicate when AI cannot confidently verify information.
* Refer applicants to official ICA guidelines or human support.
* Allow applicants to proceed when AI suggestions are uncertain, provided mandatory requirements are met.
* Never automatically modify applicant information or make approval decisions.

How we measure trust (Expert)

* Accuracy: Percentage of AI-flagged errors that are genuine (precision).
* Error detection: Percentage of actual submission errors detected by AI (recall).
* False positives: Percentage of valid information incorrectly flagged.
* User trust: Percentage of applicants who understand and find AI suggestions helpful.
* Override rate: Percentage of AI suggestions dismissed by applicants.
* Outcome: Reduce the application return rate from the current 17% baseline, without increasing application abandonment.

## AI-UX readiness
Scenario: B · ICA passport applications
Moment: Applicant previews a submission and sees prompts for additional documents / errors to edit
Level reached: 1 of 3
What we ship: You cannot pass Level 2 yet, so ship a Level-2 UI: a reliable validator the user can check in under three seconds. A reliable validator beats a magical liar.
Levels

* Level 01 · Trigger Accuracy: Pass. Prompts appear when documents are missing and/or discrepancies are detected in the submission.
* Level 02 · System Transparency: Not yet passed. Each error includes a clear explanation, reference to relevant ICA guidelines, and suggested corrective action. Applicants can distinguish mandatory requirements from AI suggestions.
* Level 03 · Invisible Integration: Not yet passed. A separate validation agent checks AI-generated prompts against ICA rules and the applicant's final submission. ICA officers conduct sample audits to detect missed errors and false positives.

Trust gaps

* Black box: Applicants do not understand why AI flags their information as incorrect. Move: Provide clear explanations for each flagged issue, with references to relevant ICA guidelines.
* Hallucination: AI may incorrectly flag valid information or documents as errors (false positives). Move: Cross-check AI findings against ICA rules and guidelines, and allow applicants to dismiss incorrect suggestions.
* Control: Applicants may feel forced to change correct information to satisfy AI checks. Move: Clearly distinguish mandatory errors from AI suggestions, and allow applicants to override AI recommendations or request human review.
