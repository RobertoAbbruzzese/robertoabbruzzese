# 🧠 MODELAGENTIA

[![ESEGUI APPLICAZIONE](https://img.shields.io/badge/RUN_APP-⭐_TEST.html-FFD700?style=for-the-badge&logo=esotericsoftware&logoColor=black)](https://github.com/RobertoAbbruzzese/robertoabbruzzese/blob/main/%E2%AD%90_TEST.html)
![Visitors](https://komarev.com/ghpvc/?username=RobertoAbbruzzese&repo=robertoabbruzzese&label=VISITE%20PROGETTO&color=brightgreen&style=flat-square)

## What it is in one sentence
A single-file web interface for orchestrating multiple AI agents in sequence, integrating language models, image and video generation, with a multi-level validation pipeline. It can run entirely in the browser (client-side) or be served by a simple server to enable access from other devices and, optionally, extend functionality with a custom backend.

---

## The problem it solves
When working with AI models through their official interfaces, every conversation is isolated, there is no memory between sessions, and you are forced to manually copy outputs from one service to another. Furthermore, single-model responses can suffer from hallucinations or superficiality because they are generated in a single pass.

**MODELAGENTIA** addresses these limitations by organizing the work into a multi-agent pipeline where different roles (analyst, executor, validator, jury) collaborate to produce a more robust and reasoned final response. Results are saved in persistent memory within the browser, making them reusable in future sessions.

By serving the file via a simple static web server (e.g., with Python), you can access it from any device on the same network and, if desired, develop a custom backend to run local models or share data among multiple users.

---

## How it works (Process Flow)
The system operates according to a hierarchy defined as **Jury Refiner**:

```text
[You write a task]
        ↓
[Planner] – Analyzes the task and decides the path
        ↓
┌───────────────────────────────────────────────┐
│  LEVEL 1 – Experts                            │
│  (Logic, Performance, Data, Creativity)       │
│  – Propose parallel solutions                 │
└───────────────────────────────────────────────┘
        ↓
┌───────────────────────────────────────────────┐
│  LEVEL 2 – Critical Validator                 │
│  – Identifies flaws and contradictions        │
└───────────────────────────────────────────────┘
        ↓
┌───────────────────────────────────────────────┐
│  LEVEL 3 – Jury                               │
│  – Evaluates feasibility, risks, and balance  │
└───────────────────────────────────────────────┘
        ↓
┌───────────────────────────────────────────────┐
│  LEVEL 4 – Final Synthesizer                  │
│  – Produces the definitive response           │
└───────────────────────────────────────────────┘
        ↓
[Final Output + Download Link]

```

All AI calls occur directly from the browser to the providers (**Gemini, Groq, DeepSeek**). Your API keys remain on your device and are securely saved in `localStorage`.

### Execution Modes
* **Local / Client‑side:** Open the `TEST.html` file directly in your browser. Everything runs on your computer.
* **With Static Server:** Start a simple web server (e.g., `python -m http.server`) on port 8080 and access via `http://127.0.0.1:8080/TEST.html`. This allows access from other devices on the same network (e.g., smartphone or tablet).
* **With Custom Backend:** Develop a backend (Python, Node.js, etc.) that implements the MODELAGENTIA API endpoints (`/api/task`, `/api/status`). This enables:
* Running local models (via **Ollama, vLLM**) without API keys.
* Sharing tasks, results, and memory across multiple devices.
* Total flexibility to add new nodes, agents, or rules.

---

## Requirements
### 1. Client‑side only
* A modern browser (Chrome, Firefox, Edge, Safari) supporting **IndexedDB** and **localStorage**.
* At least one API key from:
* **Google Gemini** (aistudio.google.com) – Free with limits.
* **Groq Cloud** (console.groq.com) – Free with limits.
* **DeepSeek** (platform.deepseek.com) – Paid, low cost.

* Active internet connection.

### 2. With Static Server
* Python installed (or any other static web server).
* Devices must be on the same local network.

### 3. With Custom Backend
* A local or remote server implementing the MODELAGENTIA API specs.
* Local models accessible from the backend (Ollama, vLLM).
* No API keys needed if using local models exclusively.

---

## Installation & Setup
### Client‑side only
1. Download `TEST.html` from this repository.
2. Open it with your browser.
3. Select a provider (GEMINI, GROQ, DEEP SEEK).
4. Paste your API key and press **Save**.
5. Write a task and press **RUN SYNCED PROCESS**.

### With Static Server
1. Open a terminal in the project folder.
2. Start the server:
```bash
python -m http.server 8080 --bind 127.0.0.1

```


3. Access via `http://127.0.0.1:8080/TEST.html`.

**Windows Batch Script Example (`start_server.bat`):**

```batch
@echo off
title Modelagentia Server
taskkill /f /im python.exe 2>nul
timeout /t 1 >nul
echo Starting...
start python -m http.server 8080 --bind 127.0.0.1
timeout /t 2 >nul
start [http://127.0.0.1:8080/TEST.html](http://127.0.0.1:8080/TEST.html)
exit

```

---

## Project Status
### ✅ Current Features
* **Multi-agent Pipeline:** L1-L4 levels with final synthesis.
* **Multi-Provider:** Gemini, Groq, DeepSeek support.
* **Smart Fallback:** Automatic model switching within the same provider (e.g., Gemini 2.0 -> 1.5).
* **Multimedia:** Image (Imagen) and Video (Veo) generation via Gemini API.
* **Persistent Memory:** Tasks and results saved in **IndexedDB**.
* **Vector Memory Panel:** View, edit, and delete checkpoints; adjust weights.
* **Real-time Stats:** Latency and token consumption tracking.

### 🔧 Known Limitations / In Development
* Video generation depends on `veo-3.1-generate-preview` availability in your Gemini account.
* No native authentication for backends; use firewalls/VPNs for remote access.
* UI is not yet fully optimized for mobile screens.
* API keys in `localStorage` are accessible to anyone with physical access to the device.

---

## Comparison Table
| Feature | MODELAGENTIA | AnythingLLM | LM Studio | GPT4All |
| --- | --- | --- | --- | --- |
| **Installation** | None (HTML) | Local Server | App | App |
| **Local Models** | ❌ (Backend only) | ✅ | ✅ | ✅ |
| **Cloud Providers** | Gemini, Groq, DS | Many | Many | Some |
| **Sequential Agents** | ✅ Integrated | ❌ | ❌ | ❌ |
| **Image Generation** | ✅ (Gemini) | Limited | ❌ | ❌ |
| **Persistent Memory** | ✅ IndexedDB | ✅ (Vector) | ❌ | ❌ |
| **Data Privacy** | Browser-based | Configurable | Local | Local |

---

## Future Roadmap
* [ ] OpenAI and Anthropic Claude support (client-side).
* [ ] Local models via Ollama/WebGPU directly in-browser.
* [ ] Mobile-responsive UI.
* [ ] Drag & Drop visual agent flow editor.
* [ ] Advanced semantic search for vector memory.
* [ ] Audio model support (Whisper via Groq).

---

## Security Notes
* In client-side mode, keys are sent **directly** to providers. They never pass through intermediate servers.
* In a shared environment, always use the **CLEAR CACHE** button to remove keys after use.

---

## License & Intellectual Property
**MODELAGENTIA** is a proprietary asset of **Roberto Abbruzzese**.
The code is public to encourage transparency and community collaboration, protected under the **CC BY-NC 4.0** license.
* ❌ **NO COMMERCIAL USE:** Sale of the software or derivative works is strictly prohibited.
* ✅ **CONTRIBUTIONS:** Pull requests are welcome to improve the project framework.
* 🔐 **ARCHITECTURE:** All rights regarding the logical synchronization architecture remain with the author.
# MODELAGENTIA
[![ESEGUI APPLICAZIONE](https://img.shields.io/badge/RUN_APP-⭐_TEST.html-FFD700?style=for-the-badge&logo=esotericsoftware&logoColor=black)]
(https://github.com/RobertoAbbruzzese/robertoabbruzzese/blob/main/%E2%AD%90_TEST.html)

---

![Visitors](https://gitstrength.netlify.app/api/visitor-count?key=RobertoAbbruzzese&color=green&style=for-the-badge)
