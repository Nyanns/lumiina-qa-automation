# Bug Report: BUG-001

## Metadata
- **Bug ID**: `BUG-001`
- **GitHub Issue**: [#26](https://github.com/Nyanns/lumiina/issues/26)
- **Jira Issue**: `LUM-5`
- **Associated Test Case**: `TC_AUTH_001`
- **Module**: Authentication (`/register`)
- **Reported By**: Sandi (QA & SDET Engineering)
- **Reported Date**: 2026-09-13
- **Status**: Open
- **Severity**: Major
- **Priority**: High (P2)

---

## Title
[Auth] Raw validator error leaked and user registration fails when username contains underscore

---

## Environment
- **Target URL**: `https://lumiina-art.vercel.app/register`
- **API Endpoint**: `POST /api/v1/auth/register`
- **Operating System**: Linux (Ubuntu 24.04 x86_64)
- **Browser**: Google Chrome 128.0 (Official Build)
- **Backend Stack**: Go 1.22+, Gin, go-playground/validator v10

---

## Steps to Reproduce (STR)
1. Navigate to the registration page at `https://lumiina-art.vercel.app/register`.
2. Input a valid username containing an underscore: `qatest_sandi`.
3. Input an active and valid email address: `sandisensei13@gmail.com`.
4. Input a strong password satisfying all complexity rules: `Lumiina2026!`.
5. Click the "Create account" button.

---

## Expected Result
The system should either:
1. Accept the underscore as standard username convention and proceed with account creation and verification email dispatch, OR
2. If underscores are strictly disallowed by product design, reject gracefully with a human-readable message (e.g., *"Username may only contain letters and numbers without special characters"*), preferably enforced via inline client-side validation before submission.

---

## Actual Result
Registration fails. The UI displays an unformatted raw Go validator error string:
```text
Field validation for 'Username' failed on the 'alphanum' tag
```

---

## Evidence
Screenshot captured during execution of `TC_AUTH_001`:
![TC_AUTH_001 Bug Evidence](../Bug%20Evidence/001.png)

---

## Root Cause Analysis (White-Box Code Audit)
1. In `internal/model/user.go`, the `RegisterRequest` struct enforces the `alphanum` validation tag:
   ```go
   Username string `json:"username" binding:"required,alphanum,min=3,max=30"`
   ```
   The `alphanum` rule strictly permits only `[a-zA-Z0-9]`, rejecting standard symbols such as underscores (`_`).
2. In `internal/middleware/error_handler.go` and `user_handler.go`, error responses from the Gin binding validator are forwarded directly to the JSON response body without a user-facing translation layer.

---

## Suggested Remediation
1. **Validation Tag Update**: If usernames should allow underscores (industry standard on GitHub, X, Discord), update the tag to a custom regex or validator rule that permits alphanumeric characters and underscores (`^[a-zA-Z0-9_]{3,30}$`).
2. **Error Translation Layer**: Sanitize validator output so internal struct field names and tag names (e.g., `'alphanum' tag`) are never leaked to end users.
