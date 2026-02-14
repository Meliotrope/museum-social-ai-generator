# Guida completa alla configurazione del workflow 🏛️ Generate museum social media copy from Google Sheets with Claude AI (Italian)

---

# 1) CONFIGURAZIONE TRIGGER

Questo workflow si attiva automaticamente ogni giorno alle 8:00 del mattino.

## Per modificare quando si attiva:
1. Clicca sul nodo "Schedule Trigger"
2. Nel pannello Parameters:
   - In "Trigger Interval" scegli la frequenza (Days, Hours, Minutes, ecc.)
   - Se scegli "Days": imposta l'ora in "Hour"
   - Se scegli "Hours" o "Minutes": imposta ogni quante ore/minuti in "Every"
3. Salva

## Per impostare l'attivazione manuale:
1. Clicca col tasto destro su "Schedule Trigger"
2. Scegli "Delete"
3. Clicca sul + in alto a destra
4. Cerca "Manual Trigger" e aggiungilo
5. Collegalo a "Google Sheets - Leggi"

# 2) GOOGLE SHEETS - LEGGI

Questo nodo legge le righe dal tuo piano editoriale Google Sheets che hanno Stato = "da processare".

## Configurazione credenziali Google:
1. Clicca sul nodo "Google Sheets - Leggi"
2. Nel pannello Parameters, clicca su "Credential to connect with"
3. Scegli "Create New Credential"
4. Segui la procedura di autenticazione con Google
5. Autorizza n8n ad accedere ai tuoi fogli Google

## Configurazione foglio:
1. In "Document" inserisci l'URL completo del tuo Google Sheets
2. In "Sheet" seleziona da "List" il foglio
3. Salva

## Configurazione filtro:
1. Scorri verso il basso fino a "Filters"
2. Clicca su "Add Filter"
3. In "Column" scrivi: **Stato**
4. In "Value" scrivi: **da processare**
5. Scorri fino a "Options"
6. Attiva l'opzione "When Filter Has Multiple Matches" e scegli **Return All Matches**

# 3) AI AGENT

Questo nodo genera i copy per Instagram e Facebook usando l'LLM Claude.

## Configurazione credenziali Anthropic:
1. Clicca sul nodo "Anthropic Chat Model"
2. In "Credential to connect with" scegli "Create New Credential"
3. Incolla la tua API key Anthropic
4. Salva

## Scelta modello AI:
1. Clicca sul nodo "Anthropic Chat Model"
2. In "Model" scegli quale versione di Claude usare: **per test e uso economico** lascia **Claude Haiku 4.5** (già impostato), **per qualità superiore** scegli **Claude Sonnet 4.5**

## Per aggiungere altri social:
Se non hai esperienze di prompt engineering, **chiedi supporto a un AI (Claude, ChatGPT...)** seguendo questi passaggi:
1. Clicca sul nodo "AI Agent"
2. Copia il testo che trovi in "prompt - user message"
3. Forniscilo a Claude o ChatGPT e chiedi: *"Aggiungi istruzioni per LinkedIn (esempio) mantenendo la struttura esistente"*
4. Aggiorna il prompt nel nodo "AI Agent" con la nuova versione
5. Vai sul nodo **"Structured Output Parser"** e aggiungi la nuova chiave nello schema JSON:
```
{
  "instagram": "",
  "facebook": "",
  "linkedin": ""
}
```
6. Vai sul nodo **"Google Sheets - Aggiorna"** e aggiungi la colonna LinkedIn nei mapping
7. Ricorda di aggiungere la colonna "LinkedIn" anche nel tuo Google Sheets.

## Lingua del prompt e dell'output:
Il prompt è scritto in **inglese** per ridurre i costi di circa il 15-25% (i modelli AI processano l'inglese in modo più efficiente).

**L'output sarà comunque in italiano** perché il prompt contiene l'istruzione esplicita di generare testi in italiano.

Se preferisci, puoi **tradurre il prompt in italiano** o **cambiare la lingua in cui restituire l'output** (chiedendo supporto a un AI per modificare il prompt, se necessario). Funzionerà ugualmente a costi leggermente superiori.

# 4) GOOGLE SHEETS - AGGIORNA

Questo nodo scrive i copy generati dall'AI nel tuo Google Sheets e aggiorna lo Stato a "processato".

## Configurazione credenziali e foglio:
1. Segui le istruzioni per le credenziali di "Google Sheets - Leggi"
2. In "Document" inserisci lo stesso **URL** del Google Sheet
3. In "Sheet" seleziona lo stesso **foglio**
4. In "Column to Match On" scegli **row_number**

## Configurazione colonne:
Scorri fino alla sezione "Values to update":

**Per row_number:**
1. Clicca sull'icona "Expression" (fx) accanto al campo
2. Cancella lo 0
3. Scrivi: `{{ $json.row_number }}`

**Per Stato:**
1. Lascia modalità "Fixed"
2. Scrivi: `processato`

**Per Instagram:**
1. Clicca sull'icona "Expression" (fx)
2. Scrivi: `{{ $json.output.instagram }}`

**Per Facebook:**
1. Clicca sull'icona "Expression" (fx)
2. Scrivi: `{{ $json.output.facebook }}`
