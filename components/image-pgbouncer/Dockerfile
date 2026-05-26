# Copyright 2017 - 2026 Crunchy Data Solutions, Inc.
#
# SPDX-License-Identifier: Apache-2.0
ARG DOCKER_REGISTRY="localhost"
ARG RHEL9_BASE="${DOCKER_REGISTRY}/rockylinux:9-minimal"
FROM ${RHEL9_BASE}

ARG PGBOUNCER_VERSION=1.24.1

# The c-ares package is in the rhel-9-for-$(arch)-baseos-rpms repo, so we must enable it
RUN source /etc/os-release && \
 rpm -ivh "https://download.postgresql.org/pub/repos/yum/reporpms/EL-$REDHAT_SUPPORT_PRODUCT_VERSION-$(arch)/pgdg-redhat-repo-EL-$REDHAT_SUPPORT_PRODUCT_VERSION-latest.noarch.rpm" ||\
 rpm -ivh "https://download.postgresql.org/pub/repos/yum/reporpms/EL-$REDHAT_SUPPORT_PRODUCT_VERSION-$(arch)/pgdg-redhat-repo-latest.noarch.rpm" \
 && microdnf install -y --nodocs --setopt install_weak_deps=0 \
 "pgbouncer-${PGBOUNCER_VERSION}" \
 'procps-ng' \
 'tar' \
 && microdnf clean all \
 && rm -f /etc/yum.repos.d/redhat.repo

USER 2
