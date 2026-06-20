# Orderly Hub

A full-stack restaurant management system with three role-based dashboards — Admin, Waiter, and Customer — built with a Python Flask backend and a React + TypeScript frontend.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, TypeScript, Vite, Tailwind CSS, shadcn/ui, Recharts |
| Backend | Python Flask, Flask-RESTful, Flask-Security |
| Database | SQLite (via SQLAlchemy ORM) |
| AI Feature | scikit-learn (TF-IDF + cosine similarity) |
| Auth | Token-based authentication (Flask-Security) |

---

## Project Structure

```
orderly-hub-final/
├── app.py                        # Flask entry point
├── requirements.txt              # Python dependencies
├── backend/
│   ├── config.py                 # App config, DB URI, secret key
│   ├── database.py               # SQLAlchemy instance
│   ├── models.py                 # DB models: User, Order, MenuItem, Table, Feedback
│   ├── routes.py                 # Auth routes: /api/login, /api/register, /api/logout
│   └── resources/
│       ├── __init__.py           # Flask-RESTful API registration
│       ├── menu_api.py           # GET/POST/PUT/DELETE /api/menu, /api/categories
│       ├── order_api.py          # GET/POST/PUT /api/orders, /api/order_items
│       ├── table_api.py          # GET/POST/PUT/DELETE /api/tables
│       ├── feedback_api.py       # GET/POST /api/feedback
│       └── suggestion_api.py     # POST /api/suggestions (AI feature)
└── frontend/
    └── src/
        ├── services/api.ts       # All API calls, auth token management
        ├── contexts/AuthContext.tsx
        ├── pages/
        │   ├── admin/            # Dashboard, Menu, Tables, Orders, Staff, Feedback
        │   ├── waiter/           # Tables, Orders, Menu, Billing
        │   └── customer/         # Menu, Cart, Orders, Feedback
        └── components/ui/        # shadcn/ui component library
```

---

## Prerequisites

### macOS
- **Python 3.10+** — check with `python3 --version`
- **Node.js 18+** — check with `node --version`

Install via [Homebrew](https://brew.sh) if needed:
```bash
brew install python node
```

### Windows
- **Python 3.10+** — download from [python.org](https://www.python.org/downloads/)
  - ✅ During install, check **"Add Python to PATH"**
- **Node.js 18+** — download from [nodejs.org](https://nodejs.org/)

---

## Setup & Run

### Step 1 — Backend

#### macOS
```bash
cd orderly-hub-final

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt
pip install scikit-learn

python app.py
```

#### Windows
```cmd
cd orderly-hub-final

python -m venv venv
venv\Scripts\activate

pip install -r requirements.txt
pip install scikit-learn

python app.py
```

Flask starts at **http://localhost:5000**

> On first run, the app auto-creates the SQLite database, seeds the roles (owner, server, customer), creates a default admin account, and seeds the menu.

---

### Step 2 — Frontend

Open a **new terminal window**:

#### macOS
```bash
cd orderly-hub-final/frontend

npm install --legacy-peer-deps
npm run dev
```

#### Windows
```cmd
cd orderly-hub-final\frontend

npm install --legacy-peer-deps
npm run dev
```

Frontend starts at **http://localhost:8080**

> All `/api` requests are automatically proxied to Flask — no CORS configuration needed.

---

## Default Admin Account

A default owner account is created automatically on first run:

| Field | Value |
|---|---|
| Email | `owner@gmail.com` |
| Password | `1234` |
| Role | Admin / Owner |

Register Waiter and Customer accounts from the Register page.

---

## Role Mapping

| Frontend Label | Backend Role |
|---|---|
| Admin | `owner` |
| Waiter | `server` |
| Customer | `customer` |

---

## Features by Role

### Admin
- Dashboard with real-time weekly revenue graph and order stats
- Full menu management — create/edit/delete categories and items
- Table management — create, update status, delete
- View and complete orders
- View staff and customer accounts
- View all customer feedback

### Waiter
- View table layout and statuses
- Manage and update orders
- Browse menu
- Handle billing

### Customer
- Browse menu with category filtering
- AI-powered dish suggestions — tap any item to see 3 similar dishes recommended by TF-IDF cosine similarity
- Add items to cart and place orders
- Track order status
- Submit feedback and ratings

---

## AI Suggestion Feature

The suggestion engine (`backend/resources/suggestion_api.py`) uses **TF-IDF vectorization** and **cosine similarity** from scikit-learn to find dishes similar to the one a customer selects. It compares item names, descriptions, and category names — no external API is used.

**Endpoint:** `POST /api/suggestions`
```json
{ "itemId": 3 }
```
**Response:**
```json
{
  "itemId": 3,
  "selectedItem": "Butter Chicken",
  "suggestions": [
    { "id": 5, "name": "Paneer Butter Masala", "price": 300.0 },
    { "id": 8, "name": "Dal Makhani", "price": 220.0 },
    { "id": 2, "name": "Veg Biryani", "price": 250.0 }
  ]
}
```

---

## Running Tests

```bash
# With venv activated, from project root
pip install pytest
pytest tests/test_api.py -v
```

37 test cases across Auth, Tables, Menu, Orders, Feedback, and AI Suggestions.

---

## Common Issues

| Problem | Fix |
|---|---|
| `ModuleNotFoundError: sklearn` | `pip install scikit-learn` |
| `npm install` fails | Use `npm install --legacy-peer-deps` |
| Frontend shows blank page | Make sure Flask is running on port 5000 first |
| API calls return 401 | Log out and log back in to refresh the token |
| Port 8080 in use (macOS) | `lsof -i :8080` then kill the process |
| Port 8080 in use (Windows) | `netstat -ano \| findstr :8080` then `taskkill /PID <pid> /F` |
| `venv\Scripts\activate` not recognized (Windows) | Run `Set-ExecutionPolicy RemoteSigned` in PowerShell first |
