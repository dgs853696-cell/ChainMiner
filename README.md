# ChainMiner: Multi-Agent Collaborative Code Vulnerability Detection & Exploitation-Chain Mining Platform

> LLM-based Static Application Security Testing (LLM-SAST) — upload a source-code project, automatically mine vulnerabilities, verify exploitability, construct **combined exploitation chains**, and generate a penetration summary with a visualized vulnerability graph.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![Node](https://img.shields.io/badge/Node.js-18%2B-green)](https://nodejs.org/)
[![Framework](https://img.shields.io/badge/Flask-SocketIO-orange)](https://flask-socketio.readthedocs.io/)

## Features

- **Multi-Agent pipeline**: Miner (vulnerability mining) → Leader (chain planning) → Reviewer (exploitability verification) → Summary (penetration summary)
- **RAG historical case retrieval**: 742 vulnerability cases + 200 exploitation-chain cases, local embedding (bge-small-zh-v1.5)
- **Combined exploitation chains**: builds attack paths from entry point to security impact; rejected candidates are kept with full reasons (traceable)
- **Real-time visualization**: vulnerability-graph node states update live (discovered → selected → exploitable / rejected), plus one-click Markdown report download

## Quick Start

Requirements: Python ≥ 3.10, Node.js ≥ 18

```bash
# 1. Install backend dependencies (from the project root)
python -m pip install -r requirements.txt

# 2. One-click startup (launches backend + frontend, opens the browser when ready)
python run.py
```

On first run, `run.py` automatically installs frontend dependencies and creates `config.json` from `config.example.json`. Then fill in your OpenAI-compatible API base URL, key and model name on the **Settings page** (or edit `config.json` directly) and upload a sample ZIP to analyze.

- Frontend: http://localhost:3000
- Backend: http://127.0.0.1:5000 (first startup loads the RAG model, ~30 s)
- Stop: press `Ctrl+C` in the run.py window

## Test Dataset

This repository ships the complete test dataset used in our evaluation:

| Folder | Contents | Description |
|---|---|---|
| `漏洞文件/` | 300 ZIP archives | **Vulnerable samples** — real-world web / Solidity / CTF-style source projects containing exploitable vulnerabilities |
| `正常文件/` | 30 ZIP archives | **Normal (control) samples** — structurally similar code archives **without** exploitable vulnerabilities, used to measure false positives |

Every sample is a ready-to-analyze ZIP: upload any of them on the page — no preprocessing required. Ground-truth annotations are available from the author upon request.

## Analysis Modes

| Mode | Description |
|---|---|
| Full Analysis | Multi-Agent + RAG full pipeline (Miner + Leader + Reviewer + Summary) |
| Direct Detection | 1 LLM call per file (fast baseline, no RAG, no collaboration) |
| Single-Agent | Miner only, no multi-Agent chain construction |
| w/o RAG | Historical case retrieval disabled |
| w/o Reviewer | Leader's selection enters the chain directly, exploitability verification skipped |
| w/o State | Decision agents are unaware of the existing chain state (no memory) |

Results of each mode are automatically appended to `result/{mode}.json`, ready for comparative and ablation analysis.

## How It Works

```
Upload ZIP → file filtering / extraction (path-traversal and size checks)
   ↓
① Miner: per-file vulnerability mining (RAG historical cases as reference context)
   ↓
② Leader ↔ Reviewer iterative loop:
   Leader picks the next node to verify (temp=0.8, exploring / reusing historical chains)
   Reviewer verifies exploitability against source evidence (temp=0.1)
   Accepted → added to the chain and state updated; rejected → candidate kept with reason
   ↓
③ Summary: penetration-flow summary + Mermaid diagram based on the final chain
```

## License

Not specified yet; please contact the author before citing.
