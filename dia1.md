sudo wget http://media.sundog-soft.com/es8/shakes-mapping.json

sudo wget http://media.sundog-soft.com/es8/shakespeare_8.0.json

curl -H "Content-Type: application/json" -XPUT 'http://localhost:9200/shakespeare' --data-binary @shakes-mapping.json

curl -H "Content-Type: application/json" -XPUT 'http://localhost:9200/shakespeare/_bulk' --data-binary @shakespeare_8.0.json 