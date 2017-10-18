# Building Real-time Web Apps with Neo4j using Python

To install requirements:
```bash
pip install -r requirements.txt
```

To run:
```bash
FLASK_APP=movies.py flask run
```
Or run in debug mode for live updates.
```bash
 FLASK_APP=movies.py FLASK_DEBUG=1 flask run
```
To view, browse to http://localhost:5000/


## Branches
- `01-getting-started` - Connecting to Neo4j
- `02-read-queries` - Run a basic read query inside a read transaction
- `03-query-parameters`- Use query parameter to find a random movie related to a movie
- `04-write-transactions`- Save the User's ratings into the graph using a write transaction, then provide a recommendation based on the similarity in taste to other users

# Deploying to Kubernetes

## Build a Docker image for the app

```
docker run -v  ~/Downloads/k8s-import:/var/lib/neo4j/import -p 7474:7474 -p 7687:7687 -v $PWD/data:/data neo4j
docker exec -i <docker-container-id> bash -c 'cat /import.cypher | bin/cypher-shell -u neo4j -p neo'
eval $(minikube docker-env)
docker build -t python-k8s-example .
```

```
kubectl cp /Users/markneedham/projects/realtime-ny2017/import neo4j-core-0:/var/lib/neo4j/import
kubectl exec neo4j-core-0 --  bash -c 'cat /import.cypher | bin/cypher-shell -u neo4j -p neo'

for i in $(seq 0 2); do
  kubectl cp graph.db.tar.gz neo4j-load-data-${i}:/data/
  kubectl exec neo4j-load-data-${i} -- bash -c "mkdir -p /data/databases && tar -xf  /data/graph.db.tar.gz -C /data/databases"
done

kubectl delete pods -l app=neo4j-loader
```

```
kubectl apply -f k8s/deployment.yaml
```

https://justinrodenbostel.com/2016/06/06/configuring-persistence-storage-with-docker-and-kubernetes/
