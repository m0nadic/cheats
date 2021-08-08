
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

```
GET /recipe/_search
{
  "query": {
    "multi_match": {
      "query": "pasta",
      "fields": ["title", "description"]
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "match": {
      "title": "pasta with parmesan and spinach"
    }
  }
}
```


```
GET /recipe/_search
{
  "query": {
    "match_phrase": {
      "title": "pasta carbonara"
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "multi_match": {
      "query": "pasta pesto",
      "fields": ["title", "description"]
    }
  }
}
```
# Compound queries
# query context
```
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": "parmesan"
          }
        },
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15
            }
          }
        }
      ]
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": "parmesan"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15
            }
          }
        }
      ]
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": "parmesan"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "ingredients.name": "tuna"
          }
        }
      ], 
      "filter": [
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15
            }
          }
        }
      ]
    }
  }
}
```

```
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": "parmesan"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "ingredients.name": "tuna"
          }
        }
      ], 
      "should": [
        {
          "match": {
            "ingredients.name": "parsley"
          }
        }
      ], 
      "filter": [
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15
            }
          }
        }
      ]
    }
  }
}
```

# Debugging with named queries

```
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": {
              "query": "parmesan",
              "_name": "parmesan_must"
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "ingredients.name": {
              "query": "tuna",
              "_name": "tuna_must_not"
            }
          }
        }
      ], 
      "should": [
        {
          "match": {
            "ingredients.name": {
              "query": "parsley",
              "_name": "parsley_should"
            }
          }
        }
      ], 
      "filter": [
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15,
              "_name": "prep_time_filter"
            }
          }
        }
      ]
    }
  }
}
```



