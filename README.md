# FixIt Project

A full-stack complaint management and maintenance request system for apartment complexes. Residents submit maintenance complaints (electrical, plumbing, general), admins assign them to available workers, and workers track and update complaint status in real-time. The system includes real-time notifications via WebSocket and Redis pub/sub architecture.

## What this is

**FixIt** is a complaint management platform designed for apartment complexes that enables:
- **Residents** to submit, track, and manage maintenance complaints
- **Workers** to receive assigned complaints and update their status with real-time notifications
- **Admins** to oversee all complaints, manage users, assign work, and monitor system logs

The system prioritizes efficiency by connecting residents with qualified workers and providing real-time communication through WebSocket notifications powered by Redis.

### Stack

- **Language(s):** JavaScript (ES modules)
- **Frontend:** React 19 + Vite + Tailwind CSS + React Router v7
- **Backend:** Node.js + Express 5 + Socket.io 4 + Mongoose (MongoDB)
- **Notable libraries:** 
  - `passport` & `passport-google-oauth20` — OAuth authentication
  - `bcryptjs` — password hashing
  - `jsonwebtoken` — JWT-based session management
  - `redis` — real-time notifications via pub/sub
  - `react-toastify` — toast notifications
  - `axios` — HTTP client for API calls

## How it's organized



**How it fits together:**

1. **Frontend (React + Vite)** serves the user interface with role-based routing (Admin, Resident, Worker).
2. **Backend (Express + Node.js)** handles API requests for complaints and user management.
3. **MongoDB (via Mongoose)** stores Users (with roles: resident/worker/admin) and Complaints (with statuses: pending/in-progress/resolved/rejected).
4. **Socket.io** establishes real-time bidirectional communication between the server and connected clients.
5. **Redis** acts as a message broker for pub/sub notifications—when admins assign a complaint or workers update status, Redis publishes events that Socket.io pushes to subscribed workers in real-time.
6. **Passport OAuth** enables Google Sign-In for streamlined authentication.

The request flow: Resident submits complaint → Admin assigns to worker → Worker receives real-time notification via Socket.io + Redis → Worker updates status → Notification sent back to resident.

## How to run it

### Prerequisites
- Node.js (v16+)
- MongoDB (local or Atlas cloud)
- Redis (local or cloud instance)
- Google OAuth credentials (for OAuth login)

### Backend

1. **Install dependencies:**
   ```bash
   cd backend
   npm install
