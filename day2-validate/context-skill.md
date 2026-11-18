# Context Skill: ICA Passport Application Validator

Scenario: B · ICA Passport Applications
All examples and data in this file are synthetic and made up.

---

## Purpose

Provide the AI agent with domain knowledge to accurately identify errors in passport applications and generate clear, actionable prompts that applicants can understand and fix without support.

This context enables the agent to:
- Distinguish between mandatory hard errors and soft warnings
- Explain WHY something is wrong in plain language
- Suggest SPECIFIC actions to fix each error type
- Know when to escalate to human review

---

## Sources

This context is compiled from (synthetic sources):

1. **ICA Passport Application Guidelines v3.2** (2025) - Official document requirements
2. **ICA Photo Standards Manual** (Updated Jan 2025) - Technical photo specifications
3. **Call Center FAQ Database** - Top 50 applicant questions (2024-2025)
4. **Officer Training Manual** - Decision trees for edge cases
5. **Usability Study Report** (Oct 2024) - How applicants interpret error messages

---

## Rules

### Document Requirements (Synthetic Rules)

**For Child Passport Renewal (Under 16):**
- Parent/guardian NRIC (front & back) - MANDATORY
- Child's existing passport - MANDATORY
- Child's birth certificate (SG or foreign with certified translation) - MANDATORY
- Recent passport photo (taken within 3 months) - MANDATORY
- Parental consent form (if only one parent applying) - CONDITIONAL

**Photo Requirements:**
- Background: Plain white (RGB 250-255, 250-255, 250-255)
- Size: 35mm x 45mm at 600 DPI minimum
- Face: Centered, neutral expression, eyes open
- NO: Glasses, hats, visible shadows, filters, retouching

