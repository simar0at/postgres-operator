# Copyright 2018 - 2026 Crunchy Data Solutions, Inc.
#
# SPDX-License-Identifier: Apache-2.0
ARG DOCKER_REGISTRY="localhost"
ARG RHEL9_BASE="${DOCKER_REGISTRY}/rockylinux:9-minimal"
ARG PGBACKREST_VERSION=2.58.0

FROM docker.io/library/golang:bookworm AS build

# Run as user 0 to allow later chmod'ing
USER 0

# Copy files to build go binary and add license
COPY . .

# Build the crunchy-pgbackrest binary and extract the licenses
# '-buildvcs=false' is required to avoid errors when Go tries to embed version
# control information, see https://go.dev/doc/go1.18#go-version
RUN go build -buildvcs=false -o ./bin/pgbackrest ./cmd/pgbackrest
RUN go run ./hack/extract-licenses.go ./licenses ./bin/pgbackrest

# Change permissions on all license files to read-only
RUN find ./licenses '(' -type d -exec chmod 0555 '{}' + ')' -o '(' -type f -exec chmod 0444 '{}' + ')'

# Create final image
FROM ${RHEL9_BASE}

# Copy licenses and crunchy-pgbackrest binary from build
COPY --from=build /go/licenses /licenses
COPY --from=build --chmod=0555 /go//bin/pgbackrest /opt/crunchy/bin/pgbackrest

# As of pgbackrest 4.26 libssh2 is required from epel.
RUN rpm -ivh \
    https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm \
    && sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/epel*.repo

# Install pgbackrest and its requisite packages.
# The pgbackrest package expects a postgres user during installation.
# Delete the repo file as it is large and unneeded.
ARG PGBACKREST_VERSION
RUN source /etc/os-release && \
 rpm -ivh "https://download.postgresql.org/pub/repos/yum/reporpms/EL-$REDHAT_SUPPORT_PRODUCT_VERSION-$(arch)/pgdg-redhat-repo-EL-$REDHAT_SUPPORT_PRODUCT_VERSION-latest.noarch.rpm" ||\
 rpm -ivh "https://download.postgresql.org/pub/repos/yum/reporpms/EL-$REDHAT_SUPPORT_PRODUCT_VERSION-$(arch)/pgdg-redhat-repo-latest.noarch.rpm" \
 && microdnf install -y --nodocs --setopt install_weak_deps=0 'shadow-utils' \
 && groupadd --gid 26 postgres \
 && useradd  --gid 26 --uid 26 --no-log-init postgres \
 && microdnf install -y --nodocs --setopt install_weak_deps=0 \
    --enablerepo="epel" \
    'libssh2' \
    "pgbackrest-${PGBACKREST_VERSION}" \
    'gettext' \
    'nss_wrapper-libs' \
    'procps-ng' \
    'tar' \
 && microdnf clean all \
 && rm -f /etc/yum.repos.d/redhat.repo

# Remove the default pgbackrest spool directory and configuration file to ensure
# no existing files interfere with pgbackrest operations
RUN rm -rf /var/spool/pgbackrest
RUN rm -f /etc/pgbackrest.conf

USER 26
