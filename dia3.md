#update
curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies/_doc/109487 -d '
    {
        "genre": ["IMAX", "Sci-Fi"],
        "title": "Interstellar foo",
        "year": 2014        
    }
'

curl -XGET http://localhost:9200/movies/_doc/109487\?pretty

#partial updade
curl -H "Content-Type: application/json" -XPOST http://localhost:9200/movies/_update/109487 -d '
    {
        "doc": {
            "title": "Interstellar"
        }        
    }
'

curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies/_doc/109487 -d '
    {
        "genre": ["IMAX", "Sci-Fi"],
        "title": "Interstellar",
        "year": 2014        
    }
'



#DELETE
curl -XGET http://localhost:9200/movies/_search\?q=Dark&pretty
curl -H "Content-Type: application/json" -XDELETEhttp://localhost:9200/movies/_doc/58559
curl -XGET http://localhost:9200/movies/_search\?q=Dark&pretty


#Concorrencia
curl -XGET http://localhost:9200/movies/_doc/109487\?pretty
curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies/_doc/109487\?pretty&if_seq_no=7&if_primary_term=2 -d '
    {
        "genre": ["IMAX", "Sci-Fi"],
        "title": "Interstellar foo",
        "year": 2014        
    }
'

curl -H "Content-Type: application/json" -XPOST http://localhost:9200/movies/_update/109487\?retry_on_conflict=5 -d '
    {
        "doc": {
            "title": "Interstellar typo"
        }        
    }
'


#Analyzers and Tokenizers
curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
		 "match": {
			 "title": "Star Trek"
		 }
	 }
	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
		 "match_phrase": {
			 "genre": "sci"
		 }
	 }
	
}'

curl -H "Content-Type: application/json" -XDELETE http://localhost:9200/movies

curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies -d '
    {
        "mappings": {
            "properties": {
                "id": { "type": "integer" },
                "year": { "type": "date" },
                "genre": { "type": "keyword" },
                "title": { "type": "text", "analyzer": "english" }
            }
        }
    }
'
curl -H "Content-Type: application/x-ndjson" -XPUT 'http://localhost:9200/_bulk' --data-binary @movies.json

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
		 "match_phrase": {
			 "genre": "Sci-Fi"
		 }
	 }
	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/movies/_search?pretty -d '
{
	 "query": {
		 "match": {
			 "title": "star wars"
		 }
	 }
	
}'

#Data Modeling and Parent/Child Relationships

curl -H "Content-Type: application/json" -XDELETE http://localhost:9200/movies

curl -H "Content-Type: application/json" -XPUT http://localhost:9200/series -d '
    {
        "mappings": {
            "properties": {
                "film_to_franchise": { 
                    "type": "join",
                    "relations": { "franchise": "film"} 
                }
            }
        }
    }
'

sudo wget http://media.sundog-soft.com/es8/series.json

curl -H "Content-Type: application/x-ndjson" -XPUT 'http://localhost:9200/_bulk' --data-binary @series.json

curl -H "Content-Type: application/json" -XGET http://localhost:9200/series/_search?pretty -d '
{
	 "query": {
        "has_parent": {
            "parent_type": "franchise",
            "query": {
                "match": {
			        "title": "Star Wars"
		        }
            }
        } 
	 }	
}'

curl -H "Content-Type: application/json" -XGET http://localhost:9200/series/_search?pretty -d '
{
	 "query": {
        "has_child": {
            "type": "film",
            "query": {
                "match": {
			        "title": "The Force Awakens"
		        }
            }
        } 
	 }	
}'