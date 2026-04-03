# Lab 8 — Report

Paste your checkpoint evidence below. Add screenshots as image files in the repo and reference them with `![description](path)`.

## Task 1A — Bare agent

### Prompt 1: "What is the agentic loop?"

> The agentic loop is the iterative cycle that allows an AI agent to autonomously accomplish tasks. Instead of just generating a single response, the agent repeatedly reasons, acts, and observes until a goal is met.
>
> Here's the core loop:
>
>  1 👁️ Perceive: The agent receives input (user prompt, tool output, or environment state).
>  2 🧠 Think: The LLM analyzes the context, plans the next step, and decides which tool to use (if any).
>  3 ⚡ Act: The agent executes the chosen action (e.g., runs a command, searches the web, edits a file).
>  4 🔄 Observe: The agent reads the result of the action. Did it work? Is there an error? Is the task done?
>  5 🔁 Repeat: If the goal isn't reached, the loop continues with the new observations.
>
> Example: "What's the weather in Tokyo?"
>
>  1 Perceive: You ask for Tokyo weather.
>  2 Think: I need to call the weather skill.
>  3 Act: I execute the weather tool.
>  4 Observe: I get back "22°C, Sunny".
>  5 Think: I have the answer. No more tools needed.
>  6 Act: I reply to you with the weather.
>
> I'm running this exact loop right now to process your message and generate this response!

### Prompt 2: "What labs are available in our LMS?"

> I don't have direct access to your LMS (Learning Management System) or any pre-configured connection to one. Let me check if there's any context in your workspace or memory about this.
>
> I don't have any information about your LMS or available labs in my memory or workspace. I also don't have a direct connection to any Learning Management System.
>
> To help you, I'd need a bit more info:
>
>  1 Which LMS are you using? (e.g., Moodle, Canvas, Blackboard, a custom platform?)
>  2 Is there a URL I could fetch to check available labs?
>  3 Do you have API access or credentials I could use?
>
> If you can share a URL or point me to where the lab info lives, I can try to fetch and summarize it for you!

**Observation:** The bare agent has no tools configured, so it cannot query the LMS. It correctly acknowledges this limitation and asks for more context — confirming the agent runs without tool access.

## Task 1B — Agent with LMS tools

### Prompt 1: "What labs are available?"

> Here are the 8 labs currently available in the LMS:
>
>  1 Lab 01 – Products, Architecture & Roles
>  2 Lab 02 — Run, Fix, and Deploy a Backend Service
>  3 Lab 03 — Backend API: Explore, Debug, Implement, Deploy
>  4 Lab 04 — Testing, Front-end, and AI Agents
>  5 Lab 05 — Data Pipeline and Analytics Dashboard
>  6 Lab 06 — Build Your Own Agent
>  7 Lab 07 — Build a Client with an AI Coding Agent
>  8 Lab 08
>
> Would you like details on any specific lab?

### Prompt 2: "Describe the architecture of the LMS system"

> Let me explore the codebase to understand the LMS architecture.
>
> Here's the architecture of the LMS system:
>
> **System Architecture**
>
> ```
>  ┌─────────────────────────────────────────────────────────────┐
>  │                        Caddy (Reverse Proxy)                 │
>  │              Single entry point — routes all traffic          │
>  └──────────┬──────────┬──────────┬──────────┬──────────────────┘
>             │          │          │          │
>       /items*     /docs,openapi  /utils/*   / (root)
>       /learners*  /interactions* /pgadmin   React SPA
>       /pipeline*  /analytics*    /victorialogs
>                                  /victoriatraces
>             │          │          │          │
>      ┌──────▼──────┐ ┌─▼──────┐ ┌─▼────────▼──────────┐
>      │  LMS Backend │ │ pgAdmin│ │ VictoriaLogs        │
>      │  (FastAPI)   │ │        │ │ VictoriaTraces      │
>      └──────┬──────┘ └────────┘ │ OTel Collector      │
>             │                   └─────────────────────┘
>      ┌──────▼──────┐
>      │  PostgreSQL  │
>      │  (LMS DB)    │
>      └─────────────┘
> ```
>
> **Core Components**
>
> 1. **Caddy** — Reverse Proxy / Gateway: Single entry point, routes API to FastAPI, serves React SPA, protects admin UIs
> 2. **LMS Backend (FastAPI / Python)**: REST API for items, learners, interactions, pipeline, analytics. Exports telemetry via OpenTelemetry
> 3. **PostgreSQL**: Stores all LMS data (labs, learners, submissions, scores)
> 4. **React Web Client**: Built SPA served by Caddy as the default dashboard
> 5. **Observability Stack**: OTel Collector → VictoriaLogs (logs) + VictoriaTraces (traces), 7-day retention
> 6. **Qwen Code API**: LLM gateway proxy with auth, retries, rate limiting
> 7. **Nanobot Agent**: AI agent with MCP tools, WebSocket chat channel, optional Flutter/Telegram clients
>
> All services run on a single Docker network (lms-network), enabling service-to-service communication by container name.

