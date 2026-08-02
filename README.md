# ARIA — Automated AI Interview System

> **Replace 40 recorded calls with one Gemini-scored summary. Hiring managers spend minutes, not hours.**

ARIA is a production AI interview platform built for a 25+-outlet entertainment chain's recruitment pipeline. Candidates record answers to structured questions in their own time. Gemini LLM evaluates every response against a scoring rubric and produces a concise scorecard — so hiring managers read a summary instead of sitting through hours of recordings.

Live deployment: `jusjumpin-hr-interviews.netlify.app`

---

## The problem it solves

Before ARIA, every candidate interview meant scheduling a live call, recording it, and then watching back 20–40 minutes of video — for every single applicant. With seasonal hiring across 25+ outlets, that's not a process, it's a bottleneck.

ARIA makes interviews asynchronous and AI-scored. Candidates do it when they're ready. Managers get a structured scorecard. The whole review cycle compresses from days to minutes.

---

## How it works

```
Candidate receives link
        ↓
Records video/audio answers (structured questions, per role)
        ↓
Responses uploaded → Netlify Functions
        ↓
Gemini LLM evaluates each answer against rubric
        ↓
Scorecard generated (per-question scores + summary + flag)
        ↓
Hiring manager reviews scorecard + recording link
        ↓
Shortlist / reject / schedule follow-up
```

---

## Key features

| Feature | Detail |
|---|---|
| **Async interviews** | Candidates self-schedule — no coordination overhead |
| **AI scoring** | Gemini LLM evaluates answers against structured rubrics per role |
| **Per-question scores** | Each answer scored individually with justification |
| **Manager dashboard** | All candidates in one view — score, summary, recording link |
| **Role-specific rubrics** | Different question sets and scoring criteria per job role |
| **Shortlist flags** | AI flags high-potential candidates automatically |
| **Recording storage** | Google Drive integration for durable recording links |

---

## Architecture

```
Netlify (Frontend SPA + Serverless Functions)
    │
    ├── /api/submit         — receives candidate responses
    ├── /api/score          — calls Gemini API, writes scorecard
    └── /api/candidates     — manager dashboard data endpoint
          │
          ├── Google Drive  — recording storage + CV uploads
          └── Gemini LLM    — structured scoring via generateContent
```

**Tech stack:** `JavaScript` · `Node.js` · `Netlify Functions` · `Gemini API` · `Google Drive API` · `HTML/CSS`

---

## AI scoring design

Each candidate answer is sent to Gemini with:
- The question text
- The role-specific rubric (what a strong answer looks like)
- Scoring dimensions (communication, role knowledge, enthusiasm, red flags)

Gemini returns structured JSON: `{ score: 0–10, justification: "...", flag: bool }` per question, plus an overall summary.

This means every candidate is evaluated against the same criteria, not the interviewer's mood on a given day.

---

## What I learned building it

**Async vs real-time:** Making interviews async removed the biggest scheduling bottleneck and actually increased candidate completion rates — they could record at 10pm instead of taking time off work.

**Rubric design matters more than the AI:** The quality of the scoring tracks almost perfectly with the quality of the rubric. Vague rubric → vague scores. Tight rubric → actionable scores. The AI is the evaluator, not the author of the criteria.

**Structured output is non-negotiable:** Using `responseMimeType: "application/json"` in the Gemini call forces structured output and eliminates parsing failures. Free-text AI responses are fine for chat; production scoring pipelines need reliable schema.

---

## Status

Currently live in production at `jusjumpin-hr-interviews.netlify.app`. Used for frontline hiring across multiple outlet locations.

**Tech:** `JavaScript` · `Node.js` · `Netlify` · `Gemini LLM` · `Google Drive API`
