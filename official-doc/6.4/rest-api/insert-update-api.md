create new index (not necassary to create index, when you index a doc and index is not found it automitically create index)

curl -X PUT "localhost:9200/customer?pretty"



https://stackoverflow.com/questions/630453/put-vs-post-in-rest
---------------------------------------------------------------------------------------------------
insert new doc (index a doc)  (Use PUT method when you want to give your _id to doc) (use POST method when you want elasticsearch to generate random uuid for doc _id)

curl -X PUT "localhost:9200/customer/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'

----------------------------------------------------------------------------------------------
using POST method (Note that in this case, we are using the POST verb instead of PUT since we didn’t specify an ID.)

curl -X POST "localhost:9200/customer/_doc?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "Jane Doe"
}
'



------------------------------------------------------------------------------------------------
update a document (replace full doc) (if doc exist it delete old doc, and replace with this new doc)

curl -X PUT "localhost:9200/customer/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "Jane Doe"
}
'

-------------------------------------------------------------------------------------------------
update a document (partial update) (update some field of doc) (pass field which you want to update, and new filed if you want to add new field)

curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "shubham chaudhary" }
}
'

curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe", "age": 20 }
}
'

Updates can also be performed by using simple scripts

curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "script" : "ctx._source.age += 5"
}
'

In the above example, ctx._source refers to the current source document that is about to be updated.

https://stackoverflow.com/questions/37696380/elasticsearch-update-with-scripts-what-does-ctx-mean

https://stackoverflow.com/questions/15722197/updating-indexed-document-in-elasticsearch

https://www.elastic.co/guide/en/elasticsearch/reference/6.4/modules-scripting-fields.html

https://www.elastic.co/guide/en/elasticsearch/reference/current/script-processor.html

https://discuss.elastic.co/t/using-ctx-to-access-source-field/115129





