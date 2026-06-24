# CivicTrack

A civic issue–reporting and resolution platform. Citizens file geotagged complaints with photos; local authorities review, respond, and resolve them — with live status updates. Built as a React + Vite single-page app on a Supabase backend.

## Features

**For citizens**
- Report a complaint with a description, photo, and location pinned on a map
- Automatic reverse-geocoding of the pinned coordinates to a human-readable address (OpenStreetMap / Nominatim)
- Personal dashboard tracking the status of every complaint you've filed
- **Discover** feed to browse complaints reported across the community
- Live status updates pushed in real time as authorities act on a complaint

**For authorities / admins**
- Dashboard of all incoming complaints, newest first
- Review a complaint and update its status through the workflow
- Resolve a complaint with a written response and a proof-of-resolution photo
- Role-gated access — authority/admin routes are separate from citizen routes

## Architecture

```
┌────────────────────────────┐         ┌──────────────────────────────┐
│  React + Vite SPA          │         │  Supabase                     │
│  ├─ Auth + Role context    │ ──────► │  ├─ Auth (citizen / authority)│
│  ├─ Citizen pages          │         │  ├─ Postgres (complaints)     │
│  ├─ Authority pages         │ ◄────── │  ├─ Realtime (postgres_changes)│
│  ├─ Leaflet map + geocode  │         │  └─ Storage (complaint-media) │
│  └─ Protected routes        │         └──────────────────────────────┘
└────────────────────────────┘                    ▲
            │                                       │
            └──── Nominatim / OpenStreetMap ────────┘
                  (reverse geocoding)
```

Role-based routing sends citizens and authorities to separate protected route trees. Complaint data lives in a Supabase `complaints` table; a realtime channel subscribed to `postgres_changes` refreshes a user's view the moment their complaint changes. Images are stored in a Supabase Storage bucket (`complaint-media`) and served via public URLs.

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite, React Router |
| Backend / DB | Supabase (Auth, Postgres, Realtime, Storage) |
| Maps | Leaflet + react-leaflet |
| Geocoding | OpenStreetMap Nominatim (reverse geocode) |
| HTTP | axios |

## Project structure

```
client/
├── src/
│   ├── api/supabaseClient.js      # Supabase client init
│   ├── app/router.jsx             # role-based routes
│   ├── context/                   # AuthContext, RoleContext
│   ├── components/                # Navbar, Sidebar, ComplaintCard, MapPreview, ProtectedRoute
│   ├── hooks/useRealtimeComplaints.js   # live complaint subscription
│   ├── layouts/MainLayout.jsx
│   ├── pages/
│   │   ├── auth/Login.jsx
│   │   ├── citizen/               # Dashboard, CreateComplaint, Discover
│   │   └── authority/             # Dashboard, ComplaintReview
│   └── services/                  # complaint, authority, admin, geocode, storage
└── vite.config.js
```

## Getting started

```bash
cd client
npm install

# create client/.env with your Supabase project credentials:
# VITE_SUPABASE_URL=https://<project>.supabase.co
# VITE_SUPABASE_ANON_KEY=<your-anon-key>

npm run dev      # start the dev server (Vite)
```

The backend expects a Supabase project with a `complaints` table (fields include `user_id`, `status`, `created_at`, `authority_response_text`, `authority_image_url`) and a public `complaint-media` storage bucket.

## Authors

Built collaboratively by **[Muhammad Taha](https://github.com/MuhammadTaha305)** and **[Hassaan Awan](https://github.com/hassaanawan48)**. Original repository: [hassaanawan48/CivicTrack](https://github.com/hassaanawan48/CivicTrack).
