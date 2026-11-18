# Infrastructure Audit

# 01 — Infrastructure Audit

## 1. Project Overview

This project aims to design, deploy, document and validate a Linux-based infrastructure lab using Proxmox VE.

The lab will be used to practice virtualization, Linux system administration, networking, load balancing, monitoring, backup and recovery, and infrastructure automation.

The initial assessment was conducted to understand the existing infrastructure, identify available resources, and establish a safe deployment scope.

---

## 2. Infrastructure Scope

**Target virtualization node:** `pve-3`

The laboratory will be deployed exclusively on the authorized Proxmox VE node `pve-3`, subject to resource availability and infrastructure policies.

A separate Proxmox node, `pve-5`, hosts an existing Nextcloud container associated with another student project. This workload is outside the scope of this laboratory.

The existing backup infrastructure is shared and will not be modified.

### Scope Restrictions

- No modifications to existing virtual machines or containers.
- No modifications to shared backup infrastructure.
- No deletion, restoration, or alteration of existing backups.
- No changes to the Proxmox host network configuration without explicit authorization.
- All laboratory workloads must be created within the authorized scope.

---

## 3. Virtualization Platform

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE |
| Version | 9.2.0 |
| Node | pve-3 |
| Kernel | 7.0.14-20-pve |
| Virtualization technology | KVM/QEMU |
| Container technology | LXC |

The environment provides a web-based management interface for virtual machines, containers, storage and network resources.

The laboratory will primarily use virtual machines to provide isolated Linux environments for infrastructure deployment and administration.

---

## 4. Hardware Inventory

### CPU

| Property | Value |
|---|---|
| Model | Intel Xeon E3-1230 V2 |
| Physical cores | 4 |
| Logical processors | 8 |
| Base frequency | 3.30 GHz |

### Memory

| Property | Value |
|---|---|
| Installed memory | 15 GiB |

### Physical Storage

| Property | Value |
|---|---|
| Reported physical disk capacity | Approximately 556.9 GB |
| Storage management | LVM and LVM-thin |

The available hardware is suitable for a small virtualization lab consisting of lightweight Linux virtual machines and infrastructure services.

Resource allocation must be planned carefully to avoid excessive memory consumption and storage contention.

---

## 5. Storage Assessment

The node currently exposes the following storage resources:

| Storage | Type | Purpose |
|---|---|---|
| `local` | Directory | Local file-based storage |
| `local-lvm` | LVM-thin | Virtual machine disk storage |
| `pbe` | Proxmox Backup Server | Backup storage |

### Local Storage

The `local` storage provides approximately 96 GiB of capacity.

At the time of assessment:

- Reported utilization: approximately 18.52%.
- Available capacity: approximately 71.7 GiB.

### LVM-Thin Storage

The `local-lvm` storage is configured as an LVM-thin pool for virtual machine disk allocation.

At the time of assessment:

- Reported utilization: 0%.
- Available capacity: approximately 427 GiB.

### Backup Storage

The `pbe` storage is configured as a Proxmox Backup Server datastore.

It contains existing backup data from infrastructure workloads outside the scope of this project.

The backup datastore is considered a shared resource and will not be modified.

---

## 6. Network Assessment

The Proxmox node uses a Linux network bridge named `vmbr0`.

| Property | Details |
|---|---|
| Network bridge | `vmbr0` |
| Bridge mode | Linux bridge |
| Physical bridge interface | `nic4` |
| Host addressing | Static IPv4 |
| Default gateway | Configured |
| Network scope | Existing institutional network |

The bridge connects the Proxmox host to the physical network.

Virtual machine connectivity will require further assessment before deployment.

The laboratory network architecture, IP addressing scheme, connectivity requirements, and potential isolation mechanisms will be documented separately in:

`docs/02-network-design.md`

No host networking changes are planned as part of the initial deployment.

---

## 7. Current Workload Assessment

The following checks were performed on the authorized node `pve-3`:

| Resource | Current findings |
|---|---|
| Virtual machines | None registered |
| LXC containers | None registered |
| Laboratory services | Not deployed |
| Laboratory virtual disks | Not created |
| Laboratory network configuration | Not implemented |

