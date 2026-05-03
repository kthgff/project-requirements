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
- collect the Google email address the user intends to use
- present a clear "Sign in with Google" action
- block access to the main app until authentication succeeds
- feel visually polished enough to be the first product impression, not a generic placeholder screen

The email field shall:
- require a syntactically valid email address before the Google sign-in action can start
- preserve the typed email during a failed or canceled auth attempt
- compare the typed email against the Google OAuth profile email after callback
- reject mismatched accounts with a clear retryable error

### Gmail federated login
The product shall support Google federated authentication.

MVP assumption:
- Google identity is used for user authentication
- the authenticated Google account is the same account whose Gmail inbox will be scanned, unless engineering decides to separate auth and Gmail authorization later
- any Google account is allowed to sign in for MVP

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
- profile image URL if available
- created_at
- updated_at

For returning users, the system shall update mutable profile fields such as email casing, display name, profile image URL, and `updated_at` without creating a duplicate user.

---

## Functional Requirements

### FR1. Login page
The system shall provide a login page for unauthenticated users.

### FR2. Google sign-in
The system shall provide Gmail federated login using Google authentication.

### FR2a. Supplied email verification
The system shall require the supplied login email to match the Google OAuth profile email before creating an application session.

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
- Google email input
- one clear Google sign-in button
- short reassurance copy that JobTrakr uses Google sign-in and does not create a separate password

### Login page visual direction
The page should present JobTrakr as a sleek, professional job-search command center.

Preferred treatment:
- full-viewport responsive composition
- strong first-screen brand signal using the JobTrakr name
- visually rich background or product-preview inspired panel
- high-contrast Google sign-in area
- subtle status indicators for scanning, matching, and tracking jobs
- accessible focus, loading, disabled, and error states

The page should avoid:
- a plain unstyled form
- a marketing-only landing page before login
- more than one competing primary action

### Login success behavior
After login:
- first-time users should be directed to the jobs dashboard
- returning users should go to the main app view
- if the dashboard has no jobs or no search profiles, its empty state should guide the user to create a search profile

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
- reject OAuth callbacks where the Google profile email does not match the email supplied on the login form
- allow anyone with a Google account to sign in for MVP unless a later account allowlist is explicitly added

---

## Open Questions

- Should Google auth and Gmail inbox authorization be a single combined flow or two distinct permissions steps?
- Should first login immediately trigger onboarding for resume upload and inbox setup?
