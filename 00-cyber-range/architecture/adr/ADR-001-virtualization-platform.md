# ADR-001: Selection of Base Virtualization Platform for Cyber Range

## Status
Accepted

## Context
The Cyber Range requires an isolated, reproducible, and cost-effective virtualization environment capable of running multi-tier operating systems (Linux, Windows Server, Active Directory, Firewalls) and private network segments on a single AMD-based host machine with 14 GB of RAM.

## Options Considered
1. **Hyper-V (Windows Native):** Native performance, but limited NAT/Internal network isolation features on Windows Home editions without complex PowerShell bridging.
2. **VMware Workstation:** Robust performance, but closed ecosystem with specific license constraints.
3. **Oracle VM VirtualBox:** Free and open-source, robust software-defined networking (Internal Networks, Host-Only, NAT Networks), scriptable via `VBoxManage`, and full support for snapshots and virtual disk exports across platforms.

## Decision
Adopt **Oracle VM VirtualBox** as the primary Type-2 Hypervisor for host VM orchestration, while leveraging **WSL2/Docker** for containerized workloads and CLI automation.

## Security & Operational Consequences
- **Positive:** Full isolation of lab subnets from the host LAN using isolated Internal Networks (`intnet`).
- **Positive:** Low overhead when deploying minimal headless Linux servers (512MB–1GB vRAM).
- **Negative:** VirtualBox requires hardware virtualization coexistence checks with WSL2/Hyper-V platform backend.
