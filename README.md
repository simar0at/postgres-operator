<h1 align="center">PGO: The Postgres Operator originally from Crunchy Data</h1>

This is a fork of [CrunchyData's repository](https://github.com/simar0at/postgres-operator). The main purpose is to rebuild any binaries CrunchyData [distributes through their registry](https://www.crunchydata.com/developers/download-postgres/containers).

According to [their Terms of Use](https://www.crunchydata.com/developers/terms-of-use) the ACDH at the OeAW may not use their registry as we as an organization employ way more than 50 people.

The Dockerfiles needed to be changed as the ACDH does not have access to RedHat's CodeReady Builder repository for RHEL9. The repository included with UBI9 is not enough to build the postres-gis container.

[![Go Report Card](https://goreportcard.com/badge/github.com/simar0at/postgres-operator)](https://goreportcard.com/report/github.com/simar0at/postgres-operator)
![GitHub Repo stars](https://img.shields.io/github/stars/CrunchyData/postgres-operator)
[![License](https://img.shields.io/github/license/CrunchyData/postgres-operator)](LICENSE.md)
[![Discord](https://img.shields.io/discord/1068276526740676708?label=discord&logo=discord)](https://discord.gg/a7vWKG8Ec9)

# Production Postgres Made Easy

[PGO](https://github.com/simar0at/postgres-operator), the [Postgres Operator](https://github.com/simar0at/postgres-operator) from [Crunchy Data](https://www.crunchydata.com), gives you a **declarative Postgres** solution that automatically manages your [PostgreSQL](https://www.postgresql.org) clusters.

Designed for your GitOps workflows, it is [easy to get started](https://access.crunchydata.com/documentation/postgres-operator/v6/quickstart/) with Postgres on Kubernetes with PGO. Within a few moments, you can have a production-grade Postgres cluster complete with high availability, disaster recovery, and monitoring, all over secure TLS communications. Even better, PGO lets you easily customize your Postgres cluster to tailor it to your workload!

With conveniences like cloning Postgres clusters to using rolling updates to roll out disruptive changes with minimal downtime, PGO is ready to support your Postgres data at every stage of your release pipeline. Built for resiliency and uptime, PGO will keep your Postgres cluster in its desired state, so you do not need to worry about it.

PGO is developed with many years of production experience in automating Postgres management on Kubernetes, providing a seamless cloud native Postgres solution to keep your data always available.

Have questions or looking for help? [Join our Discord group](https://discord.gg/a7vWKG8Ec9).

# Build Instructions

The build process is automated in a [GitHub Action](https://github.com/simar0at/postgres-operator/blob/main/.github/workflows/build-and-push.yaml).

This action has one prerequisite: It needs a current version of the `Rocky-9-Container-Minimal.latest.x86_64.tar.xz` container. At the moment this can not be found on docker hub.
[Download it here](https://dl.rockylinux.org/pub/rocky/9/images/x86_64/Rocky-9-Container-Minimal.latest.x86_64.tar.xz) and use the following docker commands to push it to ghcr.io:
You can not refer to the loaded container image by name at the moment.

```bash
docker image load -i Rocky-9-Container-Minimal.latest.x86_64.tar.xz
docker tag $(docker image ls --format json | jq -r -s 'map(select(.Repository=="rockylinux"and .Tag=="9-minimal") | .ID) | unique | .[]') ghcr.io/simar0at/postgres-operator/rockylinux:9-minimal
docker push ghcr.io/simar0at/postgres-operator/rockylinux:9-minimal
```

This only builds postgres 18.3 at the moment. More or different versions need to be added to the Action YAML as needed.
At the moment this is based on EL 9.7. When the EL 9.8 based version and so on is released the image has to be updated and the build process needs to run again.

To build manually the various containers required by PGO, please see the build instructions found in the [components directory](https://github.com/simar0at/postgres-operator/tree/main/components) of this repository.
These images are built using [packages](https://www.postgresql.org/download) developed by the PostgreSQL Global Development Group (PGDG).  
Each subdirectory contains a `README` file describing how to build each of the various containers required by PGO.

The base image needed needs to be loaded like this:
```bash
docker image load -i Rocky-9-Container-Minimal.latest.x86_64.tar.xz
docker tag $(docker image ls --format json | jq -r -s 'map(select(.Repository=="rockylinux"and .Tag=="9-minimal") | .ID) | unique | .[]') localhost/rockylinux:9-minimal
```

# Installation

Crunchy Data makes PGO available as the orchestration behind Crunchy Postgres for Kubernetes. Crunchy Postgres for Kubernetes is the integrated product that includes PostgreSQL, PGO and a collection of PostgreSQL tools and extensions that includes the various [open source components listed in the documentation](https://access.crunchydata.com/documentation/postgres-operator/latest/references/components).

We recommend following our [Quickstart](https://access.crunchydata.com/documentation/postgres-operator/v6/quickstart/) for how to install and get up and running. However, if you can't wait to try it out, here are some instructions to get Postgres up and running on Kubernetes:

Run the following commands

```sh
kubectl apply -k config/namespace
kubectl apply --server-side -k config/default
```

For more information please read the [Quickstart](https://access.crunchydata.com/documentation/postgres-operator/v6/quickstart/) and [Tutorial](https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials).

These installation instructions provide the steps necessary to install PGO along with Crunchy Data's Postgres distribution, Crunchy Postgres, as Crunchy Postgres for Kubernetes. In doing so the installation downloads a series of container images from Crunchy Data's Developer Portal. For more information on the use of container images downloaded from the Crunchy Data Developer Portal or other third party sources, please see 'License and Terms' below. The installation and use of PGO outside of the use of Crunchy Postgres for Kubernetes will require modifications of these installation instructions and creation of the necessary PostgreSQL and related containers.

# Cloud Native Postgres for Kubernetes

PGO, the Postgres Operator from Crunchy Data, comes with all of the features you need for a complete cloud native Postgres experience on Kubernetes!

#### PostgreSQL Cluster [Provisioning][provisioning]

[Create, Scale, & Delete PostgreSQL clusters with ease][provisioning], while fully customizing your
Pods and PostgreSQL configuration!

#### [High Availability][high-availability]

Safe, automated failover backed by a [distributed consensus high availability solution][high-availability].
Uses [Pod Anti-Affinity][k8s-anti-affinity] to help resiliency; you can configure how aggressive this can be!
Failed primaries automatically heal, allowing for faster recovery time.

Support for [standby PostgreSQL clusters][multiple-cluster] that work both within and across [multiple Kubernetes clusters][multiple-cluster].

#### [Disaster Recovery][disaster-recovery]

[Backups][backups] and [restores][disaster-recovery] leverage the open source [pgBackRest][] utility and
[includes support for full, incremental, and differential backups as well as efficient delta restores][backups].
Set how long you to retain your backups. Works great with very large databases!

#### Security and [TLS][tls]

PGO enforces that all connections are over [TLS][tls]. You can also [bring your own TLS infrastructure][tls] if you do not want to use the defaults provided by PGO.

PGO runs containers with locked-down settings and provides Postgres credentials in a secure, convenient way for connecting your applications to your data.

#### [Monitoring][monitoring]

[Track the health of your PostgreSQL clusters][monitoring] using the open source [pgMonitor][] library.

#### [Upgrade Management][update-postgres]

Safely [apply PostgreSQL updates][update-postgres] with minimal impact to the availability of your PostgreSQL clusters.

#### Advanced Replication Support

Choose between [asynchronous][high-availability] and synchronous replication
for workloads that are sensitive to losing transactions.

#### [Clone][clone]

[Create new clusters from your existing clusters or backups][clone] with efficient data cloning.

#### [Connection Pooling][pool]

Advanced [connection pooling][pool] support using [pgBouncer][].

#### Pod Anti-Affinity, Node Affinity, Pod Tolerations

Have your PostgreSQL clusters deployed to [Kubernetes Nodes][k8s-nodes] of your preference. Set your [pod anti-affinity][k8s-anti-affinity], node affinity, Pod tolerations, and more rules to customize your deployment topology!

#### [Scheduled Backups][backup-management]

Choose the type of backup (full, incremental, differential) and [how frequently you want it to occur][backup-management] on each PostgreSQL cluster.

#### Backup to Local Storage, [S3][backups-s3], [GCS][backups-gcs], [Azure][backups-azure], or a Combo!

[Store your backups in Amazon S3][backups-s3] or any object storage system that supports
the S3 protocol. You can also store backups in [Google Cloud Storage][backups-gcs] and [Azure Blob Storage][backups-azure].

You can also [mix-and-match][backups-multi]: PGO lets you [store backups in multiple locations][backups-multi].

#### [Full Customizability][customize-cluster]

PGO makes it easy to fully customize your Postgres cluster to tailor to your workload:

- Choose the resources for your Postgres cluster: [container resources and storage size][resize-cluster]. [Resize at any time][resize-cluster] with minimal disruption.
- - Use your own container image repository, including support `imagePullSecrets` and private repositories
- [Customize your PostgreSQL configuration][customize-cluster]

#### [Namespaces][k8s-namespaces]

Deploy PGO to watch Postgres clusters in all of your [namespaces][k8s-namespaces], or [restrict which namespaces][single-namespace] you want PGO to manage Postgres clusters in!

[backups]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backups
[backups-s3]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backups#using-s3
[backups-gcs]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backups#using-google-cloud-storage-gcs
[backups-azure]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backups#using-azure-blob-storage
[backups-multi]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backups#set-up-multiple-backup-repositories
[backup-management]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/backup-management
[clone]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/disaster-recovery#clone-a-postgres-cluster
[customize-cluster]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/day-two/customize-cluster
[disaster-recovery]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/backups-disaster-recovery/disaster-recovery
[high-availability]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/day-two/high-availability/
[monitoring]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/day-two/monitoring/
[multiple-cluster]: https://access.crunchydata.com/documentation/postgres-operator/v6/architecture/disaster-recovery/#standby-cluster-overview
[pool]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/basic-setup/connection-pooling/
[provisioning]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/basic-setup/create-cluster/
[resize-cluster]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/cluster-management/resize-cluster/
[single-namespace]: https://access.crunchydata.com/documentation/postgres-operator/v6/installation/kustomize/#installation-mode
[tls]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/day-two/customize-cluster#customize-tls
[update-postgres]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/cluster-management/update-cluster
[k8s-anti-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#inter-pod-affinity-and-anti-affinity
[k8s-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[k8s-nodes]: https://kubernetes.io/docs/concepts/architecture/nodes/
[pgBackRest]: https://www.pgbackrest.org
[pgBouncer]: https://access.crunchydata.com/documentation/postgres-operator/v6/tutorials/basic-setup/connection-pooling/
[pgMonitor]: https://github.com/simar0at/pgmonitor

## Included Components

[PostgreSQL containers](https://github.com/simar0at/crunchy-containers) deployed with the PostgreSQL Operator include the following components:

- [PostgreSQL](https://www.postgresql.org)
  - [PostgreSQL Contrib Modules](https://www.postgresql.org/docs/current/contrib.html)
  - [PL/Python + PL/Python 3](https://www.postgresql.org/docs/current/plpython.html)
  - [PL/Perl](https://www.postgresql.org/docs/current/plperl.html)
  - [PL/Tcl](https://www.postgresql.org/docs/current/pltcl.html)
  - [pgAudit](https://www.pgaudit.org/)
  - [pgAudit Analyze](https://github.com/pgaudit/pgaudit_analyze)
  - [pg_cron](https://github.com/citusdata/pg_cron)
  - [pg_partman](https://github.com/pgpartman/pg_partman)
  - [pgnodemx](https://github.com/simar0at/pgnodemx)
  - [set_user](https://github.com/pgaudit/set_user)
  - [TimescaleDB](https://github.com/timescale/timescaledb) (Apache-licensed community edition)
  - [wal2json](https://github.com/eulerto/wal2json)
- [pgBackRest](https://pgbackrest.org/)
- [pgBouncer](http://pgbouncer.github.io/)
- [pgAdmin 4](https://www.pgadmin.org/)
- [pgMonitor](https://github.com/simar0at/pgmonitor)
- [Patroni](https://patroni.readthedocs.io/)
- [LLVM](https://llvm.org/) (for [JIT compilation](https://www.postgresql.org/docs/current/jit.html))

In addition to the above, the geospatially enhanced PostgreSQL + PostGIS container adds the following components:

- [PostGIS](http://postgis.net/)
- [pgRouting](https://pgrouting.org/)

[PostgreSQL Operator Monitoring](https://access.crunchydata.com/documentation/postgres-operator/latest/architecture/monitoring/) uses the following components:

- [pgMonitor](https://github.com/simar0at/pgmonitor)
- [Prometheus](https://github.com/prometheus/prometheus)
- [Grafana](https://github.com/grafana/grafana)
- [Alertmanager](https://github.com/prometheus/alertmanager)

For more information about which versions of the PostgreSQL Operator include which components, please visit the [compatibility](https://access.crunchydata.com/documentation/postgres-operator/v6/references/components/) section of the documentation.

## [Supported Platforms](https://access.crunchydata.com/documentation/postgres-operator/latest/overview/supported-platforms)

PGO, the Postgres Operator from Crunchy Data, is tested on the following platforms:

- Kubernetes
- OpenShift
- Rancher
- Google Kubernetes Engine (GKE), including Anthos
- Amazon EKS
- Microsoft AKS
- VMware Tanzu

# Contributing to the Project

Want to contribute to the PostgreSQL Operator project? Great! We've put together
a set of contributing guidelines that you can review here:

- [Contributing Guidelines](CONTRIBUTING.md)

Once you are ready to submit a Pull Request, please ensure you do the following:

1. Reviewing the [contributing guidelines](CONTRIBUTING.md) and ensure
   that you have followed the commit message format, added testing where
   appropriate, documented your changes, etc.
1. Open up a pull request based upon the guidelines. If you are adding a new
   feature, please open up the pull request on the `main` branch.
1. Please be as descriptive in your pull request as possible. If you are
   referencing an issue, please be sure to include the issue in your pull request

## Support

If you believe you have found a bug or have a detailed feature request, please open a GitHub issue and follow the guidelines for submitting a bug.

For general questions or community support, we welcome you to join our [community Discord](https://discord.gg/a7vWKG8Ec9) and ask your questions there.

For other information, please visit the [Support](https://access.crunchydata.com/documentation/postgres-operator/latest/support/) section of the documentation.

# Documentation

For additional information regarding the design, configuration, and operation of the
PostgreSQL Operator, pleases see the [Official Project Documentation][documentation].

[documentation]: https://access.crunchydata.com/documentation/postgres-operator/latest/

## Past Versions

Documentation for previous releases can be found at the [Crunchy Data Access Portal](https://access.crunchydata.com/documentation/).

# Releases

When a PostgreSQL Operator general availability (GA) release occurs, the container images are distributed on the following platforms in order:

- [Crunchy Data Customer Portal](https://access.crunchydata.com/)
- [Crunchy Data Developer Portal](https://www.crunchydata.com/developers)

The image rollout can occur over the course of several days.

To stay up-to-date on when releases are made available in the [Crunchy Data Developer Portal](https://www.crunchydata.com/developers), please sign up for the [Crunchy Data Developer Program Newsletter](https://www.crunchydata.com/developers#email). You can also [join the PGO project community discord](https://discord.gg/a7vWKG8Ec9)

# FAQs, License and Terms

For more information regarding PGO, the Postgres Operator project from Crunchy Data, and Crunchy Postgres for Kubernetes, please see the [frequently asked questions](https://access.crunchydata.com/documentation/postgres-operator/latest/faq).

The installation instructions provided in this repo are designed for the use of PGO along with Crunchy Data's Postgres distribution, Crunchy Postgres, as Crunchy Postgres for Kubernetes. The unmodified use of these installation instructions will result in downloading container images from Crunchy Data repositories - specifically the Crunchy Data Developer Portal. The use of container images downloaded from the Crunchy Data Developer Portal are subject to the [Crunchy Data Developer Program terms](https://www.crunchydata.com/developers/terms-of-use).

The PGO Postgres Operator project source code is available subject to the [Apache 2.0 license](LICENSE.md) with the PGO logo and branding assets covered by [our trademark guidelines](docs/static/logos/TRADEMARKS.md).
