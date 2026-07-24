---
name: astro-agent-ready-website
description: >
  Guida la creazione di un sito personale (portfolio/blog) con Astro + TypeScript,
  ottimizzato per SEO, AEO e "agent readiness" (leggibilità da parte di agenti AI).
  Usa SEMPRE questa skill quando l'utente vuole creare, ristrutturare o rendere
  "agent-friendly"/"AI-friendly" un sito personale, un portfolio, o un blog —
  anche se non menziona esplicitamente Astro, SEO, AEO, llms.txt o "agent
  readiness" per nome. Fornisce un workflow spec-driven (spec.md prima del
  codice) e template pronti da copiare, tra cui spec.md, llms.txt, robots.txt
  con distinzione bot retrieval/training, endpoint .well-known
  (agent-readiness.json, webmcp.json, skills/index.json), api/catalog.json,
  middleware Astro di content negotiation Markdown, AGENTS.md, schema
  frontmatter dei post. Attiva anche per richieste come "voglio che il mio
  sito sia leggibile da ChatGPT/Claude/Perplexity", "aggiungi llms.txt al mio
  sito", o "come blocco i crawler di training AI ma non i bot di ricerca".
---

# Astro Agent-Ready Personal Website

Skill per costruire (o retrofittare) un sito personale con Astro + TypeScript
che sia contemporaneamente: veloce da realizzare con un assistente AI,
SEO-solido, e leggibile/citabile in modo affidabile dagli agenti AI (AEO /
agent readiness). Basata sul workflow descritto in "Building an Agent
Friendly Personal Website with Specs" (Salih Güler) e sul repo di riferimento
salihgueler/salih-dev.

## Quando usarla

- L'utente vuole creare da zero un sito personale/portfolio/blog.
- L'utente ha già un sito e vuole aggiungere SEO/AEO/agent-readiness.
- L'utente menziona `llms.txt`, "content negotiation", "robots.txt per bot AI",
  "far leggere il mio sito a un agente", "citazioni AI", ecc.
- L'utente chiede di scrivere uno `spec.md` per un progetto web prima di
  iniziare a programmare.

Se lo stack richiesto NON è Astro, applica comunque i principi (spec-driven,
le 5 categorie di agent readiness, content negotiation) ma adatta
l'implementazione tecnica al framework scelto — i template in `assets/`
restano un riferimento concettuale valido ovunque, solo il middleware Astro va
riscritto per lo stack target.

## Workflow (segui questo ordine)

### 1. Raccogli il minimo indispensabile
Prima di generare qualsiasi file, chiedi (se non già note):
- Nome/dominio del sito, bio breve, cosa deve contenere (blog? conferenze?
  progetti? mappa location?).
- Se ha contenuti esistenti da importare (dev.to, Medium, altro) e da dove.
- Policy sui contenuti: permettere training AI oppure no (default consigliato:
  search=yes, ai-input=yes, ai-train=no — vedi motivazioni sotto).
- Non-goal per questa iterazione (es. niente deploy, niente test, niente auth).

Non fare troppe domande in una volta: raggruppale, oppure usa i default
ragionevoli indicati nei template e segnalali esplicitamente.

### 2. Genera `spec.md` PRIMA di scrivere codice
Copia `assets/spec.md.template`, compilalo con le info raccolte. Regole da
rispettare quando lo compili:
- Usa linguaggio forte: `MUST` / `MUST NOT` / `SHOULD` / `SHOULD NOT` in
  maiuscolo per ogni regola vincolante. Non ammorbidire ("potremmo forse...").
- Le scelte tecniche (Astro, TypeScript) sono già decise: non proporre
  "valutiamo alternative".
- Mantieni la sezione "Non-Goals" esplicita: aiuta a rispettare lo scope.
- Mostra lo spec compilato all'utente e chiedi conferma prima di procedere
  all'implementazione.

### 3. Pianifica prima di implementare
Per ogni funzionalità non banale, proponi un piano breve con criteri di
accettazione e attendi l'approvazione dell'utente prima di scrivere codice.
Questo vale soprattutto per: struttura contenuti, endpoint agent-readiness,
script di import.

### 4. Implementa la base SEO
- `sitemap.xml`, `rss.xml` generati dai contenuti (non statici).
- Meta tag/Open Graph per pagina.
- URL canonici (importante se un post è anche pubblicato altrove).
- Pagine di listing per categorie/tag.

### 5. Implementa agent readiness (le 5 categorie)
Usa i file in `assets/` come base, sostituendo `<dominio>`/`<NOME>` con i
valori reali. Leggi `references/agent-readiness-details.md` per la
spiegazione di ciascuna categoria e il "perché" prima di generare i file, così
puoi spiegarli all'utente se chiede chiarimenti.

