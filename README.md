# tap-gitops

Drip drip from the TAP don't slip.

## About

A GitOps repository for usage with Tanzu Application Platform (TAP). Currently deploys a variety of ClassClaims for services including kafka, postgresql, mysql, and others.

## Pre-reqs

* TAP 1.6 cluster

## Setup

* Use `kubectl` to apply the [cluster-scoped resources](./cluster-resources/)
* Run `PROJECT_ID=<GCP PROJECT ID> ./scripts/create-gcp-providerconfig` to create a `ProviderConfig` for the GCP `Provider`
* Configure the namespace provisioner on the TAP cluster via `tap-values.yml` as follows:

```yaml
namespace_provisioner:
    controller: true
    additional_sources:
        # ...
        - git:
            ref: origin/main
            subPath: namespace-resources/services/claims
            url: https://github.com/teddyking/tap-gitops.git
          path: _ytt_lib/claims
        # ...
```

## Usage

* Create a namespace and label it for namespace provisioner - `kubectl create namespace dev && kubectl label namespaces dev apps.tanzu.vmware.com/tap-ns=""`
* List claims in the namespace - `kubectl get classclaims -n dev`
