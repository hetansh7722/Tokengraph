<h1 align="center">Tokengraph</h1>
<p align="center"><b>Stop burning tokens. Start reviewing smarter.</b></p>

`Tokengraph` builds a structural map of your codebase using Tree-sitter, tracks changes incrementally, and gives your AI coding assistant precise, targeted context — so it reads only what actually matters instead of re-scanning your entire project on every task.

Inspired by graph-based approaches to LLM context optimization.

---

## The Problem

AI coding assistants often re-read large parts of a codebase to understand context for a single change. This is slow and expensive.

`Tokengraph` fixes that by pre-computing a structural graph of your code — functions, classes, imports, and their relationships — so relevant context can be pulled instantly instead of reconstructed from scratch every time.

---

<img width="2000" height="1240" alt="image" src="https://github.com/user-attachments/assets/6fcd92b9-c277-4c76-b68f-491d4e1ab933" />


## How It Works

1. **Parse** — Your repository is parsed into an AST using Tree-sitter
2. **Graph** — Functions, classes, and imports become nodes; calls, inheritance, and test coverage become edges, stored in a local SQLite database
3. **Blast radius** — When code changes, the graph traces every caller, dependent, and test that could be affected
4. **Minimal context** — Your AI assistant reads only the relevant slice of the codebase via MCP tools, instead of scanning everything

<img width="2600" height="1700" alt="image" src="https://github.com/user-attachments/assets/5eb3261a-6ac4-4c06-947a-51a7a145b8e1" />
<img width="2000" height="1400" alt="image" src="https://github.com/user-attachments/assets/c7b9d6d4-19ff-4226-8dc1-0e4d6a521245" />


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
