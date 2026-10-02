<div align="center">

<img src="./assets/hero.svg" alt="Xeneium. AI Engineer working on fine-tuning and ML systems. Adapt the model, prove it improved, ship only what passes." />

[Emotion Detection](https://github.com/Xeneium/Emotion_Detection) · [HINDY](https://github.com/Xeneium/Security_Agent) · [LinkedIn](https://www.linkedin.com/in/sakthi-sabareesh-p-6b530a333/)

</div>

I am Sakthi. I work as an AI engineer on two connected problems: adapting models to a task, and building the systems that decide whether the adapted model is good enough to serve.

A fine-tuned model is a claim. The baseline, the held-out set and the release gate are the evidence. I build those parts.

My fine-tuning work is proprietary, so it does not appear in a public repository. The public work below shows the systems around it: evaluation, artifact handling and serving.

## From data to served model

<div align="center">

<img src="./assets/model-lifecycle.svg" alt="Pipeline from data to served model: adapt and evaluate, export and serve, and controls at the edge. Dashed stages are proprietary." />

</div>

<details>
<summary>Text description of the diagram</summary>

1. Curate: remove duplicates, split the data, check quality and freeze an evaluation set.
2. Adapt: fine-tune the weights and keep the baseline for comparison.
3. Evaluate: score the held-out set against the baseline.
4. Release gate: a checkpoint with a regression does not ship.
5. Export: convert the checkpoint to ONNX FP32.
6. Tag: write a manifest with the file hash and an HMAC-SHA256 tag.
7. Load check: the runtime refuses to load the file unless both match.
8. Serve: Django with ONNX Runtime, plus health and readiness endpoints.
9. Edge controls: the service refuses a changed model file, a request without consent, invalid credentials and excess request volume.

Stages 1 to 4 are proprietary. Stages 5 to 9 come from the Emotion Detection repository, which serves a third-party checkpoint that did not pass through stages 1 to 4. The tag check shows that the file is unchanged since bootstrap on that machine. It does not show where the checkpoint came from.

</details>

## How I work

**Baseline first.** Every adaptation run starts from a measured baseline. Without one, "improved" has no meaning.

**Freeze the evaluation set early.** Choose the held-out set before the first run and keep it out of training.

**Gate the release.** A checkpoint ships when it clears the gate. A regression blocks it, whatever the training loss shows.

## Selected work

### Emotion Detection

A Django service that serves a Vision Transformer emotion classifier through ONNX Runtime. The checkpoint is a third-party fine-tune of ViT-base on FER-2013. This repository covers export, integrity checking and serving.

| Area | Detail |
| :--- | :--- |
| **Model handling** | Checkpoint exported to ONNX FP32. The runtime refuses to load it unless the SHA-256 and HMAC-SHA256 tag match the manifest |
| **Serving** | Django, ONNX Runtime, liveness and readiness endpoints |
| **Controls** | Consent required (HTTP 451), bearer authentication, Redis sliding-window rate limit |
| **Tests** | 23 tests in the inference app |

[Repository](https://github.com/Xeneium/Emotion_Detection)

### HINDY, a SOC memory agent

A FastAPI and React agent that investigates security alerts with a memory of earlier cases, plus an evaluation harness that compares runs with and without that memory. A human analyst keeps the final decision.

| Area | Detail |
| :--- | :--- |
| **Evaluation** | 94 simulated alerts (10 attacks, 44 lookalikes, 40 recurring benign), replayed with and without memory |
| **Scope** | The second rule set came after a review of the first run on the same alerts, so the results are in-sample. The data is simulated. The agent supports a human decision and does not act alone |
| **Results** | Both runs are committed under `results/v1` and `results/v2` |

[Repository](https://github.com/Xeneium/Security_Agent)

### AcademeRAG

A retrieval-augmented question answering application for academic PDFs. It combines a Django REST API, a React and TypeScript client, Milvus retrieval, and Groq or Ollama generation. The application generates answers from retrieved context. Local use still depends on the local backend and a running Ollama service.

The repository is not public. The diagram below is a conceptual summary of the project README, and I have not verified it against code.

<div align="center">

<img src="./assets/academerag-architecture.svg" alt="Conceptual AcademeRAG architecture: ingestion, question path, ranking and generation. Repository not public; not verified against code." />

</div>

<details>
<summary>Text description of the diagram</summary>

1. Ingestion: the application ingests PDFs from a documents folder into a Milvus collection, either a local file or Zilliz Cloud.
2. Question path: the React client calls the Django REST API, which handles JWT authentication and rate limits and offers a JSON route and a streaming route. Retrieval uses the cloud store when it is healthy and a cached local PDF retriever otherwise.
3. Ranking: the project README describes the combination of dense and BM25 results as weighted score fusion. A reranker (bge-reranker-v2-m3, score cutoff 0.2) then re-scores the candidates, and neighboring chunks may join them to preserve context.
4. Generation: the application tries Groq first when configured and falls back to Ollama when it is reachable. Supported syllabus and unit questions use a separate path that returns cleaned PDF text without an LLM summary.

</details>

## Capabilities

| Domain | Detail |
| :--- | :--- |
| **Adaptation** | Dataset curation · supervised fine-tuning · parameter-efficient adaptation · baselines |
| **Evaluation** | Held-out sets · regression gates · retrieval evaluation scripts |
| **Retrieval** | Dense and BM25 hybrid search · cross-encoder reranking |
| **Serving** | ONNX Runtime · Django REST · FastAPI · Docker · health and readiness probes |
| **Stack** | Python · PyTorch · Hugging Face Transformers · scikit-learn |

## Currently working on

- Adaptation runs measured against a frozen baseline, with release gates that block regressions
- Retrieval and answer evaluation that states its method and sample size
- Serving paths that fail closed when an artifact does not pass its checks

## Connect

[LinkedIn](https://www.linkedin.com/in/sakthi-sabareesh-p-6b530a333/) · [GitHub](https://github.com/Xeneium)
