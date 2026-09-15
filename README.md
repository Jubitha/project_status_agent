# Intelligent Project Status Agent

Project 3C for the Mastering Agentic AI Week 3 project.

## Overview

The Intelligent Project Status Agent automatically retrieves sprint-task data, calculates delivery metrics, identifies blockers and risks, compares the current sprint with historical snapshots, answers project-status questions, and generates a stakeholder-ready weekly report.

This implementation follows the low-code track and uses n8n as the orchestration surface.

## Agent One-Liner

My agent helps program and project managers generate a weekly sprint-status report in n8n, replacing the manual process of collecting task updates, calculating progress, finding blockers, and comparing prior weeks. It performs this work autonomously using GitHub, mem0, and Nebius, hands the generated report to a human for review before publication, and succeeds when a manager can receive an accurate report with risk flags, blockers, and historical trends in under two minutes on at least 95% of runs.

## Features

- Retrieves Scrum task data from a GitHub-hosted CSV file
- Parses and normalizes sprint records
- Calculates completion, status, blocker, overdue, and assignment metrics
- Applies deterministic sprint-health rules
- Searches mem0 for previous weekly snapshots
- Detects tasks blocked across multiple reporting periods
- Uses Nebius to analyze risks and generate a Markdown report
- Saves the latest sprint snapshot to mem0
- Answers questions such as, "What tasks have been stuck for more than one sprint?"
- Returns a structured JSON response through an n8n webhook

## Architecture

```text
Webhook
  -> Download CSV from GitHub
  -> Parse CSV
  -> Process sprint data
  -> Search sprint history in mem0
  -> Prepare current and historical context
  -> Analyze sprint with Nebius
  -> Generate weekly report with Nebius
  -> Save weekly snapshot to mem0
  -> Format output
  -> Return JSON response
```

## Tools

| Tool | Purpose |
|---|---|
| n8n | Workflow orchestration and production webhook |
| GitHub | Hosts the Scrum task CSV, workflow export, report, and documentation |
| mem0 | Persistent week-over-week project memory |
| Nebius Token Factory | Sprint analysis and report generation |

## Project-Management Data Source

The project specification suggests Jira, Asana, or Notion. This implementation uses a structured Scrum-task CSV hosted on GitHub because a Jira account was unavailable. The CSV represents the same project fields needed by the agent, including task ID, status, assignee, priority, sprint, dates, blockers, dependencies, and tags.

A future version can replace the GitHub download node with a Jira integration while keeping the downstream analysis and memory workflow unchanged.

## Repository Structure

```text
project_status_agent/
├── README.md
├── data/
│   └── scrum_tasks_sprint_1.csv
├── workflows/
│   └── intelligent_project_status_agent.json
├── reports/
│   └── sample_weekly_report.md
└── docs/
    └── agent_framework.md
```

## Setup

1. Create an n8n workflow or import `workflows/intelligent_project_status_agent.json`.
2. Create an n8n Header Auth credential for mem0 using `Authorization: Token YOUR_MEM0_KEY`.
3. Create an n8n Header Auth credential for Nebius using `Authorization: Bearer YOUR_NEBIUS_KEY`.
4. Select the mem0 credential in both mem0 HTTP Request nodes.
5. Select the Nebius credential in both Nebius HTTP Request nodes.
6. Confirm that the GitHub HTTP Request node points to the raw CSV URL.
7. Publish the workflow.

API keys are stored in n8n credentials and are not included in the exported workflow.

## Example Production Request

```bash
curl -X POST 'https://YOUR-N8N-DOMAIN/webhook/sprint-report' \
  -H 'Content-Type: application/json' \
  -d '{"sprint":"Sprint 1","report_date":"2026-09-12","question":"What tasks have been stuck for more than one sprint?"}'
```

## Example Result

The successful production test analyzed 20 tasks, reported a 20% completion rate, identified two persistent blockers, compared two prior memory records, saved the new snapshot successfully, and generated both a detailed analysis and weekly status report.

See `reports/sample_weekly_report.md` for the stakeholder-ready output.

## Memory

Each successful run searches mem0 using the project user ID `3c-project-status-agent`. After report generation, the workflow saves a deterministic snapshot containing the report date, sprint, task totals, completion rate, status counts, blocked tasks, overdue count, unassigned count, and sprint health.

The production demonstration reached a history depth of three and correctly identified tasks that remained blocked across multiple snapshots.

## Human Review

The agent autonomously reads project data, analyzes risks, and drafts the report. A project manager reviews the returned report before it is published or shared externally. The workflow does not automatically modify project tasks or publish reports to third parties.

## Success Criteria

- The production webhook returns `success: true`.
- Current sprint metrics match the source CSV.
- mem0 returns relevant historical snapshots and stores the current snapshot.
- Persistent blockers are identified across reporting periods.
- A complete weekly status report is produced in under two minutes on at least 95% of runs.

## Limitations and Future Improvements

- Replace the GitHub CSV source with direct Jira authentication and issue retrieval.
- Add controlled retry and error-response branches for external API failures.
- Add an interactive approval interface before external publication.
- Add automated evaluation cases for metric accuracy and report completeness.

## Submission Artifacts

- Working n8n workflow export
- Representative Scrum task dataset
- Sample weekly status report
- Agent Framework documentation
- Demo recording
- Public GitHub repository
