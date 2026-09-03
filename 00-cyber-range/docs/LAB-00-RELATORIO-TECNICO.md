# LAB-00: Cyber Range Foundation and Secure Administrative Access

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-cyber-range-foundation-and-secure-administrative-access)
* [Portuguese Version - PT-BR](#lab-00-fundação-do-cyber-range-e-acesso-administrativo-seguro)

## 1. Laboratory Identification

* Category: Cybersecurity / Infrastructure / Network Security
* Objective: Construction of an isolated computing infrastructure for controlled experimentation and secure administrative access.
* Environment: Windows 11 Host, WSL2, Oracle VirtualBox 7, Debian 12 Minimal (Headless).
* Status: Completed
* Completion Date: 2026-09-03
* Version: 1.0

## 2. Executive Summary

LAB-00 established the initial computing foundation of the Cyber Range. The environment was provisioned with Debian 12 Minimal without a graphical interface, reducing unnecessary resource consumption and minimizing the operational attack surface associated with graphical components and services.

Two network interfaces were configured with distinct logical purposes. The first interface uses NAT to provide controlled external connectivity for system updates and package installation. The second interface uses a Host-Only network with a static address of 192.168.56.10/24, establishing the private management network of the Cyber Range.

Secure administrative access was configured through the OpenSSH client running under WSL2. A dedicated asymmetric Ed25519 key pair was used for authentication, avoiding password-based interactive authentication for the administrative connection. File permissions were restricted according to POSIX security practices.

Finally, a baseline snapshot was created in VirtualBox to preserve a known-good state of the environment and provide predictable rollback capability before subsequent security experiments.

## 3. Scope and Network Topology

The scope included provisioning the first virtual server, identified as srv-linux-base, segmentation of network connectivity, removal of unnecessary graphical components, configuration of secure administrative access, and creation of a recoverable baseline.

Detailed architecture and network isolation information are documented in:

* docs/arquitetura.md
* docs/configuracao-rede.md

Network interfaces:

* Interface enp0s3: NAT mode in the hypervisor, providing controlled outbound Internet connectivity for system updates and package installation.
* Interface enp0s8: Host-Only mode in the hypervisor, using the private Cyber Range subnet 192.168.56.0/24 and static address 192.168.56.10/24 for management and laboratory communication.

The separation of these interfaces establishes different network roles within the environment:

* NAT: controlled external connectivity.
* Host-Only: private Cyber Range communication.

The Host-Only network establishes the primary trust boundary between the laboratory environment and the physical network used by the host system.

## 4. Secure Administrative Access Configuration

Administrative access was configured using the OpenSSH client running under WSL2.

Password-based interactive authentication was replaced by dedicated asymmetric key authentication for the laboratory connection.

Configuration characteristics:

* Cryptographic algorithm: Ed25519.
* Public key size: 256 bits.
* Approximate security level: 128-bit security.
* Client private key permissions: 600.
* SSH configuration file permissions: 600.
* Public key permissions: 644.
* SSH host alias: srv-linux-base.
* Target address: 192.168.56.10.
* Target port: TCP/22.
* IdentitiesOnly: enabled.

The IdentitiesOnly directive was used to ensure that the SSH client uses the explicitly configured identity for the target host, avoiding unnecessary attempts with other keys available through the SSH agent or default identity set.

The private key remained restricted to the WSL2 environment and was not shared with the Windows host filesystem.

## 5. Technical Validation and Evidence Record

### Evidence 01: SSH Client Configuration and Key Management

* Attachment: evidence/01-ssh-client-config.png
* What it proves: Presence of the SSH Host configuration block, dedicated Ed25519 key generation, explicit identity configuration, and restrictive POSIX permissions for sensitive SSH files.
* Demonstrated competency: Secure SSH client configuration and basic cryptographic credential management.

The evidence demonstrates that administrative access was configured intentionally rather than relying on default SSH behavior.

### Evidence 02: Network Connectivity and Direct SSH Authentication

* Attachment: evidence/02-ssh-connectivity-validation.png
* What it proves: Layer 3 reachability through ICMP Echo validation, communication between WSL2 and the virtual machine through the Host-Only network, successful resolution of the srv-linux-base alias, TCP/22 connectivity, and successful SSH authentication without an interactive password prompt.
* Demonstrated competency: TCP/IP troubleshooting, network connectivity validation, secure remote administration, and Linux terminal operation.

The evidence also records the validation performed after resolving the routing and connectivity issues encountered during implementation.

### Evidence 03: Operational Baseline and Golden Snapshot

* Attachment: evidence/03-golden-snapshot.png
* What it proves: Creation of the GOLDEN_IMAGE_BASE_CLI baseline snapshot in VirtualBox after the environment reached a known-good operational state.
* Demonstrated competency: Virtualized infrastructure management, baseline creation, environment recovery, and operational resilience.

The snapshot provides a predictable rollback point that can be used before experiments that may modify or destabilize the laboratory environment.

## 6. Troubleshooting Summary

Three relevant implementation issues were identified and resolved during the laboratory.

### 6.1 Graphical Environment and Server Stability

The default GNOME graphical environment caused visual instability and a system lock during the initial server configuration.

Resolution:

* Temporary boot parameter applied through GNU GRUB using systemd.unit=multi-user.target.
* Graphical packages were subsequently removed using purge.
* The system was configured to operate permanently with the multi-user.target.

Result:

The Debian installation was converted into a headless server-oriented environment without unnecessary graphical components.

### 6.2 Network Interface Configuration Syntax

The networking.service failed during initialization of interface enp0s8.

Investigation:

* Service status was inspected.
* System logs were analyzed using journalctl.
* The network interface configuration was reviewed.

Root cause:

Incorrect syntax in the static interface configuration.

Resolution:

The configuration was corrected using the appropriate static network declaration without an unnecessary default gateway on the Host-Only management interface.

Result:

The Host-Only interface initialized successfully with the expected static address.

### 6.3 WSL2 to Host-Only Network Routing

The WSL2 SSH client initially reported that there was no valid route to the 192.168.56.0/24 network.

Root cause:

The WSL2 networking environment did not initially have a route capable of reaching the VirtualBox Host-Only subnet.

Resolution:

A static route was added to the Linux routing table, directing traffic destined for the Cyber Range subnet through the appropriate Windows host gateway.

Validation:

* Route presence was verified.
* ICMP reachability was validated.
* SSH connectivity to 192.168.56.10 was established.
* Key-based authentication was successfully performed.

Detailed troubleshooting records are available in:

* docs/troubleshooting.md

## 7. Security Controls Implemented

### Network Isolation

* Separation between NAT and Host-Only network functions.
* Dedicated private subnet for Cyber Range communication.
* Direct exposure of laboratory services to the physical LAN through the Host-Only interface was avoided.

### Secure Administrative Access

* SSH used instead of insecure remote administration protocols.
* Ed25519 asymmetric authentication.
* Restricted permissions on private SSH material.
* Explicit SSH identity selection through IdentitiesOnly.

### Attack Surface Reduction

* Debian Minimal installation.
* Removal of unnecessary graphical components.
* Headless server operation.
* Reduction of unnecessary services and packages.

### Recovery and Baseline

* Creation of a known-good snapshot.
* Defined rollback point before subsequent experiments.
* Preservation of a clean laboratory state.

## 8. Demonstrated Competencies

### Linux Administration

* Debian Minimal server administration.
* Package management.
* systemd service management.
* Linux filesystem and POSIX permissions.
* SSH configuration.

### Networking

* Dual-NIC virtual machine architecture.
* NAT versus Host-Only networking.
* IPv4 addressing and subnetting.
* Static routing.
* ICMP connectivity validation.
* TCP/22 service connectivity.
* Basic WSL2 and virtual network troubleshooting.

### Security

* Network segmentation.
* SSH hardening fundamentals.
* Asymmetric authentication.
* Ed25519 key management.
* Permission restriction.
* Attack surface reduction.
* Baseline and rollback practices.

### Infrastructure and SecOps

* Type-2 hypervisor operation.
* Virtual machine provisioning.
* Infrastructure baseline management.
* Snapshot-based recovery.
* Controlled laboratory experimentation.
* Troubleshooting and technical validation.

## 9. Limitations and Security Considerations

LAB-00 represents the foundation of the Cyber Range and does not constitute a complete production security architecture.

The NAT interface provides controlled outbound connectivity but should not be considered a complete security boundary by itself.

The Host-Only network provides logical isolation from the physical LAN, but the overall security of the laboratory depends on the correct configuration of the hypervisor, virtual networking, firewall rules, operating system, and services.

The static route configured for WSL2 connectivity is an operational dependency of the current laboratory architecture and should be validated again after major changes to the WSL2 or Windows networking environment.

The VirtualBox snapshot is a recovery mechanism for the virtual machine state and should not be treated as a substitute for backup, disaster recovery, or immutable infrastructure.

## 10. Lessons Learned

LAB-00 established several practical lessons:

1. A server-oriented Linux environment does not require a graphical interface when administration can be performed remotely through SSH.
2. Network interfaces should have clearly defined roles rather than being configured only for connectivity.
3. Network segmentation is a security design decision, not merely a virtualization configuration.
4. Troubleshooting requires observation and validation rather than assumptions.
5. WSL2 introduces an additional networking layer that must be considered when integrating Linux tooling with virtualized environments.
6. Secure SSH administration depends not only on cryptography but also on correct identity selection and filesystem permissions.
7. A known-good baseline significantly reduces the recovery cost of destructive security experiments.
8. Technical evidence is stronger when it demonstrates both the configuration and the successful validation of the resulting behavior.

## 11. Next Steps

With the base environment operational and the baseline snapshot established, the next engineering cycle will begin with LAB-01.

LAB-01 will focus on network traffic analysis, TCP three-way handshake dissection, packet capture, and protocol inspection using tcpdump and Wireshark.

The Cyber Range will progressively evolve from the single-server foundation established in LAB-00 into a multi-system security experimentation environment.

---

# LAB-00: Fundação do Cyber Range e Acesso Administrativo Seguro

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-cyber-range-foundation-and-secure-administrative-access)
* [Versão em Português - PT-BR](#lab-00-fundação-do-cyber-range-e-acesso-administrativo-seguro)

## 1. Identificação do Laboratório

* Categoria: Cibersegurança / Infraestrutura / Segurança de Redes
* Objetivo: Construção da infraestrutura computacional isolada para experimentação controlada e acesso administrativo seguro.
* Ambiente: Windows 11 Host, WSL2, Oracle VirtualBox 7, Debian 12 Minimal (Headless).
* Status: Concluído
* Data da Conclusão: 03/09/2026
* Versão: 1.0

## 2. Sumário Executivo

O LAB-00 estabeleceu a fundação computacional inicial do Cyber Range. O ambiente foi provisionado com Debian 12 Minimal sem interface gráfica, reduzindo o consumo desnecessário de recursos e minimizando a superfície de ataque operacional associada a componentes e serviços gráficos.

Foram configuradas duas interfaces de rede com funções lógicas distintas. A primeira utiliza NAT para fornecer conectividade externa controlada para atualizações do sistema e instalação de pacotes. A segunda utiliza uma rede Host-Only com o endereço estático 192.168.56.10/24, estabelecendo a rede privada de gerenciamento do Cyber Range.

O acesso administrativo seguro foi configurado por meio do cliente OpenSSH executado no WSL2. Foi utilizado um par de chaves assimétricas Ed25519 dedicado para autenticação, eliminando a necessidade de autenticação interativa por senha para a conexão administrativa. As permissões dos arquivos foram restringidas de acordo com práticas de segurança POSIX.

Por fim, foi criado um snapshot de baseline no VirtualBox para preservar um estado conhecido e funcional do ambiente e fornecer capacidade previsível de rollback antes dos experimentos de segurança subsequentes.

## 3. Escopo e Topologia de Rede

O escopo incluiu o provisionamento do primeiro servidor virtual, identificado como srv-linux-base, a segmentação da conectividade de rede, a remoção de componentes gráficos desnecessários, a configuração do acesso administrativo seguro e a criação de um baseline recuperável.

Os detalhes completos de arquitetura e isolamento de rede estão documentados em:

* docs/arquitetura.md
* docs/configuracao-rede.md

Interfaces de rede:

* Interface enp0s3: modo NAT no hypervisor, fornecendo conectividade externa controlada para atualização do sistema e instalação de pacotes.
* Interface enp0s8: modo Host-Only no hypervisor, utilizando a sub-rede privada 192.168.56.0/24 e o endereço estático 192.168.56.10/24 para gerenciamento e comunicação do laboratório.

A separação das interfaces estabelece diferentes funções de rede dentro do ambiente:

* NAT: conectividade externa controlada.
* Host-Only: comunicação privada do Cyber Range.

A rede Host-Only estabelece a principal fronteira de confiança entre o ambiente de laboratório e a rede física utilizada pelo sistema host.

## 4. Configuração do Acesso Administrativo Seguro

O acesso administrativo foi configurado utilizando o cliente OpenSSH executado no WSL2.

A autenticação interativa baseada em senha foi substituída por autenticação baseada em chaves assimétricas dedicada para a conexão do laboratório.

Características da configuração:

* Algoritmo criptográfico: Ed25519.
* Tamanho da chave pública: 256 bits.
* Nível aproximado de segurança: 128 bits.
* Permissões da chave privada no cliente: 600.
* Permissões do arquivo de configuração SSH: 600.
* Permissões da chave pública: 644.
* Alias SSH: srv-linux-base.
* Endereço de destino: 192.168.56.10.
* Porta de destino: TCP/22.
* IdentitiesOnly: habilitado.

A diretiva IdentitiesOnly foi utilizada para garantir que o cliente SSH utilize a identidade explicitamente configurada para o host de destino, evitando tentativas desnecessárias com outras chaves disponíveis no agente SSH ou no conjunto padrão de identidades.

A chave privada permaneceu restrita ao ambiente WSL2 e não foi compartilhada com o sistema de arquivos do host Windows.

## 5. Validação Técnica e Registro de Evidências

### Evidência 01: Configuração do Cliente SSH e Gerenciamento de Chaves

* Anexo: evidence/01-ssh-client-config.png
* O que comprova: Presença do bloco Host na configuração SSH, geração da chave Ed25519 dedicada, configuração explícita da identidade e permissões POSIX restritivas para os arquivos sensíveis do SSH.
* Competência demonstrada: Configuração segura de cliente SSH e gerenciamento básico de credenciais criptográficas.

A evidência demonstra que o acesso administrativo foi configurado de forma intencional, em vez de depender do comportamento padrão do SSH.

### Evidência 02: Conectividade de Rede e Autenticação SSH Direta

* Anexo: evidence/02-ssh-connectivity-validation.png
* O que comprova: Alcançabilidade na Camada 3 por meio de validação ICMP Echo, comunicação entre WSL2 e a máquina virtual pela rede Host-Only, resolução do alias srv-linux-base, conectividade TCP/22 e autenticação SSH bem-sucedida sem solicitação interativa de senha.
* Competência demonstrada: Troubleshooting TCP/IP, validação de conectividade de rede, administração remota segura e operação de terminal Linux.

A evidência também registra a validação realizada após a resolução dos problemas de roteamento e conectividade encontrados durante a implementação.

### Evidência 03: Baseline Operacional e Golden Snapshot

* Anexo: evidence/03-golden-snapshot.png
* O que comprova: Criação do snapshot de baseline GOLDEN_IMAGE_BASE_CLI no VirtualBox após o ambiente atingir um estado operacional conhecido e funcional.
* Competência demonstrada: Gerenciamento de infraestrutura virtualizada, criação de baselines, recuperação de ambiente e resiliência operacional.

O snapshot fornece um ponto de rollback previsível que pode ser utilizado antes de experimentos que possam modificar ou desestabilizar o ambiente de laboratório.

## 6. Síntese do Troubleshooting Executado

Três problemas relevantes de implementação foram identificados e solucionados durante o laboratório.

### 6.1 Ambiente Gráfico e Estabilidade do Servidor

O ambiente gráfico GNOME instalado por padrão causou instabilidade visual e travamento do sistema durante a configuração inicial do servidor.

Resolução:

* Parâmetro temporário de boot aplicado pelo GNU GRUB utilizando systemd.unit=multi-user.target.
* Pacotes gráficos posteriormente removidos utilizando purge.
* Sistema configurado permanentemente para operar com multi-user.target.

Resultado:

A instalação Debian foi convertida em um ambiente orientado a servidor headless, sem componentes gráficos desnecessários.

### 6.2 Erro de Sintaxe na Configuração da Interface de Rede

O serviço networking.service falhou durante a inicialização da interface enp0s8.

Investigação:

* Status do serviço analisado.
* Logs do sistema examinados utilizando journalctl.
* Configuração da interface de rede revisada.

Causa:

Sintaxe incorreta na configuração da interface estática.

Resolução:

A configuração foi corrigida utilizando a declaração estática apropriada, sem um default gateway desnecessário na interface Host-Only de gerenciamento.

Resultado:

A interface Host-Only foi inicializada corretamente com o endereço estático esperado.

### 6.3 Roteamento entre WSL2 e a Rede Host-Only

O cliente SSH do WSL2 inicialmente apresentou erro indicando ausência de uma rota válida para a rede 192.168.56.0/24.

Causa:

O ambiente de rede do WSL2 inicialmente não possuía uma rota capaz de alcançar a sub-rede Host-Only do VirtualBox.

Resolução:

Foi adicionada uma rota estática na tabela de roteamento do Linux, direcionando o tráfego destinado à sub-rede do Cyber Range pelo gateway apropriado do host Windows.

Validação:

* Presença da rota verificada.
* Alcançabilidade ICMP validada.
* Conectividade SSH com 192.168.56.10 estabelecida.
* Autenticação baseada em chave realizada com sucesso.

Os registros detalhados de troubleshooting estão disponíveis em:

* docs/troubleshooting.md

## 7. Controles de Segurança Implementados

### Isolamento de Rede

* Separação entre as funções NAT e Host-Only.
* Sub-rede privada dedicada para comunicação do Cyber Range.
* Evitada a exposição direta dos serviços do laboratório à LAN física por meio da interface Host-Only.

### Acesso Administrativo Seguro

* Utilização de SSH em substituição a protocolos remotos inseguros.
* Autenticação assimétrica Ed25519.
* Permissões restritas para materiais privados do SSH.
* Seleção explícita da identidade por meio de IdentitiesOnly.

### Redução da Superfície de Ataque

* Instalação Debian Minimal.
* Remoção de componentes gráficos desnecessários.
* Operação como servidor headless.
* Redução de serviços e pacotes desnecessários.

### Recuperação e Baseline

* Criação de snapshot conhecido e funcional.
* Definição de ponto de rollback antes dos experimentos subsequentes.
* Preservação de um estado limpo do laboratório.

## 8. Competências Demonstradas

### Administração Linux

* Administração de servidor Debian Minimal.
* Gerenciamento de pacotes.
* Gerenciamento de serviços com systemd.
* Sistema de arquivos Linux e permissões POSIX.
* Configuração SSH.

### Redes

* Arquitetura de máquina virtual com duas interfaces de rede.
* Diferença entre NAT e Host-Only.
* Endereçamento IPv4 e sub-redes.
* Roteamento estático.
* Validação de conectividade ICMP.
* Conectividade de serviço TCP/22.
* Troubleshooting básico de WSL2 e redes virtuais.

### Segurança

* Segmentação de rede.
* Fundamentos de hardening do SSH.
* Autenticação assimétrica.
* Gerenciamento de chaves Ed25519.
* Restrição de permissões.
* Redução da superfície de ataque.
* Práticas de baseline e rollback.

### Infraestrutura e SecOps

* Operação de hypervisor Tipo 2.
* Provisionamento de máquinas virtuais.
* Gerenciamento de baseline de infraestrutura.
* Recuperação baseada em snapshots.
* Experimentação controlada em laboratório.
* Troubleshooting e validação técnica.

## 9. Limitações e Considerações de Segurança

O LAB-00 representa a fundação do Cyber Range e não constitui uma arquitetura completa de segurança de produção.

A interface NAT fornece conectividade externa controlada, mas não deve ser considerada isoladamente como uma fronteira de segurança completa.

A rede Host-Only fornece isolamento lógico em relação à LAN física, mas a segurança geral do laboratório depende da configuração correta do hypervisor, das redes virtuais, das regras de firewall, do sistema operacional e dos serviços.

A rota estática configurada para conectividade com o WSL2 constitui uma dependência operacional da arquitetura atual e deve ser validada novamente após alterações relevantes no ambiente de rede do WSL2 ou do Windows.

O snapshot do VirtualBox é um mecanismo de recuperação do estado da máquina virtual e não deve ser tratado como substituto de backup, disaster recovery ou infraestrutura imutável.

## 10. Lições Aprendidas

O LAB-00 estabeleceu diversos aprendizados práticos:

1. Um ambiente Linux orientado a servidor não necessita de interface gráfica quando a administração pode ser realizada remotamente por SSH.
2. As interfaces de rede devem possuir funções claramente definidas, em vez de serem configuradas apenas para fornecer conectividade.
3. Segmentação de rede é uma decisão de arquitetura de segurança, e não apenas uma configuração de virtualização.
4. Troubleshooting exige observação e validação, e não apenas suposições.
5. O WSL2 adiciona uma camada de rede que deve ser considerada ao integrar ferramentas Linux com ambientes virtualizados.
6. A administração SSH segura depende não apenas da criptografia, mas também da correta seleção da identidade e das permissões do sistema de arquivos.
7. Um baseline conhecido e funcional reduz significativamente o custo de recuperação de experimentos destrutivos.
8. Uma evidência técnica é mais forte quando demonstra tanto a configuração quanto a validação bem-sucedida do comportamento resultante.

## 11. Próximos Passos

Com o ambiente base operacional e o snapshot de baseline estabelecido, o próximo ciclo de engenharia será iniciado com o LAB-01.

O LAB-01 será focado em análise de tráfego de rede, dissecação do handshake TCP de três vias, captura de pacotes e inspeção de protocolos utilizando tcpdump e Wireshark.

O Cyber Range evoluirá progressivamente, partindo da fundação de servidor único estabelecida no LAB-00 para um ambiente de experimentação em segurança composto por múltiplos sistemas.
