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
| [startup-idea-validation](skills/startup-idea-validation/SKILL.md) | Guida un founder, una fase alla volta, nella validazione di un'idea di startup (Idea Stage): problem framing, devil's advocate, mappa competitiva, customer discovery, fino a un verdetto GO/PIVOT/NO-GO con Problem Validation Report | 2026-07-02 |
| [latex-cv-reviewer](skills/latex-cv-reviewer/SKILL.md) | Revisiona un CV LaTeX (.tex) in 6 fasi (recruiter test, struttura, allineamento ATS/keyword, impact statement, posizionamento aziende target, polish finale), modificando solo il contenuto testuale | 2026-07-02 |
| [astro-agent-ready-website](https://github.com/Daniel-97/agent-skills/blob/main/skills/astro-agent-ready-website/SKILL.md) | Crea siti personali (portfolio/blog) con Astro + TypeScript ottimizzati per SEO, AEO e agent readiness (llms.txt, content negotiation Markdown, robots.txt bot AI, endpoint .well-known), con workflow spec-driven e template pronti. Isprirazione: | 2026-07-24 |

## Fonti & Credits

> `astro-agent-ready-website` è basata su ["Building an Agent Friendly Personal Website with Specs"](https://dev.to/aws/building-an-agent-friendly-personal-website-with-specs-11b4) di Salih Güler, e sul relativo repo di riferimento [salihgueler/salih-dev](https://github.com/salihgueler/salih-dev).

## Convenzioni

- **Naming**: le cartelle delle skill usano kebab-case (es. `pdf-extractor`, `commit-message-writer`).
- **Frontmatter obbligatorio**: ogni `SKILL.md` deve iniziare con un frontmatter YAML contenente almeno `name` e `description`. La `description` è il meccanismo principale di attivazione della skill: deve dichiarare in modo esplicito sia cosa fa sia quando va usata.
- Per le regole complete su come creare, modificare e testare le skill in questo repo, vedi [AGENTS.md](AGENTS.md).
