# 🧱 cfo-aistack: A Modular, Composable AI Stack

> A fully offline, local-first, pluggable AI runtime built from purpose-driven layers.  
> Each module can run standalone — or compose together via Docker Compose.

---

## ✅ Core Modules

| Module         | Role / Responsibility                                     | Status |
| -------------- | --------------------------------------------------------- | ------ |
| `cfo-vessel`   | Model launcher, memory manager, GPU loader (LLM engine)   | ✅ Done |
| `cfo-embed`    | Embedding encoder, vectorizer (e.g. `e5`)   | ✅ Done |
| `cfo-router`   | Smart router: model selection, RAG orchestration, prompt engineering | ✅ Done |
| `cfo-fast-rag` | Lightweight retrieval API for Qdrant collections          | ✅ Done |

---

## 📦 Combine via `docker-compose.yml`

### Folder structure (recommended)

```

cfo-aistack/
├── docker-compose.yml
├── .env
└── setup.sh ← clone all repos below
├── cfo-vessel/     ← LLM runtime
├── cfo-embed/      ← Embedding encoder API
├── cfo-router/     ← Smart router
├── cfo-fast-rag/   ← Vector search API to Qdrant
└── shared/         ← Shared logs, models, configs (mounted in each)

````

### Example `docker-compose.yml`

```yaml
services:
  vessel:
    build: ./cfo-vessel
    container_name: cfo-vessel
    volumes:
      - ./shared/models:/vessel/models
      - ./shared/logs:/vessel/logs
    ports:
      - "8000:8000"
    environment:
      - NVIDIA_VISIBLE_DEVICES=all

  embed:
    build: ./cfo-embed
    container_name: cfo-embed
    volumes:
      - ./shared/logs:/embed/logs
    ports:
      - "8005:8005"

  router:
    build: ./cfo-router
    container_name: cfo-router
    depends_on:
      - vessel
    ports:
      - "8001:8001"

  rag:
    build: ./cfo-fast-rag
    container_name: cfo-fast-rag
    depends_on:
      - embed
    ports:
      - "8002:8002"

networks:
  default:
    name: cfo-net
````

---

## 🔧 Build Strategy

1. ✅ **Build standalone**

   * Each module should work on its own
   * Confirm working APIs, log output, Docker build

2. 🔗 **Connect by dependency**

   * Use `depends_on` and shared volumes
   * Connect via internal Docker hostnames (`http://embed:8005`, etc)

3. 📦 **Wrap in single repo**

   * Top-level folder contains all modules
   * Compose handles lifecycle, logs, network

4. 🧪 **Add health check scripts**

   * Validate LLMs, embeddings, vector insertions, chaining
   * CLI test tools for prompt/embedding/model/invoke

---

## 🔐 Security + Control

Once deployed, you fully control:

* 🧠 Model lifecycle, usage, memory
* 🔐 Access control (headers, IP filters, token auth)
* 📦 Embedding model choice (instructional, multilingual, domain)
* 🧾 Prompt logging, usage auditing
* 📚 RAG logic and indexing flow
* ⚙️ On-disk model integrity (hash checks, idle unload)

---

## 🏁 Summary

Once complete, `cfo-aistack` becomes a **modular, auditable, offline AI platform**.
Models live and die on your terms. Prompts are visible. No cloud inference, no data leak.

Workflow:

```
Prompt → Router → Fast-RAG/Vessel or both → Output
```

Everything runs on your metal. Fully portable. Fully yours.

---

## 🔹 Disclaimer

This repository is a **blueprint/demo**.
Modules are described with examples; full implementations are maintained privately.

## 🔹 License

All Rights Reserved. See [LICENSE.md](./LICENSE.md).

