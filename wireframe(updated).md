#  EMS – Wireframes / UI Mockups (Low-Fidelity)

This document provides low-fidelity wireframes (ASCII sketches) for all key pages of the  Event Management System. Each page includes a description of its purpose, the intended user, a simple ASCII representation, and an explanation of the UI elements and interactions.

---

## 1. Public Home Page (Event Listing)

**Purpose:** Allow visitors (unauthenticated users) to browse published events.  
**User:** Public / Guest

```
+---------------------------------------------------------------------+
|  [Logo]                       [Login] [Sign Up]                     |
+---------------------------------------------------------------------+
|  Search: [..................]  Filter by Date: [ v ]                |
+---------------------------------------------------------------------+
|  Upcoming Events                                                     |
|  +-----------------------------------+  +--------------------------+ |
|  | TechConf 2026                     |  | Marketing Summit         | |
|  | May 10-12, 2026  | City Hall      |  | Jun 5, 2026   | Online   | |
|  | [View Details] [Register]         |  | [View Details] [Register] | |
|  +-----------------------------------+  +--------------------------+ |
|  +-----------------------------------+  +--------------------------+ |
|  | Developer Days                     |  | Design Workshop          | |
|  | Jul 20-22, 2026 | Convention Ctr  |  | Aug 2, 2026   | Studio   | |
|  | [View Details] [Register]         |  | [View Details] [Register] | |
|  +-----------------------------------+  +--------------------------+ |
+---------------------------------------------------------------------+
|  Footer: About | Contact | Terms                                    |
+---------------------------------------------------------------------+
```

**Description:**
- **Top bar:** Logo, Login and Sign Up links.
- **Search & Filter:** Search box (by keyword) and dropdown filter (by date, category).
- **Event Cards:** Each event shows name, date, venue, and action buttons.
- **Footer:** Standard links.

**Interactions:**
- Clicking "View Details" goes to Event Detail page.
- Clicking "Register" takes user directly to registration (if only one ticket type) or to Event Detail.
- Login/Sign Up triggers authentication modal or redirect.

---

## 2. Event Detail Page

**Purpose:** Display full event information and allow ticket selection.  
**User:** Public / Guest / Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                               [Login] [Dashboard]          |
+---------------------------------------------------------------------+
|  < Back to Events                                                    |
+---------------------------------------------------------------------+
|  TechConf 2026                                                       |
|  May 10-12, 2026  |  City Hall, San Francisco                       |
|  +-----------------------------------------------------------------+ |
|  | Description: Annual technology conference featuring keynotes,   | |
|  | workshops, and networking.                                      | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  Ticket Types                                                         |
|  +---------------------------+  +---------------------------+         |
|  | Early Bird                |  | General Admission         |         |
|  | $50.00                    |  | $75.00                    |         |
|  | 50 tickets left           |  | 200 tickets left          |         |
|  | Max 4 per person          |  | Max 10 per person         |         |
|  | [Select]                  |  | [Select]                  |         |
|  +---------------------------+  +---------------------------+         |
|                                                                       |
|  Promo Code: [..........] [Apply]                                     |
|                                                                       |
|  [Proceed to Registration]                                            |
+---------------------------------------------------------------------+
```

**Description:**
- Event name, dates, venue, description.
- Ticket type cards with price, availability, max per order, and select button.
- Promo code input field.
- "Proceed to Registration" button (enabled after ticket selection).

**Interactions:**
- Clicking "Select" on a ticket type highlights it and updates the proceed button.
- Apply promo code validates and shows discount.
- Clicking "Proceed to Registration" goes to registration form (with chosen ticket pre-filled).

---

## 3. Registration Form Page

**Purpose:** Collect attendee details and finalize ticket selection.  
**User:** Attendee (logged in or guest)

```
+---------------------------------------------------------------------+
|  [Logo]                                                  [Cart]     |
+---------------------------------------------------------------------+
|  TechConf 2026 - Registration                                        |
+---------------------------------------------------------------------+
|  Selected Ticket: Early Bird x 2  (Total: $100.00)                  |
|  Promo Applied: SAVE10 (10% off) -> New Total: $90.00               |
+---------------------------------------------------------------------+
|  Your Information                                                     |
|  Full Name: [........................]                               |
|  Email:    [........................]                               |
|  Company:  [........................] (optional)                     |
|  Phone:    [........................] (optional)                     |
|                                                                       |
|  Custom Fields:                                                       |
|  +-----------------------------------------------------------------+ |
|  | Dietary Restrictions: [ v ]                                     | |
|  | T-Shirt Size: ( ) S  ( ) M  ( ) L                               | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  [Back to Event]                               [Proceed to Payment]  |
+---------------------------------------------------------------------+
```

**Description:**
- Top shows selected ticket summary and applied promo.
- Form fields: name, email, company, phone (basic).
- Custom fields dynamically rendered based on event settings.
- Navigation buttons.

**Interactions:**
- Form validation on client side.
- Proceed to Payment stores data temporarily and redirects to PayPal.

---

## 4. Payment Page (PayPal Integration)

**Purpose:** Redirect to PayPal or show PayPal button for payment.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                                                              |
+---------------------------------------------------------------------+
|  Complete Payment                                                     |
+---------------------------------------------------------------------+
|  Order Summary:                                                       |
|  Event: TechConf 2026                                                 |
|  Ticket: Early Bird x 2 ................ $100.00                     |
|  Discount (SAVE10) .................... -$10.00                      |
|  Total: ................................ $90.00                      |
+---------------------------------------------------------------------+
|  [Pay with PayPal]                                                    |
|                                                                       |
|  or                                                                   |
|                                                                       |
|  [Pay with Debit/Credit Card] (via PayPal)                           |
+---------------------------------------------------------------------+
|  By completing this purchase, you agree to our Terms.                |
+---------------------------------------------------------------------+
```

