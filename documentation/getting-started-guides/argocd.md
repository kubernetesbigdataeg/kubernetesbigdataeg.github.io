---
layout: page
title: Argo CD
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

<img align="center" width="50%" src="../../images/argo-logo.png">

## Introduction

Argo CD is an open source GitOps operator for Kubernetes. The project is a 
part of the Argo family, a set of cloud-native tools for running and managing jobs and
applications on Kubernetes: 

* Argo CD: Declarative continuous delivery with a fully-loaded UI.
* Argo Rollouts: Advanced Kubernetes deployment strategies such as Canary and Blue-Green made easy.
* Argo Events: Event based dependency management for Kubernetes.

In 2020 Argo CD was accepted by the Cloud Native Computing Foundation (CNCF) as 
an incubation-level hosted project. In 2022 Argo family was CNCF graduated project
(Graduated projects are considered stable and used successfully in production).

### Continuous Deployment (CD)

Continuous deployment (CD) is a strategy or methodology for software releases 
where any new code update or change made through the rigorous automated test 
process is deployed directly into the live production environment, where it 
will be visible to customers (users).

### GitOps

Git is the most widely used version-control system in the software industry today.
GitOps is a set of procedures that uses the power of Git to provide both revision and
change control within the Kubernetes platform.

Using GitOps with Kubernetes is a natural fit, with the deployment of declara-
tive Kubernetes manifest files being controlled by common Git operations. GitOps
brings the core benefits of Infrastructure as Code and immutable infrastructure to
the deployment, monitoring, and life-cycle management of Kubernetes applications
in an intuitive, accessible way.

## ArgoCD Operation overview

Argo CD follows the GitOps pattern of using Git repositories as the source of 
truth for defining the desired application state. Kubernetes manifests can be 
specified in several ways:

* kustomize applications
* helm charts
* jsonnet files
* Plain directory of YAML/json manifests
* Any custom config management tool configured as a config management plugin

Argo CD automates the deployment of the desired application states in the specified 
target environments. Application deployments can track updates to branches, tags, 
or pinned to a specific version of manifests at a Git commit.

## Installation

You can install ArgoCD in two different fashions, one of them is fully of features, 
and the other one is a sort of headless installation without UI, SSO and other
features.

### Fully installation

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This will create a new namespace, argocd, where Argo CD services and application 
resources will live. The installation manifests include ClusterRoleBinding resources 
that reference argocd namespace. If you are installing Argo CD into a different 
namespace then make sure to update the namespace reference.

This installation creates a set of:

* customresourcedefinitions
* serviceaccounts
* role.rbac, clusterrole.rbac, rolebinding.rbac, clusterrolebinding.rbac
* configmaps
* secrets
* deployment, statefulsets
* networkpolicy

### Only Core installation

The core installation is most suitable for cluster administrators who independently 
use Argo CD and don't need multi-tenancy features. This installation includes 
fewer components and is easier to setup. The bundle does not include the API 
server or UI, and installs the lightweight (non-HA) version of each component.

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```

### Download Argo CD CLI

```
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
mv argocd-linux-amd64 $HOME/.local/bin/argocd
chmod +x $HOME/.local/bin/argocd
```

```
argocd version

argocd: v2.5.4+86b2dde
  BuildDate: 2022-12-06T20:09:11Z
  GitCommit: 86b2dde8e4bf1187acd2b4294e94451cd104dad8
  GitTreeState: clean
  GoVersion: go1.18.8
  Compiler: gc
  Platform: linux/amd64
FATA[0000] Argo CD server address unspecified
```

## Inspect ArgoCD components

```
kubectl get po -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          3m38s
argocd-applicationset-controller-66b744f75b-jw9n4   1/1     Running   0          3m39s
argocd-dex-server-d5848bfff-d77d8                   1/1     Running   0          3m39s
argocd-notifications-controller-69996dd789-9sr4p    1/1     Running   0          3m38s
argocd-redis-684fb8c6dd-bmn62                       1/1     Running   0          3m38s
argocd-repo-server-5b9d7dfb9b-sxt4x                 1/1     Running   0          3m38s
argocd-server-6f4bf59f96-kdr7r                      1/1     Running   0          3m38s
```

Argo CD is largely stateless, all data is persisted as Kubernetes objects, which 
in turn is stored in Kubernetes' etcd. Redis is only used as a throw-away cache 
and can be lost. When lost, it will be rebuilt without loss of service.

* **argocd-repo-server**

The argocd-repo-server is responsible for cloning Git repository, keeping it up 
to date and generating manifests using the appropriate tool.

* **argocd-application-controller**

The argocd-application-controller uses argocd-repo-server to get generated manifests 
and Kubernetes API server to get actual cluster state.

* **argocd-server**

The argocd-server is stateless and probably least likely to cause issues. You 
might consider increasing number of replicas to 3 or more to ensure there is 
no downtime during upgrades.

* **argocd-dex-server, argocd-redis**

The argocd-dex-server uses an in-memory database, and two or more instances 
would have inconsistent data. argocd-redis is pre-configured with the understanding 
of only three total redis servers/sentinels.

## ArgoCD UI Access

By default, the Argo CD API server is not exposed with an external IP. A simple way
of accessing tho the UI server is using kubectl port-forwarding to the API server 
without exposing the service:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

The API server can then be accessed using https://localhost:8080

The initial password for the admin account is auto-generated and stored as clear 
text in the field password in a secret named argocd-initial-admin-secret in your 
Argo CD installation namespace. You can simply retrieve this password using kubectl:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
k4tynBk3hzJ8i82Q
```

The API server can then be accessed using https://localhost:8080 (admin/k4tynBk3hzJ8i82Q)

**Note**: You should delete the argocd-initial-admin-secret from the Argo CD namespace 
once you changed the password. The secret serves no other purpose than to store 
the initially generated password in clear and can safely be deleted at any time. 
It will be re-created on demand by Argo CD if a new admin password must be re-generated.

### Changing the user/password with the CLI

```
argocd login localhost:8080
    WARNING: server certificate had error: x509: certificate signed by unknown authority. Proceed insecurely (y/n)? y
    Username: admin
    Password: 
    'admin:login' logged in successfully
    Context 'localhost:8080' updated

argocd account update-password
```
**Note**: The CLI environment must be able to communicate with the Argo CD API server. 
If it isn't directly accessible with the running "Kubectl port-forwarding", you 
can tell the CLI to access it using port forwarding through one of these mechanisms: 
1) add ```--port-forward-namespace``` argocd flag to every CLI command; or 
2) set ARGOCD_OPTS environment variable: ```export ARGOCD_OPTS='--port-forward-namespace argocd'```.



