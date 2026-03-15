# MODELAGENTIA

## What it is in one sentence

A single-file web interface for orchestrating multiple AI agents in sequence, integrating language models, image and video generation, with a multi-level validation pipeline. It can run entirely in the browser (client-side) or be served by a simple server to enable access from other devices and, optionally, extend functionality with a custom backend.

---

## The problem it solves

When working with AI models through their official interfaces, every conversation is isolated, there is no memory between sessions, and you are forced to manually copy outputs from one service to another. Furthermore, single-model responses can suffer from hallucinations or superficiality because they are generated in a single pass.

MODELAGENTIA addresses these limitations by organizing the work into a multi-agent pipeline where different roles (analyst, executor, validator, jury) collaborate to produce a more robust and reasoned final response. Results are saved in persistent memory within the browser, making them reusable in future sessions.

By serving the file via a simple static web server (e.g., with Python), you can access it from any device on the same network and, if desired, develop a custom backend to run local models or share data among multiple users.

---

## How it works (simple diagram)

The system operates according to a hierarchy defined as Jury Refiner:

```
[You write a task]
        ↓
[Planner] – analyzes the task and decides the path
        ↓
┌────────────────────────────────────┐
│  LEVEL 1 – Experts                 │
│  (Logic, Performance, Data, Creativity) │
│  – propose parallel solutions      │
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LEVEL 2 – Critical Validator      │
│  – identifies flaws and contradictions│
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LEVEL 3 – Jury                    │
│  – evaluates feasibility, risks, balance │
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LEVEL 4 – Final Synthesizer       │
│  – produces the definitive response│
└────────────────────────────────────┘
        ↓
[Final Output + link to download]
```

All AI calls occur directly from the browser to the providers (Gemini, Groq, DeepSeek). Your API keys remain on your device and are securely saved in localStorage.

### Execution Modes

- **Local / Client‑side – open the TEST.html file directly in your browser. Everything runs on your computer.
- **With static server – start a simple web server (e.g., python -m http.server) on port 8080 and access the file via http://127.0.0.1:8080/TEST.html. This allows you to use the application from other devices on the same network (e.g., smartphone or tablet) and maintains all client-side functionalities.
- **With custom backend – you can develop a true backend (for example in Python, Node.js) that implements the APIs expected by MODELAGENTIA (endpoints /api/task, /api/status, etc.). This way you can:
  - Run local models (e.g., via Ollama, vLLM) without needing API keys.
  - Share tasks, results, and memory across multiple devices.
  - Add new nodes, agents, or rules by writing a few lines of code (total flexibility).

---

## What you need to use it

### Client‑side only (file opened directly)
- A modern browser (Chrome, Firefox, Edge, recent Safari) that supports IndexedDB and localStorage.
- At least one API key from:
  - Google Gemini (aistudio.google.com) – free with limits
  - Groq Cloud (console.groq.com) – free with limits
  - DeepSeek (platform.deepseek.com) – paid, low costs
- Active internet connection (API calls occur over the network).

### With static server (for access from multiple devices)
- Python installed (or any other static web server).
- Devices must be on the same local network.

### With custom backend (for local models and collaboration)
- A server (local or remote) that implements the APIs expected by MODELAGENTIA (endpoints /api/health, /api/task, /api/status, etc.).
- Local models must be accessible from the backend (e.g., via Ollama, vLLM, or custom integrations).
- No API keys needed if using only local models.

---

## How to install it

Client‑side only (browser)
1. Download the file TEST.html from this repository.
2. Open it with your browser (double-click or drag into the window).
3. Select a provider by clicking on the badges (GEMINI, GROQ, DEEP SEEK).
4. Paste your API key into the field and press Save.
5. Write a task in the central area and press RUN SYNCED PROCESS.

All data (keys, memory, checkpoints) remain on your computer and are persistent between sessions.

With static server (for access from other devices)
1. Open a terminal in the folder where you saved MODELAGENTIA.html.
2. Start a simple HTTP server (e.g., with Python):
   ```batch
   python -m http.server 8080 --bind 127.0.0.1
   ```
3. Open your browser at http://127.0.0.1:8080/TEST.html.
4. (Optional) To access from other devices on the same network, replace 127.0.0.1 with your computer's local IP (e.g., 192.168.1.10) and ensure the firewall allows connection on port 8080.

**Example batch script (Windows)**  
Create a file start_server.bat with the following content:

```batch
@echo off
title Modelagentia Server
:: Kills any residual python processes
taskkill /f /im python.exe 2>nul
timeout /t 1 >nul
:: Starts the server on the local IP
echo Starting...
start python -m http.server 8080 --bind 127.0.0.1
timeout /t 2 >nul
:: Opens the browser directly
start http://127.0.0.1:8080/TEST.html
exit
```

