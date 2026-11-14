# KIT Assistant – Update Notes

This archive contains targeted safety, deployment and packaging improvements to the supplied project. It is not a claim that the external college API or hosted deployment has been tested.

## Changes made

1. **Conservative document visibility**
   - Uses explicit visibility/audience metadata when available.
   - Admissions/brochure/prospectus documents can be public.
   - Faculty/teacher/internal documents are staff-only.
   - Unknown documents default to student-only; they are never automatically made public just because no public document was detected.
   - Student retrieval excludes staff-only documents.
2. **Narrower role permissions**
   - Students are limited to `/v1/me/` resources, with narrowly scoped POST access for leave/on-duty request endpoints.
   - Teachers are limited to `/v1/teacher/`, `/v1/teachers/`, `/v1/staff/` workflows and read-only `/v1/me/` access.
   - Teacher write actions are limited to approval/rejection routes.
   - Common public GET endpoints remain readable after login.
3. **Latest-turn write confirmation guard**
   - Write execution requires both the model's `user_confirmed` flag and a clear affirmative confirmation in the latest user message.
   - For production-grade approval, bind the exact pending request payload to a server-side confirmation record and verify it before submission.
4. **Vercel lifecycle adjustment**
   - Does not start a perpetual background refresh task on Vercel.
   - Performs a best-effort one-time OpenAPI/document sync during serverless cold start.
   - Vercel's filesystem remains ephemeral; SQLite history is not guaranteed across instances or redeployments. Use managed PostgreSQL or persistent hosting for durable history.
5. **Safer project packaging**
   - Added `.env.example`.
   - Excluded the real `.env`, `.git` repository, local SQLite database, and Python cache files from this ZIP.

## Validation performed

- Python compilation completed successfully.
- 24 automated tests passed using a temporary local stub for the unavailable Groq SDK import. The Groq SDK itself could not be installed in this environment because network access was unavailable.
- Live college API calls, real Groq voice calls, Vercel deployment and persistent multi-instance history were not verified here.

## Before deploying

1. Extract this ZIP.
2. Copy `.env.example` to `.env` for local development and fill in your own credentials.
3. Never commit `.env`; set secrets in your hosting provider's environment-variable settings.
4. Confirm the external college API's exact teacher approval and student leave/on-duty route paths match the allow-lists in `kit/tools.py`. If the API uses different paths, update the allow-list deliberately and add tests.
5. For Vercel, configure `COLLEGE_API_URL`, `COLLEGE_API_KEY`, `GROQ_API_KEY`, and `TIMEZONE`. Use a managed database for durable history.
