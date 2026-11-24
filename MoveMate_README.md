# 🚚 MoveMate — AI-Powered Smart Relocation Assistant

> **One move. Multiple services. Zero coordination chaos.**

MoveMate is an AI-powered relocation assistant that helps people coordinate the tasks involved in moving from one home to another. It brings moving services, internet installation, and calendar appointments into one coordinated plan.

Using an AI agent and the **Model Context Protocol (MCP)**, MoveMate can interpret a relocation request, coordinate service tools, build a schedule, and propose changes when something goes wrong. A human-in-the-loop approval flow keeps the user in control of important actions.

> **Hackathon MVP note:** The current MVP is designed to use simulated service providers and mock booking operations. It does not make real-world bookings or imply partnerships with service providers.

---

## 📌 Table of Contents

- [The Problem](#-the-problem)
- [Our Solution](#-our-solution)
- [Key Features](#-key-features)
- [What Makes MoveMate Different](#-what-makes-movemate-different)
- [Example Workflow](#-example-workflow)
- [System Architecture](#️-system-architecture)
- [Technology Stack](#️-technology-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Environment Configuration](#-environment-configuration)
- [Running the Application](#-running-the-application)
- [Testing](#-testing)
- [Safety and Reliability](#-safety-and-reliability)
- [Current MVP Scope](#-current-mvp-scope)
- [Future Enhancements](#-future-enhancements)
- [Project Information](#-project-information)

---

## 🚧 The Problem

Moving to a new home involves coordinating several independent services, such as:

- Booking a moving company.
- Scheduling internet installation.
- Managing key handover and other appointments.
- Keeping all tasks within a workable timeline.

These activities are often managed across separate apps, calls, and calendars. A change to one booking can affect the rest of the schedule, leaving the user to identify conflicts and reorganize everything manually.

## 💡 Our Solution

MoveMate acts as an **intelligent coordination layer** across relocation services.

A user can describe their move in natural language. The AI agent extracts the important details and uses MCP-connected tools to gather service options. A Python scheduling engine checks task dependencies, time windows, and conflicts to create a coordinated plan.

If a provider cancels or a booking changes, MoveMate evaluates the impact and proposes a revised plan. The user reviews and approves proposed actions before simulated bookings are confirmed.

**Core principle: Code decides; AI explains.** The scheduling engine validates the plan, while the AI agent helps interpret requests and communicate the results.

---

## 🌟 Key Features

### 1. 🏠 Intelligent Relocation Planner

- Enter source and destination locations.
- Specify the moving date.
- Select the services needed for the move.
- Create a relocation plan from a natural-language request.

### 2. 🤖 AI-Powered Assistant

- Understands natural-language relocation requests.
- Extracts locations, dates, services, and user requirements.
- Identifies the tools needed for a request.
- Coordinates service tools through MCP.
- Explains the generated plan and proposed changes.

### 3. 🔌 MCP-Based Service Integration

MoveMate uses independent MCP servers to expose service-related tools to the AI agent.

| MCP server | Responsibilities |
|---|---|
| Moving MCP Server | Find simulated moving companies, check slots, estimate costs, and simulate bookings |
| Internet MCP Server | Find simulated internet providers, compare plans, and schedule installation |
| Calendar MCP Server | Check availability, manage events, and identify appointment conflicts |
| Disruption Simulator MCP Server | Simulate cancellations or delays to test the replanning workflow |

The Disruption Simulator is part of the proposed MVP architecture; include it in the running setup when that server has been implemented.

### 4. 📅 Smart Scheduling Engine

The Python scheduling engine:

- Organizes relocation activities into a timeline.
- Handles task dependencies.
- Checks time windows and overlapping appointments.
- Evaluates suitable time slots returned by service tools.
- Produces a valid schedule for the user to review.

### 5. 🔄 Dynamic Replanning

Dynamic Replanning is one of MoveMate's core innovations.

When a service provider cancels or delays a booking, MoveMate:

1. Identifies the affected task.
2. Finds other tasks that depend on it.
3. Checks alternative providers or time slots returned by the tools.
4. Re-evaluates scheduling conflicts and dependencies.
5. Generates a revised plan while aiming to minimize changes to the existing schedule.
6. Shows the proposed changes to the user for approval.

### 6. ✅ Human-in-the-Loop Approval

- Review proposed bookings and schedule changes.
- Approve or reject proposed actions.
- Keep simulated booking confirmations under user control.
- Maintain a record of important actions.

### 7. 📊 Relocation Dashboard

- View the relocation timeline and progress.
- Track pending tasks and simulated bookings.
- Monitor upcoming appointments.
- Review activity history and proposed changes.

---

## 🆚 What Makes MoveMate Different?

Traditional applications often focus on searching for or booking one service at a time. Calendar applications help organize events, but users still have to coordinate service dependencies and respond to disruptions themselves.

MoveMate focuses on **cross-service coordination and recovery when plans change**.

| Traditional approach | MoveMate |
|---|---|
| User coordinates separate services | One coordinated relocation plan |
| Individual service bookings | MCP-based service-tool coordination |
| Static schedule | Schedule can be recomputed after a disruption |
| User checks conflicts manually | Scheduling engine checks conflicts |
| User reorganizes tasks after cancellation | MoveMate proposes a revised plan |
| Actions may be spread across apps | Centralized dashboard and activity history |
| Limited control over automated actions | Human approval before simulated confirmations |

---

## 🔄 Example Workflow

**Example request**

> “I'm moving from Marathahalli to Whitefield on December 10. Arrange movers and internet installation.”

### Step 1 — Understand the request

The AI agent extracts the relocation details:

```json
{
  "source": "Marathahalli",
  "destination": "Whitefield",
  "moving_date": "2026-12-10",
  "services": [
    "moving",
    "internet_installation"
  ]
}
```

### Step 2 — Coordinate tools through MCP

The agent uses the Moving, Internet, and Calendar MCP servers to retrieve simulated service options and availability.

### Step 3 — Generate a schedule

The Python scheduling engine checks the returned time slots, task dependencies, and potential conflicts.

### Step 4 — Request user approval

MoveMate displays the proposed relocation plan. The user can review and approve or reject the proposed actions.

### Step 5 — Handle a disruption

If the moving company cancels, the Disruption Simulator can trigger the event. MoveMate identifies affected tasks, checks alternatives, and prepares a revised schedule with minimal disruption where possible.

### Step 6 — Update the dashboard

After approval, the simulated plan and booking states are updated, and the activity history records the change.

---

## 🏗️ System Architecture

```text
                         USER
                           |
                           v
                React + Tailwind Frontend
                           |
                           v
                    FastAPI Backend
                           |
                           v
                  AI AGENT (LLM)
                   + Tool Calling
                           |
                           v
                       MCP CLIENT
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          MOVING        INTERNET       CALENDAR
           MCP             MCP            MCP
          SERVER          SERVER         SERVER
             |             |             |
             +-------------+-------------+
                           |
                           v
                    SMART SCHEDULER
                 (Python constraint logic)
                           |
              +------------+------------+
              |                         |
              v                         v
        Conflict Detection       Dynamic Replanning
              |                         |
              +------------+------------+
                           |
                           v
                    APPROVAL CENTER
                           |
                           v
                     SQLITE DATABASE
                           |
                           v
                  UPDATED DASHBOARD

        DISRUPTION SIMULATOR MCP
          (triggers test events)
```

**Component responsibilities**

- **Frontend:** Collects relocation details and displays plans, approvals, and activity.
- **FastAPI backend:** Exposes application APIs and coordinates backend services.
- **AI agent:** Interprets requests, selects relevant tools, and explains results.
- **MCP client and servers:** Provide a standardized way for the agent to interact with service tools.
- **Python scheduler:** Validates task dependencies, time windows, and conflicts; evaluates revised schedules.
- **Approval center:** Ensures proposed actions are reviewed by the user.
- **SQLite database:** Stores application data such as relocation tasks, simulated bookings, approvals, and activity history.

---

## 🛠️ Technology Stack

| Component | Technology |
|---|---|
| Frontend | React.js |
| Styling | Tailwind CSS |
| Backend | Python, FastAPI |
| AI agent | LLM with tool calling |
| Agent communication | Model Context Protocol (MCP) |
| MCP implementation | Python MCP SDK |
| Database | SQLite |
| ORM | SQLAlchemy |
| Data validation | Pydantic |
| Scheduling | Python, NetworkX |
| API testing | Postman |
| Version control | Git and GitHub |

---

## 📁 Project Structure

The following is the intended project organization. Some folders or files may be added as implementation progresses.

```text
MoveMate/
│
├── frontend/
│   ├── public/
│   └── src/
│       ├── assets/
│       ├── components/
│       ├── pages/
│       ├── services/
│       ├── hooks/
│       ├── context/
│       ├── App.jsx
│       └── main.jsx
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── agent/
│   │   ├── mcp_client/
│   │   ├── scheduler/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── database/
│   │   ├── services/
│   │   └── main.py
│   └── requirements.txt
│
├── mcp_servers/
│   ├── moving_server/
│   ├── internet_server/
│   ├── calendar_server/
│   └── disruption_simulator/
│
├── data/
│   ├── mock_moving_companies.json
│   ├── mock_internet_providers.json
│   └── mock_slots.json
│
├── tests/
│   ├── test_agent.py
│   ├── test_scheduler.py
│   ├── test_mcp.py
│   └── test_replanning.py
│
├── docs/
│   └── architecture.md
│
├── README.md
├── .gitignore
└── docker-compose.yml
```

---

## 🚀 Getting Started

### Prerequisites

Install the following before running the project:

- [Python](https://www.python.org/downloads/) (a version supported by the project's dependencies).
- [Node.js and npm](https://nodejs.org/).
- [Git](https://git-scm.com/).
- An API key for the LLM provider, if the configured AI integration requires one.

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd MoveMate
```

Replace `<YOUR_GITHUB_REPOSITORY_URL>` with your repository's actual GitHub URL.

### 2. Set up the backend

Create and activate a virtual environment:

**Windows PowerShell**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

**Windows Command Prompt**

```bat
python -m venv .venv
.venv\Scripts\activate
```

**macOS / Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the Python dependencies:

```bash
pip install -r backend/requirements.txt
```

### 3. Set up the frontend

Open a second terminal:

```bash
cd frontend
npm install
```

### 4. Configure environment variables

Create a local `.env` file using the variable names expected by your backend configuration. See [Environment Configuration](#-environment-configuration) below.

### 5. Start the services

Start the backend and frontend using the commands in [Running the Application](#-running-the-application).

> **Note:** These commands assume the project follows the structure and entry points shown above. If your actual filenames, package scripts, or MCP server launch commands differ, update the commands to match the implemented code.

---

## 🔐 Environment Configuration

Keep API keys and other secrets out of source control.

Create a `.env.example` file containing the variable names your application expects, with placeholder values only. For example:

```env
# Add the exact variable names used by your implementation.
LLM_API_KEY=your_api_key_here
DATABASE_URL=sqlite:///./movemate.db
```

Copy the example to a local `.env` file and replace placeholders with your own values. Do not commit `.env` or real API keys.

The names above are examples; they must match the configuration read by your code. Add any backend URL, model name, or MCP server settings required by your implementation.

---

## ▶️ Running the Application

### Backend

From the repository root, activate the virtual environment and run:

```bash
uvicorn app.main:app --reload --app-dir backend
```

If your FastAPI application is exposed from a different module path, adjust the command accordingly.

### Frontend

In a separate terminal:

```bash
cd frontend
npm run dev
```

Open the local URL printed by the frontend development server in your browser.

### MCP servers

Start the Moving, Internet, Calendar, and Disruption Simulator MCP servers using the launch commands configured in your MCP client. The exact commands depend on how each server is implemented.

---

## 🧪 Testing

Run the tests from the repository root after installing the backend dependencies:

```bash
pytest
```

Suggested areas to test:

- Request parsing and extraction of relocation details.
- MCP tool responses and error handling.
- Task dependencies and time-window validation.
- Detection of overlapping appointments.
- Replanning after a simulated cancellation.
- User approval and rejection flows.
- Preservation of the previous confirmed plan when a new plan is only proposed.

Only report test counts, coverage, or performance metrics after running the tests and recording the actual results.

---

## 🔐 Safety and Reliability

- User approval is required before confirming proposed simulated bookings.
- The hackathon MVP uses simulated service providers and mock booking operations.
- No real-world reservations or provider partnerships are implied.
- A revised schedule is presented for review before it replaces the confirmed plan.
- Important agent actions and approval decisions should be recorded in the activity history.
- API keys and sensitive configuration belong in environment variables, not in source code.

---

## 🎯 Current MVP Scope

The MVP is intended to demonstrate one complete relocation workflow:

1. Accept a natural-language relocation request.
2. Extract the move details and required services.
3. Retrieve simulated options through MCP tools.
4. Generate a schedule using the Python scheduling engine.
5. Simulate a provider cancellation or delay.
6. Detect affected tasks and generate a revised plan.
7. Ask the user to approve or reject the proposed changes.
8. Update the simulated plan and activity history after approval.

The focus is on demonstrating reliable coordination and dynamic replanning—not on making real bookings.

---

## 🔮 Future Enhancements

- Integration with real moving companies and internet providers.
- Google Calendar synchronization.
- Email notifications and reminders.
- Multilingual AI assistant.
- Real-time moving service tracking.
- Budget optimization and cost comparison.
- Document and utility transfer assistance.
- Mobile application support.
- Predictive delay detection.

---

## 📌 Project Information

| Field | Details |
|---|---|
| Project name | MoveMate |
| Category | AI Agents / MCP / Smart Automation |
| Tagline | One Move. Multiple Services. Zero Coordination Chaos. |
| Development status | Hackathon MVP — In Development |
| Institution | New Horizon College of Engineering |
| Event | wHACKiest 2026 |
| Track | Make MCP Matter |

