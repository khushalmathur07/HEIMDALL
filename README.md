# 🛡️ HEIMDALL

**AI-Powered Identity & Document Screening System**
Smart India Hackathon 2026 · Problem Statement 26188
Ministry of Home Affairs · Sashastra Seema Bal (SSB)

HEIMDALL screens identity documents (Aadhaar, passport, visa, driving licence, national ID) and produces an **evidence-based risk assessment**. Instead of a single opaque "fake / genuine" verdict, it fuses several independent signals into a transparent risk score, shows *why* a document was flagged, and gives a human reviewer a clear recommendation.

> HEIMDALL is a **decision-support tool**. Anomalies are indicators for review, not proof of forgery, and face similarity is a supporting signal, not identity confirmation.

---

## ✨ Features

- **OCR extraction**: reads document fields with EasyOCR and scores the document type using multiple signals.
- **Document validation**: document-type-specific rule checks (for example Aadhaar format checks) and a separate, clearly labelled expiry check.
- **MRZ verification**: ICAO 9303 machine-readable-zone parsing and check-digit validation for passports and visas.
- **Image forensics**: Error Level Analysis (ELA) and metadata inspection to flag possible tampering.
- **Face verification**: compares the ID photo with a live or reference photo using DeepFace (VGG-Face).
- **Evidence fusion & risk scoring**: capped additive scoring per category, with every flag traced to a specific risk factor.
- **Blockchain audit trail**: anchors a SHA-256 hash of each result (no personal data) on an Ethereum testnet (Sepolia), or in a clearly labelled demo ledger when not configured.
- **PDF reports**: downloadable screening reports built from the canonical result.
- **Dashboard & analytics**: screening history, risk distribution, trends and engine health.
- **Authentication**: email/OTP login and Google sign-in.

---

## 🧠 How the risk decision works

| Score | Decision |
|-------|----------|
| 0 – 24 | ✅ **PASS** |
| 25 – 59 | ⚠️ **REVIEW REQUIRED** |
| 60 – 100 | 🚨 **HIGH RISK** |

Each category contributes capped points, so no single module can inflate the score and nothing is double-counted:

| Category | Max points |
|----------|-----------|
| Document validation | 30 |
| MRZ integrity | 25 |
| Image forensics | 25 |
| Face verification | 65 |
| Metadata | 5 |
| OCR quality | 0 (a failure is reported as a *limitation*, not as fraud evidence) |

Safety overrides force at least `REVIEW_REQUIRED` when the document type is unknown, OCR is unavailable, or face matching is inconclusive despite a reference photo.

---

## 🏗️ Tech stack

**Backend** (`HEIMDALL-backend`)
Python · FastAPI · SQLAlchemy (async) + SQLite · EasyOCR · DeepFace / TensorFlow · OpenCV · scikit-image · ReportLab · Solidity (`ScreeningAudit.sol`)

**Frontend** (`HEIMDALL-frontend`)
React 18 · TypeScript · Vite · Tailwind CSS · Recharts · React Router · Axios

---

## 📁 Project structure

```
HEIMDALL/
├── HEIMDALL-backend/
│   ├── main.py              # FastAPI app entry point
│   ├── routers/             # auth, screening, blockchain endpoints
│   ├── services/            # ocr, validation, mrz, tampering, face, risk, report, blockchain
│   ├── models/  database/   # data models and DB setup
│   ├── contracts/           # ScreeningAudit.sol + deployment guide
│   ├── demo_docs/           # sample valid and tampered documents
│   └── tests/
└── HEIMDALL-frontend/
    └── src/                 # pages, components, API client
```

---

## 🚀 Getting started

### Prerequisites
- Python 3.10+
- Node.js 18+

### 1. Backend

```bash
cd HEIMDALL-backend
python -m venv .venv
# Windows: .venv\Scripts\activate     |  macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

API docs are available at http://localhost:8000/api/docs. The first start downloads and warms up the ML models, so it can take a while.

### 2. Frontend

```bash
cd HEIMDALL-frontend
npm install
npm run dev
```

Open http://localhost:5173. The dev server proxies `/api` to the backend on port 8000.

### 3. Environment variables (optional)

Create `HEIMDALL-backend/.env`. Everything has a sensible default except where noted.

```env
DATABASE_URL=sqlite+aiosqlite:///./heimdall.db
CORS_ORIGINS=http://localhost:5173

# Google sign-in
GOOGLE_CLIENT_ID=

# Email OTP
SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASSWORD=
SMTP_FROM=

# Real blockchain anchoring (otherwise Demo Blockchain mode is used)
BLOCKCHAIN_RPC_URL=
BLOCKCHAIN_PRIVATE_KEY=
CONTRACT_ADDRESS=
BLOCKCHAIN_NETWORK=sepolia
```

See `HEIMDALL-backend/contracts/README.md` for deploying the audit contract.

> ⚠️ Never commit your `.env` file or private keys.

---

## 🧪 Try it with demo documents

Use the **New Screening** page to upload your own document, or run the built-in demo cases. Sample documents are in `HEIMDALL-backend/demo_docs/` (a valid and a tampered Aadhaar).

Run the backend tests:

```bash
cd HEIMDALL-backend
pytest tests/
```

---

## 🔒 Privacy & responsible AI

- Uploaded identity files are git-ignored and never committed.
- Only a hash, screening ID, score, decision and timestamp go on-chain. No names, document numbers, images or OCR text.
- Failed modules are shown as analysis limitations rather than being turned into invented risk scores.
- Wording avoids over-claiming (for example, no "identity confirmed" statements).

---

## 👥 Team

Built for SIH 2026. Add your team name and members here.

## 📄 License

Add a license of your choice (for example MIT).
