# EVENT MANAGEMENT SYSTEM

## Sprint Roadmap & Project Plan

#### 8 - Week Development Plan | MVP v1 Target: End of Week 2

**Student** Abdul Arfath

**Reg No** P05MG24S

**Institution** MGM College, Udupi – 576102

**Internship Org** Global Zenith Tech, Udupi

**Document Version** 1.0 | March 2026

**Total Sprints** 8 sprints over 8 weeks


## 1. Executive Summary

This document presents the complete 8-week sprint plan for building the Arfath Event Management
System (EMS) — a web-based B2B event platform built with Python Flask, Firebase, PayPal, Bootstrap
5, and Docker.

The plan is divided into four phases:

- Phase 1a — MVP v1 (Weeks 1–2): Core registration and payment loop, deployable and demo-ready
- Phase 1b — MVP v2 (Weeks 3–4): Check-in, attendee portal, promo codes, and polish
- Phase 2 — Engagement (Weeks 5–6): Agenda management, speakers, analytics, and
    communications
- Phase 3 — Advanced (Weeks 7–8): Sponsor module, post-event automation, admin panel, and
    security hardening

## 2. Technology Stack

```
Layer Technology Purpose
```
```
Backend Python 3.10+ / Flask Application logic, routing, APIs
```
```
Database Firebase Firestore NoSQL document storage
```
```
Authentication Firebase Auth User identity & role management
```
```
Frontend Bootstrap 5 + JavaScript ES6 Responsive UI
```
```
Payments PayPal Checkout + IPN Ticket payment processing
```
```
Email SendGrid / SMTP Transactional emails & QR codes
```
```
Charts Chart.js Organizer dashboard visualizations
```
```
Storage Firebase Storage Cover images, certificates, files
```
```
Deployment Docker + Docker Compose Containerised localdeployment - server
```

## 3. Sprint Roadmap

Each sprint is two working weeks long. Priorities map directly to requirements in the SRS (v1.0).

### Phase 1a — MVP v1 (Weeks 1–2)

Goal: A working, demonstrable product where attendees can register, pay, and receive a QR-coded
ticket. Organizers can view registrations and basic revenue metrics.

**Sprint 1 — Foundation & Authentication (Week 1)**

```
Week 1
```
```
Sprint 1
Foundation &
Auth
```
**-** Docker + Flask project scaffolding, directory structure, .env config
**-** Firebase Auth: sign up, login, logout, password reset (REQ-101 to
104)
**-** Role assignment at signup: organizer / attendee / admin (REQ-103)
**-** Venue add, edit, delete: name, address, capacity, contact (REQ-301)
**-** Event creation: name, description, start/end dates, venue (REQ-201)
**-** Event edit, delete drafts, and publish to public listing (REQ-202, 203)
**-** Public event listing page — browse published events (REQ-405)

**Sprint 2 — Ticketing, Payment & QR Codes (Week 2)**

```
Week 2
```
```
Sprint 2
Ticketing &
Payment
```
**-** Ticket type creation: name, price, quantity, max per order (REQ-401)
**-** Attendee registration flow with real-time availability validation (REQ-
406, 407)
**-** PayPal Checkout integration + IPN/webhook confirmation handling
(REQ- 501 – 504)
**-** QR code generation on confirmed payment — unique per registration
(REQ-408)
**-** SendGrid confirmation email with QR code image attachment (REQ-
409, 902)
**-** Basic organizer dashboard: registration count, revenue total (REQ-
1001)
**-** Registration list with CSV export (REQ-1002)

```
🎯 MVP v1 Deliverable (End of Week 2): Attendees can discover an event, register, pay
via PayPal, and receive a QR-coded confirmation email. Organizers can view all
registrations and export to CSV.
```

### Phase 1b — MVP v2 (Weeks 3–4)

Goal: Complete the end-to-end event lifecycle — from registration through onsite check-in — with an
attendee portal, promo codes, and polished mobile responsiveness.

**Sprint 3 — Onsite Check-In & Attendee Portal (Week 3)**

```
Week 3
```
```
Sprint 3
Check-In &
Portal
```
**-** Browser-based QR scanner check-in page using device camera
(REQ-801)
**-** Scan QR code to mark attendee as checked-in with timestamp + staff
ID (REQ-802)
**-** Manual check-in: search attendee by name or email (REQ-804)
**-** Check-in status visible on the organizer dashboard (REQ-803)
**-** Attendee dashboard: upcoming and past events with status (REQ-
601)
**-** Attendee ticket view with QR code accessible from profile (REQ-410)

**Sprint 4 — Promo Codes, Profile & Polish (Week 4)**

```
Week 4 Sprint 4^
Promo & Polish
```
**-** Promo code creation: percentage/fixed discount, validity, usage limits
(REQ-403)
**-** Promo code validation and application during checkout (REQ-407)
**-** User profile update: name, phone number, profile picture (REQ-105,
604)
**-** Event cover image upload and branding color configuration (REQ-
205)
**-** Payment failure graceful handling and retry flow (REQ-506)
**-** Bug fixes, mobile responsiveness (Bootstrap 5), and end-to-end QA
(NFR-11)

