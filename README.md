# Job Application Automation (n8n)

An end-to-end automation that runs a developer job search on autopilot — from finding live jobs, to drafting tailored cover letters with AI, to sending applications with a CV attached — all with a human-approval gate so nothing goes out without a review.

Built as a single [n8n](https://n8n.io) workflow (5 chained stages) using a Google Sheet as its database and control panel.

> ⚠️ This repository is a **sanitized** copy for portfolio purposes. Personal data (name, email, Google Sheet ID) and the embedded CV have been replaced with placeholders. API keys never live in the file — they are stored in n8n's Credentials.

---

## What it does

1. **Collect** — pulls fresh developer jobs from the official **Bundesagentur für Arbeit** (German Federal Employment Agency) Jobsuche API, then normalises, filters (keeps only relevant IT/developer roles, drops noise), and de-duplicates them into a Google Sheet.
2. **Draft** — for each new job, generates a tailored **German + English cover letter** using the OpenAI API (gpt-4o-mini), grounded in a reusable candidate profile.
3. **Send** — after the user marks a job **Approved**, emails the application via Gmail with the **CV attached**, and logs it. Jobs without a public company email are routed to a "Manual Apply" list instead.
4. **Follow up** — automatically schedules and sends polite follow-up emails after a set number of days.
5. **Report** — produces a weekly summary of the pipeline (applied / drafted / replied).

## Key features

- 🔗 **Real API integration** — official government jobs API with pagination and error handling.
- 🧠 **AI-generated, tailored content** — per-job cover letters in two languages via the OpenAI API.
- ✅ **Human-in-the-loop** — a two-gate approval flow (`Selected` → auto-draft → `Approved` → send) so no email is sent without review.
- 📎 **Attachments** — CV attached automatically to every application.
- 🗂️ **Spreadsheet as a database** — Google Sheets is both the data store and the control panel.
- 🧪 **Safe test mode** — a `TEST_MODE` flag routes every email to the candidate's own inbox for safe end-to-end testing before going live.
- 🧩 **One-run pipeline** — all five stages run from a single trigger.

## Architecture

```
Trigger
  └─ WF1  Collect jobs   (Bundesagentur API → normalise → filter → de-dupe → Sheet)
       └─ WF2  Draft      (OpenAI → German + English cover letters → Sheet)
            └─ WF3  Send   (approved jobs → Gmail + CV attachment → mark Applied)
                 └─ WF4  Follow-up   (scheduled reminder emails)
                      └─ WF5  Weekly report
```

A central **CONFIG** node holds all settings (search terms, limits, candidate profile, thresholds) in one place.

## Tech

`n8n` · `JavaScript (Code nodes)` · `REST APIs` · `OpenAI API` · `Google Sheets API` · `Gmail API`

## How to run it

1. Import `job-application-automation.n8n.json` into n8n.
2. Create credentials in n8n for Google Sheets, Gmail and OpenAI, and select them on the relevant nodes.
3. Fill in the **CONFIG** node: your `SHEET_ID`, candidate details, and search terms.
4. Replace the placeholder in **WF3 · Attach CV** with your base64-encoded CV PDF.
5. Keep `TEST_MODE: true` for the first run (emails go to your own inbox), then switch to `false` to go live.

---

*Built to solve a real problem — searching and applying for backend roles across Germany — and to practise API integration, AI prompting, and workflow design.*
