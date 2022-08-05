# Elasticsearch 


## Search queries
Get information about documents in an index
The following query will retrieve all documents that exist in the specified index. This query is a great way to explore the structure and content of your document.

Syntax:

`GET Enter_name_of_the_index_here/_search `

Example:

`GET news_headlines/_search`

## Aggregations Request

### Analyze the data to show the categories of news headlines in our dataset

``` 
GET Enter_name_of_the_index_here/_search 
{
  "aggregations": {
    "Name your aggregation here": {
      "Specify aggregation type here": {
        "field": "Name the field you want to aggregate here",
        "size": State how many buckets you want returned here
      }
    }
  
}
```

Example: 

```
GET news_headlines/_search
{
  "aggregations": {
    "by_category": {
      "terms": {
        "field": "category",
        "size": 100
      }
    }
  }
}
```

Gli stai dicendo: fai l'aggregazione, chiamala by_category, per che tipo? (quindi immagino "terms" si riferisca a stringhe) e con size gli dici quante righe vuoi prendere. 

## Full text queries
### Searching for search terms
The `match query` is a standard query for performing a full text search. This query retrieves documents that contain the search terms. It uses "OR" logic by default, meaning that it will retrieve documents that contain any one of the search terms. The order and the proximity in which the search terms are found(i.e. phrases) are not taken into account.

Syntax:
```
GET Enter_name_of_index_here/_search
{
  "query": {
    "match": {
      "Specify the field you want to search": {
        "query": "Enter search terms"
      }
    }
  }
}
```

When the `match query` is used to search for a phrase, it has high recall but low precision as it returns a lot of loosely related documents.

### Searching for phrases using the match_phrase query
If the order and the proximity in which the search terms are found(i.e. phrases) are important in determining the relevance of your search, you use the `match_phrase` query (Instead of using "match, use "match_phrase"). 

