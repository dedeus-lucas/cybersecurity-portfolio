# Host Hardware & Environment Baseline

## 1. Physical Host Specifications
- **Operating System:** Microsoft Windows 11 Home Single Language (Build 10.0.26200)
- **CPU:** AMD Ryzen 5 3450U with Radeon Vega Mobile Gfx (4 Cores / 8 Logical Processors)
- **Hardware Virtualization:** Enabled (AMD-V / SVM in BIOS/UEFI)
- **Total Physical Memory:** 13.89 GB
- **Storage Allocation:** 708 GB Free (NVMe/SSD on C:)

## 2. Cyber Range Resource Quotas
- **Target Hypervisor:** Type-2 Virtualization (Oracle VM VirtualBox)
- **Maximum Allocated vRAM for Labs:** 6.0 GB (preserving 7.89 GB for Host OS & WSL2)
- **Maximum Allocated vCPUs:** 4 vCPUs oversubscribed across lab instances
- **Primary Linux Architecture:** Debian/Ubuntu Server LTS (CLI Headless mode)
