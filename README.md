## Requirements

- minikube
- docker

## Setup the Instance

### Start minikube
```
minikube start --cpus 4 --memory 8192
```

### Install Confluent for Kubernetes (CFK) using Helm

#### Add the Repository
```
helm repo add confluentinc https://packages.confluent.io/helm
helm repo update
```

#### Create a Namespace

```
kubectl create namespace confluent
```

#### Install CFK with Helm:
```
helm install confluent-operator confluentinc/confluent-for-kubernetes -n confluent --set kRaftEnabled=true
```

### Verify the Installation
```
kubectl get pods -n confluent
```

You should see a pod named something like confluent-operator-xxxxxxxxxx-xxxxx in a `Running` state.

### Deploy a Kafka Cluster
```
kubectl apply -f kafka.yaml -n confluent
```

### Monitor Deployment
```
kubectl get pods -n confluent
kubectl get kafka -n confluent
```

### Access the Kafka Cluster
```
kubectl port-forward kafka-0 9092:9092 -n confluent
```

### Create a Topic
```
kubectl exec -it kafka-0 -n confluent -- kafka-topics --create --topic test-topic --bootstrap-server kafka:9071 --replication-factor 3 --partitions 3
```

### Other `kubectl` Commands

```
# Create a topic
kubectl exec -it kafka -n confluent -- \
  kafka-topics --create --topic test-topic \
  --bootstrap-server kafka:9092 \
  --replication-factor 3 --partitions 3

# List topics
kubectl exec -it kafka -n confluent -- \
  kafka-topics --list \
  --bootstrap-server kafka:9092
```