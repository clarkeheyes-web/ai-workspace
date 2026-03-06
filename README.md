# ai-workspace

A centralized context store for all of Clarke's projects. Every folder is a project, and every project has a HANDOFF.md that any AI tool (Claude, Codex, etc.) can read to immediately get up to speed.

## Purpose

No more re-explaining context every session. Point any AI at the relevant HANDOFF.md and it knows the codebase, what has been built, and what is pending.

## Structure

```
ai-workspace/
  solar-dashboard/
    HANDOFF.md    # Solar energy monitoring dashboard (SolarEdge + Duke Energy)
  [future-project]/
    HANDOFF.md
```

## Projects

### solar-dashboard
A residential solar monitoring dashboard built on Vercel, pulling live data from the SolarEdge Monitoring API for a 11.75 kW system in Raleigh, NC.
- Live: https://solar-dashboard-five.vercel.app
- Repo: clarkeheyes-web/solar-dashboard (private)
- See: [solar-dashboard/HANDOFF.md](./solar-dashboard/HANDOFF.md)