| Categoria | File/asset da usare | Note |
|---|---|---|
| Discoverability | `llms.txt.template`, `robots.txt.template` (+ sitemap/rss del passo 4) | `llms.txt`/`llms-full.txt` vanno **generati dinamicamente** a build time dai contenuti, non scritti a mano una tantum |
| Content Accessibility | `content-negotiation.middleware.ts.template` | Ogni pagina HTML deve avere un alternate `.md` |
| Bot Access Control | `robots.txt.template` (sezione retrieval vs training) | Aggiungi anche l'header `Content-Signal` (già nel middleware) |
| Protocol Discovery | `agent-readiness.json.template`, `webmcp.json.template`, `skills-index.json.template`, `catalog.json.template` | Vanno serviti sotto `/.well-known/...` e `/api/...` |
| Commerce | dentro `agent-readiness.json.template` (campo `commerce`) | Per un sito personale, lascia `"status": "unavailable"` con motivo — non ometterlo |

**Regola chiave da applicare sempre**: qualunque capacità elencata ma non
implementata deve rispondere con uno stato `"unavailable"` esplicito e un
motivo (vedi esempio nel template `agent-readiness.json`). Mai un 404 muto:
un agente che riceve 404 continua a fare tentativi, uno che riceve
"unavailable" con motivo si ferma.

### 6. Genera `AGENTS.md`
Copia `assets/AGENTS.md.template` nella root del repo: fissa le regole di
processo e l'elenco degli endpoint agent-readiness da non rompere in futuro,
per qualsiasi assistente che lavorerà sul repo dopo di te.

### 7. Import contenuti esistenti (se richiesto)
Se l'utente ha contenuti su dev.to/Medium/altro:
- Scrivi uno script di import dedicato per fonte (`scripts/import-<fonte>.mjs`).
- Lo script DEVE richiedere metadati revisionati manualmente (categoria,
  riassunto AI) per ogni contenuto — se mancano, fallisce esplicitamente,
  non usa default silenziosi.
- Gestisci la deduplicazione: se lo stesso post esiste su più fonti, fai
  merge (aggiungi source link) invece di creare un duplicato.
- Usa `assets/blog-post-frontmatter.md.template` come schema del frontmatter.

### 8. Polish finale
Chiedi esplicitamente "cosa manca?" prima di considerare l'iterazione chiusa.
Tipicamente restano: fix responsive/mobile, piccoli overflow CSS. Falli per
ultimi, sono rapidi e non bloccano il resto.

## Checklist di verifica finale

- [ ] `spec.md` scritto e approvato PRIMA del codice
- [ ] `robots.txt`, `sitemap.xml`, `rss.xml` presenti
- [ ] `llms.txt` + `llms-full.txt` generati dinamicamente dal contenuto reale
- [ ] Content negotiation attiva (`Accept: text/markdown` → risposta markdown)
- [ ] Header `Link` (canonical/alternate/service-desc) su ogni pagina HTML
- [ ] Header `Content-Signal` su ogni risposta
- [ ] `robots.txt` distingue bot di retrieval (consentiti) da crawler di training (bloccati)
- [ ] `/.well-known/agent-readiness.json`, `/.well-known/webmcp.json`,
      `/.well-known/skills/index.json` serviti
- [ ] `/api/catalog.json`, `/api/openapi.json` serviti
- [ ] Ogni capacità non supportata risponde "unavailable" + motivo (mai 404 muto)
- [ ] `AGENTS.md` presente nella root
- [ ] Import contenuti (se applicabile): dedup + metadati revisionati a mano

## File disponibili in questa skill

```
assets/
  spec.md.template                          — spec iniziale da compilare
  llms.txt.template                         — riassunto machine-readable del sito
  robots.txt.template                       — bot retrieval vs training
  agent-readiness.json.template             — manifest capacità (.well-known)
  webmcp.json.template                      — dichiarazione onesta "nessun tool"
  skills-index.json.template                — skill read-only del sito
  catalog.json.template                     — catalogo documenti/API
  content-negotiation.middleware.ts.template — middleware Astro Accept:text/markdown
  AGENTS.md.template                        — istruzioni persistenti per coding assistant
  blog-post-frontmatter.md.template         — schema frontmatter dei post
references/
  agent-readiness-details.md                — spiegazione estesa delle 5 categorie, da leggere se serve motivare le scelte all'utente
```

Copia sempre i template in output reali (sostituendo placeholder come
`<dominio>`, `<NOME>`), non lasciarli con i placeholder nel repo finale.
