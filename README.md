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
