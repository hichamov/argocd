# ARGOCD

## Introduction

### What is GITOPS

GITOPS can be considered as an extension of infrastructure as code, that uses Git as the version control system.

It allows us to manipulate kubernetes objects deployment automated, via a process (controller), that watches a desired state of our application, defined in manifests stored in Git, and try to match the actual state of the cluster to the desired state.

Gitops makes application management as easy as performing a git revert, or changing to an older version of your image in git, then the gitops controller takes care of the rollback process. 

### Gitops Principals

Declarative vs imperative approach: gitops demandes that the entire deployment process declarative.

Git: is the single source of truth, holds the desired state.

Operators: watches the changes in git, and tries to match the actuel state with the desired state, a single gitops operator can manage multiple clusters.

Reconcelation: the operator always makes sure that the system is always in the desired state, through a loop of (Observer, Diff, Act). this is used to avois config drift or manual/human misconfigurations. 

## Gitops Introduction

### Devops vs Gitops

In a traditional devops pipeline, the developer usualy pushes code into git, and a ci pipeline build the code, generate an atrefact, and create a docker image, then, the devops team changes the deployment through kubectl command.

In a Gitops pipeline, we have two git repositories, one for the application, and another for kubernetes deployment manifests, we use the same pipeline, except that after the docker image is created, the ci system pulls the application manifest repo, changes the tag of image, and push back the change to git, then, the gitops controller applies the change to the cluster.

### Push vs Pull Based Deployments

Most of the deployment tools use a push based strategy, meang that expose the API server of your cluster, and the client pushed the manifests through kubectl.

This can be be dangerous as the API server is the most important component of the cluster.

ArgoCD uses a pull based approach, which is more secure, as the controller is deployed inside the cluster.

### Gitops Features Set

Git is the single source of truth
Everything is stored in git
Rollback can be done through git revert
Everything is auditable
Can be integrated with a CI tool.
Detecting/Avoiding configuration drift.
Multi cluster deployment can be reached with GitOPS operators.

### Gitops Benefits and drawbacks

Benefits:

Lightweight and vendor neutral
Faster, Safer, Immutable and reproducible deployments.
Eliminating configuration drift.
Uses familiar tools and processes (git).
Revisiong with history.
 
Challenges:

Does not help with secret management.
Increase the nbr of git repositories.
Challenges with programmatic updates (Multiple PRs)

### Gitops projects and tools

ArgoCD
FluxCD
JenkinsX
Atlantis (Gitops for terraform)


## ArgoCD Basics

### What/Why/How ArgoCD

Why use ArgoCD ?

It extends the benefits of declarative specifications and Git-Based configuration management
It is the first step in achieving continuous operations based on monitoring, analytics and automated remediation.
It can deploy to multiple clusters and is enterprise friendly (auditablity, compliance, security, RBAC, SSO ...)


What is ArgoCD ?

ArgoCD is a declarative, Gitops continuous delivery tool for kubernetes resources defined in Git repositories.
continuously monitors running applications and comparing their live state to the desired state.
It reports the deviations and provides visualizations to help developers manually or automatically sync the live state with the desired state.


How ArgoCD works ?

It follows the gitops pattern by using Git repositories as the source of truth for the desired stateof app and the target deployment envs.
IT automates the synchronization of the desired application state with each of the specified target envs.
It supports (kustomize, Helm, ksonnet, Jsonnet, Yaml/Json manifests)

### Concepts/Terminology

Application: A group of kubernetes resources as define by a manifest.
Application source type: The tool is used to build the application, Eg Helm, kustomize, ksonnet.
Project: Provide a logical grouping of applications, which is useful when ArgoCD is used by multiple teams.

Target state: the desired state of an application, as represented by files in a Git repository.
Live state: The live state of that application what pods , configmaps, secrets, etc are created/deployed in a kubernetes cluster.

Sync status: Whether or not the live state matches the target state, is the deployed application the same as Git says it should be ?
Sync: The process of making an application move to its target state, Eg by applying changes to a kubernetes cluster.
Sync operation status: Whether or not a sync succeeded.

Refresh: Compare the latest code in Git with the live state. Figure out what is different.
Health: The health of the application, it is running correctly, Can it serve requests ?

### ArgoCD Features

- Automated deployment of applications to specified target environment in multiple clusters.
- Audit trails fo application events and API calls.
- SSO Integration (OIDC, OAuth2, LDAP, SAML2.0, Github ...)
- Webhook Integration (Github, Bitbucket, Gitlab)
- Rollback/Roll-anywhere to any application configuration commited in Git repository.
- Web UI which provides real-time view of application activity.
- Automated config drift detection and visualization.
- Out of the box prometheus metrics.
- support for multiple config management/templating tools (Helm, yaml ...)
- Presync, sync, Postsync hooks to support complex application rollouts (blue/green, canary upgrades)
- CLI and access tokens, for automation and CI integration.
- Health status analysis of application resources.
- Automated or manual syncing of applications to its desired state.

### ArgoCD Architecture

Users and systems can connect to ArgoCD using different methodes:

* CLI: Manual sync, Setup RBAC,SSO
* UI: Create ArgoCD Application, projects...
* Webhook events: Github for PR management.
* API (GRPC Rest): for ci systems
* Exposes prometheus metrics
* Can connect to multiple kubernetes clusters.
* Notification server.

### Installation options


* Multitenant: For organizations with multiple projects/clusters, and have an HA deployment.
* Core: For teams that have a single project, and don't need HA.

Can ba use either by kubectl or helm:

``` 
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/master/manifests/install.yaml
````

### ArgoCD App & projects

* Application: An application in argocd represents an application deployed in the cluster, and have two main parameters, a source (github repo), and a destination (a kubernetes cluster to be deployed on).

An application has also a project name which it belongs, a sync policy ...
The source can contain yaml files, helm charts.
Can be create via the UI, CLI or YAML specification.

Here is an example of an argocd application:

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: guestbook
```


