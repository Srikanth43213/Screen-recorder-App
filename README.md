# MERN Screen Recorder App

A small web app that records the active browser tab with microphone audio, lets you preview + download, and uploads to a simple Node/Express + SQL (SQLite) backend. Cloudinary support is included for easy deployment on Render (file storage), while the DB remains SQL.

## Demo / Deployment (How-to)
- **Frontend**: Deploy to Vercel or Netlify (Vite build). Set `VITE_API_URL` env to your backend URL.
- **Backend**: Deploy to Render (Node). If using Render, do **NOT** rely on local disk. Set `CLOUDINARY_URL` so uploaded files go to Cloudinary and the DB stores their public URL.
  - Example `CLOUDINARY_URL`: `cloudinary://<api_key>:<api_secret>@<cloud_name>`
  - You can alternatively swap Cloudinary with S3 fairly easily inside `server.js`.

## Tech
- **Frontend**: React (Vite), uses `getDisplayMedia`, `getUserMedia`, `MediaRecorder`. 3-minute hard cap, live timer, Start/Stop, preview, download, upload.
- **Backend**: Express, Multer for uploads, SQLite for SQL DB (via `sqlite3`), optional Cloudinary for storage. CORS enabled.

## API
- `POST /api/recordings` → multipart/form-data, fields:
  - `title`: string (optional)
  - `recording`: file (required, e.g., `.webm`)
  - Returns: inserted row with `{ id, title, filename, size, url, createdAt }`
- `GET /api/recordings` → list of all recordings
- `GET /api/recordings/:id` → redirects to the file URL

## Local Setup

### 1) Backend
```bash
cd backend
cp .env.example .env
npm install
npm run init:db
npm run dev
# Backend at http://localhost:8080
```

### 2) Frontend
```bash
cd frontend
cp .env.example .env
npm install
npm run dev
# Frontend at http://localhost:5173
```

### 3) Test
- Open the frontend in **Chrome**.
- Click **Start** → grant permissions to share a **tab** and the **microphone**.
- Click **Stop** → preview appears. You can **Download** or **Upload**.
- Visit **Recordings** page to list and play uploads.

## Deploy

### Backend on Render
1. Create a new **Web Service** from the `backend/` folder (Node).
2. Set **Build Command**: `npm install && npm run init:db`
   - For Render persistent SQL, consider swapping SQLite with managed Postgres + Prisma/Knex. For quick demo, SQLite works locally; on Render, keep DB file in the container, or switch to a managed DB.
3. Set **Start Command**: `npm start`
4. **Environment Variables**:
   - `PORT` (Render will set)
   - `ORIGIN` → your frontend URL (e.g., `https://your-frontend.vercel.app`)
   - `CLOUDINARY_URL` → to store files in Cloudinary (recommended)
   - `DB_FILE` → a path like `./data/recordings.db` (default)
5. After deploy, note the backend URL, e.g., `https://your-backend.onrender.com`.

### Frontend on Vercel/Netlify
- Set env var `VITE_API_URL` to the Render backend URL.
- Build command: `npm run build`. Output directory: `dist`.

## Known Limitations
- Safari/Firefox may not fully support `getDisplayMedia` for tab audio; Chrome required for best results.
- On Render, local file storage is ephemeral. Use Cloudinary/S3.
- SQLite is excellent locally; for production, switch to Postgres/MySQL with Prisma/Knex.

## File Structure
```
mern-screen-recorder/
  backend/
    server.js
    package.json
    .env.example
    scripts/init-db.js
  frontend/
    index.html
    package.json
    vite.config.js
    .env.example
    src/
      main.jsx
      styles.css
      pages/
        App.jsx
        List.jsx
```