**Description:**
- Order summary with breakdown.
- PayPal button (integrated with PayPal Checkout) that opens popup or redirects.
- Alternative card payment link (provided by PayPal).

**Interactions:**
- Clicking PayPal initiates payment flow.
- After successful payment, user is redirected to Success page.

---

## 5. Registration Success Page

**Purpose:** Confirm registration and display QR code ticket.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                                                              |
+---------------------------------------------------------------------+
|  Thank You for Registering!                                          |
+---------------------------------------------------------------------+
|  Your registration for TechConf 2026 is confirmed.                   |
|                                                                       |
|  Your Ticket:                                                         |
|  +-----------------------------------------------------------------+ |
|  |                                                                 | |
|  |                      [QR CODE IMAGE]                           | |
|  |                                                                 | |
|  |  Name: John Doe                                                 | |
|  |  Ticket: Early Bird                                             | |
|  |  Order #: ORD-12345                                             | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  A copy has been sent to your email: john@example.com                |
|                                                                       |
|  [Download Ticket]  [Add to Calendar]  [View My Events]              |
+---------------------------------------------------------------------+
```

**Description:**
- Success message.
- QR code generated from hash.
- Attendee details and order number.
- Action buttons: download ticket (PDF/image), add to calendar, go to My Events.

**Interactions:**
- QR code can be scanned for check-in.
- Download ticket saves image/PDF.
- Add to Calendar generates .ics file.

---

## 6. Organizer Dashboard

**Purpose:** Overview of all events for an organizer.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                        Dashboard   Events   Reports   [User]|
+---------------------------------------------------------------------+
|  Welcome back, Alex!                                                 |
+---------------------------------------------------------------------+
|  Your Events                                                          |
|  +-----------------------------------+  +--------------------------+ |
|  | TechConf 2026                     |  | Marketing Summit         | |
|  | May 10-12, 2026                   |  | Jun 5, 2026              | |
|  | Registrations: 150/200 | $7,500   |  | Registrations: 45/100    | |
|  | Check-ins: 42                      |  | Check-ins: 0             | |
|  | [Manage] [View] [Reports]          |  | [Manage] [View] [Reports] | |
|  +-----------------------------------+  +--------------------------+ |
|  +-----------------------------------+                               |
|  | Developer Days                     |                               |
|  | Jul 20-22, 2026                   |                               |
|  | Registrations: 0/300  | $0        |                               |
|  | Check-ins: 0                      |                               |
|  | [Manage] [View] [Reports]          |                               |
|  +-----------------------------------+                               |
|                                                                       |
|  [+ Create New Event]                                                 |
+---------------------------------------------------------------------+
```

