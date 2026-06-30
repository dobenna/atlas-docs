<!--
===============================================================================
Project      : Project Atlas
Repository   : atlas-docs
File         : architecture/vps-assessment.md
Description  : VPS assessment for the Project Atlas lab environment
Author       : Elba Guerra
Created      : 2026-06-29
Last Updated : 2026-06-29
Version      : 0.1.0
Status       : Draft
===============================================================================
-->

# VPS Assessment

## 1. Purpose

This document records the initial assessment of the VPS that will host
Project Atlas.

The objective is to understand the current infrastructure before
deploying new platform components.

------------------------------------------------------------------------

## 2. Host Information

  Item               Value
  ------------------ -------------------------
  Hostname           arwen
  Operating System   Ubuntu 24.04.4 LTS
  Kernel             Linux 6.8.0-124-generic
  Virtualization     KVM
  Architecture       x86_64

------------------------------------------------------------------------

## 3. Current Resources

### Storage

  Item         Value
  ------------ --------
  Total Disk   193 GB
  Used         13 GB
  Available    181 GB
  Usage        7%

### Memory

  Item        Value
  ----------- -----------
  RAM         11 GiB
  Used        \~909 MiB
  Available   \~10 GiB
  Swap        4 GiB
  Swap Used   0 B

The current resources are sufficient to start the Project Atlas
platform.

------------------------------------------------------------------------

## 4. Existing Containers

  Container   Image                      Status    Notes
  ----------- -------------------------- --------- ---------------------
  npm         jc21/nginx-proxy-manager   Running   Reverse Proxy
  n8n         n8nio/n8n                  Running   Automation Platform

------------------------------------------------------------------------

## 5. Published Ports

  Port   Service
  ------ --------------------
  22     SSH
  80     HTTP
  81     NPM Administration
  443    HTTPS

------------------------------------------------------------------------

## 6. Current Platform

                    Internet
                        │
                        ▼
             Nginx Proxy Manager
              │              │
              ▼              ▼
            n8n        Future Atlas Services

Host
├── Ubuntu 24.04
├── Podman
├── containerd
└── /opt/atlas

Future
└── k3s
    ├── ArgoCD
    ├── Jenkins
    ├── Prometheus
    ├── Grafana
    ├── Loki
    ├── Alertmanager
    └── Demo Applications

------------------------------------------------------------------------

## 7. Architectural Decisions

-   Existing services remain operational.
-   Project Atlas will share the current VPS.
-   GitLab CE will initially run as a host container.
-   Kubernetes workloads will be deployed using k3s.
-   n8n will remain outside Kubernetes to preserve operational
    independence.

------------------------------------------------------------------------

## 8. Risks

  Risk                  Mitigation
  --------------------- ------------------------------------
  Resource contention   Progressive deployment
  GitLab memory usage   Continuous monitoring
  Single VPS            Frequent backups and documentation
  Public exposure       Reverse proxy and TLS

------------------------------------------------------------------------

## 9. Next Actions

1.  Prepare the Atlas host baseline.
2.  Define DNS and naming conventions.
3.  Deploy GitLab CE.
4.  Install k3s.
5.  Deploy ArgoCD.
6.  Deploy Observability.
7.  Deploy Jenkins.
8.  Integrate n8n.

------------------------------------------------------------------------

## 10. Revision History

  Version   Date         Description
  --------- ------------ -----------------
  0.1.0     2026-06-29   Initial version