**Name Matching Rules:**
- Name on application must match child's birth certificate exactly
- Hyphenated names: both parts must be present
- Name changes: require legal deed poll or court order
- Typos of 1-2 characters: flag for officer review (don't auto-reject)

### Error Message Language Rules

1. **Be specific, not vague**
   - ❌ "Document issue detected"
   - ✅ "Child's birth certificate not uploaded"

2. **Explain WHY it's required**
   - ❌ "Missing document"
   - ✅ "Birth certificate is required to verify parental consent for child renewals"

3. **Give actionable next step**
   - ❌ "Fix this"
   - ✅ "Upload a clear copy of the birth certificate (Singapore or foreign with certified translation)"

4. **Use plain language (Primary 6 reading level)**
   - ❌ "Photographic submission exhibits chromatic background anomaly"
   - ✅ "Photo appears to have a coloured background. Passport photos must have a plain white background."

### Confidence Thresholds

- **High confidence (95%+)**: Show as definite error, block submission
- **Medium confidence (80-95%)**: Show as warning, allow submission with acknowledgment
- **Low confidence (<80%)**: Don't show to applicant, flag for officer review

### Escalation to Human Review

Defer to ICA officer if:
- Foreign documents in languages other than English/Chinese/Malay/Tamil
- Name discrepancies that might be legal name changes
- Documents that appear altered or fraudulent
- Applicant challenges AI decision and requests human review
- Edge cases not covered in training data (e.g., stateless persons, diplomatic passports)

---

## Examples

### Good Example 1: Clear, Actionable Error Message

**Scenario:** Child passport renewal, birth certificate missing

**AI Prompt Output:**
```
❌ Child's Birth Certificate not uploaded

For passport renewal of a child under 16, the child's birth certificate 
is required to verify parental consent.

What to do: Upload a clear copy of the child's birth certificate 
(Singapore or foreign birth certificate with certified translation if applicable).

📄 ICA Requirement: Document Checklist for Child Passport Renewal
```

**Why this is good:**
- States exactly what's missing (not vague)
- Explains why it's needed (parental consent verification)
- Gives specific action (upload birth cert, mentions translation)
- Links to official requirements
- Uses simple language

---

### Good Example 2: Soft Warning with Confidence Level

**Scenario:** Photo background detection is 85% confident (medium confidence)

**AI Prompt Output:**
```
⚠️ Photo may not meet requirements (Confidence: 85%)

The uploaded photo appears to have a coloured background. 
Passport photos must have a plain white background.

What to do: 
1. Upload a new photo with a plain white background, OR
2. If you believe this photo is correct, you can submit for officer review

📄 ICA Requirement: Photo Requirements for Singapore Passport

Note: You can proceed to submit, but the application may be returned 
if the photo doesn't meet requirements.
```

**Why this is good:**
- Shows confidence level (transparency)
- Explains the potential issue
- Gives TWO options: fix it or proceed with risk
- Sets expectations (may be returned)
- Doesn't block submission for medium confidence

---

### Bad Example: Vague, Unhelpful Error Message

**Scenario:** Photo background issue

**AI Prompt Output:**
```
❌ Invalid photo format

Your photo does not meet ICA standards. Please correct and re-upload.
```

**Why this is bad:**
- "Invalid photo format" is vague (format? background? size? face?)
- Doesn't explain WHAT is wrong
- "ICA standards" - which standard? where to find it?
- "Please correct" - HOW?
- No examples, no links, no guidance
- Applicant will call support asking "what's wrong with my photo?"

---

## Owner

**Product Team:** Passport Services Digital Team
**Primary Contact:** Lee Wei Ming (Synthetic) - Product Manager, Passport Services
**Last Updated:** 18 Nov 2026
**Version:** 1.0

---

## Review Trigger

Review and update this context when:

1. **ICA updates passport requirements** (usually annually in January)
2. **Call center reports new confusion patterns** (monthly review)
3. **Error message effectiveness drops below 70%** (measured by user testing)
4. **New document types are introduced** (e.g., digital birth certificates)
5. **Regulatory changes** affecting identity verification requirements

Next scheduled review: **January 2027**

---

## Test Questions & Expected Answers

All data below is synthetic and made up for testing purposes.

---

### Q1: Application Details

**Test Case:**
- Applicant: Tan Mei Ling (child, DOB: 12 Mar 2019, synthetic)
- Parent: Tan Wei Jie (synthetic)
- Application Type: Child passport renewal
- Documents uploaded: Parent NRIC, Child's existing passport, Passport photo
- Missing: Child's birth certificate

**Question:** What error should the AI flag and what message should it show?

**Expected Answer:**
```
Error Type: Missing Document (Hard Error)
Confidence: 100% (mandatory document)

Message:
"Child's Birth Certificate not uploaded

For passport renewal of a child under 16, the child's birth certificate 
is required to verify parental consent.

What to do: Upload a clear copy of the child's birth certificate."

Action: Block submission until uploaded
```

---

### Q2: Photo Background Detection

**Test Case:**
- Photo uploaded with light grey background (RGB 220, 220, 220)
- AI confidence: 88%
- Applicant: Kumar Rajan (synthetic, adult renewal)

**Question:** Should this be a hard error or soft warning? What message should show?

**Expected Answer:**
```
Error Type: Soft Warning (Medium Confidence)
Confidence: 88%

Message:
"Photo may not meet requirements (Confidence: 88%)

The uploaded photo appears to have a coloured background. 
Passport photos must have a plain white background.

What to do: 
1. Upload a new photo with a plain white background, OR
2. If you believe this photo is correct, you can submit for officer review"

Action: Allow submission with acknowledgment, flag for officer review
```

---

### Q3: Name Discrepancy

**Test Case:**
- Child's name on application: "Lee Xiao Ming"
- Child's name on birth certificate: "Lee Xiao-Ming" (with hyphen)
- Discrepancy: Missing hyphen

**Question:** How should the AI handle this? Is it an error?

**Expected Answer:**
```
Error Type: Potential discrepancy (Escalate to human)
Confidence: Cannot determine (requires judgment)

Message:
"Name spelling may not match birth certificate

The name on your application appears slightly different from the 
birth certificate. This may need officer review.

What to do: Please double-check that the name matches exactly, 
including hyphens and spacing. You can still submit, and an officer 
will verify during processing."

Action: Allow submission, flag for officer review with note: 
"Hyphenation difference - likely acceptable but needs verification"

Reasoning: Small formatting differences (hyphens, spacing) are common 
and often acceptable. Don't block applicant, but ensure officer checks.
```

---

### Q4: Foreign Birth Certificate

**Test Case:**
- Child: Wong Li Ying (synthetic)
- Birth certificate: China-issued, in Simplified Chinese only
- No English translation attached
- Required: Certified translation for non-English documents

**Question:** What should the AI flag and suggest?

**Expected Answer:**
```
Error Type: Missing Translation (Hard Error)
Confidence: 100% (policy requirement)

Message:
"Translation required for foreign birth certificate

Your child's birth certificate is in Chinese. A certified English 
translation is required for foreign documents.

What to do: 
1. Get the birth certificate translated by a certified translator
2. Upload both the original document AND the certified translation

Note: ICA accepts translations from licensed translation services 
or Singapore embassies/consulates."

Action: Block submission until translation uploaded
Escalation: If applicant disputes or claims translation not needed, 
escalate to officer
```

---

### Q5: Edge Case - Photo Quality

**Test Case:**
- Photo uploaded: acceptable white background, correct size
- BUT: Image is blurry (compression artifacts, low resolution ~200 DPI)
- AI confidence on quality issue: 75% (low-medium)

**Question:** Should the AI flag this? If yes, how?

**Expected Answer:**
```
Error Type: Soft Warning (Low-Medium Confidence)
Confidence: 75%

Message:
"Photo quality may be too low (Confidence: 75%)

The photo appears slightly blurry. Passport photos should be sharp 
and clear.

What to do: 
1. If possible, upload a higher quality version of the photo, OR
2. Proceed to submit - an officer will review and may request a new 
photo if needed

Tip: Use your phone camera's highest quality setting or a professional 
photo service."

Action: Allow submission, flag for officer review
Reasoning: Image quality assessment at 75% confidence is not reliable 
enough to block submission. Let officer make final call.
```

---

## Notes on Synthetic Data

All names, NRICs, dates, and scenarios in this document are synthetic 
and made up for testing purposes only:

- Tan Mei Ling, Tan Wei Jie (synthetic)
- Kumar Rajan (synthetic)
- Lee Xiao Ming (synthetic)
- Wong Li Ying (synthetic)

No real citizen data is used in this context skill document.
