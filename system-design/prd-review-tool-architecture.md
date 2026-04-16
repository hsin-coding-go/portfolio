# Architecture: PRD Review Tool

## GitOps Flow

```
PM pushes file to incoming/
         │
         ▼
┌─────────────────────────────────────────────────────┐
│              GitHub Actions                          │
│         (trigger: push to incoming/**)              │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
              Create branch
              prd/<feature-name>
                       │
         ┌─────────────┴─────────────┐
         ▼                           ▼
   .docx / .pdf                    .md
   Convert to Markdown         Direct use
   (python-docx / PyMuPDF)
         │                           │
         └─────────────┬─────────────┘
                       ▼
              prd/<feature>.md
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│              AI Review (Gemini 2.5)                  │
│                                                     │
│  1. Completeness     ✅ / ⚠️ / ❌                   │
│  2. Clarity Issues                                  │
│  3. Technical Feasibility                           │
│  4. Edge Cases                                      │
│  5. Risks & Dependencies                            │
│  6. Recommendations (prioritized)                   │
│  7. Summary Score (1–5 per dimension)               │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
           reviews/<feature>-review.md
                       │
              ┌────────┴────────┐
              ▼                 ▼
         Open PR            Send Email
         to main            to pusher
              │                 │
              ▼                 ▼
     PM reviews AI         Missing items
     feedback in PR        table + PR link
              │
              ▼
     PM edits prd/*.md
     directly in PR
              │
              ▼
         Merge to main
         = PRD Approved ✅
```

## Branch Strategy

```
main
  │
  ├── prd/user-login-feature      ← auto-created per PRD
  ├── prd/notification-center     ← isolated, independent
  └── prd/search-filter           ← each has own PR
```

## Email Notification Structure

```
Subject: PRD Review Ready: <Feature Name> — Needs Work

┌─────────────────────────────────┐
│  Overall Assessment             │
│  Needs Work / Ready / Major Gap │
├─────────────────────────────────┤
│  [ Go to PR → ]                 │
├─────────────────────────────────┤
│  ⚠️ Missing Items Table          │
│  Section | Status | Notes       │
├─────────────────────────────────┤
│  🔍 Clarity Issues              │
├─────────────────────────────────┤
│  💡 Top Recommendations         │
└─────────────────────────────────┘
```

## Multi-User Routing

```
GitHub actor (who pushed)
         │
         ├── hsin-coding-go   ──► hsin78318@gmail.com
         ├── yuwenhuang3721   ──► yuwen.huang0401@gmail.com
         └── others           ──► fallback email
```
