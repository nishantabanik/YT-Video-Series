# Kustomize GitOps Implementation for Kubernetes

## Introduction

Welcome to our comprehensive tutorial on Kustomize, presented by Unstuck@40. This guide is designed to help you master Kubernetes setup management using Kustomize, simplifying complex configurations across different environments such as development, staging, and production. Whether you're just starting out or are looking to refine your Kubernetes management skills, this tutorial will provide you with the necessary tools and knowledge.

Here's the URL Link of my YouTube Kustomize tutorial as a reference: [Watch the Kustomize Tutorial Video on YouTube](https://www.youtube.com/watch?v=79C05LcfulY)

![Kustomize Workflow](https://media.dev.to/cdn-cgi/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F7szhk49k18jr9yjxyomd.png)

## Why Kustomize and Its Key Features

Kustomize introduces a layer of abstraction that enhances Kubernetes' native configuration capabilities, making it an indispensable tool for managing complex applications across various environments. Here's why Kustomize is so powerful and its essential features:

- **Resource Management**: Kustomize simplifies the management of Kubernetes resources, allowing you to define template-free and reusable manifests.
- **Customization Layers**: By using overlays, Kustomize enables you to apply changes to the base configuration without altering the original files, making it easy to manage differences across multiple environments.
- **ConfigMap and Secret Generation**: Automatically generate and manage Kubernetes ConfigMaps and Secrets, which helps in maintaining sensitive configurations securely.
- **Patch Management**: Strategic merge patches and JSON patches provide the flexibility to customize applications deeply without duplicating the entire configuration.
- **Simplified Deployment**: Kustomize integrates seamlessly with `kubectl`, simplifying the deployment process by allowing you to deploy directly using kubectl commands.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Understanding Kustomize](#understanding-kustomize)
3. [Directory Structure Setup](#directory-structure-setup)
4. [Creating Base Configuration](#creating-base-configuration)
5. [Customizing for Development](#customizing-for-development)
6. [Customizing for Production](#customizing-for-production)
7. [Deploying Applications](#deploying-applications)
8. [Conclusion](#conclusion)
9. [Further Resources](#further-resources)

## Prerequisites

Before you begin, ensure you have:

- Kubernetes cluster access.
- `kubectl` installed and configured.
- Basic familiarity with Kubernetes and YAML.

## Directory Structure Setup

Initial setup of directories for Kustomize configuration:

```bash
mkdir -p base-app overlays/dev overlays/prod
```

This structure is depicted in the diagram below:

```
├── base-app
│   ├── config.properties
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── dev
    │   ├── config.properties
    │   ├── kustomization.yaml
    │   ├── namespace.yaml
    │   └── replicas.yaml
    └── prod
        ├── resource_limits.yaml
        ├── kustomization.yaml
        ├── namespace.yaml
        └── replicas.yaml
```

- **base-app**: Contains all base Kubernetes manifest files like deployment and service.
- **overlays/dev**: Specific adjustments for the development environment.
- **overlays/prod**: Specific adjustments for the production environment.

## Creating Base Configuration

Base configurations establish the default setup for Kubernetes objects.

### Creating `base-app/kustomization.yaml`

This file defines how Kustomize should handle the manifests:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
configMapGenerator:
  - name: webapp-configmap
    env: config.properties
commonLabels:
  app: Base-web
namePrefix: kustomize-
nameSuffix: -v1
```

- **Resources**: Lists the resources to include in the base configuration.
- **ConfigMapGenerator**: Automates the creation of ConfigMaps.
- **commonLabels**: Applies labels to all resources for easier management and querying.
- **namePrefix** and **nameSuffix**: Adds prefixes or suffixes to the names of resources to avoid conflicts and ensure clarity.

### Base Deployment and Service

These files (`deployment.yaml` and `service.yaml`) define the Kubernetes resources for your application.

## Customizing for Development

Customizations for the development environment include specific configurations like replica counts and resource limits.

### Creating `overlays/dev/kustomization.yaml`

Manage development-specific customizations:

```yaml
resources:
  - ../../base-app
patchesStrategicMerge:
  - replicas.yaml
configMapGenerator:
  - name: webapp-configmap
    env: config.properties
namespace: dev
namePrefix: dev-
nameSuffix: -v1
```

- **PatchesStrategicMerge**: Applies changes to resources without altering the base configuration.
- **ConfigMapGenerator**: Overrides the base ConfigMap settings for development.
- **Namespace**: Specifies the namespace for the development environment.

### Example `replicas.yaml` for Development

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 4


```

## Customizing for Production

Customizations for the production environment focus on scalability and reliability.

### Creating `overlays/prod/kustomization.yaml`

Manage production-specific customizations:

```yaml
resources:
  - ../../base-app
  - resource_limits.yaml
patchesStrategicMerge:
  - replicas.yaml
configMapGenerator:
  - name: webapp-configmap
    env: config.properties
namespace: prod
namePrefix: prod-
nameSuffix: -v2
```

- **Resource Limits**: Specifies resource requests and limits to ensure the application performs under load.

### Example `replicas.yaml` for Production

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 10
```

## Deploying Applications

Deploy your configurations using the following commands:

```bash
# For development environment
kubectl apply -k overlays/dev

# For production environment
kubectl apply -k overlays/prod
```

These commands combine the base configuration with the appropriate overlays and apply them to your Kubernetes cluster.

## Conclusion

You have successfully learned how to use Kustomize to manage and deploy Kubernetes applications across different environments. This approach not only simplifies the management of Kubernetes configurations but also ensures consistency and reduces duplication, allowing for easy adjustments and updates across multiple deployments.

## Further Resources

For more detailed information and advanced features, please refer to the [official Kustomize documentation](https://kubectl.docs.kubernetes.io/).



