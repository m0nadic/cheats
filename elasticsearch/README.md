# Elastic Search


## cluster health

```
GET /_cluster/health

GET /_cat/shards?v
```

# list nodes 

```
GET /_cat/nodes?format=json
GET /_cat/nodes?v
```

# list indices
```
GET /_cat/indices?v
```

# Create and delete index

```
PUT /pages
DELETE /pages
```

# Create index with settings

```
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```

# Create document 

```
POST /products/_doc
{
  "name": "Cofee Maker",
  "price": 64,
  "in_stock": 10
}
```


# Create docment by specifying id (Use PUT)

```
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 52,
  "in_stock": 15
}
```

# Retrieving documents

```
GET /products/_doc/100
```


# Updating documents

```
POST /products/_update/100
{
  "doc": {
    "in_stock" : 5
  }
}
```

# Updating documents, add new field

```
POST /products/_update/100
{
  "doc": {
    "tags" : ["electronics", "home-appliance"]
  }
}
```

# scripted update
# decrement
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}

# assignment
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 20"
  }
}

# using parameters (useful while used from application)
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity"
    , "params": {
      "quantity": 4
    }
  }
}

# conditions
POST /products/_update/100
{
  "script": {
    "source": """
    if (ctx._source.in_stock > 0) {
      ctx._source.in_stock--;
    }
    """
  }
}

# conditions -> noop
POST /products/_update/100
{
  "script": {
    "source": """
    if (ctx._source.in_stock == 0) {
      ctx.op = "noop"
    }
    ctx._source.in_stock--;
    """
  }
}

# conditions -> delete
POST /products/_update/100
{
  "script": {
    "source": """
    if (ctx._source.in_stock == 0) {
      ctx.op = "delete"
    }
    ctx._source.in_stock--;
    """
  }
}

# Upsert
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Microwave Oven",
    "price": 52,
    "in_stock": 10
  }
}

# Replacing documents
GET /products/_doc/100

PUT /products/_doc/100
{
  "name" : "Toaster",
  "price" : 79,
  "in_stock" : 20
}

# Deleting documents
DELETE /products/_doc/100

# Routing
# shard_num = hash(_routing) % num_primary_shards

# Optimistic Concurrency Control
GET /products/_doc/100

POST /products/_update/100?if_primary_term=2&if_seq_no=102
{
  "doc": {
    "in_stock": 30
  }
}

# Update by query
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.in_stock--"
  },
  "query": {
    "match_all": {}
  }
}

GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
