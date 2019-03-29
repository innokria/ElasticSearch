# ElasticSearch
Elastic search cheat sheet
#create table with name table1 in elastic search
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
}
#search all item  in table table1
GET /table1/_search
{
  "query":{
    "match_all": {}
  }
}

#search specific items in table1
GET /table1/_search
{
   "query" :{
     "match":{"speaker":"rahul"}
   }
}
#insert data into table1 with auto id
POST /table1/_doc
{
  "speaker": "rahul singh",
  "speech_number":300
}


