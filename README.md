<h1 align="center">Tokengraph</h1>

<p align="center"><b>Stop burning tokens. Start reviewing smarter.</b></p>

`Tokengraph` builds a structural map of your codebase using Tree-sitter, tracks changes incrementally, and gives your AI coding assistant precise, targeted context — so it reads only what actually matters instead of re-scanning your entire project on every task.

Inspired by graph-based approaches to LLM context optimization.

---

## The Problem

AI coding assistants often re-read large parts of a codebase to understand context for a single change. This is slow and expensive.

`Tokengraph` fixes that by pre-computing a structural graph of your code — functions, classes, imports, and their relationships — so relevant context can be pulled instantly instead of reconstructed from scratch every time.

---

## How It Works

1. **Parse** — Your repository is parsed into an AST using Tree-sitter
2. **Graph** — Functions, classes, and imports become nodes; calls, inheritance, and test coverage become edges, stored in a local SQLite database
3. **Blast radius** — When code changes, the graph traces every caller, dependent, and test that could be affected
4. **Minimal context** — Your AI assistant reads only the relevant slice of the codebase via MCP tools, instead of scanning everything

---
<img width="1229" height="1150" alt="architecture_dark" src="https://github.com/user-attachments/assets/3c6f8948-ea1d-45a2-975b-0df0222001e9" />
<svg viewBox="0 0 1300 850" xmlns="http://www.w3.org/2000/svg" font-family="Segoe Print, Comic Sans MS, cursive" xmlns:c2pa="http://c2pa.org/manifest"><metadata><c2pa:manifest>AAAWgmp1bWIAAAAeanVtZGMycGEAEQAQgAAAqgA4m3EDYzJwYQAAABZcanVtYgAAAEdqdW1kYzJtYQARABCAAACqADibcQN1cm46YzJwYTpkZjIzZTIyMi1jYjZiLTQ5MzgtOWQ4MC0yM2M3NTE4NWQ4YWMAAAADl2p1bWIAAAApanVtZGMyYXMAEQAQgAAAqgA4m3EDYzJwYS5hc3NlcnRpb25zAAAAALxqdW1iAAAARGp1bWRjYm9yABEAEIAAAKoAOJtxE2MycGEuaW5ncmVkaWVudC52MwAAAAAYYzJzaP+dsfMcgBRpWSRmJuWKklUAAABwY2JvcqNpZGM6Zm9ybWF0bWltYWdlL3N2Zyt4bWxqaW5zdGFuY2VJRHgseG1wOmlpZDpjNmY1NWQ1Yi04NDlmLTRkOGQtOTc4MC02YWJkZDAyYTYzMDJscmVsYXRpb25zaGlwaHBhcmVudE9mAAAB4mp1bWIAAABBanVtZGNib3IAEQAQgAAAqgA4m3ETYzJwYS5hY3Rpb25zLnYyAAAAABhjMnNoT5XnzK8m8Iuml/w5AI+0AgAAAZljYm9yomdhY3Rpb25zgqJmYWN0aW9ua2MycGEub3BlbmVkanBhcmFtZXRlcnOha2luZ3JlZGllbnRzgaJjdXJseC1zZWxmI2p1bWJmPWMycGEuYXNzZXJ0aW9ucy9jMnBhLmluZ3JlZGllbnQudjNkaGFzaFgg4Kv8iE7zU3rhJZWHOfhyG2WMs2bJ/Dx1uNwal0574WGkZmFjdGlvbngdY29tLmFudGhyb3BpYy5jbGF1ZGUucHJvdmlkZWRqcGFyYW1ldGVyc6F4H2NvbS5hbnRocm9waWMub3JpZ2luLWNvbmZpZGVuY2VndW5rbm93bmtkZXNjcmlwdGlvbnhmQ2xhdWRlIHByb3ZpZGVkIHRoaXMgZmlsZSBhdCB0aGUgcmVxdWVzdCBvZiBhIHVzZXIgYW5kIG1heSBoYXZlIGNyZWF0ZWQgb3IgbW9kaWZpZWQgdGhlIGZpbGUgY29udGVudHMubXNvZnR3YXJlQWdlbnShZG5hbWVmQ2xhdWRlcmFsbEFjdGlvbnNJbmNsdWRlZPUAAADIanVtYgAAAEBqdW1kY2JvcgARABCAAACqADibcRNjMnBhLmhhc2guZGF0YQAAAAAYYzJzaA6rcTBZy7qBRAtfKTOjacYAAACAY2JvcqVjYWxnZnNoYTI1NmNwYWRNAAAAAAAAAAAAAAAAAGRoYXNoWCDGHCt/ObOmZIxzJAnbSemo/qI0/AK+iupoV0XUy22sl2RuYW1lbmp1bWJmIG1hbmlmZXN0amV4Y2x1c2lvbnOBomVzdGFydBiwZmxlbmd0aBkeBAAAAj5qdW1iAAAAJ2p1bWRjMmNsABEAEIAAAKoAOJtxA2MycGEuY2xhaW0udjIAAAACD2Nib3KlY2FsZ2ZzaGEyNTZpc2lnbmF0dXJleE1zZWxmI2p1bWJmPS9jMnBhL3VybjpjMnBhOmRmMjNlMjIyLWNiNmItNDkzOC05ZDgwLTIzYzc1MTg1ZDhhYy9jMnBhLnNpZ25hdHVyZWppbnN0YW5jZUlEeCx4bXA6aWlkOjNjM2ZhOTBlLWVmZTItNDM1MS04NjVhLTUwNWNkMDVmZThlOHJjcmVhdGVkX2Fzc2VydGlvbnODomN1cmx4LXNlbGYjanVtYmY9YzJwYS5hc3NlcnRpb25zL2MycGEuaW5ncmVkaWVudC52M2RoYXNoWCDgq/yITvNTeuEllYc5+HIbZYyzZsn8PHW43BqXTnvhYaJjdXJseCpzZWxmI2p1bWJmPWMycGEuYXNzZXJ0aW9ucy9jMnBhLmFjdGlvbnMudjJkaGFzaFggCIc1LfwMH8WTW1xXNb5lg3oBRA73KSo681rDSJw6s96iY3VybHgpc2VsZiNqdW1iZj1jMnBhLmFzc2VydGlvbnMvYzJwYS5oYXNoLmRhdGFkaGFzaFggN3iWRXi18JH+3OawvjrPim5g6zF961UuqqbJgLKJXcp0Y2xhaW1fZ2VuZXJhdG9yX2luZm+jZG5hbWVvQW50aHJvcGljIEZpbGVzZ3ZlcnNpb25lMS4wLjBrc3BlY1ZlcnNpb25lMi40LjAAABA4anVtYgAAAChqdW1kYzJjcwARABCAAACqADibcQNjMnBhLnNpZ25hdHVyZQAAABAIY2JvctKEWQISogEmGCFZAgowggIGMIIBjaADAgECAhRA5aAK7sI50L64g/oGQgU9Z1UTADAKBggqhkjOPQQDAzBJMRcwFQYDVQQKEw5BbnRocm9waWMsIFBCQzEuMCwGA1UEAxMlQW50aHJvcGljIENvbnRlbnQgQ3JlZGVudGlhbHMgUm9vdCBDQTAeFw0yNjA4MDcxODQzNTZaFw0yODA4MDYxOTQzNTZaMEQxFzAVBgNVBAoTDkFudGhyb3BpYywgUEJDMSkwJwYDVQQDEyBBbnRocm9waWMgQ2xhdWRlIENvbnRlbnQgU2lnbmluZzBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABJh6CmvLUBgFFNU0vUKlOVtE6djd17L5SuwX0LemFisBM3dkd/3cyjxFA3Qo5S46fX0/ihY0VZ7mfb9KF703t5OjWDBWMA4GA1UdDwEB/wQEAwIHgDAVBgNVHSUEDjAMBgorBgEEAYPoXgIBMAwGA1UdEwEB/wQCMAAwHwYDVR0jBBgwFoAUzlHiBIFOZFsj+OPEz5o+nMHXXMIwCgYIKoZIzj0EAwMDZwAwZAIwMXMdFJ4BetLLVY7ORuE9noqbbAZOZn/aArXyTwFAZfKrPzxF2vPoJNf1+UCdg1XGAjBwX1zd9WGqYkqmL5SFqw1QySjr1zJfpJM9+1rdDwSPLMOPOjKuiXjoU/pUUeG9RwmhY3BhZFkNngAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAPZYQKZsAid2LXebreCedbLxFJe7Gi5htsIjGqBZa930wDKyPGKAMIxNDose4XfkbciVW1gAZxeMsx4LgRXD7xJL6J0=</c2pa:manifest></metadata>
  <defs>
    <style>
      .title { font-size: 36px; font-weight: bold; fill: #e9ecef; }
      .subtitle { font-size: 16px; fill: #adb5bd; }
      .label { font-size: 18px; fill: #e9ecef; font-weight: bold; }
      .sub { font-size: 13px; fill: #ced4da; }
      .tiny { font-size: 11px; fill: #868e96; }
      .arrow { stroke: #ced4da; stroke-width: 2.5; fill: none; marker-end: url(#arrowhead); }
      .darrow { stroke: #868e96; stroke-width: 2; fill: none; stroke-dasharray: 6,4; marker-end: url(#arrowhead2); }
    </style>
    <marker id="arrowhead" markerWidth="10" markerHeight="10" refX="8" refY="3" orient="auto">
      <path d="M0,0 L8,3 L0,6" fill="none" stroke="#ced4da" stroke-width="1.5"/>
    </marker>
    <marker id="arrowhead2" markerWidth="10" markerHeight="10" refX="8" refY="3" orient="auto">
      <path d="M0,0 L8,3 L0,6" fill="none" stroke="#868e96" stroke-width="1.5"/>
    </marker>
  </defs>

  <rect width="1300" height="850" fill="#1e1e2e"/>
  <text x="650" y="55" text-anchor="middle" class="title">tokengraph — Architecture</text>
  <text x="650" y="82" text-anchor="middle" class="subtitle">Repository → Tree-sitter → SQLite Graph → Blast Radius → Minimal Review Set</text>

  <!-- Step 1: Repository -->
  <rect x="40" y="140" width="200" height="120" rx="14" fill="#1c3a5e" stroke="#4dabf7" stroke-width="2.5"/>
  <text x="140" y="180" text-anchor="middle" class="label">Repository</text>
  <text x="140" y="205" text-anchor="middle" class="sub">.py .ts .go .rs</text>
  <text x="140" y="222" text-anchor="middle" class="sub">.java .rb ...</text>
  <text x="140" y="245" text-anchor="middle" class="tiny">your source files</text>
  <path d="M240,200 L280,200" class="arrow"/>

  <!-- Step 2: Tree-sitter -->
  <rect x="285" y="140" width="220" height="120" rx="14" fill="#4a2b1e" stroke="#ff922b" stroke-width="2.5"/>
  <text x="395" y="175" text-anchor="middle" class="label">Tree-sitter</text>
  <text x="395" y="198" text-anchor="middle" class="label">Parser</text>
  <text x="395" y="220" text-anchor="middle" class="sub">builds an AST per file</text>
  <text x="395" y="240" text-anchor="middle" class="tiny">30+ language grammars</text>
  <path d="M505,200 L545,200" class="arrow"/>

  <!-- Step 3: Extraction -->
  <rect x="550" y="140" width="220" height="120" rx="14" fill="#3a2b4a" stroke="#9775fa" stroke-width="2.5"/>
  <text x="660" y="175" text-anchor="middle" class="label">Fact</text>
  <text x="660" y="198" text-anchor="middle" class="label">Extraction</text>
  <text x="660" y="220" text-anchor="middle" class="sub">functions, classes,</text>
  <text x="660" y="238" text-anchor="middle" class="sub">calls, imports</text>
  <path d="M770,200 L810,200" class="arrow"/>

  <!-- Step 4: SQLite Graph -->
  <rect x="815" y="140" width="220" height="120" rx="14" fill="#2b3a1e" stroke="#82c91e" stroke-width="2.5"/>
  <text x="925" y="175" text-anchor="middle" class="label">SQLite</text>
  <text x="925" y="198" text-anchor="middle" class="label">Graph</text>
  <text x="925" y="220" text-anchor="middle" class="sub">nodes + edges</text>
  <text x="925" y="238" text-anchor="middle" class="tiny">.tokengraph/graph.db</text>

  <path d="M925,260 L925,300" class="arrow"/>

  <!-- Row 2: hashing / incremental loop -->
  <rect x="815" y="305" width="220" height="100" rx="14" fill="#4a1e2b" stroke="#ff6b6b" stroke-width="2.5"/>
  <text x="925" y="335" text-anchor="middle" class="label">SHA-256</text>
  <text x="925" y="357" text-anchor="middle" class="label">Hash Check</text>
  <text x="925" y="378" text-anchor="middle" class="sub">skip unchanged files</text>
  <path d="M815,355 L280,355" class="darrow"/>
  <text x="550" y="345" text-anchor="middle" class="tiny">on save / commit: only re-parse changed files</text>

  <path d="M925,405 L925,445" class="arrow"/>

  <!-- Step 5: Blast Radius -->
  <rect x="815" y="450" width="220" height="120" rx="14" fill="#4a3e1e" stroke="#fcc419" stroke-width="2.5"/>
  <text x="925" y="485" text-anchor="middle" class="label">Blast Radius</text>
  <text x="925" y="508" text-anchor="middle" class="sub">BFS traversal, N hops</text>
  <text x="925" y="528" text-anchor="middle" class="sub">callers · dependents · tests</text>
  <path d="M815,510 L780,510" class="arrow"/>

  <!-- Step 6: Minimal Review Set -->
  <rect x="550" y="450" width="220" height="120" rx="14" fill="#1e4a2e" stroke="#40c057" stroke-width="2.5"/>
  <text x="660" y="483" text-anchor="middle" class="label">Minimal</text>
  <text x="660" y="505" text-anchor="middle" class="label">Review Set</text>
  <text x="660" y="527" text-anchor="middle" class="sub">only relevant files</text>
  <path d="M550,510 L515,510" class="arrow"/>

  <!-- Step 7: MCP Server -->
  <rect x="285" y="450" width="220" height="120" rx="14" fill="#1e3a4a" stroke="#22b8cf" stroke-width="2.5"/>
  <text x="395" y="483" text-anchor="middle" class="label">MCP Server</text>
  <text x="395" y="505" text-anchor="middle" class="sub">exposes graph via</text>
  <text x="395" y="523" text-anchor="middle" class="sub">standardized tool calls</text>
  <path d="M285,510 L245,510" class="arrow"/>

  <!-- Step 8: AI Assistant -->
  <rect x="40" y="450" width="200" height="120" rx="14" fill="#1c3a5e" stroke="#4dabf7" stroke-width="2.5"/>
  <text x="140" y="490" text-anchor="middle" class="label">AI Assistant</text>
  <text x="140" y="513" text-anchor="middle" class="sub">Claude Code, Cursor,</text>
  <text x="140" y="530" text-anchor="middle" class="sub">Windsurf, etc.</text>

  <!-- feedback loop bottom -->
  <path d="M140,570 L140,650 L925,650 L925,575" class="darrow"/>
  <text x="530" y="640" text-anchor="middle" class="tiny">assistant asks a question → tool call → graph query → blast radius computed again</text>

  <!-- legend -->
  <rect x="40" y="710" width="1220" height="110" rx="10" fill="#25253a" stroke="#495057" stroke-width="1.5"/>
  <text x="65" y="735" class="sub" font-weight="bold" fill="#e9ecef">Key properties</text>
  <text x="65" y="760" class="tiny">- Persistent -- graph is stored on disk, not rebuilt per session</text>
  <text x="65" y="780" class="tiny">- Incremental -- only changed files are re-parsed via hash comparison</text>
  <text x="65" y="800" class="tiny">- Local -- SQLite file, no external services or cloud dependency</text>
  <text x="700" y="760" class="tiny">- Structural, not semantic -- real call/import/inheritance edges,</text>
  <text x="700" y="778" class="tiny">  not text-similarity search</text>
  <text x="700" y="800" class="tiny">- Token savings come from filtering + summarizing before the AI ever sees it</text>
</svg>

## Quick Start

```bash
pip install tokengraph
tokengraph install    # auto-detects and configures supported AI coding platforms
tokengraph build       # parse your codebase
```

Then ask your AI assistant:

```
Build the code review graph for this project
```

To target a specific platform:

```bash
tokengraph install --platform cursor       # configure only Cursor
tokengraph install --platform claude-code  # configure only Claude Code
```

Requires Python 3.10+.

---

## Features

| Feature                   | Details                                                                       |
|----------------------------|--------------------------------------------------------------------------------|
| Incremental updates        | Re-parses only changed files                                                  |
| Multi-language support     | Python, TypeScript/JavaScript, Go, Rust, Java, and more via Tree-sitter        |
| Blast-radius analysis      | Shows exactly which functions, classes, and files are affected by any change  |
| Auto-update hooks          | Graph updates on file edit and git commit                                     |
| Local storage              | SQLite file in `.tokengraph/` — no external database, no cloud dependency     |
| Interactive visualization  | Force-directed graph view of your codebase structure                          |
| MCP integration            | Works with Claude Code, Cursor, Windsurf, Zed, Continue, OpenCode              |

---

## Usage

Run `tokengraph --help` for the full command reference, including:

- `tokengraph build` — full graph build (re-parse all files)
- `tokengraph update` — incremental update (only changed files)
- `tokengraph watch` — watch for changes and auto-update
- `tokengraph status` — show graph statistics
- `tokengraph visualize` — generate an interactive HTML graph visualization
- `tokengraph serve` — start the MCP server

---

## Contributing

```bash
git clone https://github.com/hetansh7722/tokengraph.git
cd tokengraph
python3 -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
pytest
```

---

## License

MIT. See [LICENSE](LICENSE).
