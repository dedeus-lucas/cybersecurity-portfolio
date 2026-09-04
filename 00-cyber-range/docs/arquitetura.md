# LAB-00: Infrastructure Architecture and Trust Boundary Baseline

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-infrastructure-architecture-and-trust-boundary-baseline)
* [Portuguese Version - PT-BR](#lab-00-arquitetura-de-infraestrutura-e-fronteiras-de-confiança)

## 1. Architectural Scope and Design Rationale
The LAB-00 architecture provides a reproducible, isolated, and segmented virtualization baseline dedicated to controlled cybersecurity testing, protocol dissection, and systems hardening. The design decouples operational connectivity (package indexing, security patches) from internal laboratory traffic, establishing containment boundaries at the hypervisor, virtual networking, and guest operating system levels.

---

## 2. Logical Topology Specification

```text
+---------------------------------------------------------------------------------+
|                               WINDOWS 11 HOST                                   |
|                                                                                 |
|   +-------------------+                     +-------------------------------+   |
|   |   WSL2 (Ubuntu)   |                     | VirtualBox Host-Only Adapter  |   |
|   |   OpenSSH Client  |                     |     192.168.56.1/24 (vSwitch) |   |
|   +---------+---------+                     +---------------+---------------+   |
|             |                                               |                   |
|             | (Static Route in WSL2 via Windows Gateway)    |                   |
|             +-----------------------+-----------------------+                   |
|                                     |                                           |
+-------------------------------------|-------------------------------------------+
                                      |
                 [ NETWORK ISOLATION BOUNDARY: HOST-ONLY ]
                                      |
+-------------------------------------v-------------------------------------------+
| DEBIAN 12 MINIMAL GUEST (srv-linux-base)                                        |
|                                                                                 |
|   +--------------------------+                 +----------------------------+   |
|   | enp0s8 (Host-Only NIC)   |                 | enp0s3 (NAT NIC)           |   |
|   | IP: 192.168.56.10/24     |                 | IP: 10.0.2.15/24 (DHCP)    |   |
|   | Target: SSH / Management |                 | Target: Egress Transit Only|   |
|   +--------------------------+                 +-------------+--------------+   |
|                                                              |                  |
+--------------------------------------------------------------|------------------+
                                                               |
                                                  [ VirtualBox NAT Engine ]
                                                               |
                                                               v
                                                      [ Physical LAN / WAN ]
```

---

## 3. Component Specification Matrix

| Component | Technical Role | Hypervisor Switch | IP / CIDR | Security Policy |
| :--- | :--- | :--- | :--- | :--- |
| **Windows 11 Host** | Type-2 Hypervisor Platform | N/A | LAN IP / DHCP | Physical interface isolation |
| **WSL2 Runtime** | Administrative Client Environment | Virtual vSwitch | Dynamic Hyper-V NAT | Dedicated Ed25519 asymmetric access |
| **VirtualBox vSwitch** | Management Interconnect | Host-Only; not bridged to physical LAN | 192.168.56.1/24 | Isolated segment |
| **srv-linux-base (enp0s8)**| Lab Target (Debian 12) | Host-Only Network | 192.168.56.10/24 | No default route assigned |
| **srv-linux-base (enp0s3)**| Outbound Update Pipe | NAT Router Engine | 10.0.2.15/24 | Inbound closed without port forward |

---

## 4. Trust Boundaries and Isolation Models

### Perimeter 1: Physical LAN Isolation
The internal management network (192.168.56.0/24) uses the VirtualBox Host-Only virtual switch. By design, frames generated within this segment are not forwarded across the physical network interface card (NIC) of the host system. This provides network isolation for experimentation, packet captures, and potential attack simulations within the local workstation.

### Perimeter 2: Management Plane vs. Egress Transit
The virtual machine implements a strict dual-homed network model:
- Management Plane (enp0s8): Restricts administrative interaction to the local host. The absence of a default route prevents this interface from being selected for general outbound traffic.
- Egress Transit (enp0s3): Connected via hypervisor NAT to allow outbound connectivity for package management (apt-get) and time synchronization. Direct inbound access from external networks is not exposed through the NAT interface because no VirtualBox port-forwarding rules are configured for this adapter.

### Perimeter 3: Cryptographic Access Control
Administrative boundaries between WSL2 and srv-linux-base are enforced through asymmetric cryptographic controls:
- SSH authentication is performed using dedicated Ed25519 public-key authentication.
- The client configuration uses IdentitiesOnly yes to restrict authentication attempts to the explicitly configured identity.

---

# LAB-00: Arquitetura de Infraestrutura e Fronteiras de Confiança

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-infrastructure-architecture-and-trust-boundary-baseline)
* [Versão em Português - PT-BR](#lab-00-arquitetura-de-infraestrutura-e-fronteiras-de-confiança)

## 1. Escopo Arquitetural e Princípios de Projeto
A arquitetura do LAB-00 provê uma linha de base de virtualização reproduzível, isolada e segmentada, dedicada a testes controlados de cibersegurança, dissecação de protocolos e hardening de sistemas operacionais. O modelo separa o tráfego operacional (atualização de pacotes e correções de segurança) do tráfego interno de gerenciamento do laboratório, estabelecendo limites de contenção nos níveis do hypervisor, da rede virtual e do sistema operacional convidado.

---

## 2. Especificação da Topologia Lógica

```text
+---------------------------------------------------------------------------------+
|                               HOST WINDOWS 11                                   |
|                                                                                 |
|   +-------------------+                     +-------------------------------+   |
|   |   WSL2 (Ubuntu)   |                     | Adaptador Host-Only VirtualBox|   |
|   |   Cliente OpenSSH |                     |     192.168.56.1/24 (vSwitch) |   |
|   +---------+---------+                     +---------------+---------------+   |
|             |                                               |                   |
|             | (Rota Estática no WSL2 via Gateway Windows)   |                   |
|             +-----------------------+-----------------------+                   |
|                                     |                                           |
+-------------------------------------|-------------------------------------------+
                                      |
             [ LIMITE DE ISOLAMENTO DE REDE: HOST-ONLY ]
                                      |
+-------------------------------------v-------------------------------------------+
| MÁQUINA DEBIAN 12 MINIMAL (srv-linux-base)                                      |
|                                                                                 |
|   +--------------------------+                 +----------------------------+   |
|   | enp0s8 (NIC Host-Only)   |                 | enp0s3 (NIC NAT)           |   |
|   | IP: 192.168.56.10/24     |                 | IP: 10.0.2.15/24 (DHCP)    |   |
|   | Papel: SSH / Gerência    |                 | Papel: Trânsito de Saída   |   |
|   +--------------------------+                 +-------------+--------------+   |
|                                                              |                  |
+--------------------------------------------------------------|------------------+
                                                               |
                                                  [ Mecanismo NAT VirtualBox ]
                                                               |
                                                               v
                                                      [ LAN Física / Internet ]
```

---

## 3. Matriz de Especificação dos Componentes

| Componente | Função Técnica | Switch no Hypervisor | IP / CIDR | Política de Segurança |
| :--- | :--- | :--- | :--- | :--- |
| **Host Windows 11** | Plataforma de Hypervisor Tipo 2 | N/A | IP da LAN / DHCP | Isolamento da interface física |
| **Ambiente WSL2** | Ambiente de Cliente Administrativo | vSwitch Virtual | NAT Dinâmico Hyper-V | Acesso assimétrico Ed25519 dedicado |
| **vSwitch VirtualBox** | Interconexão de Gerenciamento | Host-Only; sem ponte com a LAN física | 192.168.56.1/24 | Segmento isolado |
| **srv-linux-base (enp0s8)**| Servidor Alvo (Debian 12) | Rede Host-Only | 192.168.56.10/24 | Sem rota padrão configurada |
| **srv-linux-base (enp0s3)**| Saída para Internet | Roteador NAT | 10.0.2.15/24 | Entrada fechada sem redirecionamento |

---

## 4. Fronteiras de Confiança e Modelos de Isolamento

### Perímetro 1: Isolamento da Rede Local Física (LAN)
A rede de gerenciamento interno (192.168.56.0/24) opera no switch virtual Host-Only do VirtualBox. Por projeto, os quadros gerados nesse segmento não são encaminhados para a placa de rede física do host. Essa topologia proporciona isolamento de rede para experimentações, capturas de pacotes e simulações de tráfego dentro da estação de trabalho local.

### Perímetro 2: Plano de Gerenciamento vs. Trânsito de Saída
A máquina virtual adota um modelo de dois adaptadores de rede (Dual-NIC):
- Plano de Gerenciamento (enp0s8): Restringe o acesso administrativo à comunicação com o host local. A ausência de uma rota padrão impede que essa interface seja selecionada para o tráfego geral de saída.
- Trânsito de Saída (enp0s3): Conectada via NAT do hypervisor para permitir requisições de saída para atualização de repositórios (apt-get) e sincronização de tempo. O acesso direto de entrada a partir de redes externas não fica exposto através da interface NAT porque nenhuma regra de redirecionamento de portas do VirtualBox foi configurada para esse adaptador.

### Perímetro 3: Controle Criptográfico de Acesso
O limite administrativo entre o WSL2 e o servidor srv-linux-base baseia-se em controles criptográficos assimétricos:
- A autenticação SSH é realizada exclusivamente através de chave pública dedicada Ed25519.
- A configuração do cliente utiliza a diretiva IdentitiesOnly yes para restringir as tentativas de autenticação estritamente à identidade configurada de forma explícita.
