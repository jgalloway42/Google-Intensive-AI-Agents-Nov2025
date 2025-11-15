# Google 5-Day AI Agents Course (Kaggle)

A comprehensive guide to building production-ready AI agents using Google's Agent Development Kit (ADK).

## Course Overview

This repository contains materials from the Google/Kaggle 5-day intensive course on AI agents. The course progresses from basic agent concepts to production deployment on Google Cloud.

**Learning Path:** Basic Agents → Advanced Tools → State Management → Quality Assurance → Production Deployment

---

## Day-by-Day Content

### [Day 1: Introduction to AI Agents](Day%201/)

**Core Concept:** From static LLM responses to action-taking agents

**Files:**
- [Day_1_v4_Induction_to_Agents.pdf](Day%201/Day_1_v4_Induction_to_Agents.pdf) - Course introduction and concepts
- [day-1a-from-prompt-to-action.ipynb](Day%201/day-1a-from-prompt-to-action.ipynb) - Hands-on notebook

**What You'll Learn:**
- **Agent vs LLM difference:** Agents can reason → act → observe → respond (not just text output)
- **Setting up ADK:** Install `google-adk`, configure Gemini API
- **Your first agent:** Build a simple assistant with Google Search tool
- **ADK Web UI:** Interactive testing and debugging interface
- **Runner pattern:** Use `InMemoryRunner` for agent orchestration

**Key Code Pattern:**
```python
root_agent = Agent(
    name="helpful_assistant",
    model=Gemini(model="gemini-2.5-flash-lite"),
    instruction="You are a helpful assistant.",
    tools=[google_search]
)
```

---

### [Day 2: Agent Tools & Model Context Protocol](Day%202/)

**Core Concept:** Building custom tools and integrating external services

**Files:**
- [Agent Tools & Interoperability with MCP.pdf](Day%202/Agent%20Tools%20&%20Interoperability%20with%20Model%20Context%20Protocol%20%28MCP%29.pdf)
- [day-2a-agent-tools.ipynb](Day%202/day-2a-agent-tools.ipynb) - Custom function tools
- [day-2b-agent-tools-best-practices.ipynb](Day%202/day-2b-agent-tools-best-practices.ipynb) - MCP & long-running operations

**What You'll Learn:**

**From 2a:**
- **Custom function tools:** Turn Python functions into agent tools
- **Agent-as-tool pattern:** Use specialist agents as tools for delegation
- **Tool best practices:** Type hints, docstrings, structured returns
- **Complete tool taxonomy:** Function, Agent, MCP, OpenAPI, Built-in tools

**From 2b:**
- **MCP integration:** Connect external services (file systems, databases, APIs)
- **Long-running operations:** Human-in-the-loop approval workflows
- **Tool context:** Access to session state and confirmation requests

**Key Code Patterns:**
```python
# Agent as Tool
calculation_agent = LlmAgent(tools=[BuiltInCodeExecutor()])
currency_agent = LlmAgent(tools=[AgentTool(agent=calculation_agent)])

# Long-running with approval
def place_order(num_containers: int, tool_context: ToolContext):
    if num_containers > THRESHOLD:
        tool_context.request_confirmation(hint="Large order - approve?")
```

---

### [Day 3: Context Engineering - Sessions & Memory](Day%203/)

**Core Concept:** Conversation continuity and long-term memory

**Files:**
- [Context Engineering_ Sessions & Memory.pdf](Day%203/Context%20Engineering_%20Sessions%20&%20Memory.pdf)
- [day-3a-agent-sessions.ipynb](Day%203/day-3a-agent-sessions.ipynb) - Session management
- [day-3b-agent-memory.ipynb](Day%203/day-3b-agent-memory.ipynb) - Persistent memory

**What You'll Learn:**

**From 3a (Sessions):**
- **Session architecture:** Events (conversation history) + State (shared data)
- **Persistence:** InMemory → Database (SQLite) → Agent Engine (production)
- **Context compaction:** Automatic summarization to reduce token usage
- **Session state:** Cross-turn data storage with `tool_context.state`

