# MODELAGENTIA

## Cos'è in una frase

Un’interfaccia web a file singolo per orchestrare più agenti AI in sequenza, integrando modelli di linguaggio, generazione di immagini e video, con una pipeline di validazione a più livelli. Può funzionare interamente nel browser (client‑side) oppure essere servita da un semplice server per abilitare l’accesso da altri dispositivi e, opzionalmente, estendere le funzionalità con un backend personalizzato.

---

## Il problema che risolve

Quando si lavora con modelli AI tramite le loro interfacce ufficiali, ogni conversazione è isolata, non c’è memoria tra sessioni e si è costretti a copiare manualmente gli output da un servizio all’altro. Inoltre le risposte di un singolo modello possono soffrire di allucinazioni o superficialità perché generate in un unico passaggio.

MODELAGENTIA affronta questi limiti organizzando il lavoro in una **pipeline multi‑agente** dove diversi ruoli (analista, esecutore, validatore, giuria) collaborano per produrre una risposta finale più robusta e ragionata. I risultati vengono salvati in una memoria persistente nel browser, rendendoli riutilizzabili in sessioni future.

Servendo il file tramite un semplice server web statico (es. con Python) puoi accedervi da qualsiasi dispositivo sulla stessa rete e, volendo, sviluppare un backend personalizzato per eseguire modelli locali o condividere dati tra più utenti.

---

## Come funziona (schema semplice)

Il sistema opera secondo una gerarchia definita **Jury Refiner**:

```
[Tu scrivi un task]
        ↓
[Pianificatore] – analizza il task e decide il percorso
        ↓
┌────────────────────────────────────┐
│  LIVELLO 1 – Esperti               │
│  (Logica, Performance, Dati, Creatività) │
│  – propongono soluzioni parallele   │
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LIVELLO 2 – Validatore Critico    │
│  – identifica falle e contraddizioni│
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LIVELLO 3 – Giuria                 │
│  – valuta fattibilità, rischi, equilibrio │
└────────────────────────────────────┘
        ↓
┌────────────────────────────────────┐
│  LIVELLO 4 – Sintetizzatore Finale  │
│  – produce la risposta definitiva   │
└────────────────────────────────────┘
        ↓
[Output finale + link per scaricare]
```

Tutte le chiamate AI avvengono direttamente dal browser verso i provider (Gemini, Groq, DeepSeek). Le tue chiavi API rimangono nel dispositivo e vengono salvate in modo sicuro nel `localStorage`.

### Modalità di esecuzione

- **Locale / Client‑side** – apri il file `TEST.html` direttamente dal browser. Tutto gira sul tuo computer.
- **Con server statico** – avvii un semplice server web (es. `python -m http.server`) sulla porta 8080 e accedi al file via `http://127.0.0.1:8080/TEST.html`. Questo permette di usare l’applicazione da altri dispositivi sulla stessa rete (es. smartphone o tablet) e mantiene tutte le funzionalità client‑side.
- **Con backend personalizzato** – puoi sviluppare un vero backend (ad esempio in Python, Node.js) che implementi le API attese da MODELAGENTIA (endpoint `/api/task`, `/api/status`, ecc.). In questo modo puoi:
  - Eseguire modelli locali (es. tramite Ollama, vLLM) senza bisogno di chiavi API.
  - Condividere task, risultati e memoria tra più dispositivi.
  - Aggiungere nuovi nodi, agenti o regole scrivendo poche righe di codice (flessibilità totale).

---

## Cosa serve per usarlo

