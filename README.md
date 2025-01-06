# Helm Playground

https://github.com/helm/helm/

Helm is a tool for managing Charts. Charts are packages of pre-configured Kubernetes resources.

## Table of Contents
- [Helm Playground](#helm-playground)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Installing Helm](#installing-helm)
  - [Quick start with Repositories](#quick-start-with-repositories)
  - [Quick start Install sample chart](#quick-start-install-sample-chart)
  - [Artifact Hub](#artifact-hub)
  - [Lifecycle management with Helm](#lifecycle-management-with-helm)
  - [Creating helm chart](#creating-helm-chart)

## Introduction

Use Helm to:
* Find and use popular software packaged as Helm Charts to run in Kubernetes
* Share your own applications as Helm Charts
* Create reproducible builds of your Kubernetes applications
* Intelligently manage your Kubernetes manifest files
* Manage releases of Helm packages

Helm was first released in February 2016 (v1.0). Helm v2.0 followed in November 2016, with the final release of Helm 2 (v2.17.0) on November 13, 2020. This marked the end of Helm 2's support, encouraging users to migrate to Helm 3. Released in November 2019, Helm 3 introduced significant changes, including the removal of the Tiller component, enhancing security and simplifying the Helm architecture. The latest version as of 2025 is v3.16.4.

## Installing Helm
https://helm.sh/docs/intro/install/

```bash
# MacOS
brew install helm
```

## Quick start with Repositories
```bash
# list repositories
helm repo list

# Add new repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# Remove repo
helm repo remove bitnami

# Make sure we get the latest list of charts
helm repo update
```

## Quick start Install sample chart
https://helm.sh/docs/intro/quickstart/

```bash
# List charts available from a repo
helm search repo bitnami

# Describe chart
helm show chart bitnami/mysql

# Install chart
helm install bitnami/mysql --generate-name

# List chart installed/released
helm list

# Check the status of a release
helm status mysql-1736106819

# Uninstall a release
helm uninstall mysql-1736106819

# Download a Helm chart from a repository
helm pull bitnami/wordpress --untar

# Install from local Helm chart
helm install wp1 ./wordpress

```

## Artifact Hub

```bash
# Search
helm search hub wordpress
helm search repo wordpress
```

## Lifecycle management with Helm

```bash
# Search by app version
helm search repo bitnami/nginx --versions | grep 1.25

# Check helm chart
helm search repo bitnami/nginx --version 15.0.0
helm show chart bitnami/nginx --version 15.0.0

# Install a previous version of Nginx 1.25.0
helm install myenginx bitnami/nginx --version 15.0.0

# Upgrade the existing release
helm upgrade myenginx bitnami/nginx

# List the revisions of a release
helm history myenginx

# Rollback to an specific revision
helm rollback myenginx 1

```

## Creating helm chart

```bash
helm create hello-world-chart
helm install myapp1 ./hello-world-chart
```