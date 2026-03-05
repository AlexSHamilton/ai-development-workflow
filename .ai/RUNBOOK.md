# RUNBOOK — [YOUR PROJECT NAME]

All commands for launch, testing, and maintenance.

---

## 1. Prerequisites
```bash
node --version      # >= 20.x
python3 --version   # >= 3.11
docker --version    # Docker Desktop
supabase --version  # Supabase CLI
```

---

## 2. Frontend Repo ([your-web-repo])

### First run
```bash
cd [your-web-repo]
npm install
cp .env.example .env.local    # fill values
npm run dev
```

### Database (Supabase)
```bash
supabase start
supabase db push
```

---

## 3. Workers Repo ([your-workers-repo]) — if applicable

### First run
```bash
cd [your-workers-repo]
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

### ⛔ Running Python Scripts
Always use full path + PYTHONPATH:
```bash
VENV_PY="/Users/[YOUR-USERNAME]/[YOUR-PATH]/venv/bin/python3"
cd /Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-workers-repo]
PYTHONPATH=. $VENV_PY pipeline/script.py
```

---

## 4. Running Long Python Scripts (anti-token-burn pattern)
```bash
python3 pipeline/script.py --quiet > /tmp/run.log 2>&1 \
  && echo "EXIT OK" && tail -5 /tmp/run.log
```