### Solo client‑side (file aperto direttamente)
- Un browser moderno (Chrome, Firefox, Edge, Safari recenti) che supporti IndexedDB e `localStorage`.
- Almeno una chiave API tra:
  - **Google Gemini** ([aistudio.google.com](https://aistudio.google.com/)) – gratuito con limiti
  - **Groq Cloud** ([console.groq.com](https://console.groq.com/)) – gratuito con limiti
  - **DeepSeek** ([platform.deepseek.com](https://platform.deepseek.com/)) – a pagamento, costi contenuti
- Connessione internet attiva (le chiamate API avvengono in rete).

### Con server statico (per accesso da più dispositivi)
- Python installato (o qualsiasi altro server web statico).
- I dispositivi devono essere sulla stessa rete locale.

### Con backend personalizzato (per modelli locali e collaborazione)
- Un server (locale o remoto) che implementi le API attese da MODELAGENTIA (endpoint `/api/health`, `/api/task`, `/api/status`, ecc.).
- I modelli locali devono essere accessibili dal backend (es. tramite Ollama, vLLM, o integrazioni personalizzate).
- Nessuna chiave API necessaria se si usano solo modelli locali.

---

## Come installarlo

### Solo client‑side (browser)
1. **Scarica il file** `TEST.html` da questo repository.
2. **Aprilo** con il tuo browser (doppio clic o trascina nella finestra).
3. Seleziona un provider cliccando sui badge (GEMINI, GROQ, DEEP SEEK).
4. Incolla la tua chiave API nel campo e premi **Salva**.
5. Scrivi un task nell’area centrale e premi **ESEGUI PROCESSO SINCRONIZZATO**.

Tutti i dati (chiavi, memoria, checkpoint) rimangono sul tuo computer e sono persistenti tra le sessioni.

### Con server statico (per accesso da altri dispositivi)
1. Apri un terminale nella cartella dove hai salvato `TEST.html`.
2. Avvia un semplice server HTTP (es. con Python):
   ```batch
   python -m http.server 8080 --bind 127.0.0.1
   ```
3. Apri il browser all’indirizzo `http://127.0.0.1:8080/TEST.html`.
4. (Opzionale) Per accedere da altri dispositivi sulla stessa rete, sostituisci `127.0.0.1` con l’IP locale del tuo computer (es. `192.168.1.10`) e assicurati che il firewall permetta la connessione sulla porta 8080.

**Esempio di script batch (Windows)**  
Crea un file `avvia_server.bat` con il seguente contenuto:

```batch
@echo off
title Modelagentia Server
:: Uccide eventuali processi python residui
taskkill /f /im python.exe 2>nul
timeout /t 1 >nul
:: Avvia il server sull'IP locale
echo Avvio in corso...
start python -m http.server 8080 --bind 127.0.0.1
timeout /t 2 >nul
:: Apre direttamente il browser
start http://127.0.0.1:8080/TEST.html
exit
```

### Con backend personalizzato (per modelli locali e collaborazione)
1. Sviluppa un server che esponga le API attese da MODELAGENTIA (vedi il codice in `MODELAGENTIA.Adapter` per i dettagli degli endpoint).
2. Configuralo per ascoltare sulla porta desiderata (es. 8080).
3. Nel file `TEST.html`, imposta `MODELAGENTIA.Config.useBackend = true` e, se necessario, modifica `apiBase` per puntare al tuo server.
4. Avvia il server e apri il file come al solito. Le richieste verranno inoltrate al backend, che può eseguire modelli locali e condividere lo stato tra più utenti.

---

## Stato attuale del progetto

### ✅ Funziona oggi (client‑side)
- Pipeline multi‑agente con livelli L1‑L4 e generazione di output testuale.
- Supporto per i provider **Gemini**, **Groq**, **DeepSeek**.
- Fallback automatico tra modelli dello stesso provider (es. se `gemini‑2.0‑flash` non risponde, prova `gemini‑2.5‑flash`).
- Generazione di immagini (Imagen) e video (Veo) tramite API Gemini.
- Memoria persistente: i task e i risultati vengono salvati in IndexedDB e possono essere richiamati in sessioni future.
- Pannello di gestione della memoria vettoriale (visualizza, modifica, cancella checkpoint, regola pesi).
- Download dei risultati come file con link rigenerabili anche dopo il refresh.
- Statistiche in tempo reale (latenza media, token consumati per provider).
- Esportazione e importazione dello stato in formato JSON.

### 🔧 In sviluppo / limitazioni note
- La generazione video dipende dalla disponibilità del modello `veo‑3.1‑generate‑preview` nel tuo account Gemini.
- Non esiste ancora un sistema di autenticazione per l’accesso al backend; chiunque possa raggiungere il server può inviare task.
- L’interfaccia non è ottimizzata per schermi piccoli (mobile).
- Le chiavi API salvate in `localStorage` sono leggibili da chiunque abbia accesso fisico al computer o ai DevTools.

### 🧠 Modalità backend personalizzato
- Il codice client prevede la connessione a un backend, ma il backend stesso **non è incluso** in questo repository. Puoi svilupparlo tu stesso seguendo le specifiche nell’adapter (`MODELAGENTIA.Adapter`).
- Una volta attivo, il backend permette di:
  - Usare modelli locali (es. Ollama) senza chiavi API.
  - Condividere task e memoria tra più dispositivi.
  - Aggiungere nodi personalizzati (agenti, regole) modificando poche righe di codice, sfruttando tutta la flessibilità di un linguaggio di programmazione lato server.

---

## Differenze rispetto alle alternative

| | MODELAGENTIA | AnythingLLM | LM Studio | GPT4All |
|---|---|---|---|---|
| **Installazione** | Nessuna (file HTML) o server statico | Server locale richiesto | App da installare | App da installare |
| **Modelli locali** | ❌ (solo via backend personalizzato) | ✅ | ✅ | ✅ |
| **Provider cloud** | Gemini, Groq, DeepSeek | Molti | Molti | Alcuni |
| **Agenti in sequenza** | ✅ Integrati | ❌ (singolo modello) | ❌ | ❌ |
| **Generazione immagini** | ✅ (via Gemini) | Limitata | ❌ | ❌ |
| **Memoria persistente** | ✅ IndexedDB (client) | ✅ (vettoriale) | ❌ | ❌ |
| **Privacy dati** | Chiavi nel browser | Configurabile | Tutto locale | Tutto locale |
| **Curva di apprendimento** | Bassa | Media | Bassa | Bassa |

**Quando scegliere MODELAGENTIA:**  
- Vuoi un ambiente multi‑agente che funzioni subito senza installare nulla.  
- Lavori già con API cloud e vuoi un’interfaccia unificata.  
- Ti serve la persistenza dei risultati tra sessioni senza configurare un database.  
- Vuoi accedere all’app da smartphone/tablet sulla stessa rete con un semplice server statico.

**Quando preferire le alternative:**  
- Vuoi eseguire modelli completamente offline → **LM Studio** o **GPT4All**.  
- Hai bisogno di una knowledge base aziendale con RAG avanzato → **AnythingLLM**.  
- Vuoi supporto per OpenAI, Anthropic Claude o decine di altri provider → le alternative offrono più scelta.

---

## Roadmap futura

Le seguenti funzionalità sono in fase di valutazione e **non ancora implementate**:

- [ ] Supporto per OpenAI e Anthropic Claude (client‑side).
- [ ] Integrazione con modelli locali via Ollama direttamente dal browser (WebGPU/WebLLM).
- [ ] Interfaccia responsive per dispositivi mobili.
- [ ] Editor visuale del flusso di agenti (drag & drop).
- [ ] Sistema di autenticazione e autorizzazione per il backend multi‑utente.
- [ ] Ricerca semantica avanzata sulla memoria vettoriale.
- [ ] Supporto per modelli audio (es. Whisper via Groq).

## Note sulla sicurezza

- Nella modalità client‑side, le chiavi API sono salvate in `localStorage` e vengono inviate **direttamente** ai provider (Google, Groq, DeepSeek). Non passano mai per server intermedi.
- Se usi un server statico, le chiavi rimangono comunque sul dispositivo client; il server non le vede.
- Se sviluppi un backend personalizzato, assicurati di proteggerlo (es. con un firewall o autenticazione) per evitare usi non autorizzati.
- In ambiente condiviso, cancella sempre le chiavi dal browser dopo l’uso usando il pulsante **PULISCI CACHE** nella toolbar.
---
---
--- 
<img width="1801" height="845" alt="ModelAgentIA2" src="https://github.com/user-attachments/assets/bbf05ffd-6ba9-490c-b0da-4f75ffc410ae" />
--- 
<img width="1807" height="853" alt="ModelAgentIAmemoriavettoriale" src="https://github.com/user-attachments/assets/b5e9484f-d41f-4b36-87b3-cb10a1479e64" />
---
<img width="1795" height="853" alt="ModelAgentIAmemoriavettoriale2" src="https://github.com/user-attachments/assets/d20f460e-a83c-42c3-95b3-359ad74e0039" />
---
---
![maia-ezgif com-optimize](https://github.com/user-attachments/assets/a3d44cda-15ad-4c14-a9e8-c80e1783e286)
---
https://github.com/user-attachments/assets/32f448ea-ed29-49f7-85ec-dc627ae8c6af
---
---
---
--- 
(https://gitstrength.netlify.app/api/visitor-count?key=[RobertoAbbruzzese])
---
(https://gitstrength.netlify.app/api/card?username=[RobertoAbbruzzese])](https://gitstrength.netlify.app)



