curl -X GET "localhost:9200/customer/_doc/1?pretty"

curl -X GET "localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty"

curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}
'