```
🎯 MVP v2 Deliverable (End of Week 4): Full registration-to-check-in lifecycle
operational. Promo codes, attendee portal, and organizer dashboard with check-in
visibility all working.
```

### Phase 2 — Engagement (Weeks 5–6)

Goal: Add session and agenda management, speaker profiles, analytics charts, and automated
communications so the platform feels production-grade.

**Sprint 5 — Agenda & Session Management (Week 5)**

```
Week 5
```
```
Sprint 5
Agenda &
Sessions
```
**-** Session create/edit/delete: title, description, start/end time, track,
location (REQ-701)
**-** Speaker management: add/edit bio, photo, assign to sessions (REQ-
702)
**-** Virtual streaming URL per session (YouTube/Zoom embed) (REQ-
703)
**-** Public event page displays full agenda with session details (REQ-
704)
**-** Attendee session save and personal schedule management (REQ-
602, 603)

**Sprint 6 — Analytics & Communications (Week 6)**

```
Week 6
```
```
Sprint 6
Analytics &
Comms
```
**-** Dashboard charts: registrations over time, revenue by ticket type
(REQ-1003)
**-** Ticket sales window: start and end date configuration per ticket type
(REQ-402)
**-** Custom registration fields: text, dropdown, checkbox with conditional
logic (REQ-404)
**-** Automated pre-event reminder emails to registered attendees (REQ-
902)
**-** Event status management: set event as cancelled or completed
(REQ-204)


### Phase 3 — Advanced Features (Weeks 7–8)

Goal: Deliver the differentiating Sponsor Management module, post-event automation, admin controls,
and production-grade security and performance hardening.

**Sprint 7 — Post-Event & Sponsor Features (Week 7)**

```
Week 7
```
```
Sprint 7
Post-Event &
Sponsors
```
**-** Automated thank-you email with feedback survey link post-event
(REQ-1201)
**-** PDF attendance certificate generation from custom template (REQ-
1202)
**-** Sponsor deliverable tracking module: log and monitor fulfillment
status
**-** Sponsor ROI summary report generation (synopsis objective)
**-** Event cloning: copy an existing event as a new template (REQ-206)

**Sprint 8 — Admin, Reporting & Hardening (Week 8)**

```
Week 8
```
```
Sprint 8
Admin &
Hardening
```
**-** Admin panel: view/manage all users, suspend accounts, change
roles (REQ-1301, 107)
**-** Admin global settings: configure payment gateway and email provider
(REQ-1303)
**-** Custom report generation: PDF/CSV on attendance, demographics
(REQ-1004)
**-** Webhook support for external systems (new registration, check-in
events) (REQ-1102)
**-** Security audit: Firestore rules, environment secrets in Docker,
HTTPS (NFR-05 to 10)
**-** Performance testing: validate 50+ concurrent users, Firestore index
review (NFR-03, 04)


## 4. SRS Requirements Coverage — MVP Scope

The table below maps the Must-priority requirements from SRS v1.0 to the sprint that delivers them.

### User Management & Authentication

```
Req ID Requirement Priority MVP?
```
```
REQ- 101 Sign up using email and password via Firebase Auth Must Yes
```
```
REQ- 102 Log in and log out securely Must Yes
```
```
REQ- 103 Role assigned at signup (organizer / attendee / admin) Must Yes
```
```
REQ- 104 Password reset via email Must Yes
```
```
REQ- 105 Update profile: name, phone, profile picture Should No
```
### Event Management

```
Req ID Requirement Priority MVP?
```
```
REQ- 201 Create event: name, description, dates, venue Must Yes
```
```
REQ- 202 Edit and delete draft events Must Yes
```
```
REQ- 203 Publish event to public listing Must Yes
```
```
REQ- 204 Set event as cancelled or completed Should No
```
```
REQ- 205 Upload cover image and branding colors Should No
```
```
REQ- 206 Clone an existing event as a template Could No
```
### Ticketing & Registration

```
Req ID Requirement Priority MVP?
```
```
REQ- 401 Create multiple ticket types per event Must Yes
```
```
REQ- 402 Ticket type sales start/end dates Should No
```
```
REQ- 403 Promo codes with discount, validity, and limits Should No
```
```
REQ- 405 Public users browse and view published events Must Yes
```
```
REQ- 406 Attendees select ticket type, quantity, apply promo code Must Yes
```
```
REQ- 407 Validate ticket availability and promo code eligibility Must Yes
```
```
REQ- 408 Confirmed payment generates unique QR code Must Yes
```

```
REQ- 409 Confirmation email with QR code sent to attendee Must Yes
```
### Payment Processing

```
Req ID Requirement Priority MVP?
```
```
REQ- 501 PayPal Checkout integration for payment processing Must Yes
```
```
REQ- 502 PayPal IPN/webhook confirmation on payment completion Must Yes
```
```
REQ- 503 Update registration status from webhook confirmation Must Yes
```
```
REQ- 504 Store payment records (amount, transaction ID, status) in Firestore Must Yes
```
```
REQ- 506 Handle payment failures gracefully and allow retry Should No
```
### Analytics & Reporting

