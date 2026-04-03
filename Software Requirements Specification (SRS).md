# Software Requirements Specification (SRS)  
## Arfath Event Management System (EMS)  
**Version 1.0**

---

## Document Control

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2026-03-13 | Arfath EMS Team | Initial SRS – covers MVP and full system requirements |

---

## Table of Contents

1. [Introduction](#1-introduction)  
   1.1 Purpose  
   1.2 Scope  
   1.3 Definitions and Acronyms  
   1.4 References  
2. [Overall Description](#2-overall-description)  
   2.1 Product Perspective  
   2.2 User Characteristics  
   2.3 Assumptions and Dependencies  
   2.4 Use Case Overview  
3. [System Features and Requirements](#3-system-features-and-requirements)  
   3.1 User Management & Authentication  
   3.2 Event Management  
   3.3 Venue Management  
   3.4 Ticketing & Registration  
   3.5 Payment Processing  
   3.6 Attendee Portal  
   3.7 Agenda & Session Management  
   3.8 Onsite Check-In  
   3.9 Marketing & Communication  
   3.10 Analytics & Reporting  
   3.11 Integrations  
   3.12 Post-Event Features  
   3.13 Admin Functions  
4. [Non-Functional Requirements](#4-non-functional-requirements)  
   4.1 Performance  
   4.2 Security  
   4.3 Usability  
   4.4 Reliability  
   4.5 Scalability  
   4.6 Maintainability  
5. [Constraints and Assumptions](#5-constraints-and-assumptions)  
6. [Future Scope](#6-future-scope)  
7. [Appendices](#7-appendices) (Optional)

---

## 1. Introduction

### 1.1 Purpose
This Software Requirements Specification (SRS) document defines the complete set of requirements for the Arfath Event Management System (EMS). It serves as a communication medium between stakeholders, developers, and testers, and provides the basis for design, implementation, and validation. The document covers both the Minimum Viable Product (MVP) to be delivered in two weeks and the full system planned over subsequent iterations.

### 1.2 Scope
Arfath EMS is a web‑based platform that enables event organizers to create and manage events, sell tickets, engage attendees, and analyze performance. Attendees can discover events, register, pay, and participate both onsite and virtually. The system is built using Python (Flask), Firebase Firestore, Firebase Authentication, PayPal, and Docker, and is designed for deployment on a local server.

The MVP focuses on core functionality: event creation, ticket sales with PayPal, email confirmation with QR codes, and a basic organizer dashboard. Subsequent releases add agenda management, attendee portals, onsite check‑in, promotional tools, CRM integration, networking, and advanced analytics.

### 1.3 Definitions and Acronyms

| Term | Definition |
|------|------------|
| EMS | Event Management System |
| MVP | Minimum Viable Product |
| Organizer | User who creates and manages events |
| Attendee | User who registers and participates in events |
| Admin | System administrator with full privileges |
| Speaker | Individual presenting at a session |
| Sponsor | Organization financially supporting an event |
| Exhibitor | Company with a booth at an event |
| QR Code | Quick Response code used for ticket validation and check‑in |
| CRM | Customer Relationship Management (e.g., Salesforce, HubSpot) |
| Firestore | NoSQL document database provided by Firebase |
| Firebase Auth | Authentication service by Firebase |
| Flask | Python web framework used for the backend |
| PayPal | Payment gateway for processing transactions |
| Docker | Containerization platform for deployment |

### 1.4 References
- Arfath EMS Feature List (previous document)
- Arfath EMS ER Diagram (Firestore logical model)
- Arfath EMS Sprint Roadmap (2‑week increments)
- Firebase Documentation
- PayPal Developer Documentation

---

## 2. Overall Description

### 2.1 Product Perspective
Arfath EMS is a new, standalone system. It replaces manual event management processes with an integrated digital platform. The system interacts with external services:
- **Firebase Authentication** – for user identity.
- **Firebase Firestore** – for data persistence.
- **PayPal** – for payment processing.
- **SendGrid / SMTP** – for email notifications.
- **YouTube / Zoom** – for embedding live streams (future).
- **HubSpot / Salesforce** – for CRM synchronization (future).

The system is deployed using Docker containers on a local server, accessible via a local network.

### 2.2 User Characteristics

| Stakeholder | Description | Key Responsibilities / Needs |
|-------------|-------------|------------------------------|
| **Organizer** | Event planners who create and manage events. | Create events, configure tickets, view registrations, manage agenda, check in attendees, run reports. |
| **Attendee** | End‑users who register and attend events. | Discover events, register, pay, access agenda, network, provide feedback. |
| **Admin** | Platform‑level superuser. | Manage users, oversee system settings, handle support issues. |
| **Speaker** | Individuals presenting at sessions. | Provide profile, upload materials, engage with attendees (future). |
| **Sponsor** | Organizations sponsoring events. | Receive recognition, access leads (future). |
| **Exhibitor** | Companies with booths. | Manage booth, capture attendee leads (future). |

### 2.3 Assumptions and Dependencies
- Users have internet access and a modern web browser.
- Organizers will provide accurate event details (dates, venue, sessions).
- Firebase services (Auth, Firestore) are available and reliable.
- PayPal merchant account is configured and active.
- Email service (SendGrid or SMTP) is properly set up.
- The local server running Docker has sufficient resources (CPU, RAM, disk).
- Docker and Docker Compose are installed on the deployment machine.

### 2.4 Use Case Overview
The following diagram illustrates the high‑level use cases for the system. (A textual representation is provided below; a graphical diagram can be added in the final document.)

**Actors**: Organizer, Attendee, Admin, PayPal (external), Email Service (external).

**Key Use Cases**:
- UC1: Register / Login
- UC2: Create Event (Organizer)
- UC3: Manage Ticket Types (Organizer)
- UC4: Browse Events (Attendee)
- UC5: Register for Event (Attendee)
- UC6: Process Payment (PayPal)
- UC7: View Registrations (Organizer)
- UC8: Check In Attendee (Organizer)
- UC9: Manage Agenda (Organizer)
- UC10: Save Sessions (Attendee)
- UC11: Generate Reports (Organizer)
- UC12: Sync with CRM (Organizer/Admin)

---

## 3. System Features and Requirements

Requirements are tagged with a unique ID and a priority (**Must**, **Should**, **Could**). The **MVP** column indicates whether the feature is included in the initial two‑week release.

### 3.1 User Management & Authentication

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑101 | Users shall be able to sign up using email and password (via Firebase Auth). | Must | Yes |
| REQ‑102 | Users shall be able to log in and log out securely. | Must | Yes |
| REQ‑103 | Users shall have a role (organizer, attendee, admin) assigned at signup (default attendee). | Must | Yes |
| REQ‑104 | Users shall be able to reset their password via email. | Must | Yes |
| REQ‑105 | Users shall be able to update their profile (name, phone, profile picture). | Should | No |
| REQ‑106 | Organizers can invite team members with role‑based permissions (view‑only, editor). | Could | No |
| REQ‑107 | Admins can view and manage all users (suspend, change roles). | Should | No |

### 3.2 Event Management

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑201 | Organizers shall be able to create an event with name, description, start/end dates, and venue. | Must | Yes |
| REQ‑202 | Organizers shall be able to edit and delete draft events. | Must | Yes |
| REQ‑203 | Organizers shall be able to publish an event to make it publicly visible. | Must | Yes |
| REQ‑204 | Organizers shall be able to set an event as cancelled or completed. | Should | No |
| REQ‑205 | Organizers shall be able to upload a cover image and set branding colors. | Should | No |
| REQ‑206 | Organizers shall be able to clone an existing event to use as a template. | Could | No |

### 3.3 Venue Management

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑301 | Organizers shall be able to add, edit, and delete venues (name, address, capacity, contact info). | Must | Yes |
| REQ‑302 | Venues can optionally have a floor plan image (upload). | Should | No |

### 3.4 Ticketing & Registration

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑401 | Organizers shall be able to create multiple ticket types per event (name, price, quantity, max per order). | Must | Yes |
| REQ‑402 | Ticket types can have sales start/end dates. | Should | No |
| REQ‑403 | Organizers shall be able to create promo codes (percentage/fixed discount, validity period, usage limits). | Should | No |
| REQ‑404 | Organizers shall be able to define custom registration fields (text, dropdown, checkbox) with conditional logic. | Should | No |
| REQ‑405 | Public users shall be able to browse published events and view details. | Must | Yes |
| REQ‑406 | Attendees shall be able to select ticket type, quantity, and apply promo code during registration. | Must | Yes |
| REQ‑407 | System shall validate ticket availability and promo code eligibility. | Must | Yes |
| REQ‑408 | On successful payment, system shall create a confirmed registration and generate a unique QR code. | Must | Yes |
| REQ‑409 | Attendees shall receive a confirmation email with QR code (as image or link). | Must | Yes |
| REQ‑410 | Attendees shall be able to view their tickets (with QR code) in their profile. | Should | No |
| REQ‑411 | Attendees shall be able to transfer tickets to another person (if allowed by organizer). | Could | No |
| REQ‑412 | System shall support waitlisting when tickets are sold out. | Could | No |
| REQ‑413 | Organizers shall be able to process refunds (partial/full) from the dashboard. | Could | No |

### 3.5 Payment Processing

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑501 | System shall integrate with PayPal (PayPal Checkout) to process payments. | Must | Yes |
| REQ‑502 | On payment completion, PayPal shall send an IPN/webhook to confirm the transaction. | Must | Yes |
| REQ‑503 | System shall update registration status based on webhook confirmation. | Must | Yes |
| REQ‑504 | Payment records (amount, transaction ID, status) shall be stored in Firestore. | Must | Yes |
| REQ‑505 | System shall support multiple currencies (future). | Could | No |
| REQ‑506 | System shall handle payment failures gracefully and allow retry. | Should | No |

### 3.6 Attendee Portal

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑601 | Attendees shall have a dashboard showing their upcoming and past events. | Must | No (MVP+1) |
| REQ‑602 | Attendees shall be able to view event agenda and mark sessions as “saved”. | Should | No |
| REQ‑603 | Attendees shall be able to access session details (speakers, description, streaming link). | Should | No |
| REQ‑604 | Attendees shall be able to update their profile information. | Should | No |

### 3.7 Agenda & Session Management

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑701 | Organizers shall be able to create, edit, and delete sessions (title, description, start/end, track, location). | Should | No |
| REQ‑702 | Organizers shall be able to manage speakers (add, edit, bio, photo) and assign them to sessions. | Should | No |
| REQ‑703 | Organizers shall be able to provide a streaming URL for virtual sessions. | Should | No |
| REQ‑704 | Public event page shall display the agenda with session details. | Should | No |
| REQ‑705 | Attendees shall be able to view live stream from session page (embed or link). | Should | No |

### 3.8 Onsite Check‑In

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑801 | Organizers shall have a web‑based check‑in page with QR code scanner (using device camera). | Should | No |
| REQ‑802 | Scanning a valid QR code shall mark the attendee as checked in (timestamp, staff ID). | Should | No |
| REQ‑803 | Check‑in status shall be visible in the organizer dashboard. | Should | No |
| REQ‑804 | Organizers shall be able to manually check in attendees (search by name/email). | Should | No |
| REQ‑805 | System shall support offline check‑in with sync later (future). | Could | No |

### 3.9 Marketing & Communication

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑901 | Organizers shall be able to send email campaigns to segmented attendee lists (by ticket type, check‑in status). | Could | No |
| REQ‑902 | System shall send automated emails: registration confirmation, reminders, post‑event thanks. | Must (confirmation) | Yes (confirmation) |
| REQ‑903 | Attendees shall be able to manage their notification preferences. | Should | No |
| REQ‑904 | System shall support SMS notifications (future). | Could | No |

### 3.10 Analytics & Reporting

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑1001 | Organizers shall see a dashboard with key metrics: total registrations, revenue, check‑ins. | Must (basic) | Yes (counts) |
| REQ‑1002 | Organizers shall be able to view a list of registrations and export to CSV. | Must | Yes |
| REQ‑1003 | Organizers shall be able to view charts (registrations over time, sales by ticket type). | Should | No |
| REQ‑1004 | Organizers shall be able to generate custom reports (PDF/Excel) on attendance, demographics, session popularity. | Should | No |
| REQ‑1005 | System shall provide predictive insights (popular sessions, attrition risk) – future. | Could | No |

### 3.11 Integrations

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑1101 | System shall sync attendee data with CRM (HubSpot/Salesforce) via API (organizer configurable). | Could | No |
| REQ‑1102 | System shall provide webhooks for external systems to subscribe to events (new registration, check‑in). | Could | No |
| REQ‑1103 | System shall allow embedding of YouTube/Zoom live streams. | Should | No |

### 3.12 Post‑Event Features

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑1201 | Organizers shall be able to send automated thank‑you emails with feedback survey link. | Should | No |
| REQ‑1202 | System shall generate attendance certificates (custom template) and email to attendees. | Could | No |
| REQ‑1203 | Event data shall be archived and available for historical analysis. | Should | No |

### 3.13 Admin Functions

| ID | Requirement | Priority | MVP |
|----|-------------|----------|-----|
| REQ‑1301 | Admins shall be able to view and manage all users (suspend, change roles). | Should | No |
| REQ‑1302 | Admins shall have access to system logs and audit trails. | Could | No |
| REQ‑1303 | Admins shall be able to configure global settings (payment gateways, email providers). | Should | No |

---

## 4. Non‑Functional Requirements

### 4.1 Performance
| ID | Requirement | Target |
|----|-------------|--------|
| NFR‑01 | Page load time for public event list and registration page shall be < 3 seconds (under normal load). | |
| NFR‑02 | Registration and payment flow shall complete within 5 seconds (excluding PayPal redirect). | |
| NFR‑03 | The system shall support at least 50 concurrent users during MVP, scaling to 500+ in later releases. | |
| NFR‑04 | Firestore queries shall use appropriate indexes to ensure response times < 200ms for common queries. | |

### 4.2 Security
| ID | Requirement |
|----|-------------|
| NFR‑05 | All communication shall be over HTTPS (SSL/TLS). |
| NFR‑06 | Passwords shall be securely hashed via Firebase Auth (never stored in Firestore). |
| NFR‑07 | Payment information shall be handled directly by PayPal; no card data stored in our system. |
| NFR‑08 | Firebase Firestore security rules shall restrict access: organizers can only modify their own events; attendees can only read their own registrations. |
| NFR‑09 | Flask routes shall validate user authentication and authorization (role‑based access). |
| NFR‑10 | Environment variables (API keys, secrets) shall not be hard‑coded; use `.env` and Docker secrets. |

### 4.3 Usability
| ID | Requirement |
|----|-------------|
| NFR‑11 | The user interface shall be responsive and work on desktop, tablet, and mobile browsers. |
| NFR‑12 | Forms shall provide clear validation messages and error handling. |
| NFR‑13 | The organizer dashboard shall present data in an intuitive, visual manner (charts, summaries). |
| NFR‑14 | The system shall support keyboard navigation and screen readers for basic accessibility. |

### 4.4 Reliability
| ID | Requirement |
|----|-------------|
| NFR‑15 | System uptime target: 99.5% (excluding planned maintenance). |
| NFR‑16 | Critical errors (payment failures, registration issues) shall be logged and alerted. |
| NFR‑17 | Firestore and Firebase Auth are managed services with high availability; application shall handle temporary outages gracefully (e.g., retry logic). |

### 4.5 Scalability
| ID | Requirement |
|----|-------------|
| NFR‑18 | The application shall be stateless (session data stored in Firebase or client‑side cookies) to allow horizontal scaling. |
| NFR‑19 | Firestore automatically scales; design shall avoid hotspots (e.g., using distributed counters for registration counts). |

### 4.6 Maintainability
| ID | Requirement |
|----|-------------|
| NFR‑20 | Code shall follow PEP 8 standards and be well‑documented. |
| NFR‑21 | The project shall use Git for version control with clear commit messages. |
| NFR‑22 | Configuration shall be externalized (environment variables, Docker Compose). |
| NFR‑23 | Unit tests shall cover critical business logic (registration, promo codes, check‑in). |

---

## 5. Constraints and Assumptions

### 5.1 Constraints
- Development must use Python (Flask), Firebase (Auth, Firestore), PayPal, and Docker.
- The MVP must be delivered within two weeks.
- The system is deployed on a local server using Docker; no cloud hosting initially.
- All data must reside in Firestore (NoSQL); relational joins are not available.

### 5.2 Assumptions
- Organizers have basic technical proficiency to manage events.
- Attendees have access to email and a modern browser.
- PayPal merchant account is active and configured to send IPNs to the server.
- The local server has a static IP or hostname accessible on the local network.
- Docker and Docker Compose are installed and properly configured.

---

## 6. Future Scope

The following features are planned for releases beyond the initial 8‑week cycle:
- Mobile application (React Native) with offline capabilities.
- AI‑powered session recommendations and chatbots.
- Sponsors and exhibitors management with lead capture (QR scanning at booths).
- Multi‑currency and multi‑language support.
- White‑labeling: custom domains and full branding control.
- Advanced networking: matchmaking, virtual meeting rooms.
- Sustainability tracking (carbon footprint estimation).
- Accessibility enhancements (WCAG 2.1 AA compliance).
- Blockchain‑based ticketing for anti‑counterfeiting.

---

## 7. Appendices

(Optional – can include use case diagrams, wireframe references, or detailed data dictionary.)

