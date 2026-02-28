# GDP Growth Prediction Model - Backend Deployment Guide

## 🚀 Render Deployment Instructions

### Prerequisites
- Render account (https://render.com)
- Firebase credentials JSON file
- This repository

### Step 1: Create Web Service on Render

1. Go to https://render.com/dashboard
2. Click "New +" → "Web Service"
3. Connect your GitHub repository: `https://github.com/jay192005/gdp_growth_prediction_model_backend.git`
4. Configure the service:
   - **Name**: `gdp-backend` (or your preferred name)
   - **Region**: Choose closest to your users
   - **Branch**: `main`
   - **Runtime**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn app_scenario:app`

### Step 2: Add Environment Variables

In Render dashboard, go to "Environment" tab and add:

```
PORT=10000
PYTHON_VERSION=3.11.0
```

### Step 3: Add Firebase Credentials

**IMPORTANT**: You need to add your Firebase credentials as an environment variable.

1. In Render dashboard, go to "Environment" tab
2. Add a new environment variable:
   - **Key**: `FIREBASE_CREDENTIALS`
   - **Value**: Paste the entire content of your `firebase_credentials.json` file

3. Update `firebase_auth.py` to read from environment variable (see code below)

### Step 4: Update firebase_auth.py

Replace the credentials loading section in `firebase_auth.py`:

```python
import os
import json

def initialize_firebase_auth():
    global firebase_app
    
    try:
        if firebase_app is not None:
            print("✅ Firebase already initialized")
            return firebase_app
        
        # Try to load from environment variable first (for production)
        firebase_creds = os.environ.get('FIREBASE_CREDENTIALS')
        
        if firebase_creds:
            # Parse JSON from environment variable
            cred_dict = json.loads(firebase_creds)
            cred = credentials.Certificate(cred_dict)
        else:
            # Fallback to file (for local development)
            cred = credentials.Certificate('firebase_credentials.json')
        
        firebase_app = firebase_admin.initialize_app(cred)
        print("✅ Firebase Authentication initialized successfully")
        return firebase_app
        
    except Exception as e:
        print(f"⚠️ Firebase initialization failed: {e}")
        traceback.print_exc()
        return None
```

### Step 5: Deploy

1. Click "Create Web Service"
2. Render will automatically:
   - Clone your repository
   - Install dependencies from `requirements.txt`
   - Start your Flask app with Gunicorn

### Step 6: Verify Deployment

Once deployed, test your API:

```bash
# Get API info
curl https://your-app-name.onrender.com/

# Get countries list
curl https://your-app-name.onrender.com/api/countries

# Test simulation
curl -X POST https://your-app-name.onrender.com/simulate \
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

## 📦 Files Included in Repository

### Core Application Files
- `app_scenario.py` - Main Flask application
- `config.py` - Configuration settings
- `firebase_auth.py` - Firebase authentication module
- `requirements.txt` - Python dependencies

### Model Files
- `gdp_scenario_model.pkl` - Trained ML model
- `country_encoder_scenario.pkl` - Country encoder
- `feature_info_scenario.pkl` - Feature information
- `final_data_with_year.csv` - Historical data

### Configuration Files
- `.gitignore` - Git ignore rules
- `README.md` - Project documentation

## ⚠️ Important Notes

### Large Files Warning
The model files (`gdp_model.pkl`, `gdp_scenario_model.pkl`) are large (>50MB). GitHub will show warnings but they will still work. Consider using Git LFS for production.

### Firebase Credentials
**NEVER commit `firebase_credentials.json` to the repository!** Always use environment variables in production.

### CORS Configuration
The app uses `flask-cors` to allow cross-origin requests. Update CORS settings in production if needed:

```python
CORS(app, origins=["https://your-frontend-domain.com"])
```

## 🔧 Local Development

```bash
# Install dependencies
pip install -r requirements.txt

# Run locally
python app_scenario.py

# Or with Gunicorn
gunicorn app_scenario:app
```

## 📝 Environment Variables Summary

| Variable | Required | Description |
|----------|----------|-------------|
| `PORT` | No | Port number (default: 5000) |
| `FIREBASE_CREDENTIALS` | Yes | Firebase credentials JSON |
| `PYTHON_VERSION` | No | Python version (default: 3.11.0) |

## 🆘 Troubleshooting

### Issue: Model files not loading
- Ensure all `.pkl` files are committed to the repository
- Check Render logs for file loading errors

### Issue: Firebase authentication fails
- Verify `FIREBASE_CREDENTIALS` environment variable is set correctly
- Ensure the JSON is valid and properly formatted

### Issue: CORS errors
- Update CORS configuration in `app_scenario.py`
- Add your frontend domain to allowed origins

## 📞 Support

For issues or questions, check:
- Render documentation: https://render.com/docs
- Flask documentation: https://flask.palletsprojects.com/
- Firebase Admin SDK: https://firebase.google.com/docs/admin/setup
