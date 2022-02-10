# ARGOCD Installation

ArgoCD has two types of installation

## Multi-Tenant

The multi tenant is the most common way to install ArgoCD, this type of installation has all components needed to interact with the cluster, an API server, a GUI ...

With this type of installation, two manifests are provided:

* Non High Availability: Used for development envronments.
* High Availability: Used in production environments, this installation has two types, which are different in term of scope, 'ha/install.yaml' lets argocd monitor all the cluster, and all namespaces, while 'ha/namespace-install.yaml' is used to use argocd in a single namespace

## Core

 This installation is suitable for sysadmins who don't need multi-tenancy features, it doesn't offer an aPI server, or UI, it contains the minimal functionalities of ArgoCD.


## High Availability Installation

In this document we will be installing a Multi-tenant high availability installation.

First of all, we need to create ArgoCD namespace

``` 
kubectl create -f manifests/00-namespace.yaml
```

Then, we will deploy argocd 

```
kubectl create -f manifests/01-argocd.yaml -n argocd
```

To access the UI, we will create a nodeport service

By default, ArgoCD creates a ClusterIP service named 'argocd-server' that targets the two instances of argocd, we will expose this service 

```
k expose svc argocd-server --name='argocd-dashboard-nodeport' --type='NodePort' --port='8080'
```

Then run the following command to get the nodeport

```
kubectl get svc | grep -i argocd-dashboard-nodeport | awk '{print $5}' | cut -d ':' -f 2 | cut -d / -f 1
```

You can access the dashboard via an ip of any cluster instance, and the nodeport we've just retrieved.

The username is admin and the password is stored in `argocd-initial-admin-secret` secret.