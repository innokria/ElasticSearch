# ElasticSearch
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


#

GET /nestedtable/_search
{
  "query":{
    "match_all": {}
  }
}

