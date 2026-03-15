# StudyMind AI — Python Backend + Supabase History

A Flask proxy that sits between the browser and the Anthropic API.
All Claude calls go through Python — the API key never travels across
the network in a raw API call from the browser.

## File structure

```
studymind-backend/
├── app.py                   ← Flask server (the filter)
├── requirements.txt
├── .env.example             ← copy to .env and fill in
├── supabase_migration.sql   ← run once in Supabase SQL Editor
└── studymind.html           ← the UI (open this file in a browser)
```

---

## Quick start

### 1 — Install dependencies

```bash
cd studymind-backend
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2 — Configure environment (optional)

```bash
cp .env.example .env
# Edit .env and add your Supabase URL + key if you want history
```

### 3 — Set up Supabase (optional but recommended)

1. Create a free project at https://supabase.com
2. Go to **SQL Editor → New query**
3. Paste the contents of `supabase_migration.sql` and click **Run**
4. Note your project URL and anon key (Settings → API)

### 4 — Start the server

```bash
python app.py
```

### 5 — Open the app

**Open your browser and go to: http://localhost:5000**

> ⚠️ Do **not** open `studymind.html` by double-clicking it.  
> Opening it as a `file://` URL causes browsers to block all API calls  
> ("Failed to fetch"). Always use the URL above instead.

---

## Using the UI

| Field | What to enter |
|-------|---------------|
| **BACKEND** | `http://localhost:5000` (default — leave as-is) |
| **API KEY** | Your Anthropic API key (`sk-ant-api03-…`) |
| **Supabase** | Expand the section, paste your project URL + anon key, click *Test connection* |

### History panel

Click **📚 History** (top right) to open the slide panel. Every
generated output is saved to Supabase and appears here with:

- Session title (auto-generated from file name / subject)
- Output type chip (Summary, Flashcards, etc.)
- Subject / level / goal chips
- Timestamp
- Click any item → full result viewer
- 🗑 button → delete that entry

---

## API endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/test-key` | Validate Anthropic API key |
| `POST` | `/api/generate` | Generate one output type |
| `GET`  | `/api/history` | List history records |
| `GET`  | `/api/history/<id>` | Get one full record |
| `DELETE` | `/api/history/<id>` | Delete a record |
| `POST` | `/api/supabase-config` | Test Supabase credentials |
| `GET`  | `/api/health` | Server health check |

---

## Outputs supported

`summary` · `flashcards` · `quiz` · `mindmap` · `studyplan` ·
`explain` · `keywords` · `enhanced_notes` · `timeline` · `comparison`

---

## Production tips

- Set `FLASK_DEBUG=0` in `.env` before deploying
- Use `gunicorn app:app` instead of `python app.py` in production
- Restrict CORS origins in `app.py` to your domain
- Tighten Supabase RLS policies for multi-user deployments
