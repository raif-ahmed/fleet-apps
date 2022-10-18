
# Introduction

This repo/location 'cluster-config' is nothing but a list of another GitRepo(s), that will be deployed to fleet-default.

To understand the idea, lets examine main files.

- fleet.yaml

```` yaml
defaultNamespace: fleet-default

kustomize:
  # To use a kustomization.yaml different from the one in the root folder
  dir: ""
````

- kustomization.yaml, this will point to our repos folder.

```` yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- repos
```` 

# Mapping to Downstream Clusters

When deploying GitRepos to downstream clusters, the clusters must be mapped to a target.

## Defining targets

The deployment targets of GitRepo is done using the spec.targets field to match clusters or cluster groups. 

The YAML specification is as below.

- repos/gitrepo-cluster-longhorn.yaml

```` yaml
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  name: cluster-longhorn
  namespace: fleet-default
spec:
  repo: https://gitlab.devops.telekom.de/Access40/dev/c3/ecp-fleet/longhorn.git
  branch: develop
  clientSecretName: gitrepo-auth
  insecureSkipTLSVerify: false
  targets: <1>
  - name: all
    clusterSelector: {}
````

<1> you will need to customize the labels to match your cluster labels, if needed. For now longhorn will be deployed to any cluster (PODs and suse-tools)

The GitRepo defines which clusters a git repository should be deployed to and the fleet.yaml in the repository determines how the resources are customized per target.

All clusters and cluster groups in the *same namespace as the GitRepo* will be evaluated against all targets of that GitRepo. The targets list is evaluated one by one and if there is a match the resource will be deployed to the cluster. If no match is made against the target list on the GitRepo then the resources will not be deployed to that cluster. *Once a target cluster is matched the fleet.yaml from the git repository is then consulted for customizations*. The targetCustomizations in the fleet.yaml will be evaluated one by one and the first match will define how the resource is to be configured. If no match is made the resources will be deployed with no additional customizations.

# History

Here a bit feedback on our tries and findings

- We started with a all application are in the same repo but in separate folders and we switched over to a single git repo per application, because of the following;
  1. the challenge we saw with “folders” instead of separate “gitrepos” is that every push/merge will force all of them to get refreshed
  2. We may also had the requirement that different persons should have the rights for different applications.. (i.e. monitoring team vs. logging team… - not really needed for now, but keep room for the future -))
