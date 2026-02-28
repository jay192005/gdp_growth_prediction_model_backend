# GDP Growth Prediction Model - Backend API

A Flask-based REST API for GDP growth prediction and economic scenario simulation powered by Machine Learning.

## 🚀 Quick Deploy to Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy)

## 📋 Overview

This backend API provides:
- **GDP Growth Prediction** - Forecast GDP growth based on economic indicators
- **Scenario Simulation** - Test "what-if" economic scenarios
- **Historical Data** - Access historical economic data for 203 countries
- **Firebase Authentication** - Secure API endpoints

## 🛠️ Tech Stack

- **Framework**: Flask 2.3.0
- **ML Model**: Scikit-learn Random Forest
- **Authentication**: Firebase Admin SDK
- **Server**: Gunicorn
- **Data Processing**: Pandas, NumPy

## 📦 Installation

### Local Development

```bash
# Clone repository
git clone https://github.com/jay192005/gdp_growth_prediction_model_backend.git
cd gdp_growth_prediction_model_backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run development server
python app_scenario.py
```

Server will start at `http://localhost:5000`

## 🔧 Environment Variables

Create a `.env` file (optional for local development):

```env
PORT=5000
FLASK_ENV=development
PYTHON_VERSION=3.11.0
```

For production (Render), these are configured in `render.yaml`

## 📁 Project Structure

```
backend/
├── app_scenario.py              # Main Flask application
├── config.py                    # Configuration settings
├── firebase_auth.py             # Firebase authentication
├── requirements.txt             # Python dependencies
├── render.yaml                  # Render deployment config
├── gdp_scenario_model.pkl       # Trained ML model
├── country_encoder_scenario.pkl # Country encoder
├── feature_info_scenario.pkl    # Feature metadata
├── final_data_with_year.csv     # Historical dataset
└── firebase_credentials.json    # Firebase credentials (not in repo)
```

## 🌐 API Endpoints

### Public Endpoints

#### `GET /`
API information and health check

**Response:**
```json
{
  "name": "Vikalp.ai - GDP Economic Scenario Simulator",
  "version": "v4.0-scenario",
  "model_loaded": true,
  "endpoints": {...}
}
```

#### `GET /api/countries`
Get list of all available countries

**Response:**
```json
["Afghanistan", "Albania", "Algeria", ...]
```

#### `GET /api/history?country=United States`
Get historical GDP data for a country

**Response:**
```json
[
  {
    "Country": "United States",
    "Year": 2020,
    "GDP_Growth": 2.3,
    "Exports_Growth": 3.1,
    "Imports_Growth": 2.8
  },
  ...
]
```

### Protected Endpoints (Require Authentication)

#### `POST /simulate`
Simulate economic scenario

**Request Body:**
```json
{
  "Country": "United States",
  "Population_Growth_Rate": 1.0,
  "Exports_Growth_Rate": 10.0,
  "Imports_Growth_Rate": 5.0,
  "Investment_Growth_Rate": 8.0,
  "Consumption_Growth_Rate": 3.0,
  "Govt_Spend_Growth_Rate": 2.0
}
```

**Response:**
```json
{
  "scenario": {...},
  "predicted_gdp_growth": 4.5,
  "model_type": "Scenario Simulator",
  "interpretation": "If these growth rates occur simultaneously, GDP is predicted to grow by 4.5%"
}
```

#### `GET /api/baseline?country=United States`
Get baseline growth rates for a country

**Response:**
```json
{
  "country": "United States",
  "baseline_rates": {
    "population": 0.8,
    "exports": 3.2,
    "imports": 2.9,
    "investment": 4.1,
    "consumption": 2.5,
    "govt_spend": 1.8
  }
}
```

## 🔐 Authentication

The API uses Firebase Authentication. Protected endpoints require a valid Firebase ID token in the Authorization header:

```
Authorization: Bearer <firebase_id_token>
```

## 🚀 Deployment to Render

### Method 1: Via Render Dashboard (Recommended)

