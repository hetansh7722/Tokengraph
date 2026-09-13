# CLAUDE.md - Project Context for Claude Code

## Project Overview

**tokengraph** is a persistent, incrementally-updated knowledge graph for token-efficient code reviews with Claude Code. It parses codebases using Tree-sitter, builds a structural graph in SQLite, and exposes it via MCP tools and prompts.

## Architecture

- **Core Package**: `tokengraph/` (Python 3.10+)
  - `parser.py` â€” Tree-sitter multi-language AST parser (18 languages including Vue SFC, Solidity, Dart, R, Perl)
  - `graph.py` â€” SQLite-backed graph store (nodes, edges, BFS impact analysis)
  - `tools.py` â€” 22 MCP tool implementations
  - `main.py` â€” FastMCP server entry point (stdio transport), registers 22 tools + 5 prompts
  - `incremental.py` â€” Git-based change detection, file watching
  - `embeddings.py` â€” Optional vector embeddings (Local sentence-transformers, Google Gemini, MiniMax)
  - `visualization.py` â€” D3.js interactive HTML graph generator
  - `cli.py` â€” CLI entry point (install, build, update, watch, status, visualize, serve, wiki, detect-changes, register, unregister, repos, eval)
  - `flows.py` â€” Execution flow detection and criticality scoring
  - `communities.py` â€” Community detection (Leiden algorithm or file-based grouping) and architecture overview
  - `search.py` â€” FTS5 hybrid search (keyword + vector)
  - `changes.py` â€” Risk-scored change impact analysis (detect-changes)
  - `refactor.py` â€” Rename preview, dead code detection, refactoring suggestions
  - `hints.py` â€” Review hint generation
  - `prompts.py` â€” 5 MCP prompt templates (review_changes, architecture_map, debug_issue, onboard_developer, pre_merge_check)
  - `wiki.py` â€” Markdown wiki generation from community structure
  - `skills.py` â€” Skill definitions for Claude Code plugin
  - `registry.py` â€” Multi-repo registry with connection pool
  - `migrations.py` â€” Database schema migrations (v1-v5)
  - `tsconfig_resolver.py` â€” TypeScript path alias resolution

- **VS Code Extension**: `tokengraph-vscode/` (TypeScript)
  - Separate subproject with its own `package.json`, `tsconfig.json`
  - Reads from `.tokengraph/graph.db` via SQLite

- **Database**: `.tokengraph/graph.db` (SQLite, WAL mode)

## Key Commands

```bash
# Development
uv run pytest tests/ --tb=short -q          # Run tests (486 tests)
uv run ruff check tokengraph/        # Lint
uv run mypy tokengraph/ --ignore-missing-imports --no-strict-optional

# Build & test
uv run tokengraph build              # Full graph build
uv run tokengraph update             # Incremental update
uv run tokengraph status             # Show stats
uv run tokengraph serve              # Start MCP server
uv run tokengraph wiki               # Generate markdown wiki
uv run tokengraph detect-changes     # Risk-scored change analysis
uv run tokengraph register <path>    # Register repo in multi-repo registry
uv run tokengraph repos              # List registered repos
uv run tokengraph eval               # Run evaluation benchmarks
```

## Code Conventions

- **Line length**: 100 chars (ruff)
- **Python target**: 3.10+
- **SQL**: Always use parameterized queries (`?` placeholders), never f-string values
- **Error handling**: Catch specific exceptions, log with `logger.warning/error`
- **Thread safety**: `threading.Lock` for shared caches, `check_same_thread=False` for SQLite
- **Node names**: Always sanitize via `_sanitize_name()` before returning to MCP clients
- **File reads**: Read bytes once, hash, then parse (TOCTOU-safe pattern)

## Security Invariants

- No `eval()`, `exec()`, `pickle`, or `yaml.unsafe_load()`
- No `shell=True` in subprocess calls
- `_validate_repo_root()` prevents path traversal via repo_root parameter
- `_sanitize_name()` strips control characters, caps at 256 chars (prompt injection defense)
- `escH()` in visualization escapes HTML entities including quotes and backticks
- SRI hash on D3.js CDN script tag
- API keys only from environment variables, never hardcoded

## Test Structure

- `tests/test_parser.py` â€” Parser correctness, cross-file resolution
- `tests/test_graph.py` â€” Graph CRUD, stats, impact radius
- `tests/test_tools.py` â€” MCP tool integration tests
- `tests/test_visualization.py` â€” Export, HTML generation, C++ resolution
- `tests/test_incremental.py` â€” Build, update, migration, git ops
- `tests/test_multilang.py` â€” 18 language parsing tests (including Vue, Solidity, Dart, R, Perl)
- `tests/test_embeddings.py` â€” Vector encode/decode, similarity, store
- `tests/test_flows.py` â€” Execution flow detection and criticality
- `tests/test_communities.py` â€” Community detection, architecture overview
- `tests/test_changes.py` â€” Risk-scored change analysis
- `tests/test_refactor.py` â€” Rename preview, dead code, suggestions
- `tests/test_search.py` â€” FTS5 hybrid search
- `tests/test_hints.py` â€” Review hint generation
- `tests/test_prompts.py` â€” MCP prompt template tests
- `tests/test_wiki.py` â€” Wiki generation
- `tests/test_skills.py` â€” Skill definitions
- `tests/test_registry.py` â€” Multi-repo registry
- `tests/test_migrations.py` â€” Database migrations
- `tests/test_eval.py` â€” Evaluation framework
- `tests/test_tsconfig_resolver.py` â€” TypeScript path resolution
- `tests/test_integration_v2.py` â€” v2 pipeline integration test
- `tests/fixtures/` â€” Sample files for each supported language

## CI Pipeline

- **lint**: ruff on Python 3.10
- **type-check**: mypy
- **security**: bandit scan
- **test**: pytest matrix (3.10, 3.11, 3.12, 3.13) with 50% coverage minimum
