# JobTrakr Auth and Login Spec v1

## Purpose

Define the MVP authentication and login requirements for JobTrakr.

## Goal

Allow the user to securely access JobTrakr through Gmail federated login and prevent unauthenticated access to the application.

## Scope

In scope for MVP:
- login page
- Gmail federated login
- authenticated session creation
- protected app access
- user record creation on first successful login

Out of scope for MVP:
- username/password auth
- social providers other than Google
- multi-factor auth customization
- admin user management

---

## User Need

Keith should be able to open JobTrakr, sign in with his Google account, and access the app without managing a separate password.

---

## Core Requirements

### Login page
The product shall provide a dedicated login page.

The login page shall:
- explain that sign-in uses Google
- present a clear "Sign in with Google" action
- block access to the main app until authentication succeeds

### Gmail federated login
The product shall support Google federated authentication.

MVP assumption:
- Google identity is used for user authentication
- the authenticated Google account is the same account whose Gmail inbox will be scanned, unless engineering decides to separate auth and Gmail authorization later

### Authenticated session
After successful Google login:
- the system shall create or restore an authenticated session
- the user shall be redirected into the app
- the session shall persist across normal navigation until expiry or logout

### Protected routes
The product shall require authentication to access:
- the jobs table
- job detail views
- resume upload
- inbox scan features
- user-specific data

Unauthenticated users shall be redirected to the login page.

### User record
On first successful login, the system shall create a user record with at minimum:
- Google account id or stable subject id
- email address
- display name if available
- created_at
- updated_at

---

## Functional Requirements

### FR1. Login page
The system shall provide a login page for unauthenticated users.

### FR2. Google sign-in
The system shall provide Gmail federated login using Google authentication.

### FR3. Session creation
The system shall create an authenticated session after successful login.

### FR4. Protected app access
The system shall block access to authenticated product areas until login succeeds.

### FR5. User provisioning
The system shall create a user record on first successful login.

### FR6. Session reuse
The system shall allow returning authenticated users to resume access without signing in on every page load, subject to normal session expiry.

### FR7. Logout
The system shall provide a logout capability.

---

## UX Requirements

### Login page content
At minimum, the page should include:
- product name
- short value statement
- one clear Google sign-in button

### Login success behavior
After login:
- first-time users may be directed to resume upload or Gmail connection setup
- returning users should go to the main app view

### Error handling
If login fails:
- show a clear error state
- allow retry
- avoid exposing technical internals to the user

---

## Security and Access Notes

MVP recommendations:
- use provider-managed OAuth flow for Google sign-in
- store the minimum user profile data needed
- do not allow app access before authentication
- consider restricting access to approved Google accounts if this remains a single-user MVP

---

## Open Questions

- Should MVP allow only Keith's Google account, or any Google account?
- Should Google auth and Gmail inbox authorization be a single combined flow or two distinct permissions steps?
- Should first login immediately trigger onboarding for resume upload and inbox setup?