1. **Go to Render:**
   - Visit [render.com/new](https://render.com/new)
   - Sign in with GitHub

2. **Create New Web Service:**
   - Click "New +" → "Web Service"
   - Connect your GitHub account
   - Select repository: `gdp_growth_prediction_model_backend`

3. **Configure Service:**
   - Name: `vikalp-ai-backend`
   - Region: Oregon (or closest to you)
   - Branch: `main`
   - Runtime: Python 3
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `gunicorn app_scenario:app`

4. **Environment Variables:**
   Add these in Render dashboard:
   ```
   PYTHON_VERSION=3.11.0
   PORT=10000
   FLASK_ENV=production
   ```

5. **Deploy:**
   - Click "Create Web Service"
   - Wait 5-10 minutes for build
   - Your API will be live at `https://your-service.onrender.com`

### Method 2: Via render.yaml (Automatic)

The repository includes `render.yaml` for automatic deployment:

1. Push code to GitHub
2. In Render Dashboard, click "New +" → "Blueprint"
3. Select your repository
4. Render will automatically configure from `render.yaml`
5. Click "Apply"

## 📊 Model Information

- **Algorithm**: Random Forest Regressor
- **Training Data**: 50+ years of economic data (1973-present)
- **Countries**: 203 countries
- **Accuracy**: ~90% on validation set
- **Features**: 7 economic indicators
  - Population Growth Rate
  - Exports Growth Rate
  - Imports Growth Rate
  - Investment Growth Rate
  - Consumption Growth Rate
  - Government Spending Growth Rate
  - Country (encoded)

## 🧪 Testing

### Test Locally

```bash
# Start server
python app_scenario.py

# Test health endpoint
curl http://localhost:5000/

# Test countries endpoint
curl http://localhost:5000/api/countries

# Test simulation
curl -X POST http://localhost:5000/simulate \
  -H "Content-Type: application/json" \
  -d '{
    "Country": "United States",
    "Population_Growth_Rate": 1.0,
    "Exports_Growth_Rate": 10.0,
    "Imports_Growth_Rate": 5.0,
    "Investment_Growth_Rate": 8.0,
    "Consumption_Growth_Rate": 3.0,
    "Govt_Spend_Growth_Rate": 2.0
  }'
```

### Test Production

Replace `localhost:5000` with your Render URL:
```bash
curl https://your-service.onrender.com/
```

## 🔄 CORS Configuration

CORS is enabled for all origins. For production, update in `app_scenario.py`:

```python
CORS(app, origins=[
    "http://localhost:5173",
    "https://your-frontend.vercel.app"
])
```

## 📝 Required Files for Deployment

Ensure these files are in your repository:

- ✅ `app_scenario.py` - Main application
- ✅ `requirements.txt` - Dependencies
- ✅ `render.yaml` - Render configuration
- ✅ `gdp_scenario_model.pkl` - ML model
- ✅ `country_encoder_scenario.pkl` - Encoder
- ✅ `feature_info_scenario.pkl` - Feature info
- ✅ `final_data_with_year.csv` - Dataset
- ✅ `config.py` - Configuration
- ✅ `firebase_auth.py` - Authentication
- ⚠️ `firebase_credentials.json` - Add as environment variable in Render

## 🐛 Troubleshooting

### Build Fails

**Error: "Could not find a version that satisfies the requirement"**
```bash
# Solution: Update requirements.txt with compatible versions
pip freeze > requirements.txt
```

### Model Not Loading

**Error: "Model not loaded"**
- Ensure `.pkl` files are in repository
- Check file paths in `config.py`
- Verify files are not in `.gitignore`

### CORS Errors

**Error: "CORS policy blocked"**
- Update CORS origins in `app_scenario.py`
- Add your frontend domain to allowed origins

### Slow Cold Starts

Render free tier has cold starts (~30 seconds). Solutions:
- Upgrade to paid tier
- Use a keep-alive service
- Accept cold start delay

## 📈 Performance

- **Response Time**: < 200ms (warm)
- **Cold Start**: ~30 seconds (free tier)
- **Concurrent Requests**: 100+ (depends on plan)
- **Uptime**: 99.9% (paid tier)

## 🔐 Security

- ✅ Firebase Authentication
- ✅ Input validation
- ✅ Error handling
- ✅ HTTPS (automatic on Render)
- ✅ Environment variables for secrets

## 📞 Support

- **Documentation**: See `RENDER_DEPLOYMENT.md`
- **Issues**: GitHub Issues
- **Render Docs**: [render.com/docs](https://render.com/docs)

## 📄 License

MIT

## 👤 Author

Jay Gavali

## 🤝 Contributing

Contributions welcome! Please open an issue or PR.

---

**Deployment Status:** ✅ Ready for Production

**Live API:** `https://your-service.onrender.com`
