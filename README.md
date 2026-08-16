# 🚗 Car Price Prediction API

A production-ready **FastAPI** Machine Learning REST API that predicts car selling prices based on vehicle specifications. It features **JWT authentication**, **API key verification**, **Redis response caching**, **Prometheus & Grafana monitoring**, and full **Docker & Render deployment support**.

---

## 🌟 Key Features

- **Machine Learning Pipeline**: Built with `scikit-learn` (`RandomForestRegressor`, `ColumnTransformer`, `OneHotEncoder`, `StandardScaler`).
- **Authentication & Security**: Dual-layer security using **JWT tokens** (`python-jose`) and custom **API Key headers**.
- **Redis Response Caching**: Caches prediction results in Redis to reduce redundant model inference latency.
- **Monitoring & Metrics**: Exposed `/metrics` endpoint powered by **Prometheus Instrumentator** and visual dashboards via **Grafana**.
- **Containerized Stack**: Complete multi-container environment via `Dockerfile` & `docker-compose.yml`.
- **Cloud Deployment**: Pre-configured `render.yaml` for seamless deployment on Render.

---

## 📂 Project Architecture

```text
car-price-prediction-api/
├── app/
│   ├── api/
│   │   ├── routes_auth.py       # Login & JWT token generation
│   │   └── routes_predict.py    # Car price prediction endpoint
│   ├── cache/
│   │   ├── redis_cache.py       # Production Redis caching layer
│   │   └── redis_cache_local.py # Local Redis caching fallback
│   ├── core/
│   │   ├── config.py            # Environment configuration & settings
│   │   ├── dependencies.py      # FastAPI security dependencies (API Key & JWT)
│   │   ├── exceptions.py        # Global exception handler
│   │   └── security.py          # JWT encoding and decoding utilities
│   ├── middleware/
│   │   └── logging_middleware.py# HTTP request/response logger
│   ├── models/
│   │   └── model.joblib         # Serialized ML model artifact
│   ├── services/
│   │   └── model_service.py     # Inference service with Redis caching
│   └── main.py                  # FastAPI application entrypoint
├── data/
│   └── car-details.csv          # Vehicle dataset used for training
├── notebooks/
│   └── sample.ipynb             # Exploratory Data Analysis & experiments
├── training/
│   ├── train_utils.py           # Paths & dataset file constants
│   └── train_model.py           # ML training pipeline script
├── Dockerfile                   # Single container build configuration
├── docker-compose.yml           # Full stack setup (API, Redis, Prometheus, Grafana)
├── prometheus.yml               # Prometheus scraping configuration
├── render.yaml                  # Render cloud deployment blueprint
├── requirements.txt             # Python dependencies
├── .env.example                 # Example environment variables
└── README.md                    # Project documentation
```

---

## ⚙️ Environment Variables

Create a `.env` file in the root directory by copying `.env.example`:

```bash
cp .env.example .env
```

| Variable Name | Default Value | Description |
| :--- | :--- | :--- |
| `API_KEY` | `demo-key` | API Key required in request header `api-key` |
| `JWT_SECRET_KEY` | `secret` | Secret key used to sign JWT tokens |
| `REDIS_URL` | `redis://localhost:6379` | Connection string for Redis cache |
| `MODEL_PATH` | `app/models/model.joblib` | Filepath to trained model file |

---

## 🛠️ Step-by-Step Setup Guide

### 1. Virtual Environment Setup

Activate your existing virtual environment or create a new one:

```bash
# Option A: Activate existing venv (if located in parent workspace)
source ../venv/bin/activate

# Option B: Create and activate a new virtual environment
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## 🏋️ Training the Machine Learning Model

To retrain the Random Forest model on `data/car-details.csv` and export `app/models/model.joblib`:

```bash
python -m training.train_model
```

---

## 🚀 Running the API Locally

Launch the local Uvicorn development server:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

- **Interactive API Docs (Swagger)**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **Alternative Docs (ReDoc)**: [http://localhost:8000/redoc](http://localhost:8000/redoc)
- **Prometheus Metrics**: [http://localhost:8000/metrics](http://localhost:8000/metrics)

---

## 🐳 Running with Docker Compose (Full Stack)

Run the entire application stack including **FastAPI**, **Redis**, **Prometheus**, and **Grafana**:

```bash
docker-compose up --build
```

### Service Access URLs:

| Service | Port | Access URL |
| :--- | :--- | :--- |
| **FastAPI App** | `8000` | [http://localhost:8000/docs](http://localhost:8000/docs) |
| **Prometheus** | `9090` | [http://localhost:9090](http://localhost:9090) |
| **Grafana** | `3000` | [http://localhost:3000](http://localhost:3000) *(Default login: `admin` / `admin`)* |
| **Redis** | `6379` | `localhost:6379` |

To stop the containers:

```bash
docker-compose down
```

---

## 🔑 API Usage & Endpoints

### 1. User Authentication (`POST /login`)

Obtain a JWT access token:

```bash
curl -X 'POST' \
  'http://localhost:8000/login' \
  -H 'Content-Type: application/json' \
  -d '{
  "username": "admin",
  "password": "admin"
}'
```

**Response**:
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

---

### 2. Predict Car Price (`POST /predict`)

Pass vehicle parameters along with the `token` and `api-key` headers:

```bash
curl -X 'POST' \
  'http://localhost:8000/predict' \
  -H 'Content-Type: application/json' \
  -H 'api-key: demo-key' \
  -H 'token: YOUR_JWT_ACCESS_TOKEN' \
  -d '{
  "company": "Maruti",
  "year": 2017,
  "owner": "First Owner",
  "fuel": "Diesel",
  "seller_type": "Individual",
  "transmission": "Manual",
  "km_driven": 60000,
  "mileage_mpg": 23.0,
  "engine_cc": 1248,
  "max_power_bhp": 88.5,
  "torque_nm": 200,
  "seats": 5
}'
```

**Response**:
```json
{
  "predicted_price": "550,000.00"
}
```

---

## 🌐 Cloud Deployment (Render)

This repository includes a [`render.yaml`](file:///Users/shubham/Desktop/fastapi/car-price-prediction-api/render.yaml) configuration file for automated deployment on Render.

1. Connect your GitHub repository to [Render](https://render.com/).
2. Select **Blueprint Deployment** using `render.yaml`.
3. Set your secret environment variables (`API_KEY`, `JWT_SECRET_KEY`, `REDIS_URL`) in the Render dashboard.

---

## 📄 License

This project is licensed under the MIT License.
