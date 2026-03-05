# ARCHITECTURE — [YOUR PROJECT NAME]

## Tech Stack
| Component | Solution | Phase | Rationale |
|-----------|---------|-------|-----------|
| Frontend | [e.g. Next.js 14] | [Phase] | [Why] |
| Database | [e.g. PostgreSQL/Supabase] | [Phase] | [Why] |
| Search | [e.g. Postgres FTS] | [Phase] | [Why] |
| Styling | [e.g. Tailwind + shadcn/ui] | [Phase] | [Why] |
| AI Hub | [e.g. OpenRouter] | [Phase] | [Why] |
| Workers | [e.g. Python + httpx] | [Phase] | [Why] |
| Deploy | [e.g. Vercel] | [Phase] | [Why] |

## Architecture Diagram
```
[Draw ASCII diagram here]
```

## URL Structure (if web project)
```
[Define your URL hierarchy]
```

## Data Pipeline
```
[Describe how data flows from source to UI]
```

## Deployment
[Describe where things run: Frontend, Backend, Workers, Database]

## ISR / Caching Strategy (if applicable)
| Page Type | Strategy | Revalidate |
|-----------|----------|------------|
| Home | ISR | 3600s |
| ... | ... | ... |

## Critical Free-Tier Limits
| Service | Limit | Upgrade trigger |
|---------|-------|-----------------|
| Supabase | 500 MB | 60% usage |
| ... | ... | ... |

## Security Model
[Define your layers: Edge, App, DB, Monitoring]
