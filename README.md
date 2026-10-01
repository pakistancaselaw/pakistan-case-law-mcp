# Pakistan Case Law — MCP server

**Connect Claude, ChatGPT, Gemini, Grok, Cursor or any MCP client to 230,000+ reported Pakistani
judgments** — Supreme Court, Federal Shariat Court, High Courts and tribunals, 1970 onward — so your
assistant searches real case law and walks the citation graph instead of answering from memory.

- **Endpoint:** `https://pakistancaselaw.com/mcp`
- **Transport:** Streamable HTTP (SSE also works)
- **Authentication:** none — free, read-only, no API key, no account
- **Official MCP registry:** [`com.pakistancaselaw/caselaw`](https://registry.modelcontextprotocol.io/v0/servers?search=pakistancaselaw)
- **Website:** [pakistancaselaw.com](https://pakistancaselaw.com) · full setup guide: [pakistancaselaw.com/for-ai](https://pakistancaselaw.com/for-ai)

This repository holds the documentation and the registry manifest (`server.json`). The server itself
is hosted — there is nothing to install.

## Connect in one minute

### Claude (web and desktop)
Settings → **Connectors** → **+** → **Add custom connector** → name it `Pakistan Case Law`, URL
`https://pakistancaselaw.com/mcp`, leave OAuth fields empty → **Add**. Works on the Free plan (one
custom connector) and on Pro, Max, Team and Enterprise.

### Claude Code
```bash
claude mcp add --transport http caselaw https://pakistancaselaw.com/mcp
```

### ChatGPT (Plus, Pro, Business, Enterprise, Edu — web)
Settings → Security and login → turn on **Developer mode** → create an app with the MCP server URL
`https://pakistancaselaw.com/mcp` and **No Authentication**.

### Gemini CLI
```bash
gemini mcp add --transport http caselaw https://pakistancaselaw.com/mcp
```
Gemini Spark (Google AI Pro / Ultra): gemini.google.com → Settings & help → Connected Apps →
**Custom apps for Spark** → add the URL above.

### Grok
[grok.com/connectors](https://grok.com/connectors) → **New Connector** → **Custom** → paste the URL →
no authentication.

### Cursor, VS Code and other MCP clients
```json
{
  "mcpServers": {
    "caselaw": {
      "type": "http",
      "url": "https://pakistancaselaw.com/mcp"
    }
  }
}
```
A client that only runs local commands can bridge with `npx -y mcp-remote https://pakistancaselaw.com/mcp`.

## Tools

All seven are read-only and annotated as such (`readOnlyHint: true`, `destructiveHint: false`).

| Tool | What it does |
|---|---|
| `caselaw_search` | Full-text search across all judgments, with optional filters: **court** ("Supreme Court", "LHC", "Sindh" ...), **judge** (spelling-tolerant - "Asif Khosa" finds Asif Saeed Khan Khosa; the reply names the judge matched), **journal** (with years, a volume such as SCMR 2025) and **year_from / year_to**. Keywords are optional when a filter is given: `court='Supreme Court'` alone lists the latest Supreme Court judgments. Sort: relevance, newest, court, or recently_added. For the current law on a point, keep relevance and add `year_from`. |
| `caselaw_search_questions` | Find judgments by the legal question they settle — e.g. *"Can bail once granted be cancelled?"* — rather than by the words they contain. Filters: court, judge, year_from, year_to. |
| `caselaw_lookup_citation` | The judgment at an exact law-report citation (journal, year, page), e.g. PLD 1995 Supreme Court 34. |
| `caselaw_get_case` | One judgment: metadata, outcome and headnote (`section='summary'`), or the full text too (`section='full'`). |
| `caselaw_get_citations` | Walk the citation graph forward — the precedents a judgment relied on. |
| `caselaw_get_cited_by` | Walk it backward — later judgments that cite this one, and how it was treated. |
| `caselaw_most_cited` | The most-cited landmark judgments — a good entry point to the leading authorities. |

The server also offers a prompt, `research_proposition`, that runs a multi-step research plan:
re-phrase and search several ways, triage on the headnotes, then walk the citation graph both ways.

## Example prompts

- *"Using the Pakistan Case Law tools, find Supreme Court authority on whether bail once granted can be
  cancelled without strong grounds, and tell me which cases are still followed."*
- *"Look up PLD 1995 Supreme Court 34, summarise what it decided, then show me the later cases that cite it."*
- *"What are the leading Pakistani authorities on maintainability of a constitutional petition where an
  alternate remedy exists? Give me the contrary authority as well."*
- *"Trace the line of authority on khula — start from the most-cited case and work forward to the
  current position."*
- *"What has Justice Asif Saeed Khan Khosa held on bail? List his judgments and what each decided."*
- *"Show me the latest Supreme Court judgments, and the pre-arrest bail cases reported since 2025."*

## Coverage and limits

- 230,000+ reported judgments, new ones added daily. Citations follow the Pakistani law reports
  (PLD, SCMR, CLC, YLR, MLD, PCrLJ, PLC, PTD and others).
- Headnotes and "questions settled" cover part of the corpus and are growing.
- Rate limit: 240 tool calls per minute per session; over it, the reply says so and sets `Retry-After`.

## How to rely on the results

Every result carries its citation and a link to the judgment page, so any answer can be checked
against the source. Headnotes are a finding aid, not authority. This is research assistance, not legal
advice: verify every authority against the official law report before relying on it in practice.

## Privacy

No account and no personal data. The server receives only the search terms or judgment ids your client
sends in a tool call — never your conversation, files or chat history. Full policy:
[pakistancaselaw.com/privacy](https://pakistancaselaw.com/privacy) · Terms:
[pakistancaselaw.com/terms](https://pakistancaselaw.com/terms).

## About

Pakistan Case Law is built and maintained by
**[Hamadullah Shah](https://www.linkedin.com/in/hamadullah-shah-8a906a1a4), Advocate**, as a free public
resource for Pakistani lawyers, students and researchers.

Questions, problems or a client that will not connect: **contact@pakistancaselaw.com**, or open an issue
in this repository.

## Licence

The documentation and configuration in this repository are MIT-licensed (see `LICENSE`). The judgments
are public documents of the courts; use of the website and the server is governed by the
[Terms of Use](https://pakistancaselaw.com/terms).
