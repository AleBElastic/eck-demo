# ECK Installation Demo

This repository provides a step-by-step demo script for installing **Elastic Cloud on Kubernetes (ECK)** and deploying both a primary and a monitoring Elasticsearch cluster with Kibana. The demo is designed for users who want to quickly explore ECK’s main capabilities in a Kubernetes environment.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Demo Steps](#demo-steps)
- [Script Details](#script-details)
- [Cleanup](#cleanup)
- [References](#references)
- [Support](#support)

---

## Overview

**Elastic Cloud on Kubernetes (ECK)** is the official Kubernetes operator from Elastic for deploying and managing Elasticsearch, Kibana, and other Elastic Stack components on Kubernetes.  
This demo script walks you through:

- Installing ECK and its Custom Resource Definitions (CRDs)
- Deploying Elasticsearch and Kibana
- Scaling and upgrading your Elasticsearch cluster
- Setting up a dedicated monitoring cluster
- Accessing Kibana UIs for both clusters

---

## Prerequisites

Before running the demo, ensure you have:

- A running Kubernetes cluster (e.g., Minikube, GKE, EKS, AKS)
- `kubectl` configured and connected to your cluster
- Internet access to download manifests from Elastic
- Sufficient permissions to create namespaces and cluster resources

> **Note:** This demo is intended for learning and testing purposes, not for production use.

---

## Demo Steps

1. **Install ECK CRDs and Operator**
2. **Verify Operator Status**
3. **Deploy Elasticsearch and Kibana**
4. **Retrieve Cluster Credentials**
5. **Access Kibana UI**
6. **Scale and Upgrade the Cluster**
7. **Set Up a Monitoring Cluster**
8. **Access Monitoring Kibana**

---

## Script Details

Below is a summary of the commands used in the demo script:

### 1. Install ECK CRDs and Operator

```sh
kubectl create -f https://download.elastic.co/downloads/eck/3.0.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/3.0.0/operator.yaml
```

### 2. Check Operator Logs and Status

```sh
kubectl -n elastic-system logs -f statefulset.apps/elastic-operator
kubectl get pods -n elastic-system
```

### 3. Deploy Elasticsearch & Kibana

```sh
kubectl apply -f basic-deployment.yaml
```

### 4. Retrieve Elasticsearch Credentials

```sh
PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')
echo $PASSWORD
```

### 5. Access Kibana UI

```sh
kubectl port-forward service/quickstart-kb-http 5601 &
# Visit https://localhost:5601 in your browser
```

### 6. Scale and Upgrade the Cluster

```sh
kubectl apply -f cluster-3-nodes.yaml
kubectl apply -f cluster-upgrade.yaml
```

### 7. Set Up Monitoring Cluster

```sh
kubectl create namespace observability
kubectl apply -f monitoring-deployment.yaml -n observability
kubectl apply -f cluster-with-monitoring.yaml
```

### 8. Retrieve Monitoring Cluster Credentials

```sh
PASSWORD_MONITORING=$(kubectl get secret monitoring-cluster-es-elastic-user -n observability -o go-template='{{.data.elastic | base64decode}}')
echo $PASSWORD_MONITORING
```

### 9. Access Monitoring Kibana

```sh
kubectl port-forward service/kibana-monitoring-kb-http 5602:5601 -n observability &
# Visit https://localhost:5602 in your browser
```

---

## Cleanup

To remove the resources created by this demo, delete the applied manifests and namespaces:

```sh
kubectl delete -f basic-deployment.yaml
kubectl delete -f cluster-3-nodes.yaml
kubectl delete -f cluster-upgrade.yaml
kubectl delete -f monitoring-deployment.yaml -n observability
kubectl delete -f cluster-with-monitoring.yaml
kubectl delete namespace observability
kubectl delete -f https://download.elastic.co/downloads/eck/3.0.0/operator.yaml
kubectl delete -f https://download.elastic.co/downloads/eck/3.0.0/crds.yaml
```

---

## References

- [Elastic Cloud on Kubernetes Documentation](https://www.elastic.co/guide/en/cloud-on-k8s/current/index.html)
- [ECK GitHub Repository](https://github.com/elastic/cloud-on-k8s)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

---

## Support

If you have questions or encounter issues:

- Check the [ECK troubleshooting guide](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-troubleshooting.html)
- Visit the [Elastic community forums](https://discuss.elastic.co/)

---

> **Note:**  
This demo is for educational purposes. For production deployments, consult the official Elastic documentation and follow best practices.

---

**Happy Exploring with ECK!**
