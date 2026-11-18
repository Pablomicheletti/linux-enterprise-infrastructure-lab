# Network Design

# 02 — Network Design

## 1. Project Overview

This document describes the proposed network architecture for the Linux Enterprise Infrastructure Lab.

The environment will run on a Proxmox VE virtualization host and will demonstrate a highly available load-balancing architecture using HAProxy, Keepalived, VRRP, and two NGINX backend servers.

The design focuses on:

- Network segmentation and IP address planning.
- Redundant load balancing.
- Virtual IP failover.
- Backend service distribution.
- Network security and access control.
- Infrastructure monitoring and troubleshooting.

All network addresses and configurations described in this document are preliminary and must be validated before deployment.

## 2. Infrastructure Architecture

The proposed environment consists of four virtual machines:

| VMID | Hostname | Role | RAM |
|---|---|---|---:|
| 101 | lb-haproxy-01 | Primary HAProxy + Keepalived | 1 GB |
| 102 | lb-haproxy-02 | Secondary HAProxy + Keepalived | 1 GB |
| 103 | web-01 | NGINX backend server 1 | 1 GB |
| 104 | web-02 | NGINX backend server 2 | 1 GB |

Total proposed memory allocation: 4 GB.

All virtual machines will initially use Debian 13 minimal (AMD64).

## 3. Network Addressing Plan

The following private subnet is proposed for the laboratory:

**Network:** `10.10.10.0/24`

| Component | IP Address | Description |
|---|---|---|
| HAProxy 01 | 10.10.10.11 | Primary load balancer |
| HAProxy 02 | 10.10.10.12 | Secondary load balancer |
| Virtual IP | 10.10.10.10 | Shared service entry point |
| NGINX 01 | 10.10.10.21 | Backend web server |
| NGINX 02 | 10.10.10.22 | Backend web server |

The Virtual IP (VIP) will be managed by Keepalived and will move between the two load balancer nodes during a failover event.

The proposed addressing scheme must be checked against existing institutional networks before implementation.

## 4. High Availability Design

### 4.1 HAProxy Redundancy

Two independent HAProxy instances will be deployed:

- `lb-haproxy-01`: primary node.
- `lb-haproxy-02`: secondary node.

Both nodes will maintain equivalent HAProxy configurations and will distribute incoming HTTP traffic to the NGINX backend servers.

### 4.2 Keepalived and VRRP

Keepalived will manage the Virtual IP using the Virtual Router Redundancy Protocol (VRRP).

The initial configuration will use:

| Parameter | Primary | Secondary |
|---|---:|---:|
| VRRP priority | 150 | 100 |
| Expected state | MASTER | BACKUP |
| Virtual IP | 10.10.10.10 | 10.10.10.10 |

Keepalived will monitor the local HAProxy service.

If the primary node becomes unavailable, or its HAProxy service fails and the health-check mechanism detects the failure, the secondary node should take ownership of the Virtual IP.

VRRP communication and Virtual IP movement require a compatible Layer 2 network. The selected Proxmox bridge and network policies must support the required traffic.

Multicast or unicast VRRP configuration will be selected after validating the network environment.

### 4.3 Failover Behavior

Under normal conditions:

1. Clients connect to `10.10.10.10`.
2. The Virtual IP is assigned to `lb-haproxy-01`.
3. HAProxy distributes requests between `web-01` and `web-02`.
4. Keepalived monitors the load balancer's health.

During a primary-node failure:

1. Keepalived detects the failure.
2. The secondary node assumes the Virtual IP.
3. Clients can continue reaching the service through the same IP address.
4. The secondary HAProxy instance distributes traffic to the available backend servers.

Existing TCP connections may be interrupted during failover. This architecture does not guarantee transparent session preservation.

## 5. Backend Web Servers

Two NGINX instances will provide the backend web service.

