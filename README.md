# Humming search

Code from blog [Searching by Music: Leveraging Vector Search for Music Information Retrieval](https://www.elastic.co/fr/blog/searching-by-music-leveraging-vector-search-audio-information-retrieval)

**Author:** Alex Salgado

**Modified by:** David Pilato

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dadoonet/music-search/blob/main/elastic_music_search.ipynb)

## Installation

Create a `.env` file which contains the following content and replace the values `<ES_URL>` and `<ES_APIKEY>` with the Elasticsearch URL (Serverless is supported) and the API Key you want to use:

```env
ES_URL="<ES_URL>"
ES_APIKEY="<ES_APIKEY>"
```

If you want to use login / password authentification, you can define `ES_USER` and `ES_PASSWORD` variables instead of `ES_APIKEY`. For example, if you started a local Elasticsearch instance with the default user `elastic` and password `changeme`, you can define:

```env
ES_URL="https://localhost:9200"
ES_USER="elastic"
ES_PASSWORD="changeme"
```
