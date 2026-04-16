# Architecture: Job Search Bot

## System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                   GitHub Actions (Scheduler)                 │
│         Daily 7:23 AM (Taiwan) · Weekly Sunday 7:23 AM      │
└──────────────────────┬──────────────────────────────────────┘
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
   job_search.py              weekly_report.py
   (Daily ×2 emails)          (Weekly ×1 email)
          │                         │
          ▼                         ▼
┌─────────────────┐      ┌──────────────────────┐
│  Data Sources   │      │  Market Analysis      │
│                 │      │                       │
│  104 Job Bank   │      │  60+ jobs collected   │
│  JSearch API    │      │  across all regions   │
│  MyCareersFuture│      │                       │
└────────┬────────┘      │  Gemini AI analyzes:  │
         │               │  · Top skills         │
         ▼               │  · Skill gaps         │
┌─────────────────┐      │  · Location trends    │
│  Deduplication  │      │  · Market summary     │
└────────┬────────┘      └──────────┬───────────┘
         │                          │
         ▼                          ▼
┌─────────────────┐      ┌──────────────────────┐
│  AI Scoring     │      │  Weekly Email         │
│  (Gemini 2.5)   │      │  hsin78318@gmail.com  │
│                 │      └──────────────────────┘
│  Per job:       │
│  · Score 0–100  │
│  · Role type    │
│  · Strengths    │
│  · Skill gaps   │
│  · Salary est.  │
│  · Interview    │
│    tips         │
│  · Visa check   │
└────────┬────────┘
         │ score ≥ 70
         ▼
┌─────────────────┐
│  Email Delivery │
│  Gmail API      │
│                 │
│  🇹🇼 Taiwan     │
│  🌏 Intl        │
└─────────────────┘
```

## Data Flow

```
104 API ──────────────────────────────────┐
JSearch API (Japan / SG / HK) ────────────┤──► Deduplicate ──► AI Score ──► Filter ≥70 ──► Email
MyCareersFuture API (Singapore) ──────────┘
```

## Filter Logic

```
Incoming job
    │
    ├── Foreign / multinational company? ──No──► Skip
    │
    ├── Salary ≥ NT$50,000? ──No──► Skip
    │
    ├── Outsourcing / staffing agency? ──Yes──► Skip
    │
    ├── Requires local citizenship? ──Yes──► Skip
    │
    └── AI Score ≥ 70? ──No──► Skip
                │
                ▼
          Include in email
```
