# Getting started with Dynatrace on K8s

## Installing Dynatrace Operator 
* Create the K8s cluster in Azure Kubernetes Service

Commands to run to get acquainted with K8s: 

``````
kubectl get pods -n dynatrace
```````

``````
kubectl logs --tail=20 <podname> -n dynatrace
``````
## Installing Fluent Bit and shipping logs to Dynatrace 
1. Add Fluent Bit Helm chart

``````
helm repo add fluent https://fluent.github.io/helm-charts
``````

2. Update the helm charts: 

``````
helm repo update
``````

3. Install Fluent bit with the values file that you obtain from the Dynatrace Kubernetes Install wizard: 

``````
helm install fluent-bit fluent/fluent-bit -f fluent-bit-values.yaml `
--create-namespace `
--namespace dynatrace-fluent-bit
``````

4. Check the logs of Fluent bit pods: 

``````
kubectl get pods -n dynatrace-fluent-bit
kubectl logs --tail=20 <podname> -n dynatrace-fluent-bit
``````

## Ingest Prometheus metrics into Dynatrace
Run the following commands on your Kubernetes cluster:

1. Create an API token with Ingest metrics permissions within Dynatrace tenant and replace the values of tenant and API Token: 

``````
   kubectl create secret generic dynatrace-otelcol-dt-api-credentials --from-literal=DT_ENDPOINT=https://<tenant>.live.dynatrace.com/ --from-literal=DT_API_TOKEN=dt0c01.T2JTCLQUKJN4FER4LHEUQSTG.XXXXYYYYZZZ
``````

2. Download the cert Manager:

```js
  kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
```

3. Download Opentelemetry operator

```js
  kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml
```

4. Add the Otel Helm Charts:

```js
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```

5. Provide the details of what to import within Dynatrace using the values-deployment.yaml

```js
  helm upgrade -i dynatrace-collector open-telemetry/opentelemetry-collector -f values-deployment.yaml
```


## Adding Istio and Envoy


