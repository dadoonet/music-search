# Humming search

Code inspired from blog [Searching by Music: Leveraging Vector Search for Music Information Retrieval](https://www.elastic.co/blog/searching-by-music-leveraging-vector-search-audio-information-retrieval), originally created by Alex Salgado.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dadoonet/music-search/blob/main/elastic_music_search.ipynb)

## Available embedding providers

The notebook supports four embedding providers — pick one by editing the `PROVIDER_NAME` variable at the top of the notebook:

| `PROVIDER_NAME` | Backend | Where it runs | Modalities | Extra requirement |
|---|---|---|---|---|
| `"jina"` (default) | Elasticsearch managed inference (`.jina-embeddings-v5-omni-small`) | Inside Elasticsearch | audio + text | none |
| `"jina-nano"` | Elasticsearch managed inference (`.jina-embeddings-v5-omni-nano`) | Inside Elasticsearch | audio + text | none |
| `"panns"` | PANNs (`panns-inference`, AudioSet checkpoint) | Local PyTorch | audio only | downloads ~300 MB checkpoint on first run |
| `"gemini"` | Google Gemini multimodal (`gemini-embedding-2`) | Google Cloud API | audio + text | `GOOGLE_API_KEY` in `.env` |

Each provider writes to its own Elasticsearch index (`music-jina`, `music-jina-nano`, `music-panns`, `music-gemini`), so you can switch back and forth without re-indexing.

The notebook ends with a comparison cell that runs the same audio query (`dataset/bella_ciao_david.mp3`) through all 3 providers side by side and displays a table of top-5 hits with embedding durations — useful for evaluating speed and result quality. Populate the 3 indices first by running the notebook once with each `PROVIDER_NAME` value.

## Installation

Create a `.env` file which contains the following content and replace the values `<ELASTICSEARCH_URL>` and `<ELASTICSEARCH_API_KEY>` with the Elasticsearch URL (Serverless is supported) and the API Key you want to use:

```env
ELASTICSEARCH_URL="<ELASTICSEARCH_URL>"
ELASTICSEARCH_API_KEY="<ELASTICSEARCH_API_KEY>"
```

If you want to use login / password authentification, you can define `ELASTICSEARCH_USER` and `ELASTICSEARCH_PASSWORD` variables instead of `ELASTICSEARCH_API_KEY`. For example, if you started a local Elasticsearch instance with the default user `elastic` and password `changeme`, you can define:

```env
ELASTICSEARCH_URL="https://localhost:9200"
ELASTICSEARCH_USER="elastic"
ELASTICSEARCH_PASSWORD="changeme"
```

If you plan to use the `"gemini"` provider, also add a Google API key:

```env
GOOGLE_API_KEY="<GOOGLE_API_KEY>"
```

### Python dependencies

The notebook installs everything it needs via `%pip install`. The base setup needs `elasticsearch`, `python-dotenv`, and `pandas` (used by the final comparison cell). Provider-specific extras:

- **Jina** — no extra dependency, the inference runs inside Elasticsearch.
- **Gemini** — `google-genai`.
- **PANNs** — `panns-inference`, `librosa`, `torch` (heavier; downloads a ~300 MB checkpoint on first use).

You can comment out the `%pip install` lines for providers you don't intend to use.
