# estudo-elasticsearch


docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -v elasticsearch:/usr/share/elasticsearch/data -e "discovery.type=single-node" -e "xpack.security.enabled=false" elasticsearch:8.8.1

