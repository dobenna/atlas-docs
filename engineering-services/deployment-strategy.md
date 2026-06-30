<!--
===============================================================================
Project      : Project Atlas
Repository   : atlas-docs
File         : architecture/gitlab-deployment-strategy.md
Description  : GitLab CE deployment strategy for Project Atlas
Author       : Elba Guerra
Created      : 2026-06-29
Last Updated : 2026-06-29
Version      : 0.1.0
Status       : Draft
===============================================================================
-->

# GitLab CE Deployment Strategy

## 1. Purpose

This document defines the initial deployment strategy for GitLab CE in Project Atlas.

The objective is to deploy GitLab in an efficient, controlled and maintainable way on the current VPS without disrupting the existing services.

---

## 2. Context

Project Atlas will run on the current VPS named `arwen`.

The VPS already runs:

- Nginx Proxy Manager
- n8n
- Podman
- containerd
- SSH

Available resources are currently healthy:

| Resource | Status |
|----------|--------|
| RAM | 11 GiB |
| Swap | 4 GiB |
| Disk | 181 GB available |
| Current containers | npm, n8n |

GitLab will be the first major engineering service added to Atlas.

---

## 3. Selected Edition

Project Atlas will use:

```text
GitLab Community Edition
```

## 3.1 Why GitLab CE

GitLab CE is selected because:

- It is open source.
- It is enough for learning Git repositories, CI/CD, runners, users, groups and project workflows.
- It avoids unnecessary enterprise complexity.
- It fits better with the open-source-first principle of Project Atlas.

## 3.2 Why Not GitLab EE

GitLab EE provides enterprise features that are not required at this stage.

Examples:

- Advanced compliance features
- Enterprise portfolio management
- Advanced security dashboards
- Premium governance features

These features are useful in real organizations, but they are not necessary for the first Atlas implementation.

---

## 4. Deployment Location

GitLab CE will run as a host-level container using Podman.

It will not run inside Kubernetes during the first phase.

---

## 5. Why GitLab Outside Kubernetes

GitLab is a heavy service with several internal components.

Running GitLab inside Kubernetes is possible, but it increases complexity in areas such as:

- Persistent volumes
- Helm chart configuration
- Ingress
- Backups
- Resource limits
- Upgrades
- Troubleshooting

For Project Atlas, the efficient initial approach is:

```text
GitLab CE as a Podman container on the host
```

This keeps the deployment simpler and leaves Kubernetes available for:

- ArgoCD
- Jenkins
- Prometheus
- Grafana
- Loki
- Alertmanager
- Demo applications

---

## 6. Network Strategy

GitLab will not bind directly to public ports 80 or 443.

Those ports are already used by Nginx Proxy Manager.

GitLab will expose internal host ports only.

| GitLab Function | Host Port | Public Exposure |
|-----------------|-----------|-----------------|
| Web UI / HTTP | 8081 | Through Nginx Proxy Manager |
| SSH Git access | 2222 | Optional / controlled |
| HTTPS | Not exposed directly | Terminated by Nginx Proxy Manager |

Nginx Proxy Manager will publish GitLab externally using a subdomain.

Example:

```text
gitlab.example.com -> http://127.0.0.1:8081
```

---

## 7. SSH Strategy

The VPS already uses port 22 for system SSH access.

GitLab SSH access will use port 2222.

Example clone format:

```bash
git clone ssh://git@gitlab.example.com:2222/group/project.git
```

During the first stage, GitLab may be used mainly through HTTPS.

SSH access can be enabled and validated after the web interface is stable.

---

## 8. Storage Strategy

GitLab persistent data will be stored under `/opt/atlas`.

Planned directories:

```text
/opt/atlas/configs/gitlab
/opt/atlas/data/gitlab
/opt/atlas/logs/gitlab
/opt/atlas/backups/gitlab
```

Directory purpose:

