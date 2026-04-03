# Arfath EMS – Technology Stack (Python + Firebase + PayPal + Docker)

Based on your requirements, here is the complete technology stack for building the Arfath Event Management System. This stack combines a Python backend with Firebase Firestore as the database, PayPal for payments, and Docker for deployment on a local server.

---

## 1. Overview
The application will be a traditional server-rendered web application with client-side interactivity. The Python backend serves HTML pages, handles business logic, and communicates with Firebase services (Firestore, Authentication, Storage). PayPal is integrated for payment processing. Docker containers ensure consistent deployment on your local server.

---

## 2. Frontend Stack

| Component          | Technology        | Justification |
|--------------------|-------------------|---------------|
| **HTML5**          | Semantic markup   | Foundation for structure. |
| **CSS3**           | Custom CSS + **Bootstrap 5** | Bootstrap provides responsive grid, pre-built components, and rapid UI development. |
| **JavaScript**     | Vanilla JS (ES6+) with **Fetch API** | For dynamic interactions, form submissions, and calling backend APIs. |
| **Optional**       | **Alpine.js**     | Lightweight framework for adding reactivity (e.g., toggling modals, form validation) without heavy dependencies. |
| **Templating**     | **Jinja2** (Flask) or **Django Templates** | Server-side rendering of dynamic content (event lists, registration forms). |

---

## 3. Backend Stack

| Component          | Technology        | Justification |
|--------------------|-------------------|---------------|
| **Web Framework**  | **Flask** (recommended) or **Django** | Flask is lightweight and flexible, ideal for integrating with Firebase. Django is also possible but adds ORM overhead we won't use. |
| **Firebase Integration** | **Firebase Admin SDK for Python** | Provides access to Firestore, Firebase Authentication, and Firebase Storage from the backend. |
| **Authentication** | **Firebase Authentication** | Handles user signup, login, password reset, and social logins. The backend verifies tokens using the Admin SDK. |
| **Background Tasks** | **Celery** (optional) or simple threading | For sending emails, processing webhooks, etc. |
| **Environment Variables** | `python-dotenv` | Manage API keys and configuration securely. |

---

## 4. Database – Firebase Firestore

| Component          | Technology        | Justification |
|--------------------|-------------------|---------------|
| **Primary Database** | **Firestore** (NoSQL) | Real-time capabilities, scalability, and seamless integration with Firebase Authentication. |
| **Data Modeling**  | Collections and documents as per ER diagram | We'll implement the schema using Firestore collections with references. |
| **Security**       | Firestore Security Rules | Define rules for client-side access if we ever use the Firebase JS SDK directly. For backend-only access, the Admin SDK bypasses rules. |

---

## 5. Payment Processing

| Component          | Technology        | Justification |
|--------------------|-------------------|---------------|
| **Payment Gateway**| **PayPal** (REST API or PayPal Checkout) | Integrate PayPal for receiving payments. Use PayPal's Python SDK or REST API. |
| **Webhooks**       | PayPal IPN (Instant Payment Notification) or Webhooks | To handle payment confirmations asynchronously. |

---

## 6. Infrastructure & Deployment

| Component          | Technology        | Justification |
|--------------------|-------------------|---------------|
| **Containerization**| **Docker**        | Package the Python app and any dependencies (Nginx, etc.) into containers for consistent deployment. |
| **Deployment Target** | Local server (main computer) | Run Docker containers on your local machine, accessible over the local network. |
| **Web Server**     | **Gunicorn** (inside container) + **Nginx** (optional) | Gunicorn serves the Flask app; Nginx can be used as reverse proxy for static files and load balancing. |
| **Orchestration**  | **Docker Compose** | Define multi-container setup (app, Nginx, maybe Redis) in a single YAML file. |
| **Static & Media** | Serve via Nginx or Whitenoise | For production, Nginx handles static files; for simplicity, Whitenoise can serve them directly from Gunicorn. |
| **Firebase Service Account** | JSON key file | Store securely in the container as a volume or environment variable. |

---

## 7. Third-Party Integrations

| Service            | Technology        | Purpose |
|--------------------|-------------------|---------|
| **Email**          | **SendGrid** or **SMTP** | Transactional emails (confirmation, reminders). SendGrid has a Python library. |
| **SMS**            | **Twilio** (future) | For SMS notifications. |
| **Video Streaming**| YouTube / Zoom (embed) | No direct integration; just embed URLs. |
| **CRM**            | HubSpot / Salesforce API (future) | Sync attendee data. |
| **Logging**        | **Sentry**        | Error tracking. |

---

## 8. Development Tools

| Tool               | Purpose |
|--------------------|---------|
| **VS Code**        | Code editor with Python extensions. |
| **pip + virtualenv**| Dependency management. |
| **Git + GitHub**   | Version control. |
| **Postman**        | API testing. |
| **Firebase Emulator Suite** | Local testing of Firestore and Auth. |
| **Docker Desktop** | Build and run containers locally. |

---

## 9. Project Structure (Flask Example)

```
arath-ems/
├── app/
│   ├── __init__.py              # Flask app factory
│   ├── config.py                 # Configuration (Firebase creds, PayPal keys)
│   ├── models/                    # Firebase data access layer (helper functions)
│   │   ├── user.py
│   │   ├── event.py
│   │   ├── registration.py
│   │   └── ...
│   ├── routes/                    # Blueprints for different modules
│   │   ├── auth.py
│   │   ├── events.py
│   │   ├── registration.py
│   │   ├── payment.py
│   │   └── dashboard.py
│   ├── templates/                 # Jinja2 HTML templates
│   │   ├── base.html
│   │   ├── index.html
│   │   ├── event_detail.html
│   │   └── ...
│   ├── static/                    # CSS, JS, images
│   │   ├── css/
│   │   ├── js/
│   │   └── ...
│   └── utils/                     # Helper functions (email, PayPal)
│       ├── email.py
│       └── paypal.py
├── requirements.txt
├── .env                            # Environment variables
├── docker-compose.yml              # Docker services definition
├── Dockerfile                       # Instructions to build the app image
├── nginx/                           # (Optional) Nginx config
│   └── default.conf
└── firebase-service-account.json   # Firebase Admin SDK credentials (keep secure)
```

---

## 10. How It All Works Together

1. **User requests a page** – Flask routes render HTML using Jinja2 templates.
2. **Data retrieval** – Flask calls helper functions that use Firebase Admin SDK to fetch data from Firestore.
3. **Authentication** – Firebase Auth handles user login. On the backend, we verify the Firebase ID token (sent via cookie or header) using the Admin SDK.
4. **Registration flow** – User fills registration form; JavaScript sends a POST request to Flask endpoint; Flask validates, creates a pending registration in Firestore, and initiates PayPal payment (creates PayPal order).
5. **Payment** – User is redirected to PayPal or PayPal popup completes. PayPal sends IPN/webhook to a Flask endpoint to confirm payment. On confirmation, Flask updates registration to confirmed, generates QR code (store in Firestore), and sends email.
6. **Docker deployment** – All components (Flask app, Nginx, maybe Redis) run in Docker containers on the local server. The app is accessible via the server's IP address on the local network.

---

## 11. Migration Notes (from previous Firebase-only plan)
- **Backend**: We now have a Python Flask server instead of Firebase Functions. This gives us full control over business logic.
- **Database**: Still Firestore, but accessed via Admin SDK.
- **Authentication**: Still Firebase Auth, but token verification happens in Flask.
- **Payments**: Stripe replaced with PayPal.
- **Deployment**: Docker on local server instead of Firebase Hosting.

This stack provides flexibility, scalability, and aligns with your technology preferences.