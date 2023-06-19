# tap-gitops

Drip drip from the TAP don't slip.

## About

A GitOps repository for usage with Tanzu Application Platform (TAP). Currently deploys a variety of ClassClaims for services including kafka, postgresql, mysql, and others.

## Pre-reqs

* TAP 1.6 cluster

## Usage

### AWS Integration

Offerings:

* AWS RDS PostgreSQL (using default VPC)
  * All instances get created in the default VPC
* AWS RDS PostgreSQL (new VPC per instance)
  * New VPC and supporting resources created for each new instance

#### Usage

* Use `kubectl` to apply [cluster-resources/services/aws/provider-family.yml](./cluster-resources/services/aws/provider-family.yml)
* Export `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` and `AWS_SESSION_TOKEN` env vars, then run `./scripts/create-aws-providerconfig` to create a `ProviderConfig` for the AWS `Provider` family
* Choose an offering and apply the corresponding cluster-scoped resources (e.g. `kubectl apply -f cluster-resources/services/aws/default-vpc/`)
* Create claims for the service (e.g. `kubectl apply -f namespace-resources/services/claims/rds-default-vpc.yml`)

### GCP Integration

Offerings:

* GCP PostgreSQL CloudSQL

#### Usage

* Use `kubectl` to apply [cluster-resources/services/gcp/](./cluster-resources/services/gcp/)
* Run `PROJECT_ID=<GCP PROJECT ID> ./scripts/create-gcp-providerconfig` to create a `ProviderConfig` for the GCP `Provider`
* Create claims for the service (e.g. `kubectl apply -f namespace-resources/services/claims/gcp-cloud-sql.yml`)
