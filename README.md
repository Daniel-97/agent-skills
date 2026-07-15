# agent-skills

Raccolta personale di skill agentiche in formato [SKILL.md](https://github.com/anthropics/skills), sviluppate nel tempo per estendere le capacità di agenti AI (es. Claude) su compiti specifici e ripetibili. Il repo contiene inoltre le mie convenzioni personali di scrittura del codice, in formato agent-ready (vedi [Convenzioni personali di codice](#convenzioni-personali-di-codice)).

## Struttura

Il repo è organizzato in due aree principali:

```
agent-skills/
├── skills/          — le skill agentiche, una per cartella
└── conventions/     — convenzioni personali di codice (AGENTS.md)
```

Ogni skill vive nella propria cartella sotto `skills/<nome-skill>/`:

```
skills/<nome-skill>/
├── SKILL.md         (obbligatorio) — frontmatter YAML (name, description) + istruzioni
├── scripts/         (opzionale)    — codice eseguibile per task deterministici
├── references/      (opzionale)    — documentazione di dettaglio caricata solo quando serve
└── assets/          (opzionale)    — template, icone, font usati nell'output
```

Solo `SKILL.md` è obbligatorio: le altre cartelle si aggiungono solo se la skill ne ha effettivamente bisogno.

## Indice skill

| Skill | Descrizione | Ultimo aggiornamento |
|---|---|---|
| [example-skill](skills/example-skill/SKILL.md) | Skill di esempio/template da copiare per crearne di nuove | 2026-07-01 |
| [spec-architect](skills/spec-architect/SKILL.md) | Guida conversazionale, una decisione alla volta, dall'idea grezza a una specifica software completa (SRS) | 2026-07-01 |
| [build-architect](skills/build-architect/SKILL.md) | Trasforma una specifica consolidata (SRS) in artefatti pratici per l'implementazione: piano milestone, schema database, AGENTS.md, README, scaffold di progetto | 2026-07-01 |
| [startup-idea-validation](skills/startup-idea-validation/SKILL.md) | Guida un founder, una fase alla volta, nella validazione di un'idea di startup (Idea Stage): problem framing, devil's advocate, mappa competitiva, customer discovery, fino a un verdetto GO/PIVOT/NO-GO con Problem Validation Report | 2026-07-02 |
| [latex-cv-reviewer](skills/latex-cv-reviewer/SKILL.md) | Revisiona un CV LaTeX (.tex) in 6 fasi (recruiter test, struttura, allineamento ATS/keyword, impact statement, posizionamento aziende target, polish finale), modificando solo il contenuto testuale | 2026-07-02 |
| [telegram-bot-ux](skills/telegram-bot-ux/SKILL.md) | Best practice UX per bot Telegram (comandi, reply keyboard, inline keyboard). Usa SEMPRE questa skill quando l'utente progetta o sviluppa un bot Telegram e deve strutturare menu, pulsanti, comandi o flussi. | 2026-07-13 |

## Convenzioni personali di codice

La cartella [`conventions/`](conventions/) contiene le mie convenzioni personali di scrittura del codice, pensate per essere language-agnostic e riutilizzabili in qualunque progetto:

- [`conventions/AGENTS.md`](conventions/AGENTS.md) — il documento completo: naming, stile e struttura del codice (early return, no magic numbers, layering, KISS/DRY/YAGNI), commenti, error handling e le 7 regole per i commit message di Chris Beams, con riferimenti e letture di approfondimento.

Il file è pensato per un doppio uso: come documentazione leggibile e condivisibile, e come istruzioni da includere globalmente in un coding agent (es. `~/.claude/CLAUDE.md` o l'`AGENTS.md` globale dell'agente), così che ogni sessione di coding rispetti le stesse convenzioni senza doverle ripetere.

> Nota: non confondere `conventions/AGENTS.md` (convenzioni personali di codice, riusabili ovunque) con l'[`AGENTS.md`](AGENTS.md) alla radice del repo, che contiene le regole specifiche per lavorare *su questo repo*.

## Skill gemelle: spec-architect + build-architect

`spec-architect` e `build-architect` sono pensate per essere usate in sequenza, come due metà dello stesso percorso:

- **spec-architect** guida dall'idea grezza a una specifica software completa (l'SRS) — risponde alla domanda "cosa costruire".
- **build-architect** parte da una spec consolidata (idealmente l'SRS prodotto da spec-architect) e genera gli artefatti pratici per l'implementazione — risponde alla domanda "come costruirlo".

Flusso tipico: "Ho un'idea per un'app" → `spec-architect` produce l'SRS → "Ora trasforma questa spec in un piano di implementazione" → `build-architect` produce milestone, schema DB, AGENTS.md, README e scaffold di progetto.

Non è obbligatorio passare da spec-architect: `build-architect` può accettare anche una spec consolidata proveniente da altre fonti; se invece i requisiti sono ancora vaghi o contraddittori, rimanda a `spec-architect` prima di generare gli artefatti di build.

## Come aggiungere una nuova skill

1. Crea la cartella `skills/<nome-skill>/` usando il naming kebab-case.
2. Scrivi `SKILL.md` partendo dal template in [skills/example-skill/SKILL.md](skills/example-skill/SKILL.md), con frontmatter YAML (`name`, `description`) e le istruzioni per l'agente.
3. Aggiungi `scripts/`, `references/` o `assets/` solo se necessari.
4. Aggiorna la tabella indice qui sopra con la nuova riga.
5. Fai commit con messaggio `feat(nome-skill): descrizione breve`.

## Convenzioni

- **Naming**: le cartelle delle skill usano kebab-case (es. `pdf-extractor`, `commit-message-writer`).
- **Frontmatter obbligatorio**: ogni `SKILL.md` deve iniziare con un frontmatter YAML contenente almeno `name` e `description`. La `description` è il meccanismo principale di attivazione della skill: deve dichiarare in modo esplicito sia cosa fa sia quando va usata.
- Per le regole complete su come creare, modificare e testare le skill in questo repo, vedi [AGENTS.md](AGENTS.md).
