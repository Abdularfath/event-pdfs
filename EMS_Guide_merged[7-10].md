###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

# Day 7 — Setup Guide

## Ticket Type Management

Sprint 1 Final Day | Wireframe Page 8 | 4 Parts | ~5 hours

## 🏆 SPRINT 1 COMPLETE

```
Week 1 of 2 done — MVP v1 foundation is complete
Days 1–7 | 7 features shipped | Sprint 2 starts tomorrow
```
**Part A**

```
Update validators.py — add validate_ticket_type()
(~20 min)
```
**Part B**

```
Write app/routes/tickets.py — ticket type CRUD
routes (~75 min)
```
**Part C**

```
Write templates/organizer/tickets/ — list and form
pages (~60 min)
```
**Part D**

```
Wire up, add ticket link to event edit page, rebuild &
test (~30 min)
```
**End goal**

```
Organizer can add Early Bird, General, VIP tickets
to any event. Tickets appear on public event detail
page immediately.
```

###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

## Overview — What we are building today

Ticket types are stored as a Firestore subcollection under each event:
/events/{event_id}/ticket_types/{ticket_type_id}. Today you build the full CRUD for this subcollection —

the last feature needed to complete Sprint 1.

After today, the test event you created manually in Firebase Console on Day 5 can be deleted — the

organizer can now manage everything from the web app.

**Wireframe reference**

Wireframe page 8 shows a table of ticket types with columns: Name, Price, Quantity, Sold, Actions
(Edit/Delete). It also has an Add New Ticket Type button. Today's pages match this layout exactly.

##### New files today

