# Workforce Intelligence: SAS Data Hackathon edition
Three roles (Student, Recruiter, Course Agency) on top of the SAS hackathon data: 14.8k analytics job postings, 1.6k company posting rows, 139 junior skill profiles (salary-hike outcome) and 161 senior personality profiles (success outcome).

## Run
`bash start.sh` (needs Docker Desktop, Java 21+, Maven, Node). Opens http://localhost:5174. Demo logins (password `Demo@12345`): riya@, aryan@, kabir@, sana@, dev@ (students), recruiter@, agency@ `demo.com`.
Optional AI: put a free Gemini key (or `ANTHROPIC_API_KEY`) when asked. Optional own model: `echo "http://localhost:9000/chat" > .bot-url` (see model-adapter/).

## Data pipeline (data-prep/build_data.py)
Cleans the four files, builds `backend/src/main/resources/data/insights.json`, and fits two logistic-regression models (5-fold CV: junior accuracy 0.82 / AUC 0.91, senior accuracy 0.93 / AUC 0.97). Re-run with:
`python data-prep/build_data.py "<folder with the 4 files>" backend/src/main/resources/data`
Cleaning notes are shown in the app (Market page, bottom) and cover: salary text ("7.8L") parsing, 1,001 duplicate postings removed, placeholder "..." skills dropped, missing descriptions, outliers.

## Chatbot order
1. Your model (CUSTOM_BOT_URL) 2. Claude / Gemini 3. Built-in data answers (no key needed). Every path is given the dataset facts; data questions are answered from them, other questions from general knowledge.

## Assessment
AI writes up to 8 questions of rising difficulty for ANY skill (offline sets for SQL, Python, Statistics, Machine Learning, Data Visualization). 12 s per answer, timer enforced by the server, weighted scoring, no paste, leaving the tab voids the answer, 5-minute retake cooldown.

## Security / operations
Passwords hashed (BCrypt), JWT + rotating refresh cookie, role checks on the server, Gmail/email format checks, login lockout (5 failures / 15 min), AI rate limit, `/api/health`. For real users: serve over HTTPS, set COOKIE_SECURE=true, use managed MongoDB, set strong JWT secrets.
