# LAB-00: Cyber Range Foundation & Secure Administrative Access

[![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)]()
[![Platform](https://img.shields.io/badge/Platform-Debian%2012%20Minimal-blue?style=flat-square)]()
[![Hypervisor](https://img.shields.io/badge/Hypervisor-VirtualBox%207%20%7C%20WSL2-orange?style=flat-square)]()

---

# LAB-00: Cyber Range Foundation & Secure Administrative Access

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-cyber-range-foundation--secure-administrative-access-1)
* [Portuguese Version - PT-BR](#lab-00-fundação-do-cyber-range-e-acesso-administrativo-seguro)

## 1. Executive Overview
LAB-00 establishes the isolated virtualization foundation and secure administrative management baseline for the Cyber Range portfolio. Designed to support subsequent experimentation, protocol inspection, and defense-in-depth engineering, this environment eliminates desktop display overhead, enforces Dual-NIC network segregation, and implements Ed25519 public-key authentication from a dedicated WSL2 administrative terminal.

---

## 2. Technical Architecture & Topology

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
|   | Role: Management / SSH   |                 | Role: Egress Updates Only  |   |
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

## 3. Implemented Engineering Controls

| Domain | Control Implemented | Verification & Evidence |
| :--- | :--- | :--- |
| Attack Surface | Headless transformation; GNOME display stack purged; systemd default target set to multi-user.target. | Idle RAM below 180 MB; listening socket audit (ss -tulpn) confirms that TCP/22 is the only network service listening at validation time. |
| Network Segmentation | Dual-NIC separation: NAT for external transit updates; Host-Only for isolated management (192.168.56.0/24). | Single default route via enp0s3; direct Layer 2 reachability and ARP resolution on enp0s8. |
| Access Security | Asymmetric Ed25519 EdDSA key authentication; POSIX 600/644 file permission enforcement. | Matching public-key fingerprints confirm correspondence between client and server; IdentitiesOnly active in SSH config. |
| Operational Resilience | Registration of a known-good recovery baseline using the GOLDEN_IMAGE_BASE_CLI snapshot. | Snapshot tree verification via VBoxManage CLI; validated rollback capability. |

---

## 4. Technical Documentation Pipeline
The complete engineering lifecycle is documented across modular artifacts:

* Master Technical Report: docs/LAB-00-RELATORIO-TECNICO.md (Comprehensive executive summary, technical specifications, and end-to-end laboratory identification).
* Network Configuration Baseline: docs/configuracao-rede.md (Layer 2 / Layer 3 interface specifications, routing tables, and WSL2 transit policies).
* Infrastructure Architecture: docs/arquitetura.md (Trust boundaries, hypervisor switch isolation models, and component matrix).
* Hardening Baseline: docs/hardening.md (Package elimination, SSH authentication hardening, filesystem permissions, and listening socket posture).
* Troubleshooting & RCA: docs/troubleshooting.md (Incident analysis: graphical environment display instability, networking.service syntax failure, WSL2 route isolation).
* Engineering Lessons Learned: docs/lessons-learned.md (Retrospective insights on headless administration, routing hygiene, and virtualization boundaries).
* Evidence Verification Matrix: reports/LAB-00-EVIDENCE-MATRIX.md (Direct correlation between raw telemetry logs, visual screenshots, and verified competencies).

---

## 5. Evidence & Telemetry Repositories

```text
00-cyber-range/
|-- commands/
|   |-- firewall-validation.txt
|   |-- network-baseline.txt
|   |-- snapshot-validation.txt
|   `-- ssh-validation.txt
|-- docs/
|   |-- LAB-00-RELATORIO-TECNICO.md
|   |-- arquitetura.md
|   |-- configuracao-rede.md
|   |-- hardening.md
|   |-- lessons-learned.md
|   `-- troubleshooting.md
|-- evidence/
|   |-- 01-ssh-client-config.png
|   |-- 02-ssh-connectivity-validation.png
|   `-- 03-golden-snapshot.png
`-- reports/
    `-- LAB-00-EVIDENCE-MATRIX.md
```

---

## 6. Next Engineering Phase
With the computing foundation and recovery snapshot finalized, the portfolio advances to LAB-01: Network Traffic Analysis & Protocol Dissection, focusing on packet capture with tcpdump, Wireshark analysis, ARP behavior, and TCP three-way handshake dissection.

---

# LAB-00: Fundação do Cyber Range e Acesso Administrativo Seguro

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-cyber-range-foundation--secure-administrative-access-1)
* [Versão em Português - PT-BR](#lab-00-fundação-do-cyber-range-e-acesso-administrativo-seguro)

## 1. Visão Executiva
O LAB-00 estabelece a infraestrutura de virtualização isolada e a linha de base de gerenciamento administrativo seguro para o portfólio de Cyber Range. Projetado para suportar experimentações controladas, inspeção profunda de tráfego e engenharia de defesa em profundidade, o ambiente elimina interfaces visuais, assegura a segregação por dois adaptadores de rede (Dual-NIC) e implementa autenticação por chave pública Ed25519 a partir de um terminal administrativo dedicado no WSL2.

---

## 2. Arquitetura Técnica e Topologia

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
|   | Papel: Gerência / SSH    |                 | Papel: Saída p/ Internet   |   |
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

## 3. Controles de Engenharia Implementados

| Domínio | Controle Implementado | Verificação e Evidência |
| :--- | :--- | :--- |
| Superfície de Ataque | Conversão headless; pilha gráfica do GNOME purgada; alvo padrão do systemd definido para multi-user.target. | Memória RAM ociosa inferior a 180 MB; auditoria de sockets (ss -tulpn) confirma que TCP/22 é o único serviço em escuta na validação. |
| Segmentação de Rede | Segregação Dual-NIC: NAT para saídas de atualização; Host-Only para gerência isolada (192.168.56.0/24). | Rota padrão única via enp0s3; alcançabilidade direta em Camada 2 e resolução ARP na interface enp0s8. |
| Segurança de Acesso | Autenticação assimétrica Ed25519 EdDSA; permissões de arquivo estritas em padrão POSIX 600/644. | Fingerprints correspondentes validam conformidade cliente-servidor; diretiva IdentitiesOnly ativa no SSH. |
| Resiliência Operacional | Registro de linha de base funcional de recuperação através do snapshot GOLDEN_IMAGE_BASE_CLI. | Verificação da árvore de snapshots via interface de linha de comando do VBoxManage; capacidade de rollback validada. |

---

## 4. Esteira de Documentação Técnica
O ciclo completo de engenharia está documentado em artefatos modulares:

* Relatório Técnico Mestre: docs/LAB-00-RELATORIO-TECNICO.md (Sumário executivo detalhado, especificações técnicas e identificação completa do laboratório).
* Linha de Base de Rede: docs/configuracao-rede.md (Especificações de interfaces Camada 2 e 3, tabelas de roteamento e políticas de trânsito WSL2).
* Arquitetura de Infraestrutura: docs/arquitetura.md (Fronteiras de confiança, modelos de isolamento por switches virtuais e matriz de componentes).
* Linha de Base de Hardening: docs/hardening.md (Remoção de pacotes, mitigação de vulnerabilidades no SSH, permissões POSIX e auditoria de sockets).
* Troubleshooting e RCA: docs/troubleshooting.md (Análise de incidentes: instabilidade no display gráfico, erro de sintaxe no networking.service e isolamento WSL2).
* Lições Aprendidas de Engenharia: docs/lessons-learned.md (Retrospectiva técnica sobre administração headless, higiene de rotas e limites de virtualização).
* Matriz de Verificação de Evidências: reports/LAB-00-EVIDENCE-MATRIX.md (Correlação direta entre logs brutos, capturas visuais e competências validadas).

---

## 5. Repositórios de Evidências e Telemetria

```text
00-cyber-range/
|-- commands/
|   |-- firewall-validation.txt
|   |-- network-baseline.txt
|   |-- snapshot-validation.txt
|   `-- ssh-validation.txt
|-- docs/
|   |-- LAB-00-RELATORIO-TECNICO.md
|   |-- arquitetura.md
|   |-- configuracao-rede.md
|   |-- hardening.md
|   |-- lessons-learned.md
|   `-- troubleshooting.md
|-- evidence/
|   |-- 01-ssh-client-config.png
|   |-- 02-ssh-connectivity-validation.png
|   `-- 03-golden-snapshot.png
`-- reports/
    `-- LAB-00-EVIDENCE-MATRIX.md
```

---

## 6. Próxima Fase de Engenharia
Com a fundação computacional concluída e o snapshot de recuperação estabelecido, o portfólio avança para o LAB-01: Análise de Tráfego de Rede e Dissecação de Protocolos, abordando captura de pacotes com tcpdump, análise em Wireshark, comportamento de ARP e dissecação do handshake TCP de 3 vias.