**event-management-system/**
├── app/routes/
│ └── tickets.py ← NEW — ticket type CRUD
├── app/utils/validators.py ← UPDATE — add
validate_ticket_type()
├── templates/organizer/
│ └── tickets/ ← NEW folder
│ ├── list.html ← NEW — ticket types table
│ └── form.html ← NEW — shared add/edit form
├── templates/organizer/events/
│ └── form.html ← UPDATE — add Manage Tickets link
└── app.py ← UPDATE — register tickets
blueprint


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

**Part A**

#### Update app/utils/validators.py — Add validate_ticket_type()

Open app/utils/validators.py in VS Code. Replace the validate_ticket_type stub with this full
implementation:

def validate_ticket_type(data):
"""
Validate ticket type form data.
Returns list of error strings. Empty = passed.
"""
errors = []

name = data.get('name','').strip()
price = data.get('price','').strip()
qty_total = data.get('quantity_total','').strip()
max_order = data.get('max_per_order','').strip()

# Name
if not name:
errors.append('Ticket name is required.')
elif len(name) > 80 :
errors.append('Ticket name must be under 80 characters.')

# Price
if price == '':
errors.append('Price is required. Enter 0 for free tickets.')
else:
try:
p = float(price)
if p < 0 :
errors.append('Price cannot be negative.')
elif p > 999999 :
errors.append('Price seems unrealistically high.')
except ValueError:
errors.append('Price must be a number.')

# Quantity total
if not qty_total:
errors.append('Total quantity is required.')
else:
try:
q = int(qty_total)
if q < 1 :
errors.append('Total quantity must be at least 1.')
except ValueError:
errors.append('Total quantity must be a whole number.')

# Max per order
if not max_order:
errors.append('Max per order is required.')
else:
try:
m = int(max_order)
if m < 1 :


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

errors.append('Max per order must be at least 1.')
# Validate max <= total only when both are valid numbers
elif qty_total.isdigit() and m > int(qty_total):
errors.append('Max per order cannot exceed total quantity.')
except ValueError:
errors.append('Max per order must be a whole number.')

return errors


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

**Part B**

#### Write app/routes/tickets.py — Ticket Type CRUD

Create a new file app/routes/tickets.py in VS Code:

from flask import (Blueprint, render_template, redirect,
url_for, session, flash, request)
from app.firebase_config import db
from app.decorators import login_required, role_required
from app.utils.validators import validate_ticket_type
from google.cloud.firestore import SERVER_TIMESTAMP

tickets_bp =
Blueprint('tickets',__name__,url_prefix='/organizer/events/<event_id>/tickets')

# ── Helper: verify organizer owns the event ──────────────────────────
def verify_event_ownership(event_id):
"""Returns (event_doc, event_data) or (None, None) if not
found/forbidden."""
doc = db.collection('events').document(event_id).get()
if not doc.exists:
return None, None
data = doc.to_dict()
if data.get('organizer_uid') != session.get('uid'):
return None, None
return doc, data

# ── Helper: get ticket type subcollection ref ────────────────────────
def tt_col(event_id):
return (
db.collection('events')
.document(event_id)
.collection('ticket_types')
)

# ── LIST ticket types for an event ───────────────────────────────────
@tickets_bp.route('/')
@login_required
@role_required('organizer')
def list_tickets(event_id):
event_doc, event_data = verify_event_ownership(event_id)
if event_doc is None:
flash('Event not found or access denied.','danger')
return redirect(url_for('events.list_events'))

docs = tt_col(event_id).order_by('name').stream()
tickets = [{**d.to_dict(),'id':d.id} for d in docs]

return render_template('organizer/tickets/list.html',
event=event_data, event_id=event_id,
tickets=tickets)


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

# ── CREATE ticket type ───────────────────────────────────────────────
@tickets_bp.route('/create',methods=['GET','POST'])
@login_required
@role_required('organizer')
def create_ticket(event_id):
event_doc, event_data = verify_event_ownership(event_id)
if event_doc is None:
flash('Event not found or access denied.','danger')
return redirect(url_for('events.list_events'))

if request.method == 'POST':
form_data = request.form.to_dict()
errors = validate_ticket_type(form_data)

if errors:
for e in errors: flash(e,'danger')
return render_template('organizer/tickets/form.html',
event=event_data,event_id=event_id,
form_data=form_data,action='create')

tt_col(event_id).add({
'name': form_data['name'].strip(),
'description': form_data.get('description','').strip(),
'price': float(form_data['price']),
'quantity_total': int(form_data['quantity_total']),
'quantity_sold': 0,
'max_per_order': int(form_data['max_per_order']),
'is_active': True,
'created_at': SERVER_TIMESTAMP,
})

flash(f"Ticket '{form_data['name']}' created!",'success')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

return render_template('organizer/tickets/form.html',
event=event_data,event_id=event_id,
form_data={},action='create')

# ── EDIT ticket type ──────────────────────────────────────────────────
@tickets_bp.route('/<tt_id>/edit',methods=['GET','POST'])
@login_required
@role_required('organizer')
def edit_ticket(event_id, tt_id):
event_doc, event_data = verify_event_ownership(event_id)
if event_doc is None:
flash('Event not found or access denied.','danger')
return redirect(url_for('events.list_events'))

tt_doc = tt_col(event_id).document(tt_id).get()
if not tt_doc.exists:
flash('Ticket type not found.','danger')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

tt_data = tt_doc.to_dict()
# Convert numbers to strings for form pre-fill
tt_data['price'] = str(tt_data.get('price',0))
tt_data['quantity_total'] = str(tt_data.get('quantity_total',0))
tt_data['max_per_order'] = str(tt_data.get('max_per_order',1))


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

if request.method == 'POST':
form_data = request.form.to_dict()
errors = validate_ticket_type(form_data)

if errors:
for e in errors: flash(e,'danger')
return render_template('organizer/tickets/form.html',
event=event_data,event_id=event_id,
form_data=form_data,action='edit',tt_id=tt_id)

# Prevent reducing quantity below sold count
new_qty = int(form_data['quantity_total'])
sold = tt_doc.to_dict().get('quantity_sold',0)
if new_qty < sold:
flash(f'Cannot reduce quantity below {sold} (already
sold).','danger')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

tt_col(event_id).document(tt_id).update({
'name': form_data['name'].strip(),
'description': form_data.get('description','').strip(),
'price': float(form_data['price']),
'quantity_total': new_qty,
'max_per_order': int(form_data['max_per_order']),
'updated_at': SERVER_TIMESTAMP,
})
flash(f"Ticket '{form_data['name']}' updated!",'success')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

return render_template('organizer/tickets/form.html',
event=event_data,event_id=event_id,
form_data=tt_data,action='edit',tt_id=tt_id)

# ── DELETE ticket type ───────────────────────────────────────────────
@tickets_bp.route('/<tt_id>/delete',methods=['POST'])
@login_required
@role_required('organizer')
def delete_ticket(event_id, tt_id):
event_doc, event_data = verify_event_ownership(event_id)
if event_doc is None:
flash('Event not found or access denied.','danger')
return redirect(url_for('events.list_events'))

tt_doc = tt_col(event_id).document(tt_id).get()
if not tt_doc.exists:
flash('Ticket type not found.','danger')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

# Block delete if tickets have been sold
sold = tt_doc.to_dict().get('quantity_sold',0)
if sold > 0 :
flash(f'{sold} tickets already sold. Cannot delete this ticket
type.','danger')
return redirect(url_for('tickets.list_tickets',event_id=event_id))

tt_name = tt_doc.to_dict().get('name','this ticket')
tt_col(event_id).document(tt_id).delete()
flash(f"Ticket '{tt_name}' deleted.",'info')


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

return redirect(url_for('tickets.list_tickets',event_id=event_id))

# ── TOGGLE is_active ──────────────────────────────────────────────────
@tickets_bp.route('/<tt_id>/toggle',methods=['POST'])
@login_required
@role_required('organizer')
def toggle_ticket(event_id, tt_id):
event_doc, _ = verify_event_ownership(event_id)
if event_doc is None:
return redirect(url_for('events.list_events'))

tt_ref = tt_col(event_id).document(tt_id)
tt_doc = tt_ref.get()
if tt_doc.exists:
current = tt_doc.to_dict().get('is_active',True)
tt_ref.update({'is_active': not current})
status = 'enabled' if not current else 'disabled'
flash(f'Ticket {status}.','info')
return redirect(url_for('tickets.list_tickets',event_id=event_id))


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

**Part C**

#### Write the 2 Ticket Type Templates

Create the folder and files first:

mkdir -p templates/organizer/tickets
touch templates/organizer/tickets/list.html
templates/organizer/tickets/form.html

##### File 1 — templates/organizer/tickets/list.html

This is wireframe page 8 — a table with Name, Price, Quantity, Sold, and Actions columns. Open the

file and paste:

{% extends 'base.html' %}
{% block title %}Ticket Types — {{ event.name }}{% endblock %}

{% block content %}
<div class="container py-4">

{# Breadcrumb #}
<nav aria-label="breadcrumb" class="mb-3"><ol class="breadcrumb">
<li class="breadcrumb-item"><a href="{{ url_for('events.list_events')
}}">My Events</a></li>
<li class="breadcrumb-item active">{{ event.name }} — Ticket Types</li>
</ol></nav>

<div class="d-flex justify-content-between align-items-center mb-4">
<div>
<h3 class="fw-bold mb-1">{{ event.name }}</h3>
<p class="text-muted mb-0">Manage ticket types for this event.</p>
</div>
<a href="{{ url_for('tickets.create_ticket', event_id=event_id) }}"
class="btn btn-primary">
<i class="bi bi-plus-lg me-2"></i>Add Ticket Type
</a>
</div>

{% if not tickets %}
<div class="ems-card card text-center py-5">
<i class="bi bi-ticket-perforated fs-1 text-muted mb-3"></i>
<h5 class="text-muted">No ticket types yet</h5>
<p class="text-muted small">Add your first ticket type to start
selling.</p>
<a href="{{ url_for('tickets.create_ticket', event_id=event_id) }}"
class="btn btn-primary btn-sm">
Add First Ticket Type</a>
</div>

{% else %}
<div class="ems-card card">
<div class="table-responsive">
<table class="table table-hover align-middle mb-0">


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

<thead class="table-dark">
<tr>
<th>Name</th><th>Price</th><th>Quantity</th>
<th>Sold</th><th>Available</th><th>Status</th><th class="text-
end">Actions</th>
</tr>
</thead>
<tbody>
{% for tt in tickets %}
<tr class="{{ 'table-secondary' if not tt.is_active }}">
<td>
<strong>{{ tt.name }}</strong>
{% if tt.description %}
<div class="text-muted small">{{ tt.description }}</div>
{% endif %}
</td>
<td>
{% if tt.price == 0 %}
<span class="badge bg-success">Free</span>
{% else %}
₹{{ tt.price | int if tt.price == tt.price | int else tt.price
}}
{% endif %}
</td>
<td>{{ tt.quantity_total }}</td>
<td>{{ tt.quantity_sold or 0 }}</td>
<td>
{% set avail = tt.quantity_total - (tt.quantity_sold or 0) %}
{% if avail <= 0 %}
<span class="badge bg-danger">Sold Out</span>
{% elif avail <= 10 %}
<span class="badge bg-warning text-dark">{{ avail }}
left</span>
{% else %}
{{ avail }}
{% endif %}
</td>
<td>
{% if tt.is_active %}
<span class="badge bg-success">Active</span>
{% else %}
<span class="badge bg-secondary">Inactive</span>
{% endif %}
</td>
<td class="text-end">
{# Edit #}
<a href="{{ url_for('tickets.edit_ticket', event_id=event_id,
tt_id=tt.id) }}"
class="btn btn-sm btn-outline-primary me-1">
<i class="bi bi-pencil"></i>
</a>
{# Toggle active/inactive #}
<form method="POST" action="{{ url_for('tickets.toggle_ticket',
event_id=event_id, tt_id=tt.id) }}" class="d-inline">
<input type="hidden" name="csrf_token" value="{{ csrf_token()
}}">
<button type="submit" class="btn btn-sm btn-outline-secondary
me-1"
title="{{ 'Deactivate' if tt.is_active else
'Activate' }}">
<i class="bi bi-{{ 'toggle-on' if tt.is_active else
'toggle-off' }}"></i>


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

</button>
</form>
{# Delete (blocked if sold > 0) #}
{% if (tt.quantity_sold or 0) == 0 %}
<form method="POST" action="{{ url_for('tickets.delete_ticket',
event_id=event_id, tt_id=tt.id) }}" class="d-inline"
onsubmit="return confirm('Delete ticket: {{ tt.name
}}?')">
<input type="hidden" name="csrf_token" value="{{ csrf_token()
}}">
<button type="submit" class="btn btn-sm btn-outline-danger">
<i class="bi bi-trash"></i>
</button>
</form>
{% endif %}
</td>
</tr>
{% endfor %}
</tbody>
</table>
</div>
</div>
{% endif %}
</div>
{% endblock %}

##### File 2 — templates/organizer/tickets/form.html

{% extends 'base.html' %}
{% block title %}{{ 'Edit' if action=='edit' else 'Add' }} Ticket — {{
event.name }}{% endblock %}

{% block content %}
<div class="container py-4"><div class="row justify-content-center"><div
class="col-lg-6">

<nav aria-label="breadcrumb" class="mb-3"><ol class="breadcrumb">
<li class="breadcrumb-item"><a href="{{ url_for('events.list_events')
}}">My Events</a></li>
<li class="breadcrumb-item"><a href="{{ url_for('tickets.list_tickets',
event_id=event_id) }}">Tickets</a></li>
<li class="breadcrumb-item active">{{ 'Edit' if action=='edit' else 'Add'
}} Ticket</li>
</ol></nav>

<div class="ems-card card p-4">
<h5 class="fw-bold mb-4">{{ 'Edit Ticket Type' if action=='edit' else
'Add New Ticket Type' }}</h5>

{% if action=='edit' %}
<form method="POST" action="{{ url_for('tickets.edit_ticket',
event_id=event_id, tt_id=tt_id) }}" novalidate>
{% else %}
<form method="POST" action="{{ url_for('tickets.create_ticket',
event_id=event_id) }}" novalidate>
{% endif %}
<input type="hidden" name="csrf_token" value="{{ csrf_token() }}">


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

<div class="mb-3">
<label class="form-label fw-semibold">Ticket Name <span class="text-
danger">*</span></label>
<input type="text" name="name" class="form-control"
placeholder="e.g. Early Bird, General Admission, VIP"
value="{{ form_data.get('name','') }}" required>
</div>

<div class="mb-3">
<label class="form-label fw-semibold">Description (optional)</label>
<input type="text" name="description" class="form-control"
placeholder="Short description shown on event page"
value="{{ form_data.get('description','') }}">
</div>

<div class="row">
<div class="col-md-6 mb-3">
<label class="form-label fw-semibold">Price (₹) <span class="text-
danger">*</span></label>
<input type="number" name="price" class="form-control"
placeholder="0 for free" min="0" step="0.01"
value="{{ form_data.get('price','') }}" required>
</div>
<div class="col-md-6 mb-3">
<label class="form-label fw-semibold">Total Quantity <span
class="text-danger">*</span></label>
<input type="number" name="quantity_total" class="form-control"
placeholder="e.g. 100" min="1"
value="{{ form_data.get('quantity_total','') }}" required>
</div>
</div>

<div class="mb-4">
<label class="form-label fw-semibold">Max per Order <span
class="text-danger">*</span></label>
<input type="number" name="max_per_order" class="form-control"
placeholder="e.g. 4" min="1"
value="{{ form_data.get('max_per_order','') }}" required>
<div class="form-text">Maximum number of this ticket type an attendee
can buy in one order.</div>
</div>

<div class="d-flex gap-2">
<button type="submit" class="btn btn-primary">
{{ 'Save Changes' if action=='edit' else 'Add Ticket Type' }}
</button>
<a href="{{ url_for('tickets.list_tickets', event_id=event_id) }}"
class="btn btn-outline-secondary">Cancel</a>
</div>
</form>
</div>
</div></div></div>
{% endblock %}


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

**Part D**

#### Wire Up, Rebuild Docker & Test All Flows

##### Step 1 — Register tickets blueprint in app.py

from app.routes.tickets import tickets_bp
app.register_blueprint(tickets_bp)

##### Step 2 — Add Manage Tickets link to the event edit form

Open templates/organizer/events/form.html. Find the breadcrumb section near the top. Add a Manage

Tickets button that only appears when editing (not creating) an event:

<div class="d-flex justify-content-between align-items-center mb-4">
<h4 class="fw-bold mb-0">
<i class="bi bi-calendar-plus me-2 text-primary"></i>
{{ 'Edit Event' if action=='edit' else 'Create New Event' }}
</h4>
{# Add this block — shows Manage Tickets only when editing #}
{% if action == 'edit' %}
<a href="{{ url_for('tickets.list_tickets', event_id=event_id) }}"
class="btn btn-outline-primary btn-sm">
<i class="bi bi-ticket-perforated me-1"></i>Manage Tickets
</a>
{% endif %}
</div>

##### Step 3 — Rebuild Docker and test

docker compose down
docker compose build
docker compose up

**Action What to test Expected result**

Edit an event Click Manage Tickets button

```
Ticket type list page loads for that
event
```
Empty ticket list First time view Empty state with Add Ticket Type
button

Add Early Bird ticket Name, price ₹50, qty 100, max 4 Flash: Ticket 'Early Bird' created!

Add General Admission Name, price ₹75, qty 200, max 10 Appears in table alongside Early Bird

Add VIP ticket Name, price ₹150, qty 50, max 2 Three tickets visible in table


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

Validate — empty name Submit form blank Flash: Ticket name is required

Validate — price = - 5 Negative price Flash: Price cannot be negative

Validate — max > qty Max 200, qty 50

```
Flash: Max per order cannot exceed
total quantity
```
Edit Early Bird Change price to ₹45 Flash: Ticket updated! Price shows
₹

Toggle inactive Click toggle icon Ticket row grays out, badge shows Inactive

Toggle back active Click toggle again Badge shows Active, row is normal

Delete VIP (0 sold) Click trash icon Confirmation dialog, then deleted

Visit event detail page localhost:5000/events/<id> All 2 remaining tickets visible with prices

Ticket availability Check counts Shows correct available count on public page

🎯 **Day 7 deliverable: Organizer can add Early Bird, General, and VIP tickets to any**

**event. Tickets appear on the public event detail page immediately with correct
availability counts.**

##### Step 4 — Commit

git add.
git status # confirm secrets/ and .env not listed
git commit -m "Day 7: ticket type CRUD, Sprint 1 complete"
git push origin develop


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

## End-of-Day 7 Checklist

**Item**^ **How to verify**^

###### ☐

```
validate_ticket_type()
done
```
Full validation including max > qty check

###### ☐

```
app/routes/tickets.py
created
```
Has list, create, edit, delete, toggle routes

###### ☐ tickets/list.html created^ Table with Name, Price, Qty, Sold, Available, Status, Actions^

###### ☐ tickets/form.html created^ Handles both add and edit, all fields present^

###### ☐ tickets_bp registered^ Blueprint imported and registered in app.py^

###### ☐ Manage Tickets button^ Appears on event edit form, links to ticket list^

###### ☐ Docker builds cleanly^ docker compose build completes without errors^

###### ☐ Add ticket works^ Ticket saved to Firestore subcollection^

###### ☐ Validation works^ Empty name, negative price, max>qty all caught^

###### ☐ Edit ticket works^ Price and quantity update in Firestore^

###### ☐ Toggle works^ is_active toggles, badge updates on list page^

###### ☐ Delete works^ Ticket deleted, blocked if quantity_sold > 0^

###### ☐ Tickets on public page^ Event detail page shows all active ticket types^

###### ☐ Committed & pushed^ GitHub develop branch shows Day 7 commit^


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

### 🏆 SPRINT 1 COMPLETE

```
Week 1 of 2 done — MVP v1 foundation is complete
Days 1–7 | 7 features shipped | Sprint 2 starts tomorrow
```
## Sprint 1 Complete — What you built in Week 1

**Day Feature What works now**

**Day
1 Project scaffold**^ Docker + Flask running, Git set up, project structure^

**Day
2 Firebase connection**^ Firestore read/write confirmed, full schema designed^

**Day
3 Authentication**^ Sign up, login, logout, forgot password, role-based guards^

**Day
4 Venue management**^ Organizer can add, edit, delete venues with validation^

**Day
5 Public event listing**^

Event cards grid, event detail page, ticket type display

**Day
6 Event management**^

Organizer creates, publishes, edits, cancels events from web app

**Day
7 Ticket types**^ Early Bird, General, VIP tickets with availability tracking^

## Sprint 2 Preview — Week 2 (Days 8–14)

Sprint 2 completes MVP v1. By Day 14 you will have a fully working event registration and payment

system.

**Day Feature What you will build**

**Day
8**

**Check-in system** QR code scanner, manual check 12 - in, check-in log —^ wireframe page

**Day
9**

**Registration flow** Attendee registers for event, selects ticket, fills form page 3 —^ wireframe

**Day
10**

**PayPal payment** Payment page, PayPal Checkout, webhook confirmation page 4 —^ wireframe

**Day
11**

**QR code + Email** Generate QR code PNG, send confirmation email via SendGrid wireframe page 5 —^

**Day
12 Organizer dashboard v2**^ Registration list, CSV export, revenue metrics, check-in count^


###### EMS — Day 7 Guide | Ticket Type Management | Sprint 1 Complete | Wireframe Page 8

**Day
13**

**Attendee portal** My Events page, ticket view with QR code 11 —^ wireframe pages 10 &

**Day
14**

**MVP v1 QA & tag** End-to-end regression, bug fixes, Git tag v0.1.0-mvp

**Tag and backup reminder**

After completing Day 7 — Sprint 1 complete:

git tag v0.1.0-sprint1 -m 'Sprint 1 complete — all 7 days done'

git push origin v0.1.0-sprint

cp -r /d/projects/event-management-system /d/projects/event-management-system-sprint1-backup


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

# Day 8 — Setup Guide

## QR Code Check-In System

Sprint 2 | Wireframe Page 12 | 4 Parts | ~5 hours

**Part A**

```
Write app/utils/qr_utils.py — QR code generation &
HMAC verification (~30 min)
```
**Part B**

```
Write app/routes/checkin.py — scan, manual
check-in, check-in log (~75 min)
```
**Part C**

```
Write templates/organizer/checkin/ — scanner
page and log page (~60 min)
```
**Part D**

```
Wire up, add check-in link to event list, rebuild
Docker, test (~30 min)
```
**End goal**

```
Organizer can scan a QR code (or search by
email) to check in an attendee. Check-in log
updates in real time.
```

###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

## Overview — What we are building today

The check-in system lets organizers scan attendee QR codes during the event to mark them as
checked in. It matches wireframe page 12 exactly: a live camera feed for QR scanning, a manual entry

field for backup, and a recent check-ins log.

**Day 8 vs Day 11 — important distinction**

Today you build the check-in READER — the scanner that reads a QR code and marks an attendee
as checked in.

Day 11 builds the check-in WRITER — the system that generates and sends the QR code when a
registration is confirmed.

Because we are building the reader before the writer, we will test today using a manually created
registration document in Firestore (same approach as the test event on Day 5). This is intentional and
expected.

##### New files today

**event-management-system/**
├── app/
│ ├── utils/
│ │ └── qr_utils.py ← NEW — QR generation & HMAC verify
│ └── routes/
│ └── checkin.py ← NEW — check-in routes
├── templates/organizer/
│ └── checkin/ ← NEW folder
│ ├── scanner.html ← NEW — camera QR scanner page
│ └── log.html ← NEW — recent check-ins table
└── app.py ← UPDATE — register checkin
blueprint


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

**Part A**

#### Write app/utils/qr_utils.py — QR Generation & HMAC

#### Verification

The QR code payload is an HMAC-signed string. This means when we scan a QR code, we can verify
it is genuine (not forged) by checking the signature against our secret key. Create app/utils/qr_utils.py

in VS Code:

import hmac, hashlib, os
import qrcode
from io import BytesIO
import base

# ── QR payload format ────────────────────────────────────────────────
# reg:{registration_id}:ev:{event_id}:sig:{hmac_signature}
# Example: reg:ABC123:ev:XYZ789:sig:a1b2c3d4e5f6...

def _get_secret():
secret = os.getenv('QR_HMAC_SECRET','dev-secret-change-in-production')
return secret.encode()

def build_qr_payload(registration_id, event_id):
"""Build a signed payload string for a registration QR code."""
raw = f'reg:{registration_id}:ev:{event_id}'
sig = hmac.new(_get_secret(),raw.encode(),hashlib.sha256).hexdigest()
return f'{raw}:sig:{sig}'

def verify_qr_payload(payload):
"""
Verify a scanned QR payload.
Returns (registration_id, event_id) if valid, or (None, None) if invalid.
"""
try:
# Expected format: reg:{reg_id}:ev:{ev_id}:sig:{signature}
parts = payload.split(':')
# parts = ['reg', reg_id, 'ev', ev_id, 'sig', signature]
if len(parts) != 6 or parts[ 0 ] != 'reg' or parts[ 2 ] != 'ev' or
parts[ 4 ] != 'sig':
return None, None

registration_id = parts[ 1 ]
event_id = parts[ 3 ]
received_sig = parts[ 5 ]

# Recompute expected signature
raw = f'reg:{registration_id}:ev:{event_id}'
expected_sig =
hmac.new(_get_secret(),raw.encode(),hashlib.sha256).hexdigest()

# Constant-time comparison prevents timing attacks
if not hmac.compare_digest(expected_sig, received_sig):


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

return None, None

return registration_id, event_id

except Exception:
return None, None

def generate_qr_image_base64(payload):
"""
Generate a QR code PNG and return as base64 string.
Used to embed QR codes in HTML pages and emails.
"""
qr = qrcode.QRCode(
version= 1 ,
error_correction=qrcode.constants.ERROR_CORRECT_M,
box_size= 10 ,
border= 4
)
qr.add_data(payload)
qr.make(fit=True)
img = qr.make_image(fill_color='black', back_color='white')

buffer = BytesIO()
img.save(buffer, format='PNG')
buffer.seek( 0 )
return base64.b64encode(buffer.read()).decode()

**Why HMAC?**

Without a signature, anyone could forge a QR code by just guessing a registration ID. The HMAC
signature means the QR code can only be generated by our server (which has the secret key). When
scanning, we recompute the signature and compare — if they don't match, the QR is rejected.


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

**Part B**

#### Write app/routes/checkin.py — Check-In Routes

Create app/routes/checkin.py in VS Code:

from flask import (Blueprint, render_template, request,
jsonify, session, flash, redirect, url_for)
from app.firebase_config import db
from app.decorators import login_required, role_required
from app.utils.qr_utils import verify_qr_payload
from datetime import datetime, timezone
from google.cloud.firestore import SERVER_TIMESTAMP, Increment

checkin_bp = Blueprint('checkin',__name__,url_prefix='/organizer/checkin')

# ── Helper: perform the actual check-in ──────────────────────────────
def do_checkin(registration_id, event_id, staff_uid):
"""
Mark a registration as checked in.
Returns (success: bool, message: str, attendee_name: str)
"""
reg_ref = db.collection('registrations').document(registration_id)
reg_doc = reg_ref.get()

if not reg_doc.exists:
return False, 'Registration not found.', ''

reg = reg_doc.to_dict()

# Verify this registration belongs to the correct event
if reg.get('event_id') != event_id:
return False, 'QR code does not match this event.', ''

# Check payment status
if reg.get('payment_status') != 'paid':
return False, 'Payment not confirmed for this registration.', ''

# Check if already checked in
if reg.get('status') == 'checked_in':
name = reg.get('attendee_name','Attendee')
return False, f'{name} is already checked in.', name

# Get attendee name for display
attendee_name = reg.get('attendee_name','Unknown')

# Use Firestore transaction to atomically update registration + event counter
@db.transaction()
def update_in_transaction(transaction):
transaction.update(reg_ref, {
'status': 'checked_in',
'checked_in_at': SERVER_TIMESTAMP,
'checked_in_by': staff_uid,
})
# Atomically increment the event's check-in counter


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

event_ref = db.collection('events').document(event_id)
transaction.update(event_ref,{'total_checkins':Increment( 1 )})

update_in_transaction()
return True, f'Check-in successful!', attendee_name

# ── SCANNER PAGE ─────────────────────────────────────────────────────
@checkin_bp.route('/<event_id>')
@login_required
@role_required('organizer')
def scanner(event_id):
event_doc = db.collection('events').document(event_id).get()
if not event_doc.exists:
flash('Event not found.','danger')
return redirect(url_for('events.list_events'))
event = event_doc.to_dict()
return render_template('organizer/checkin/scanner.html',
event=event, event_id=event_id)

# ── QR SCAN API — called by JS camera scanner ────────────────────────
@checkin_bp.route('/<event_id>/scan',methods=['POST'])
@login_required
@role_required('organizer')
def scan_qr(event_id):
"""JSON endpoint — called by the camera scanner JS on scan."""
payload = request.json.get('payload','').strip()
if not payload:
return jsonify({'success':False,'message':'Empty QR code.'}),400

registration_id, qr_event_id = verify_qr_payload(payload)
if not registration_id:
return jsonify({'success':False,'message':'Invalid or tampered QR
code.'}),400

success, message, name = do_checkin(registration_id, event_id, session['uid'])
return jsonify({'success':success,'message':message,'name':name})

# ── MANUAL CHECK-IN — search by email or order number ────────────────
@checkin_bp.route('/<event_id>/manual',methods=['POST'])
@login_required
@role_required('organizer')
def manual_checkin(event_id):
query = request.form.get('query','').strip().lower()
if not query:
flash('Please enter an email or order number.','warning')
return redirect(url_for('checkin.scanner',event_id=event_id))

# Search registrations for this event by email
docs = (
db.collection('registrations')
.where('event_id','==',event_id)
.where('attendee_email','==',query)
.limit( 1 )
.stream()
)
results = list(docs)


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

if not results:
flash(f'No registration found for: {query}','warning')
return redirect(url_for('checkin.scanner',event_id=event_id))

reg_doc = results[ 0 ]
success, message, name = do_checkin(reg_doc.id, event_id, session['uid'])
category = 'success' if success else 'warning'
flash(message, category)
return redirect(url_for('checkin.scanner',event_id=event_id))

# ── CHECK-IN LOG ─────────────────────────────────────────────────────
@checkin_bp.route('/<event_id>/log')
@login_required
@role_required('organizer')
def checkin_log(event_id):
docs = (
db.collection('registrations')
.where('event_id','==',event_id)
.where('status','==','checked_in')

.order_by('checked_in_at',direction=db.collection('x').document.__class__.DESCENDING
if False else 'DESCENDING')
.limit( 50 )
.stream()
)
checkins = [{**d.to_dict(),'id':d.id} for d in docs]
event_doc = db.collection('events').document(event_id).get()
event = event_doc.to_dict() if event_doc.exists else {}
return render_template('organizer/checkin/log.html',
event=event,event_id=event_id,checkins=checkins)

**Simplify the check-in log route — replace the order_by line**

The order_by with DESCENDING shown above is verbose. Replace the entire docs query block in
checkin_log() with this simpler version:

docs = (

db.collection('registrations')

.where('event_id', '==', event_id)

.where('status', '==', 'checked_in')

.stream()

)

checkins = sorted(

[{**d.to_dict(), 'id': d.id} for d in docs],

key=lambda x: x.get('checked_in_at') or '',

reverse=True

)[:50]


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

**Part C**

#### Write the 2 Check-In Templates

Create the folder and files:

mkdir -p templates/organizer/checkin
touch templates/organizer/checkin/scanner.html
templates/organizer/checkin/log.html

##### File 1 — templates/organizer/checkin/scanner.html

This is wireframe page 12 — camera feed, manual entry, and recent check-ins. The camera QR

scanning uses the html5-qrcode library from CDN. Open the file and paste:

{% extends 'base.html' %}
{% block title %}Check-In — {{ event.name }}{% endblock %}

{% block content %}
<div class="container py-4">

<div class="d-flex justify-content-between align-items-center mb-4">
<div>
<h3 class="fw-bold mb-1">Check-In: {{ event.name }}</h3>
<p class="text-muted mb-0">Scan attendee QR codes or search
manually.</p>
</div>
<a href="{{ url_for('checkin.checkin_log', event_id=event_id) }}"
class="btn btn-outline-primary">
<i class="bi bi-list-check me-1"></i>View Log
</a>
</div>

<div class="row g-4">

{# ── Camera scanner column ─── #}
<div class="col-lg-6">
<div class="ems-card card p-3">
<h5 class="fw-bold mb-3"><i class="bi bi-qr-code-scan me-2 text-
primary"></i>QR Scanner</h5>
{# Camera feed — html5-qrcode renders here #}
<div id="qr-reader" style="width:100%;border-
radius:8px;overflow:hidden"></div>
<div id="scan-result" class="mt-3 p-3 rounded d-none">
<strong id="scan-name"></strong>
<p id="scan-msg" class="mb-0 small"></p>
</div>
<button id="switch-camera" class="btn btn-sm btn-outline-secondary
mt-2 d-none">
<i class="bi bi-arrow-repeat me-1"></i>Switch Camera
</button>
</div>
</div>


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

{# ── Manual entry column ──── #}
<div class="col-lg-6">
<div class="ems-card card p-3 mb-3">
<h5 class="fw-bold mb-3"><i class="bi bi-search me-2 text-
primary"></i>Manual Entry</h5>
<form method="POST" action="{{ url_for('checkin.manual_checkin',
event_id=event_id) }}">
<input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
<div class="input-group">
<input type="text" name="query" class="form-control"
placeholder="Enter email address..." required>
<button type="submit" class="btn btn-primary">
<i class="bi bi-check-lg me-1"></i>Check In
</button>
</div>
<div class="form-text">Enter the attendee's registered email
address.</div>
</form>
</div>

{# Recent check-ins live list — updated on each scan #}
<div class="ems-card card p-3">
<h6 class="fw-bold mb-3">Recent Check-Ins</h6>
<ul id="recent-list" class="list-group list-group-flush">
<li class="list-group-item text-muted small" id="no-scans">No scans
yet this session.</li>
</ul>
</div>
</div>
</div>
</div>

{% block extra_js %}
{# html5-qrcode library for browser camera scanning #}
<script src="https://unpkg.com/html5-qrcode@2.3.8/html5-
qrcode.min.js"></script>
<script>
const EVENT_ID = '{{ event_id }}';
const SCAN_URL = '{{ url_for("checkin.scan_qr", event_id=event_id) }}';
const CSRF_TOKEN = document.querySelector('[name=csrf_token]')?
document.querySelector('[name=csrf_token]').value : '';

let lastScanned = ''; // prevent duplicate scans
let scanCooldown = false;

const html5QrCode = new Html5Qrcode('qr-reader');

// Start the camera scanner
html5QrCode.start(
{ facingMode: 'environment' }, // use rear camera on mobile
{ fps: 10, qrbox: { width: 250, height: 250 } },
async (decodedText) => {
// Ignore if same QR scanned within 3 seconds
if (decodedText === lastScanned || scanCooldown) return;
lastScanned = decodedText;
scanCooldown = true;
setTimeout(() => { scanCooldown = false; lastScanned = ''; }, 3000);

// Send scanned payload to Flask for verification
try {
const resp = await fetch(SCAN_URL, {


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

method: 'POST',
headers: { 'Content-Type': 'application/json', 'X-CSRFToken':
CSRF_TOKEN },
body: JSON.stringify({ payload: decodedText })
});
const data = await resp.json();
showResult(data.success, data.message, data.name || '');
if (data.success) addToRecentList(data.name);
} catch(e) {
showResult(false, 'Network error. Try manual check-in.', '');
}
},
(err) => {} // ignore scan errors (camera noise)
).catch(err => {
document.getElementById('qr-reader').innerHTML =
'<p class="text-warning p-3">Camera not available. Use manual check-in
below.</p>';
});

function showResult(success, message, name) {
const box = document.getElementById('scan-result');
box.className = `mt-3 p-3 rounded alert alert-${success? 'success' :
'danger'}`;
document.getElementById('scan-name').textContent = name? name + ' — ' :
'';
document.getElementById('scan-msg').textContent = message;
}

function addToRecentList(name) {
document.getElementById('no-scans').remove();
const now = new Date().toLocaleTimeString();
const li = document.createElement('li');
li.className = 'list-group-item d-flex justify-content-between small';
li.innerHTML = `<span><i class='bi bi-check-circle text-success me-
2'></i>${name}</span>
<span class='text-muted'>${now}</span>`;
document.getElementById('recent-list').prepend(li);
}
</script>
{% endblock %}
{% endblock %}

##### File 2 — templates/organizer/checkin/log.html

{% extends 'base.html' %}
{% block title %}Check-In Log — {{ event.name }}{% endblock %}

{% block content %}
<div class="container py-4">
<div class="d-flex justify-content-between align-items-center mb-4">
<div>
<h3 class="fw-bold mb-1">Check-In Log: {{ event.name }}</h3>
<p class="text-muted mb-0">{{ checkins|length }} attendees checked
in.</p>
</div>
<a href="{{ url_for('checkin.scanner', event_id=event_id) }}" class="btn
btn-primary">
<i class="bi bi-qr-code-scan me-1"></i>Back to Scanner


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

</a>
</div>

{% if not checkins %}
<div class="ems-card card text-center py-5">
<i class="bi bi-person-x fs-1 text-muted mb-3"></i>
<h5 class="text-muted">No check-ins yet</h5>
</div>
{% else %}
<div class="ems-card card">
<div class="table-responsive">
<table class="table table-hover align-middle mb-0">
<thead class="table-dark">
<tr><th>Name</th><th>Email</th><th>Ticket</th><th>Checked In
At</th></tr>
</thead>
<tbody>
{% for c in checkins %}
<tr>
<td><strong>{{ c.attendee_name or '—' }}</strong></td>
<td>{{ c.attendee_email or '—' }}</td>
<td>{{ c.ticket_type_name or '—' }}</td>
<td>
{% if c.checked_in_at %}
{{ c.checked_in_at.strftime('%d %b %Y %I:%M %p') }}
{% else %}—{% endif %}
</td>
</tr>
{% endfor %}
</tbody>
</table>
</div>
</div>
{% endif %}
</div>
{% endblock %}


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

**Part D**

#### Wire Up, Create Test Registration, Rebuild & Test

##### Step 1 — Register checkin blueprint in app.py

from app.routes.checkin import checkin_bp
app.register_blueprint(checkin_bp)

##### Step 2 — Add Check-In link to the event list page

Open templates/organizer/events/list.html. Find the action buttons section for each event. Add a

Check-In button alongside the existing Edit and View buttons:

<a href="{{ url_for('checkin.scanner', event_id=event.id) }}"
class="btn btn-sm btn-outline-success">
<i class="bi bi-qr-code-scan me-1"></i>Check-In
</a>

##### Step 3 — Add CSRF header support for fetch() calls

The QR scanner JavaScript sends a POST request using fetch() with a custom X-CSRFToken

header. Flask-WTF needs to be configured to accept this. Open app.py and add this line after the

CSRFProtect setup:

# Allow CSRF token in request headers (needed for fetch() AJAX calls)
app.config['WTF_CSRF_CHECK_DEFAULT'] = False
csrf.exempt(checkin_bp) # scanner JS sends token in header, not form

##### Step 4 — Create a test registration in Firestore

Since registration is built on Day 9, we manually create a test registration in Firebase Console to test

check-in today. Go to Firestore Console → Start collection:

- Collection ID: registrations → Auto-ID
- Add these fields:

**Field Type Value**

**event_id** string Paste your event's document ID from Firestore

**attendee_uid** string Paste your attendee user UID from Firebase Auth

**attendee_name** string Test Attendee


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

**attendee_email** string your actual email you used to sign up as attendee

**ticket_type_name** string Early Bird

**ticket_type_id** string Paste the Early Bird ticket type document ID

**status** string confirmed

**payment_status** string paid

**quantity** number 1

**total_amount** number 50

**order_number** string ORD-TEST01

Now generate a QR payload for this registration. In Git Bash, run a quick Python snippet to get the

payload string:

cd /d/projects/event-management-system
docker compose exec web python3 -c "
from app.utils.qr_utils import build_qr_payload;
print(build_qr_payload('YOUR-REGISTRATION-ID', 'YOUR-EVENT-ID'))"

Replace YOUR-REGISTRATION-ID and YOUR-EVENT-ID with the actual IDs from Firestore. This

prints the payload string — copy it. You will use it to test manual check-in.

##### Step 5 — Rebuild and test

docker compose down
docker compose build
docker compose up

**Action What to test Expected result**

Event list page Click Checkevent - In button on an Scanner page loads with camera feed

Camera scanner Allow camera in browser QR reader box appears with viewfinder

Manual entry — valid email Type test attendee email Flash: Check-in successful! in green

Check-in log Click View Log Test Attendee shows in log with timestamp

Manual again — same email Try checking in again

```
Flash: Test Attendee is already
checked in
```
Dashboard Visit organizer dashboard Check-in count incremented by 1

Manual — wrong email Type nonexistent@email.com Flash: No registration found warning

**Camera not working?**

The browser camera (getUserMedia) requires HTTPS or localhost. Since you are on localhost:5000 it
should work. If the camera is blocked, check your browser settings — click the camera icon in the
address bar and allow it. On mobile, you need HTTPS which comes later in the project.


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

🎯 **Day 8 deliverable: Organizer can open the check-in scanner page, manually**

**search an attendee by email, mark them as checked in, and view the check-in log.
The check-in counter on the dashboard increments correctly.**

##### Step 6 — Commit

git add.
git status # confirm secrets/ and .env not listed
git commit -m "Day 8: QR check-in system, HMAC verification, manual check-in,
check-in log"
git push origin develop


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

## End-of-Day 8 Checklist

**Item**^ **How to verify**^

###### ☐

```
app/utils/qr_utils.py Has build_qr_payload(), verify_qr_payload(),
generate_qr_image_base64()
```
###### ☐

```
HMAC verification verify_qr_payload returns None,None for
tampered payload
```
###### ☐

```
app/routes/checkin.py Has scanner, scan_qr, manual_checkin,
checkin_log routes
```
###### ☐

```
do_checkin() helper Checks payment status, prevents double
check-in, increments counter
```
###### ☐

```
Firestore transaction Atomically updates registration + event check-in
counter
```
###### ☐

```
templates/organizer/checkin/scanner.html Camera feed with html5-qrcode, manual entry,
recent list
```
###### ☐ templates/organizer/checkin/log.html^ Table of checked-in attendees with timestamps^

###### ☐ checkin_bp registered^ Blueprint imported and registered in app.py^

###### ☐

```
CSRF exempt for scan route WTF_CSRF_CHECK_DEFAULT=False +
csrf.exempt(checkin_bp)
```
###### ☐ Check-In button on event list^ Button links to scanner for each event^

###### ☐

```
Test registration created registrations collection has test doc with
payment_status=paid
```
###### ☐

```
Manual check-in works Email search finds attendee, marks
status=checked_in in Firestore
```
###### ☐

```
Double check-in blocked Second attempt shows already checked in
message
```
###### ☐ Dashboard counter^ total_checkins incremented after check-in^

###### ☐

```
Check-in log shows entry Log page displays attendee name and
timestamp
```
###### ☐ Committed & pushed^ GitHub develop branch shows Day 8 commit^

## What's Next — Day 9 Preview

Tomorrow you build the full attendee registration flow — the most critical page of the entire application.

Attendees select a ticket, fill in their details, and proceed to payment.

- Registration form page — wireframe page 3


###### EMS — Day 8 Guide | QR Code Check-In System | Sprint 2 | Wireframe Page 12

- app/routes/registration.py — create pending registration in Firestore
- Ticket availability validation (server-side, prevents overselling)
- Session-based cart (store selected ticket while user fills the form)
- Promo code validation wired to backend
- This is the page the Select & Register button on Day 5 links to

**Tag and backup reminder**

After completing Day 8:

git tag v0.0.8-day8 -m 'Day 8 complete — QR check-in system working'

git push origin v0.0.8-day8

cp -r /d/projects/event-management-system /d/projects/event-management-system-day8-backup


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

# Day 9 — Setup Guide

## Attendee Registration Flow

Sprint 2 | Wireframe Page 3 | 4 Parts | ~6 hours

**Part A**

```
Write app/routes/registration.py — registration form
and promo validation (~90 min)
```
**Part B**

```
Write templates/attendee/register.html —
registration form page (~60 min)
```
**Part C**

```
Write templates/attendee/payment_summary.html
— order summary before payment (~45 min)
```
**Part D**

```
Wire up, fix the Select & Register button from Day
5, rebuild & test (~30 min)
```
**End goal**

```
Attendee can select a ticket, fill the registration
form, apply a promo code, and reach the payment
summary page ready for PayPal on Day 10.
```

###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

## Overview — What we are building today

The registration flow is the most critical path in the entire application. It connects the public event detail
page (Day 5) to the PayPal payment page (Day 10). Today you build the middle part — the form where

the attendee fills in their details and confirms their ticket selection.

**The full registration flow across Days 9, 10, and 11**

Day 9 (today): Attendee selects ticket → fills registration form → applies promo code → sees order
summary → clicks Proceed to Payment

Day 10 (tomorrow): PayPal payment page → PayPal Checkout → webhook confirms payment →
registration status = confirmed

Day 11 (Day after): Generate QR code PNG → upload to Firebase Storage → send confirmation
email with QR via SendGrid

##### Registration flow — step by step

**# Step What happens**

**1**

```
Attendee clicks Select &
Register on event detail page
(Day 5)
```
Redirected to /register/<event_id>/<ticket_type_id>

**2 GET /register —**^ **registration
form loads**

```
Form pre-fills ticket name and price. Attendee sees quantity selector
and promo code field.
```
**3 Attendee applies a promo
code (optional)**

```
AJAX call to /register/validate-promo — returns discount amount.
Total updates live.
```
**4 Attendee fills form and clicks
Proceed to Payment**

```
POST /register — server validates availability, creates PENDING
registration in Firestore
```
**5 Redirect to
/payment/<registration_id>**

```
Order summary page showing final amount. PayPal button appears
(wired Day 10).
```
##### New files today

**event-management-system/**
├── app/routes/
│ └── registration.py ← NEW — registration + promo validation
├── templates/attendee/
│ ├── register.html ← NEW — registration form (wireframe
pg.3)
│ └── payment_summary.html ← NEW — order summary before PayPal
├── templates/public/
│ └── event_detail.html ← UPDATE — fix Select & Register button
└── app.py ← UPDATE — register registration
blueprint


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

**Part A**

#### Write app/routes/registration.py — Registration & Promo

#### Validation

Create app/routes/registration.py in VS Code. This file has 3 routes: the registration form, the promo

code validator, and the payment summary page.

import os, secrets
from flask import (Blueprint, render_template, redirect,
url_for, session, flash, request, jsonify, abort)
from app.firebase_config import db
from app.decorators import login_required
from datetime import datetime, timezone
from google.cloud.firestore import SERVER_TIMESTAMP, Increment

registration_bp = Blueprint('registration',__name__,url_prefix='/register')

# ── Helper: fetch event + ticket type + verify availability ──────────
def get_event_and_ticket(event_id, ticket_type_id):
"""Returns (event_data, ticket_data) or aborts with 404."""
event_doc = db.collection('events').document(event_id).get()
if not event_doc.exists or event_doc.to_dict().get('status') != 'published':
abort( 404 )

tt_doc = (
db.collection('events')
.document(event_id)
.collection('ticket_types')
.document(ticket_type_id)
.get()
)
if not tt_doc.exists:
abort( 404 )

event = {**event_doc.to_dict(), 'id': event_doc.id}
ticket = {**tt_doc.to_dict(), 'id': tt_doc.id}
return event, ticket

# ── Helper: apply promo code discount ────────────────────────────────
def apply_promo(event_id, code, subtotal):
"""Returns (discount_amount, promo_doc_id, error_msg)."""
from datetime import timezone
now = datetime.now(timezone.utc)

docs = (
db.collection('promo_codes')
.where('event_id','==',event_id)
.where('code','==',code.upper())
.where('is_active','==',True)
.limit( 1 ).stream()
)
results = list(docs)


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

if not results:
return 0, None, 'Invalid promo code.'

promo_doc = results[ 0 ]
promo = promo_doc.to_dict()

# Check validity dates
if promo.get('valid_from') and now < promo['valid_from']:
return 0, None, 'Promo code is not yet active.'
if promo.get('valid_until') and now > promo['valid_until']:
return 0, None, 'Promo code has expired.'

# Check usage limit
used = promo.get('used_count',0)
limit = promo.get('max_uses',0)
if limit > 0 and used >= limit:
return 0, None, 'Promo code usage limit reached.'

# Calculate discount
dtype = promo.get('discount_type','fixed')
dval = promo.get('discount_value',0)
if dtype == 'percentage':
discount = round(subtotal * dval / 100 , 2 )
else:
discount = min(dval, subtotal) # cannot discount more than subtotal

return discount, promo_doc.id, None

# ── REGISTRATION FORM — GET shows form, POST creates pending reg ──────
@registration_bp.route('/<event_id>/<ticket_type_id>',methods=['GET','POST'])
@login_required
def register(event_id, ticket_type_id):
event, ticket = get_event_and_ticket(event_id, ticket_type_id)

# Check ticket availability
available = ticket.get('quantity_total',0) - ticket.get('quantity_sold',0)
if available <= 0 :
flash('Sorry, this ticket type is sold out.','warning')
return redirect(url_for('public.event_detail',event_id=event_id))

if request.method == 'POST':
quantity = int(request.form.get('quantity','1'))
promo_code = request.form.get('promo_code','').strip()

# Server-side quantity validation
max_order = ticket.get('max_per_order',10)
if quantity < 1 or quantity > min(max_order, available):
flash(f'Please select between 1 and {min(max_order,available)} tickets.','danger')
return redirect(request.url)

subtotal = round(ticket['price'] * quantity, 2 )
discount = 0
promo_id = None

# Apply promo code if provided
if promo_code:
discount, promo_id, err = apply_promo(event_id, promo_code, subtotal)
if err:


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

flash(err, 'warning')
discount = 0
promo_id = None
promo_code = ''

total_amount = max( 0 , subtotal - discount)

# Create PENDING registration in Firestore
user_doc = db.collection('users').document(session['uid']).get()
user_data = user_doc.to_dict() if user_doc.exists else {}

_,reg_ref = db.collection('registrations').add({
'event_id': event_id,
'attendee_uid': session['uid'],
'attendee_name': user_data.get('full_name', session.get('email','').split('@')[0]),
'attendee_email': session['email'],
'ticket_type_id': ticket_type_id,
'ticket_type_name': ticket['name'],
'quantity': quantity,
'unit_price': ticket['price'],
'subtotal': subtotal,
'discount_amount': discount,
'total_amount': total_amount,
'promo_code_used': promo_code or None,
'status': 'pending',
'payment_status': 'pending',
'order_number': f'ORD-{secrets.token_hex(4).upper()}',
'created_at': SERVER_TIMESTAMP,
})

# If ticket is free — skip payment, confirm immediately
if total_amount == 0 :
reg_ref.update({
'status': 'confirmed',
'payment_status': 'paid',
'confirmed_at': SERVER_TIMESTAMP,
})
db.collection('events').document(event_id).update({
'total_registrations': Increment( 1 )
})

db.collection('events').document(event_id).collection('ticket_types').document(ticket_type_id).update({
'quantity_sold': Increment(quantity)
})
flash('Registration confirmed! This is a free ticket.','success')
return redirect(url_for('attendee.my_events'))

# Paid ticket — go to payment summary
return redirect(url_for('registration.payment_summary',
registration_id=reg_ref.id))

# GET — show the registration form
return render_template('attendee/register.html',
event=event,ticket=ticket,available=available)

# ── PROMO CODE VALIDATION API — called by JavaScript ─────────────────
@registration_bp.route('/validate-promo',methods=['POST'])
@login_required
def validate_promo():


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

"""JSON endpoint for live promo code validation."""
data = request.json
event_id = data.get('event_id','')
code = data.get('code','').strip()
subtotal = float(data.get('subtotal', 0 ))

if not code:
return jsonify({'valid':False,'message':'Enter a promo code.'})

discount, _, err = apply_promo(event_id, code, subtotal)
if err:
return jsonify({'valid':False,'message':err})

return jsonify({
'valid':True,
'message':f'Promo applied! You save ₹{discount}',
'discount':discount,
'new_total':round(max( 0 ,subtotal-discount), 2 )
})

# ── PAYMENT SUMMARY PAGE ─────────────────────────────────────────────
@registration_bp.route('/payment/<registration_id>')
@login_required
def payment_summary(registration_id):
reg_doc = db.collection('registrations').document(registration_id).get()
if not reg_doc.exists:
abort( 404 )

reg = reg_doc.to_dict()

# Security: only the attendee who created this registration can view it
if reg.get('attendee_uid') != session.get('uid'):
abort( 403 )

# Guard: already paid registrations should not re-show payment page
if reg.get('payment_status') == 'paid':
flash('This registration is already paid.','info')
return redirect(url_for('attendee.my_events'))

event_doc = db.collection('events').document(reg['event_id']).get()
event = event_doc.to_dict() if event_doc.exists else {}

return render_template('attendee/payment_summary.html',
reg=reg,
registration_id=registration_id,
event=event)


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

**Part B**

#### Write templates/attendee/register.html — Registration Form

Create templates/attendee/register.html in VS Code. This is wireframe page 3 — the form with ticket
summary, quantity selector, and promo code input.

{% extends 'base.html' %}
{% block title %}Register — {{ event.name }}{% endblock %}

{% block content %}
<div class="container py-4">
<div class="row justify-content-center">
<div class="col-lg-7">

{# Back link #}
<a href="{{ url_for('public.event_detail', event_id=event.id) }}"
class="text-decoration-none text-muted mb-3 d-inline-block">
<i class="bi bi-arrow-left me-1"></i>Back to Event
</a>

{# Event + ticket summary — wireframe page 3 top section #}
<div class="ems-card card p-3 mb-3 bg-light">
<h5 class="fw-bold mb-1">{{ event.name }}</h5>
<div class="d-flex justify-content-between align-items-center">
<span class="text-muted">
<i class="bi bi-ticket-perforated me-1"></i>{{ ticket.name }}
</span>
<span class="fw-bold fs-5 text-primary">
{% if ticket.price == 0 %}Free{% else %}₹{{ ticket.price }}{%
endif %}
</span>
</div>
<small class="text-muted">{{ available }} tickets remaining</small>
</div>

<div class="ems-card card p-4">
<h5 class="fw-bold mb-4">
<i class="bi bi-person-plus me-2 text-primary"></i>Registration
Details
</h5>

<form method="POST" novalidate>
<input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
<input type="hidden" name="promo_code" id="hidden_promo" value="">

{# Quantity selector #}
<div class="mb-3">
<label class="form-label fw-semibold">Number of Tickets</label>
<select name="quantity" id="qty_select" class="form-select"
onchange="updateTotal()">
{% for i in range(1, [ticket.max_per_order+1, available+1]|min)
%}
<option value="{{ i }}">{{ i }}</option>
{% endfor %}
</select>


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

</div>

{# Attendee info — pre-filled from session #}
<div class="mb-3">
<label class="form-label fw-semibold">Email (registered
account)</label>
<input type="email" class="form-control" value="{{
session.get('email','') }}" disabled>
<div class="form-text">Your registration will be linked to this
email.</div>
</div>

{# Promo code — live validation via JS #}
<div class="mb-4">
<label class="form-label fw-semibold">Promo Code
(optional)</label>
<div class="input-group">
<input type="text" id="promo_input" class="form-control text-
uppercase"
placeholder="Enter promo code">
<button type="button" class="btn btn-outline-secondary"
onclick="applyPromo()">Apply</button>
</div>
<div id="promo_msg" class="form-text mt-1"></div>
</div>

{# Order total display #}
<div class="border rounded p-3 mb-4 bg-light">
<div class="d-flex justify-content-between mb-1">
<span class="text-muted">Subtotal</span>
<span id="subtotal_display">₹{{ ticket.price }}</span>
</div>
<div class="d-flex justify-content-between mb-1 d-none"
id="discount_row">
<span class="text-success">Discount</span>
<span class="text-success" id="discount_display">-₹0</span>
</div>
<hr class="my-2">
<div class="d-flex justify-content-between fw-bold">
<span>Total</span>
<span id="total_display" class="text-primary fs-5">₹{{
ticket.price }}</span>
</div>
</div>

<div class="d-grid">
<button type="submit" class="btn btn-primary btn-lg">
{% if ticket.price == 0 %}
<i class="bi bi-check-circle me-2"></i>Confirm Free
Registration
{% else %}
<i class="bi bi-credit-card me-2"></i>Proceed to Payment
{% endif %}
</button>
</div>
</form>
</div>
</div>
</div>
</div>


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

{% block extra_js %}
<script>
const PRICE = {{ ticket.price }};
const EVENT_ID = '{{ event.id }}';
let discount = 0;

function updateTotal() {
const qty = parseInt(document.getElementById('qty_select').value);
const subtotal = PRICE * qty;
const total = Math.max(0, subtotal - discount);
document.getElementById('subtotal_display').textContent = '₹' +
subtotal.toFixed(2);
document.getElementById('total_display').textContent = '₹' +
total.toFixed(2);
}

async function applyPromo() {
const code = document.getElementById('promo_input').value.trim();
const qty = parseInt(document.getElementById('qty_select').value);
const subtotal = PRICE * qty;
const msgEl = document.getElementById('promo_msg');

if (!code) { msgEl.textContent = 'Enter a promo code first.'; return; }

const resp = await fetch('/register/validate-promo', {
method: 'POST',
headers: { 'Content-Type': 'application/json' },
body: JSON.stringify({ event_id: EVENT_ID, code, subtotal })
});
const data = await resp.json();

if (data.valid) {
discount = data.discount;
document.getElementById('hidden_promo').value = code;
document.getElementById('discount_display').textContent = '-₹' +
discount.toFixed(2);
document.getElementById('discount_row').classList.remove('d-none');
msgEl.className = 'form-text text-success mt-1';
msgEl.textContent = data.message;
updateTotal();
} else {
discount = 0;
document.getElementById('hidden_promo').value = '';
document.getElementById('discount_row').classList.add('d-none');
msgEl.className = 'form-text text-danger mt-1';
msgEl.textContent = data.message;
updateTotal();
}
}
</script>
{% endblock %}
{% endblock %}


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

**Part C**

#### Write templates/attendee/payment_summary.html — Order

#### Summary

Create templates/attendee/payment_summary.html in VS Code. This is wireframe page 4 — the order
summary before PayPal payment. The PayPal button will be wired tomorrow on Day 10. Today it shows

as a placeholder.

{% extends 'base.html' %}
{% block title %}Complete Payment — {{ event.name }}{% endblock %}

{% block content %}
<div class="container py-4">
<div class="row justify-content-center">
<div class="col-lg-6">

<div class="ems-card card p-4">
<h4 class="fw-bold mb-4 text-center">
<i class="bi bi-receipt me-2 text-primary"></i>Complete Payment
</h4>

{# Order summary — wireframe page 4 #}
<div class="border rounded p-3 mb-4 bg-light">
<h6 class="fw-bold text-muted text-uppercase small mb-3">Order
Summary</h6>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Event</span>
<span class="fw-semibold">{{ event.get('name','') }}</span>
</div>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Ticket</span>
<span>{{ reg.ticket_type_name }} x {{ reg.quantity }}</span>
</div>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Subtotal</span>
<span>₹{{ reg.subtotal }}</span>
</div>
{% if reg.discount_amount and reg.discount_amount > 0 %}
<div class="d-flex justify-content-between mb-2 text-success">
<span>Discount ({{ reg.promo_code_used }})</span>
<span>-₹{{ reg.discount_amount }}</span>
</div>
{% endif %}
<hr>
<div class="d-flex justify-content-between fw-bold fs-5">
<span>Total</span>
<span class="text-primary">₹{{ reg.total_amount }}</span>
</div>
<div class="text-muted small mt-2">Order #: {{ reg.order_number
}}</div>
</div>

{# PayPal button — placeholder today, wired on Day 10 #}
<div id="paypal-button-container" class="mb-3">
<div class="alert alert-info text-center">


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

<i class="bi bi-paypal me-2"></i>
PayPal payment integration coming on Day 10.
<br><small class="text-muted">Registration ID: {{ registration_id
}}</small>
</div>
</div>

<p class="text-muted small text-center">
By completing this purchase, you agree to our
<a href="#">Terms and Conditions</a>.
</p>
</div>
</div>
</div>
</div>
{% endblock %}


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

**Part D**

#### Wire Up, Fix Select & Register Button, Rebuild & Test

##### Step 1 — Register blueprint in app.py

from app.routes.registration import registration_bp
app.register_blueprint(registration_bp)

##### Step 2 — Exempt validate-promo from CSRF in app.py

The promo validation uses fetch() with JSON body, not a form. Add it to the csrf exempt list in app.py:

csrf.exempt(registration_bp) # validate-promo uses fetch() not form POST

##### Step 3 — Fix the Select & Register button in event_detail.html

On Day 5 we disabled this button with a placeholder. Open templates/public/event_detail.html and find

the disabled button we added:

{# FIND and REMOVE this disabled placeholder button: #}
<button class="btn btn-outline-primary btn-sm mt-2 w-100" disabled>
<i class="bi bi-check-circle me-1"></i>Select & Register
<span class="badge bg-secondary ms-1">Coming soon</span>
</button>

Replace it with this real link:

<a href="{{ url_for('registration.register', event_id=event.id,
ticket_type_id=tt.id) }}"
class="btn btn-outline-primary btn-sm mt-2 w-100">
<i class="bi bi-check-circle me-1"></i>Select & Register
</a>

##### Step 4 — Create a Firestore index for promo validation

The promo code query uses 3 where() clauses — Firestore needs a composite index for this. Run

docker compose up and visit the registration page. If an index error appears in the logs, copy the URL
and create the index. Manual creation if needed:

**Collection Fields Purpose**

**promo_codes** event_id ASC + code ASC + Promo code lookup during registration


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

is_active ASC

**registrations** event_id ASC + attendee_email ASC Manual check-in search (Day 8)

##### Step 5 — Rebuild and test

docker compose down
docker compose build
docker compose up

**Action What to test Expected result**

Visit event detail as attendee Click Select & Register on a ticket Registration form loads with ticket name and price

Quantity selector Change quantity to 2 Subtotal updates to 2x the ticket price

Sold out ticket Try to access a soldURL directly - out ticket Flash: Sold out. Redirect to event detail

Max quantity exceeded Select more than max_per_order Flash: Please select between 1 and X tickets

Promo code — invalid Type FAKECODE and click Apply Red message: Invalid promo code

Promo code — valid (if you have
one)

```
Type a real promo code from
Firestore
```
```
Green message: Promo applied!
Discount row appears
```
Submit form — paid ticket Click Proceed to Payment Payment summary page loads with
order details

Payment summary Check all fields Event name, ticket, quantity, subtotal, total all correct

Order number Check format Shows ORD-XXXXXXXX format

Free ticket registration Register for a ₹0 ticket

```
Immediately confirmed, redirected to
My Events
```
🎯 **Day 9 deliverable: Attendee can select a ticket, see a registration form with live**

**promo code validation and total calculation, and reach the payment summary page.**

**Free tickets are confirmed immediately.**

##### Step 6 — Commit

git add.
git status # confirm secrets/ and .env not listed
git commit -m "Day 9: registration flow, promo validation, payment summary
page"
git push origin develop


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

## End-of-Day 9 Checklist

**Item**^ **How to verify**^

###### ☐

```
app/routes/registration.py Has register(), validate_promo(),
payment_summary() routes
```
###### ☐

```
get_event_and_ticket() helper Aborts 404 if event not published or ticket not
found
```
###### ☐

```
apply_promo() helper Checks validity dates, usage limit, calculates
discount
```
###### ☐

```
Free ticket path ₹0 tickets confirmed immediately without
PayPal
```
###### ☐

```
Pending registration created Firestore registrations collection has new doc
with status=pending
```
###### ☐

```
Order number generated Format ORD-XXXXXXXX using
secrets.token_hex
```
###### ☐

```
templates/attendee/register.html Quantity selector, email pre-fill, promo input,
live total
```
###### ☐

```
templates/attendee/payment_summary.html Order breakdown with event, ticket, subtotal,
discount, total
```
###### ☐ registration_bp registered in app.py^ Blueprint imported and registered^

###### ☐

```
csrf.exempt(registration_bp) Promo validate-promo fetch() works without
CSRF error
```
###### ☐

```
Select & Register button fixed Event detail page button links to registration
form
```
###### ☐

```
Sold-out guard works Accessing sold-out ticket URL shows flash
and redirects
```
###### ☐

```
Quantity validation Selecting more than max_per_order shows
error
```
###### ☐ Invalid promo shows error^ Red message on invalid code^

###### ☐ Valid promo updates total^ Discount row appears, total reduces^

###### ☐ Payment summary loads^ All order fields correct after form submission^

###### ☐ Committed & pushed^ GitHub develop branch shows Day 9 commit^

## What's Next — Day 10 Preview


###### EMS — Day 9 Guide | Registration Flow | Sprint 2 | Wireframe Page 3

Tomorrow is the most technically complex day of the project — PayPal payment integration. You will

wire the PayPal Checkout button on the payment summary page, handle the PayPal webhook that

confirms payment, and update the registration status to confirmed.

- Payment summary page gets a real PayPal Checkout JS button
- POST /payment/create-order — creates a PayPal order with the correct amount
- POST /payment/capture-order — captures approved payment, verifies amount
- PayPal webhook/IPN confirmation updates registration status to confirmed
- Atomically increments total_registrations and quantity_sold in Firestore
- ngrok setup for local webhook testing

**Prepare for Day 10**

Before Day 10, set up your PayPal Sandbox accounts at developer.paypal.com:

1. Log in with your PayPal account (or create one free)
2. Go to Sandbox → Accounts
3. Create a Sandbox Business account (the merchant — receives payments)
4. Create a Sandbox Personal account (the buyer — makes payments)
5. Note the Client ID and Secret from Apps & Credentials → Create App

You will need these on Day 10.

**Tag and backup**

After completing Day 9:

git tag v0.0.9-day9 -m 'Day 9 complete — registration flow, promo codes, payment summary'

git push origin v0.0.9-day9

cp -r /d/projects/event-management-system /d/projects/event-management-system-day9-backup


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

# Day 10 — Setup Guide

## PayPal Payment Integration

Sprint 2 | Wireframe Page 4 | 5 Parts | ~6 hours

**Part A**

```
Set up PayPal Sandbox — accounts, app
credentials, ngrok (~45 min)
```
**Part B**

```
Write app/routes/payment.py — create-order and
capture-order routes (~75 min)
```
**Part C**

```
Update payment_summary.html — wire the real
PayPal JS SDK button (~30 min)
```
**Part D**

```
Update registration on payment success —
confirm, increment counters (~30 min)
```
**Part E**

```
Rebuild Docker, test full payment flow end to end
(~30 min)
```
**End goal**

```
Attendee pays via PayPal Sandbox, registration
status changes to confirmed, counters increment.
```

###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

## Overview — How PayPal Checkout works

PayPal Checkout uses the PayPal JavaScript SDK on the frontend and your Flask backend for order

creation and capture. The flow has exactly 4 steps:

**# Who What happens**

**1 Browser (JS)** PayPal button renders. Attendee clicks it.

**2 Flask backend** POST /payment/createamount. Returns PayPal order ID.-order —^ creates a PayPal order with the correct

**3 PayPal (popup)** Attendee logs in to their PayPal Sandbox buyer account and approves the payment.

**4 Flask backend** POST /payment/captureamount. Updates registration to confirmed. Increments Firestore counters.-order —^ captures the approved payment. Verifies

**Sandbox vs Production**

Today we use PayPal Sandbox — a complete test environment with fake money. The code is
identical to production. When you are ready to go live, you simply change PAYPAL_MODE from
sandbox to live and replace the sandbox credentials with your real PayPal merchant credentials.

##### New files today

**event-management-system/**
├── app/routes/
│ └── payment.py ← NEW — PayPal create-order &
capture-order
├── templates/attendee/
│ └── payment_summary.html ← UPDATE — add real PayPal JS button
└── app.py ← UPDATE — register payment
blueprint


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

**Part A**

#### Set Up PayPal Sandbox — Accounts, Credentials & ngrok

##### Step 1 — Create PayPal Developer Account

- Open your browser and go to: https://developer.paypal.com
- Click Log In — use your existing PayPal account, or create a free one
- Once logged in, you land on the Developer Dashboard

##### Step 2 — Create a Sandbox App (get Client ID and Secret)

- In the left sidebar, click Apps & Credentials
- Make sure you are on the Sandbox tab (not Live)
- Click Create App
- App Name: type EMS-Development
- App Type: select Merchant
- Click Create App
- You will see your Client ID and Secret Key — copy both

Open your .env file in VS Code and fill in these values:

PAYPAL_MODE=sandbox
PAYPAL_CLIENT_ID=AYour-actual-sandbox-client-id-here
PAYPAL_CLIENT_SECRET=EYour-actual-sandbox-secret-here

##### Step 3 — Find your Sandbox Buyer account

- In the left sidebar, click Sandbox → Accounts
- You will see two default accounts — one Personal (buyer) and one Business (merchant)
- Click on the Personal account → click View/Edit Account
- Note the Email and Password — you will use these to log in when testing payment

**You will use the Sandbox Personal account to simulate a buyer making a payment during**

**testing. Use the email and password shown here — not your real PayPal credentials.**

E6F1FB

##### Step 4 — Set up ngrok for webhook testing

PayPal needs a publicly accessible URL to send webhook notifications. ngrok creates a temporary

public URL that tunnels to your localhost. This is only needed for webhook testing — the Checkout

button works without it.


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

- Go to https://ngrok.com and create a free account
- Download ngrok for Windows — extract the .exe file
- Open a NEW Git Bash window (keep Docker running in the other one)
- Navigate to where you extracted ngrok and run:

./ngrok http 5000

- ngrok will show a Forwarding URL like: https://abc123.ngrok-free.app → localhost:5000
- Copy that https URL — you will use it in the next step

**ngrok free tier limitation**

The free ngrok URL changes every time you restart ngrok. For today's testing this is fine. Keep the
ngrok terminal window open the entire time you are testing payments.

##### Step 5 — Add ngrok URL to .env

Open .env and add this line — replace with your actual ngrok URL:

PAYPAL_WEBHOOK_URL=https://abc123.ngrok-free.app


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

**Part B**

#### Write app/routes/payment.py — PayPal Routes

Create app/routes/payment.py in VS Code:

import os, requests
from flask import (Blueprint, request, jsonify,
session, redirect, url_for, flash)
from app.firebase_config import db
from app.decorators import login_required
from google.cloud.firestore import SERVER_TIMESTAMP, Increment

payment_bp = Blueprint('payment',__name__,url_prefix='/payment')

PAYPAL_BASE = {
'sandbox': 'https://api-m.sandbox.paypal.com',
'live': 'https://api-m.paypal.com'
}

# ── Helper: get PayPal access token ──────────────────────────────────
def get_paypal_token():
mode = os.getenv('PAYPAL_MODE','sandbox')
base = PAYPAL_BASE[mode]
cid = os.getenv('PAYPAL_CLIENT_ID')
secret = os.getenv('PAYPAL_CLIENT_SECRET')

resp = requests.post(
f'{base}/v1/oauth2/token',
headers={'Accept':'application/json','Accept-Language':'en_US'},
data='grant_type=client_credentials',
auth=(cid, secret),
timeout= 10
)
return resp.json().get('access_token')

# ── CREATE ORDER — called by PayPal JS SDK on button click ───────────
@payment_bp.route('/create-order',methods=['POST'])
@login_required
def create_order():
"""Creates a PayPal order. Returns the PayPal order ID to the JS SDK."""
registration_id = request.json.get('registration_id')
if not registration_id:
return jsonify({'error':'Missing registration ID'}),400

# Fetch registration to get the amount
reg_doc = db.collection('registrations').document(registration_id).get()
if not reg_doc.exists:
return jsonify({'error':'Registration not found'}),404

reg = reg_doc.to_dict()

# Security: only the attendee who owns this registration can pay
if reg.get('attendee_uid') != session.get('uid'):


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

return jsonify({'error':'Unauthorised'}),403

amount = str(round(reg.get('total_amount',0), 2 ))

mode = os.getenv('PAYPAL_MODE','sandbox')
base = PAYPAL_BASE[mode]
token = get_paypal_token()

resp = requests.post(
f'{base}/v2/checkout/orders',
headers={
'Content-Type':'application/json',
'Authorization':f'Bearer {token}'
},
json={
'intent':'CAPTURE',
'purchase_units':[{
'reference_id':registration_id,
'description':f"EMS Ticket: {reg.get('ticket_type_name','')}",
'amount':{'currency_code':'USD','value':amount}
}],
'application_context':{
'return_url':url_for('payment.success',_external=True,registration_id=registration_id),

'cancel_url':url_for('registration.payment_summary',_external=True,registration_id=registration_id)
}
},
timeout= 10
)
data = resp.json()
if resp.status_code != 201 :
return jsonify({'error':data}), 400

return jsonify({'orderID':data['id']})

# ── CAPTURE ORDER — called after buyer approves in PayPal popup ───────
@payment_bp.route('/capture-order',methods=['POST'])
@login_required
def capture_order():
"""Captures the approved PayPal order and confirms the registration."""
data = request.json
order_id = data.get('orderID')
registration_id = data.get('registration_id')

mode = os.getenv('PAYPAL_MODE','sandbox')
base = PAYPAL_BASE[mode]
token = get_paypal_token()

# Capture the payment with PayPal
resp = requests.post(
f'{base}/v2/checkout/orders/{order_id}/capture',
headers={
'Content-Type':'application/json',
'Authorization':f'Bearer {token}'
},
timeout= 10
)
capture_data = resp.json()


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

if capture_data.get('status') != 'COMPLETED':
return jsonify({'error':'Payment not completed'}),400

# Extract PayPal transaction details
capture = capture_data['purchase_units'][ 0 ]['payments']['captures'][ 0 ]
transaction_id = capture['id']
captured_amount = float(capture['amount']['value'])

# Fetch registration to confirm amounts match
reg_ref = db.collection('registrations').document(registration_id)
reg_doc = reg_ref.get()
if not reg_doc.exists:
return jsonify({'error':'Registration not found'}),404

reg = reg_doc.to_dict()

# Verify captured amount matches expected total (fraud prevention)
expected = round(reg.get('total_amount',0), 2 )
if abs(captured_amount - expected) > 0.01:
return jsonify({'error':'Amount mismatch'}),400

# Update registration to confirmed
reg_ref.update({
'status': 'confirmed',
'payment_status': 'paid',
'paypal_order_id': order_id,
'paypal_transaction_id': transaction_id,
'confirmed_at': SERVER_TIMESTAMP,
})

# Atomically increment event counters
event_id = reg.get('event_id')
db.collection('events').document(event_id).update({
'total_registrations': Increment( 1 ),
'total_revenue': Increment(captured_amount)
})

# Atomically increment ticket quantity_sold
ticket_type_id = reg.get('ticket_type_id')

db.collection('events').document(event_id).collection('ticket_types').document(ticket_type_id).update({
'quantity_sold': Increment(reg.get('quantity',1))
})

return jsonify({
'success':True,
'transaction_id':transaction_id,
'redirect_url':url_for('payment.success',_external=True,registration_id=registration_id)
})

# ── PAYMENT SUCCESS PAGE ─────────────────────────────────────────────
@payment_bp.route('/success/<registration_id>')
@login_required
def success(registration_id):
reg_doc = db.collection('registrations').document(registration_id).get()
if not reg_doc.exists:
flash('Registration not found.','danger')
return redirect(url_for('public.index'))
reg = reg_doc.to_dict()


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

event_doc = db.collection('events').document(reg['event_id']).get()
event = event_doc.to_dict() if event_doc.exists else {}
return render_template('attendee/success.html',reg=reg,event=event,registration_id=registration_id)


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

**Part C**

#### Update payment_summary.html — Wire Real PayPal Button

Open templates/attendee/payment_summary.html in VS Code. Replace the entire file with this
updated version that includes the real PayPal JS SDK button:

{% extends 'base.html' %}
{% block title %}Complete Payment — {{ event.name }}{% endblock %}

{% block extra_head %}
{# PayPal JS SDK — loads the PayPal button #}
<script src="https://www.paypal.com/sdk/js?client-id={{ paypal_client_id
}}&currency=USD"></script>
{% endblock %}

{% block content %}
<div class="container py-4"><div class="row justify-content-center"><div
class="col-lg-6">
<div class="ems-card card p-4">
<h4 class="fw-bold mb-4 text-center">
<i class="bi bi-receipt me-2 text-primary"></i>Complete Payment
</h4>

{# Order summary #}
<div class="border rounded p-3 mb-4 bg-light">
<h6 class="fw-bold text-muted text-uppercase small mb-3">Order
Summary</h6>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Event</span>
<span class="fw-semibold">{{ event.get('name','') }}</span>
</div>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Ticket</span>
<span>{{ reg.ticket_type_name }} x {{ reg.quantity }}</span>
</div>
<div class="d-flex justify-content-between mb-2">
<span class="text-muted">Subtotal</span>
<span>₹{{ reg.subtotal }}</span>
</div>
{% if reg.discount_amount and reg.discount_amount > 0 %}
<div class="d-flex justify-content-between mb-2 text-success">
<span>Discount ({{ reg.promo_code_used }})</span>
<span>-₹{{ reg.discount_amount }}</span>
</div>
{% endif %}
<hr>
<div class="d-flex justify-content-between fw-bold fs-5">
<span>Total</span>
<span class="text-primary">₹{{ reg.total_amount }}</span>
</div>
<div class="text-muted small mt-2">Order #: {{ reg.order_number
}}</div>
</div>

{# PayPal button container #}


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

<div id="paypal-button-container" class="mb-3"></div>
<div id="payment-error" class="alert alert-danger d-none"></div>

<p class="text-muted small text-center">
By completing this purchase, you agree to our
<a href="#">Terms and Conditions</a>.
</p>
</div>
</div></div></div>

{% block extra_js %}
<script>
const REGISTRATION_ID = '{{ registration_id }}';
const CREATE_URL = '/payment/create-order';
const CAPTURE_URL = '/payment/capture-order';

paypal.Buttons({
style: { layout: 'vertical', color: 'blue', shape: 'rect', label: 'pay'
},

// Step 1: Create the PayPal order
createOrder: async () => {
const resp = await fetch(CREATE_URL, {
method: 'POST',
headers: { 'Content-Type': 'application/json' },
body: JSON.stringify({ registration_id: REGISTRATION_ID })
});
const data = await resp.json();
if (data.error) throw new Error(data.error);
return data.orderID;
},

// Step 2: Capture the payment after buyer approves
onApprove: async (data) => {
const resp = await fetch(CAPTURE_URL, {
method: 'POST',
headers: { 'Content-Type': 'application/json' },
body: JSON.stringify({
orderID: data.orderID,
registration_id: REGISTRATION_ID
})
});
const result = await resp.json();
if (result.success) {
window.location.href = result.redirect_url;
} else {
document.getElementById('payment-error').textContent = result.error
|| 'Payment failed.';
document.getElementById('payment-error').classList.remove('d-none');
}
},

// Step 3: Handle cancellation
onCancel: () => {
document.getElementById('payment-error').textContent = 'Payment
cancelled. You can try again.';
document.getElementById('payment-error').classList.remove('d-none');
},

// Step 4: Handle errors


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

onError: (err) => {
document.getElementById('payment-error').textContent = 'A payment error
occurred. Please try again.';
document.getElementById('payment-error').classList.remove('d-none');
}
}).render('#paypal-button-container');
</script>
{% endblock %}
{% endblock %}


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

**Part D**

#### Create Success Page & Wire Up Everything

##### Step 1 — Create templates/attendee/success.html

Create templates/attendee/success.html in VS Code — this is wireframe page 5:

{% extends 'base.html' %}
{% block title %}Registration Confirmed!{% endblock %}

{% block content %}
<div class="container py-5">
<div class="row justify-content-center">
<div class="col-lg-6 text-center">

<div class="mb-4">
<i class="bi bi-check-circle-fill text-success" style="font-
size:4rem"></i>
</div>
<h2 class="fw-bold mb-2">Thank You for Registering!</h2>
<p class="text-muted mb-4">Your registration for <strong>{{
event.get('name','') }}</strong> is confirmed.</p>

{# Ticket summary card — wireframe page 5 #}
<div class="ems-card card p-4 mb-4 text-start">
<h5 class="fw-bold mb-3"><i class="bi bi-ticket-perforated me-2 text-
primary"></i>Your Ticket</h5>
<table class="table table-borderless mb-0">
<tr><td class="text-muted">Name</td>
<td class="fw-semibold">{{ reg.attendee_name }}</td></tr>
<tr><td class="text-muted">Ticket</td>
<td>{{ reg.ticket_type_name }}</td></tr>
<tr><td class="text-muted">Quantity</td>
<td>{{ reg.quantity }}</td></tr>
<tr><td class="text-muted">Order #</td>
<td class="fw-bold text-primary">{{ reg.order_number
}}</td></tr>
<tr><td class="text-muted">Amount Paid</td>
<td>₹{{ reg.total_amount }}</td></tr>
</table>

{# QR code will appear here after Day 11 #}
{% if reg.qr_code_url %}
<div class="text-center mt-3">
<img src="{{ reg.qr_code_url }}" alt="QR Code" class="img-fluid"
style="max-width:200px">
</div>
{% else %}
<div class="alert alert-info mt-3 small">
<i class="bi bi-info-circle me-1"></i>
Your QR code ticket will be emailed to {{ reg.attendee_email }}.
QR generation coming on Day 11.
</div>
{% endif %}
</div>


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

{# Action buttons — wireframe page 5 #}
<div class="d-flex gap-2 justify-content-center flex-wrap">
<a href="{{ url_for('attendee.my_events') }}" class="btn btn-
primary">
<i class="bi bi-calendar-check me-2"></i>View My Events
</a>
<a href="{{ url_for('public.index') }}" class="btn btn-outline-
secondary">
<i class="bi bi-search me-2"></i>Browse More Events
</a>
</div>

</div>
</div>
</div>
{% endblock %}

##### Step 2 — Register payment blueprint and pass Client ID in app.py

Open app.py. Add the blueprint import and registration:

from app.routes.payment import payment_bp
app.register_blueprint(payment_bp)
csrf.exempt(payment_bp) # PayPal JS SDK sends JSON, not form POST

Also add a context processor so the PayPal Client ID is available in all templates:

@app.context_processor()
def inject_paypal_client_id():
return dict(paypal_client_id=os.getenv('PAYPAL_CLIENT_ID',''))


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

**Part E**

#### Rebuild Docker & Test the Full Payment Flow

##### Step 1 — Rebuild

docker compose down
docker compose build
docker compose up

##### Step 2 — Full end-to-end payment test

**Action What to test Expected result**

Visit event detail as attendee Click Select & Register Registration form loads

Fill form and click Proceed to
Payment Submit registration form^ Payment summary page loads^

Payment summary page Check PayPal button

```
Blue PayPal button renders (not
placeholder)
```
Click PayPal button Button click PayPal popup or redirect opens

Log in with Sandbox buyer Use Personal sandbox email/password PayPal checkout screen appears with correct amount

Approve the payment Click Pay Now in PayPal

```
Redirected back to
/payment/success/<id>
```
Success page Check all fields Name, ticket, order number, amount
paid shown

Firestore Console Check registration doc

```
status=confirmed,
payment_status=paid,
paypal_transaction_id set
```
Event dashboard Check organizer dashboard total_registrations and total_revenue
incremented

Ticket availability Check event detail page quantity_sold incremented, available count reduced

🎯 **Day 10 deliverable: Attendee pays via PayPal Sandbox, registration confirmed in**

**Firestore, all counters increment. The core payment loop is complete.**

##### Step 3 — Commit

git add.
git status # confirm secrets/ and .env not listed
git commit -m "Day 10: PayPal Sandbox payment integration, create-order,
capture-order, success page"
git push origin develop


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

## End-of-Day 10 Checklist

**Item**^ **How to verify**^

###### ☐ PayPal Sandbox app created^ Client ID and Secret copied from developer.paypal.com^

###### ☐

```
PAYPAL_CLIENT_ID in .env Payment summary page loads PayPal JS SDK without
error
```
###### ☐

```
app/routes/payment.py created Has get_paypal_token(), create_order(), capture_order(),
success() routes
```
###### ☐ Amount verification^ captured_amount vs expected check prevents fraud^

###### ☐

```
Firestore updates on capture status=confirmed, payment_status=paid, transaction_id
saved
```
###### ☐

```
Counters increment total_registrations, total_revenue, quantity_sold all
increment
```
###### ☐ payment_summary.html updated^ Real PayPal button renders —^ not the Day 9 placeholder^

###### ☐ templates/attendee/success.html^ Shows registration details, order number, QR placeholder^

###### ☐ payment_bp registered^ Blueprint imported and registered in app.py^

###### ☐ csrf.exempt(payment_bp)^ PayPal JS SDK fetch() calls work without CSRF error^

###### ☐ context_processor^ paypal_client_id available in all templates^

###### ☐ PayPal button renders^ Blue Pay button visible on payment summary page^

###### ☐ Sandbox payment approved^ Popup/redirect shows correct amount in USD^

###### ☐

```
Success page loads After payment, /payment/success/<id> shows
confirmation
```
###### ☐ Committed & pushed^ GitHub develop branch shows Day 10 commit^

## What's Next — Day 11 Preview

Tomorrow you complete the payment confirmation loop — generating the QR code ticket and sending

the confirmation email via SendGrid. After Day 11 the attendee will receive their ticket by email
immediately after payment.

- Generate HMAC-signed QR code PNG using qr_utils.py (already written Day 8)
- Upload QR code PNG to Firebase Storage, save URL to registration document
- Design HTML email template with event details and embedded QR code
- Send confirmation email via SendGrid API


###### EMS — Day 10 Guide | PayPal Payment Integration | Sprint 2 | Wireframe Page 4

- Update the success page to show the real QR code image
- QR code can then be scanned by the Day 8 check-in scanner

**Tag and backup reminder**

After completing Day 10:

git tag v0.0.10-day10 -m 'Day 10 complete — PayPal payment integration working'

git push origin v0.0.10-day10

cp -r /d/projects/event-management-system /d/projects/event-management-system-day10-backup
