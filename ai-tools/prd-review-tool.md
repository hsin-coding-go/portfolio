# PRD Review Tool

> GitOps-based pipeline that automatically reviews Product Requirements Documents using AI and opens a Pull Request with structured feedback.

---

## Problem

PRD quality is inconsistent across teams. Missing edge cases, undefined success metrics, and vague requirements only surface late — after engineering has already started.

Manual peer reviews are slow and depend on reviewer availability.

**Goal**: Instant AI-powered PRD review on every upload, with human sign-off preserved via PR workflow.

---

## What It Does

1. PM uploads a `.docx`, `.pdf`, or `.md` file to `incoming/`
2. GitHub Actions detects the new file and triggers the pipeline
3. File is converted to Markdown (if needed)
4. Gemini AI reviews the PRD across 7 dimensions
5. A Pull Request is automatically opened with the review report
6. An email notification is sent to the uploader with key findings
7. PM reviews AI feedback, edits the PRD in the PR, then merges when ready

---

## System Flow

```
PM pushes file to incoming/
         │
         ▼
GitHub Actions detects change
         │
         ▼
Create branch: prd/<feature-name>
         │
         ├── Convert to Markdown
         │   .docx → python-docx
         │   .pdf  → PyMuPDF
         │   .md   → direct
         │
         ├── AI Review (Gemini 2.5 Flash Lite)
         │   · Completeness
         │   · Clarity
         │   · Technical Feasibility
         │   · Edge Cases
         │   · Risks & Dependencies
         │   · Recommendations
         │   · Summary Score (1–5)
         │
         ├── Commit outputs to branch
         │   prd/<feature>.md
         │   reviews/<feature>-review.md
         │
         ├── Open PR → main
         │
         └── Send email notification
             · Missing items table
             · Top recommendations
             · Direct link to PR
                  │
                  ▼
         PM reviews, edits, merges ✅
         (main = approved PRD)
```

---

## Key Design Decisions

**Why GitOps instead of a web app?**
PMs already use GitHub for code reviews. Keeping PRDs in the same workflow means no new tool to learn, and version history is preserved automatically.

**Why keep human-in-the-loop via PR?**
AI review surfaces gaps — but business context, stakeholder constraints, and strategic priorities require human judgment. The PR is the contract: AI proposes, PM decides.

**Why auto-create a branch per PRD?**
Isolation. Each PRD review is independent. Merging to `main` = explicit approval signal. Branch history preserves the full review trail.

**Why email notification with findings summary?**
Reduce friction. PMs shouldn't have to check GitHub to know if review is done. The email surfaces the 3 most critical gaps so they can act immediately.

**Why support .docx and .pdf?**
PMs write PRDs in Word and sometimes share PDFs. Requiring Markdown-only would create an adoption barrier. The tool meets PMs where they already work.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Trigger | GitHub Actions (push event) |
| File Conversion | python-docx · PyMuPDF |
| AI Review | Google Gemini 2.5 Flash Lite |
| PR Automation | GitHub CLI (`gh pr create`) |
| Email | Gmail API (OAuth 2.0) |
| Language | Python 3.11 |

---

## Results

- PRD review time: from **days → minutes**
- Supports 3 input formats: `.md`, `.docx`, `.pdf`
- Full audit trail preserved in Git history
- Cost: ~**NT$0.025 per review**
