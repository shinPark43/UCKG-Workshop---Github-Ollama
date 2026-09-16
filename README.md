# UCKG Local LLM Workshop

Run a local AI chatbot that answers questions about the UCKG research paper — no internet, no cloud API, no coding required. Everything runs on your own machine using [Ollama](https://ollama.com) and [Open WebUI](https://github.com/open-webui/open-webui).

---

## What You'll Build

A locally-hosted chatbot powered by **llama3.2:3b** (a 3-billion parameter open-source model) that can read and discuss our UCKG research paper. The full paper is loaded into the model's context window so it can answer detailed questions about the ontology design, data sources, architecture, and future work — all without sending any data to the cloud.

---

## Prerequisites (Do This Before the Workshop)

These steps require downloading ~3GB, so complete them at home on a good internet connection.

### Step 1 — Install Ollama

Follow the OS-specific instructions in [`docs/ollama_install_instruction.md`](docs/ollama_install_instruction.md).

Verify Ollama is running by visiting `http://localhost:11434` in your browser — you should see: `Ollama is running`

### Step 2 — Install Docker Desktop

Download and install Docker Desktop for your OS:
- macOS / Windows: [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
- Linux: install Docker Engine + Docker Compose plugin via your package manager

### Step 3 — Pull the Model and Create the UCKG Assistant

Open a terminal and run:

```bash
# Download the base model (~2GB)
ollama pull llama3.2:3b

# Clone this repo (skip if already done)
git clone https://github.com/YOUR-ORG/UCKG-Workshop---Github-Ollama.git
cd UCKG-Workshop---Github-Ollama

# Create the UCKG assistant (sets context window and system prompt)
ollama create uckg-assistant -f Modelfile
```

Verify the model was created:

```bash
ollama list
# Should show: uckg-assistant   ...
```

---

## Day-of Workshop Setup

### Step 4 — Start Open WebUI

In the repo directory, run:

```bash
docker compose up -d
```

First launch pulls the Open WebUI image (~1GB). Subsequent launches are instant.

Open your browser and go to: **http://localhost:3000**

### Step 5 — Disable Builtin Tools for uckg-assistant

This is a one-time setup that prevents Open WebUI from injecting tools the model doesn't need.

1. Click your avatar (bottom-left) → **Admin Panel**
2. Click **Models** in the left sidebar
3. Find `uckg-assistant` → click the pencil (edit) icon
4. Scroll to **Capabilities** → uncheck **Builtin Tools**
5. Click **Save**

### Step 6 — Load the Research Paper

1. In Open WebUI, click **Workspace** in the left sidebar
2. Click **Knowledge** → **+ Create Knowledge Base**
3. Name it `UCKG Paper` and click **Create**
4. Click **+ Add Content** → **Upload File**
5. Upload the PDF from `docs/Toward a Unified Cybersecurity Knowledge Graph_...pdf`
6. Wait for the upload to complete

### Step 7 — Start Chatting

1. Go back to the main **Chat** view
2. In the model selector (top of page), choose **uckg-assistant**
3. Click the paperclip icon in the message box → upload the PDF from `docs/`
4. Ask your question in the same message — the full paper will be loaded into context

---

## Example Questions

| Type | Question |
|------|----------|
| Concept | "What is the difference between UCO and UCKG?" |
| Architecture | "How does UCKG transform CVE data into RDF?" |
| Data sources | "What are the six data sources UCKG integrates and how are they linked?" |
| Comparison | "How does UCKG differ from STIX and STUCCO?" |
| Methodology | "Explain the topic/subscriber update model described in the paper" |
| Future work | "What does the paper say about using LLMs in UCKG?" |

---

## Stopping the Workshop Environment

```bash
docker compose down
```

Chat history and uploaded files are preserved in a Docker volume. To reset everything:

```bash
docker compose down -v
```

---

## Troubleshooting

**Open WebUI shows "Ollama not reachable"**
- Make sure Ollama desktop is running (look for the Ollama icon in your menu bar / system tray)
- Visit `http://localhost:11434` to confirm it's up

**Port 3000 already in use**
- Edit `docker-compose.yml` and change `"3000:8080"` to `"3001:8080"`, then access at `http://localhost:3001`

**`uckg-assistant` not showing in model dropdown**
- Run `ollama list` to confirm it was created
- If missing, re-run: `ollama create uckg-assistant -f Modelfile`

**Responses feel slow**
- Close memory-heavy apps (Chrome tabs, VS Code, etc.)
- The model uses ~2.0GB RAM; the machine needs ~8GB free total

---

## Architecture

```
Your Machine
├── Ollama (native install, port 11434)
│   └── uckg-assistant  ←  llama3.2:3b + num_ctx 32768 + system prompt
│
└── Docker Desktop
    └── Open WebUI (port 3000)
        └── connects to Ollama via host.docker.internal:11434
```

The UCKG paper (~10,000 tokens) fits entirely within the model's 32K context window — no embedding pipeline or vector database needed.

---

## Resources

- Architecture decisions and rationale: [`docs/research.md`](docs/research.md)
- Ollama install guide: [`docs/ollama_install_instruction.md`](docs/ollama_install_instruction.md)
- Open WebUI docs: [docs.openwebui.com](https://docs.openwebui.com)
- Ollama model library: [ollama.com/library](https://ollama.com/library)
