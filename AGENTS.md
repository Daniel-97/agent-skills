# AGENTS.md

Guida per agenti autonomi che lavorano su questo repository.

## Cos'è questo repo

Raccolta personale di skill agentiche in formato SKILL.md. Ogni skill è una
cartella indipendente sotto `skills/` che istruisce un agente (es. Claude)
su come svolgere un compito specifico.

## Struttura

skills/<nome-skill>/
├── SKILL.md         (obbligatorio) — frontmatter YAML (name, description) + istruzioni
├── scripts/         (opzionale)    — codice eseguibile per task deterministici
├── references/      (opzionale)    — documentazione caricata solo quando serve
└── assets/          (opzionale)    — template, icone, font usati nell'output

## Regole per creare o modificare una skill

1. Nome cartella in kebab-case, coerente con il campo `name` nel frontmatter.
2. Il frontmatter YAML è obbligatorio e deve contenere `name` e `description`.
   La `description` è il meccanismo principale di attivazione: deve dichiarare
   sia COSA fa la skill sia QUANDO va usata, in modo esplicito e specifico.
3. Tenere SKILL.md sotto le 500 righe. Se il contenuto cresce, spostare il
   materiale di dettaglio in references/ e linkarlo dal corpo del SKILL.md.
4. Non duplicare skill: se una skill esistente copre già il caso d'uso,
   estenderla invece di crearne una nuova.
5. Dopo aver creato o modificato una skill, aggiornare la tabella indice
   in README.md (colonna Skill, Descrizione, Ultimo aggiornamento).

## Convenzioni di commit

- feat(skill-name): descrizione breve   → nuova skill o funzionalità
- fix(skill-name): descrizione breve    → correzione
- chore: descrizione breve              → manutenzione repo (struttura, README, ecc.)

## Cosa NON fare

- Non inserire credenziali, API key o dati sensibili in nessun file.
- Non creare skill con side-effect distruttivi senza documentarli chiaramente
  nel corpo del SKILL.md (es. cancellazione file, chiamate API che modificano dati).
- Non rimuovere o rinominare una skill esistente senza aggiornare i riferimenti
  nel README.

## Testing di una skill

Se la skill produce output verificabili (trasformazioni di file, generazione
di codice, ecc.), aggiungere alcuni prompt di test nella cartella della skill
(es. references/test-cases.md) prima di considerarla completa.