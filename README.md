# agent-skills

Raccolta personale di skill agentiche in formato [SKILL.md](https://github.com/anthropics/skills), sviluppate nel tempo per estendere le capacità di agenti AI (es. Claude) su compiti specifici e ripetibili.

## Struttura

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
