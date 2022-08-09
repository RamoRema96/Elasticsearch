# STEP 1: Create a new index(ecommerce_data) with the following mapping.

(Sta cosa la puoi fare anche in python) 

```
PUT ecommerce_data
{
  "mappings": {
    "properties": {
      "Country": {
        "type": "keyword"
      },
      "CustomerID": {
        "type": "long"
      },
      "Description": {
        "type": "text"
      },
      "InvoiceDate": {
        "type": "date",
        "format": "M/d/yyyy H:m"
      },
      "InvoiceNo": {
        "type": "keyword"
      },
      "Quantity": {
        "type": "long"
      },
      "StockCode": {
        "type": "keyword"
      },
      "UnitPrice": {
        "type": "double"
      }
    }
  }
}
```
## STEP 2: Reindex the data from the original index(source) to the one you just created(destination).
```
POST _reindex
{
  "source": {
    "index": "name of your original index when you added the data to Elasticsearch"
  },
  "dest": {
    "index": "ecommerce_data"
  }
}
```


## 
