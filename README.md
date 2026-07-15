# AI-Powered Medical Platform

Full-stack healthcare demo application with a React frontend and a FastAPI backend. The project combines patient-facing wellness tools with an AI-powered chest X-ray analysis API.

## What This Project Does

The application includes:

- Medication tracking UI with add, mark-taken, and delete flows
- Mood monitoring UI with mood logging, trigger selection, and wellness tips
- Wellness journal UI for free-form reflection and mock mood analysis
- Health dashboard with charts and summary cards
- Healthcare cost calculator with estimated insurance-adjusted costs
- Medical image detector page connected to a deployed backend prediction API
- Backend API for chest X-ray anomaly scoring using `torchxrayvision`
- Backend chat and voice endpoints designed to use Ollama and Coqui TTS

## Current Project Status

This repo is a mix of working features and demo-only UI.

Implemented and wired:

- Frontend route structure and page navigation
- Medical image upload flow from frontend to backend API
- FastAPI prediction endpoint for chest X-ray inference
- FastAPI chat endpoints for text and voice responses

Present but mostly mock/local-state only:

- Medication tracker data
- Mood monitor history and tips
- Journal save/analyze behavior
- Dashboard metrics and charts
- Contact form submission
- About page company/team content

Not currently wired into the main frontend router:

- `frontend/src/pages/Chatbot.jsx`
- `frontend/src/pages/CameraFeed.jsx`
- `frontend/src/pages/MoodMonitorNew.jsx`

## Tech Stack

### Frontend

- React 19
- React Router
- Vite
- Chart.js + `react-chartjs-2`
- `lucide-react`

### Backend

- FastAPI
- Uvicorn
- PyTorch
- TorchXRayVision
- Pillow
- NumPy
- `httpx`
- Coqui TTS

## Project Structure

```text
AI-Powered-Medical-Platform/
├─ backend/
│  ├─ main.py               # Modular FastAPI entry point
│  ├─ app.py                # Older/minimal standalone FastAPI app
│  ├─ routes/
│  │  ├─ health.py
│  │  ├─ predict.py
│  │  └─ chat.py
│  ├─ models/
│  │  └─ cxr_model.py
│  ├─ services/
│  │  ├─ ollama_service.py
│  │  └─ tts_service.py
│  └─ utils/
│     └─ image.py
├─ frontend/
│  ├─ package.json
│  ├─ src/
│  │  ├─ App.jsx
│  │  ├─ components/
│  │  └─ pages/
└─ README.md
```

## Frontend Pages

### Routed Pages

- `/` - marketing/home landing page
- `/medication-tracker` - local medication management UI
- `/mood-monitor` - local mood logging UI
- `/journal` - local wellness journal UI
- `/dashboard` - chart-based health overview
- `/medical-detector` - image upload and AI result display
- `/cost-calculator` - estimated healthcare cost calculator
- `/about` - static product/company information
- `/contact` - static contact/help form

### Key Frontend Notes

- Most pages use in-component state only; there is no persistent database layer in this repo.
- `MedicalImageDetector.jsx` calls a deployed backend URL:
  - `https://ai-powered-medical-platform.onrender.com/predict/`
- The frontend is styled as a healthcare dashboard/product demo, with substantial inline styles and mock copy.

## Backend API

The modular backend entry point is `backend/main.py`.

### Registered Routers

- `health.router` mounted at `/health`
- `predict.router` mounted at `/predict`
- `chat.router` mounted at `/api/chat`

### Effective Endpoints

- `GET /health/health`
  - returns `{ "ok": true }`
- `POST /predict/`
  - accepts multipart image upload as `file`
  - returns top findings, anomaly score, confidence, recommendations, and disclaimer
- `POST /api/chat/text`
  - body: `{ "message": "..." }`
  - returns text response from Ollama
- `POST /api/chat/voice`
  - body: `{ "message": "..." }`
  - returns text response plus generated audio file path

### Prediction Flow

1. Read uploaded image
2. Convert to grayscale
3. Normalize with TorchXRayVision preprocessing
4. Resize/crop to model input size
5. Run pretrained DenseNet chest X-ray model
6. Return top 3 predicted pathologies and an anomaly score

## Setup

### Frontend

```bash
cd frontend
npm install
npm run dev
```

Default Vite dev server:

- `http://localhost:5173`

### Backend

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```

Default backend server:

- `http://127.0.0.1:8000`

## Environment and External Dependencies

Some backend features depend on tools not installed or configured by default:

- Ollama must be running locally for chat endpoints
  - expected URL: `http://localhost:11434/api/generate`
  - expected model in code: `llama3`
- Coqui TTS is required for voice generation
- PyTorch and TorchXRayVision model downloads may be large on first run

## Known Issues and Limitations

- `backend/app.py` and `backend/main.py` overlap conceptually. `main.py` is the cleaner entry point.
- Health route path is duplicated as `/health/health` because both prefix and route path include `health`.
- The frontend image detector points to a deployed backend instead of a local environment variable-based API URL.
- Several frontend pages contain placeholder/demo data rather than real persistence.
- There is no database, authentication, or user account system in this repo.
- The repository contains generated/cache files such as `__pycache__` and uploaded images under `backend/uploads/`.
- Some text in the codebase contains encoding artifacts from saved Unicode characters.

## Build and Verification Notes

I was able to inspect the full codebase, but I could not complete a frontend production build in the current workspace because local frontend dependencies are not installed. The build currently fails with:

- `'vite' is not recognized as an internal or external command`

That indicates `npm install` has not been run successfully in `frontend/` in this environment.

## Suggested Next Improvements

- Add a `.env`-based API base URL for frontend/backend switching
- Persist medication, mood, and journal data in a database
- Add authentication and user profiles
- Normalize backend routing paths
- Remove unused pages or connect them to navigation
- Add tests for API routes and image preprocessing
- Clean out generated files from version control

## Summary

This project is best described as an AI-healthcare demo platform with one real ML-backed backend capability, chest X-ray analysis, surrounded by several polished but mostly mock frontend wellness tools.
