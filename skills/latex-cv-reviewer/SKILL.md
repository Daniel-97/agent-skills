---
name: latex-cv-reviewer
description: "Revisiona e ottimizza un CV gia' scritto in LaTeX (.tex), applicando un workflow strutturato in 6 fasi ispirato al punto di vista di un recruiter: prima impressione, struttura, allineamento linguistico/keyword rispetto a una job description, riscrittura delle esperienze in termini di impatto misurabile, posizionamento rispetto ad aziende target (verificato via ricerca web reale, mai inventato), ed editing finale di stile. L'input e' sempre un file .tex esistente e l'output e' un altro file .tex, con la stessa struttura, classe del documento e comandi/pacchetti LaTeX invariati: viene modificato solo il contenuto testuale. Nessuna compilazione o generazione di PDF. Usa SEMPRE questa skill quando l'utente carica o menziona un file .tex che e' un CV/resume e chiede di rivederlo, ottimizzarlo, adattarlo a un ruolo o un'azienda specifica, o migliorarne il contenuto, anche se non usa parole come 'skill' o 'workflow'."
---

# LaTeX CV Reviewer

## Principio chiave

L'input è sempre un CV LaTeX già esistente. Modifica solo il CONTENUTO TESTUALE (frasi, bullet point, summary, keyword). Non toccare `\documentclass`, `\usepackage`, macro/comandi custom del template, o la struttura degli ambienti (`\cventry`, `\resumeItem`, ecc.). Non compilare il file: l'output è solo `.tex`, la verifica finale spetta all'utente.

**Escaping**: quando riscrivi testo dentro un comando esistente, ricorda `%`→`\%`, `&`→`\&`, `_`→`\_`, `#`→`\#`, e controlla che le parentesi graffe restino bilanciate. Non raddoppiare un escape già presente nel testo originale.

## Input necessari

1. **Il file .tex del CV** — obbligatorio.
2. **Job description del ruolo target** — solo per la Fase 3. Se assente, chiedila o salta la fase segnalandolo.
3. **Aziende target** — solo per la Fase 5, opzionale.

**Template multi-file**: se il file caricato contiene `\input{}` o `\include{}`, chiedi anche i file richiamati (il contenuto vero è lì) e restituisci tutti i file modificati.

Non bloccare l'intero workflow per input mancanti che servono solo a fasi specifiche: esegui quelle possibili e segnala le altre come saltate.

## Lunghezza

I template CV sono quasi sempre pensati per una pagina esatta. Mantieni ogni voce riscritta entro un ±15% della lunghezza originale, privilegiando wording più denso invece di aggiungere frasi nuove. Se il CV cresce comunque in modo significativo, segnalalo nel riepilogo finale.

## Lingua

Rileva la lingua del CV originale e mantienila in tutto il testo riscritto, anche se la job description è in un'altra lingua. Il riepilogo in chat può restare nella lingua della conversazione.

## Workflow in 6 fasi (sequenziali, scope non sovrapposti)

**Fase 1 — Recruiter Attention Test** (solo diagnosi): valuta il CV come un recruiter nei primi 10 secondi — cosa colpisce, cosa è dimenticabile. Feedback in chat, nessuna modifica al file.

**Fase 2 — Structural Weakness Scan** (diagnosi + modifiche solo dopo conferma): problemi di struttura/ordine/credibilità. Se trovi un problema strutturale concreto, proponi la modifica e chiedi conferma esplicita prima di riordinare qualsiasi sezione — specialmente su template a colonne/sidebar fissa. Se la modifica richiede sia riattivare una sezione commentata SIA spostarla in una posizione diversa, trattale come due operazioni distinte ed esplicite (prima scommenta, poi sposta con un edit separato) — un singolo `str_replace` che fa entrambe le cose insieme rischia di scommentare in-place senza effettivamente spostare nulla. Dopo qualunque modifica strutturale (riordino, riattivazione, spostamento di sezioni), rileggi il file con `view` per confermare la posizione effettiva risultante prima di dichiararla completata: non fidarti della sola operazione di edit.

**Fase 3 — ATS & Language Alignment** (richiede job description): identifica keyword/competenze rilevanti assenti, integra solo dove riflettono esperienza reale. Non promettere che il CV "supererà" un ATS — l'obiettivo è allineamento linguistico onesto, non keyword stuffing.

**Fase 4 — Impact Statement Rebuilder**: riscrivi le esperienze in termini di risultati misurabili. Prima scansiona tutte le voci senza metriche e raggruppa le domande mancanti in un'unica richiesta in chat, invece di chiederle una alla volta. Non inventare numeri.

**Fase 5 — Market Positioning** (richiede aziende target, opzionale): usa ricerca web per dati reali e aggiornati sullo stile/valori delle aziende indicate. Se non trovi info affidabili, dillo — non inventare pattern.

**Fase 6 — Final Polish Pass**: ultimo giro solo su coerenza di tono, ripetizioni, wording vago, frasi corporate abusate. Non toccare più struttura o metriche.

## Output finale

- Il/i file `.tex` rivisto/i in `/mnt/user-data/outputs`, stesso nome + suffisso `_revised` (mantieni la struttura di cartelle originale per i template multi-file)
- Un file `_changes.md` con l'elenco delle modifiche, pensato per essere letto e copiato selettivamente dall'utente nel CV originale (vedi formato sotto)
- Riepilogo breve in chat: quante modifiche totali, fasi saltate e perché, eventuale crescita di lunghezza
- Presenta tutti i file con `present_files`

### Formato del file `_changes.md`

Ogni modifica è un'unità copiabile a sé stante, non prosa discorsiva. Struttura:

1. **Indice in cima**: tabella con colonne `ID | Sezione CV | Fase | Descrizione breve | Attenzione`. La colonna "Attenzione" segnala con ⚠️ le modifiche che contengono un placeholder da completare (es. metriche mancanti) prima di poterle incollare così come sono.

2. **Raggruppamento per sezione del CV** (Profilo, Esperienza, Competenze, ecc.), non per fase — l'utente pensa in termini di "cosa è cambiato nella mia Esperienza", non in termini delle 6 fasi.

3. **Una modifica = un blocco**, con questo schema:
   ```
   ### C[n] — [etichetta breve, es. "Bullet 1, TechCorp Srl"]
   Fase: [numero e nome fase] · [⚠️ contiene placeholder da completare, se applicabile]

   Prima:
   ```latex
   [snippet originale esatto]
   ```
   Dopo:
   ```latex
   [snippet riscritto esatto, pronto da incollare]
   ```
   ```

4. **Granularità atomica**: se una riscrittura combina più interventi indipendenti (es. metrica aggiunta + keyword ATS + polish stilistico), preferisci comunque presentarli come un solo blocco per riga del CV (per restare copiabile 1:1), ma nella descrizione elenca sinteticamente i motivi separati (es. "aggiunta metrica; integrata keyword 'CI/CD' da job description; rimossa frase generica").

5. Non includere nel changelog le fasi saltate, né modifiche non applicate (restrutturazioni proposte ma non confermate dall'utente in Fase 2): quelle restano solo nella conversazione, non nel file.

Questo cambia solo COSA delivera la skill, non altera le 6 fasi né il principio di non toccare pacchetti/macro LaTeX.

## Da evitare

- Non promettere che il CV "supererà" un sistema ATS
- Non inventare metriche, aziende, competenze o pattern di hiring
- Non modificare pacchetti/comandi/macro che non capisci con certezza — se ambigui, lascia invariato e segnala
- Non eseguire le fasi fuori ordine: sono sequenziali, ognuna si basa sulla precedente