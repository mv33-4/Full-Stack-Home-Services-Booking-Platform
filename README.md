# FixIt Project

A full-stack complaint management and maintenance request system for apartment complexes. Residents submit maintenance complaints (electrical, plumbing, general), admins assign them to available workers, and workers update complaint statuses in real-time.

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

```text
backend/
├── config/
│   ├── db.js                # MongoDB connection
│   ├── redisClient.js       # Redis pub/sub setup
│   └── passport-setup.js    # Google OAuth configuration
├── models/
│   ├── User.js              # User schema (resident, worker, admin)
│   └── Complaint.js         # Complaint schema with status tracking
├── controllers/
│   ├── userController.js    # User management, auth, profile
│   └── complaintController.js # Complaint CRUD, assignment logic
├── routes/
│   ├── userRoutes.js        # /api/users endpoints
│   └── complaintRoutes.js   # /api/complaints endpoints
├── middleware/
│   └── errorMiddleware.js   # Global error handler
├── utils/
│   └── [helper functions]
├── package.json
└── server.js                # Express app, Socket.IO, Redis setup
```

### Frontend Structure

```text
frontend/
├── public/                  # Static assets
├── src/
│   ├── pages/               # Route-level components organized by role
│   │   ├── General/
│   │   │   ├── Home.jsx             # Landing page
│   │   │   ├── About.jsx            # About page
│   │   │   ├── Contact.jsx          # Contact page
│   │   │   ├── Login.jsx            # User login
│   │   │   ├── Register.jsx         # User registration
│   │   │   └── NotFound.jsx         # 404 error page
│   │   ├── Dashboard.jsx            # Unified dashboard for all roles
│   │   ├── Resident/                # Resident role pages
│   │   │   ├── SubmitComplaint.jsx  # Submit new complaint
│   │   │   ├── ComplaintHistory.jsx # View past complaints
│   │   │   ├── Notifications.jsx    # Real-time notifications
│   │   │   └── Profile.jsx          # Resident profile
│   │   ├── Worker/                  # Worker role pages
│   │   │   ├── AssignedComplaints.jsx # View assigned work
│   │   │   ├── UpdateComplaint.jsx  # Update complaint status
│   │   │   └── Profile.jsx          # Worker profile
│   │   └── Admin/                   # Admin role pages
│   │       ├── ManageUsers.jsx      # User management
│   │       ├── AssignComplaints.jsx # Assign work to workers
│   │       ├── ViewAllComplaints.jsx # System-wide complaint view
│   │       ├── ManageCategories.jsx # Complaint categories
│   │       └── ViewLogs.jsx         # System activity logs
│   ├── components/          # Reusable UI components
│   │   ├── Auth/            # Authentication components
│   │   ├── Forms/           # Reusable form components
│   │   ├── Cards/           # Complaint/User card components
│   │   ├── Navbar/          # Navigation header
│   │   ├── Footer/          # Footer component
│   │   ├── Dashboard/       # Dashboard-specific components
│   │   ├── Modals/          # Modal dialogs
│   │   └── Shared/          # Shared utilities (ProtectedRoute, etc.)
│   ├── contexts/            # React Context for state management
│   ├── utils/               # Helper functions, API calls
│   ├── assets/              # Images and static files
│   ├── App.jsx              # Main routing configuration
│   ├── main.jsx             # React entry point
│   └── index.css            # Global styles
├── package.json
└── vite.config.js
```

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
