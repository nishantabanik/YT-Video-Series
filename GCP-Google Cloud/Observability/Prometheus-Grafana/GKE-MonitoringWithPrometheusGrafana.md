


# Kubernetes (GKE) Observability Setup Guide

## Introduction

Welcome to the Kubernetes Observability Setup Guide! In this guide, we'll walk you through setting up monitoring and observability for your Kubernetes cluster using Prometheus, Grafana, and some handy tools. Whether you're a seasoned Kubernetes user or just getting started, this guide will help you gain insights into your cluster's health and performance.


Here's the URL Link of my YouTube Prometheus-Grafana tutorial as a reference: [Watch the GKE Observability Tutorial Video on YouTube](https://www.youtube.com/watch?v=_6DLJnLOwc8)


![Kubernetes Logo](https://media.licdn.com/dms/image/C5612AQFqPzlOaOvwbw/article-cover_image-shrink_720_1280/0/1606979524095?e=1716422400&v=beta&t=fS9U_3WFoBEaCUd9-UYd--iR96_OSotTk5ZasxhmFDY)

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Authentication and Project Setup](#authentication-and-project-setup)
3. [Cluster Creation and Configuration](#cluster-creation-and-configuration)
4. [Installation of Monitoring Tools](#installation-of-monitoring-tools)
5. [Accessing and Configuring Prometheus and Grafana](#accessing-and-configuring-prometheus-and-grafana)
6. [Importing Dashboards for Observability](#importing-dashboards-for-observability)
7. [Conclusion](#conclusion)

## Prerequisites

Before diving into the setup, make sure you have:

- A Google Cloud Platform (GCP) account
- Basic familiarity with Kubernetes concepts
- Access to the GCP Console and Google Cloud SDK (gcloud)

## Authentication and Project Setup

```bash
gcloud auth login
gcloud config set project gke-project
```

1. **`gcloud auth login`**: Initiates the authentication process with your Google Cloud account.
2. **`gcloud config set project gke-project`**: Sets the default project to `gke-project`.

## Cluster Creation and Configuration

```bash
gcloud services enable container.googleapis.com
gcloud container clusters create my-cluster --zone us-central1-a --num-nodes=1
gcloud container clusters get-credentials my-cluster --zone us-central1-a --project gke-project
```

3. **`gcloud services enable container.googleapis.com`**: Enables the Google Kubernetes Engine API.
4. **`gcloud container clusters create my-cluster --zone us-central1-a --num-nodes=1`**: Creates a Kubernetes cluster named `my-cluster` with one node in the `us-central1-a` zone.
5. **`gcloud container clusters get-credentials my-cluster --zone us-central1-a --project gke-project`**: Retrieves cluster credentials for `my-cluster` in the `us-central1-a` zone and sets the appropriate project.


### **Note: _Here we are using GKE by running the above commands, you can use the Prometheus-Grafana Observability stack for EKS (AWS), AKS (Azure) and Kind (or any other local or managed K8s cluster) by running the below-mentioned commands. Just make sure your EKS, AKS or Kind cluster is ready._** 

## Installation of Monitoring Tools

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring
```

6. **`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`**: Adds the Prometheus Helm repository.
7. **`helm repo update`**: Updates the Helm repositories.
8. **`kubectl create namespace monitoring`**: Creates a namespace named "monitoring" to organize resources.
9. **`helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring`**: Installs Prometheus and related components into the "monitoring" namespace.

## Accessing and Configuring Prometheus and Grafana

```bash
kubectl edit service prometheus-kube-prometheus-prometheus -n monitoring
kubectl edit service prometheus-grafana -n monitoring
```

10. Edit the service configurations for Prometheus and Grafana in the "monitoring" namespace to change the service type from ClusterIP to LoadBalancer for external access.
    We will not change the service type from ClusterIP to LoadBalancer for "prometheus-kube-prometheus-prometheus" service, WHY?? Because we'll access Prometheus using port-forwarding but we'll
    certainly change the service type from ClusterIP to LoadBalancer "prometheus-grafana" service, to show you, how you can access the services via the LoadBalancer service-type feature.

## Additional Steps

```bash
kubectl get service -n monitoring
kubectl port-forward prometheus-prometheus-kube-prometheus-prometheus-0 9090 -n monitoring
kubectl get secret prometheus-grafana -o jsonpath="{.data.admin-password}" -n monitoring | base64 --decode ; echo
k get service -n monitoring -w
```

11. **`kubectl get service -n monitoring`**: Lists the services in the "monitoring" namespace, including external IPs.
12. **`kubectl port-forward prometheus-prometheus-kube-prometheus-prometheus-0 9090 -n monitoring`**: Forwards port 9090 of the Prometheus pod to your local machine for access. 
We will now access prometheus using "127.0.0.1:9090" and explore prometheus scraping metrics Targets, Configuration of the prometheus server, Service discovery config of prometheus etc.
13. **`kubectl get secret prometheus-grafana -o jsonpath="{.data.admin-password}" -n monitoring | base64 --decode ; echo`**: Retrieves and decodes the admin password for Grafana in the "monitoring" namespace.
14. **`k get service -n monitoring -w`**: we will get the external ip and login inside Grafana using the same IP via internet browser with above password and username as "admin"

## Importing Dashboards for Observability

Visit [Grafana Dashboards](https://grafana.com/grafana/dashboards/) and search for Kubernetes dashboards. Choose desired dashboards (e.g., [Kubernetes Cluster Prometheus](https://grafana.com/grafana/dashboards/6417-kubernetes-cluster-prometheus/)). Import dashboards into Grafana and select Prometheus as the data source.

Certainly! Here's a more descriptive explanation for importing dashboards from Grafana's official site, tailored for your GitHub page:

## Importing Dashboards for Observability

To enhance the observability of your Kubernetes (K8s) cluster, we'll leverage pre-built dashboards from the Grafana official site. Follow these steps to import dashboards seamlessly:

1. **Visit Grafana's Official Site**: Head over to [Grafana Dashboards](https://grafana.com/grafana/dashboards/).

2. **Search for Kubernetes Dashboards**: In the search box, type "kubernetes" to explore a plethora of Kubernetes-related dashboards available for import.

3. **Select a Dashboard**: Browse through the list and select a dashboard that aligns with your monitoring requirements. Each dashboard provides unique insights into various aspects of your Kubernetes environment, such as cluster health, resource utilization, and pod performance.

4. **Copy Dashboard ID**: Once you've chosen a dashboard, click on its page to access detailed information. Look for the "Copy ID to clipboard" option and click on it. This action copies the unique identifier of the dashboard to your clipboard.

5. **Import Dashboard in Grafana**: Switch to your Grafana portal and navigate to the "Import Dashboard" section. Paste the copied dashboard ID into the designated field.

6. **Select Data Source**: Ensure that you select "Prometheus" as the data source for the imported dashboard. Prometheus seamlessly integrates with Grafana, providing access to rich monitoring metrics and data points from your Kubernetes cluster.

7. **Finalize and Visualize**: With the dashboard imported and configured, finalize the process. Voila! You now have a comprehensive K8s cluster dashboard at your fingertips. Explore the various panels, graphs, and metrics to gain valuable insights into your cluster's performance and health status.

By leveraging pre-built dashboards, you can expedite the setup process and gain immediate visibility into your Kubernetes environment, empowering you to make informed decisions and optimize resource utilization effectively.


## Conclusion

Congratulations! You've successfully set up monitoring and observability for your Kubernetes cluster. Explore the imported dashboards and gain valuable insights into your cluster's performance.


