<p align="center">
  <img src="docs/assets/banner.jpg" alt="code-review-graph" width="100%" />
</p>

<h1 align="center">code-review-graph</h1>

<p align="center">
<strong>Give Claude a map of your codebase so it stops reading every file.</strong>
</p>

<p align="center">
  <a href="https://github.com/tirth8205/code-review-graph/stargazers"><img src="https://img.shields.io/github/stars/tirth8205/code-review-graph?style=flat-square" alt="Stars"></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square" alt="MIT License"></a>
  <a href="https://github.com/tirth8205/code-review-graph/actions/workflows/ci.yml"><img src="https://github.com/tirth8205/code-review-graph/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="https://www.python.org/"><img src="https://img.shields.io/badge/python-3.10%2B-blue.svg?style=flat-square" alt="Python 3.10+"></a>
  <a href="https://modelcontextprotocol.io/"><img src="https://img.shields.io/badge/MCP-compatible-green.svg?style=flat-square" alt="MCP"></a>
  <a href="#"><img src="https://img.shields.io/badge/version-1.7.0-purple.svg?style=flat-square" alt="v1.7.0"></a>
</p>

---

Claude Code is powerful, but it wastes tokens re-reading your entire codebase on every review and every coding task. `code-review-graph` builds a structural map of your code using [Tree-sitter](https://tree-sitter.github.io/tree-sitter/), tracks it incrementally, and tells Claude exactly which files matter — so it reads 5 files instead of 500.

Benchmarked on real open-source repos: **6.8x fewer tokens for code reviews, up to 49x fewer tokens for coding tasks.**

---

## Install in 45 Seconds

### Option A: Claude Code Plugin (recommended)

```bash
claude plugin add tirth8205/code-review-graph
```

Restart Claude Code. Done.

### Option B: pip

```bash
pip install git+https://github.com/tirth8205/code-review-graph.git
code-review-graph install
```

This creates a `.mcp.json` file in your project that tells Claude Code where to find the MCP server. Restart Claude Code to activate.

> Requires Python 3.10+ and [uv](https://docs.astral.sh/uv/). Install uv with `pip install uv` or `brew install uv`.

---

## First Time in a Project

After installing, open your project in Claude Code and say:

```
Build the code review graph for this project
```

That's it. Takes about 10 seconds for a 500-file project. After that, the graph updates automatically whenever you edit files or make commits — you never need to think about it again.

---

## How It Works (for Normal People)

```
Your codebase has hundreds of files.
Claude doesn't know which ones matter for what you're doing.

Without this plugin:
  Claude reads everything → slow, expensive, shallow understanding

With this plugin:
  1. A graph maps every function, class, import, and dependency
  2. When you ask Claude to review code or make changes,
     it checks the graph first: "What changed? What depends on it?"
  3. Claude reads only those specific files
  4. Result: faster, cheaper, and actually better reviews
     (because it sees the full blast radius, not just the diff)
```

**You don't change how you work.** You still talk to Claude exactly the same way. It just has a map now, so it doesn't get lost.

---

## Benchmarks

All numbers from real tests on 3 production open-source repos — not toy examples.

### Code Reviews: 6.8x Token Reduction

Tested across 6 real git commits. The graph replaces reading entire files with a compact structural summary (156–207 tokens) that includes blast radius, test coverage gaps, and dependency chains.

| Repo | Size | Standard Approach | With Graph | Savings | Review Quality |
|------|-----:|------------------:|-----------:|--------:|:-:|
| [httpx](https://github.com/encode/httpx) | 125 files | 12,507 tokens | 458 tokens | **26.2x** | 9.0 vs 7.0 |
| [FastAPI](https://github.com/fastapi/fastapi) | 2,915 files | 5,495 tokens | 871 tokens | **8.1x** | 8.5 vs 7.5 |
| [Next.js](https://github.com/vercel/next.js) | 27,732 files | 21,614 tokens | 4,457 tokens | **6.0x** | 9.0 vs 7.0 |
| **Average** | | **13,205** | **1,928** | **6.8x** | **8.8 vs 7.2** |

> "Standard approach" = reading all changed files + diff (what you'd do without the graph). Quality scored on accuracy, completeness, bug-catching, and actionable insight (1–10 scale).

### Live Coding Tasks: 14.1x Average, 49x Peak

An AI agent performed 6 real coding tasks (adding features, fixing bugs) across the same repos. The graph told it which files to read and which to skip.

| Task | Repo | With Graph | Without Graph | Savings | Files Skipped |
|------|------|--------:|-----------:|--------:|---:|
| Add rate limiter | httpx | 14,090 | 64,666 | 4.6x | 58 |
| Fix streaming bug | httpx | 14,090 | 64,666 | 4.6x | 59 |
| Add rate limiter | FastAPI | 37,217 | 138,585 | 3.7x | 1,120 |
| Fix streaming bug | FastAPI | 36,986 | 138,585 | 3.7x | 1,121 |
| Add rate limiter | Next.js | 15,049 | 739,352 | **49.1x** | ~16,000 |
| Fix streaming bug | Next.js | 16,135 | 739,352 | **45.8x** | ~16,000 |

The graph pointed to exactly the right files **100% of the time** — zero false leads across all tasks. The bigger your repo, the bigger the savings.

---

## Usage

### Slash Commands (inside Claude Code)

| Command | What it does |
|---------|-------------|
| `/code-review-graph:build-graph` | Build or rebuild the code graph (~10s for 500 files) |
| `/code-review-graph:review-delta` | Review only what changed since last commit |
| `/code-review-graph:review-pr` | Full PR review with blast-radius analysis |

### CLI

```bash
code-review-graph install     # Register MCP server with Claude Code
code-review-graph build       # Parse your entire codebase
code-review-graph update      # Incremental update (only changed files)
code-review-graph status      # Show graph statistics
code-review-graph watch       # Real-time auto-updates as you code
code-review-graph visualize   # Interactive HTML graph visualization
code-review-graph serve       # Start MCP server manually
```

### MCP Tools (Claude uses these automatically)

| Tool | What it does |
|------|-------------|
| `build_or_update_graph_tool` | Build or incrementally update the graph |
| `get_impact_radius_tool` | Show blast radius of changed files |
| `get_review_context_tool` | Token-optimized review context with structural summary |
| `query_graph_tool` | Find callers, callees, tests, imports, inheritance |
| `semantic_search_nodes_tool` | Search code entities by name or meaning |
| `embed_graph_tool` | Compute vector embeddings for semantic search |
| `list_graph_stats_tool` | Graph size and health check |
| `get_docs_section_tool` | Retrieve docs sections with minimal tokens |

---

## Features

| Feature | Details |
|---------|---------|
| **Incremental updates** | Only re-parses changed files. Updates take <2s after initial build. |
| **12 languages** | Python, TypeScript, JavaScript, Go, Rust, Java, C#, Ruby, Kotlin, Swift, PHP, C/C++ |
| **Blast-radius analysis** | See exactly which functions, classes, and files are impacted by any change |
| **Auto-update hooks** | Graph stays current on every file edit and git commit — zero maintenance |
| **Semantic search** | Optional vector embeddings with sentence-transformers |
| **Interactive visualization** | D3.js graph with edge-type toggles and search |
| **No external DB** | Everything in a local SQLite file — no Neo4j, no cloud, no config |
| **Watch mode** | Real-time graph updates as you code |

---

## It Just Works

After the initial `build`, you don't need to think about this plugin. Here's what happens automatically:

- **You edit a file** → hook updates the graph in <2s
- **You make a commit** → hook updates the graph
- **You ask Claude to review code** → Claude checks the graph, reads only impacted files
- **You ask Claude to add a feature** → Claude uses the graph to find exactly where to make changes

No configuration. No maintenance. No commands to remember.

---

## Configuration

Optionally create `.code-review-graphignore` in your repo root to exclude paths:

```
generated/**
*.generated.ts
vendor/**
node_modules/**
```

For semantic search (optional, requires extra dependencies):

```bash
pip install code-review-graph[embeddings]
```

Then ask Claude to "embed the code graph" — this enables searching by meaning, not just name.

---

## Contributing

```bash
git clone https://github.com/tirth8205/code-review-graph.git
cd code-review-graph
python3 -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
pytest
```

**Adding a new language?** Edit `code_review_graph/parser.py` — add your extension to `EXTENSION_TO_LANGUAGE` and node type mappings to `_CLASS_TYPES`, `_FUNCTION_TYPES`, `_IMPORT_TYPES`, `_CALL_TYPES`. Then add a test fixture and submit a PR.

---

## License

MIT — see [LICENSE](LICENSE).

---

<p align="center">
<br>
<strong>Try it now:</strong>
<br><br>
<code>pip install git+https://github.com/tirth8205/code-review-graph.git && code-review-graph install</code>
<br><br>
Build the graph once. Let Claude handle the rest.
</p>
