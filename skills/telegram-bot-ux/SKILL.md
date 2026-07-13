---
name: telegram-bot-ux
description: Best practice UX per bot Telegram (comandi, reply keyboard, inline keyboard). Usa SEMPRE questa skill quando l'utente progetta o sviluppa un bot Telegram e deve strutturare menu, pulsanti, comandi o flussi.
---

# Telegram Bot UX — Best Practice: Comandi, Reply Keyboard e Inline Keyboard

> Guida di riferimento per il design dell'interazione nei bot Telegram.
> Basata sulla documentazione ufficiale (core.telegram.org/bots/features, /bots/api, /bots/tutorial) e su pattern consolidati nella community.
> Pensata per essere usata come contesto da un agent di sviluppo.

## Come usare questa skill

Quando progetti o implementi l'interazione di un bot Telegram:

1. Per ogni funzionalità del bot, decidi la superficie giusta usando l'**albero decisionale** (sezione 6).
2. Applica le regole specifiche della superficie scelta (sezioni 2–4) e il rapporto comandi ↔ keyboard (sezione 5).
3. Prima di consegnare, verifica il design contro la **checklist** (sezione 7) e gli **anti-pattern** (sezione 8).
4. In caso di dubbio su dettagli API, verifica sulle fonti ufficiali elencate in fondo: non inventare parametri o comportamenti.

---

## 1. Le tre superfici di interazione

Un bot Telegram ha tre superfici principali di input, con ruoli distinti e complementari:

| Superficie | Cos'è | Dove appare | Cosa genera lato bot |
|---|---|---|---|
| **Comandi** (`/comando`) | Parole chiave testuali che iniziano con `/` (max 32 caratteri) | Menu button, autocompletamento con `/`, testo digitato | Un normale `Message` testuale |
| **Reply keyboard** (custom keyboard) | Pulsanti che sostituiscono la tastiera di sistema, sotto il campo di input | Sotto il campo di testo, "globale" per la chat | Un normale `Message` con il testo esatto del pulsante |
| **Inline keyboard** | Pulsanti attaccati a un singolo messaggio del bot | Sotto il messaggio a cui appartengono | Una `CallbackQuery` (nessun messaggio in chat) |

**Principio chiave:** non sono alternative, sono livelli. I bot ben progettati le usano tutte e tre, ciascuna nel proprio ruolo.

---

## 2. Comandi

### A cosa servono
- Sono l'**API pubblica e stabile** del bot: il contratto minimo, sempre disponibile anche senza keyboard.
- Sono scopribili: menu button accanto al campo di testo, autocompletamento digitando `/`, highlight nei messaggi (un comando evidenziato è tappabile e viene re-inviato).
- Funzionano nei gruppi (dove le keyboard sono spesso inadatte) e nei deep link.

### Comandi obbligatori (richiesti da Telegram)
Telegram chiede a **tutti** gli sviluppatori di supportare questi comandi, per cui le app hanno scorciatoie di interfaccia native:

- `/start` — inizia l'interazione (messaggio introduttivo). Supporta parametri via deep linking (`t.me/bot?start=payload`).
- `/help` — mostra aiuto e capacità del bot.
- `/settings` — (se applicabile) mostra le impostazioni e come modificarle.

### Regole di design
1. **Registra sempre tutti i comandi su @BotFather** (o via `setMyCommands`) con descrizioni chiare: è ciò che popola il menu button e l'autocompletamento. Il menu button è l'indice completo dei comandi — non delegare questo ruolo alla reply keyboard.
2. **`/start` deve essere idempotente e riparatore**: deve sempre ripristinare lo stato iniziale e re-inviare la reply keyboard principale. È l'ancora di salvezza se l'utente perde la keyboard.
3. **`/help` non deve dipendere dallo stato**: deve funzionare in qualsiasi punto della conversazione.
4. Comandi brevi, in minuscolo, senza ambiguità. Usa `command@nomebot` nei gruppi per disambiguare.
5. Puoi impostare **scope e lingue diverse** per i comandi (per utente, per gruppo, per lingua) via `setMyCommands` con `BotCommandScope`.

---

## 3. Reply Keyboard (custom keyboard)

### A cosa serve
- È l'**interfaccia ergonomica persistente**: il menu principale del bot, sempre a portata di pollice.
- Ogni tap invia in chat **esattamente il testo scritto sul pulsante** (non è possibile mostrare un testo e inviarne un altro — se serve, usa inline keyboard).
- È l'**unico modo** per richiedere all'utente: numero di telefono, posizione (`request_contact`, `request_location`), condivisione di chat/utenti (`request_chat`, `request_users`), creazione di poll. Questi pulsanti funzionano solo in chat private.

