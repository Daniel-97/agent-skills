# Agent Readiness — dettagli per categoria

Riferimento esteso, da consultare quando serve spiegare o motivare le scelte
implementative all'utente. Il SKILL.md rimanda qui per non appesantirsi.

## 1. Discoverability
Le basi: `robots.txt`, `sitemap.xml`, RSS. In più, `llms.txt`: un riassunto
machine-readable del sito (chi sei, quali pagine esistono, one-line
description di ogni post, policy sui contenuti). Deve essere **generato
dinamicamente** dal contenuto reale — se scritto a mano una volta, va
disallineato al primo nuovo post pubblicato.

`llms-full.txt` è la versione estesa: tutto il corpus markdown pubblico in un
unico file, utile per un agente che vuole ingerire tutto in un colpo solo.

## 2. Content Accessibility (Markdown content negotiation)
Ogni pagina HTML ha un alternate in Markdown. Un middleware controlla
l'header `Accept` della richiesta in ingresso: se il client preferisce
`text/markdown`, il server risponde con il markdown pulito, senza che
l'agente debba fare scraping/parsing del DOM.

Ogni risposta HTML include header `Link` che pubblicizzano:
- il canonical HTML
- l'alternate markdown
- il service-desc (catalogo API)

```
Link: <https://tuosito/pagina/>; rel="canonical"; type="text/html"
Link: <https://tuosito/pagina.md>; rel="alternate"; type="text/markdown"
Link: <https://tuosito/api/catalog.json>; rel="service-desc"; type="application/json"
```

## 3. Bot Access Control
Il `robots.txt` distingue esplicitamente:
- **bot di retrieval/ricerca** (OAI-SearchBot, ChatGPT-User, PerplexityBot,
  Claude-User...) → consentiti, perché servono per rispondere a domande
  dell'utente citando la fonte
- **crawler di training** (GPTBot, CCBot, Google-Extended, anthropic-ai,
  ClaudeBot...) → bloccati, se non vuoi che i tuoi contenuti finiscano nei
  dataset di training

In più, un header `Content-Signal` su ogni risposta dichiara la policy a
livello di singola pagina (es. `search=yes, ai-input=yes, ai-train=no`), non
solo a livello di file `robots.txt` globale.

Questa distinzione (retrieval sì, training no) è una scelta di default
ragionevole per un sito personale che vuole essere citabile ma non "ingerito"
per addestrare modelli — ma è una preferenza personale: chiedi sempre
conferma all'utente prima di applicarla, potrebbe voler permettere anche il
training.

## 4. Protocol Discovery
Endpoint `.well-known/` e API che un agente può interrogare per sapere "cosa
sa fare" il sito, senza indovinare:

- `/.well-known/agent-readiness.json` — manifest: policy, rappresentazioni
  disponibili, inventario capacità con stato per ciascuna.
- `/.well-known/skills/index.json` — skill disponibili (tipicamente: lettura
  read-only del contenuto in markdown).
- `/.well-known/webmcp.json` — dichiara onestamente l'assenza di tool/azioni
  transazionali, se non ce ne sono. Sapere che la risposta è "niente" evita
  probing inutile da parte dell'agente.
- `/api/catalog.json` — catalogo di documenti ed endpoint disponibili.
- `/api/openapi.json` — spec OpenAPI della superficie API del sito (se
  esiste).

**Principio chiave**: per le capacità NON implementate (OAuth discovery,
DNS-AID, MCP server, protocolli di commerce...) non restituire un 404 muto.
Servi comunque l'endpoint con una risposta strutturata: `"status":
"unavailable"` + `"reason"`. Un 404 lascia l'agente a indovinare se si tratta
di "non esiste ancora" o "non esisterà mai"; una risposta esplicita elimina
l'ambiguità.

## 5. Commerce (quasi sempre vuoto per un sito personale)
Protocolli come x402, MPP, UCP, ACP riguardano transazioni/pagamenti agentici.
Per un blog/portfolio personale, quasi certamente non si applicano.
Dichiaralo esplicitamente come `unavailable` con una nota, invece di ometterlo
dal manifest. Dichiarare cosa NON supporti è informativo tanto quanto
dichiarare cosa supporti.

## Perché tutto questo conta
Con questa impostazione, un agente può:
- richiedere `Accept: text/markdown` e ottenere contenuto pulito, senza
  scraping;
- leggere `llms.txt` e capire l'intero sito in pochi secondi;
- leggere `agent-readiness.json` e sapere subito quali protocolli sono
  supportati, senza tentativi a vuoto;
- rispettare le preferenze sull'uso dei contenuti (search sì, training no)
  perché dichiarate in modo machine-readable e verificabile per pagina.
