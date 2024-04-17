
# Argo CD GitOps Implementation in GKE

## Introduction

Welcome to the Argo CD GitOps Implementation Guide for Google Kubernetes Engine (GKE)! This guide will walk you through setting up a GitOps workflow using Argo CD, a declarative, Git-based continuous delivery tool for Kubernetes. Whether you're new to Kubernetes or have some experience, this guide aims to provide you with a clear path to implement GitOps principles in your GKE cluster, including application deployment, upgrades, and downgrades.

![Argo CD Logo](https://argo-cd.readthedocs.io/en/stable/assets/argo.png)

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Creating a GKE Cluster](#creating-a-gke-cluster)
3. [Installing Argo CD](#installing-argo-cd)
4. [Exposing Argo CD UI](#exposing-argo-cd-ui)
5. [Deploying an Application with Argo CD](#deploying-an-application-with-argo-cd)
6. [Upgrading and Downgrading Application Versions](#upgrading-and-downgrading-application-versions)
7. [Conclusion](#conclusion)

## Prerequisites

Before starting, ensure you have:

- A Google Cloud Platform (GCP) account.
- `gcloud`, `kubectl`, and `helm` installed and configured on your machine.
- Basic familiarity with Kubernetes and Git concepts.

## Creating a GKE Cluster

Create a GKE cluster and configure `kubectl` to use it:

```bash
gcloud container clusters create gitops-cluster --zone us-central1-a --num-nodes=3 --machine-type=e2-medium
gcloud container clusters get-credentials gitops-cluster --zone us-central1-a
```

## Installing Argo CD

Install Argo CD in your cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get all -n argocd
```

## Exposing Argo CD UI

Expose the Argo CD web UI for easy access:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Alternatively, expose it via a LoadBalancer:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get service -n argocd -w | grep argocd-server
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

```

## Deploying an Application with Argo CD

1. **Fork the sample repository**: Fork [this sample repository](https://github.com/nishantabanik/YT-Video-Series.git) containing a simple Nginx deployment (Directory path: GCP-Google Cloud/GitOps/GitOps using ArgoCD/sample-app/nginx-deployment.yaml).

2. **Create an Argo CD application**: Pointing to your forked repository.

```bash

argocd app create nginx-deployment \
  --repo https://github.com/nishantabanik/YT-Video-Series.git \
  --path "GCP-Google Cloud/GitOps/GitOps using ArgoCD/sample-app" \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default

```

3. **Sync the application**: To deploy Nginx to your cluster.

```bash
argocd app sync nginx-deployment
```

## Upgrading and Downgrading Application Versions

To upgrade or downgrade Nginx:

1. **Edit `nginx-deployment.yaml`** in your repository, changing the Nginx image version.
2. **Commit and push** the changes to your repository via Git commit and push command.
3. Argo CD will Not automatically detect the changes and apply them, upgrading or downgrading Nginx in your cluster. You have to either click on "Sync" on the ArgoCD portal or you need to run:

```bash
argocd app set nginx-deployment --sync-policy automated
```

### Example: Downgrading Nginx

Change the image in `nginx-deployment.yaml`:

```yaml
containers:
- name: nginx
  image: nginx:1.19.0
```

Commit and push via Git commit and push command, then watch Argo CD perform the downgrade.


### Example: Upgrading Nginx

Change the image in `nginx-deployment.yaml`:

```yaml
containers:
- name: nginx
  image: nginx:1.20.0
```

Commit and push via Git commit and push command, then watch Argo CD perform the upgrade.

## Conclusion

Congratulations! You've successfully implemented a GitOps workflow in your GKE cluster using Argo CD. You've learned how to deploy applications, and perform version upgrades and downgrades through simple Git operations. Explore further by adding more complex applications, configuring Argo CD with custom Helm charts, and integrating with CI pipelines for a fully automated deployment process.


This guide provides a foundational understanding of GitOps with Argo CD in GKE, including practical examples to get you started. For more advanced configurations and best practices, refer to the [Argo CD documentation](https://argo-cd.readthedocs.io/).