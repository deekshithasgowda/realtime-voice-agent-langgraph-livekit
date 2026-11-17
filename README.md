# Real-Time Voice Agent with LangGraph and LiveKit

A real-time, full-duplex voice assistant that connects a LiveKit audio session to a LangGraph agent. The project demonstrates a voice pipeline with voice activity detection (VAD), speech-to-text (STT), LLM-based reasoning, text-to-speech (TTS), and conversation continuity.

> **Project status:** This repository is based on the open-source LangGraph Voice Call Agent project. The current version should be treated as the baseline implementation until you have run it locally, verified the complete voice flow, and added and tested your own improvements.

## Features

- Real-time voice interaction through LiveKit rooms.
- Full-duplex audio communication.
- Voice activity detection using Silero.
- Speech-to-text and text-to-speech integrations using Deepgram and the configured LiveKit plugins.
- LangGraph integration for agent reasoning and tool workflows.
- Thread-based conversation continuity.
- Example Todo agent with add, list, complete, and delete operations.

## Architecture

```text
User microphone
      |
      v
LiveKit room / audio stream
      |
      v
Voice agent (LiveKit)
      |
      +--> VAD and turn detection
      |
      +--> Speech-to-Text (Deepgram)
      |
      v
LangGraph agent / workflow
      |
      v
LLM response
      |
      v
Text-to-Speech
      |
      v
LiveKit audio stream
      |
      v
User hears the response
```

## Technology Stack

- **Language:** Python 3.12+
- **Agent orchestration:** LangGraph and LangChain
- **Real-time audio:** LiveKit Agents
- **Speech services:** Deepgram
- **LLM integration:** OpenAI (as configured in the example agent)
- **Voice activity detection:** Silero
- **Package management:** uv
- **Local infrastructure:** Docker Compose

## Repository Structure

```text
.
├── src/
│   ├── livekit/
│   │   ├── agent.py              # LiveKit voice-agent entry point
│   │   └── adapter/
│   │       └── langgraph.py      # Adapter between LiveKit and LangGraph
│   └── langgraph/
│       └── agent.py              # Example LangGraph Todo agent
├── compose.yml                   # Local LiveKit server
├── langgraph.json                # LangGraph configuration
├── pyproject.toml                # Python dependencies and project config
├── Makefile                      # Development commands
├── .env.example                  # Environment-variable template
└── README.md
```

## Setup

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd realtime-voice-agent-langgraph-livekit
```

If you are working from an extracted ZIP instead, open the extracted project folder in your terminal.

### 2. Install dependencies

```bash
uv sync
```

### 3. Configure environment variables

```bash
cp .env.example .env
```

Open `.env` and add your own API keys. The example file includes these variables:

```env
LIVEKIT_URL=ws://localhost:7880
LIVEKIT_API_KEY=devkey
LIVEKIT_API_SECRET=secret

OPENAI_API_KEY=your_openai_api_key
DEEPGRAM_API_KEY=your_deepgram_api_key
CARTESIA_API_KEY=your_cartesia_api_key

LANGSMITH_TRACING=true
LANGSMITH_API_KEY=
LANGSMITH_PROJECT=langgraph-voice-call-agent

LANGGRAPH_URL=http://localhost:2024
HUME_API_KEY=
```

Only add credentials for services used by your chosen configuration. **Never commit `.env` or expose API keys in source code, screenshots, or GitHub.**

### 4. Download required model files

```bash
make download-files
```

### 5. Start the local LiveKit server

```bash
docker compose up -d
```

Check that it is running:

```bash
docker compose ps
```

### 6. Start the LangGraph development server

Open a terminal in the project folder and run:

```bash
uv run langgraph dev
```

Keep this terminal running. The default local URL is `http://localhost:2024`.

### 7. Start the LiveKit voice agent

Open a second terminal in the same project folder and run:

```bash
uv run -m src.livekit.agent dev
```

The agent should connect to the local LiveKit server and wait for a participant.


## Example Agent Workflow

The included example LangGraph agent is a Todo assistant. It demonstrates tool-based actions such as:

1. Adding a todo item.
2. Listing todo items.
3. Marking an item as complete.
4. Deleting an item with a confirmation step.

This example is intended to demonstrate how a LangGraph workflow can be connected to a real-time voice interface.

## Troubleshooting

**Model files are missing**

```bash
make download-files
```

**LiveKit ports are already in use**

```bash
docker compose ps
docker compose down
docker compose up -d
```

**LangGraph connection refused**

Make sure the LangGraph development server is running:

```bash
uv run langgraph dev
```

**Python or dependency errors**

Check that Python 3.12+ is being used, then run:

```bash
uv sync
```

## Planned Personal Improvements

The following are potential extensions. Do not describe them as completed until you have implemented and tested them.

- Replace the sample Todo workflow with a voice-based lead qualification workflow.
- Store caller details, conversation summaries, and follow-up status in PostgreSQL.
- Add CRM tools for creating leads and scheduling follow-ups.
- Add human approval before important actions.
- Measure response latency and improve error handling.
- Add tests and document a complete end-to-end demo.

## Learning Outcomes

Through this project, I am exploring:

- Real-time voice-agent architecture.
- Connecting STT, LLM, and TTS components.
- Building tool-using agents with LangGraph.
- Managing conversation state and thread continuity.
- Integrating real-time communication infrastructure with Python AI backends.

