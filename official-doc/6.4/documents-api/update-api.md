https://www.elastic.co/guide/en/elasticsearch/reference/6.4/docs-update.html

https://www.elastic.co/guide/en/elasticsearch/guide/current/partial-updates.html


Update API


The update API allows to update a document based on a script provided. The operation gets the document (collocated with the shard) from the index, runs the script (with optional script language and parameters), and index back the result (also allows to delete, or ignore the operation). It uses versioning to make sure no updates have happened during the "get" and "reindex".

Note, this operation still means full reindex of the document, it just removes some network roundtrips and reduces chances of version conflicts between the get and the index. The _source field needs to be enabled for this feature to work.


For example, let’s index a simple doc:


curl -X PUT "localhost:9200/test/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
    "counter" : 1,
    "tags" : ["red"]
}
'

--------------------------------------------------------------------------------------------------------------------------
### https://www.elastic.co/guide/en/elasticsearch/reference/6.4/docs-update.html#_scripted_updates


#### Now, we can execute a script that would increment the counter:

curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    }
}
'


#### We can add a tag to the list of tags (note, if the tag exists, it will still add it, since its a list):

curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "ctx._source.tags.add(params.tag)",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}
'

#### We can also add a new field to the document:


curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : "ctx._source.new_field = \u0027value_of_new_field\u0027"
}
'

curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : "ctx._source.new_field = \"value_of_new_field\""
}
'


curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "ctx._source.new_field = params.new_field_value",
        "lang": "painless",
        "params" : {
            "new_field_value" : "my new field value"
        }
    }
}
'


#### In addition to _source, the following variables are available through the ctx map: _index, _type, _id, _version, _routing and _now (the current timestamp).




#### Or remove a field from the document:

curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : "ctx._source.remove(\u0027new_field\u0027)"
}
'


#### And, we can even change the operation that is executed. This example deletes the doc if the tags field contain green, otherwise it does nothing (noop):


curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "if (ctx._source.tags.contains(params.tag)) { ctx.op = \u0027delete\u0027 } else { ctx.op = \u0027none\u0027 }",
        "lang": "painless",
        "params" : {
            "tag" : "red"
        }
    }
}
'

------------------------------------------------------------------------------------------------------------------------------------

https://www.elastic.co/guide/en/elasticsearch/reference/6.4/docs-update.html#_updates_with_a_partial_document

The update API also support passing a partial document, which will be merged into the existing document (simple recursive merge, inner merging of objects, replacing core "keys/values" and arrays). To fully replace the existing document, the index API should be used instead. The following partial update adds a new field to the existing document:

curl -X POST "localhost:9200/test/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "doc" : {
        "name" : "new_name"
    }
}
'

***If both doc and script are specified, then doc is ignored. Best is to put your field pairs of the partial document in the script itself.***


------------------------------------------------------------------------------------------------------------------------------------

https://www.elastic.co/guide/en/elasticsearch/reference/6.4/docs-update.html#upserts

***If the document does not already exist, the contents of the upsert element will be inserted as a new document. If the document does exist, then the script will be executed instead:***


curl -X POST "localhost:9200/test/_doc/12345/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    },
    "upsert" : {
        "counter" : 1
    }
}
'



doc_as_upsertedit

Instead of sending a partial doc plus an upsert doc, setting doc_as_upsert to true will use the contents of doc as the upsert value:


curl -X POST "localhost:9200/test/_doc/69/_update?pretty" -H 'Content-Type: application/json' -d'
{
    "doc" : {
        "name" : "new_name"
    },
    "doc_as_upsert" : true
}
'


