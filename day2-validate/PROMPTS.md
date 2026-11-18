# Prompt chain

**Scenario:** B · ICA passport applications
**Prototype today:** The prototype uses AI to check passport applications for missing documents and discrepancies, prompting applicants to correct errors before submission.

All data is synthetic and labelled as made up.

## 1. Expand

**Prompt:** Progressive validation: Guide applicants at the point of need, validate documents upon upload, and perform a final consistency check before submission

**Business rules:**
- Show relevant ICA requirements and guidance before applicants upload documents.
- Tailor document requirements based on applicant circumstances (e.g. child renewal, foreign birth certificate).
- Display passport photo requirements with visual examples before upload.
- Validate each document immediately upon upload and provide actionable feedback.
- Use deterministic rules for mandatory requirements and AI for contextual checks.
- Clearly distinguish mandatory errors from AI-generated suggestions.
- Explain each flagged issue with references to relevant ICA guidelines.
- Allow applicants to dismiss uncertain AI suggestions or request human review.
- Avoid repeatedly flagging the same issue without providing new information or a clear resolution path.
- Perform a final consistency check across all submitted information before submission.
- Never block submission solely based on AI-generated warnings.
- Allow applicants to proceed through the standard process if AI validation fails or is unavailable.
- Final application approval remains with ICA officers.

**Edge cases:**
- User uploads a valid passport photo, but AI incorrectly flags it as non-compliant (false positive).
- User uploads an invalid document, but AI fails to detect the issue (false negative).
- User uploads a photo with a white background, but shadows or lighting cause AI to flag it incorrectly.
- User uploads a document in a foreign language that AI cannot reliably interpret.
- User applies on behalf of a child and receives incorrect or incomplete document requirements.
- User uploads a blurry or partially obscured document that AI cannot confidently validate.
- User receives conflicting guidance from AI and official ICA guidelines.
- User repeatedly uploads corrected documents, but AI continues flagging the same error.
- User has a valid exception that is not covered by standard ICA requirements.
- User changes application details midway, making earlier document guidance inaccurate.
- User ignores upfront guidance and uploads a non-compliant document.
- User receives too many AI prompts during the application process, causing frustration or abandonment.
- User has poor internet connectivity, causing real-time validation to fail or time out.
- User chooses to proceed despite an AI warning they believe is incorrect.
- AI validation service is unavailable, requiring fallback to standard application checks.

**Result:** _fill in after you run it: what worked, what broke._

## 2. Behavior

**Prompt:** Guide applicants with relevant ICA requirements before they upload documents, using clear explanations and visual examples.
Validate documents immediately upon upload and provide specific, actionable feedback.
Clearly distinguish mandatory errors from AI-generated suggestions, with references to ICA guidelines.
Allow applicants to correct errors, dismiss AI suggestions, or request human review.
Dynamically update document requirements when applicants change their information.
Perform a final consistency check before submission without unnecessarily repeating previously dismissed warnings.
Never automatically modify applicant information or make final approval decisions.

**Forced failure:** Scenario: AI incorrectly flags a valid passport photo with a white background as non-compliant (false positive). Applicant uploads a valid passport photo with a white background. AI incorrectly flags the background as non-compliant and asks for a replacement. Applicant selects "I believe my photo is correct." System acknowledges that AI may be wrong and offers options to proceed with the original photo or request human review. Applicant proceeds without being forced into repeated uploads. Expected behavior: The system handles AI uncertainty transparently, preserves user control, and does not block submission based solely on an AI-generated warning.

**Business rules:**
- Show relevant ICA requirements before applicants upload documents.
- Tailor document requirements based on applicant circumstances.
- Display passport photo requirements with visual examples before upload.
- Validate documents immediately upon upload.
- Use deterministic rules for mandatory requirements and AI for contextual checks.
- Clearly distinguish mandatory errors from AI-generated suggestions.
- Explain flagged issues with references to ICA guidelines.
- Allow applicants to dismiss uncertain AI suggestions or request human review.
- Never block submission solely based on AI-generated warnings.
- Avoid repeatedly flagging the same issue without new evidence.
- Revalidate affected documents when application details change.
- Perform a final consistency check before submission.
- Fall back to standard application checks when AI is unavailable.
- Final application approval remains with ICA officers.