| Directory | Purpose |
|-----------|---------|
| `/opt/atlas/configs/gitlab` | GitLab configuration |
| `/opt/atlas/data/gitlab` | GitLab application data |
| `/opt/atlas/logs/gitlab` | GitLab logs |
| `/opt/atlas/backups/gitlab` | GitLab backups |

This structure keeps Atlas services organized and easier to back up.

---

## 9. Container Strategy

GitLab will be deployed using the official GitLab CE container image.

Expected image:

```text
docker.io/gitlab/gitlab-ce:latest
```

For future stability, Atlas may later move from `latest` to a fixed version tag.

Initial deployment will prioritize learning and validation.

---

## 10. Resource Strategy

GitLab can consume significant memory.

Initial controls:

- Deploy GitLab alone before installing k3s.
- Validate memory usage after startup.
- Monitor disk growth.
- Avoid enabling optional services at the beginning.
- Delay GitLab Container Registry until a later phase.

---

## 11. Container Registry Decision

GitLab Container Registry will not be enabled in the initial deployment.

Reason:

The registry introduces additional configuration and storage requirements.

It also requires extra reverse proxy handling.

The first goal is to deploy GitLab CE successfully and validate:

- Web access
- Initial root login
- Project creation
- Repository push/pull
- Basic CI/CD readiness

The registry will be evaluated in a separate phase.

---

## 12. Backup Strategy

GitLab backups will be planned before GitLab becomes critical.

Backup targets:

- GitLab configuration
- GitLab repositories
- GitLab database
- GitLab uploads
- GitLab secrets

Backups will eventually be stored under:

```text
/opt/atlas/backups/gitlab
```

A backup automation strategy will be defined in a later Atlas phase.

---

## 13. Security Strategy

Initial security practices:

- Expose GitLab only through Nginx Proxy Manager.
- Use TLS at the reverse proxy.
- Avoid storing credentials in Git.
- Store sensitive files only under `/opt/atlas/secrets`.
- Limit public ports.
- Use strong root password during initial setup.
- Disable or restrict unnecessary features if needed.

---

## 14. Initial DNS Plan

Proposed subdomain:

```text
gitlab.<atlas-domain>
```

The final domain will be decided before public exposure through Nginx Proxy Manager.

---

## 15. Deployment Phases

### Phase 1 - Prepare Host Directories

Create required directories under `/opt/atlas`.

### Phase 2 - Deploy GitLab Container

Run GitLab CE using Podman with persistent volumes.

### Phase 3 - Validate Local Access

Validate GitLab through the internal host port.

### Phase 4 - Configure Nginx Proxy Manager

Publish GitLab through a subdomain.

### Phase 5 - Create Initial Project

Create a test project and validate repository operations.

### Phase 6 - Evaluate Resource Usage

Check RAM, disk and container logs.

### Phase 7 - Document Operational Procedures

Create runbooks for start, stop, logs, backup and troubleshooting.

---

## 16. Expected Outcome

At the end of this deployment, Project Atlas will have an internal GitLab CE instance available for:

- Git repository management
- CI/CD experimentation
- GitLab pipeline comparison
- Internal platform engineering workflows
- Future GitLab Runner integration

---

## 17. Risks and Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| GitLab consumes too much RAM | High | Deploy before k3s and monitor |
| Port conflict with NPM | Medium | Use internal port 8081 |
| SSH conflict with host SSH | Medium | Use port 2222 |
| Disk growth | Medium | Monitor and plan backups |
| Reverse proxy misconfiguration | Medium | Validate local access first |
| GitLab startup time | Low | Wait and monitor logs |

---

## 18. Decision Summary

Project Atlas will deploy GitLab CE as a Podman host-level container.

GitLab will run outside Kubernetes initially, use `/opt/atlas` for persistence, expose HTTP internally on port `8081`, optionally expose SSH on port `2222`, and be published externally through Nginx Proxy Manager.

This is the most efficient first-stage approach for the current single-VPS Atlas architecture.

---

## 19. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 0.1.0 | 2026-06-29 | Initial GitLab CE deployment strategy |
