# the-dripping-tap

Drip drip from the TAP don't slip.

## About

This repo holds configurations and integrations for a variety of backing services for Tanzu Application Platform (TAP). Suitable for learning and demonstration purposes only.

### TL;DR list of service integrations

* **on-cluster/unmanaged**
  * Kafka
  * MongoDB
  * MySQL
  * PostgreSQL
  * RabbitMQ
  * Redis
* **cloud/managed**
  * AWS RDS
  * GCP CloudSQL

## Pre-reqs

* TAP 1.6 cluster

## Usage

### AWS Integration

Offerings:

* [rds-postgresql-default-vpc](./cluster-resources/services/aws/rds-postgresql-default-vpc/) - AWS RDS PostgreSQL instances
  * All instances get created in the default VPC
  * Requires a one-time manual configuration to open port 5432 on the security group associated with the default VPC
* [rds-postgresql-new-vpc-per-instance](./cluster-resources/services/aws/rds-postgresql-new-vpc-per-instance/) - AWS RDS PostgreSQL instances
  * New VPC and supporting resources created for each new instance
  * TODO: Take the Managed Resources in this dir and wrap them in an XRD/Composition/Class

#### Usage

* **Operator Steps**
  * Use `kubectl` to apply [cluster-resources/services/aws/provider-family.yml](./cluster-resources/services/aws/provider-family.yml)
  * Export `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` and `AWS_SESSION_TOKEN` env vars, then run `./scripts/create-aws-providerconfig` to create a `ProviderConfig` for the AWS `Provider` family
  * Choose an offering and apply the corresponding cluster-scoped resources
    * E.g. `kubectl apply -f cluster-resources/services/aws/rds-postgresql-default-vpc/`
* **Developer Steps**
  * Create claims for the service
    * `kubectl apply -f namespace-resources/services/claims/managed/aws/rds-postgresql-default-vpc.yml`, or
    * `tanzu service class-claim create aws-rds-psql-1 --class rds-postgresql-default-vpc -p storageGB=20`

### GCP Integration

Offerings:

* [cloudsql-postgresql](./cluster-resources/services/gcp/cloudsql-postgresql/) - CloudSQL PostgreSQL instances

#### Usage

* **Operator Steps**
  * Use `kubectl` to apply [cluster-resources/services/gcp/provider.yml](./cluster-resources/services/gcp/provider.yml)
  * Run `PROJECT_ID=<GCP PROJECT ID> ./scripts/create-gcp-providerconfig` to create a `ProviderConfig` for the GCP `Provider`
  * Choose an offering and apply the corresponding cluster-scoped resources
    * E.g. `kubectl apply -f cluster-resources/services/gcp/cloudsql-postgresql/`
* **Developer Steps**
  * Create claims for the service
    * `kubectl apply -f namespace-resources/services/claims/managed/gcp/cloudsql-postgresql.yml`, or
    * `tanzu service class-claim create gcp-cloudsql-psql-1 --class cloudsql-postgresql -p storageGB=20`

### Bitnami Integration

* **Operator Steps**
  * No additional steps required
* **Developer Steps**
  * Create claims for whichever services you need
    * `kubectl apply -f namespace-resources/services/claims/unmanaged/kafka.yml`, or
    * `tanzu service class-claim create bitnami-kafka-1 --class kafka-unmanaged -p storageGB=5`

### Namespace Provisioner Integration

* Configure namespace provisioner on a TAP cluster via `tap-values.yml` as follows:

```yaml
namespace_provisioner:
    controller: true
    additional_sources:
        # ...
        - git:
            ref: origin/main
            subPath: namespace-resources/services/claims/unmanaged # update this per your requirements
            url: https://github.com/teddyking/tap-gitops.git
          path: _ytt_lib/claims
        # ...
```