The node currently has no virtual machines or containers registered for this project.

This provides an opportunity to plan the laboratory deployment from a clean guest-workload state.

---

## 8. Backup Infrastructure Assessment

The Proxmox Backup Server storage `pbe` is configured and accessible from the Proxmox environment.

A read-only inspection of backup metadata identified an existing container backup:

| Property | Value |
|---|---|
| VMID | 111 |
| Hostname | CT-Nextcloud |
| Operating system | Debian |
| Architecture | amd64 |
| CPU allocation | 4 cores |
| Memory allocation | 4096 MiB |
| Root filesystem | 20 GiB |
| Container type | Unprivileged LXC |

This backup is part of the existing infrastructure and is not related to the laboratory.

No backup restoration, deletion, modification, or retention changes have been performed.

Backup and recovery procedures for laboratory workloads will be designed separately.

---

## 9. Infrastructure Constraints and Risks

The following constraints were identified during the assessment:

- The physical host has limited CPU and memory resources.
- Virtual machine resource allocation must be planned before deployment.
- The existing institutional network must not be disrupted.
- Shared backup infrastructure must remain untouched.
- Existing workloads on other Proxmox nodes are outside the project scope.
- Network isolation requirements must be confirmed before deploying laboratory services.
- Monitoring and backup services will increase resource consumption.

### Risk Mitigation

| Risk | Mitigation |
|---|---|
| Resource contention | Monitor host resource usage and allocate conservative VM resources |
| Network disruption | Avoid host network modifications and verify network policies |
| Impact on existing workloads | Restrict all changes to authorized laboratory resources |
| Backup data loss | Do not modify shared PBS backups or retention policies |
| Insufficient capacity | Reassess CPU, RAM and storage before deployment |

---

## 10. Preliminary Resource Assessment

The initial laboratory architecture is expected to include three lightweight Linux virtual machines:

| VM ID | Hostname | Role | Planned RAM |
|---|---|---|---:|
| 101 | lb-haproxy | Load balancer | 1 GiB |
| 102 | web-01 | Web server | 1 GiB |
| 103 | web-02 | Web server | 1 GiB |
| | | **Total** | **3 GiB** |

These allocations are preliminary and subject to validation.

The planned guest operating system is Debian Linux.

Additional services such as Zabbix, Grafana, Ansible and backup components will be evaluated in later stages.

---

## 11. Initial Findings

The infrastructure assessment identified the following:

- Proxmox VE 9.2 is available as the virtualization platform.
- The node provides 4 physical CPU cores, 8 logical processors and approximately 15 GiB of RAM.
- LVM-thin storage is available for virtual machine disks.
- A Linux bridge is already configured.
- No laboratory virtual machines or containers have been deployed.
- Shared backup infrastructure contains workloads unrelated to this project.
- A dedicated deployment plan is required to prevent interference with existing infrastructure.

---

## 12. Audit Conclusion

The Proxmox node `pve-3` appears to provide a suitable foundation for a small Linux infrastructure laboratory.

The available CPU, memory and LVM-thin storage resources are potentially sufficient for an initial deployment consisting of a load balancer and two web servers.

However, final resource allocation and network configuration must be validated before implementation.

The project will proceed through network design, deployment planning, Linux installation, service configuration, monitoring, backup testing and automation.

No laboratory workloads have been deployed at the time of this assessment.

---

## 13. Project Status

**Current phase:** Infrastructure assessment and planning

| Phase | Status |
|---|---|
| Initial infrastructure discovery | Completed |
| Hardware inventory | Completed |
| Storage assessment | Completed |
| Workload assessment | Completed |
| Scope definition | Completed |
| Network design | Pending |
| VM deployment | Pending |
| Linux configuration | Pending |
| Load balancing | Pending |
| Monitoring | Pending |
| Backup and recovery testing | Pending |
| Automation | Pending |

---

*This document reflects the infrastructure state observed during the initial assessment. Capacity, configuration and workload status may change over time and should be revalidated before deployment.*
