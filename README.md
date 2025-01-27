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
    - [Structure of a Chart](#structure-of-a-chart)
    - [Objects](#objects)
    - [Verifying Helm charts](#verifying-helm-charts)
    - [Modifying Helm Charts](#modifying-helm-charts)
      - [Show values](#show-values)
  - [Libraries](#libraries)
  - [Tests](#tests)
  - [Functions](#functions)
    - [String](#string)
  - [Flow Controls](#flow-controls)
  - [Template helpers](#template-helpers)
  - [Hooks](#hooks)
  - [Publish Helm Chart to GitHub](#publish-helm-chart-to-github)
    - [1. Package Your Helm Chart](#1-package-your-helm-chart)
    - [2. Generate the index.yaml](#2-generate-the-indexyaml)
    - [3. Push to the repository](#3-push-to-the-repository)
    - [4. Configure GitHub Pages](#4-configure-github-pages)
    - [5. Run the app from the repository](#5-run-the-app-from-the-repository)

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

# for error related to dial tcp [2606:50c0:8003::153]:443: connect: no route to host
# this will forces Helm to use IPv4 instead of IPv6
export HELM_HOST=0.0.0.0

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

### Structure of a Chart

MyChart/
  Chart.yaml --> Contain information about the chart, dependencies, version, etc.
  LICENSE --> Contain license informatino, if any.
  README.md --> Markdown-formatted chart description, options, etc.
  values.yaml --> Contain predefined (default) values which will be passed to the templates. Values in this can be overriden by values passed on the command line during installation.
  values.schema.json --> Provides an optional schema for the values.yaml file.
  charts/ --> Dependencies are downloaded into this folder
  crds/ --> Contains Custom Resource Definition (CRDs)
  templates/ --> This directory contains templates which are passed through the template engine. This templates incorporate the content of the values.yaml
  templates/NOTES.txt --> This plain text file is printed out post-installation and when viewing the release status

```bash
helm create hello-world-chart
helm install myapp1 ./hello-world-chart
helm install myapp2 ./hello-world-chart
helm install myapp3 ./hello-world-chart
helm upgrade myapp1 ./hello-world-chart
helm upgrade myapp1 ./hello-world-chart --set replicaCount=3
helm upgrade myapp3 ./hello-world-chart --debug --force
```


### Objects
Release
* Release.Name
* Release.Namespace
* Release.Revision
* Release.IsUpgrade
* Release.IsInstall
* Release.Service

Chart
* Chart.Name
* Chart.ApiVersion
* Chart.Version
* Chart.Type
* Chart.Home

Capabilities
* Capabilities.KubeVersion
* Capabilities.ApiVersions
* Capabilities.HelmVersion
* Capabilities.GitCommit
* Capabilities.GitTreeState
* Capabilities.GoVersion

User Defined
* Values.replicaCount
* Values.image

### Verifying Helm charts

```bash
# Lint: helps to verify that the chart and the yaml format is correct
helm lint ./hello-world-chart

# Template: helps to verify that the templating part is working as expected, render the template before send to Kubernetes
helm template ./hello-world-chart
helm template myapp4 ./hello-world-chart
helm template myapp4 ./hello-world-chart --set replicaCount=3

# Dry Run: helps to verify that the chart works well with Kubernetes itself.
helm install myapp4 ./hello-world-chart --set replicaCount=3 --dry-run

```

### Modifying Helm Charts

1. Inline using set: you can use the set option with the install command to override the contents of the values.yaml file.
Exampple
```bash
helm template myapp4 ./hello-world-chart --set replicaCount=3
```
2. Passing a file: You can override the chart’s values.yaml by passing a file that contains the values you want to override. This has the advantage of being able to commit the override file to source control to track changes.
Example

override-values.yaml
```yaml
replicaCount: 4
image:
  repository: nginx
  tag: 1.9.6
```

```bash
helm install myapptest ./hello-world-chart --values override-values.yaml
```
3. Customize the Chart: You can “fetch” the chart and then directly overwrite the values.yaml with your custom values. This is a destructive change and creates a new version of the chart. 

Example
```bash
# Fetch the Chart
helm fetch bitnami/wordpress

# Uncompress the Chart
tar -xvz wordpress-24.1.5.tgz

# Modify the value wordpress/values.yaml
```

#### Show values

Tip: The command helm show values can be used to show all of the information contained in the values.yaml file
example:
```bash
helm show values ./hello-world-chart
helm show values langchain/langsmith
```

## Libraries
A library is a shared template that can be used to prevent repetitive code.


## Tests
These tests also help the chart consumer understand what your chart is supposed to do.

A test in a helm chart lives under the `templates/` directory and is a job definition that specifies a container with a given command to run. The container should exit successfully (exit 0) for a test to be considered a success. The job definition must contain the helm test hook annotation: `helm.sh/hook: test`.

Example https://helm.sh/docs/topics/chart_tests/
```bash
helm create demo

```
demo/templates/tests/test-connection.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "demo.fullname" . }}-test-connection"
  labels:
    {{- include "demo.labels" . | nindent 4 }}
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['{{ include "demo.fullname" . }}:{{ .Values.service.port }}']
  restartPolicy: Never
```

Run the tests
```bash
# Install the Chart
helm install demo demo

# Run the test
helm test demo
```


## Functions
https://helm.sh/docs/chart_template_guide/function_list/
Helm includes many template functions you can take advantage of in templates.

They are listed here and broken down by the following categories:
* Cryptographic and Security
* Date
* Dictionaries
* Encoding
* File Path
* Kubernetes and Chart
* Logic and Flow Control
* Lists
* Math
* Float Math
* Network
* Reflection
* Regular Expressions
* Semantic Versions
* String
* Type Conversion
* URL
* UUID

In the following example, we will use some functions like 
default, lower, title, b64enc, and quote

Example
[functions-chart/](functions-chart/)

### String
* upper
* lower
* quote
* replace "x" "y"
* shuffle
* camelcase
* kebabcase
* snakecase
* title
* contains
* hasPrefix
* ident
* nospace
* print
* printf
* randAlpha
* randAlphaNum
* randNumeric
* substr
* trim
* trimAll
* trunc

## Flow Controls
https://helm.sh/docs/chart_template_guide/control_structures/

Control structures (called "actions" in template parlance) provide you, the template author, with the ability to control the flow of a template's generation. Helm's template language provides the following control structures:
* `if/else` for creating conditional blocks
* `with` to specify a scope
* `range`, which provides a "for each"-style loop


Example `if/else`
[conditionals-chart/](conditionals-chart/)

Example `with`
[with-chart/](with-chart/)

## Template helpers
> A named template (sometimes called a partial or a subtemplate) is simply a template defined inside of a file, and given a name.
Official documentation: https://helm.sh/docs/chart_template_guide/named_templates/

In my example just defined common lables>
[webapp-color/teamplates/_helpers.tpl](webapp-color/teamplates/_helpers.tpl)
```yaml
{{/* Generate basic labels */}}
{{- define "webapp-color.labels" }}
    app: {{ .Values.name }}
    environment: {{ .Values.environment }}
    date: {{ now | htmlDate }}
{{- end }}
```

Then used in each manifest with `include`
```yaml
metadata:
  labels:
    {{- include "webapp-color.labels" . }}
```

## Hooks
https://helm.sh/docs/topics/charts_hooks/#helm

Helm provides a hook mechanism to allow chart developers to intervene at certain points in a release's life cycle. For example, you can use hooks to:
* Load a ConfigMap or Secret during install before any other charts are loaded.
* Execute a Job to back up a database before installing a new chart, and then execute a second job after the upgrade in order to restore data.
* Run a Job before deleting a release to gracefully take a service out of rotation before removing it.

Note: hook resources are not deleted with helm, but deletions are controled with an anotation, [more info](https://helm.sh/docs/topics/charts_hooks/#hook-deletion-policies).
```bash
annotations:
  "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
```
values:
* before-hook-creation	Delete the previous resource before a new hook is launched (default)
* hook-succeeded	Delete the resource after the hook is successfully executed
* hook-failed	Delete the resource if the hook failed during execution

Example
[./chart-hooks-example](./chart-hooks-examplel)

```bash
# will delay 10 seconds after install
helm install app ./hello-world-chart

## After installed, we can see the install-hook pod with status completed
k get pod
k logs install-hook-xxxx

# The upgrade hook doesn't include a delay, but using hooks add a bit of delay
helm upgrade app ./hello-world-chart

## After upgrade, we can see the upgrade-hook pod with status completed
k get pod
k logs upgrade-hook-xxxx
```

## Publish Helm Chart to GitHub
### 1. Package Your Helm Chart
```bash
helm package webapp-color/
```
*This creates a .tgz file (e.g., webapp-color-0.1.0.tgz) in the current directory.*

### 2. Generate the index.yaml
Use the helm repo index command to generate the index.yaml file:
```bash
helm repo index . --url https://<your-github-username>.github.io/<repository-name>

# in my case
helm repo index . --url https://josefloressv.github.io/helm
```
Make sure you have configured GitHub pages https://pages.github.com/

### 3. Push to the repository
Make sure the .tgz file and index.yaml file are in the root of your repository, then commit and push your changes.

### 4. Configure GitHub Pages
1. Go to your repository on GitHub.
2. Navigate to Settings > Pages.
3. Under "Build and deployment > Source" select the branch you want to use (e.g., main) and the root directory.
4. Save the settings.

Now you have published the Helmchart to GitHub Pages

### 5. Run the app from the repository

```bash
# Add your GitHub Pages URL as a Helm repository:
helm repo add my-charts https://josefloressv.github.io/helm
helm repo update

# List the Charts from your repository
helm search repo my-charts

# Install teh Chart
helm install myapp my-charts/webapp-color
```