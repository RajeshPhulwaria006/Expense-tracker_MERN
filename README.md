# Expense Tracker — MERN

A full-stack personal expense tracker built with **MongoDB, Express, React, and Node.js**. It provides account authentication, expense management, filtering and pagination, and a dashboard with spending summaries and category breakdowns.

## Features

- Register and log in using email and password.
- JWT-protected application routes and API requests.
- Create, view, update, and delete expenses.
- Search expenses and filter by category, payment method, and date range.
- Paginated and sortable expense lists.
- Dashboard summaries, monthly spending, category breakdowns, and recent expenses.
- Centralized request validation and API error handling.

## Tech Stack

- **Frontend:** React, Vite, React Router, Axios, Recharts, Lucide React
- **Backend:** Node.js, Express
- **Database:** MongoDB with Mongoose
- **Authentication & validation:** JSON Web Token (JWT), bcryptjs, express-validator

## Project Structure

```text
Expense-tracker_MERN/
├── client/
│   ├── src/
│   │   ├── api/                 # Axios client and API functions
│   │   ├── components/          # Layout, navigation, protected routes, expense dialogs
│   │   ├── context/             # Authentication state
│   │   ├── pages/               # Login/register, dashboard, expenses, categories
│   │   └── styles/              # Page and component styles
│   ├── .env.example
│   └── package.json
└── server/
    ├── config/                  # MongoDB connection
    ├── controllers/             # Authentication, expense, and dashboard logic
    ├── middleware/              # Authentication, validation, and error handling
    ├── models/                  # User and Expense schemas
    ├── routes/                  # Authentication and expense routes
    ├── utils/                   # API error utility
    ├── validators/              # Request validation rules
    ├── app.js                   # Express app and middleware setup
    ├── server.js                # Environment loading, DB connection, server startup
    └── package.json
```

## Getting Started

### Prerequisites

- Node.js and npm
- A MongoDB instance (local MongoDB or MongoDB Atlas)

### 1. Clone the repository

```bash
git clone https://github.com/RajeshPhulwaria006/Expense-tracker_MERN.git
cd Expense-tracker_MERN
```

### 2. Configure the backend

Create `server/.env`:

```env
PORT=5000
NODE_ENV="development"
MONGODB_URI="mongodb://127.0.0.1:27017/expense_tracker"
JWT_SECRET="expense-tracker-123456"
JWT_EXPIRES_IN="1h"
CLIENT_URL="http://localhost:5173"
```

For MongoDB Atlas, replace `MONGODB_URI` with your Atlas connection string. Use a strong, private JWT secret. **Never commit `.env` files or real credentials.**

Install dependencies and start the API:

```bash
cd server
npm install
npm run dev
```

The API runs at `http://localhost:5000` by default.

### 3. Configure and start the frontend

In a second terminal:

```bash
cd Expense-tracker_MERN/client
```

Create `client/.env`:

```env
VITE_API_BASE_URL=http://localhost:5000/api
```

Then run:

```bash
npm install
npm run dev
```

Open the local URL printed by Vite (typically `http://localhost:5173`).

### Production frontend build

From the `client` directory:

```bash
npm run build
npm run preview
```

## API Overview

Base URL: `http://localhost:5000/api`

All request and response bodies use JSON unless otherwise noted.

| Method | Endpoint | Authentication | Purpose |
|---|---|---|---|
| `GET` | `/health` | No | Check API status |
| `POST` | `/auth/register` | No | Create an account |
| `POST` | `/auth/login` | No | Verify credentials and receive a JWT |
| `GET` | `/auth/me` | Bearer token | Return the authenticated user's profile |
| `POST` | `/auth/logout` | Bearer token | Acknowledge logout; the client clears its stored token |
| `GET` | `/expenses/summary` | Bearer token | Return spending totals, monthly total, category breakdown, and recent expenses |
| `POST` | `/expenses` | Bearer token | Create an expense |
| `GET` | `/expenses` | Bearer token | List expenses with pagination, filters, search, and sorting |
| `GET` | `/expenses/:id` | Bearer token | Retrieve one of the user's expenses |
| `PUT` | `/expenses/:id` | Bearer token | Update one of the user's expenses |
| `DELETE` | `/expenses/:id` | Bearer token | Delete one of the user's expenses |

### Expense list query parameters

`GET /expenses` supports:

- `page`, `limit` — pagination
- `category`, `paymentMethod` — exact filters
- `startDate`, `endDate` — date range
- `search` — searches the title and description
- `sortBy`, `order` — sorting field and `asc`/`desc` order

The summary endpoint also accepts `startDate` and `endDate`.

### Typical expense fields

Expense requests use fields such as `title`, `amount`, `category`, `description`, `paymentMethod`, and `date`. The backend associates each expense with the authenticated user automatically; clients should not send an owner ID to determine ownership.

## Authentication and Data Isolation

1. Registration stores the user through the User model, where passwords are hashed with `bcryptjs`.
2. Login compares the supplied password with the stored hash and issues a signed JWT.
3. The frontend attaches the token as `Authorization: Bearer <token>` to API requests.
4. Authentication middleware verifies the token, loads the associated user, and sets `req.user`.
5. Expense queries include the authenticated user's ID. Reading, updating, or deleting an expense requires both its ID and the current user's ID to match.

This ownership check prevents a logged-in user from accessing another user's expense simply by changing an expense ID. The frontend's protected routes improve navigation flow, while **backend authentication and ownership checks enforce access control**.

## Error Handling and Validation

The API validates relevant authentication, expense, and query inputs before controller logic runs. A centralized error handler standardizes API errors, and unknown endpoints return a not-found response. CORS is configured for the frontend origin; update `CLIENT_URL` when using a different frontend URL.

## Security Notes

- Keep `JWT_SECRET` and database credentials private.
- Use HTTPS and appropriately configured production CORS when deploying.
- Validate all incoming data on the server; frontend validation alone is not a security boundary.
- The logout endpoint uses bearer-token authentication and does not maintain a server-side token blacklist. The client logs out by clearing its stored token.

## License

No license file is currently included in the repository. Unless a license is added, reuse and redistribution are not automatically granted.