**From 3b (Memory):**
- **Session vs Memory:** Short-term (one conversation) vs Long-term (all conversations)
- **Memory tools:** `load_memory` (reactive) vs `preload_memory` (proactive)
- **Auto-save pattern:** Callbacks to persist sessions to memory
- **Memory consolidation:** LLM extracts key facts from verbose conversation history

**Key Distinctions:**
| Feature | Session | Memory |
|---------|---------|--------|
| Scope | Current conversation | All conversations |
| Lifespan | Until session ends | Persistent |
| Use case | "What did I just say?" | "What's my favorite color from last month?" |

---

### [Day 4: Agent Quality - Observability & Evaluation](Day%204/)

**Core Concept:** Debugging and systematic testing

**Files:**
- [Agent Quality.pdf](Day%204/Agent%20Quality.pdf)
- [day-4a-agent-observability.ipynb](Day%204/day-4a-agent-observability.ipynb) - Logging and debugging
- [day-4b-agent-evaluation.ipynb](Day%204/day-4b-agent-evaluation.ipynb) - Testing and evaluation

**What You'll Learn:**

**From 4a (Observability):**
- **Three pillars:** Logs (what happened) + Traces (execution flow) + Metrics (statistics)
- **ADK Web UI:** Debug with events timeline, trace view, function call inspection
- **Plugins & Callbacks:** Production logging with `LoggingPlugin()` or custom callbacks
- **Callback types:** before/after agent, tool, model + error handling

**From 4b (Evaluation):**
- **Why evaluate:** Agents are non-deterministic; small changes → big impacts
- **Test cases:** JSON format with inputs, expected outputs, expected tool calls
- **Metrics:** Response match score (text similarity) + Tool trajectory score (correct tools)
- **User simulation:** LLM-generated dynamic test scenarios

**Evaluation Workflow:**
```bash
# Create test cases in *.evalset.json
# Define criteria in test_config.json
adk eval my_agent integration.evalset.json --config_file_path=test_config.json
```

---

### [Day 5: Prototype to Production](Day%205/)

**Core Concept:** Multi-agent systems and cloud deployment

**Files:**
- [Prototype to Production.pdf](Day%205/Prototype%20to%20Production.pdf)
- [day-5a-agent2agent-communication.ipynb](Day%205/day-5a-agent2agent-communication.ipynb) - A2A protocol
- [day-5b-agent-deployment.ipynb](Day%205/day-5b-agent-deployment.ipynb) - Vertex AI deployment

**What You'll Learn:**

**From 5a (Agent2Agent):**
- **A2A protocol:** Standard for cross-framework, cross-language, cross-org agent communication
- **When to use A2A:** External services, different teams, network-based
- **Agent cards:** JSON documents describing agent capabilities at `/.well-known/agent-card.json`
- **Architecture patterns:** Cross-framework, cross-language, cross-organization

**A2A vs Sub-Agents Decision:**
| Use A2A | Use Sub-Agents |
|---------|----------------|
| External service | Same codebase |
| Different org/team | Your team |
| Network-based | Same process |
| Cross-language needed | Same language |

**From 5b (Deployment):**
- **Vertex AI Agent Engine:** Fully managed, serverless agent hosting
- **Prerequisites:** GCP account, billing enabled, APIs activated
- **Deployment config:** CPU, memory limits, auto-scaling (min/max instances)
- **Memory Bank:** Production long-term memory with semantic search
- **Testing deployed agents:** Stream queries with `user_id` for personalization

**Deployment Command:**
```bash
adk deploy agent_engine \
    --project=PROJECT_ID \
    --region=us-west1 \
    sample_agent \
    --agent_engine_config_file=.agent_engine_config.json
```

---

## Core Technologies

### Google Agent Development Kit (ADK)

**Key Components:**
- **Agents:** `Agent`, `LlmAgent`, `RemoteA2aAgent`
- **Models:** Gemini 2.5 Flash (fast, cost-effective)
- **Tools:** Function, Built-in, MCP, Agent, OpenAPI
- **Session Services:** InMemory, Database, Agent Engine
- **Memory Services:** InMemory, Vertex AI Memory Bank
- **Runners:** InMemory, Production with sessions/memory

