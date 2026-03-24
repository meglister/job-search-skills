---
name: resume-edit
description: When the user types /resume-edit or asks to "edit my resume" and pastes a job description link, clone the base resume Google Doc, rename it with the target company name, then tailor resume bullets to the job description.
disable-model-invocation: true
---

# Resume Editing Skill

Use this skill when the user explicitly invokes it (for example by typing `/resume-edit` or saying “Edit my resume”) and provides a job description link or full job description text.

## Workflow overview

When this skill is invoked:

1. Take the base resume Google Doc:
   - Source doc URL: `[LINK]
2. Extract the target company name from the job posting.
3. Create a copy of the base resume doc and rename it by appending `_[CompanyName]` to the existing document name.
4. Analyze the job description for required skills, responsibilities, and keywords.
5. Compare the job description against the existing resume content in the cloned doc.
6. Edit relevant bullet points to better align with the job description while staying truthful and consistent with the user’s experience.
7. Provide a brief summary of the changes and highlight any bullets you recommend the user review.

## Trigger conditions

Run this skill only when:

- The user invokes `/resume-edit` or uses phrasing like “Edit my resume for this role” or “Tailor my resume”, and
- The user supplies a job description URL or pastes the job description text.

If the job description is missing or the link is inaccessible, ask the user to paste the full job description text before proceeding.

## Handling the job description

1. If the user shares a URL:
   - Treat it as the authoritative job posting.
   - If you cannot read the page, ask the user to paste the full text.
2. If the user pastes the job description:
   - Use the pasted text as the source of truth.

From the job description:

- Identify the company name (for example from the company field, page title, or header).
- Extract key responsibilities, required skills, and recurring keywords.

If the company name is ambiguous or missing, ask the user to specify the company name explicitly.

## Cloning and renaming the resume

1. Copy the base resume document from:

   [LINK]

2. Derive the company-specific name:
   - Let `[CompanyName]` be the normalized company name from the job posting (remove legal suffixes like Inc., LLC, Ltd. unless they are essential).
   - Append `_[CompanyName]` to the base document name.
   - Example: `NAME_Resume_General` → `NAME_Resume_General_ACME`.

3. Use the cloned document as the working copy for all edits.
4. Do not modify the original base resume file.

If you cannot programmatically copy or rename the Google Doc, instead:

- Generate the full revised resume content and clearly indicate the intended filename with the `_[CompanyName]` suffix so the user can create the file manually.

## Tailoring logic

When editing the cloned resume:

1. Focus on impact roles and bullets most relevant to the target role.
2. Map job description requirements to existing experience:
   - For each key requirement in the job description, try to match it to a relevant role, project, or bullet in the resume.
   - Prefer updating existing bullets over adding many new ones.
3. Update bullets to:
   - Reflect relevant skills, tools, and domain keywords from the job description.
   - Emphasize scope, impact, metrics, and outcomes where possible.
   - Use clear, action-oriented verbs aligned with the target role’s seniority.

4. Maintain truthfulness:
   - Do not invent responsibilities, roles, or metrics.
   - You may refine or quantify impact where the user has previously implied it, but avoid fabricating data.
   - If you need clarification (for example actual metrics), ask the user for details rather than guessing.

5. Maintain structure:
   - Preserve the resume layout (sections, headings, ordering) unless the user asks to change it.
   - Avoid radically changing the overall format without explicit instruction.

## Editing principles

- Keep the resume to an appropriate length for the user’s seniority (typically 1–2 pages).
- Prioritize tailoring the most recent and most relevant roles.
- Avoid keyword stuffing; incorporate important terms naturally in bullets and section headings.
- Maintain consistent tense, person, and style across bullets.
- Preserve any user-specified constraints (for example avoiding certain phrases, limiting buzzwords) if they are mentioned.

## Output expectations

When you finish tailoring the resume:

1. Provide:
   - The updated resume content (suitable to paste back into Google Docs if needed).
   - The target filename including the `_[CompanyName]` suffix.
2. Summarize:
   - The main changes made (for example “Tailored product metrics bullets to highlight crypto compliance and AML tooling.”).
   - Any assumptions you made about responsibilities or metrics.
3. Flag:
   - Any bullets where user confirmation is recommended.
   - Any gaps where additional detail from the user could significantly strengthen the resume.

## Examples of valid user prompts

- `/resume-edit` and a pasted job description link.
- “Edit my resume for this Coinbase product role:” followed by the job description.
- “Tailor my resume for this job” plus a job posting URL.
