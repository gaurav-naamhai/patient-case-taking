# Deployment Guide: Patient Case-Taking System

This project consists of two core components:
1. **Frontend**: Next.js 16 (React 19) web app (`frontend/`)
2. **Backend**: FastAPI Python API (`backend/`)

---

## Architecture Overview

- **Frontend Hosting**: Vercel (Recommended), Netlify, or AWS Amplify
- **Backend Hosting**: Render (Recommended), Railway, Fly.io, or AWS / GCP Cloud Run
- **Database**: MongoDB Atlas Cluster
- **AI Service**: Google Gemini API

---

## Step 1: Deploy Backend (Render / Railway)

### Option A: Deploy on Render (Recommended)
1. Push your latest code to GitHub.
2. Sign in to [Render Dashboard](https://dashboard.render.com/).
3. Click **New +** → **Web Service**.
4. Connect the repository `rumaosera-lab/patient-case-taking`.
5. Configure the service settings:
   - **Name**: `patient-case-taking-backend`
   - **Root Directory**: Leave empty or `./` (or `patient-case-taking` if monorepo)
   - **Runtime**: `Python 3`
   - **Build Command**: `pip install -r backend/requirements.txt`
   - **Start Command**: `uvicorn backend.main:app --host 0.0.0.0 --port $PORT`
6. Under **Environment Variables**, add:
   - `MONGODB_URI`: `<Your MongoDB Connection String>`
   - `MONGODB_DB_NAME`: `patient_case_taking`
   - `GEMINI_API_KEY`: `<Your Gemini API Key>`
   - `ALLOWED_ORIGINS`: `https://your-frontend.vercel.app,http://localhost:3000`
7. Click **Create Web Service**.
8. Once deployed, copy your Backend URL (e.g., `https://patient-case-taking-backend.onrender.com`).

---

## Step 2: Deploy Frontend (Vercel)

### Option A: Deploy on Vercel (Recommended)
1. Sign in to [Vercel](https://vercel.com).
2. Click **Add New...** → **Project**.
3. Import the repository `rumaosera-lab/patient-case-taking`.
4. Configure the project settings:
   - **Root Directory**: `frontend`
   - **Framework Preset**: `Next.js`
5. In **Environment Variables**, add:
   - `NEXT_PUBLIC_API_BASE_URL`: `https://patient-case-taking-backend.onrender.com/api/v1`
   *(Replace with your actual backend URL from Step 1, appending `/api/v1`)*
6. Click **Deploy**.
7. Once deployment finishes, copy your live frontend URL (e.g. `https://patient-case-taking.vercel.app`).
8. Return to your Backend settings on Render/Railway and add your frontend URL to `ALLOWED_ORIGINS`.

---

## Step 3: Docker Deployment (Alternative)

If deploying to a VPS, AWS EC2, GCP Cloud Run, or DigitalOcean:

### Build and Run Backend Container:
```bash
docker build -t patient-case-backend .
docker run -d -p 8000:8000 \
  -e MONGODB_URI="your_mongodb_uri" \
  -e MONGODB_DB_NAME="patient_case_taking" \
  -e GEMINI_API_KEY="your_gemini_api_key" \
  -e ALLOWED_ORIGINS="*" \
  patient-case-backend
```

---

## Summary of Environment Variables

| Variable | Target | Example / Purpose |
|---|---|---|
| `NEXT_PUBLIC_API_BASE_URL` | Frontend | `https://backend-domain.com/api/v1` |
| `MONGODB_URI` | Backend | `mongodb+srv://user:pass@cluster.mongodb.net/?retryWrites=true` |
| `MONGODB_DB_NAME` | Backend | `patient_case_taking` |
| `GEMINI_API_KEY` | Backend | Google Gemini API Key |
| `ALLOWED_ORIGINS` | Backend | Comma-separated frontend domains (e.g. `https://app.vercel.app`) |
