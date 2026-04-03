# Arfath EMS – Incremental Release Plan (2-Week Iterations)

This document outlines the phased delivery of the Arfath Event Management System using **Python (Flask), Firebase (Firestore, Auth), PayPal, and Docker**. Each release is a fully functional version that builds upon the previous one, with new features added every two weeks. The goal is to have an MVP by Week 2 and a complete production-ready application by Week 8.

---

## Release 1 (Week 2) – MVP: Core Event Creation & Registration

**Goal**: Enable an organizer to create an event and an attendee to register, pay via PayPal, and receive a QR ticket via email.

### Functional Scope
- **User Authentication** (Firebase Auth)
  - Sign up / login with email/password.
  - Password reset via email.
  - User roles: organizer, attendee.
- **Event Management**
  - Organizers can create an event with name, description, start/end dates, venue selection (from pre-defined venues).
  - Organizers can add/edit/delete draft events.
  - Organizers can publish events to make them public.
  - Basic venue management: add, edit, delete (name, address).
- **Ticket Types**
  - Organizers can create ticket types for an event (name, price, quantity, max per order).
- **Public Event Browsing**
  - List of published events with basic details.
  - Event detail page showing description and ticket options.
- **Registration & Payment**
  - Attendees select ticket type and quantity.
  - Simple registration form (name, email, optional company).
  - Integration with **PayPal** (PayPal Checkout) to process payment.
  - On payment success (via PayPal IPN/webhook):
    - Create confirmed registration in Firestore.
    - Generate unique QR code hash.
    - Send confirmation email via SendGrid/SMTP with QR code (as attachment or inline).
- **Organizer Dashboard**
  - List of organizer's events with basic stats (registrations, revenue).
  - View registrations per event (attendee name, email, ticket type, quantity).
  - Export registrations to CSV.

### Technical Deliverables
- Flask application with routes and templates.
- Firebase Admin SDK integration for Firestore and Auth.
- PayPal REST API integration (with webhook endpoint).
- Dockerized application (Flask app, Nginx optional) with docker-compose for local server deployment.
- Basic responsive UI using Bootstrap 5.

---

## Release 2 (Week 4) – Agenda, Attendee Portal & Onsite Tools

**Goal**: Add session/speaker management, attendee self-service, and basic onsite check-in.

### New Features
- **Agenda & Sessions**
  - Organizers can create sessions (title, description, start/end time, track, location).
  - Organizers can manage speakers (add, edit, bio, photo) and assign them to sessions.
  - Public event page shows agenda with session details.
- **Attendee Portal**
  - Attendees log in to see their upcoming/past events.
  - Attendees can view their tickets (with QR code) and event agenda.
  - Attendees can mark sessions as "saved" to build a personal schedule.
- **Onsite Check-In**
  - Organizers have a check-in page with QR code scanner (web-based, using device camera).
  - Scanning a valid QR code marks the attendee as checked in (timestamp stored).
  - Check-in status visible in organizer dashboard.

### Technical Deliverables
- New Flask blueprints for agenda, speaker management, and check-in.
- Firestore collections for sessions, speakers, and attendee-session relationships.
- QR scanner integration using JavaScript library (e.g., Instascan or Html5Qrcode).
- Enhanced dashboard with check-in stats.

---

## Release 3 (Week 6) – Advanced Registration, Promo Codes & Hybrid Basics

**Goal**: Enhance registration with promo codes, custom fields, and basic virtual event support.

### New Features
- **Promo Codes**
  - Organizers can create promo codes (percentage or fixed discount, validity period, usage limits).
  - Attendees can apply promo codes during registration; discount calculated.
- **Custom Registration Fields**
  - Organizers can define custom fields (text, dropdown, checkbox) for the registration form.
  - Fields are stored per registration and displayed in the organizer dashboard.
- **Virtual Event Basics**
  - Sessions can include a streaming URL (YouTube, Zoom).
  - Attendees can click to join live stream from session detail.
  - Simple "Live Now" indicator for sessions.
- **Waitlist** (optional)
  - If ticket type sells out, attendees can join a waitlist.
  - Organizers can release additional tickets or notify waitlisted attendees.

### Technical Deliverables
- Promo code logic in Flask (validation, discount calculation).
- Dynamic form rendering based on custom fields schema stored in event.
- Streaming URL display and embed (iframe) on session page.
- Waitlist collection and notification mechanism.

---

## Release 4 (Week 8) – CRM, Networking, Analytics & Polish

**Goal**: Add CRM sync, attendee networking, advanced analytics, and final polish.

### New Features
- **CRM Integration** (HubSpot / Salesforce)
  - Organizers can configure CRM API keys.
  - On registration, attendee data is pushed to CRM.
  - Leads captured by exhibitors can be synced.
- **Networking Tools**
  - Attendee directory (opt-in) with search.
  - Attendees can send 1:1 meeting requests to others.
  - Meeting request management (accept/decline).
- **Advanced Analytics**
  - Real-time dashboard with charts: registrations over time, sales by ticket type, check-in rates.
  - Session popularity (number of attendees who saved/attended).
  - Export reports (PDF/Excel).
- **Post-Event Features**
  - Automated thank-you emails with feedback survey link.
  - Certificate generation (simple template) for attendees.
- **Polish & Performance**
  - Final UI/UX improvements.
  - Error handling and logging (Sentry).
  - Security hardening (Firestore rules, input validation).

### Technical Deliverables
- Integration with CRM APIs (using Python requests).
- Meeting request models and notification system.
- Chart.js for analytics graphs.
- PDF generation library (ReportLab or WeasyPrint) for certificates.
- Comprehensive testing and documentation.

---

## Release 5+ (Beyond Week 8) – Future Enhancements

After the initial 8 weeks, additional features can be added in subsequent 2-week cycles based on feedback and priorities. Potential features include:
- Mobile app (React Native) with offline mode.
- AI-powered session recommendations.
- Sponsors/exhibitors management with lead capture.
- Multi-currency support.
- White-labeling and custom domains.
- Accessibility and sustainability tracking.

---

## Development Approach

- **Iterative**: Each release is a working product that can be deployed and tested.
- **Testing**: Unit tests for critical functions; manual testing for UI flows.
- **Deployment**: Each release is tagged and deployed to the local server using Docker.
- **Documentation**: Updated with each release (user guides, API docs).

