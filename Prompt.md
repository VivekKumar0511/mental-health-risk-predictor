# MindScan — Mental Wellness Screening and Mood Tracker

Okay so this is the full project spec. read everything carefully before writing any code. the project is a mental wellness web app — users take a PHQ-9 or DASS-21 questionnaire, an ML model predicts their depression/anxiety/stress levels, and they can track mood daily through a journal. everything shows up on a personal dashboard with charts and history.

three folders to build:

- backend/ — Python Flask, MongoDB, ML model, all API routes
- frontend/ — the pages users actually open and interact with
- dashboard/ — separate site showing history, mood charts, analytics

tech stack is Flask + MongoDB + scikit-learn on the backend. plain HTML CSS Vanilla JS on frontend, no React. Chart.js for graphs. JWT for auth.

---

AUTH

signup takes name, email, password. hash passwords with bcrypt before saving, 10 rounds. login returns a JWT token, store it in localStorage, expires in 7 days. any protected page checks for the token first — if missing, redirect to login immediately. show the users name in the navbar once logged in.

pages needed: login.html, signup.html. centered card layout, clean and simple.

---

SCREENING

this is the core feature of the whole app.

PHQ-9 — 9 questions, screens for depression only
DASS-21 — 21 questions, covers depression + anxiety + stress

how the questionnaire page works:
- show one question at a time, never all at once
- progress bar at the top showing completion percentage
- four answer options per question shown as clickable cards
- options are: Not at all / Several days / More than half the days / Nearly every day
- user clicks an answer, it highlights, then hits Next
- they can go back and change any answer
- last question shows Submit instead of Next
- after submit, show a loading animation for 2-3 seconds while model runs, then redirect to results

before any questions start, show a disclaimer card. something like — this tool screens for wellness patterns, it is not a clinical diagnosis. please speak to a licensed professional for real support. user clicks "I understand, let's start" to proceed.

---

ML MODEL

write train_model.py as a completely separate script. run it once to generate the pkl files, then the Flask app loads those files at startup.

DATA PREPROCESSING — do this before training, and also repeat it every time a real user submits answers:

- raw answers are integers 0 to 3 per question
- check for missing values or anything outside 0-3 range and handle those before proceeding
- calculate subscores by summing question groups — PHQ-9 is a straight total of all 9 answers, DASS-21 splits into three subscales so calculate depression anxiety stress scores separately
- normalize using StandardScaler so PHQ-9 scores (max 27) and DASS-21 subscales (max 42 each) dont bias the model
- save the scaler with joblib alongside the model — same scaler must be used at prediction time or results will be wrong

training:
- use PHQ-9 and DASS-21 clinical scoring thresholds to generate synthetic training data if no real dataset is available
- algorithm is Random Forest, either three separate classifiers or one multi-output
- output labels are Minimal / Mild / Moderate / Severe for each condition
- after training: joblib.dump(model, 'backend/ml/mindscan_model.pkl') and joblib.dump(scaler, 'backend/ml/scaler.pkl')
- load both files once when Flask starts, not on every request

---

RESULTS PAGE

show output in a way a non-technical person understands immediately. no raw numbers dumped on screen.

at the top, a wellness score out of 100 calculated from the three DASS subscores. if any result is Moderate or Severe, put a crisis helpline number right at the top before anything else — dont bury it below the cards.

then three result cards side by side — Depression, Anxiety, Stress. each card has:
- color coded severity badge (green = Minimal, yellow = Mild, orange = Moderate, red = Severe)
- two lines in plain language explaining what that level generally means
- no medical jargon

bottom section shows 3-4 resource links based on the worst severity level. Minimal gets breathing exercises and sleep tips. Mild gets mindfulness content and journaling guides. Moderate gets CBT exercises and online therapy platforms. Severe gets crisis lines and how to find a psychiatrist.

two buttons: Save to History and Retake Screening. disclaimer repeated at the very bottom of the page.

---

MOOD JOURNAL

this is separate from screening. daily mood logging feature.

the journal entry form has:
- five emoji mood buttons at the top — Very Happy, Happy, Neutral, Sad, Very Sad
- a textarea for free writing, max 2000 characters with a live counter
- tag input below — user types a word, hits enter, it becomes a small chip. chips have an X to remove
- date field auto-filled to today, editable
- Save Entry button

past entries displayed below the form in a vertical timeline. each entry card shows the date, mood emoji, tags as chips, and first two lines of text. click anywhere on the card to expand and read the full entry. each card has Edit and Delete buttons. search bar at the top filters entries by keyword or tag across everything.

