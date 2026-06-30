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

This document records the initial assessment of the VPS that will host Project Atlas.

The goal is to understand the available resources, current services, risks and architectural decisions before deploying new platform components.

---

## 2. VPS Role

The current VPS will be used as the primary infrastructure for Project Atlas.

It will host both existing services and new Atlas platform services.

This VPS is considered a learning and engineering laboratory.

---

## 3. Current Disk Status

Command used:

df -h

Observed status:

Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       193G  8.3G  185G   5% /

Summary:

Resource	Value
Total disk	193 GB
Used disk	8.3 GB
Available disk	185 GB
Usage	5%

Disk capacity is currently healthy for the initial Atlas deployment.

## 4. Current Memory Status

Command used:

free -h

Observed status after swap creation:

Mem:            11Gi       913Mi        10Gi       1.8Mi       508Mi        10Gi
Swap:          4.0Gi          0B       4.0Gi

Summary:

Resource	Value
Total RAM	11 GiB
Used RAM	~913 MiB
Available RAM	~10 GiB
Swap	4 GiB
Swap used	0 B

The VPS has enough available memory to begin the Atlas platform deployment progressively.

## 5. Swap Configuration

A 4 GB swap file was created as a safety buffer for memory spikes.

Validation command:

swapon --show

Observed status:

NAME      TYPE SIZE USED PRIO
/swapfile file   4G   0B   -2

The swap file is active and available.

## 6. Existing Services

The VPS already hosts the following services:

Service	                Current Placement	        Notes
Nginx Proxy Manager	    Host container	            Existing edge/reverse proxy
n8n	                    Host container	            Existing automation platform
Podman/Docker-compat.   Host	                    Used to run existing containers

These services must be considered when adding Atlas workloads.

## 7. Initial Architecture Decision

Project Atlas will use the current VPS as its primary lab infrastructure.

The platform will follow a hybrid approach:

Layer	                        Placement
Existing Nginx Proxy Manager	Host container
Existing n8n	                Host container
GitLab CE	                    Host container
k3s	                            Host
ArgoCD	                        Kubernetes
Jenkins	                        Kubernetes
Prometheus	                    Kubernetes
Grafana	                        Kubernetes
Loki	                        Kubernetes
Alertmanager	                Kubernetes
Demo applications	            Kubernetes

## 8. Resource Risk Assessment

Risk	                                Impact	        Current Mitigation
GitLab CE memory usage	                High	        Deploy progressively and monitor
Multiple platform services on one VPS	Medium	        Use phased deployment
No previous swap	                    Medium	        4 GB swap file created
Existing n8n disruption	                Medium	        Keep n8n outside Kubernetes
Disk growth from logs and images	    Medium	        Add monitoring and cleanup policies later

## 9. Deployment Recommendation

The recommended deployment order is:

Keep existing Nginx Proxy Manager and n8n running.
Prepare the VPS host baseline.
Define /opt/atlas directory structure.
Deploy GitLab CE as a host-level container.
Install k3s.
Deploy ArgoCD.
Deploy observability stack.
Deploy Jenkins.
Integrate n8n with alerts and operational workflows.


## 10. Current Status
Item	                        Status
Disk capacity validated	        Completed
Memory capacity validated	    Completed
Swap created	                Completed
Existing services identified	Pending detailed inventory
Port usage inventory	        Pending
Container inventory	            Pending
DNS/subdomain plan	            Pending
Backup strategy	                Pending

## 11. Conclusion

The VPS has enough disk and memory resources to start Project Atlas.

The main architectural constraint is that all services will initially run on a single VPS. This is acceptable for a lab environment, but resource usage must be monitored carefully as GitLab, Kubernetes, Jenkins and observability components are added.

The next recommended step is to perform a detailed inventory of current containers, exposed ports and service dependencies before deploying new components.

## 12. Revision History

Version	        Date	        Description
0.1.0	        2026-06-29	    Initial VPS assessment