**Edge cases:**
- User uploads a valid document, but AI incorrectly flags it as an error (false positive).
- User uploads an invalid document, but AI fails to detect the issue (false negative).
- User uploads a passport photo with a white background, but shadows cause AI to flag it incorrectly.
- User uploads a blurry or partially obscured document that AI cannot confidently validate.
- User uploads a foreign-language document that AI cannot reliably interpret.
- User applies on behalf of a child and receives incorrect document requirements.
- User has a valid exception not covered by standard ICA requirements.
- User changes application details midway, making earlier guidance inaccurate.
- User receives conflicting guidance from AI and official ICA guidelines.
- User repeatedly uploads corrected documents, but AI continues flagging the same issue.
- User ignores upfront guidance and uploads a non-compliant document.
- User receives too many AI prompts, causing frustration or abandonment.
- User chooses to proceed despite an AI warning they believe is incorrect.
- User experiences slow or failed validation due to poor connectivity.
- AI validation service becomes unavailable during the application.

**Result:** _fill in after you run it: what worked, what broke._

## 3. Refine

**Prompt:** Polish the prototype into a clean, intuitive, mobile-first government service experience.

Use simple, citizen-friendly language, avoiding technical terms such as "AI confidence" or "false positive."

Make document requirements easy to understand with visual examples of acceptable and unacceptable uploads.

Provide immediate, contextual feedback without interrupting the application flow unnecessarily.

Clearly differentiate mandatory errors, AI suggestions, and successfully validated documents using consistent visual indicators.

Make corrective actions obvious, with clear buttons such as "Replace photo," "Keep original," and "Request review."

Ensure applicants can understand each prompt and decide what to do within three seconds.

Minimise unnecessary clicks, repeated warnings, and cognitive load.

Show progress and provide reassurance without implying that AI validation guarantees approval.

Preserve the existing forced-failure scenario and demonstrate a smooth recovery experience.

**Business rules:**
- Display only requirements relevant to the applicant's circumstances.
- Present guidance at the point of need rather than overwhelming applicants upfront.
- Validate documents immediately after upload without requiring additional clicks.
- Use clear visual indicators to distinguish mandatory errors from AI suggestions.
- Every flagged issue must include a reason and a recommended next action.
- Reference official ICA guidelines for document requirements.
- Never present uncertain AI findings as definitive errors.
- Allow applicants to dismiss AI suggestions or request human review.
- Never block submission solely because of an AI-generated warning.
- Do not repeat dismissed warnings unless new information changes the assessment.
- Preserve uploaded documents and entered information when applicants navigate between steps.
- Provide a final application summary before submission.
- Ensure the interface is accessible, responsive, and usable on mobile devices.
- Keep final application approval with ICA officers.

**Edge cases:**
- Applicant does not understand the error message or required corrective action.
- Applicant uploads a valid photo that AI incorrectly flags as non-compliant.
- Applicant disagrees with an AI suggestion and wants to proceed.
- Applicant repeatedly receives the same warning despite making corrections.
- Applicant uploads a document that cannot be confidently validated.
- Applicant encounters conflicting instructions between AI and ICA guidelines.
- Applicant changes application details after uploading documents.
- Applicant has multiple errors and becomes overwhelmed by simultaneous prompts.
- Applicant uses a small mobile screen where guidance and error messages may be difficult to read.
- Applicant has limited English proficiency or low digital literacy.
- Applicant accidentally navigates away during document validation.
- Applicant experiences slow internet connectivity or a validation timeout.
- Applicant requests human review when immediate assistance is unavailable.
- Applicant reaches final submission with unresolved AI suggestions.
- AI validation becomes unavailable, requiring a seamless fallback to standard checks.

**Result:** _fill in after you run it: what worked, what broke._
