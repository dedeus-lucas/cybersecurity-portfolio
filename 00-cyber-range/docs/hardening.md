# LAB-00: Operating System and Access Hardening Baseline

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-operating-system-and-access-hardening-baseline)
* [Portuguese Version - PT-BR](#lab-00-linha-de-base-de-hardening-do-sistema-e-acesso)

## 1. Scope and Hardening Objectives
This document establishes the initial hardening baseline applied to srv-linux-base and its administrative clients. The primary objective is attack surface minimization: eliminating unused packages, disabling non-essential daemons and services, enforcing least privilege, and implementing robust cryptographic identity controls for administrative access.

---

## 2. Attack Surface Reduction: Headless Conversion

### Elimination of Graphical Display Components
For this laboratory, the server is operated without a graphical user interface to reduce memory consumption, minimize unnecessary attack surface, and avoid non-essential graphical components in the virtualized environment:
- Target Configuration: The default systemd target was configured to multi-user.target via systemctl set-default multi-user.target.
- Package Elimination: Graphical packages and display managers were uninstalled:
  apt-get purge -y gnome-core gdm3 task-gnome-desktop
  apt-get autoremove --purge -y
- Measured Memory Footprint: Measured idle RAM consumption after graphical package removal: below 180 MB.

---

## 3. Cryptographic and Administrative Hardening

### Asymmetric Identity Specification (Ed25519)
Authentication uses Ed25519, an EdDSA signature scheme based on the Edwards25519 curve, mathematically related to Curve25519.
- Key Parameters: 256-bit key length providing approximately 128 bits of security strength.
- Timing Attack Resilience: Ed25519 implementations are designed to use constant-time operations for sensitive computations, reducing exposure to certain timing-based side-channel attacks.
- Client Key Storage: Private keys are stored within the WSL2 Linux filesystem (~/.ssh/id_cyberrange), rather than in shared Windows-mounted paths.

### Strict File System Permissions (POSIX)
Permissions on administrative files are enforced to satisfy OpenSSH client verification policies:
- Client private key (~/.ssh/id_cyberrange): Mode 600 (-rw-------).
- Client configuration (~/.ssh/config): Mode 600 (-rw-------).
- Client public key (~/.ssh/id_cyberrange.pub): Mode 644 (-rw-r--r--).
- Guest authorization list (~/.ssh/authorized_keys): Mode 600 (-rw-------).
- Guest SSH configuration directory (~/.ssh): Mode 700 (drwx------).

---

## 4. Socket Inspection and Attack Surface Verification

Inspecting open listening sockets on srv-linux-base provides an audit baseline of active listening network interfaces:

| Protocol | Local Address | Port | Process | Access Policy |
| :--- | :--- | :--- | :--- | :--- |
| TCP | 0.0.0.0 / [::] | 22 | sshd | Ed25519 public-key authentication configured |
| UDP | None | N/A | None | No exposed UDP listeners |

This verified socket baseline confirms that no additional listening services were detected on the guest at the time of validation. Network reachability remains dependent on interface exposure, routing, and firewall policy.

---

# LAB-00: Linha de Base de Hardening do Sistema e Acesso

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-operating-system-and-access-hardening-baseline)
* [Versão em Português - PT-BR](#lab-00-linha-de-base-de-hardening-do-sistema-e-acesso)

## 1. Escopo e Objetivos de Hardening
Este documento formaliza a linha de base inicial de hardening aplicada ao servidor srv-linux-base e aos seus clientes administrativos. O objetivo central é a minimização da superfície de ataque: remoção de pacotes desnecessários, desativação de daemons e serviços não necessários, aplicação do princípio de menor privilégio e adoção de identidades criptográficas assimétricas para a gestão do ativo.

---

## 2. Redução da Superfície de Ataque: Conversão Headless

### Remoção dos Componentes de Interface Gráfica
Para este laboratório, o servidor é operado sem interface gráfica para reduzir o consumo de memória, minimizar a superfície de ataque desnecessária e evitar componentes gráficos não essenciais no ambiente virtualizado:
- Configuração de Alvo: O alvo padrão do systemd foi alterado para multi-user.target por meio do comando systemctl set-default multi-user.target.
- Eliminação de Pacotes: Os utilitários gráficos e gerenciadores de display foram purgados:
  apt-get purge -y gnome-core gdm3 task-gnome-desktop
  apt-get autoremove --purge -y
- Uso de Memória Medido: Consumo de RAM em estado ocioso medido após a remoção dos componentes gráficos: inferior a 180 MB.

---

## 3. Hardening Administrativo e Criptográfico

### Especificação de Chaves Assimétricas (Ed25519)
A autenticação remota utiliza Ed25519, um esquema de assinatura EdDSA baseado na curva Edwards25519, matematicamente relacionada à Curve25519.
- Parâmetros da Chave: Comprimento de 256 bits, equivalendo a aproximadamente 128 bits de nível de segurança.
- Resiliência a Ataques de Temporização: Implementações de Ed25519 são projetadas para utilizar operações em tempo constante durante cálculos sensíveis, reduzindo a exposição a determinados ataques de canal lateral baseados em temporização.
- Armazenamento de Credenciais: As chaves privadas são armazenadas no sistema de arquivos Linux do WSL2 (~/.ssh/id_cyberrange), e não em caminhos compartilhados ou montados a partir do Windows.

### Controle Restrito de Permissões POSIX
As permissões dos arquivos sensíveis seguem as exigências operacionais do daemon OpenSSH:
- Chave privada no cliente (~/.ssh/id_cyberrange): Modo 600 (-rw-------).
- Configuração do cliente (~/.ssh/config): Modo 600 (-rw-------).
- Chave pública no cliente (~/.ssh/id_cyberrange.pub): Modo 644 (-rw-r--r--).
- Lista de chaves autorizadas no guest (~/.ssh/authorized_keys): Modo 600 (-rw-------).
- Diretório de configuração SSH no guest (~/.ssh): Modo 700 (drwx------).

---

## 4. Auditoria de Sockets e Verificação de Exposição

A auditoria de portas e sockets de escuta na máquina srv-linux-base estabelece a linha de base de serviços ativos:

| Protocolo | Endereço Local | Porta | Processo | Política de Acesso |
| :--- | :--- | :--- | :--- | :--- |
| TCP | 0.0.0.0 / [::] | 22 | sshd | Autenticação por chave pública Ed25519 configurada |
| UDP | Nenhum | N/A | Nenhum | Sem listeners UDP abertos |

Essa linha de base de sockets confirma que nenhum serviço adicional em estado de escuta foi detectado no guest no momento da validação. A alcançabilidade pela rede permanece dependente da exposição das interfaces, do roteamento e das políticas de firewall.