### Quando usarla
- Menu principale con **3–6 azioni frequenti** di primo livello (es. "📋 Ordini", "⚙️ Impostazioni", "❓ Aiuto").
- Bot con poche funzioni ricorrenti usate continuamente.
- Richiesta di contatto/posizione.

### Quando NON usarla
- Azioni contestuali legate a un messaggio specifico → inline keyboard.
- Flussi multi-step, paginazione, toggle → inline keyboard.
- Nei gruppi (invade l'input di tutti, salvo uso di `selective`).

### Parametri utili
- `resize_keyboard: true` — quasi sempre da usare: adatta l'altezza ai pulsanti (di default la keyboard è enorme).
- `one_time_keyboard: true` — nasconde la keyboard dopo il primo uso (per scelte una tantum).
- `is_persistent: true` — mostra sempre la keyboard quando quella di sistema è nascosta.
- `input_field_placeholder` — testo guida nel campo di input.
- `ReplyKeyboardRemove` — rimuove esplicitamente la keyboard (ricorda: resta visibile finché non la sostituisci o rimuovi).
- `selective` — mostra la keyboard solo a utenti menzionati / al destinatario del reply (utile nei gruppi).

### Vincoli da ricordare
- Il bot **non può distinguere** un tap sul pulsante da un testo identico digitato a mano: sono lo stesso update.
- La keyboard è per-chat, non per-messaggio: quella attiva è sempre l'ultima inviata.
- Etichetta = payload. Se cambi l'etichetta di un pulsante, devi aggiornare l'handler.

---

## 4. Inline Keyboard

### A cosa serve
- Pulsanti **contestuali attaccati a un messaggio specifico**. Premendo un pulsante non viene inviato nulla in chat: il bot riceve una `CallbackQuery`.
- La documentazione ufficiale la consiglia per: **modifica impostazioni, toggle di opzioni, navigazione tra risultati** — tutto ciò che non deve produrre messaggi in chat.
- Il superpotere: il bot può **modificare il proprio messaggio** (`editMessageText`, `editMessageReplyMarkup`) invece di inviarne di nuovi → menu multi-livello, wizard e paginazione senza riempire la chat.

### Tipi di pulsante inline
- **Callback button** (`callback_data`, max **64 byte**) — il tipo principale: invia dati strutturati al bot.
- **URL button** — apre un link.
- **Switch inline** (`switch_inline_query`) — porta l'utente a usare il bot in inline mode in un'altra chat.
- **Login URL** — autenticazione via sito web.
- **Web App** — lancia una mini app.
- **Pay** — pulsante di pagamento (solo con invoice).

### Quando usarla
- Conferme e azioni su un elemento specifico ("✅ Conferma", "❌ Annulla", "🗑 Elimina ordine #123").
- Paginazione e navigazione ("⬅️ Indietro", "➡️ Avanti") con edit del messaggio.
- Impostazioni con toggle on/off aggiornati in place.
- Menu ad albero / wizard multi-step.
- Qualsiasi situazione in cui servono dati strutturati nel payload (`callback_data` tipo `order:123:confirm`).

### Regole tecniche critiche
1. **Rispondi SEMPRE alla callback con `answerCallbackQuery`**, anche senza parametri: altrimenti il client mostra uno spinner di caricamento sul pulsante fino a ~1 minuto.
2. Progetta il `callback_data` come **mini-protocollo con prefissi** (es. `prefisso:id:azione`) per instradare le callback ai giusti handler. Ricorda il limite di 64 byte: metti nel payload solo ID e azione, lo stato va nel tuo database.
3. Dopo un'azione conclusiva, **modifica o rimuovi la keyboard** dal messaggio (es. sostituisci i pulsanti con "✅ Confermato") per evitare che l'utente riprema pulsanti su messaggi vecchi.
4. Gestisci le callback su **messaggi obsoleti** (l'utente può scrollare indietro e premere pulsanti di giorni prima): valida sempre lo stato lato server.

---

## 5. Rapporto tra comandi e reply keyboard

**Non esiste alcuna regola — né nella documentazione ufficiale né di fatto — che imponga corrispondenza 1:1 tra comandi e pulsanti della reply keyboard.** Sono due canali d'accesso complementari alle stesse funzioni.

### Principi
1. **I comandi fondamentali esistono sempre, con o senza keyboard.** `/start` e `/help` devono funzionare comunque: sono la via di recupero se la keyboard si perde.
2. **Non serve un pulsante per ogni comando.** `/start` non ha senso come pulsante; comandi rari o amministrativi non meritano spazio permanente. La keyboard mostra le azioni *frequenti*, il menu comandi elenca *tutto*.
3. **Non serve un comando per ogni pulsante**, ma dare un comando alle sezioni principali è una cortesia per i power user (salto diretto senza navigazione).
4. **Un handler, più trigger.** `/aiuto` e il tap su "❓ Aiuto" devono convergere sulla **stessa funzione**. Mai duplicare la logica: il testo del pulsante e il comando sono solo due entry point.
5. **Etichette umane sui pulsanti, comandi tecnici nel menu.** Il pulsante può dire "📋 I miei ordini", il comando è `/ordini`. Non forzare le etichette a somigliare ai comandi.

### Struttura di esempio
Comandi registrati: `/start`, `/aiuto`, `/impostazioni`, `/ordini` (tutti, con descrizioni).
Reply keyboard: `[📋 Ordini] [⚙️ Impostazioni] [❓ Aiuto]` — niente pulsante Start.
`/start` → messaggio di benvenuto + invio della reply keyboard principale.

---

## 6. Albero decisionale rapido

```
L'azione riguarda un messaggio/elemento specifico o uno stato da modificare?
  → INLINE KEYBOARD (con edit del messaggio, answerCallbackQuery sempre)

È navigazione globale del bot, sempre disponibile?
  → REPLY KEYBOARD (3–6 voci, resize_keyboard, etichette umane)

Serve che sia scopribile, linkabile, usabile nei gruppi o senza UI?
  → COMANDO (registrato su BotFather con descrizione)

Serve input libero di testo dall'utente?
  → ForceReply o semplice richiesta testuale + gestione stato conversazionale
  → input_field_placeholder per guidare l'utente

Serve telefono / posizione / condivisione chat?
  → REPLY KEYBOARD con request_contact / request_location / request_chat
     (solo chat private)
```

---

## 7. Checklist di design per un nuovo bot

- [ ] `/start`, `/help` (+ `/settings` se applicabile) implementati e registrati su @BotFather
- [ ] `/start` idempotente: ripristina stato + re-invia la reply keyboard principale
- [ ] Tutti i comandi registrati con descrizioni chiare (popolano menu button e autocomplete)
- [ ] Reply keyboard principale: max 3–6 voci, `resize_keyboard: true`, etichette con emoji
- [ ] Ogni pulsante reply e il comando equivalente puntano allo **stesso handler**
- [ ] Azioni contestuali, conferme, paginazione, toggle → inline keyboard con `callback_data` a prefissi
- [ ] `answerCallbackQuery` chiamato per **ogni** callback, sempre
- [ ] Dopo azioni conclusive: edit del messaggio / rimozione della inline keyboard
- [ ] Callback su messaggi vecchi gestite con validazione dello stato lato server
- [ ] Nei gruppi: privacy mode attivo, comandi con `@nomebot`, niente reply keyboard globali
- [ ] Nessuna dipendenza dalla keyboard per funzioni critiche: tutto raggiungibile anche via comandi

---

## 8. Anti-pattern da evitare

- ❌ Reply keyboard come clone del menu comandi (ridondante, spreca spazio).
- ❌ Inviare un nuovo messaggio a ogni pressione di un inline button invece di editare quello esistente.
- ❌ Dimenticare `answerCallbackQuery` → spinner infinito lato utente.
- ❌ Logica duplicata tra handler del comando e handler del testo del pulsante.
- ❌ Stato applicativo dentro `callback_data` (64 byte!): mettici solo ID e azione.
- ❌ Nessuna via d'uscita se la keyboard sparisce: `/start` deve sempre rigenerarla.
- ❌ Reply keyboard con troppe voci o troppi livelli: per la navigazione profonda usa inline.
- ❌ Pulsanti inline lasciati attivi su messaggi ormai conclusi.

---

## Fonti

- Telegram Bot Features: https://core.telegram.org/bots/features
- Bot API Reference: https://core.telegram.org/bots/api
- Tutorial ufficiale "From BotFather to Hello World": https://core.telegram.org/bots/tutorial
- Bot FAQ: https://core.telegram.org/bots/faq
