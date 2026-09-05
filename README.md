# Cybersecurity Engineering Laboratory Portfolio

[![Portfolio Status](https://img.shields.io/badge/Portfolio-Active%20Development-blue?style=flat-square)]()
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Windows%20%7C%20WSL2-orange?style=flat-square)]()
[![Verification Standard](https://img.shields.io/badge/Evidence-Audited%20Telemetry-success?style=flat-square)]()

---

**Document Navigation:**

* [English Version](#cybersecurity-engineering-laboratory-portfolio)
* [Versao em Portugues - PT-BR](#portfolio-de-engenharia-e-laboratorios-de-ciberseguranca)

---

## 1. Portfolio Overview & Engineering Vision
This portfolio documents hands-on engineering implementations across systems administration, network security, defensive infrastructure, identity and access management, application security, DevSecOps, cloud security, detection engineering, incident response, digital forensics, and offensive security. Every laboratory adheres to an evidence-based framework where architectural decisions, configuration files, raw telemetry outputs, and visual operational captures are linked directly to verified technical competencies.

---

## 2. Laboratory Index & Progression Roadmap

| Laboratory ID | Project Module | Technical Scope | Primary Focus | Status |
| :--- | :--- | :--- | :--- | :--- |
| **LAB-00** | [00-cyber-range](00-cyber-range/) | Cyber Range Foundation | Virtualization, segmentation, secure SSH, baseline recovery | Completed |
| **LAB-01** | `01-network-traffic-analysis` | Network Traffic Analysis | tcpdump, Wireshark, ARP, TCP, DNS, protocol analysis | Next |
| **LAB-02** | `02-host-network-firewalls` | Network & Host Firewalls | nftables, UFW, stateful filtering, ingress/egress control | Planned |
| **LAB-03** | `03-soc-telemetry-monitoring` | Security Telemetry & Monitoring | journald, rsyslog, auditd, centralized logging | Planned |
| **LAB-04** | `04-vulnerability-management` | Vulnerability Management | Nmap, OpenVAS/Nessus, CVSS, remediation | Planned |
| **LAB-05** | `05-identity-access-governance` | Identity & Access Management | SSH, PAM, sudo, RBAC, PKI | Planned |
| **LAB-06** | `06-linux-security` | Linux Security Engineering | Linux hardening, services, permissions, capabilities | Planned |
| **LAB-07** | `07-windows-security` | Windows Security | Windows internals, PowerShell, security controls | Planned |
| **LAB-08** | `08-active-directory` | Active Directory Security | AD, Kerberos, LDAP, Group Policy, identity attacks | Planned |
| **LAB-09** | `09-web-security` | Web Application Security | HTTP, authentication, sessions, OWASP vulnerabilities | Planned |
| **LAB-10** | `10-api-security` | API Security | REST, JWT, authorization, API attack surface | Planned |
| **LAB-11** | `11-devsecops` | DevSecOps Security | SAST, DAST, SCA, CI/CD security gates | Planned |
| **LAB-12** | `12-container-security` | Container Security | Docker, images, registries, secrets, runtime security | Planned |
| **LAB-13** | `13-kubernetes-security` | Kubernetes Security | RBAC, network policies, secrets, workloads | Planned |
| **LAB-14** | `14-cloud-security` | Cloud Security | IAM, network security, storage, logging, cloud posture | Planned |
| **LAB-15** | `15-detection-engineering` | Detection Engineering | Detection rules, Sigma, ATT&CK mapping, alert logic | Planned |
| **LAB-16** | `16-soc` | SOC Operations | Alert triage, investigation, escalation, threat monitoring | Planned |
| **LAB-17** | `17-incident-response` | Incident Response | Containment, eradication, recovery, IR procedures | Planned |
| **LAB-18** | `18-dfir` | Digital Forensics & Incident Response | Disk, memory, logs, timeline and forensic analysis | Planned |
| **LAB-19** | `19-reconnaissance` | Security Reconnaissance | OSINT, DNS, enumeration, attack-surface discovery | Planned |
| **LAB-20** | `20-red-team` | Red Team Operations | Recon, exploitation, privilege escalation, lateral movement | Planned |
| **LAB-21** | `21-ad-red-team` | Active Directory Red Team | Kerberos attacks, credential abuse, AD privilege escalation | Planned |
| **LAB-22** | `22-adversary-emulation` | Adversary Emulation | ATT&CK-based attack simulation and operational procedures | Planned |
| **LAB-23** | `23-purple-team` | Purple Team Operations | Attack simulation, detection validation, collaborative defense | Planned |
| **LAB-24** | `24-detection-gap` | Detection Gap Analysis | Identify, reproduce and close detection gaps | Planned |
| **LAB-25** | `25-security-automation` | Security Automation | Python, APIs, SOAR-style workflows, automated response | Planned |
| **LAB-26** | `26-security-engineering` | Security Engineering | Secure architecture, controls, integrations, engineering decisions | Planned |
| **LAB-99** | `99-capstone` | Cybersecurity Engineering Capstone | End-to-end offensive, defensive and engineering operation | Planned |

---

## 3. Competency Progression Model

```text
Foundation
    ↓
Network & Systems
    ↓
Identity & Access
    ↓
Web & Application Security
    ↓
DevSecOps & Cloud
    ↓
Detection & SOC
    ↓
DFIR & Incident Response
    ↓
Reconnaissance & Red Team
    ↓
Purple Team & Detection Engineering
    ↓
Security Automation
    ↓
Security Engineering
    ↓
Capstone
```

---

## 4. Evidence-Based Quality Framework
All laboratories within this repository follow a five-tier documentation standard:
- Master Technical Report (docs/): Full bilingual report covering implementation steps, specifications, and observed results.
- Root Cause Analysis (docs/troubleshooting.md): SRE-style documentation detailing symptom identification, hypothesis formulation, remediation, and re-testing.
- Architectural & Hardening Baselines (docs/): Isolation models, network topology diagrams, and attack surface reduction records.
- Raw Telemetry Logs (commands/): Plain-text terminal transcripts capturing observed system state, routing tables, socket states, and service status.
- Audit Verification Matrix (reports/): Systematic traceability tables linking captured visual evidence (evidence/) to verified engineering skills.

---

## 5. Repository Structure

```text
cybersecurity-portfolio/
|-- 00-cyber-range/
|   |-- commands/
|   |-- docs/
|   |-- evidence/
|   |-- reports/
|   `-- README.md
|-- 01-network-traffic-analysis/
|-- 02-host-network-firewalls/
|-- 03-soc-telemetry-monitoring/
|-- 04-vulnerability-management/
|-- 05-identity-access-governance/
|-- 06-linux-security/
|-- 07-windows-security/
|-- 08-active-directory/
|-- 09-web-security/
|-- 10-api-security/
|-- 11-devsecops/
|-- 12-container-security/
|-- 13-kubernetes-security/
|-- 14-cloud-security/
|-- 15-detection-engineering/
|-- 16-soc/
|-- 17-incident-response/
|-- 18-dfir/
|-- 19-reconnaissance/
|-- 20-red-team/
|-- 21-ad-red-team/
|-- 22-adversary-emulation/
|-- 23-purple-team/
|-- 24-detection-gap/
|-- 25-security-automation/
|-- 26-security-engineering/
|-- 99-capstone/
`-- README.md
```

---

# Portfólio de Engenharia e Laboratórios de Cibersegurança

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#cybersecurity-engineering-laboratory-portfolio)
* [Versão em Português - PT-BR](#portfólio-de-engenharia-e-laboratórios-de-cibersegurança)

## 1. Visão Geral do Portfólio e Filosofia de Engenharia
Este portfólio documenta implementações práticas de engenharia em administração de sistemas, segurança de redes, infraestrutura defensiva, gestão de identidades e acessos, segurança de aplicações, DevSecOps, segurança em nuvem, engenharia de detecção, resposta a incidentes, computação forense e segurança ofensiva. Todos os laboratórios seguem uma metodologia baseada em evidências em que decisões de arquitetura, arquivos de configuração, registros de telemetria bruta e capturas visuais de operação estão diretamente associados a competências técnicas comprovadas.

---

## 2. Índice dos Laboratórios e Roteiro de Progressão

| ID do Laboratório | Módulo do Projeto | Escopo Técnico | Foco Primário | Status |
| :--- | :--- | :--- | :--- | :--- |
| **LAB-00** | [00-cyber-range](00-cyber-range/) | Fundação do Cyber Range | Virtualização, segmentação, SSH seguro, recuperação de baseline | Concluído |
| **LAB-01** | `01-network-traffic-analysis` | Análise de Tráfego de Rede | tcpdump, Wireshark, ARP, TCP, DNS, análise de protocolos | Próximo |
| **LAB-02** | `02-host-network-firewalls` | Firewalls de Rede e Host | nftables, UFW, filtragem stateful, controle de entrada/saída | Planejado |
| **LAB-03** | `03-soc-telemetry-monitoring` | Telemetria de Segurança e Monitoramento | journald, rsyslog, auditd, gerenciamento centralizado de logs | Planejado |
| **LAB-04** | `04-vulnerability-management` | Gestão de Vulnerabilidades | Nmap, OpenVAS/Nessus, CVSS, remediação | Planejado |
| **LAB-05** | `05-identity-access-governance` | Gestão de Identidade e Acesso | SSH, PAM, sudo, RBAC, PKI | Planejado |
| **LAB-06** | `06-linux-security` | Engenharia de Segurança Linux | Hardening Linux, serviços, permissões, capabilities | Planejado |
| **LAB-07** | `07-windows-security` | Segurança Windows | Windows internals, PowerShell, controles de segurança | Planejado |
| **LAB-08** | `08-active-directory` | Segurança em Active Directory | AD, Kerberos, LDAP, Diretivas de Grupo, ataques de identidade | Planejado |
| **LAB-09** | `09-web-security` | Segurança de Aplicações Web | HTTP, autenticação, sessões, vulnerabilidades OWASP | Planejado |
| **LAB-10** | `10-api-security` | Segurança de APIs | REST, JWT, autorização, superfície de ataque em APIs | Planejado |
| **LAB-11** | `11-devsecops` | Segurança em DevSecOps | SAST, DAST, SCA, gates de segurança em CI/CD | Planejado |
| **LAB-12** | `12-container-security` | Segurança de Contêineres | Docker, imagens, registros, segredos, segurança em runtime | Planejado |
| **LAB-13** | `13-kubernetes-security` | Segurança em Kubernetes | RBAC, políticas de rede, segredos, proteção de workloads | Planejado |
| **LAB-14** | `14-cloud-security` | Segurança em Nuvem | IAM, segurança de rede, storage, logs, postura em nuvem | Planejado |
| **LAB-15** | `15-detection-engineering` | Engenharia de Detecção | Regras de detecção, Sigma, mapeamento ATT&CK, lógica de alertas | Planejado |
| **LAB-16** | `16-soc` | Operações de SOC | Triagem de alertas, investigação, escalonamento, monitoramento | Planejado |
| **LAB-17** | `17-incident-response` | Resposta a Incidentes | Contenção, erradicação, recuperação, procedimentos de RI | Planejado |
| **LAB-18** | `18-dfir` | Forense Digital e Resposta a Incidentes | Disco, memória, logs, análise de linha do tempo e forense | Planejado |
| **LAB-19** | `19-reconnaissance` | Reconhecimento de Segurança | OSINT, DNS, enumeração, descoberta de superfície de ataque | Planejado |
| **LAB-20** | `20-red-team` | Operações de Red Team | Reconhecimento, exploração, elevação de privilégio, movimento lateral | Planejado |
| **LAB-21** | `21-ad-red-team` | Red Team em Active Directory | Ataques Kerberos, abuso de credenciais, elevação de privilégio em AD | Planejado |
| **LAB-22** | `22-adversary-emulation` | Emulação de Adversários | Simulação de ataques baseada em ATT&CK e procedimentos operacionais | Planejado |
| **LAB-23** | `23-purple-team` | Operações de Purple Team | Simulação de ataques, validação de detecção, defesa colaborativa | Planejado |
| **LAB-24** | `24-detection-gap` | Análise de Lacunas de Detecção | Identificar, reproduzir e sanar falhas e gaps de detecção | Planejado |
| **LAB-25** | `25-security-automation` | Automação de Segurança | Python, APIs, fluxos estilo SOAR, resposta automatizada | Planejado |
| **LAB-26** | `26-security-engineering` | Engenharia de Segurança | Arquitetura segura, controles, integrações, decisões de engenharia | Planejado |
| **LAB-99** | `99-capstone` | Capstone de Engenharia de Cibersegurança | Operação integrada de ponta a ponta ofensiva, defensiva e de engenharia | Planejado |

---

## 3. Modelo de Progressão de Competências

```text
Fundação
    ↓
Redes e Sistemas
    ↓
Identidade e Acesso
    ↓
Segurança Web e Aplicações
    ↓
DevSecOps e Nuvem
    ↓
Detecção e SOC
    ↓
DFIR e Resposta a Incidentes
    ↓
Reconhecimento e Red Team
    ↓
Purple Team e Engenharia de Detecção
    ↓
Automação de Segurança
    ↓
Engenharia de Segurança
    ↓
Capstone
```

---

## 4. Padrão de Qualidade Baseado em Evidências
Cada projeto deste repositório adota um padrão de documentação estruturado em cinco pilares:
- Relatório Técnico Mestre (docs/): Documento técnico bilíngue detalhando passos de execução, parâmetros de engenharia e resultados obtidos.
- Análise de Causa Raiz (docs/troubleshooting.md): Registros no padrão SRE contendo identificação de sintomas, hipóteses, remediação aplicada e revalidação.
- Linhas de Base de Arquitetura e Hardening (docs/): Modelos de isolamento, diagramas de topologia e mitigação de superfície de ataque.
- Telemetria Bruta de Terminal (commands/): Transcrições em texto simples contendo estados observados do sistema, tabelas de roteamento, sockets em escuta e status de serviços.
- Matriz de Auditoria e Verificação (reports/): Tabelas formais de rastreabilidade correlacionando as imagens da pasta evidence/ às competências demonstradas.

---

## 5. Estrutura do Repositório

```text
cybersecurity-portfolio/
|-- 00-cyber-range/
|   |-- commands/
|   |-- docs/
|   |-- evidence/
|   |-- reports/
|   `-- README.md
|-- 01-network-traffic-analysis/
|-- 02-host-network-firewalls/
|-- 03-soc-telemetry-monitoring/
|-- 04-vulnerability-management/
|-- 05-identity-access-governance/
|-- 06-linux-security/
|-- 07-windows-security/
|-- 08-active-directory/
|-- 09-web-security/
|-- 10-api-security/
|-- 11-devsecops/
|-- 12-container-security/
|-- 13-kubernetes-security/
|-- 14-cloud-security/
|-- 15-detection-engineering/
|-- 16-soc/
|-- 17-incident-response/
|-- 18-dfir/
|-- 19-reconnaissance/
|-- 20-red-team/
|-- 21-ad-red-team/
|-- 22-adversary-emulation/
|-- 23-purple-team/
|-- 24-detection-gap/
|-- 25-security-automation/
|-- 26-security-engineering/
|-- 99-capstone/
`-- README.md
```
