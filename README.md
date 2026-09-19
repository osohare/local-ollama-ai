# Local Ollama AI

A lightweight local AI stack for running Open WebUI with Ollama-backed inference, web search via SearXNG, and vector storage via Qdrant.

## Overview

This project runs the following services together:

- Open WebUI: local chat interface and RAG UI
- Ollama: local LLM runtime on the host machine
- SearXNG: web search backend for RAG-enabled web search
- Qdrant: vector database for retrieval-augmented generation (RAG)

The Docker Compose stack is configured so Open WebUI can connect to Ollama through the host machine and use SearXNG and Qdrant as supporting services.

## Architecture

- Open WebUI is exposed on: http://localhost:3000
- Ollama is expected to run locally on the host at: http://localhost:11434
- SearXNG is internal to the Docker network and is used by Open WebUI for web search
- Qdrant is exposed on: http://localhost:6333

## Prerequisites

Before starting the stack, make sure you have:

- Docker Desktop or Docker Engine installed
- Ollama installed and running locally
- At least one model pulled in Ollama (for example: `llama3.1` or another model you want to use)

Example:

```bash
ollama pull llama3.1
```

## Quick Start

1. Start the services:

```bash
docker compose up -d
```

2. Open the app in your browser:

```text
http://localhost:3000
```

3. In Open WebUI, configure or select an Ollama model and start using the local chat interface.

4. If you want web search or RAG features, ensure the model and embedding settings are available through Ollama.

## Services

### Open WebUI

- Container: `openwebui`
- Port: `3000:8080`
- Main interface for chat, assistant workflows, and local AI usage

### SearXNG

- Container: `searxng`
- Config directory: `./searxng`
- Used by Open WebUI for web search capability

### Qdrant

- Container: `qdrant`
- REST API: `http://localhost:6333`
- gRPC API: `http://localhost:6334`
- Used for embeddings and vector search in RAG workflows

## Configuration Notes

The compose file sets the following environment variables for Open WebUI:

- `OLLAMA_BASE_URL=http://host.docker.internal:11434`
- `RAG_EMBEDDING_ENGINE=ollama`
- `RAG_EMBEDDING_MODEL=nomic-embed-text`
- `ENABLE_WEB_SEARCH=true`
- `WEB_SEARCH_ENGINE=searxng`
- `VECTOR_DB=qdrant`

Note: newer Open WebUI releases use the `ENABLE_WEB_SEARCH` and `WEB_SEARCH_ENGINE` variables; the older `ENABLE_RAG_WEB_SEARCH` / `RAG_WEB_SEARCH_ENGINE` names are legacy and are not recognized by current builds.

The SearXNG config in `searxng/settings.yml` is intentionally minimal and local-first.

## Useful Commands

Start services:

```bash
docker compose up -d
```

Stop services:

```bash
docker compose down
```

View running containers:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

## Troubleshooting

### Open WebUI cannot reach Ollama

Check that Ollama is running on the host machine and that the model exists:

```bash
ollama list
```

If needed, pull a model again:

```bash
ollama pull llama3.1
```

### SearXNG or Qdrant not working

Verify the containers are healthy and running:

```bash
docker compose ps
```

Then inspect logs:

```bash
docker compose logs searxng
```

```bash
docker compose logs qdrant
```

## Notes

- This is designed for local development and experimentation.
- It is not intended as a production deployment configuration.
- The stack can be customized by editing `docker-compose.yml` and the files under `searxng/`.

## License

This project is provided as-is for local development use.