**Description:**
- Navigation tabs: Dashboard, Events, Reports, User profile.
- List of events (cards) with key metrics: registrations, revenue, check-ins.
- Action buttons: Manage (edit event), View (public view), Reports.
- Create New Event button.

**Interactions:**
- Clicking Manage goes to Event Management pages.
- Create New Event opens event creation form.

---

## 7. Create / Edit Event Page

**Purpose:** Form to create or edit event details.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                       [Save Draft] [Publish] |
+---------------------------------------------------------------------+
|  Create New Event                                                    |
+---------------------------------------------------------------------+
|  Basic Information                                                    |
|  Event Name:    [TechConf 2026.........................]            |
|  Description:   [Annual tech conference........................]    |
|                 [............................................]    |
|  Start Date:    [05/10/2026] [09:00 AM]                             |
|  End Date:      [05/12/2026] [06:00 PM]                             |
|  Venue:         [City Hall, San Francisco  v]  [Add New Venue]      |
|  Status:        ( ) Draft  ( ) Published                             |
|                                                                       |
|  [Save Changes]                                                       |
+---------------------------------------------------------------------+
```

**Description:**
- Form fields for event details.
- Venue dropdown with option to add new venue.
- Status radio buttons.
- Save Draft and Publish buttons (top right).

**Interactions:**
- Add New Venue opens a modal or redirects to venue management.
- On save, event is stored; if Publish, becomes public.

---

## 8. Manage Ticket Types Page

**Purpose:** Add/edit/delete ticket types for an event.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                    < Back to Event Overview |
+---------------------------------------------------------------------+
|  TechConf 2026 - Ticket Types                                        |
+---------------------------------------------------------------------+
|  Existing Ticket Types:                                               |
|  +----------------------+-------+----------+--------+------------+  |
|  | Name                 | Price | Quantity | Sold   | Actions    |  |
|  +----------------------+-------+----------+--------+------------+  |
|  | Early Bird           | $50   | 100      | 52     | [Edit] [Del]|  |
|  | General Admission    | $75   | 200      | 98     | [Edit] [Del]|  |
|  | VIP                  | $150  | 50       | 12     | [Edit] [Del]|  |
|  +----------------------+-------+----------+--------+------------+  |
|                                                                       |
|  [+ Add New Ticket Type]                                              |
+---------------------------------------------------------------------+
```

**Description:**
- Table listing existing ticket types with editable fields.
- Sold count automatically calculated from registrations.
- Edit/Delete actions.
- Button to add new ticket type (opens form).

**Interactions:**
- Edit opens pre-filled form to modify.
- Delete requires confirmation.
- Add New opens form.

---

## 9. View Registrations Page

**Purpose:** List all registrations for an event with export option.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                    < Back to Event Overview |
+---------------------------------------------------------------------+
|  TechConf 2026 - Registrations (150)                                 |
|  [Export CSV] [Export PDF]                                           |
+---------------------------------------------------------------------+
|  Search: [..............]  Filter: [All  v]                         |
+---------------------------------------------------------------------+
|  +----+-----------------+-----------------+----------+---------+---+|
|  | #  | Name            | Email           | Ticket   | Status  |   ||
|  +----+-----------------+-----------------+----------+---------+---+|
|  | 1  | John Doe        | john@ex.com     | Early    | Checked |   ||
|  | 2  | Jane Smith      | jane@ex.com     | General  | Confirmed|  ||
|  | 3  | Bob Johnson     | bob@ex.com      | VIP      | Pending  |   ||
|  +----+-----------------+-----------------+----------+---------+---+|
|                                                                       |
|  [Prev] 1 2 3 4 ... [Next]                                           |
+---------------------------------------------------------------------+
```

**Description:**
- Registration count, export buttons.
- Search and filter by ticket type/status.
- Table with attendee details, ticket, status (confirmed, checked-in, etc.).
- Pagination.

**Interactions:**
- Export CSV downloads file.
- Click on row to view registration details (optional).

---

## 10. Attendee Portal – My Events

**Purpose:** Attendee dashboard showing registered events.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                                         [Profile] [Logout]  |
+---------------------------------------------------------------------+
|  My Events                                                           |
+---------------------------------------------------------------------+
|  Upcoming:                                                            |
|  +-----------------------------------+  +--------------------------+ |
|  | TechConf 2026                     |  | Marketing Summit         | |
|  | May 10-12, 2026                   |  | Jun 5, 2026              | |
|  | [View Ticket] [Agenda] [Join]     |  | [View Ticket] [Agenda]   | |
|  +-----------------------------------+  +--------------------------+ |
|                                                                       |
|  Past:                                                                |
|  +-----------------------------------+                               |
|  | Developer Days 2025                |                               |
|  | Jul 20-22, 2025                   |                               |
|  | [Certificate] [Feedback]           |                               |
|  +-----------------------------------+                               |
+---------------------------------------------------------------------+
```

