# helm-chart

Margarita Serve helm-chart

# Minimum Cluster Requirements
- >= kubenetes v.1.21
- >= 3 Nodes
  
  
# Single Cluster Install

1. Install Cert-Manager
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.10.1/cert-manager.yaml
```

1. Install Istio

https://knative.dev/docs/install/installing-istio/

```
$ curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.11.0 TARGET_ARCH=x86_64 sh -
$ cd istio-1.11.0
$ export PATH=$PWD/bin:$PATH
$ istioctl install -y
```


3. Install Knative Serving
```
kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.1.0/serving-crds.yaml
kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.1.0/serving-core.yaml
kubectl apply -f https://github.com/knative/net-istio/releases/download/knative-v1.1.0/net-istio.yaml

```

4. Install Kserve
```
kubectl apply -f https://github.com/kserve/kserve/releases/download/v0.8.0/kserve.yaml
kubectl apply -f https://github.com/kserve/kserve/releases/download/v0.8.0/kserve-runtimes.yaml

```

5. Install Knative Eventing
```
kubectl apply -f https://github.com/knative/eventing/releases/download/knative-v1.1.0/eventing-crds.yaml
kubectl apply -f https://github.com/knative/eventing/releases/download/knative-v1.1.0/eventing-core.yaml
kubectl apply -f https://github.com/knative-sandbox/eventing-kafka-broker/releases/download/knative-v1.1.4/eventing-kafka-controller.yaml
kubectl apply -f https://github.com/knative-sandbox/eventing-kafka-broker/releases/download/knative-v1.1.4/eventing-kafka-broker.yaml
```

6. Knative-Kafka Broker setup
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafka-broker-config
  namespace: knative-eventing
  labels:
    kafka.eventing.knative.dev/release: "devel"
data:
  default.topic.partitions: "10"
  default.topic.replication.factor: "1"
  bootstrap.servers: "<helm-release-name>-kafka.<helm-release-namespace>.svc.cluster.local:9092"
EOF
```

7. Install Mararita Serve
```
helm repo add margarita-serve https://margarita-serve.github.io/helm-chart-repository/
helm repo update
helm install <release-name> margarita-serve/margarita-serve -n <namespace> --wait
```

Check Services 

first, get your service port(NodePort is the default)
kubectl get service <release-name>-margarita-serve-frontend -n <namespace> 

then check REST API service, Please refer to the url below
http://<your-cluster-ip>:<service-port>/openapi/swagger/index.html

check WEB UI service, Please refer to the url below
http://<your-cluster-ip>:<service-port>

default ID, Password is 
admin.koreserve / koreserve