**Observation:** With the MCP server wired up, the agent returns real lab names from the LMS backend and can describe the full system architecture by combining MCP tool results with workspace exploration.

## Task 1C — Skill prompt

### Prompt: "Show me the scores" (without specifying a lab)

> I'll check the LMS to show you the scores. Let me start by seeing what labs are available.
>
> There are 8 labs. Let me get the pass rates and completion rates for each one.
>
> Here's a summary of the LMS scores across all labs:
>
> **📊 Lab Completion Rates**
>
> | Lab    | Title                                           | Completion | Passed / Total |
> |--------|-------------------------------------------------|------------|----------------|
> | Lab 01 | Products, Architecture & Roles                  | 96.4%      | 186 / 193      |
> | Lab 02 | Run, Fix, and Deploy a Backend Service          | 90.3%      | 112 / 124      |
> | Lab 03 | Backend API: Explore, Debug, Implement, Deploy  | 76.9%      | 10 / 13        |
> | Lab 04 | Testing, Front-end, and AI Agents               | 0.0%       | 0 / 0          |
> | Lab 05 | Data Pipeline and Analytics Dashboard           | 0.0%       | 0 / 0          |
> | Lab 06 | Build Your Own Agent                            | 0.0%       | 0 / 0          |
> | Lab 07 | Build a Client with an AI Coding Agent          | 0.0%       | 0 / 0          |
> | Lab 08 | lab-08                                          | 0.0%       | 0 / 0          |
>
> **📝 Task-Level Average Scores**
>
> Lab 01:
>
> | Task                                        | Avg Score | Attempts |
> |---------------------------------------------|-----------|----------|
> | Lab setup                                   | 97.2      | 335      |
> | Task 0: Practice the Git workflow           | 65.3      | 282      |
> | Task 1: Product & architecture description  | 55.6      | 146      |
> | Task 2: Roles and skills mapping            | 50.7      | 76       |
> | Optional: Resolve a merge conflict          | 37.5      | 12       |
> | Optional: Create a release                  | 60.0      | 5        |
> | Optional: Add a CI check                    | 42.9      | 7        |
> | Optional: Plan skill development            | 25.0      | 4        |
>
> Lab 02:
>
> | Task                                            | Avg Score | Attempts |
> |-------------------------------------------------|-----------|----------|
> | Task 2: Identify, report, and fix a bug         | 68.5      | 181      |
> | Task 1: Run the web server                      | 68.4      | 182      |
> | Task 4: Deploy the web server to the VM         | 56.8      | 69       |
> | Optional 3: Implement the post-order traversal  | 70.0      | 5        |
> | Optional 1: Implement the /outcomes endpoint    | 33.3      | 6        |
> | Optional 2: Make your VM a proxy                | 0.0       | 3        |
>
> Lab 03:
>
> | Task                                                | Avg Score | Attempts |
> |-----------------------------------------------------|-----------|----------|
> | Task 1: Explore the API                             | 60.5      | 26       |
> | Task 2: Enable and debug the interactions endpoint  | 58.8      | 16       |
> | Optional 2: Set up CI with GitHub Actions           | 0.0       | 1        |
>
> **Key takeaways:**
>
> - Lab 01 has the most engagement (193 students) and highest completion (96.4%)
> - Lab 02 also has strong participation (124 students, 90.3% completion)
> - Lab 03 has fewer students (13) but still decent completion at 76.9%
> - Labs 04–08 have no submissions yet — they may not be released or active yet
>
> Want me to dive deeper into any specific lab (e.g., top learners, group performance, or submission timeline)?

**Observation:** With the skill prompt in place, the agent first listed available labs, then fetched and formatted scores as tables with percentages and counts — following the skill's formatting rules. It also provided key takeaways and offered follow-up actions (top learners, group performance, timeline).

## Task 2A — Deployed agent

nanobot-1  | ✓ Heartbeat: every 1800s
nanobot-1  | MCP server 'lms': connected, 9 tools registered
nanobot-1  | Agent loop started

## Task 2B — Web client

<!-- Screenshot of a conversation with the agent in the Flutter web app -->

## Task 3A — Structured logging

<!-- Paste happy-path and error-path log excerpts, VictoriaLogs query screenshot -->

## Task 3B — Traces

<!-- Screenshots: healthy trace span hierarchy, error trace -->

## Task 3C — Observability MCP tools

<!-- Paste agent responses to "any errors in the last hour?" under normal and failure conditions -->

## Task 4A — Multi-step investigation

