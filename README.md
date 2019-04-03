# ElasticSearch
SQL to elastic search query can be done by https://www.toolsbuzz.com/query-converter
# Elastic search cheat sheet

```
# create table with name table1 in elastic search
PUT /table1
{
 "mappings": {
  "_doc": {
   "properties": {
    "speaker": {"type": "text"},
    "play_name": {"type": "keyword"},
    "line_id": {"type": "integer"},
    "speech_number": {"type": "integer"}
   }
  }
 }
}'

# search all item  in table table1
GET /table1/_search
{
  "query":{
    "match_all": {}
  }
}

# search specific items in table1
GET /table1/_search
{
   "query" :{
     "match":{"speaker":"rahul"}
   }
}
# insert data into table1 with auto id
POST /table1/_doc
{
  "speaker": "rahul singh",
  "speech_number":300
}

#nested

PUT my_nested1/_doc/1
{
  "group" : "fans",
  "user" : [ 
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}

GET /my_nested1/_search
{
  "query":{
    "match_all": {}
  }
}


GET my_nested1/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "user.first": "Alice" }},
        { "match": { "user.last":  "Smith" }}
      ]
    }
  }
}



# table with type nested 
PUT my_nested
{
  "mappings": {
    "_doc": {
      "properties": {
        "user": {
          "type": "nested" 
        }
      }
    }
  }
}

PUT my_nested/_doc/1
{
  "group" : "fans",
  "user" : [
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}

GET my_nested/_search
{
  "query": {
    "nested": {
      "path": "user",
      "query": {
        "bool": {
          "must": [
            { "match": { "user.first": "Alice" }},
            { "match": { "user.last":  "Smith" }} 
          ]
        }
      }
    }
  }
}


GET my_nested/_search
{
  "query": {
    "nested": {
      "path": "user",
      "query": {
        "bool": {
          "must": [
            { "match": { "user.first": "Alice" }},
            { "match": { "user.last":  "White" }} 
          ]
        }
      }
    }
  }
}

#nested more
#we have an index of products, and each product holds the list of resellers - each having its own price for the product. 
PUT /nestedtable
{
  "mappings": {
    "product" : {
        "properties" : {
            "resellers" : { 
                "type" : "nested",
                "properties" : {
                    "name" : { "type" : "text" },
                    "price" : { "type" : "double" }
                }
            }
        }
    }
  }
}

#insert items for resellers
PUT nestedtable/product/1
{

  "resellers":[
      
    {
      "name"
      : "led tv",
      "price":300
    }
  ]
}

# serach in nested table
GET nestedtable/_search
{
    "query" : {
        "match" : { "name" : "led" }
    },
    "aggs" : {
        "resellers" : {
            "nested" : {
                "path" : "resellers"
            },
            "aggs" : {
                "min_price" : { "min" : { "field" : "resellers.price" } }
            }
        }
    }
}

GET /item/_doc/aaa

POST /item/_doc/aaa/_update
{
  "script": {
    "source": "ctx._source.item = 's5';"
  }
}



#

GET /nestedtable/_search
{
  "query":{
    "match_all": {}
  }
}

# update by query
 have an index already filled with many documents.
All of the documents in the index have a name string field.
Now query and update all of them which have name = A and set it to name = B
# SQL - UPDATE FROM table SET name = 'B' WHERE name = 'A';
POST my_index/_update_by_query
{
  "script": {
    "inline": "ctx._source.name = 'B'",
    "lang": "painless"
  },
  "query": {
    "match": {
      "name" : "A"
    }
  }
}


#If you want to simply count version conflicts, and not cause the _update_by_query to abort, you can set conflicts=proceed on the url or "conflicts": "proceed" in the request body.
The simplest usage of _update_by_query just performs an update on every document in the index without changing the source.

POST twitter/_doc/_update_by_query?conflicts=proceed




# limit _update_by_query using the Query DSL. This will update all documents from the twitter index for the user rahul
POST twitter/_update_by_query?conflicts=proceed
{
  "query": { 
    "term": {
      "user": "rahul"
    }
  }
}