**Description:**
- Split into Upcoming and Past events.
- For each event: action buttons – View Ticket, Agenda, Join (if live), Certificate, Feedback.

**Interactions:**
- View Ticket shows ticket with QR.
- Agenda goes to event agenda page.
- Join opens live stream if session is live.

---

## 11. Attendee Ticket View

**Purpose:** Display the QR ticket for an event.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to My Events    |
+---------------------------------------------------------------------+
|  TechConf 2026 - Your Ticket                                         |
+---------------------------------------------------------------------+
|  +-----------------------------------------------------------------+ |
|  |                                                                 | |
|  |                      [QR CODE IMAGE]                           | |
|  |                                                                 | |
|  |  Name: John Doe                                                 | |
|  |  Ticket: Early Bird                                             | |
|  |  Order #: ORD-12345                                             | |
|  |  Status: Confirmed                                              | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  [Download Ticket]  [Email Ticket]  [Add to Calendar]                |
+---------------------------------------------------------------------+
```

**Description:**
- Large QR code.
- Attendee and order details.
- Action buttons.

**Interactions:**
- Download saves image.
- Email Ticket resends email.

---

## 12. Check-In Page (Organizer)

**Purpose:** Scan attendee QR codes for onsite check-in.  
**User:** Organizer / Staff

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Dashboard    |
+---------------------------------------------------------------------+
|  Check-In: TechConf 2026                                             |
+---------------------------------------------------------------------+
|  [Camera Feed]                                                        |
|  +-----------------------------------------------------------------+ |
|  |                                                                 | |
|  |                 [Camera View Here]                             | |
|  |                                                                 | |
|  |                 [Switch Camera]                                 | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  Manual Entry:                                                        |
|  Enter Order # or Email: [......................] [Check In]        |
|                                                                       |
|  Recent Check-Ins:                                                    |
|  +-----------------+---------------------+----------+              |
|  | John Doe        | 10:32 AM            | Success  |              |
|  | Jane Smith      | 10:30 AM            | Success  |              |
|  +-----------------+---------------------+----------+              |
+---------------------------------------------------------------------+
```

