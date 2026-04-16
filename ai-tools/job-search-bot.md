# Job Search Bot

> Automated job search pipeline that scores roles against my profile using AI and delivers a daily email digest.

---

## Problem

Manually checking job boards across Taiwan, Japan, Singapore, and Hong Kong was taking 30+ minutes daily — with inconsistent quality and no way to prioritize which roles actually fit my background.

**Goal**: Zero-touch daily job discovery with AI-powered fit scoring.

---

## What It Does

- Searches **3 platforms** daily: 104 Job Bank (Taiwan), JSearch (Japan/Singapore/Hong Kong), MyCareersFuture (Singapore)
- **AI scores each job** 0–100 against a TPM/PM candidate profile
- Filters: foreign companies only, salary ≥ NT$50,000, no outsourcing, no citizenship-required roles
- Sends **2 daily emails**: Taiwan jobs + International jobs
- Sends **1 weekly market trends report** every Sunday: top skills, skill gaps, location breakdown

---

## System Flow

```
GitHub Actions (cron: daily 7:23 AM Taiwan)
         │
         ▼
┌─────────────────────────────────┐
│         Data Collection         │
│  104 Job Bank  ·  JSearch API   │
│  MyCareersFuture API            │
└────────────────┬────────────────┘
                 │ deduplicate
                 ▼
┌─────────────────────────────────┐
│         AI Scoring              │
│  Gemini 2.5 Flash Lite          │
│  · Role fit score (0–100)       │
│  · Tech vs PM classification    │
│  · Strengths / skill gaps       │
│  · Estimated salary             │
│  · Interview prep tips          │
│  · Visa / citizenship filter    │
└────────────────┬────────────────┘
                 │ score ≥ 70
                 ▼
┌─────────────────────────────────┐
│         Email Delivery          │
│  Gmail API (OAuth 2.0)          │
│  · 🇹🇼 Taiwan email             │
│  · 🌏 International email       │
└─────────────────────────────────┘
```

---

## Key Design Decisions

**Why Gemini 2.5 Flash Lite over GPT-4?**
Cost efficiency. At ~NT$0.025 per full scoring run, it's 20x cheaper than GPT-4 while delivering sufficient structured output quality for job scoring.

**Why GitHub Actions over a dedicated server?**
Zero infrastructure cost. The job runs once a day for ~2 minutes — spinning up a server for this would be wasteful. GitHub Actions free tier covers the usage comfortably.

**Why two separate emails instead of one?**
Different mental contexts. Taiwan jobs require different preparation than international roles. Combining them created cognitive overload during review.

**Why a 70-point score threshold?**
Calibrated through manual review of 50+ scored jobs. Below 70 consistently mapped to roles with 2+ disqualifying factors (wrong domain, citizenship required, outsourcing firm).

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Scheduling | GitHub Actions (cron) |
| Job Sources | 104 API · JSearch (RapidAPI) · MyCareersFuture API |
| AI Scoring | Google Gemini 2.5 Flash Lite |
| Email | Gmail API (OAuth 2.0) |
| Language | Python 3.11 |

---

## Results

- Reduced daily job search time from 30+ minutes to **0 minutes** (fully automated)
- Covers **4 markets** simultaneously: Taiwan, Japan, Singapore, Hong Kong
- Cost: ~**NT$2–5/month** in API fees
