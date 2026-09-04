# LAB-00: Network Architecture and Interface Configuration Baseline

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-network-architecture-and-interface-configuration-baseline)
* [Portuguese Version - PT-BR](#lab-00-arquitetura-de-rede-e-configuração-de-interfaces)

## 1. Scope and Design Principles
This document formalizes the Layer 2 (Data Link) and Layer 3 (Network) architectural configuration implemented for the Cyber Range base system (srv-linux-base). The core design enforces a dual-homed network model (Dual-NIC) separating operational outbound traffic from management and internal experimentation flows:
- Interface Isolation: Outbound operational requirements do not traverse or bridge directly into the internal private subnet.
- Deterministic Addressing: The administrative interface uses static IP allocation to prevent dependency on dynamic lease servers (DHCP) on the management segment.
- Single Default Route: Only the external transit interface provides the default route, avoiding multiple competing default gateways and reducing routing ambiguity within the guest operating system.

---

## 2. Layer 2 / Layer 3 Interface Specification Matrix

### Interface enp0s3 (Hypervisor NAT)
- Functional Purpose: Controlled outbound Internet transit for OS security patching, package indexing (apt-get), and upstream time synchronization (NTP).
- Virtual Switch Mode: VirtualBox NAT Engine (Emulated NAT router).
- Guest PCI Address: enp0s3 (PCI address 00:03.0).
- IPv4 Configuration: Dynamic (DHCP via VirtualBox built-in engine).
- IPv4 Assigned Address: 10.0.2.15/24.
- Network Broadcast: 10.0.2.255.
- Default Gateway: 10.0.2.2.
- Hardware Address (MAC): Dynamic VirtualBox OUI allocation (08:00:27:xx:xx:xx).
- Maximum Transmission Unit (MTU): 1500 bytes.
- Security Constraint: Inbound connections from the physical LAN are not directly exposed through the VirtualBox NAT interface unless explicit port forwarding is configured.

### Interface enp0s8 (Hypervisor Host-Only Private Segment)
- Functional Purpose: Out-of-band private management interface, terminal access (SSH), telemetry, and secure intra-range interconnectivity.
- Virtual Switch Mode: VirtualBox Host-Only Ethernet Adapter (Virtual Private Switch).
- Guest PCI Address: enp0s8 (PCI address 00:08.0).
- IPv4 Configuration: Static (Manual ifupdown declaration).
- IPv4 Assigned Address: 192.168.56.10/24.
- Subnet Mask: 255.255.255.0 (/24 CIDR prefix).
- Network ID: 192.168.56.0.
- Usable Host Range: 192.168.56.1 through 192.168.56.254.
- Network Broadcast: 192.168.56.255.
- Default Gateway: None (Deliberately unassigned).
- Hardware Address (MAC): Dynamic VirtualBox OUI allocation (08:00:27:xx:xx:xx).
- Maximum Transmission Unit (MTU): 1500 bytes.
- Security Constraint: The Host-Only segment is isolated from the physical LAN by the VirtualBox network topology and is intended for communication between the Windows host and attached virtual machines.

---

## 3. Persistent Operating System Declarations

The network configuration is managed deterministically through the ifupdown subsystem within /etc/network/interfaces:

# Loopback loop
auto lo
iface lo inet loopback

# Outbound Transit Interface (NAT)
auto enp0s3
iface enp0s3 inet dhcp

# Internal Management Segment (Host-Only)
auto enp0s8
iface enp0s8 inet static
    address 192.168.56.10
    netmask 255.255.255.0

---

## 4. Routing Table Architecture and Transit Logic

### Guest Linux Kernel Routing Table (srv-linux-base)
Inspected via ip route show:
- default via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100
- 10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
- 192.168.56.0/24 dev enp0s8 proto kernel scope link src 192.168.56.10

Transit Behavior:
All packets destined for external internet destinations (0.0.0.0/0) match the default route via 10.0.2.2 over enp0s3. Packets addressed to the directly connected subnet 192.168.56.0/24 are resolved through the enp0s8 interface at Layer 2 using ARP, without requiring an upstream gateway.

### WSL2 Transit Routing Policy
The WSL2 Linux environment operates within its own virtualized networking boundary:
- Transit Rule: 192.168.56.0/24 via <GW_WINDOWS_HOST> dev eth0
- Routing Path: WSL2 -> Windows host -> VirtualBox Host-Only network -> srv-linux-base.
- Effect: Allows the WSL OpenSSH client to communicate directly with srv-linux-base on port TCP/22.
- Persistence Note: The WSL2 route is an operational routing dependency and should be considered dynamic unless an explicit persistence mechanism is configured.

---

# LAB-00: Arquitetura de Rede e Configuração de Interfaces

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-network-architecture-and-interface-configuration-baseline)
* [Versão em Português - PT-BR](#lab-00-arquitetura-de-rede-e-configuração-de-interfaces)

## 1. Escopo e Princípios de Arquitetura
Este documento formaliza a arquitetura e a configuração de rede nas Camadas 2 (Enlace de Dados) e 3 (Rede) implementadas para o ativo base do Cyber Range (srv-linux-base). O projeto baseia-se em um modelo com dois adaptadores de rede (Dual-NIC) para segregar o tráfego de saída à internet dos fluxos privados de gerência e experimentação:
- Isolamento de Interfaces: O tráfego operacional de saída não cruza nem cria pontes diretas com a sub-rede privada interna.
- Endereçamento Determinístico: A interface administrativa utiliza alocação estática de IPv4 para mitigar a dependência de servidores DHCP no segmento de gerenciamento.
- Rota Padrão Única: Apenas a interface de trânsito externo fornece a rota padrão, evitando múltiplos gateways default concorrentes e reduzindo ambiguidades de roteamento dentro do sistema convidado.

---

## 2. Matriz de Especificação das Interfaces (Camadas 2 e 3)

### Interface enp0s3 (Modo NAT do Hypervisor)
- Finalidade Funcional: Trânsito de saída controlado para a internet para atualização de pacotes (apt-get), correções de segurança do sistema operacional e sincronização de horário (NTP).
- Modo do Switch Virtual: NAT nativo do Oracle VirtualBox.
- Endereço PCI no Guest: enp0s3 (endereço PCI 00:03.0).
- Configuração IPv4: Dinâmica (via DHCP do VirtualBox).
- Endereço IPv4 Atribuído: 10.0.2.15/24.
- Endereço de Broadcast: 10.0.2.255.
- Gateway Padrão: 10.0.2.2.
- Endereço Físico (MAC): OUI padrão VirtualBox (08:00:27:xx:xx:xx).
- Unidade Máxima de Transmissão (MTU): 1500 bytes.
- Restrição de Segurança: Conexões de entrada a partir da LAN física não são expostas diretamente através da interface NAT do VirtualBox, a menos que um redirecionamento de portas explícito esteja configurado.

### Interface enp0s8 (Modo Host-Only / Segmento Privado)
- Finalidade Funcional: Interface de gerência out-of-band, acesso administrativo remoto via terminal (SSH), coleta de telemetria e interconexão segura no Cyber Range.
- Modo do Switch Virtual: Host-Only Ethernet Adapter do VirtualBox (Switch Virtual Privado).
- Endereço PCI no Guest: enp0s8 (endereço PCI 00:08.0).
- Configuração IPv4: Estática (declaração manual através do ifupdown).
- Endereço IPv4 Atribuído: 192.168.56.10/24.
- Máscara de Sub-rede: 255.255.255.0 (prefixo CIDR /24).
- Identificador de Rede: 192.168.56.0.
- Faixa de Hosts Úteis: 192.168.56.1 até 192.168.56.254.
- Endereço de Broadcast: 192.168.56.255.
- Gateway Padrão: Nenhum (deliberadamente não configurado).
- Endereço Físico (MAC): OUI padrão VirtualBox (08:00:27:xx:xx:xx).
- Unidade Máxima de Transmissão (MTU): 1500 bytes.
- Restrição de Segurança: O segmento Host-Only é isolado da LAN física pela topologia de rede do VirtualBox e é destinado à comunicação entre o host Windows e as máquinas virtuais conectadas ao segmento.

---

## 3. Declaração Persistente no Sistema Operacional

A persistência das interfaces é assegurada pelo subsistema ifupdown no arquivo /etc/network/interfaces:

# Interface de Loopback
auto lo
iface lo inet loopback

# Interface de Trânsito Externo (NAT)
auto enp0s3
iface enp0s3 inet dhcp

# Segmento Privado de Gerência (Host-Only)
auto enp0s8
iface enp0s8 inet static
    address 192.168.56.10
    netmask 255.255.255.0

---

## 4. Arquitetura da Tabela de Roteamento e Lógica de Trânsito

### Tabela de Roteamento do Kernel Linux (srv-linux-base)
Inspecionada pelo comando ip route show:
- default via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100
- 10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
- 192.168.56.0/24 dev enp0s8 proto kernel scope link src 192.168.56.10

Comportamento de Trânsito:
Todo pacote destinado à internet pública (0.0.0.0/0) coincide com a rota default via 10.0.2.2 saindo pela interface enp0s3. Pacotes destinados à sub-rede diretamente conectada 192.168.56.0/24 são resolvidos através da interface enp0s8 na Camada 2 utilizando ARP, sem necessidade de consulta a gateways externos.

### Política de Roteamento de Trânsito no WSL2
O ambiente Linux do WSL2 opera dentro de seu próprio limite de rede virtualizado:
- Regra de Trânsito: 192.168.56.0/24 via <GW_HOST_WINDOWS> dev eth0
- Caminho de Roteamento: WSL2 -> host Windows -> rede Host-Only do VirtualBox -> srv-linux-base.
- Efeito Prático: Viabiliza o tráfego direto do cliente OpenSSH do WSL para a porta TCP/22 da máquina srv-linux-base.
- Nota de Persistência: A rota adicionada no WSL2 constitui uma dependência operacional de roteamento e deve ser considerada dinâmica enquanto nenhum mecanismo explícito de persistência estiver configurado.
