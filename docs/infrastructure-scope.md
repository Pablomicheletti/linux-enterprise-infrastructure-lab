
 Infrastructure Scope and Isolation

Project Environment

The Linux Enterprise Infrastructure Lab is planned
to run on the authorized Proxmox VE node `pve-3`.

The node is dedicated to educational testing and
infrastructure experimentation.

## External Infrastructure

A separate Proxmox node, `pve-5`, hosts an existing
Nextcloud LXC container used by another student project.

This workload is outside the scope of this laboratory.

## Backup Infrastructure

The Proxmox Backup Server storage (`pbe`) contains
existing backups from multiple containers.

A read-only inspection of backup metadata identified
a container named `CT-Nextcloud` (VMID 111).

The backup infrastructure and its existing data are
considered shared resources and are excluded from
the laboratory scope.

solation Policy

- No modifications to existing containers.
- No modifications to existing virtual machines.
- No deletion or restoration of existing backups.
- No changes to PBS configuration or retention policies.
- All laboratory resources must be explicitly allocated
  within the authorized `pve-3` node.

Current Project Status

Planning and infrastructure assessment.

No laboratory virtual machines or containers have
been deployed yet.
