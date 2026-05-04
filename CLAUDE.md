# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Jupyter notebook demo for music/audio search using Elasticsearch vector search. Audio files are encoded as base64 data URIs and sent to Elastic's managed inference endpoint (`.jina-embeddings-v5-omni-small`), which generates embeddings server-side. Documents are stored using a `semantic_text` field and queried with a `semantic` query. Supports both audio-to-audio and text-to-audio search.

## Environment Setup

Requires Python 3.14+. To create the `.venv` and install dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -qU elasticsearch python-dotenv notebook
```

The `.env` file (gitignored) must contain:

```env
ELASTICSEARCH_URL="..."
ELASTICSEARCH_API_KEY="..."   # or ELASTICSEARCH_USER + ELASTICSEARCH_PASSWORD for local
```

No external model API key is needed — inference runs inside Elasticsearch.

## Running the Notebook

```bash
source .venv/bin/activate
jupyter notebook elastic_music_search.ipynb
```

The notebook is also runnable in Google Colab via the badge in the README.

## Architecture

The single notebook `elastic_music_search.ipynb` follows this flow:

1. **Settings** — loads `.env`, sets `INDEX_NAME = "music"` and `AUDIO_PATH = "dataset"`
2. **Inference check** — verifies `GET _inference/embedding/.jina-embeddings-v5-omni-small` is available
3. **Index creation** — creates/recreates an Elasticsearch index with a `semantic_text` field `audio-embedding` (inference_id: `.jina-embeddings-v5-omni-small`), plus `keyword` field `path`, `text` fields `title` and `genre`
4. **Audio encoding** — `audio_to_data_uri(filepath)` reads a `.wav` file and returns a `data:audio/wav;base64,…` URI
5. **Demo cell** — `show_embedding_demo(audio_filepath, text_query)` calls `POST _inference/embedding/.jina-embeddings-v5-omni-small` directly to display generated vectors (for demo purposes)
6. **Indexing** — walks `dataset/` for `.wav` files, assigns a genre by matching filename against a keyword list, stores the base64 URI in `audio-embedding` (Elasticsearch runs inference automatically)
7. **Search** — `query_audio_vector(input_data, index_name)` runs a `semantic` query on `audio-embedding`; input can be a base64 audio URI or plain text; results displayed with score, genre, path, and an inline audio player

## Dataset

`dataset/` contains `.wav` files named `{song}_{style}.wav` (e.g. `bella_ciao_piano-solo.wav`, `mozart_symphony25_string-quartet.wav`). Genre is auto-detected from the filename. `bella_ciao_david.wav` and `mozart_symphony25_prompt.wav` are the query/humming reference files.