### With custom backend (for local models and collaboration)
1. Develop a server that exposes the APIs expected by MODELAGENTIA (see the code in MODELAGENTIA.Adapter for endpoint details).
2. Configure it to listen on the desired port (e.g., 8080).
3. In the TEST.html file, set MODELAGENTIA.Config.useBackend = true and, if necessary, modify apiBase to point to your server.
4. Start the server and open the file as usual. Requests will be forwarded to the backend, which can execute local models and share the state among multiple users.

---

## Stato attuale del progetto

### ✅ Current state of the project)
- Multi-agent pipeline with levels L1-L4 and textual output generation.
- Support for Gemini, Groq, DeepSeek providers.
- Automatic fallback between models from the same provider (e.g., if gemini-2.0-flash doesn't respond, it tries gemini-2.5-flash).
- Image (Imagen) and video (Veo) generation via Gemini API.
- Persistent memory: tasks and results are saved in IndexedDB and can be recalled in future sessions.
- Vector memory management panel (view, edit, delete checkpoints, adjust weights).
- Download results as files with links that can be regenerated even after refresh.
- Real-time statistics (average latency, tokens consumed per provider).
- Export and import of state in JSON format.

### 🔧 In development / known limitations
- Video generation depends on the availability of the veo-3.1-generate-preview model in your Gemini account.
- There is no authentication system yet for backend access; anyone who can reach the server can send tasks.
- The interface is not optimized for small screens (mobile).
- API keys saved in localStorage are readable by anyone with physical access to the computer or DevTools.

### 🧠 Custom backend mode
- The client code includes the connection to a backend, but the backend itself is not included in this repository. You can develop it yourself following the specifications in the adapter (MODELAGENTIA.Adapter).
- Once active, the backend allows you to:
  - Use local models (e.g., Ollama) without API keys.
  - Share tasks and memory across multiple devices.
  - Add custom nodes (agents, rules) by modifying a few lines of code, leveraging the full flexibility of a server-side programming language.

---

## Differences compared to alternatives

| | MODELAGENTIA | AnythingLLM | LM Studio | GPT4All |
|---|---|---|---|---|
| Installation | None (HTML file) or static server | Local server required | App to install | App to install |
| Local Models | ❌ (only via custom backend) | ✅ | ✅ | ✅ |
| Cloud Providers | Gemini, Groq, DeepSeek | Many | Many | Some |
| Sequential Agents | ✅ Integrated | ❌ (single model) | ❌ | ❌ |
| Image Generation | ✅ (via Gemini) | Limited | ❌ | ❌ |
| Persistent Memory | ✅ IndexedDB (client) | ✅ (vector) | ❌ | ❌ |
| Data Privacy | Keys in browser | Configurable | All local | All local |
| Learning Curve | Low | Medium | Low | Low |

---
**When to choose MODELAGENTIA:**  
- You want a multi-agent environment that works immediately without installing anything. 
- You already work with cloud APIs and want a unified interface.  
- You need persistence of results between sessions without configuring a database.  
- You want to access the app from smartphone/tablet on the same network with a simple static server.


---

## Future Roadmap

The following features are being evaluated and are not yet implemented:

[ ] Support for OpenAI and Anthropic Claude (client-side).

[ ] Integration with local models via Ollama directly from the browser (WebGPU/WebLLM).

[ ] Responsive interface for mobile devices.

[ ] Visual agent flow editor (drag & drop).

[ ] Authentication and authorization system for multi-user backend.

[ ] Advanced semantic search on vector memory.

[ ] Support for audio models (e.g., Whisper via Groq).

## Security Notes

- In client-side mode, API keys are saved in localStorage and are sent directly to the providers (Google, Groq, DeepSeek). They never pass through intermediate servers.
- If you use a static server, the keys still remain on the client device; the server does not see them.
- If you develop a custom backend, make sure to protect it (e.g., with a firewall or authentication) to prevent unauthorized use.
- In a shared environment, always clear keys from the browser after use using the CLEAR CACHE button in the toolbar.
---



...

![maia-ezgif com-optimize]![vid](https://github.com/user-attachments/assets/13a11a87-f59e-4261-b141-4c5f15b4c142)

---


https://github.com/user-attachments/assets/61b8677f-846d-4cfb-acff-ad53c2bf9a75


---

https://github.com/RobertoAbbruzzese/robertoabbruzzese/discussions/1#discussion-9634620

---

## Licenza e Proprietà Intellettuale
Il progetto **MODELAGENTIA** è un asset proprietario di [Roberto Abbruzzese]. 
Il codice è pubblico per favorire la trasparenza e la collaborazione della community, ma è protetto dalla licenza **CC BY-NC 4.0**.

- ❌ **NO USO COMMERCIALE:** Non è permessa la vendita del software o di derivati.
- ✅ **SI CONTRIBUTI:** Le pull request sono benvenute per migliorare lo scheletro del progetto.
- 🔐 **ARCHITETTURA:** I diritti sull'architettura logica di sincronizzazione restano dell'autore.

---

![Visitors](https://komarev.com/ghpvc/?username=RobertoAbbruzzese&color=blue&style=flat-square)

---
