# node-exporter
A Prometeheus node-exporter image with a script to add node.id and node.hostname to node-exporter metrics.


This will allow node-exporter to expose the hostname of the node that is running a Dockerized version of node-exporter:

Add the following environmental variables to the compose file:

``` 
environment:
      - NODE_ID={{.Node.ID}}
      - NODE_NAME={{.Node.Hostname}}
```

Add to the command section of the compose file:
```
      - --collector.textfile.directory=/etc/node-exporter/
```

Ths Dockerfile copies the following script to the node-exporter container:

docker-entrypoint.sh
```
#!/bin/sh -e

echo "node_meta{node_id=\"$NODE_ID\", container_label_com_docker_swarm_node_id=\"$NODE_ID\", node_name=\"$NODE_NAME\"} 1" > /etc/node-exporter/node-meta.prom

set -- /bin/node_exporter "$@"

exec "$@"
```


*borrowed heavily from stefanprodan/swarmprom-node-exporter.  