### Design Patterns

1. **Agent-as-Tool:** Delegate to specialist agents
2. **Human-in-the-Loop:** Request confirmation for critical operations
3. **Callbacks & Plugins:** Hook into agent lifecycle for logging/validation
4. **Context Compaction:** Automatic summarization to reduce costs
5. **Memory Consolidation:** LLM extracts key facts from conversation history

---

## Tool Taxonomy Reference

### Custom Tools
- **Function Tools:** Python functions → agent tools
- **Long-Running Function Tools:** Async operations with human approval
- **Agent Tools:** Other agents as tools (delegation)
- **MCP Tools:** External integrations (file systems, databases)
- **OpenAPI Tools:** Auto-generated from API specs

### Built-in Tools
- **Gemini Tools:** `google_search`, `BuiltInCodeExecutor`
- **Google Cloud Tools:** BigQuery, Spanner, API Hub
- **Third-party Tools:** Hugging Face, Firecrawl, GitHub

---

## Skills Progression

By the end of this course, you can:

- ✅ Create agents with tools and instructions
- ✅ Build custom function tools with best practices
- ✅ Integrate external services via MCP
- ✅ Implement human-in-the-loop workflows
- ✅ Manage conversation state and long-term memory
- ✅ Debug agents with logs, traces, and Web UI
- ✅ Evaluate agent performance systematically
- ✅ Deploy agents to Google Cloud (Vertex AI)
- ✅ Build multi-agent systems with A2A protocol

---

## Quick Reference

### Common Commands

```bash
# Install ADK
pip install google-adk

# Start Web UI for debugging
adk web --log_level DEBUG

# Run evaluation
adk eval agent_name test.evalset.json --config_file_path=config.json

# Deploy to Vertex AI
adk deploy agent_engine --project=PROJECT_ID --region=us-west1 agent_dir/
```

### Essential Code Snippets

**Basic Agent:**
```python
from google.adk import Agent, Gemini, InMemoryRunner
from google.adk.tools import google_search

agent = Agent(
    name="assistant",
    model=Gemini(model="gemini-2.5-flash-lite"),
    instruction="You are a helpful assistant.",
    tools=[google_search]
)

runner = InMemoryRunner(agent=agent)
response = await runner.run("What's the weather?")
```

**Agent with Memory:**
```python
from google.adk import LlmAgent, Runner
from google.adk.sessions import DatabaseSessionService
from google.adk.memory import InMemoryMemoryService

agent = LlmAgent(tools=[preload_memory])
runner = Runner(
    agent=agent,
    session_service=DatabaseSessionService("sessions.db"),
    memory_service=InMemoryMemoryService()
)
```

**Custom Tool:**
```python
def get_temperature(city: str) -> dict:
    """Get the current temperature for a city.

    Args:
        city: Name of the city

    Returns:
        Dictionary with temperature info
    """
    # Implementation
    return {"city": city, "temp": 72, "unit": "F"}
```

---

## External Resources

- **ADK Documentation:** https://google.github.io/adk-docs/
- **A2A Protocol:** https://a2a-protocol.org/
- **Vertex AI Agent Engine:** https://cloud.google.com/agent-builder/agent-engine/overview
- **Gemini API:** https://ai.google.dev/gemini-api/
- **Google AI Studio:** https://aistudio.google.com/
- **Kaggle Discord:** https://discord.com/invite/kaggle

---

## Repository Structure

```
Google-Intensive-AI-Agents-Nov2025/
├── Day 1/          # Introduction to AI Agents
├── Day 2/          # Agent Tools & MCP
├── Day 3/          # Sessions & Memory
├── Day 4/          # Observability & Evaluation
├── Day 5/          # A2A & Deployment
└── README.MD       # This file
```

---

**Course Provider:** Google via Kaggle
**Format:** 5-Day Intensive
**Focus:** Production-ready AI agent development with Google ADK