## What Went Wrong - Error Analysis
Based on the logs, here's what happened:
### Root Cause: **Database Connection Failure**
The LMS backend cannot connect to its PostgreSQL database. The error is:
```
socket.gaierror: [Errno -2] Name or service not known
```
This is a **DNS resolution failure** - the backend service cannot resolve the database hostname.
### Timeline of Events
| Time (UTC) | Event | Status |
|------------|-------|--------|
| 12:20:41 | GET /items/ | ❌ Failed - DNS error on DB connection |
| 12:23:40-41 | GET /docs, /openapi.json | ✅ Success (no DB needed) |
| 16:48:21 | GET /items/ | ✅ Success (200) - DB was reachable |
| 16:50:52 | GET /items/ | ❌ Failed (404) - "connection is closed" |
| 16:51:05-14 | POST /pipeline/sync | ❌ **Failed** - DNS resolution error |
### Error Details
**Most Recent Failure (16:51:14):**
- **Endpoint:** `POST /pipeline/sync`
- **Error:** `gaierror: [Errno -2] Name or service not known`
- **Location:** `asyncpg` connection attempt to PostgreSQL
- **Trace ID:** `3e8c84cef60fa2831de4de3b0adc490d`
**Earlier Failure (16:50:52):**
- **Endpoint:** `GET /items/`
- **Error:** `InterfaceError: connection is closed`
- **Trace ID:** `45f8b96c8ea80e2e7baef6e6712c2b15`
### Summary
1. **The database connection is unstable** - it worked at 16:48 but failed by 16:50
2. **DNS resolution is failing** - the PostgreSQL hostname cannot be resolved
3. **The sync pipeline fails** because it cannot write to the database
4. **API endpoints return 404** when DB queries fail
### Likely Causes
- PostgreSQL service is down or restarting
- Network connectivity issue between backend and database
- Database hostname configuration is incorrect or changed
- DNS service issues in the container network
The backend service itself is running (it responds to requests), but it cannot reach its database dependency.

## Task 4B — Proactive health check

**Time:** 2026-03-29 16:56 UTC

| Check | Status | Details |
|-------|--------|---------|
| Backend Errors (last 2 min) | ✅ OK | 0 errors |
| LMS Backend Health | ⚠️ Unhealthy | HTTP 404 |
| Recent Error Logs | ✅ Clean | No errors found |

**Summary:**
- No backend errors detected in the last 2 minutes
- However, the LMS health endpoint is returning HTTP 404, which suggests the LMS service may be unreachable or misconfigured
- No error logs found to indicate what's causing the 404

**Recommendation:** The LMS backend health check failure (HTTP 404) warrants investigation. This could indicate:
- LMS service is down
- Health endpoint path is incorrect
- Network/routing issue between services

**Time:** 2026-03-29 16:59 UTC

| Check | Status | Details |
|-------|--------|---------|
| Backend Errors (last 2 min) | ✅ OK | 0 errors |
| LMS Backend Health | ⚠️ Unhealthy | Service unreachable |
| Recent Logs | ⚠️ Empty | No logs found |

**Summary:**
- No backend errors in the last 2 minutes
- LMS backend remains unhealthy (no specific error message this time)
- No logs available from the backend service

**Status:** The LMS backend appears to be down or disconnected. This is the second consecutive health check showing the same issue.

The LMS system is still **unhealthy** (HTTP 404 error), and the request to fetch learners timed out. This is consistent with the database connectivity issues we identified earlier.

**Current Status:**
- The backend cannot reliably connect to its PostgreSQL database
- API endpoints that require database access (like `/learners`) are timing out or failing
- The health endpoint returns 404

Until the database connection issue is resolved, I won't be able to retrieve the list of registered learners. The DNS resolution failure (`socket.gaierror: [Errno -2] Name or service not known`) needs to be fixed first.

Would you like me to continue monitoring the health status, or is there something else I can help with?



## Task 4C — Bug fix and recovery

## What Went Wrong - Latest Analysis

### Root Cause: **Database DNS Resolution Failure** (Ongoing)

The same issue persists - the LMS backend cannot resolve the PostgreSQL database hostname.

### Recent Errors

| Time (UTC) | Endpoint | Error |
|------------|----------|-------|
| **16:57:27** | `GET /learners/` | `socket.gaierror: [Errno -2] Name or service not known` |
| **16:51:14** | `POST /pipeline/sync` | `socket.gaierror: [Errno -2] Name or service not known` |

### Error Details

**Most Recent Failure (16:57:27):**
- **Endpoint:** `GET /learners/` (this is why your earlier learners query timed out)
- **Error:** `gaierror: [Errno -2] Name or service not known`
- **Location:** `asyncpg` attempting to connect to PostgreSQL
- **Trace ID:** `52d191a2335528e13e28f8a3497ac377`

### Current Status (17:05)

| Check | Status |
|-------|--------|
| Health endpoint | ⚠️ Unhealthy |
| Errors (last 10 min) | 2 errors (both DNS-related) |
| Errors (last 2 min) | ✅ 0 errors |

### Summary

The **PostgreSQL database hostname cannot be resolved** by the backend service. This is a infrastructure/networking issue:

1. The backend service is running and responding to requests
2. But it cannot connect to its database dependency
3. Any endpoint requiring database access fails (`/learners`, `/items`, `/pipeline/sync`)
4. No new errors in the last 2 minutes, but the underlying issue is not resolved

**Fix needed:** The database service needs to be running and reachable, or the database hostname configuration needs to be corrected in the backend's environment variables.