**Description:**
- Live camera feed for QR scanning (using browser's getUserMedia).
- Manual entry field for backup.
- Recent check-ins log.

**Interactions:**
- On successful scan, attendee is marked checked in, log updates, feedback (sound/visual).
- Manual entry validates and checks in.

---

## 13. Agenda / Sessions View (Organizer)

**Purpose:** Manage sessions for an event.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Event        |
+---------------------------------------------------------------------+
|  TechConf 2026 - Agenda                                              |
|  [Add Session] [Import] [Export]                                     |
+---------------------------------------------------------------------+
|  Day 1 - May 10, 2026                                                |
|  +----+----------+-----------------+----------+--------+----------+|
|  |Time| Track A  | Track B         | Track C  | Speaker | Actions  ||
|  +----+----------+-----------------+----------+--------+----------+|
|  |9:00| Keynote  | -               | -        | John S. | [E][D]   ||
|  |10:00| AI/ML   | Web Dev         | UX       | Alice,  | [E][D]   ||
|  |    | Workshop | Workshop        | Panel    | Bob     |          ||
|  +----+----------+-----------------+----------+--------+----------+|
|                                                                       |
|  Day 2 - May 11, 2026                                                |
|  +----+----------+-----------------+----------+--------+----------+|
|  ...                                                                  |
+---------------------------------------------------------------------+
```

**Description:**
- Sessions organized by day and track.
- Add Session button opens form.
- Edit/Delete icons per session.
- Import/Export (CSV) options.

**Interactions:**
- Click Add Session opens modal with fields: title, description, start/end, track, location, speaker(s).

---

## 14. Speaker Management Page

**Purpose:** Manage speaker profiles.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Agenda       |
+---------------------------------------------------------------------+
|  Speakers                                                             |
|  [+ Add Speaker]                                                      |
+---------------------------------------------------------------------+
|  +-----------------+-----------------+--------------+--------------+|
|  | Name            | Email           | Sessions     | Actions      ||
|  +-----------------+-----------------+--------------+--------------+|
|  | Dr. Sarah Chen  | sarah@ex.com    | Keynote, AI  | [Edit] [Del] ||
|  | Mark Johnson    | mark@ex.com     | Web Dev Wkshp| [Edit] [Del] ||
|  +-----------------+-----------------+--------------+--------------+|
+---------------------------------------------------------------------+
```

**Description:**
- List of speakers with session count.
- Add/Edit/Delete actions.

**Interactions:**
- Add/Edit opens form with name, bio, photo, social links, email, phone.

---

## 15. Promo Code Management Page

**Purpose:** Create and manage promo codes for an event.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Event        |
+---------------------------------------------------------------------+
|  Promo Codes - TechConf 2026                                         |
|  [+ Add Promo Code]                                                  |
+---------------------------------------------------------------------+
|  +------+------------+-------+-------------+--------+-------------+|
|  | Code | Discount   | Valid | Uses        | Status | Actions     ||
|  +------+------------+-------+-------------+--------+-------------+|
|  |SAVE10| 10% off    | 5/1-5/9| 45/100 used| Active | [E][D]      ||
|  |VIP20 | $20 off    | 5/1-5/9| 12/50 used | Active | [E][D]      ||
|  +------+------------+-------+-------------+--------+-------------+|
+---------------------------------------------------------------------+
```

**Description:**
- Table of promo codes with details.
- Add button opens form with code, discount type, value, validity, max uses.

**Interactions:**
- Edit/Delete actions.

---

## 16. Custom Fields Builder

**Purpose:** Design custom registration fields.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Event        |
+---------------------------------------------------------------------+
|  Custom Registration Fields - TechConf 2026                          |
|  [Add Field] [Preview Form]                                          |
+---------------------------------------------------------------------+
|  Current Fields:                                                      |
|  +---------------------+----------+--------+--------+-------------+|
|  | Field Label         | Type     | Required| Options | Actions    ||
|  +---------------------+----------+--------+--------+-------------+|
|  | Dietary Restrictions| Dropdown | No     | Veg,Non,| [E][D] [↑][↓]||
|  | T-Shirt Size        | Radio    | Yes    | S,M,L,XL| [E][D] [↑][↓]||
|  | LinkedIn Profile    | Text     | No     | -      | [E][D] [↑][↓]||
|  +---------------------+----------+--------+--------+-------------+|
+---------------------------------------------------------------------+
```

**Description:**
- List of custom fields with properties.
- Drag handles (↑↓) to reorder.
- Add Field opens modal with field type (text, dropdown, radio, checkbox), label, required, options (if applicable).

**Interactions:**
- Preview Form shows how the registration form will look.

---

## 17. Analytics Dashboard

**Purpose:** Visualize event performance.  
**User:** Organizer

```
+---------------------------------------------------------------------+
|  [Logo]                        Dashboard   Events   Reports   [User]|
+---------------------------------------------------------------------+
|  Analytics: TechConf 2026                                            |
|  [Date Range: Last 7 days  v]  [Refresh]                            |
+---------------------------------------------------------------------+
|  +------------------------+   +------------------------+           |
|  | Registrations          |   | Revenue                |           |
|  | 150 total (+12 today)  |   | $7,500 total (+$600)   |           |
|  +------------------------+   +------------------------+           |
|  +------------------------+   +------------------------+           |
|  | Check-ins              |   | Avg. Ticket Price      |           |
|  | 42 today               |   | $50.00                 |           |
|  +------------------------+   +------------------------+           |
|                                                                       |
|  Registrations Over Time                                              |
|  +-----------------------------------------------------------------+ |
|  |                    Chart (line graph)                           | |
|  |   [#######################################]                     | |
|  |   [###########]                                                | |
|  +-----------------------------------------------------------------+ |
|                                                                       |
|  Sales by Ticket Type                                                 |
|  +-----------------------------------------------------------------+ |
|  | Early Bird: 52 ($2,600)   ████████████                         | |
|  | General:     98 ($7,350)   ████████████████████████             | |
|  | VIP:         12 ($1,800)   ███                                 | |
|  +-----------------------------------------------------------------+ |
+---------------------------------------------------------------------+
```

**Description:**
- Key metric cards.
- Charts (line, bar) for registrations over time and sales breakdown.
- Date range filter.

**Interactions:**
- Click on chart elements to drill down.

---

## 18. Networking / Attendee Directory

**Purpose:** Allow attendees to find and connect with each other.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  [Logo]                                      < Back to Event        |
+---------------------------------------------------------------------+
|  Attendee Directory - TechConf 2026                                  |
|  Search: [..................]  Filter by Company: [ v ]             |
+---------------------------------------------------------------------+
|  +-----------------+-----------------+----------------+-----------+|
|  | Name            | Company         | Job Title      | Actions   ||
|  +-----------------+-----------------+----------------+-----------+|
|  | John Doe        | Acme Inc        | Developer      | [Message]  ||
|  | Jane Smith      | Beta Corp       | Marketing Mgr | [Message]  ||
|  | Bob Johnson     | Gamma Ltd       | CEO           | [Message]  ||
|  +-----------------+-----------------+----------------+-----------+|
|                                                                       |
|  [Prev] 1 2 3 4 ... [Next]                                           |
+---------------------------------------------------------------------+
```

**Description:**
- Directory of attendees who opted in.
- Search and filter.
- Message button sends meeting request.

**Interactions:**
- Click Message opens meeting request modal.

---

## 19. Meeting Request Modal

**Purpose:** Send a 1:1 meeting request to another attendee.  
**User:** Attendee

```
+---------------------------------------------------------------------+
|  Send Meeting Request                                                |
+---------------------------------------------------------------------+
|  To: Jane Smith                                                      |
|  Proposed Time: [05/11/2026] [03:00 PM]                             |
|  Message (optional):                                                 |
|  [Hello, I'd love to discuss...                                   ]  |
|  [...............................................................]  |
|                                                                       |
|  [Cancel]                              [Send Request]               |
+---------------------------------------------------------------------+
```

**Description:**
- Pre-filled recipient.
- Date/time picker.
- Message box.
- Send/Cancel buttons.

**Interactions:**
- On send, request is stored; recipient gets notification.

---

## 20. Admin Panel – User Management

**Purpose:** Administer users and system settings.  
**User:** Admin

```
+---------------------------------------------------------------------+
|  [Logo]                        Dashboard   Users   Settings   [Admin]|
+---------------------------------------------------------------------+
|  User Management                                                      |
|  Search: [..................]  Role: [All  v]                        |
+---------------------------------------------------------------------+
|  +----+-----------------+-----------------+--------+-------+-------+|
|  | ID | Name            | Email           | Role   | Status| Actions||
|  +----+-----------------+-----------------+--------+-------+-------+|
|  | 1  | Alex Organizer  | alex@org.com    | Org    | Active| [E][S] ||
|  | 2  | John Attendee   | john@att.com    | Att    | Active| [E][S] ||
|  | 3  | Suspended User  | bad@user.com    | Att    | Susp. | [E][A] ||
|  +----+-----------------+-----------------+--------+-------+-------+|
|                                                                       |
|  [Prev] 1 2 3 ... [Next]                                             |
+---------------------------------------------------------------------+
```

**Description:**
- Table of all users.
- Actions: Edit (change role), Suspend/Activate, Delete.

**Interactions:**
- Edit opens user details modal.
- Suspend disables login.

---

These wireframes cover the main pages of the  EMS, providing a visual blueprint for developers and stakeholders. They are intentionally low-fidelity to focus on layout and functionality rather than visual design.