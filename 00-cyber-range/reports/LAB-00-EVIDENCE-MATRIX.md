# LAB-00: Technical Evidence and Competency Verification Matrix

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-technical-evidence-and-competency-verification-matrix)
* [Portuguese Version - PT-BR](#lab-00-matriz-de-evidências-técnicas-e-verificação-de-competências)

## 1. Audit Framework and Traceability Standard
This matrix establishes the correlation between executed commands, observed technical telemetry, captured visual/textual artifacts, and verified competencies for LAB-00. Each evidence entry validates specific implementation claims to demonstrate defensible systems engineering practices.

---

## 2. Core Visual Evidence Registry

| Evidence ID | Target File | Execution Scope | Core Observation | Technical Validation | Verified Competency |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **E01** | `evidence/01-ssh-client-config.png` | WSL2 Client | `cat ~/.ssh/config` output and `ls -la ~/.ssh/id_cyberrange*` file mode | Expected POSIX permissions (600 for private key, 644 for public key), dedicated Ed25519 key definition, and IdentitiesOnly enforcement | Cryptographic Key Management & Secure Client Configuration |
| **E02** | `evidence/02-ssh-connectivity-validation.png` | WSL2 to Guest | `ping -c 3 192.168.56.10` and `ssh srv-linux-base "whoami && hostname && ip -brief addr"` | 0% ICMP packet loss during validation, successful key-based authentication, and dual-NIC allocation (10.0.2.15 / 192.168.56.10) | L3/L4 Network Troubleshooting, Dual-NIC Segmentation & Remote Administration |
| **E03** | `evidence/03-golden-snapshot.png` | Host Hypervisor | `VBoxManage snapshot "srv-linux-base" list --details` | Registered snapshot GOLDEN_IMAGE_BASE_CLI with baseline hardware configuration | Hypervisor Baseline Control, Disaster Recovery & Operational Resilience |

---

## 3. Telemetry and Raw Artifact Cross-Reference

| Telemetry Source | Associated Raw Log | Corroborating Observation | Security Relevance |
| :--- | :--- | :--- | :--- |
| **Network Interfaces** | `commands/network-baseline.txt` | `ip -brief address`, `ip route`, and `/etc/network/interfaces` confirm the intended interface addressing and routing configuration | Confirms non-conflicting default gateway topology |
| **SSH Daemon State** | `commands/ssh-validation.txt` | Port 22 listening on TCP; matching public-key fingerprints confirm correspondence between the client and authorized key material | Confirms intentional pairing between client and server credentials |
| **Listening Sockets** | `commands/firewall-validation.txt` | Clean socket table; confirms the absence of additional network listening services at validation time | Demonstrates minimal exposure of listening ports on the guest |
| **Hypervisor State** | `commands/snapshot-validation.txt` | Virtual hardware inventory matching dual-NIC profile and resource constraints | Documents exact baseline configuration prior to destructive testing |

---

## 4. Verification Methodology Matrix

| Item | Action Executed | Expected State | Observed State | Audit Status |
| :--- | :--- | :--- | :--- | :--- |
| **V01** | Client Key Permission Audit | Permissions strictly set to mode 600 | Mode 600 confirmed on private key file | Validated |
| **V02** | Layer 3 Routing Validation | Reachability from WSL2 to the Host-Only subnet through the configured Windows host routing path | 0% packet loss to 192.168.56.10 via static route | Validated |
| **V03** | SSH Public-Key Authentication Validation | Interactive password prompt bypassed | Successful authentication using the configured Ed25519 private key and corresponding authorized public key | Validated |
| **V04** | Recovery Baseline Registration | Snapshot tree records consistent base image | Snapshot GOLDEN_IMAGE_BASE_CLI registered as the known-good recovery baseline | Validated |

---

# LAB-00: Matriz de Evidências Técnicas e Verificação de Competências

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-technical-evidence-and-competency-verification-matrix)
* [Versão em Português - PT-BR](#lab-00-matriz-de-evidências-técnicas-e-verificação-de-competências)

## 1. Estrutura de Auditoria e Padrão de Rastreabilidade
Esta matriz formaliza a correlação entre os comandos executados, a telemetria técnica observada, os artefatos visuais/textuais capturados e as competências verificadas no LAB-00. Cada entrada de evidência valida alegações específicas de implementação para comprovar práticas defensáveis de engenharia de sistemas.

---

## 2. Registro Principal de Evidências Visuais

| ID Evidência | Arquivo Alvo | Escopo de Execução | Observação Central | Validação Técnica | Competência Verificada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **E01** | `evidence/01-ssh-client-config.png` | Cliente WSL2 | Saída de `cat ~/.ssh/config` e permissões de `ls -la ~/.ssh/id_cyberrange*` | Permissões POSIX esperadas (600 para chave privada, 644 para chave pública), definição de chave Ed25519 dedicada e uso de IdentitiesOnly | Gestão de Credenciais Criptográficas e Configuração Segura de Clientes |
| **E02** | `evidence/02-ssh-connectivity-validation.png` | WSL2 para Guest | `ping -c 3 192.168.56.10` e `ssh srv-linux-base "whoami && hostname && ip -brief addr"` | 0% de perda de pacotes ICMP durante a validação, autenticação por chave bem-sucedida e alocação Dual-NIC (10.0.2.15 / 192.168.56.10) | Troubleshooting de Redes L3/L4, Segmentação Dual-NIC e Administração Remota |
| **E03** | `evidence/03-golden-snapshot.png` | Hypervisor Host | `VBoxManage snapshot "srv-linux-base" list --details` | Snapshot GOLDEN_IMAGE_BASE_CLI registrado com a configuração de hardware da linha de base | Controle de Linha de Base em Hypervisor, Recuperação de Desastres e Resiliência |

---

## 3. Rastreabilidade Cruzada com Logs Brutos de Telemetria

| Fonte de Telemetria | Log Bruto Associado | Observação Corroborativa | Relevância para Segurança |
| :--- | :--- | :--- | :--- |
| **Interfaces de Rede** | `commands/network-baseline.txt` | `ip -brief address`, `ip route` e `/etc/network/interfaces` confirmam o endereçamento e a configuração de roteamento previstos para as interfaces | Garante topologia sem rotas padrão concorrentes |
| **Estado do Daemon SSH** | `commands/ssh-validation.txt` | Porta 22 em escuta TCP; fingerprints correspondentes confirmam a correspondência entre a chave do cliente e o material de chave autorizado | Confirma o emparelhamento intencional entre credenciais de cliente e servidor |
| **Sockets em Escuta** | `commands/firewall-validation.txt` | Tabela de sockets limpa; confirma a ausência de serviços adicionais em estado de escuta na rede no momento da validação | Demonstra exposição mínima de portas em estado de escuta no guest |
| **Estado do Hypervisor** | `commands/snapshot-validation.txt` | Inventário de hardware virtual consistente com o perfil Dual-NIC e restrições de recursos | Documenta os parâmetros exatos da baseline antes de testes destrutivos |

---

## 4. Matriz de Validação Metodológica

| Item | Ação Executada | Estado Esperado | Estado Observado | Status de Auditoria |
| :--- | :--- | :--- | :--- | :--- |
| **V01** | Auditoria de Permissões de Chave | Permissões estritamente restritas em 600 | Modo 600 confirmado no arquivo da chave privada | Validado |
| **V02** | Validação de Roteamento Camada 3 | Alcançabilidade do WSL2 até a sub-rede Host-Only por meio da rota configurada através do host Windows | 0% de perda de pacotes para 192.168.56.10 via rota estática | Validado |
| **V03** | Validação da Autenticação SSH por Chave Pública | Ausência de prompt interativo de senha | Autenticação bem-sucedida utilizando a chave privada Ed25519 configurada e a respectiva chave pública autorizada | Validado |
| **V04** | Registro da Baseline de Recuperação | Árvore de snapshots documenta estado íntegro | Snapshot GOLDEN_IMAGE_BASE_CLI registrado como linha de base conhecida para recuperação | Validado |
