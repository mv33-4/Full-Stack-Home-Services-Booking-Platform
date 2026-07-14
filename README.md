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

### Backend Structure

backend/            Express server, routes, controllers, DB connector
  config/           config files (DB/clients)
  controllers/      request handlers (business logic)
  middleware/       auth, error handlers, etc.
  models/           DB schema/models (MongoDB)
  routes/           Express route modules (e.g., users, complaints, clients)
  utils/             helpers and utilities
  server.js         backend entrypoint (Express + Socket.IO + Redis)
  package.json
  
### Frontend Structure

frontend/           React + Vite application
  public/           static assets
  src/
    components/     reusable UI components
    pages/          route pages (Admin, Resident, Worker, etc.)
    contexts/       React contexts (auth, socket, etc.)
    utils/          client helpers (api, formatters)
    App.jsx         app-level routing and layout
    main.jsx        React mount / client setup
  package.json

## How It Works

### Data Flow

1. **User Registration/Login**
   - Users register with email/password or Google OAuth
   - Assigned roles: `resident`, `worker`, or `admin`
   - JWT tokens stored for authenticated requests

2. **Complaint Submission** (Resident)
   - Resident submits complaint with title, description, category (electrical/plumbing/general)
   - Complaint created in MongoDB with status `pending`
   - Admin receives notification

3. **Complaint Assignment** (Admin)
   - Admin assigns complaint to available worker
   - Worker is notified via Socket.io + Redis pub/sub
   - Toast notification appears in real-time

4. **Complaint Resolution** (Worker)
   - Worker views assigned complaints
   - Updates complaint status (in progress → resolved/rejected)
   - Resident receives status update notification
   - Real-time synced via WebSocket

5. **Real-Time Communication**
   - Socket.io establishes WebSocket connection on client login
   - User ID mapped to socket ID in server memory
   - Redis pub/sub broadcasts notifications to Socket.io rooms
   - Instant updates without page refresh

### Database Models

#### User Model
| Field | Type | Description |
|-------|------|-------------|
| name | String | User's full name |
| email | String | Unique email address |
| role | String | resident, worker, or admin |
| apartmentNumber | String | Apartment identifier |
| isAvailable | Boolean | Worker availability status |
| assignedComplaints | Array | List of complaint IDs |

**Complaint Model**

| Field | Type | Description |
|-------|------|-------------|
| title | String | Complaint title |
| description | String | Detailed complaint description |
| category | String | electrical, plumbing, or general |
| status | String | pending, in progress, resolved, rejected |
| assignedTo | ObjectId | Worker assigned to this complaint |
| priority | String | low, medium, or high |


## Prerequisites

- **Node.js** (v16+)
- **MongoDB** (local or MongoDB Atlas)
- **Redis** (local or cloud instance)
- **Google OAuth Credentials** (for OAuth login)
