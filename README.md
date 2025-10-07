# DoctoGreed - AI-Powered Cardiology Assistant

An intelligent medical platform designed to optimize cardiology consultations through AI-driven patient pre-evaluation, automated medical reporting, and post-consultation follow-up.

## 🎯 Project Overview

### The Problem
The cardiology field faces critical challenges:
- **Shortage of cardiologists** with uneven geographical distribution
- **Inefficient patient triage** without prior medical assessment
- **Time-consuming consultations** spent gathering basic information
- **Limited post-consultation follow-up** and patient monitoring

### The Solution
DoctoGreed addresses these challenges by providing:
- **Pre-consultation assessment** - AI-powered questionnaire to gather patient symptoms and medical history
- **Intelligent patient triage** - Automatic risk assessment using the Kansas City Cardiomyopathy Questionnaire (KCCQ)
- **Structured medical reports** - Automated generation of comprehensive patient summaries
- **Doctor dashboard** - Prioritized patient view with detailed diagnostic information

## 🏗️ Architecture

The application uses a microservices architecture orchestrated with Docker Compose:

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   Frontend      │      │    Backend      │      │   PostgreSQL    │
│   (Streamlit)   │◄────►│    (FastAPI)    │◄────►│    Database     │
│   Port 8501     │      │   Port 8000     │      │   Port 5432     │
└─────────────────┘      └─────────────────┘      └─────────────────┘
                                 │
                                 ▼
                         ┌─────────────────┐
                         │  Scaleway AI    │
                         │  (Mistral LLM)  │
                         └─────────────────┘
```

### Components

**Frontend (Streamlit)**
- Patient interface for questionnaire completion
- Doctor dashboard for viewing and managing patient diagnostics
- PDF report generation and download

**Backend (FastAPI + Tortoise ORM)**
- RESTful API for patient and diagnostic management
- Integration with Scaleway AI for medical text generation
- KCCQ score calculation and risk assessment
- PostgreSQL database interaction via Tortoise ORM

**Database (PostgreSQL)**
- Patient records
- Diagnostic data with questionnaire responses
- Doctor information

## 🚀 Quick Start

### Prerequisites

- **Docker** (version 20+)
- **Docker Compose** (version 3.8+)
- **Scaleway AI API key** (or OpenAI-compatible API)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/your-account/doctogreed-hackathon.git
cd doctogreed-hackathon
```

2. **Configure environment variables**

Create a `.env` file in the root directory:
```bash
cp .envexemple .env
```

Edit `.env` with your credentials:
```env
DATABASE_URL=postgres://user:password@db:5432/mydatabase
OPENAI_BASE_URL=https://api.scaleway.ai/YOUR-PROJECT-ID/v1
OPENAI_API_KEY=your_api_key_here
```

3. **Launch the application**
```bash
docker-compose up --build
```

This will start:
- **Backend API**: http://localhost:8000
- **Frontend UI**: http://localhost:8501
- **PostgreSQL**: localhost:5432

### First Use

1. **Create a patient**
```bash
curl -X POST http://localhost:8000/create/patient/ \
  -H "Content-Type: application/json" \
  -d '{"nom":"John Doe"}'
```

2. **Access the patient interface**
   - Navigate to http://localhost:8501
   - Click "Chatbot patient" in the sidebar
   - Enter patient name and complete the questionnaire

3. **View results in doctor dashboard**
   - Click "Docteur" in the sidebar
   - Select a patient to view their assessment and KCCQ score

### Stopping the Application

```bash
docker-compose down
```

To remove all data including the database:
```bash
docker-compose down -v
```

## 📋 Features in Detail

### Patient Questionnaire
- 12-question KCCQ assessment covering:
  - Physical limitations
  - Symptom frequency and burden
  - Quality of life impact
  - Social limitations
- AI-powered question translation and adaptation
- Automatic score calculation (0-100 scale)

### Risk Assessment
The system calculates a comprehensive KCCQ score:
- **0-25**: Severe limitations (High Risk)
- **26-50**: Moderate limitations (Medium Risk)
- **51-75**: Mild limitations (Low-Medium Risk)
- **76-100**: Minimal limitations (Low Risk)

### Doctor Dashboard
- Patient list with complete diagnostic information
- Detailed questionnaire responses
- AI-generated medical summary in French
- Risk level assessment based on KCCQ score

### Medical Report Generation
- Automated summary using Mistral NeMo LLM
- References to scientific literature (KCCQ validation studies)
- PDF export capability

## 🛠️ API Documentation

Once running, access the interactive API documentation:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### Key Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/create/patient/` | Create a new patient |
| POST | `/create/docteur/` | Create a new doctor |
| GET | `/diagnostic` | Retrieve questionnaire questions |
| POST | `/recup-diagnostic/` | Submit patient responses |
| GET | `/get_diagnostique/` | Get all diagnostic records |

## 📁 Project Structure

```
doctogreed-hackathon/
├── docker-compose.yaml       # Docker orchestration
├── .env                      # Environment variables (create this)
├── .envexemple              # Example environment file
│
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── BACKEND.md           # Backend documentation
│   └── app/
│       ├── main.py          # FastAPI application entry
│       ├── routes.py        # API endpoints
│       ├── db_models.py     # Database models
│       ├── enter_model.py   # Pydantic schemas
│       ├── ds.py            # AI question adaptation
│       ├── diago.py         # Diagnostic summary generation
│       └── score.py         # KCCQ score calculation
│
└── frontend/
    ├── Dockerfile
    ├── requirements.txt
    ├── FRONTEND.md          # Frontend documentation
    └── pages/
        ├── patient.py       # Patient interface
        └── doctor.py        # Doctor dashboard
```

## 🔧 Development

### Local Development (without Docker)

**Backend**
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
export DATABASE_URL="postgres://user:password@localhost:5432/mydatabase"
uvicorn app.main:app --reload
```

**Frontend**
```bash
cd frontend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
streamlit run pages/patient.py
```

### Database Migrations

Using Aerich for database schema management:
```bash
cd backend
aerich init -t app.db_models
aerich migrate
aerich upgrade
```

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is open source. Please specify your preferred license (MIT, Apache 2.0, etc.).

## 🙏 Acknowledgments

- Kansas City Cardiomyopathy Questionnaire (KCCQ) by John A. Spertus et al.
- Scaleway AI for LLM infrastructure
- Mistral AI for the Mistral NeMo model

## 📞 Support

For questions or issues:
- Open an issue on GitHub
- Refer to component-specific documentation:
  - `backend/BACKEND.md`
  - `frontend/FRONTEND.md`

---

**Note**: This project was developed as part of the DoctoGreed Hackathon to demonstrate AI applications in healthcare optimization.
