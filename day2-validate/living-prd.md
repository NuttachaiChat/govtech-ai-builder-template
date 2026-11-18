# Living PRD - Day 2: Validate

**[solid: clear user flow]**  
**[fragile: no state management, user actions lost on navigation]**  
**[surprise: CSS styling file has the longest code]**

---

Scenario: B · ICA Passport Applications

All data is synthetic and labelled as made up.

## Current State

This directory contains validation prototypes demonstrating progressive validation, forced failure handling, and citizen-friendly design for AI-powered passport application checking.

### Files
- `prototype/` - HTML/CSS/JS mockups (2,119 lines total)
- `PROMPTS.md` - Expand/Behavior/Refine prompt chain with documented results
- `build-log.md` - Three leaders' objections and riskiest assumption testing
- `context-skill.md` - Domain knowledge for AI agent

## What Works

**User Flow (Solid)**
- Progressive validation: Guide → Upload → Validate → Review
- Clear error messages with actionable next steps
- Three options for AI warnings: Replace, Keep Original, Request Human Review
- No submission blocking on AI suggestions
- Visual examples (good/bad photo requirements)
- ICA corporate identity consistently applied

**Demonstrated Behaviors**
- False positive handling (forced-failure-test.html)
- Immediate feedback on document upload
- Confidence levels shown (85% Confident)
- Human review fallback option
- Transparent AI uncertainty ("AI may be incorrect")

## What's Fragile

**State Management (Critical Gap)**
- User dismisses AI warning in upload-flow.html
- Navigates to index.html (final review)
- Warning reappears - dismissal lost
- No localStorage, sessionStorage, or state passing between pages
- Each page has isolated JavaScript scope

**No Real File Handling**
- Clicks simulate uploads (`onclick="simulateUpload()"`)
- No `<input type="file">` elements
- No actual file size/type validation
- No image preview
- No backend upload

**Hardcoded Validation**
- AI results predetermined (`'warning'` hardcoded)
- 1.5s setTimeout simulates validation delay
- Can't test real confidence thresholds
- No error handling for failed validation

**Inline Code Patterns**
- 14 onclick handlers across files
- 8+ inline styles mixed with CSS
- CSS duplication (`.error-card` defined in 3 files)
- ICA Blue (#1C3F6E) hardcoded 15+ times
- No componentization

## Surprising Findings

**CSS as Longest File (549 lines)**
- `styles.css` = 549 lines (26% of total codebase)
- Longer than any single HTML file
- Contains: ICA identity, form controls, error cards, progress bars, buttons
- Plus 100-200 lines of `<style>` per HTML file
- Total CSS: ~1,000 lines for a 6-page prototype

**Why CSS Dominates**
- Government service requires extensive visual polish
- Multiple error states need distinct styling
- ICA corporate identity has specific color rules
- Accessibility requires larger touch targets, high contrast
- Responsive design (claimed, not implemented) needs media queries

## Next Steps (If Building Production)

### Phase 1: Foundation
1. Add real file upload handling
2. Implement state management (React Context or Redux)
3. Build backend API (upload, validate, submit endpoints)
4. Add error boundaries and retry logic

### Phase 2: Hardening
5. Integrate real AI validation service
6. Implement persistence (save drafts, resume applications)
7. Add accessibility (ARIA labels, keyboard nav)
8. Mobile optimization (media queries, responsive grid)

### Phase 3: Measurement
9. Analytics (track dismissal rates, drop-off points)
10. A/B testing confidence thresholds
11. Monitor officer override rates

## Engineering Estimate

**Prototype → Production: 6-8 weeks**
- Keep: Visual design, UX flows, copy
- Rebuild: File handling, state management, API integration, real validation

## Open Questions

1. How to handle state across multi-page flow? (SPA vs MPA with session storage)
2. What happens if user dismisses 3 AI warnings in a row? (Trust erosion mitigation)
3. How to track whether dismissed warnings were correct? (Feedback loop for AI improvement)
4. Officer review backlog: What's acceptable rate of "Keep Original" selections? (30%? 10%?)
5. Mobile: Stacked vs tabbed layout for photo examples on small screens?
