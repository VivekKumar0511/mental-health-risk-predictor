# Predicting mental health condition


A full-stack mental wellness web application that uses the **DASS-21 clinical questionnaire** and a **Random Forest ML model** to screen for depression, anxiety, and stress — combined with a mood journal and personal dashboard.

---

## 🌟 Features

- 🔐 **User Authentication** — Secure signup/login with bcrypt password hashing and JWT tokens
- 🧪 **DASS-21 Mental Health Screening** — 21-question clinical questionnaire with animated progress
- 🤖 **ML-Powered Predictions** — Random Forest classifier predicts severity levels (Minimal / Mild / Moderate / Severe)
- 📊 **Wellness Score** — Composite score out of 100 based on depression, anxiety, and stress subscales
- 📓 **Mood Journal** — Daily entries with emoji mood picker, tags, search, and delete
- 📈 **Dashboard** — 30-day mood trend chart, wellness score, day streak, motivational quotes
- 🆘 **Crisis Resources** — Auto-shows helpline numbers when Moderate/Severe results detected
- 📚 **Resource Library** — Curated mental health links for meditation, therapy, and self-help

---

## 🖥️ Screenshots

| Login | Screening | Dashboard |
|-------|-----------|-----------|
| Sign up / Login screen | 21-question DASS-21 flow | Mood chart + wellness score |

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Python, Flask, Flask-CORS |
| Database | MongoDB (pymongo) |
| ML Model | scikit-learn (Random Forest, StandardScaler) |
| Auth | bcrypt, PyJWT |
| Frontend | Vanilla HTML, CSS, JavaScript (SPA) |
| Charts | Chart.js |

---

## 📁 Project Structure

```
Golden response/
├── mindscan_app.py          ← Main Flask app (all routes + inline frontend)
├── mindscan_frontend.py     ← Frontend HTML module (optional)
├── mindscan_requirements.txt← Python dependencies
├── mindscan_.env.example    ← Environment variables template
├── mindscan_.gitignore      ← Git ignore rules
├── mindscan_README.md       ← This file
└── ml_models/               ← Auto-generated on first run
    ├── mindscan_model.pkl   ← Trained Random Forest model
    └── scaler.pkl           ← Fitted StandardScaler
```

---

## ⚙️ Installation & Setup

### Prerequisites
- Python 3.12+
- MongoDB 8.x (running locally on port 27017)

### Step 1 — Clone the repository
```bash
git clone https://github.com/VivekKumar0511/Mental-Wellness-Screening-and-Mood-Tracker.git
cd Mental-Wellness-Screening-and-Mood-Tracker
```

### Step 2 — Install dependencies
```bash
pip install flask flask-cors pymongo bcrypt pyjwt scikit-learn numpy pandas joblib python-dotenv
```

### Step 3 — Start MongoDB
```bash
# Windows
net start MongoDB

# Mac
brew services start mongodb-community
```

### Step 4 — Run the app
```bash
python mindscan_app.py
```

### Step 5 — Open in browser
```
http://localhost:5000
```

> The ML model trains automatically on first run (~10-20 seconds) and saves to `ml_models/`.

---

## 🧠 How the ML Model Works

1. **Dataset** — 5000 synthetic DASS-21 samples generated using clinical scoring rules
2. **Features** — PHQ-9 score, DASS Depression score, DASS Anxiety score, DASS Stress score
3. **Model** — `MultiOutputClassifier` wrapping `RandomForestClassifier` (100 estimators)
4. **Preprocessing** — `StandardScaler` normalizes features at both training and inference time
5. **Output** — Severity label for each subscale: `Minimal` / `Mild` / `Moderate` / `Severe`

### DASS-21 Severity Thresholds

| Subscale | Minimal | Mild | Moderate | Severe |
|----------|---------|------|----------|--------|
| Depression | 0–9 | 10–13 | 14–20 | 21+ |
| Anxiety | 0–7 | 8–9 | 10–14 | 15+ |
| Stress | 0–14 | 15–18 | 19–25 | 26+ |

---

## 🔑 Environment Variables

Copy `.env.example` to `.env` and update values:

```env
JWT_SECRET=your_secret_key_here
MONGO_URI=mongodb://localhost:27017/mindscan
PORT=5000
```

---

## 📡 API Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/auth/signup` | Create account | ❌ |
| POST | `/api/auth/login` | Login | ❌ |
| GET | `/api/auth/profile` | Get user profile | ✅ |
| POST | `/api/screening/submit` | Submit DASS-21 answers | ✅ |
| GET | `/api/screening/history` | Get screening history | ✅ |
| POST | `/api/journal/add` | Add journal entry | ✅ |
| GET | `/api/journal/entries` | Get all entries | ✅ |
| PUT | `/api/journal/:id` | Update entry | ✅ |
| DELETE | `/api/journal/:id` | Delete entry | ✅ |
| GET | `/api/resources` | Get mental health resources | ❌ |

---

## ⚠️ Disclaimer

> This application is a **screening tool only** and does **not** provide clinical diagnosis. Always consult a licensed mental health professional for proper evaluation and treatment.

**Crisis Support (India):**
- iCall Helpline: **9152987821**
- Global: [findahelpline.com](https://findahelpline.com)

---

## 👨‍💻 Author

**Vivek Kumar**
- GitHub: [@VivekKumar0511](https://github.com/VivekKumar0511)

---

## 📄 License

This project is licensed under the MIT License.
