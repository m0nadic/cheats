
```
GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
```

```
GET /products/_search
{
  "query": {
    "term": {
      "name": "lobster"
    }
  }
}

# This return empty result 
GET /products/_search
{
  "query": {
    "term": {
      "name": "Lobster"
    }
  }
}

GET /products/_search
{
  "query": {
    "match": {
      "name": "Lobster"
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "term": {
      "is_active": false
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "terms": {
      "tags.keyword": [
        "Cake",
        "Soup"
      ]
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "range": {
      "in_stock": {
        "gte": 1,
        "lte": 5
      }
    }
  }
}
```

```
GET /products/_search 
{
  "query": {
    "range": {
      "created": {
        "gte": "2010/01/01",
        "lte": "2010/12/31"
      }
    }
  }
}
```

```
GET /products/_search 
{
  "query": {
    "range": {
      "created": {
        "gte": "01-01-2010",
        "lte": "31-12-2010",
        "format": "dd-MM-yyyy"
      }
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "exists": {
      "field": "tags"
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "prefix": {
      "tags.keyword": "Vege"
    }
  }
}
```

## Wildcard search
```
GET /products/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": "Veg*ble"
    }
  }
}


GET /products/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": "Vegetabl?"
    }
  }
}
```
## Regexp search
```
GET /products/_search
{
  "query": {
    "regexp": {
      "tags.keyword": "C[a-zA-Z]+"
    }
  }
}
```

## Range queries
```
GET /products/_search
{
  "query": {
    "range": {
      "sold": {
        "lte": 10
      }
    }
  }
}


GET /products/_search 
{
  "query": {
    "range": {
      "sold": {
        "gte": 10,
        "lte": 30
      }
    }
  }
}
```
## Term queries
```
GET /products/_search 
{
  "query": {
    "term": {
      "tags.keyword": "Meat"
    }
  }
}
```

```
GET /products/_search
{
  "query": {
    "terms": {
      "tags.keyword": [
        "Tomato",
        "Paste"
      ]
    }
  }
}
```

```
GET /products/_search 
{
  "query": {
    "wildcard": {
      "name": "past?"
    }
  }
}
```

```
GET /products/_search 
{
  "query": {
    "regexp": {
      "name": "[0-9]+"
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "match_all": {}
  }
}
```

## Get mapping for an index
```
GET /recipe/_mapping 
```
# Full text queries

```
GET /recipe/_search 
{
  "query": {
    "match": {
      "title": "Recipes with pasta or spagetti"
    }
  }
}
```

## Using operator

```
GET /recipe/_search 
{
  "query": {
    "match": {
      "title": {
        "query": "pasta spaghetti",
        "operator": "and"
      }
    }
  }
}
```

## Match phrase

```
GET /recipe/_search
{
  "query": {
    "match_phrase": {
      "title": "spaghetti puttanesca"
    }
  }
}
```

This doesn't return any result (order of terms matter)

```
GET /recipe/_search
{
  "query": {
    "match_phrase": {
      "title": "puttanesca spaghetti"
    }
  }
}
```

## Searching multiple fields





