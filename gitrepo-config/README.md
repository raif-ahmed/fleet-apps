
# Introduction

I was always impressed with [ArgoCD App Of Apps Pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern), which is Declaratively specify one ArgoCD app that consists only of other apps, Its main use is to bootstrap the K8s with all the needed applications.

This repo is an explanation on how to do the same in fleet using [fleet GitRepo](https://fleet.rancher.io/gitrepo-add)
This file will declaratively specify **main** fleet GitRepo. this **main** GitRepo will be deployed to local rancher (fleet-local) and will cause other GitRepo(s) (representing different applications) to be deployed and initialized.

```` yaml
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  name: cluster-config
  namespace: fleet-local
spec:
  repo: https://github.com/raif-ahmed/fleet-apps.git
  branch: main
  paths:
    - /cluster-config

  targets:
  - name: local
    clusterSelector: {}
````

<1> fleet-local: The fleet-local namespace is a special namespace used for the single cluster use case or to bootstrap the configuration of the Fleet manager. 

When fleet is installed the fleet-local namespace is created along with *one Cluster called local (in our case we renamed it to rancher) and one ClusterGroup called default*. If no targets are specified on a GitRepo, it is by default targeted to the ClusterGroup named default. **This means that all GitRepos created in fleet-local will automatically target the local Cluster.** *The local Cluster refers to the cluster the Fleet manager is running on*.

now this location 'cluster-config' is nothing but a lit of another GitRepo(s), deployed to fleet-default. But this time it will target the down-stream clusters.

# Workflow

So the proposed workflow for initializing the bootstrap GitRepo on Rancher is as follows;

-deploy GitRepo crd (gitrepo-cluster-config.yaml) to rancher.

- Once Rancher is up it will sync the cluster-config repo and initiate the fleet deployment process