```
Req ID Requirement Priority MVP?
```
```
REQ-
1001 Dashboard: total registrations, revenue, check-in count^ Must^ Yes^
```
```
REQ-
1002 View registrations list and export to CSV^ Must^ Yes^
```
```
REQ-
1003 Charts: registrations over time, sales by ticket type^ Should^ No^
```
```
REQ-
1004 Custom reports: PDF/Excel on attendance and demographics^ Should^ No^
```

## 5. Non-Functional Requirements

```
ID Requirement Sprint
```
```
NFR- 01 Public event list and registration page loads in < 3 seconds Sprint 2
```
```
NFR- 02 Registration and payment completes within 5 seconds (excl. PayPal redirect) Sprint 2
```
```
NFR- 03 Support at least 50 concurrent users in MVP; 500+ in later releases Sprint 8
```
```
NFR- 04 Firestore queries use indexes for < 200ms response on common queries Sprint 8
```
```
NFR- 05 All communication over HTTPS (SSL/TLS) Sprint 8
```
```
NFR- 06 Passwords managed by Firebase Auth; never stored in Firestore Sprint 1
```
```
NFR- 07 No card data stored in the system; handled entirely by PayPal Sprint 2
```
```
NFR- 08 Firestore security rules: organizers can only modify their own events Sprint 8
```
```
NFR- 09 Flask routes validate authentication and roleauthorization - based Sprint 1
```
```
NFR- 10 API keys and secrets stored in .env and Docker secrets never hard-coded —^ Sprint 1
```
```
NFR- 11 Responsive UI on desktop, tablet, and mobile (Bootstrap 5) Sprint 4
```
```
NFR- 15 System uptime target: 99.5% (excluding planned maintenance) Ongoing
```
```
NFR- 20 Code follows PEP 8 standards with documentation Ongoing
```
```
NFR- 21 Git version control with clear commit messages Sprint 1
```

## 6. Risks & Mitigations

```
Risk Severity Mitigation
```
```
PayPal IPN/webhook
setup complexity High^
```
```
Set up ngrok early in Sprint 2 to expose a local endpoint for
testing. Use PayPal Sandbox throughout development.
```
```
Firebase Firestore
security rules
inadequate before go-
live
```
```
High Draft security rules in Sprint 1 alongside the data model. Audit and finalize in Sprint 8 before any external access.
```
```
QR code scanner
requires HTTPS for
camera access on
mobile
```
```
Medium Ensure Docker deployment has SSL configured (even selfsigned) before Sprint 3 check-in testing. -
```
```
Firebase Auth and
Firestore service
outages
```
```
Medium
```
```
Implement retry logic (REQ-NFR-17) and graceful
degradation. Show user-friendly error states instead of blank
screens.
```
```
PayPal payment race
conditions (duplicate
registrations)
```
```
Medium Idempotency check on IPN webhook: validate transaction ID against Firestore before creating registration.
```
```
Scope creep delaying
MVP v1 High^
```
```
Strictly enforce MVP scope in Sprints 1–2. All 'Should' and
'Could' requirements are deferred to Sprint 3+.
```
```
Local Docker
deployment lacks
cloud DNS/SSL
```
```
Low Use static IP or mDNS hostname on the local network. Document SSL setup for organizer self-hosting.
```

## 7. Future Scope (Post 8-Week Cycle)

The following features are planned for releases beyond the 8-week cycle, as defined in SRS v1.
Section 6:

- Mobile application (React Native) with offline capabilities
- CRM integration: HubSpot and Salesforce sync via API (REQ-1101)
- SMS notifications via Twilio (REQ-904)
- Exhibitor management module with booth-level lead capture
- Multi-currency and multi-language support (REQ-505)
- Attendee ticket transfer (REQ-411) and waitlisting (REQ-412)
- Refund processing from the organizer dashboard (REQ-413)
- White-labeling: custom domains and full brand control
- Advanced networking: matchmaking and virtual meeting rooms
- AI-powered session recommendations and chatbot assistant
- Blockchain-based ticketing for anti-counterfeiting
- Sustainability tracking: carbon footprint estimation per event

## 8. References

[1] Jeny et al. (2022). A web-based college event management system and notification sender. ICIRCA,
IEEE.

[2] Liew & Tan (2021). QR code-based student attendance system. ICSGRC, IEEE.

[3] Shah et al. (2023). Event management systems (EMS). Journal of Applied Technology and
Innovation, 7(2).

[4] Chavan (2022). A review on Firebase for mobile application development. IJRASET, 10(1).

[5] Hadiwiyanti et al. (2022). Evaluation of campus EMS using system usability scale. ITIS, IEEE.

[6] Chen et al. (2023). Using Docker container technology for rapid deployment. ICEIB, IEEE.

[7] Ferreira et al. (2024). Accelerating cloud deployment through containerization. SMC, IEEE.


