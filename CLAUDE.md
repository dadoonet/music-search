# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Jupyter notebook demo for music/audio search using Elasticsearch vector search. The notebook supports **three interchangeable embedding providers** selected via the `PROVIDER_NAME` variable; each provider writes to its own Elasticsearch index (`music-jina`, `music-panns`, `music-gemini`). Documents are stored using a `dense_vector` field `audio-embedding` and queried with a `knn` query. Audio→audio search is supported by all three providers; text→audio search is supported by Jina and Gemini (PANNs raises `NotImplementedError`).

## Embedding providers

| `PROVIDER_NAME` | Model | Where inference runs | Modalities | Extra setup |
|---|---|---|---|---|
| `"jina"` (default) | `.jina-embeddings-v5-omni-small` | Inside Elasticsearch (managed inference) | audio + text | none |
| `"panns"` | PANNs (AudioSet checkpoint, 2048 dims, L2-normalized) | Local PyTorch (CPU by default) | audio only | downloads ~300 MB checkpoint on first run |
| `"gemini"` | `gemini-embedding-2` (multimodal) | Google Cloud API | audio + text | `GOOGLE_API_KEY` in `.env` |

The provider abstraction lives in the `EmbeddingProvider` ABC and three subclasses (`JinaProvider`, `PannsProvider`, `GeminiProvider`). Each provides `setup()`, `embed_audio(filepath) -> list[float]`, and `embed_text(text) -> list[float]`.

## Environment Setup

Requires Python 3.14+. To create the `.venv` and install dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -qU elasticsearch python-dotenv notebook
# Optional, depending on the provider you want to try:
pip install -qU google-genai                  # for "gemini"
pip install -qU panns-inference librosa torch # for "panns"
```

The `.env` file (gitignored) must contain:

```env
ELASTICSEARCH_URL="..."
ELASTICSEARCH_API_KEY="..."   # or ELASTICSEARCH_USER + ELASTICSEARCH_PASSWORD for local
GOOGLE_API_KEY="..."          # only required when PROVIDER_NAME = "gemini"
```

## Running the Notebook

```bash
source .venv/bin/activate
jupyter notebook elastic_music_search.ipynb
```

The notebook is also runnable in Google Colab via the badge in the README.

## Architecture

The single notebook `elastic_music_search.ipynb` follows this flow:

1. **Settings** — loads `.env`, sets `PROVIDER_NAME` (default `"jina"`), `AUDIO_PATH = "dataset"`, and reads `GOOGLE_API_KEY`.
2. **Provider setup** — instantiates the chosen provider and calls `provider.setup()` (endpoint check / model download / API key check). `INDEX_NAME` is derived from `provider.index_name`.
3. **Index creation** — creates/recreates an Elasticsearch index with a `dense_vector` field `audio-embedding`, plus `keyword` field `path` and `text` field `title`.
4. **Audio encoding** — `audio_to_data_uri(filepath)` reads a `.mp3` file and returns a `data:audio/mpeg;...` URI (used only by the Jina provider).
5. **Indexing** — walks `dataset/` for `.mp3` files, calls `provider.embed_audio(path)`, stores the resulting vector via `store_in_elasticsearch(...)`.
6. **Search** — `query_audio_vector(embedding, index_name)` runs a `knn` query on `audio-embedding`. Queries can be audio (`provider.embed_audio`) or text (`provider.embed_text`). Results are displayed via `display_hits_table(...)` with an inline audio player.

## Dataset

`dataset/` contains `.mp3` files named `{song}_{style}.mp3` (e.g. `bella_ciao_piano-solo.mp3`, `mozart_symphony25_string-quartet.mp3`). `bella_ciao_david.mp3` and `mozart_symphony25_prompt.mp3` are the query/humming reference files.