| Hostname | IP Address | Service |
|---|---|---|
| web-01 | 10.10.10.21 | NGINX |
| web-02 | 10.10.10.22 | NGINX |

Both servers will initially host a simple test website.

The content will identify the responding backend server, allowing load-balancing behavior to be verified.

HAProxy will use both backend servers and perform health checks to detect unavailable instances.

## 6. Network Traffic and Ports

The following ports are expected to be used:

| Port | Protocol | Purpose |
|---|---|---|
| 22 | TCP | SSH administration |
| 80 | TCP | HTTP traffic |
| 443 | TCP | HTTPS traffic (future implementation) |
| 53 | TCP/UDP | DNS, if required |
| 112 | IP protocol | VRRP communication |

VRRP uses IP protocol number 112 rather than TCP or UDP port 112.

Access to administrative services should be restricted to authorized management networks.

## 7. Network Connectivity Model

The laboratory network design will be finalized after reviewing the existing Proxmox network configuration and institutional network policies.

Two possible approaches will be evaluated:

### Option A — Isolated Virtual Network

A dedicated internal Proxmox bridge may be used to isolate laboratory traffic.

Advantages:

- Reduced exposure to the institutional network.
- Controlled communication between laboratory VMs.
- Suitable for internal service testing.

Limitations:

- External connectivity may require routing or NAT.
- VRRP and Virtual IP behavior must be tested.
- An isolated bridge does not automatically guarantee that all required Layer 2 behavior is available.

### Option B — Bridged Network

Laboratory VMs may connect through a Proxmox bridge associated with a physical network interface.

Advantages:

- Potential access to external network services.
- Easier access to repositories and package updates.

Limitations:

- Requires institutional network authorization.
- IP addressing and security policies must be respected.
- VRRP traffic and Virtual IP announcements may be restricted.

No changes to the Proxmox host network configuration will be made without prior validation and authorization.

## 8. Security Considerations

The following security controls are planned:

- SSH administration using authorized credentials.
- Restricted administrative access.
- Firewall rules allowing only required traffic.
- No unnecessary services exposed to external networks.
- Regular system and package updates.
- Monitoring of service availability.
- Secure handling of configuration files and credentials.

## 9. Monitoring and Testing

The environment will be tested through practical scenarios:

| Test | Expected Result |
|---|---|
| Access the VIP | Application responds |
| Stop one NGINX backend | HAProxy uses the remaining backend |
| Restore the backend | Both backends become available |
| Stop primary HAProxy | Secondary assumes the VIP |
| Restore primary HAProxy | Expected VRRP behavior is verified |
| Stop secondary HAProxy | Primary continues serving requests |

Additional tests will validate:

- VRRP communication.
- Keepalived health checks.
- HAProxy backend health status.
- Network connectivity between virtual machines.
- Failover recovery time.
- Service logs and troubleshooting evidence.

## 10. Infrastructure Limitations

The initial implementation will run all four virtual machines on a single physical Proxmox host.

This provides redundancy at the virtual machine and service level, but it does not provide full physical high availability.

A failure affecting the Proxmox host, its physical network interface, power supply, or storage may interrupt the entire laboratory environment.

A future multi-host implementation could improve physical redundancy.

## 11. Implementation Strategy

The project will be deployed incrementally:

1. Validate the Proxmox host and available resources.
2. Confirm the network design and IP addressing.
3. Deploy the two NGINX backend servers.
4. Deploy and configure the primary HAProxy instance.
5. Validate load balancing and backend health checks.
6. Deploy the secondary HAProxy instance.
7. Configure Keepalived and VRRP.
8. Test Virtual IP failover.
9. Document results, failures, and troubleshooting procedures.

This staged approach allows each infrastructure component to be validated independently before introducing additional complexity.

## 12. Current Status

**Status: Design completed — deployment pending.**

No virtual machines, network interfaces, Virtual IP addresses, or services described in this document have been configured yet.

All proposed settings remain subject to infrastructure and network validation.