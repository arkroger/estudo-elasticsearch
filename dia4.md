curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&q=title:star

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&q=+year>2010+title:trek

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "match": {
            "title": "star"            
        } 
	 }	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "bool": {
            "must": {"term": {"title": "trek"}},
            "filter": {"range": {"year": {"gte": 2010}}}
        } 
	 }	
}'

#phrase search

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "match_phrase": {
            "title": "star wars"            
        } 
	 }	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "match_phrase": {
            "title": {"query":  "star beyond", "slop": 1}
        } 
	 }	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "match_phrase": {
            "title": {"query":  "star beyond", "slop": 100}
        } 
	 }	
}'

#pagination
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&size=2
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&size=2&from=2
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
    "from": 2,
    "size": 2,
	 "query": {
        "match": {
            "genre": "Sci-Fi"
        } 
	 }	
}'

#sort
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&sort=year

curl -H "Content-Type: application/json" -XDELETE http://localhost:9200/movies

curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies -d '
    {
        "mappings": {
            "properties": {
                "id": { "type": "integer" },                
                "genre": { "type": "keyword" },
                "title": { 
                    "type": "text",                     
                    "fields": {"raw": {"type": "keyword"}}
                }
            }
        }
    }
'
curl -H "Content-Type: application/x-ndjson" -XPUT 'http://localhost:9200/_bulk' --data-binary @movies.json

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty&sort=title.raw

#more filters
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
        "bool": {
            "must": {"match": { "genre": "Sci-Fi"}},
            "must_not": {"match": { "title": "trek"}},
            "filter": {"range": { "year": {"gte": 2000, "lt": 2015}}}           
        } 
	 }	
}'

#exercice
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
    "sort": "title.raw",
	 "query": {
        "bool": {
            "must": {"match": { "genre": "Sci-Fi"}},            
            "filter": {"range": { "year": {"lt": 1960}}}           
        } 
	 }	
}'
