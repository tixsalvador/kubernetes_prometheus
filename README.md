# Kubernetes prometheus

#### Create namespace ###
```sh
$ kubectl create ns prometheus
```

#### Deploy RBAC role for prometheus
```sh
$ kubectl create -f prometheus-clusterrole.yaml
```

#### Deploy configmap
```sh
$ kubectl create -f prometheus-config-map.yaml
```
Check https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/ for info on config map   

### Create Persistent Volume claim
```sh
$ kubectl create -f prometheus-pvc-nfs.yaml
```

### Build deployment
```sh
$ kubectl create -f prometheus-deployment.yaml
```

### Setup Kube State metrics
```sh
$ git clone https://github.com/devopscube/kube-state-metrics-configs.git
$ kubectl apply -f kube-state-metrics-configs/
$ kubectl get deployments kube-state-metrics -n kube-system
```
You need to add the following job configuration to your prometheus config for prometheus to scrape all the kube state metrics.
```sh
- job_name: 'kube-state-metrics'
  static_configs:
    - targets: ['kube-state-metrics.kube-system.svc.cluster.local:8080']
```

### Expose Prometheus as a Service [NodePort]
```sh
$ kubectl create -f prometheus-service.yaml
```

### Install Kubernetes grafana
```sh
$ git clone https://github.com/bibinwilson/kubernetes-grafana.git

# Edit grafana-datasource-config.yaml
Change:
namespace: default
To:
namespace: prometheus
```
Note: The following data source configuration is for Prometheus. If you have more data sources, you can add more data sources with different YAMLs under the data section.  

```sh
$ kubectl create -f grafana-datasource-config.yaml
```

### Create grafana persistent volume claim
```sh
$ kubectl create -f kubernetes-grafana/grafana-pvc-nfs.yaml
```
### Build grafana deployment
```sh
$ kubectl create -f kubernetes-grafana/deployment.yaml
```

### Expose Grafana as a Service [NodePort]
```sh
$ kubectl create -f kubernetes-grafana/service.yaml
```




****************************************************************************************
### kubernetes prometheus Setup

Complete prometheus monitoring stack setup on Kubernetes.

You can find the full tutorial from here https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/

Idea of this repo to understand all the components involved in prometheus setup.

### Other Manifest repos

Kube State metrics manifests: https://github.com/devopscube/kube-state-metrics-configs

Alert manager Manifests: https://github.com/bibinwilson/kubernetes-alert-manager

Grafana manifests: https://github.com/bibinwilson/kubernetes-grafana

Node Exporter manifests: https://github.com/bibinwilson/kubernetes-node-exporter

### Source

Fork from https://github.com/bibinwilson/kubernetes-prometheus

### Trick
Watch selected namespace
```sh
watch kubectl get all,pvc,pv   -A  --field-selector metadata.namespace!=kube-system,metadata.namespace!=kubernetes-dashboard
```