---

DASHBOARD

separate HTML files in the dashboard folder. sidebar on the left with navigation links — Home, Screening History, Journal, Resources, Profile, Settings. sidebar collapses to hamburger on mobile.

dashboard home page shows:
- wellness score from the most recent screening
- line chart (Chart.js) showing mood score day by day for the last 30 days
- last 3 screenings shown as small cards with date and severity badges
- journaling streak — how many consecutive days the user has written an entry
- one random motivational quote, picked from a hardcoded list of 25-30 quotes

screening history page is a table with columns: Date, PHQ-9 Score, Depression, Anxiety, Stress. clicking a row expands below it to show full result details without leaving the page.

resources page organizes links by category — Meditation, Therapy, Crisis Support, Self-Help Tools. each link has a short one-line description.

---

BACKEND ROUTES

POST /api/auth/signup
POST /api/auth/login
GET /api/auth/profile
POST /api/screening/submit
GET /api/screening/history
POST /api/journal/add
GET /api/journal/entries
PUT /api/journal/:id
DELETE /api/journal/:id
GET /api/resources

ERROR HANDLING — 
every route needs this, no skipping:

- signup: if email already registered return 409 with clear message
- login: wrong password returns 401, not 500
- screening submit: if answers array is missing or wrong length return 400 with what was expected. wrap the joblib prediction in try/except so if the model crashes it returns 500 with "model unavailable" and doesnt take down the whole server
- journal: if entry id doesnt exist or belongs to a different user return 404
- MongoDB connection failure: catch at startup, log the error clearly, dont silently fail
- frontend side: every fetch call needs try/catch. failed API calls show a toast notification with a readable message, never just a console.log. if a 401 comes back on any protected page, automatically redirect to login instead of showing a broken empty page

---

DATABASE SCHEMAS

users — name, email, password (hashed), createdAt

screenings — userId, answers array, phq9Score, dass21 object (depression/anxiety/stress keys), prediction object (same keys), wellnessScore, date

journal — userId, mood string, moodScore (1 to 5), entryText, tags array, date, createdAt

---

FILES TO CREATE

frontend: index.html, login.html, signup.html, screening.html, results.html, journal.html, style.css, app.js

backend: app.py, config/db.py, models/user.py, models/screening.py, models/journal.py, routes/auth.py, routes/screening.py, routes/journal.py, ml/train_model.py, ml/mindscan_model.pkl, ml/scaler.pkl, .env, requirements.txt

dashboard: index.html, history.html, journal.html, resources.html, profile.html, settings.html, dashboard.css, dashboard.js

---

UI NOTES

color palette — soft blues (#4A90D9), muted greens (#5BAD8F), white backgrounds, light gray for cards. nothing harsh or loud, this is a wellness app.

font is Poppins from Google Fonts. use it for everything.

small UI details that matter:
- questionnaire answer cards get a colored border on selection, not just a background change
- progress bar on questionnaire animates smoothly, dont make it jump
- results page severity badges fade in one by one, not all at once
- toast notifications for every save, delete, and error — bottom right corner
- dashboard cards use loading skeletons while data is fetching, not a spinner
- journal tag chips have rounded corners and an X button that actually works
- on mobile the dashboard sidebar slides in from left, doesnt push content

---

SECURITY

- bcrypt for passwords, 10 salt rounds
- JWT secret lives only in .env, never written in code
- all screening and journal routes behind JWT middleware
- validate all form inputs on frontend before the request even goes out
- CORS restricted to known origins in production
- .env in .gitignore, provide a .env.example instead

---

BUILD ORDER

1. Flask setup and MongoDB connection
2. auth routes — test signup and login in Postman before moving on
3. write train_model.py, run it, confirm pkl files are generated
4. screening submit route with preprocessing and ML prediction
5. journal CRUD routes
6. frontend auth pages
7. questionnaire page with one-question-at-a-time logic
8. results page
9. journal page
10. dashboard home with Chart.js mood chart
11. dashboard history and resources pages
12. final round of testing, fix any broken API connections

---

WHAT TO DELIVER

complete working code for all three folders. train_model.py fully commented explaining every step. requirements.txt with all dependencies listed. a .env.example file. setup instructions covering how to run the backend, open the frontend, set up MongoDB locally and on Atlas, and deploy backend to Render and frontend to Vercel or Netlify.
