# elasticsearch-cheatsheet
Some helpful CURLs for interacting directly with es


#### get elasticsearch version:
```
curl -XGET 'localhost:9200'
```

#### show cluster health status:
```
curl -XGET 'http://localhost:9200/_cluster/health?pretty=true'
```

#### show nodes:
```
curl -XGET 'http://localhost:9200/_cat/nodes?pretty=true'
```

#### show shards:
```
curl -XGET http://localhost:9200/_cat/shards
```

#### show indices:
```
curl -XGET 'http://localhost:9200/_cat/indices?v'
```

#### activate shard allocation:
```
curl -XPUT http://localhost:9200/_cluster/settings -d '{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}'
```

#### reallocate an index
```
curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
        "commands" : [ {
              "allocate" : {
                  "index" : "logstash-2016.06.02", 
                  "shard" : 0, 
                  "node" : "Persuasion", 
                  "allow_primary" : true
              }
            }
        ]
    }'
```

####  reallocate all unallocated indices
```
for shard in $(curl -XGET http://localhost:9200/_cat/shards | grep UNASSIGNED | awk '{print $2}'); do
    curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
        "commands" : [ {
              "allocate" : {
                  "index" : "logstash-2016.06.02", 
                  "shard" : $shard, 
                  "node" : "Assassin", 
                  "allow_primary" : true
              }
            }
        ]
    }'
    sleep 5
done
```


#### flush for rolling cluster restart
```
curl -XPOST "http://localhost:9200/elasticsearch/_flush/synced"
```

## Helpful Links

https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-health.html

https://www.elastic.co/guide/en/elasticsearch/reference/1.4/cluster-nodes-shutdown.html

http://blog.florian-hopf.de/2015/02/fixing-elasticsearch-allocation-issues.html

http://blog.kiyanpro.com/2016/03/06/elasticsearch/reroute-unassigned-shards/

https://t37.net/how-to-fix-your-elasticsearch-cluster-stuck-in-initializing-shards-mode.html