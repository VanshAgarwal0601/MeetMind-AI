# MeetMind AI

A full-stack AI meeting assistant that records or accepts uploaded audio, transcribes and diarizes it, and uses Google Gemini to generate structured summaries, action items, decisions, and follow-up questions.

## Features

- **Authentication** — Email/password sign up and sign in with bcrypt password hashing and JWT-based sessions.
- **Audio capture** — In-browser recording via the MediaRecorder API (pause/resume/stop) or drag-and-drop upload of audio files up to 25MB.
- **AI processing** — Google Gemini (`gemini-2.5-flash`) with structured (`responseSchema`) output for transcripts, summaries, action items, decisions, and follow-ups. Falls back to a local mock AI when no API key is configured, so the app works offline.
- **Persistence** — MongoDB (via Mongoose) for meeting storage, with automatic fallback to a local `database.json` file when MongoDB isn't available.
- **Dashboard** — Meeting history with keyword search/filtering, interactive action-item checklists, a speech-synthesis "read aloud" feature, and running metrics (meeting count, hours saved, total actions).

## Tech Stack

| Layer    | Technology |
|----------|------------|
| Backend  | Node.js, Express |
| Auth     | bcryptjs, jsonwebtoken |
| AI       | `@google/genai` (Gemini) |
| Database | MongoDB / Mongoose, with local JSON fallback |
| Uploads  | Multer (in-memory, 25MB limit) |
| Frontend | Vanilla HTML/CSS/JS |

## Project Structure

```
.
├── backend/
│   ├── server.js       # Express app, routes, auth middleware, AI processing
│   ├── database.js      # MongoDB connection + local JSON fallback logic
│   └── models.js        # Mongoose schemas
├── frontend/
│   ├── index.html        # Landing page
│   ├── login.html        # Sign in / sign up
│   ├── dashboard.html     # Main app dashboard
│   ├── app.js             # Client-side logic (recording, upload, rendering)
│   └── style.css
├── database.json          # Local fallback data store
├── package.json
└── .env                   # Environment configuration (not committed)
```

## Getting Started

### Prerequisites
- Node.js
- (Optional) A local or hosted MongoDB instance
- (Optional) A Google Gemini API key

### Installation

```bash
npm install
```

### Configuration

Create a `.env` file in the project root:

```
PORT=5000
MONGO_URI=mongodb://localhost:27017/meetmind
JWT_SECRET=your_jwt_secret_here
GEMINI_API_KEY=your_gemini_api_key_here
```

> If `GEMINI_API_KEY` is left blank, the app uses a built-in mock AI so you can still test the full flow without a live key. If `MONGO_URI` is unreachable, meeting data is stored in `database.json` instead.

### Run

```bash
npm start
```

The app will be available at `http://localhost:5000` (or the `PORT` you configured).

## API Endpoints

| Method | Endpoint                | Description                                  | Auth required |
|--------|--------------------------|-----------------------------------------------|:---:|
| GET    | `/api/health`             | Health check                                   | No |
| POST   | `/api/auth/signup`        | Create a new account                          | No |
| POST   | `/api/auth/login`         | Log in and receive a JWT                      | No |
| GET    | `/api/meetings`           | List the current user's meetings              | Yes |
| POST   | `/api/process-audio`      | Upload/process an audio file into a meeting   | Yes |

Page routes `/`, `/login`, and `/dashboard` serve the corresponding frontend views.

## Notes

- This is a personal/development project — before deploying it anywhere public, rotate the `JWT_SECRET` and any API keys, and remove the checked-in `.env` and `node_modules` from version control (add a `.gitignore`).
