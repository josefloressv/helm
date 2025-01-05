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

## Introduction

Use Helm to:
* Find and use popular software packaged as Helm Charts to run in Kubernetes
* Share your own applications as Helm Charts
* Create reproducible builds of your Kubernetes applications
* Intelligently manage your Kubernetes manifest files
* Manage releases of Helm packages

The final release of Helm 2 was version 2.17.0, which was made available on November 13, 2020. This release marked the end of Helm 2's support, with users encouraged to migrate to Helm 3 for continued updates and improvements. Helm 3 was released in November 2019, introducing significant changes, including the removal of the Tiller component, enhancing security and simplifying the Helm architecture.

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
```