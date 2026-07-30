# ARIA — Architecture Notes

## Deployment

- **Frontend + API:** Netlify (SPA + Serverless Functions)
- **AI:** Google Gemini API (`gemini-flash-latest`)
- **Storage:** Google Drive (recordings, CVs)
- **Live URL:** `jusjumpin-hr-interviews.netlify.app`

## Scoring pipeline

1. Candidate submits answers via the interview frontend
2. `POST /api/score` triggers a Netlify Function
3. Function builds a role-specific prompt with the answer + rubric
4. Gemini called with `responseMimeType: "application/json"` for structured output
5. Scores written to the candidate record
6. Manager dashboard reads scored records via `GET /api/candidates`

## Key design decisions

**Why async interviews?**  
Live scheduling across a 22-outlet chain with rotating shifts is a coordination nightmare. Async removes that entirely. Completion rates are higher when candidates choose their own time.

**Why Gemini Flash?**  
Speed and cost. Flash provides fast, cheap scoring with sufficient quality for structured rubric evaluation. Reserved for audio/video analysis tasks where the model needs multimodal capability.

**Why structured JSON output?**  
`responseMimeType: "application/json"` in the Gemini call forces a reliable schema. Free-text parsing is fragile in production pipelines — one unexpected format and the scorecard breaks.

**Why Google Drive for storage?**  
The organisation already uses Google Workspace. Drive gives durable links, built-in access control, and zero additional infrastructure cost.
