# Agent Framework: Intelligent Project Status Agent

## Project

Project 3C - Intelligent Project Status Agent

## Build Track

Low-code track using n8n.

## The Primer: One-Liner

My agent helps program and project managers generate a weekly sprint-status report in n8n, replacing the manual process of collecting task updates, calculating progress, finding blockers, and comparing prior weeks, which can take hours each week. It performs this work autonomously using GitHub, mem0, and Nebius, hands the generated report to a human for review before publication, and succeeds when a manager can receive an accurate report with risk flags, blockers, and historical trends in under two minutes on at least 95% of runs.

## 1. Agent Goal

Automatically analyze current sprint-task data, identify blockers and delivery risks, compare results with previous sprint snapshots, answer project-status questions, and generate a weekly stakeholder-ready report.

## 2. Where Do People Use It?

Program and project managers use the agent through an n8n production webhook. The returned JSON contains calculated metrics, historical trends, risk analysis, and a Markdown weekly report.

## 3. What Steps Does It Take, in Order?

1. Receive the requested sprint, report date, and question through a webhook.
2. Download and parse the Scrum-task CSV from GitHub.
3. Normalize the task records and calculate deterministic sprint metrics.
4. Search mem0 for previous weekly sprint snapshots.
5. Combine current metrics with historical memory and identify persistent blockers.
6. Use Nebius to analyze sprint health, risks, blockers, and trends.
7. Use Nebius to generate the final weekly status report.
8. Save the current weekly snapshot to mem0.
9. Return the structured result through the webhook.

## 4. What Can It Actually Do?

- **Read:** Retrieve the Scrum-task CSV from GitHub.
- **Read:** Search prior project snapshots in mem0.
- **Analyze:** Calculate completion, status, blocker, overdue, and assignment metrics.
- **Analyze:** Detect tasks that remain blocked across multiple reporting periods.
- **Generate:** Produce sprint analysis and a stakeholder-ready weekly report with Nebius.
- **Write:** Save a deterministic weekly snapshot to mem0 for future trend analysis.

The agent does not automatically modify project tasks, send the report, or publish it externally.

## 5. What Does It Need to Remember?

The agent retains weekly sprint snapshots across sessions in mem0 under the project identifier `3c-project-status-agent`. Each snapshot contains the sprint and report date, task totals, completion rate, status counts, blocked-task details, overdue count, unassigned count, and sprint health.

## 6. What Should It Never Do?

The agent must never invent project facts, expose API keys, delete or modify source tasks, publish a report without human review, or use information outside the verified sprint data and relevant historical memory.

## 7. Human-in-the-Loop

The agent autonomously reads, calculates, analyzes, and drafts. A project manager reviews the returned weekly report before it is shared or published externally and can correct the source CSV before requesting a revised report.

## 8. What Happens When Something Breaks?

If GitHub, mem0, or Nebius returns an error, n8n stops the affected run and records the failed node and error details in its execution history. The manager checks the URL or credential, corrects the problem, and reruns the request; no external publication occurs during a failed run.

## 9. How Do You Know It Worked?

The workflow succeeds when it returns `success: true`, matches the source sprint metrics, retrieves relevant historical snapshots, identifies persistent blockers, stores the new snapshot, and produces a complete weekly report in under two minutes on at least 95% of runs.

## Demonstrated Result

The production test analyzed 20 tasks, calculated a 20% completion rate, found two persistent blocked tasks, compared two previous mem0 records, stored a third snapshot successfully, and generated a complete weekly status report answering which tasks had been stuck for more than one sprint.
