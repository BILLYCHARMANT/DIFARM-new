# DiFarm – Project Overview

**Digital Farm Management System** – Full-stack app for managing farms, cattle, production, stock, waste logs, vaccinations, veterinarians, and inseminations.

---

## Repository structure

```
difarm/
├── frontend/Difarm-Fe/     # React (Craco/CRA) SPA
├── backend/Difarm-Be/      # Express + TypeScript API
└── PROJECT_OVERVIEW.md     # This file
```

---

## Tech stack

### Backend (Difarm-Be)

- **Runtime:** Node.js
- **Framework:** Express
- **Language:** TypeScript (compiled with Babel/ts-node)
- **Database:** PostgreSQL with **Prisma** ORM
- **Auth:** Passport (local), JWT, express-session
- **Other:** bcrypt, joi (validation), nodemailer (email), morgan, cors

### Frontend (Difarm-Fe)

- **UI:** React 18, Craco (Create React App)
- **Styling:** Tailwind CSS, MUI, Mantine, Emotion
- **State:** Redux (RTK)
- **Routing:** React Router v6
- **HTTP:** Axios (base URL from `REACT_APP_SERVER_URL`)
- **i18n:** i18next
- **Other:** FullCalendar, ApexCharts, react-hook-form, Formik, Yup/Zod

---

## Data model (Prisma)

- **Account** – login (username/email/phone), role (SUPERADMIN, ADMIN, MANAGER), linked to **User**
- **User** – profile (fullname, gender, profilePic), linked to **Farm**(s)
- **Farm** – name, location, size, type, owner, manager, status
- **Cattle** – tagNumber, breed, gender, DOB, weight, status (HEALTHY/SICK/SOLD/PROCESSED), vaccine history
- **Production** – per cattle/farm (productName, quantity, dates)
- **ProductionTotals** / **ProductionTransaction** – by ProductType (MILK, MEAT, DUNG, LIQUIDMANURE)
- **WastesLog** – waste type (DUNG, LIQUIDMANURE), quantity, date
- **Stock** – name, quantity, type (FOOD, MEDICATION, etc.)
- **Transaction** – stock movements (ADDITION, CONSUME)
- **Vaccination** / **Veterinarian** / **Insemination** – health and breeding records

---

## API (backend)

- Base path: **`/api/v1`**
- Auth: **`/api/v1/auth`** (signup, login, logout, forgot/reset password, super-admin register)
- All other routes use **`isAuthorized`** middleware (JWT).
- Main route groups: **users**, **farms**, **cattles**, **productions**, **production-totals**, **production-transaction**, **stocks**, **stock-transactions**, **waste-logs**, **vaccinations**, **veterinarians**, **inserminations**.

---

## Frontend routes

- **/home** – Home
- **/login** – Login
- **/choose-farm** – Farm selection
- **/account** – Main app layout: Overview, profile, farms, users, production, stock, waste-logs, production_totals, production_transactions, stock_transactions, cattle, health
- **/stock** – Widget, suppliers, items

---

## Environment variables

### Backend (`backend/Difarm-Be/.env`)

| Variable | Purpose |
|----------|--------|
| `PORT` | Server port (default 4000) |
| `DATABASE_URL` | PostgreSQL connection string |
| `FRONTEND_UrL` | Frontend origin (CORS + password-reset links) |
| `JWT_SECRET` | Main JWT signing secret |
| `EXPIRE_TIME` | JWT expiry (e.g. 7d) |
| `JWT_VERIF_SECRET` | Email verification token secret |
| `EXPIRE_VERIF_TIME` | Verification token expiry (e.g. 24h) |
| `EMAIL_USERNAME` | Gmail address for nodemailer |
| `EMAIL_PASS` | Gmail app password |
| `EMAIL` | From address for emails |

A copy with placeholders is in **`.env.example`**. Duplicate it to `.env` and fill in real values.

### Frontend (`frontend/Difarm-Fe/.env`)

| Variable | Purpose |
|----------|--------|
| `REACT_APP_SERVER_URL` | Backend API base URL (e.g. http://localhost:4000) |
| `REACT_APP_NAME` | Optional app name (defaults to "farm") |

Create React App loads `.env` from the **project root** (`Difarm-Fe/`). There is also an older `.env` under `src/app/`; the root `.env` is the one that will be used by default.

---

## How to run on your machine

### 1. PostgreSQL

- Install PostgreSQL and create a database (e.g. `difarm`).
- Set `DATABASE_URL` in `backend/Difarm-Be/.env`, e.g.  
  `postgresql://postgres:YOUR_PASSWORD@localhost:5432/difarm`

### 2. Backend

```bash
cd backend/Difarm-Be
npm install
npx prisma generate
npx prisma db push
# or: npx prisma migrate dev
npm run dev
```

Server runs at **http://localhost:4000** (or the port in `PORT`).

### 3. Frontend

```bash
cd frontend/Difarm-Fe
npm install
npm start
```

App runs at **http://localhost:3000** and uses `REACT_APP_SERVER_URL` (e.g. http://localhost:4000) for API calls.

### 4. First super-admin

- Register the first super-admin via:  
  **POST** `/api/v1/auth/register/super`  
  (body validated by `adminValidation.adminSchema`).  
- Then log in and create other users/farms as needed.

---

## Summary

- **Backend:** Express + TypeScript + Prisma + PostgreSQL; JWT + Passport; env in `backend/Difarm-Be/.env` (see `.env.example`).
- **Frontend:** React + Craco; env in `frontend/Difarm-Fe/.env` with `REACT_APP_SERVER_URL` pointing to the backend.
- Update **DATABASE_URL**, **EMAIL_***, and **JWT_*** in backend `.env` for your environment; keep **FRONTEND_UrL** in sync with the URL where the React app is served.

Once this is done, you can run the app locally and apply the user feedback changes you have in mind.
