# Troubleshooting

## Database lock errors
The graph uses SQLite with WAL mode. If you see lock errors:
- Ensure only one build process runs at a time
- The database auto-recovers; just retry
- Delete `.tokengraph/graph.db-wal` and `.tokengraph/graph.db-shm` if corrupt

## Large repositories (>10k files)
- First build may take 30-60 seconds
- Subsequent incremental updates are fast (<2s)
- Add more ignore patterns to `.tokengraphignore`:
  ```
  generated/**
  vendor/**
  *.min.js
  ```

## Missing nodes after build
- Check that the file's language is supported (see [FEATURES.md](FEATURES.md))
- Check that the file isn't matched by an ignore pattern
- Run with `full_rebuild=True` to force a complete re-parse

## Graph seems stale
- Hooks auto-update on edit/commit
- If stale, run `/tokengraph:build-graph` manually
- Check that hooks are configured in `hooks/hooks.json` (see [hooks documentation](../hooks/hooks.json))

## Embeddings not working
- Install with: `pip install tokengraph[embeddings]`
- Run `embed_graph_tool` to compute vectors
- First embedding run downloads the model (~90MB, one time)

## MCP server won't start
- Verify `uv` is installed (`uv --version`; install with `pip install uv` or `brew install uv`)
- Check that `uvx tokengraph serve` runs without errors
- If using a custom `.mcp.json`, ensure it uses `"command": "uvx"` with `"args": ["tokengraph", "serve"]`
- Re-run `tokengraph install` to regenerate the config

## Windows / WSL

- Use forward slashes in paths when passing `repo_root` to MCP tools
- In WSL, ensure `uv` is installed inside WSL (not the Windows version): `curl -LsSf https://astral.sh/uv/install.sh | sh`
- If `uv` is not found after install, add `~/.cargo/bin` to your PATH
- File watching (`tokengraph watch`) may have delays on WSL1 due to filesystem event limitations; WSL2 is recommended
- On Windows native (non-WSL), long path support may need to be enabled: `git config --system core.longpaths true`

## Community detection requires igraph

- Install with: `pip install tokengraph[communities]`
- Without igraph, community detection falls back to file-based grouping (less precise but functional)

## Wiki generation with LLM summaries

- Install with: `pip install tokengraph[wiki]`
- Requires a running Ollama instance for LLM-powered summaries
- Without Ollama, wiki pages are generated with structural information only (no prose summaries)

## Optional dependency groups

If a tool returns an ImportError, install the relevant optional group:
- `pip install tokengraph[embeddings]` for semantic search
- `pip install tokengraph[google-embeddings]` for Google Gemini embeddings
- `pip install tokengraph[communities]` for igraph-based community detection
- `pip install tokengraph[eval]` for evaluation benchmarks (matplotlib)
- `pip install tokengraph[wiki]` for wiki LLM summaries (ollama)
- `pip install tokengraph[all]` for everything
