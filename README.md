# 🧠 AI-Powered Grievance Intelligence System

> A production-ready, intelligent email management platform that automates email processing, priority classification, and grievance categorization using AI-driven workflows — built for government and institutional use.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Highlights](#-key-highlights)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Features](#-features)
- [Project Structure](#-project-structure)
- [Quick Start](#-quick-start)
- [Configuration](#-configuration)
- [API Reference](#-api-reference)
- [Deployment](#-deployment)
- [Contributing](#-contributing)

---

## 🌟 Overview

The **AI Grievance Intelligence System** is a full-stack, intelligent email triage platform designed to handle high-volume institutional grievance email workflows. It automatically classifies incoming emails by category and priority, retrieves relevant policy context via RAG (Retrieval-Augmented Generation), and provides a clean dashboard for reviewing and responding to grievances — all without manual sorting.

Built originally for the **Government of West Bengal's Higher Education Department**, the system is fully generalizable to any institution dealing with large volumes of structured or unstructured email grievances.

---

## 🏆 Key Highlights

### 1. 🤖 Intelligent Email Classification & Priority Triage
Designed and developed a **production-ready intelligent email management system** that automates email processing, priority classification, and grievance categorization using AI-driven workflows. The system uses a multi-tier triage engine:
- **Tier 1 — Heuristic Triage**: Fast keyword-based classification across 16 grievance categories with 4-level priority scoring (Critical → High → Medium → Low)
- **Tier 2 — LLM-Augmented Triage**: Gemini / DeepSeek R1 LLM for context-aware classification on ambiguous cases
- **Tier 3 — RAG Policy Retrieval**: FAISS-indexed policy documents provide grounded, citation-backed responses

### 2. 📧 Native Gmail Integration via Portable Architecture
Conceptualized a **portable, loosely coupled architecture** integrating automated email classification directly into Gmail through labels — eliminating the need for a separate web application:
- Implements **Gmail API Watch method** with **Google Cloud Pub/Sub** to capture real-time incoming email notifications
- Triggers **AWS Lambda functions** for automated, event-driven email processing
- Provides a **customizable sidebar dashboard** for policy management, classification rules, and label configuration
- Zero friction for end users — classifications appear directly as Gmail labels

### 3. 🔐 Secure, Privacy-First Local LLM Architecture
Architected a **secure, privacy-focused backend** using **Cloudflare Tunnel** and **FastAPI** to expose a locally hosted **DeepSeek R1 LLM** running on a high-performance GPU workstation:
- **Encrypted communication** for all data in transit via Cloudflare Tunnel (no open ports)
- Confidential email data remains within controlled, on-premise infrastructure
- Enables scalable and secure AI-powered email processing without sending sensitive data to third-party cloud LLM APIs

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        FRONTEND (React + Vite)                  │
│   Dashboard · Email Inbox · Priority View · Policy Management   │
│                    http://localhost:3000                         │
└──────────────────────────┬──────────────────────────────────────┘
                           │ /api proxy
┌──────────────────────────▼──────────────────────────────────────┐
│                    BACKEND (FastAPI + Uvicorn)                   │
│                    http://localhost:8000                         │
│                                                                  │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │  Triage     │  │  RAG Engine  │  │   Gmail / Microsoft  │   │
│  │  Service    │  │  (FAISS)     │  │   OAuth Integration  │   │
│  │             │  │              │  │                      │   │
│  │ Heuristic → │  │ Embed Query  │  │  Gmail Watch API     │   │
│  │ LLM Triage  │  │ Retrieve Top │  │  Pub/Sub Webhooks    │   │
│  │ Priority 0-3│  │ Policy Chunks│  │  Microsoft Graph     │   │
│  └─────────────┘  └──────────────┘  └──────────────────────┘   │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    SQLite / PostgreSQL                   │    │
│  │   emails · categories · priority_rules · chat_history   │    │
│  └─────────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
   ┌──────────┐    ┌──────────────┐   ┌────────────────┐
   │ Google   │    │  DeepSeek R1 │   │  AWS Lambda    │
   │ Gemini   │    │  (Local GPU) │   │  (Event-driven │
   │ Cloud API│    │  via CF Tunnel│  │   processing)  │
   └──────────┘    └──────────────┘   └────────────────┘
```

---

## 🛠️ Tech Stack

### Backend
| Layer | Technology |
|-------|-----------|
| API Framework | FastAPI + Uvicorn |
| AI / LLM | Google Gemini, DeepSeek R1 (local via Ollama), LangChain, LangGraph |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2` |
| Vector Search | FAISS (CPU + AVX2) |
| Email Integration | Gmail API (OAuth2), Microsoft Graph API |
| Event Processing | Google Cloud Pub/Sub, AWS Lambda |
| Secure Tunneling | Cloudflare Tunnel |
| Auth | PyJWT, Google OAuth2, Microsoft OAuth2 |
| Database | SQLite (dev), PostgreSQL (prod) |
| PDF Parsing | pdfplumber, pytesseract (OCR) |
| Task Queue | Celery + Redis |

### Frontend
| Layer | Technology |
|-------|-----------|
| Framework | React 18 + TypeScript |
| Build Tool | Vite 8 (Rolldown) |
| Styling | Tailwind CSS + shadcn/ui (Radix UI) |
| State / Data | TanStack Query (React Query) |
| Routing | React Router v6 |
| Charts | Recharts |
| Form Handling | React Hook Form + Zod |

### Infrastructure
| Component | Technology |
|-----------|-----------|
| Containerisation | Docker + Docker Compose |
| Orchestration | Kubernetes (K8s manifests in `/k8s`) |
| Reverse Proxy | Nginx |
| Database | PostgreSQL 15 |

---

## ✨ Features

- **📥 Unified Inbox** — Aggregates Gmail and Microsoft/Outlook emails into a single prioritized view
- **🏷️ Auto-Classification** — 16 built-in grievance categories (RTI, Legal, Scholarship, Suspension, etc.)
- **🚦 4-Level Priority Engine** — Critical / High / Medium / Low with fully customizable keyword rules
- **📚 RAG Policy Retrieval** — Upload policy PDFs; the system retrieves relevant clauses when drafting replies
- **💬 AI Chat Assistant** — Context-aware chatbot grounded in uploaded policy documents
- **📊 Analytics Dashboard** — Real-time stats on volume, category distribution, and priority breakdown
- **🔒 Multi-tenant** — Each user's emails, categories, rules, and FAISS index are fully isolated
- **🔄 Real-time Sync** — Gmail Push (Watch API) + polling fallback for Microsoft
- **📱 Responsive UI** — Full dark/light mode, works on all screen sizes
- **🐳 Docker Ready** — One-command local or production deployment

---

## 📁 Project Structure

```
GrievancelQ/
├── backend/
│   ├── main.py                  # Entry point (Uvicorn)
│   ├── requirements.txt         # Python dependencies
│   ├── Dockerfile
│   ├── .env.example             # Config template
│   ├── scripts/
│   │   ├── import_gov_emails.py # Bulk email import
│   │   └── triage_demo_emails.py# Demo/evaluation runner
│   └── src/
│       ├── app.py               # FastAPI app + lifecycle
│       ├── config.py            # Settings (env-driven)
│       ├── routes/              # API route handlers
│       │   ├── auth_routes.py
│       │   ├── email_routes.py
│       │   ├── gmail_routes.py
│       │   ├── intelligence_routes.py
│       │   ├── policy_routes.py
│       │   ├── settings_routes.py
│       │   └── security_routes.py
│       ├── services/
│       │   ├── triage_service.py     # Classification engine
│       │   ├── rag_service.py        # FAISS RAG retrieval
│       │   ├── policy_service.py     # PDF ingestion + chunking
│       │   ├── ai_service.py         # LLM orchestration
│       │   └── gmail_sync_service.py # Gmail sync + Watch
│       └── db/
│           ├── repository.py     # DB CRUD operations
│           └── session.py        # DB connection
├── frontend/
│   ├── src/
│   │   ├── App.tsx
│   │   ├── components/          # Reusable UI components
│   │   └── pages/               # Route pages
│   ├── vite.config.ts
│   └── Dockerfile
├── k8s/                         # Kubernetes manifests
├── nginx/nginx.conf             # Reverse proxy config
├── postgres/                    # PostgreSQL config
├── docker-compose.yml
├── INSTALLATION_MANUAL.md       # Detailed setup guide
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites
- Python 3.11+
- Node.js 18+ (tested on v24)
- Git

### 1. Clone the Repository

```bash
git clone https://github.com/abir1010200/GrievancelQ.git
cd GrievancelQ
```

### 2. Backend Setup

```bash
# Install Python dependencies
pip install -r backend/requirements.txt

# Create your environment config (never committed)
cp backend/.env.example backend/.env
# Edit backend/.env with your API keys

# Initialize the database
python -c "import sys; sys.path.insert(0, 'backend/src'); from db.repository import init_db; init_db()"

# Start the backend server
python backend/main.py
# → Running at http://localhost:8000
```

### 3. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install --legacy-peer-deps

# Copy environment config
cp .env.example .env

# Start the dev server (Node 24 workaround)
node --experimental-require-module node_modules/vite/bin/vite.js
# → Running at http://localhost:3000
```

### 4. Open the App

| URL | Purpose |
|-----|---------|
| http://localhost:3000 | Main dashboard |
| http://localhost:8000/docs | Interactive API docs (Swagger) |
| http://localhost:8000/health | Backend health check |

---

## ⚙️ Configuration

Copy `backend/.env.example` to `backend/.env` and fill in:

```env
# --- AI / LLM ---
GOOGLE_API_KEY=your_gemini_api_key          # For cloud LLM (optional)
AI_PREFER_LOCAL=false                        # Set true to use local Ollama

# --- Gmail OAuth ---
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:8000/auth/google/callback

# --- Microsoft OAuth (optional) ---
MICROSOFT_CLIENT_ID=your_azure_client_id
MICROSOFT_CLIENT_SECRET=your_azure_client_secret
MICROSOFT_TENANT_ID=common

# --- Database ---
DATABASE_URL=sqlite:///./storage/data/grievances.db   # dev default

# --- Security ---
JWT_SECRET_KEY=your_secret_key_here
```

> **Note:** `backend/.env` is gitignored — your credentials are never committed.

---

## 📡 API Reference

The full interactive API is available at **http://localhost:8000/docs** when the backend is running.

### Core Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/health` | System health check |
| `GET` | `/` | Backend status |
| `POST` | `/auth/google` | Initiate Gmail OAuth |
| `POST` | `/auth/microsoft` | Initiate Microsoft OAuth |
| `GET` | `/emails/` | List all emails (paginated) |
| `GET` | `/emails/{id}` | Get email detail |
| `POST` | `/emails/{id}/reply` | AI-assisted reply |
| `POST` | `/intelligence/triage` | Classify a grievance text |
| `POST` | `/policies/upload` | Upload a policy PDF |
| `GET` | `/policies/search` | RAG-powered policy search |
| `GET` | `/settings/categories` | List user categories |
| `PUT` | `/settings/categories/{id}` | Update category |

---

## 🐳 Deployment

### Docker Compose (Recommended for production)

```bash
# Build and start all services
docker-compose up --build -d

# Services:
# - backend:  http://localhost:8000
# - frontend: http://localhost:80
# - postgres: port 5432
# - redis:    port 6379
```

### Kubernetes

```bash
# Apply manifests in order
kubectl apply -f k8s/00-secrets.example.yaml   # Edit secrets first!
kubectl apply -f k8s/01-namespaces-and-config.yaml
kubectl apply -f k8s/02-database-tier.yaml
kubectl apply -f k8s/03-application-tier.yaml
kubectl apply -f k8s/04-frontend-and-routing.yaml
kubectl apply -f k8s/05-pod-disruption-budgets.yaml
```

### Cloudflare Tunnel (Secure Local LLM Exposure)

```bash
# Install cloudflared
# https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/

cloudflared tunnel create grievance-ai
cloudflared tunnel route dns grievance-ai your-subdomain.yourdomain.com
cloudflared tunnel run grievance-ai
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License.

---

## 👤 Author

**Abir Chatterjee**
- GitHub: [@abir1010200](https://github.com/abir1010200)
- Email: abirchatterjee182004@gmail.com

---

<div align="center">
  <sub>Built with ❤️ using FastAPI · React · FAISS · LangChain · Gmail API</sub>
</div>
