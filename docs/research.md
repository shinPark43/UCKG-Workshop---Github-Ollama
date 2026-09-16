# UCKG Workshop — Architecture & Workflow Research

## Goal

Give the UCKG research team a hands-on experience of running a local LLM on their own device — no internet, no cloud API, no coding required. The demo application is a chatbot that answers questions about the UCKG research paper.

---

## Hardware Constraint

- **16GB RAM** per participant machine
- Mix of macOS, Windows, Linux

---

## Final Architecture

```
Ollama (installed natively on host)
  └── gemma3:4b  — chat model, num_ctx set to 32768
Docker Desktop
  └── Open WebUI — chat UI with built-in PDF document support
docs/
  └── UCKG research paper (PDF, pre-loaded into Open WebUI)
```

Open WebUI connects to the host Ollama instance via:
```
OLLAMA_BASE_URL: http://host.docker.internal:11434
```

---

## Why Native Ollama + Dockerized Open WebUI

| Decision | Reason |
|----------|--------|
| Ollama on host, not in Docker | Native GPU acceleration (Metal on Mac, CUDA on Windows) — significantly faster responses |
| Open WebUI in Docker | Tiny image, pulls in seconds, zero configuration needed |
| gemma3:4b | Fits in ~3GB RAM, fast enough for real-time demo on CPU, good quality for Q&A |
| num_ctx = 32768 | UCKG paper is ~10K tokens — fits entirely in one context load, no chunking needed |

---

## Why Not Other Approaches

| Approach | Why Rejected |
|----------|-------------|
| Ollama inside Docker | No GPU access by default — CPU-only, noticeably slower |
| Eve agent framework (Vercel) | Beta, Node 24 required, built for Vercel hosted infra, no Ollama support out of the box |
| RAG with embeddings + ChromaDB | Unnecessary for a 15-page paper that fits in context window |
| Agent workflow (file read/search tools) | Requires 7B+ model for reliable tool calling — gemma3:4b is too unreliable for agentic loops |
| gemma3:12b or larger | Slower on 16GB CPU-only machines, worse workshop experience |

---

## Document Strategy: Long Context Stuffing

The UCKG paper is 15 pages (~10,000 tokens). Instead of embedding or agent-based file reading:

- Open WebUI's built-in document upload loads the full PDF into context on each query
- gemma3:4b supports 128K context architecturally — Ollama default is 8K, so `num_ctx` must be explicitly set to at least 32768
- No vector store, no embedding model, no extra RAM overhead

---

## RAM Budget (16GB)

```
macOS/Windows OS          ~4.0 GB
Docker Desktop            ~1.0 GB
gemma3:4b (quantized)     ~2.5 GB
Open WebUI                ~0.5 GB
Headroom                  ~8.0 GB
─────────────────────────────────
Total used                ~8.0 GB  ✓
```

Participants should close heavy apps (Chrome with many tabs, etc.) before the session.

---

## Prerequisites for Participants

Two installs required before the workshop:

1. **Ollama desktop** — [ollama.com/download](https://ollama.com/download)
2. **Docker Desktop** — [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

Model pre-pull (do at home, ~3GB download):
```bash
ollama pull gemma3:4b
```

Verify Ollama is running by visiting `http://localhost:11434` — should show `Ollama is running`.

Full OS-specific instructions: see `docs/ollama_install_instruction.md`

---

## Workshop Flow

```
Before workshop (homework)
  1. Install Ollama desktop
  2. Install Docker Desktop
  3. ollama pull gemma3:4b

Day of workshop
  4. docker compose up
  5. Open browser → http://localhost:3000
  6. Upload UCKG paper PDF into Open WebUI
  7. Start chatting
```

---

## Example Questions for the Demo

| Type | Question |
|------|----------|
| Concept | "What is the difference between UCO and UCKG?" |
| Architecture | "How does UCKG transform CVE data into RDF?" |
| Data sources | "What are the six data sources UCKG integrates and how are they linked?" |
| Comparison | "How does UCKG differ from STIX and STUCCO?" |
| Methodology | "Explain the topic/subscriber update model described in the paper" |
| Future work | "What does the paper say about using LLMs in UCKG?" |

---

## Next Steps

- [ ] Create `docker-compose.yml` for Open WebUI
- [ ] Add model pull command to pre-instruction docs
- [ ] Pre-load UCKG PDF into Open WebUI default knowledge base (optional)
- [ ] Test end-to-end on a 16GB machine
