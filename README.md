# Humming search

Code from blog [Searching by Music: Leveraging Vector Search for Music Information Retrieval](https://www.elastic.co/fr/blog/searching-by-music-leveraging-vector-search-audio-information-retrieval)

**Author:** Alex Salgado

**Modified by:** David Pilato

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dadoonet/music-search/blob/main/elastic_music_search.ipynb)

## Installation

Create a `.env` file which contains the following content and replace the values `CLOUD_ID` and `PASSWORD` with the Elastic Cloud ID and the password. If you want to use another user than `elastic`, also modify `ES_USER` variable.

If you want to run Elasticsearch locally, remove the `ES_CLOUD_ID` variable and set `ES_URL` to `https://localhost:9200`.

```env
ES_CLOUD_ID="CLOUD_ID"
ES_URL="https://localhost:9200"
ES_USER="elastic"
ES_PASSWORD="PASSWORD"
```
