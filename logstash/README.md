## Logstash ##

The grok filter plugin enables you to parse the unstructured log data into something structured and queryable.

### install logstash ###
sudo apt-get install logstash

before starting logstash, create dummy pipeline otherwise you will get error
vim /etc/logstash/conf.d/first.conf
input {
    beats {
        port => "5044"
    }
}
output {
    elasticsearch {
      hosts => ["10.100.79.93:9200"]
      index => "jboss-%{+YYYY.MM.dd}"
    }
    stdout { codec => rubydebug }
}

sudo systemctl restart logstash.service


### Install Filebeat ###

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install filebeat

https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html


Cluster: collections of one or more nodes
Node: single server for storing data. It is called uuid 
Shards: distribute data accorss multiple nodes for better performance
Replicas: These are the replicas of shards. e.g I have one index and multiple types in that index. My data will be distributed on multiple node meaning, one type would be on a different server and second type on different server. These would be replicated on other server so that if one shard have issues, my data is safe
Index: Database name/ schema is called index
Types: 1 table in RDBMS is called one type.
Fields: called column in relational DB
Documents with properties: One record/row is called one document Data in JSON form. one row in RDBMS is equal to one document
e.g {
	"ID": 1
	"Name": "Munawra",
	"Positon": "DevOps Engineer",
	"Age": 31
	}
Above is a single document or single record
Mapping: Formate of data values ( user_id, first_name, last_name, age ) with data types

RDBMS --> Databases --> Tables --> Columns/Rows
Elasticsearch --> Index --> Types --> Documents with properties

RDBMS --> Database --> Tables --> Columns/Rows
Elasticsearch --> Index(tables) --> documents (rows)

Analysis: Converting text into Token or terms
e.g
sentence: "A quick brown fox jumped over the lazy dog"
Tokens: [quick, brown, fox, jumped, over, lazy, dog]
Analysis performed by:

document = json + metadat
	all keys are store with _keys

document
e.g {

	"ID": 1
	"Name": "Munawra",
	"Positon": "DevOps Engineer",
	"Age": 31
	}


Filebeat:
	It collects the data and send to elasticsearch or to logstash.
	3 ways to collect the data
		- Data collection modules — simplify the collection, parsing, and visualization of common log formats
		- ECS loggers — structure and format application logs into ECS-compatible JSON
		- Manual Filebeat configuration
	list the available modules
		filebeat modules list
	enable the module
		filebeat modules enable nginx
How filebeat work
	There are two main components: input and harvesters. These components work togather to tail files and send event data to the output we specify in filebeat configurations.
What is a harvester?
	A harvester is responsible for reading the content of a single file. The harvester reads each file, line by line, and sends the content to the output. One harvester is started for each file. The harvester is responsible for opening and closing the file, which means that the file descriptor remains open while the harvester is running. If a file is removed or renamed while it’s being harvested, Filebeat continues to read the file. This has the side effect that the space on your disk is reserved until the harvester closes. By default, Filebeat keeps the file open until close_inactive is reached
What is an input?
	An input is responsible for managing the harvesters and finding all sources to read from.If the input type is log, the input finds all files on the drive that match the defined glob paths and starts a harvester for each file. Each input runs in its own Go routine.


installation
configuration
directory structure


# Check elastic search
curl -X GET "10.130.21.90:9200"
# List the indices
curl -XGET http://10.130.21.90:9200/_cat/indices?

# Create new indices
curl -XPUT '10.130.21.90:9200/twitter?pretty' -H 'Content-Type: application/json' -d'{"settings" : {"index" : {"number_of_shards" : 3, "number_of_replicas" : 0 }}}'
# Delete indices
curl -X DELETE "http://10.130.21.90:9200/twitter"
 
# Explore the indices
curl -X GET "10.130.21.90:9200/twitter/_search?pretty"