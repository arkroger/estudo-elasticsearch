#tests

curl -H "Content-Type: application/json" -XPUT http://localhost:9200/movies -d '
    {
        "mappings": {
            "properties": {
                "year": {
                    "type": "date"
                }
            }
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


#import 
wget http://media.sundog-soft.com/es8/movies.json

curl -H "Content-Type: application/x-ndjson" -XPUT 'http://localhost:9200/_bulk' --data-binary @movies.json



