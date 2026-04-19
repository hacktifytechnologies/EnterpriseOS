# Assessment — EnterpriseOS: Broken Access Control (Stateless JWT vs Database State)

---

## Question 1 — MCQ

**Why did the API allow access to the audit export endpoint after subscription cancellation?**

- A) The browser failed to clear the cached session
- B) The token expired too slowly
- C) The UI logic was bypassed by the database
- D) **The JWT was still valid and the API skipped the database subscription check** ✅

> **Answer:** D — The `/api/admin/export-audit` endpoint trusts the JWT role claim without re-validating the account's subscription status in the database.

---

## Question 2 — MCQ

**Where was the subscription check correctly enforced, and where was it missing?**

- A) Enforced in the JWT payload; missing in the database
- B) **Enforced at the UI `/admin/audit` page; missing in the `/api/admin/export-audit` endpoint** ✅
- C) Enforced at the API; missing in the browser routing
- D) Enforced in LocalStorage; missing in session storage

> **Answer:** B — The frontend performs a stateful database check and shows a 403 page. The backend API only checks the JWT role claim, creating an inconsistent authorization architecture.

---

## Question 3 — MCQ

**What does the JWT payload contain that grants API access despite cancellation?**

- A) An encrypted database session ID
- B) A time-based one-time token
- C) **The `enterprise_admin` role claim, with no subscription status field** ✅
- D) An IP allowlist entry

> **Answer:** C — The JWT encodes the role at issuance with no revocation mechanism. The API trusts this role claim even after the account is cancelled in the database.

---

## Question 4 — Fill in the Blank

**What API endpoint is called directly (bypassing the UI) to exfiltrate the audit export CSV after subscription cancellation?**

**Answer:** `/api/admin/export-audit`

> The UI correctly blocks access via a 403 page. However, calling this API endpoint directly with the still-valid JWT token returns HTTP 200 with the full `audit_export.csv`, because the backend does not re-check the database subscription status.

---

## Question 5 — Fill in the Blank

**Where in the browser is the JWT token stored that must be extracted to perform the direct API call?**

**Answer:** `LocalStorage`

> The JWT is stored in the browser's `LocalStorage` (accessible via `Application → Local Storage` in DevTools). It persists after subscription cancellation because there is no server-side token revocation mechanism.

---

*Lab target:* `http://localhost:3000`
