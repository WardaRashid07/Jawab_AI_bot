# 🤖 Jawab — AI Customer Support Bot for Pakistani SMEs

![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.0+-000000?style=flat&logo=flask&logoColor=white)
![Gemini](https://img.shields.io/badge/Google_Gemini-2.5_Flash-4285F4?style=flat&logo=google&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-Database-34A853?style=flat&logo=googlesheets&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-Bot_API-26A5E4?style=flat&logo=telegram&logoColor=white)
![License](https://img.shields.io/badge/License-Open_Source-green)

> **Jawab** — Instant, intelligent, always-on customer support across Telegram and web for Pakistani small businesses.

---

## 📌 The Problem

In Pakistan, millions of SME owners manage customer support manually. A customer who doesn't get a reply in 10 minutes often doesn't come back. Most small businesses have no system to guarantee fast responses — and enterprise chatbot platforms cost 100,000+ PKR/month.

**Jawab solves this for free.**

---

## ✅ What Jawab Does

- Receives messages from customers on **Telegram** or the **web chat interface**
- **Classifies intent** instantly — complaint, query, or escalation
- **Generates intelligent replies** using Google Gemini AI in English, Urdu, or Roman Urdu
- **Logs every ticket** automatically to Google Sheets
- **Escalates urgent issues** and flags them for human agents
- Provides a **live dashboard** to monitor all support activity in real time

---

## 🏗️ Architecture

```
    Customer (Telegram / Web)
               ↓
     Flask Backend (app.py)
               ↓
  ┌────────────┼──────────────┐
  ↓            ↓              ↓
Classifier  Gemini AI  Google Sheets
(Intent)   (Reply)     (Ticket Log)
  └────────────┼──────────────┘
               ↓
   Reply sent back to customer
               ↓
   Dashboard updated in real time
```

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Backend** | Python 3.12 + Flask | Web server, routing, API |
| **AI Engine** | Google Gemini 2.5 Flash | Natural language replies (free tier) |
| **Messaging** | Telegram Bot API | Customer messaging channel |
| **Database** | Google Sheets (gspread) | Ticket logging, conversation history |
| **Classifier** | Custom Python | Intent detection (complaint/query/escalate) |
| **Frontend** | HTML5 + CSS3 + Vanilla JS | Web chat UI + Admin dashboard |
| **Fonts** | Orbitron, DM Sans, JetBrains Mono | UI typography |
| **Production** | Gunicorn | WSGI server for deployment |

---

## 📁 Project Structure

```
Jawab/
├── backend/
│   ├── app.py                  # Main Flask server — 6 routes
│   ├── ai_engine.py            # Gemini AI integration (google.genai)
│   ├── classifier.py           # Message classification logic
│   ├── sheets_handler.py       # Google Sheets read/write
│   ├── telegram_handler.py     # Telegram Bot API integration
│   ├── config.py               # Environment variable loader
│   ├── requirements.txt        # Python dependencies
│   ├── Procfile                # Gunicorn start command (for Render)
│   ├── .env                    # ⚠️ Secret keys — never uploaded
│   └── credentials.json        # ⚠️ Google service account — never uploaded
│
├── frontend/
│   ├── pages/
│   │   ├── index.html          # Landing page — hero, features, stats
│   │   ├── chat.html           # Web chat demo — main judge demo page
│   │   ├── dashboard.html      # Admin ticket dashboard
│   │   └── about.html          # Project story, problem, team
│   └── static/
│       ├── style.css           # Shared styles
│       └── script.js           # Shared JS logic
│
├── render.yaml                 # Render deployment config
├── run.sh                      # Local start script
├── .gitignore                  # Excludes .env, credentials.json, __pycache__
└── README.md
```

---

## 🚀 How to Run Locally

### Prerequisites
- Python 3.9+
- A Google account
- A Telegram bot token (from @BotFather)

### Step 1 — Clone the repo

```bash
git clone https://github.com/p-u-p-x/Jawab.git
cd Jawab
```

### Step 2 — Install dependencies

```bash
cd backend
pip install -r requirements.txt
```

### Step 3 — Create your `.env` file

Create `backend/.env` with the following:

```env
GEMINI_API_KEY=your_gemini_api_key_here
TELEGRAM_TOKEN=your_telegram_bot_token_here
SHEET_ID=your_google_sheet_id_here
PORT=8080
FLASK_ENV=development
```

**Where to get these:**
- `GEMINI_API_KEY` → [aistudio.google.com](https://aistudio.google.com) → Get API Key
- `TELEGRAM_TOKEN` → Telegram → @BotFather → /newbot
- `SHEET_ID` → From your Google Sheet URL: `docs.google.com/spreadsheets/d/[THIS_PART]/edit`

### Step 4 — Set up Google Sheets

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Enable **Google Sheets API** and **Google Drive API**
3. Create a **Service Account** → download `credentials.json` → save to `backend/`
4. Create a Google Sheet named `Jawab Support Tickets`
5. Add headers in Row 1: `Ticket ID | Phone | Message | Category | Status | Timestamp | Escalated`
6. Share the sheet with the service account email from `credentials.json`

### Step 5 — Start the server

```bash
cd Jawab
bash run.sh
```

Or directly:

```bash
cd backend
python app.py
```

Server runs at: **http://localhost:8080**

### Step 6 — Connect Telegram webhook (optional, for Telegram channel)

While running locally with ngrok:

```bash
ngrok http 8080
# Copy the HTTPS URL, then:
curl "https://api.telegram.org/bot<YOUR_TOKEN>/setWebhook?url=https://your-ngrok-url/webhook"
```

---

## 🌐 Pages & Routes

| Route | Page | Description |
|-------|------|-------------|
| `/` | `index.html` | Landing page with hero, features, flow |
| `/chat-page` | `chat.html` | **Live web chat demo — main demo for judges** |
| `/dashboard` | `dashboard.html` | Admin ticket view with live updates |
| `/about` | `about.html` | Project story, problem, solution, team |
| `/api/health` | — | JSON health check |
| `/chat` | — | POST endpoint for web chat messages |
| `/tickets` | — | GET all tickets (used by dashboard) |
| `/webhook` | — | Telegram webhook receiver |

---

## 🔌 API Reference

### POST `/chat` — Web chat
```json
Request:  { "message": "mera order nahi aya", "session_id": "web-user" }
Response: { "reply": "Mujhe afsos hai...", "category": "complaint", "escalated": false }
```

### GET `/tickets` — All tickets
```json
Response: { "tickets": [...], "count": 42 }
```

### GET `/api/health` — Health check
```json
Response: { "status": "running", "message": "AI Support Bot is online", "version": "1.0" }
```

---

## ⚙️ Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `GEMINI_API_KEY` | ✅ | Google Gemini API key |
| `TELEGRAM_TOKEN` | ✅ | Telegram bot token from @BotFather |
| `SHEET_ID` | ✅ | Google Sheet ID from URL |
| `PORT` | ✅ | Server port (use 8080 locally) |
| `FLASK_ENV` | ✅ | `development` or `production` |

---

## ☁️ Deploy to Render (Free)

1. Push repo to GitHub
2. Go to [render.com](https://render.com) → New Web Service → Connect GitHub repo
3. Set these in Render dashboard:

| Field | Value |
|-------|-------|
| **Build Command** | `cd backend && pip install -r requirements.txt` |
| **Start Command** | `cd backend && gunicorn app:app --bind 0.0.0.0:$PORT --workers 2` |
| **Instance Type** | Free ($0/month) |

4. Add environment variables in Render dashboard (same as `.env`)
5. Add `credentials.json` content under **Secret Files**
6. After deploy, set Telegram webhook:

```bash
curl "https://api.telegram.org/bot<TOKEN>/setWebhook?url=https://your-app.onrender.com/webhook"
```

7. Set up [UptimeRobot](https://uptimerobot.com) (free) to ping `/api/health` every 5 minutes — keeps Render free tier awake

---

## 💬 Test Scenarios

| Input | Expected Category | Expected Response Language |
|-------|------------------|--------------------------|
| `"mera order nahi aya"` | complaint | Roman Urdu |
| `"What is your return policy?"` | query | English |
| `"I want to speak to a human"` | escalate | English |
| `"payment kaise karun?"` | query | Roman Urdu |
| `"میرا آرڈر کہاں ہے"` | complaint | Urdu script |

---

## ✨ Key Features

- **Bilingual AI** — Detects Urdu, Roman Urdu, and English. Always replies in same language as customer
- **3-way classification** — Every message tagged as `complaint`, `query`, or `escalate`
- **Conversation memory** — AI remembers last 5 messages per customer for context
- **Auto ticket logging** — Every conversation logged to Google Sheets automatically
- **Live dashboard** — Real-time ticket view, category filters, auto-refresh every 10 seconds
- **Web chat fallback** — Full demo works via browser even without Telegram
- **100% free stack** — No subscription costs, no paid APIs required

---

## ⚠️ Known Limitations

| Limitation | Status |
|-----------|--------|
| Gemini free tier rate limits (15 req/min) | Sufficient for demo |
| Render free tier spins down after inactivity | Fixed with UptimeRobot |
| No dashboard authentication | Phase 2 roadmap |
| Keyword-based classifier (not ML) | Phase 2 upgrade planned |

---

## 🚀 Roadmap

- [ ] Voice message support (WhatsApp voice note transcription)
- [ ] Urdu-native NLP classifier
- [ ] CRM integration (HubSpot, Pipedrive)
- [ ] Multi-business SaaS at jawab.pk
- [ ] Embeddable widget for any website
- [ ] Support for Sindhi, Punjabi, Pashto

---

## 👥 Team


| Name               | Role                     |
|--------------------|--------------------------|
| **Sajeela Noor**   | AI Engine + Backend      |
| **Warda Rashid**   | Google Sheets + Telegram |
| **Haroon Rasheed** | Frontend + UI            |
| **Kashaf Noor**    | Troubleshooting          |


---

## 📄 License

Open source. Built during a Gen AI Hackathon. Free to use, modify, and share.

---

**Built with ❤️ for Pakistani SMEs.**  
**Jawab — Your Support, Always On.**

---

## 📬 Contact

- ✉️ **Email:** [i.sajeela.noor@gmail.com](mailto:i.sajeela.noor@gmail.com)  **-[wardahrashid07@gmail.com](mailto:wardahrashid07@gmail.com)**

- 💼 **LinkedIn:** [Sajeela Noor](https://www.linkedin.com/in/sajeela-noor-82b510256) **-[Warda_Rashid_Butt](www.linkedin.com/in/warda-rashid-2415632a7)

