# Elasticsearch 
## Start elasticsearch
`cd elastic... 
` 

entra nella cartella dove hai scaricato elastic sul tuo computer.

`
bin/elasticsearch
`
Stai tipo mandando richiesta per accedere ad un cluster(?) (sicuramente ad un'istanza di elastic che vive all'interno del tuo computer, locale).


`
curl http://localhost:9200/ 
`

Vedi lo stato di elastic e  se la richiesta è andata a buon fine. 
 
Entra dentro la cartella di kibana che hai scaricato sul tuo pc e fai partire kibana

`bin/kibana`


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
Nel risultato trovi una  specie di chiave  chiamata "aggregations" con dentro il nome della tua aggregazione che a sua volta ha all'interno la lista di tutte le category e quanti doc hanno quella category.

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

Example:
```
GET news_headlines/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "Shape of you"
      }
    }
  }
}
```

When the `match query` is used to search for a phrase, it has high recall but low precision as it returns a lot of loosely related documents.

### Searching for phrases using the match_phrase query
If the order and the proximity in which the search terms are found(i.e. phrases) are important in determining the relevance of your search, you use the `match_phrase` query (Instead of using "match, use "match_phrase"). 
Sintax:
```
GET Enter_name_of_index_here/_search
{
  "query": {
    "match_phrase": {
      "Specify the field you want to search": {
        "query": "Enter search terms"
      }
    }
  }
}
```
Example: 
```
GET news_headlines/_search
{
  "query": {
    "match_phrase": {
      "headline": {
        "query": "Shape of You"
      }
    }
  }
}
```

When the `match_phrase` parameter is used, all hits must meet the following criteria:

the search terms "Shape", "of", and "you" must appear in the field headline.

the terms must appear in that order.

the terms must appear next to each other.



### Running a match query against multiple fields
When designing a query, you don't always know the context of a user's search. When a user searches for "Michelle Obama", the user could be searching for statements written by Michelle Obama or articles written about her.

To accommodate these contexts, you can write a `multi_match query`, which searches for terms in multiple fields.

The `multi_match` query runs a match query on multiple fields and calculates a score for each field. Then, it assigns the highest score among the fields to the document.

This score will determine the ranking of the document within the search results.

Syntax:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search terms here",
      "fields": [
        "List the field you want to search over",
        "List the field you want to search over",
        "List the field you want to search over"
      ]
    }
  }
}
```

### Per-field boosting
To improve the precision of your search, you can designate one field to carry more weight than the others.

Syntax:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search terms",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
      ]
    }
  }
}
```
Supponi di voler fare una multi_match + una match_phrase si fa così:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search phrase",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
      ],
      "type": "phrase"
    }
  }
}
```
Oltre a fare il Per-field  boosting, con il "type": "phrase" stai facendo la match_phrase


## Combined Queries

There will be times when a user asks a multi-faceted question that requires multiple queries to answer.

For example, a user may want to find political headlines about Michelle Obama published before the year 2016.

This search is actually a combination of three queries:

Query headlines that contain the search terms "Michelle Obama" in the field headline.

Query "Michelle Obama" headlines from the "POLITICS" category.

Query "Michelle Obama" headlines published before the year 2016

One of the ways you can combine these queries is through a bool query

### Bool Query
The bool query retrieves documents matching boolean combinations of other queries.

With the bool query, you can combine multiple queries into one request and further specify boolean clauses to narrow down your search results.

There are four clauses to choose from:

must

must_not

should

filter

You can build combinations of one or more of these clauses. Each clause can contain one or multiple queries that specify the criteria of each clause.

These clauses are optional and can be mixed and matched to cater to your use case. The order in which they appear does not matter either!

Syntax:

```
GET name_of_index/_search
{
  "query": {
    "bool": {
      "must": [
        {One or more queries can be specified here. A document MUST match all of these queries to be considered as a hit.}
      ],
      "must_not": [
        {A document must NOT match any of the queries specified here. It it does, it is excluded from the search results.}
      ],
      "should": [
        {A document does not have to match any queries specified here. However, it if it does match, this document is given a higher score.}
      ],
      "filter": [
        {These filters(queries) place documents in either yes or no category. Ones that fall into the yes category are included in the hits. }
      ]
    }
  }
}
```
