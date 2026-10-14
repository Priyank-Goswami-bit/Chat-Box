# Chatbot & Support Ticket System

One project, three parts, one database.

| Part | What it does |
| --- | --- |
| `backend/` | Node + Express + SQLite API: login, chatbot, FAQs, tickets, email notifications |
| `frontend/` | One Angular app: customer chat widget, login/sign up, admin dashboard |
| SQLite file | `backend/src/database/support_system.sqlite`, created automatically on first start |

Everything you see on screen is read from and saved to the database. There is no dummy data.

## Run it

Needs Node.js 20.19+ or 22.12+.

```bash
npm run setup     # installs backend + frontend and builds the frontend (once)
npm start         # starts everything on http://localhost:5000
```

Open http://localhost:5000.

On first start the server creates:
- one admin account (from `backend/.env`: `admin@support.com` / `Admin@123`, change these before going live)
- 27 starter FAQs for the chatbot (edit or delete them from the admin dashboard)

Customers create their own accounts with **Sign up**.

## Pages

| URL | Who | What |
| --- | --- | --- |
| `/` | Everyone | Chat widget. The chatbot answers from the FAQs in the database. Raising a ticket and My tickets need login |
| `/login` | Everyone | Log in or sign up. Admins are sent to `/admin`, everyone else to `/` |
| `/admin` | Admin only | Ticket list with filters and live counts, reply, change status, assign, FAQ manager |

## How the pieces connect

- Chatbot: type a question, the backend scores it against FAQ question, keywords and answer. A good match returns the FAQ answer. No match offers to raise a ticket with category and subject pre-filled.
- Customer raises a ticket: saved in the database, confirmation email goes to the customer and admin.
- Admin replies or changes status: customer sees it under **My tickets** (a badge on the chat button shows new replies, checked every 30 seconds) and gets an email.
- Customer replying on a Resolved ticket reopens it. Closed tickets cannot be replied to.
- Admin FAQ changes are used by the chatbot immediately. Fill in **Keywords** for better matching.

## Email

By default emails are printed in the server console. To send real emails, fill `EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_USER`, `EMAIL_PASS` in `backend/.env`.

## Development mode (auto reload)

Two terminals:

```bash
npm run dev:backend      # API on :5000
npm run dev:frontend     # Angular on http://localhost:4200, proxies /api to :5000
```

## Tests

```bash
npm test
```

Runs 25 API checks (login, roles, chatbot, tickets, FAQs) against a temporary database. Your real data is not touched.

## Adding another admin

Set `ADMIN_EMAIL`, `ADMIN_NAME`, `ADMIN_PASSWORD` in `backend/.env`, then delete the existing admin row, or insert a user with `user_type = 'admin'` in the database. Admin accounts cannot be created from the sign up page on purpose.

## Before going live

- Change `JWT_SECRET` and the admin password in `backend/.env`.
- Fill in the email settings.
