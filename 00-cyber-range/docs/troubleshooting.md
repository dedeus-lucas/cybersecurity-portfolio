# LAB-00: Technical Troubleshooting Record and Root Cause Analysis

**Language: English (EN-US)**

**Document Navigation:**

* [English Version](#lab-00-technical-troubleshooting-record-and-root-cause-analysis)
* [Portuguese Version - PT-BR](#lab-00-registro-técnico-de-troubleshooting-e-análise-de-causa-raiz)

## 1. Overview and Engineering Methodology

This document formalizes the operational issues, diagnostic procedures, hypotheses, corrective measures, and technical validations executed during the deployment of LAB-00 (Cyber Range Foundation).

The troubleshooting process followed a structured systems administration and reliability engineering methodology:

* Symptom Identification and Observation
* Telemetry and Log Inspection
* Root Cause Hypothesis Formulation
* Controlled Testing
* Corrective Implementation
* Empirical Re-testing
* Evidence Capture
* Final Validation

Each incident is documented using the following engineering sequence:

Observation -> Hypothesis -> Investigation -> Root Cause -> Remediation -> Validation

The distinction between observed facts and technical hypotheses is maintained throughout the document. Conclusions are only classified as confirmed when supported by direct system evidence or successful validation.

---

## 2. Issue Log: Detailed Technical Breakdowns

### Issue 01: Graphical Environment Display Lockup

* Severity: High (Blocking interactive graphical access and normal boot completion)
* Subsystem: Linux Graphical Stack / systemd Targets / VirtualBox Virtual Display Environment
* Occurrence Phase: First post-installation reboot.

#### Impact

The graphical environment prevented normal interactive access to the server after boot and conflicted with the intended headless architecture of the laboratory.

#### Symptom

Following the GNU GRUB selection screen, the virtual machine displayed a black screen with an inactive blinking cursor.

The expected GDM3 graphical login interface was not presented, and graphical initialization did not complete successfully.

#### Hypothesis

The Debian installation included GNOME graphical components that were unnecessary for the intended server-oriented architecture.

The failure was suspected to be related to the interaction between the graphical stack and the virtualized display environment, potentially involving display driver or graphical-session initialization issues.

This hypothesis was not treated as fully confirmed because the primary objective of the investigation was to determine whether the Linux kernel and non-graphical operating environment remained functional.

#### Investigation and Diagnostic Commands

1. The VM was forcefully restarted through the VirtualBox management interface.

2. The automated boot countdown was interrupted at the GNU GRUB menu.

3. The kernel boot parameters were edited through the GNU GRUB editor.

4. The following systemd target selector was appended:

   systemd.unit=multi-user.target

5. Boot was resumed using the GRUB boot command.

#### Diagnostic Result

The system successfully booted into the text-based multi-user target and presented the TTY1 console.

This established that:

* The Linux kernel was operational.
* The base operating system remained functional.
* The VM was capable of completing the boot process without the graphical target.
* The failure was isolated to the graphical initialization path.

#### Root Cause

The confirmed operational root cause was the presence of an unnecessary graphical environment in a server-oriented headless virtual machine.

The exact underlying graphical driver/session interaction was not required to be fully isolated because the architectural decision was to remove the graphical stack entirely.

#### Remediation and Corrective Implementation

The following actions were executed from the administrative text console:

1. The system was configured to use the multi-user target as the persistent default:

   systemctl set-default multi-user.target

2. The GDM3 display manager was stopped and disabled:

   systemctl stop gdm3
   systemctl disable gdm3

3. Unnecessary graphical packages were removed:

   apt-get purge -y gnome-core gdm3 task-gnome-desktop
   apt-get autoremove --purge -y
   apt-get clean

#### Validation

The VM was rebooted and successfully initialized directly into the text-based login prompt:

srv-linux-base login:

The observed memory consumption after conversion to the headless configuration remained below 180 MB during the validation state.

#### Final Result

The graphical failure was eliminated and the VM was aligned with its intended server-oriented architecture.

---

### Issue 02: Network Interface Manager Initialization Failure

* Severity: Medium (Blocking persistent Host-Only IP configuration)
* Subsystem: systemd / ifupdown / /etc/network/interfaces
* Occurrence Phase: Network baseline configuration for enp0s8.

#### Impact

The failure prevented the Host-Only interface from receiving its intended persistent static IPv4 configuration during service initialization.

#### Symptom

Executing:

systemctl restart networking

resulted in a service failure:

Job for networking.service failed because the control process exited with error code.

#### Hypothesis

The /etc/network/interfaces configuration contained an invalid or malformed declaration affecting interface enp0s8.

#### Investigation and Diagnostic Commands

The systemd journal for the networking service was inspected:

journalctl -xeu networking.service -n 20 --no-pager

The following error was observed:

ifup: /etc/network/interfaces:11: unknown or no address type and no inherits keyword specified
ifup: couldn't read interfaces file /etc/network/interfaces

#### Root Cause

A syntax error was confirmed in line 11 of /etc/network/interfaces.

The malformed configuration prevented ifupdown from correctly parsing the network interface declaration.

#### Remediation and Corrective Implementation

A temporary runtime configuration was first applied using iproute2:

ip link set enp0s8 up
ip addr flush dev enp0s8
ip addr add 192.168.56.10/24 dev enp0s8

The persistent configuration was then corrected using the appropriate ifupdown declaration:

auto enp0s8
iface enp0s8 inet static
address 192.168.56.10
netmask 255.255.255.0

No default gateway was assigned to enp0s8 because the NAT interface enp0s3 was responsible for the default outbound route.

#### Validation

The networking service was restarted:

systemctl restart networking

The interface state and address assignment were then inspected:

ip -brief address show

Expected result:

* enp0s8: UP
* IPv4 address: 192.168.56.10/24

Persistence was subsequently confirmed across reboot.

#### Final Result

The Host-Only interface successfully initialized with the intended static address and remained operational after system restart.

---

### Issue 03: WSL2 Routing Failure to the VirtualBox Host-Only Subnet

* Severity: High (Blocking remote administrative access from the WSL2 client)
* Subsystem: WSL2 Linux Routing Table / Windows Host Networking / VirtualBox Host-Only Network
* Occurrence Phase: First remote SSH connectivity test.

#### Impact

The WSL2 environment could not reach the Debian virtual machine through the Host-Only management network, preventing SSH-based administrative access.

#### Symptom

An SSH connection attempt from WSL2 returned:

ssh: connect to host 192.168.56.10 port 22: No route to host

ICMP validation initially resulted in complete packet loss:

3 packets transmitted, 0 received

#### Hypothesis

WSL2 operates within its own virtualized networking environment and uses a virtual gateway to communicate with the Windows host.

The WSL2 routing table did not contain a route for the VirtualBox Host-Only subnet:

192.168.56.0/24

Therefore, traffic destined for the Cyber Range management network could not be forwarded correctly from the WSL2 environment.

#### Investigation and Diagnostic Commands

The existing routing table was inspected:

ip route show

The default gateway was then tested:

ping -c 2 $(ip route show default | awk '{print $3}')

Successful responses confirmed that communication between WSL2 and the Windows host gateway was operational.

This narrowed the problem to routing beyond the WSL2 default gateway toward the VirtualBox Host-Only network.

#### Root Cause

The WSL2 Linux routing table lacked a route for the 192.168.56.0/24 subnet.

The missing route prevented WSL2 from forwarding traffic toward the Windows host interface that provided access to the VirtualBox Host-Only network.

#### Remediation and Corrective Implementation

The Windows host transit gateway visible from WSL2 was dynamically identified:

GW_WIN=$(ip route show default | awk '{print $3}')

A route for the Cyber Range management subnet was then added:

sudo ip route add 192.168.56.0/24 via "$GW_WIN"

This created the required forwarding path from WSL2 toward the Windows host and subsequently to the VirtualBox Host-Only network.

#### Validation - Layer 3

ICMP reachability was tested again:

ping -c 3 192.168.56.10

Result:

* 0% packet loss
* Round-trip latency below 1 ms during the observed validation

This confirmed Layer 3 reachability between the WSL2 client and the Debian VM.

#### Validation - SSH Application Service

The administrative SSH connection was then tested:

ssh srv-linux-base "whoami && hostname"

Result:

* Successful TCP connection to port 22.
* SSH key-based authentication completed successfully.
* No interactive password prompt was presented.
* Remote command execution succeeded.
* The expected remote hostname was returned.

#### Final Result

The WSL2-to-Cyber-Range management path became operational, allowing secure administrative access to srv-linux-base through SSH.

#### Operational Consideration

The route was added dynamically through iproute2.

Therefore, unless an additional persistence mechanism is configured, this route should be considered an operational dependency that may need to be recreated after WSL2 restart or networking changes.

The persistence mechanism should be documented separately if implemented in a future iteration of the Cyber Range.

---

## 3. Cross-Incident Validation Summary

The three incidents were independently resolved and subsequently validated.

| Validation              | Expected Result             | Observed Result       | Status |
| ----------------------- | --------------------------- | --------------------- | ------ |
| Headless boot           | Direct TTY access           | Successful            | PASS   |
| RAM baseline            | Reduced server footprint    | Below 180 MB observed | PASS   |
| enp0s8 configuration    | 192.168.56.10/24            | Address assigned      | PASS   |
| Network service restart | No service failure          | Successful            | PASS   |
| WSL2 route              | Route to 192.168.56.0/24    | Route operational     | PASS   |
| ICMP                    | Reachable VM                | 0% packet loss        | PASS   |
| SSH TCP/22              | Administrative connectivity | Successful            | PASS   |
| SSH key authentication  | No password prompt          | Successful            | PASS   |
| Golden Snapshot         | Recoverable baseline        | Created               | PASS   |

---

## 4. Engineering Lessons

The troubleshooting process generated the following operational lessons:

1. A failure during graphical initialization does not necessarily indicate operating system failure. Booting into multi-user.target provided an effective isolation mechanism for determining whether the base system remained operational.
2. Server architecture should be aligned with actual operational requirements. Removing unnecessary graphical components reduced complexity and attack surface.
3. System logs should be inspected before modifying network configuration blindly.
4. The ifupdown configuration parser provided direct evidence of the syntax error responsible for the networking failure.
5. WSL2 and VirtualBox can introduce multiple independent virtual networking layers that must be understood when designing laboratory connectivity.
6. Successful communication with the WSL2 default gateway does not prove reachability to every network accessible through the Windows host.
7. A static route can restore connectivity quickly, but persistence must be considered separately.
8. Network validation should proceed from lower layers toward higher-level services:

   * Routing
   * ICMP
   * TCP
   * SSH authentication
   * Remote command execution
9. Troubleshooting evidence is stronger when the initial failure and the successful post-remediation state are both documented.

---

## 5. Final Technical Assessment

LAB-00 successfully transitioned from an initially unstable installation state into a functional, segmented, server-oriented Cyber Range foundation.

The final environment provides:

* A Debian 12 Minimal headless server.
* Dual virtual network interfaces with distinct roles.
* A dedicated Host-Only management subnet.
* SSH-based administrative access.
* Ed25519 key-based authentication.
* Restricted SSH file permissions.
* Reduced graphical and service overhead.
* A known-good VirtualBox recovery baseline.
* Documented troubleshooting procedures and validation results.

The remaining operational consideration is the persistence of the WSL2 static route, which should be addressed or explicitly documented as a dependency before the Cyber Range becomes more complex.

---

# LAB-00: Registro Técnico de Troubleshooting e Análise de Causa Raiz

**Idioma: Português do Brasil (PT-BR)**

**Navegação do Documento:**

* [Versão em Inglês - EN](#lab-00-technical-troubleshooting-record-and-root-cause-analysis)
* [Versão em Português - PT-BR](#lab-00-registro-técnico-de-troubleshooting-e-análise-de-causa-raiz)

## 1. Visão Geral e Metodologia de Engenharia

Este documento formaliza as falhas operacionais, os procedimentos de diagnóstico, as hipóteses, as medidas corretivas e as validações técnicas executadas durante a implementação do LAB-00 (Fundação do Cyber Range).

O processo de troubleshooting seguiu uma metodologia estruturada de administração de sistemas e engenharia de confiabilidade:

* Identificação e Observação do Sintoma
* Inspeção de Telemetria e Logs
* Formulação da Hipótese de Causa Raiz
* Testes Controlados
* Implementação da Correção
* Reexecução dos Testes
* Registro das Evidências
* Validação Final

Cada incidente é documentado seguindo a seguinte sequência de engenharia:

Observação -> Hipótese -> Investigação -> Causa Raiz -> Remediação -> Validação

A distinção entre fatos observados e hipóteses técnicas é mantida ao longo do documento. As conclusões são classificadas como confirmadas somente quando sustentadas por evidência direta do sistema ou por validação bem-sucedida.

---

## 2. Registro de Incidentes: Detalhamento Técnico

### Incidente 01: Travamento do Ambiente Gráfico

* Severidade: Alta (Bloqueio do acesso gráfico interativo e da conclusão normal do boot)
* Subsistema: Stack Gráfico do Linux / Targets do systemd / Ambiente Gráfico Virtualizado do VirtualBox
* Fase de Ocorrência: Primeiro reboot pós-instalação.

#### Impacto

O ambiente gráfico impediu o acesso interativo normal ao servidor após a inicialização e estava em desacordo com a arquitetura headless planejada para o laboratório.

#### Sintoma

Após a tela de seleção do GNU GRUB, a máquina virtual permaneceu em uma tela preta com cursor piscante inerte.

A interface gráfica de login esperada do GDM3 não foi apresentada e a inicialização gráfica não foi concluída com sucesso.

#### Hipótese

A instalação do Debian continha componentes gráficos do GNOME que eram desnecessários para a arquitetura orientada a servidor definida para o laboratório.

Suspeitou-se que a falha estivesse relacionada à interação entre o stack gráfico e o ambiente de vídeo virtualizado, potencialmente envolvendo problemas de driver ou inicialização da sessão gráfica.

Essa hipótese não foi tratada como totalmente confirmada porque o objetivo principal da investigação era determinar se o kernel Linux e o ambiente operacional não gráfico permaneciam funcionais.

#### Investigação e Diagnóstico

1. A VM foi reinicializada de forma forçada pela interface de gerenciamento do VirtualBox.

2. A contagem automática de inicialização foi interrompida no menu do GNU GRUB.

3. Os parâmetros de inicialização do kernel foram editados por meio do editor do GNU GRUB.

4. Foi acrescentado o seguinte seletor de target do systemd:

   systemd.unit=multi-user.target

5. A inicialização foi retomada pelo comando de boot do GRUB.

#### Resultado do Diagnóstico

O sistema inicializou com sucesso no target multi-user e apresentou o console de texto TTY1.

Isso comprovou que:

* O kernel Linux estava operacional.
* O sistema operacional base permanecia funcional.
* A VM era capaz de concluir o processo de boot sem o target gráfico.
* A falha estava isolada no caminho de inicialização gráfica.

#### Causa Raiz

A causa operacional confirmada foi a presença de um ambiente gráfico desnecessário em uma máquina virtual orientada a servidor headless.

A interação específica entre driver gráfico, servidor gráfico e sessão gráfica não precisou ser isolada até o nível de componente individual, pois a decisão arquitetural foi remover completamente o stack gráfico.

#### Remediação e Ações Corretivas

As seguintes ações foram executadas a partir do console administrativo de texto:

1. O sistema foi configurado para utilizar o target multi-user como padrão persistente:

   systemctl set-default multi-user.target

2. O gerenciador gráfico GDM3 foi interrompido e desabilitado:

   systemctl stop gdm3
   systemctl disable gdm3

3. Os pacotes gráficos desnecessários foram removidos:

   apt-get purge -y gnome-core gdm3 task-gnome-desktop
   apt-get autoremove --purge -y
   apt-get clean

#### Validação

A VM foi reinicializada e inicializou diretamente no prompt de login em modo texto:

srv-linux-base login:

O consumo de memória observado após a conversão para o ambiente headless permaneceu abaixo de 180 MB durante o estado de validação.

#### Resultado Final

A falha gráfica foi eliminada e a VM foi alinhada à arquitetura orientada a servidor definida para o laboratório.

---

### Incidente 02: Falha na Inicialização do Gerenciador de Interfaces de Rede

* Severidade: Média (Bloqueio da configuração persistente do IP da rede Host-Only)
* Subsistema: systemd / ifupdown / /etc/network/interfaces
* Fase de Ocorrência: Configuração do baseline de rede da interface enp0s8.

#### Impacto

A falha impedia que a interface Host-Only recebesse sua configuração IPv4 estática persistente durante a inicialização do serviço.

#### Sintoma

A execução de:

systemctl restart networking

resultou em falha do serviço:

Job for networking.service failed because the control process exited with error code.

#### Hipótese

O arquivo /etc/network/interfaces continha uma declaração inválida ou malformada afetando a interface enp0s8.

#### Investigação e Diagnóstico

O journal do systemd referente ao serviço de rede foi consultado:

journalctl -xeu networking.service -n 20 --no-pager

O seguinte erro foi observado:

ifup: /etc/network/interfaces:11: unknown or no address type and no inherits keyword specified
ifup: couldn't read interfaces file /etc/network/interfaces

#### Causa Raiz

Foi confirmado um erro de sintaxe na linha 11 do arquivo /etc/network/interfaces.

A configuração malformada impedia o ifupdown de interpretar corretamente a declaração da interface de rede.

#### Remediação e Ações Corretivas

Primeiramente, uma configuração temporária foi aplicada em runtime utilizando iproute2:

ip link set enp0s8 up
ip addr flush dev enp0s8
ip addr add 192.168.56.10/24 dev enp0s8

Em seguida, a configuração persistente foi corrigida utilizando a declaração apropriada do ifupdown:

auto enp0s8
iface enp0s8 inet static
address 192.168.56.10
netmask 255.255.255.0

Nenhum default gateway foi atribuído à enp0s8 porque a interface NAT enp0s3 era responsável pela rota padrão de saída.

#### Validação

O serviço de rede foi reiniciado:

systemctl restart networking

Em seguida, o estado da interface e o endereço atribuído foram inspecionados:

ip -brief address show

Resultado esperado:

* enp0s8: UP
* Endereço IPv4: 192.168.56.10/24

A persistência foi posteriormente confirmada após a reinicialização do sistema.

#### Resultado Final

A interface Host-Only passou a ser inicializada corretamente com o endereço estático definido e permaneceu operacional após o reboot.

---

### Incidente 03: Falha de Roteamento entre WSL2 e a Sub-rede Host-Only do VirtualBox

* Severidade: Alta (Bloqueio do acesso administrativo remoto a partir do cliente WSL2)
* Subsistema: Tabela de Rotas do Linux no WSL2 / Rede do Host Windows / Rede Host-Only do VirtualBox
* Fase de Ocorrência: Primeiro teste de conectividade SSH remota.

#### Impacto

O ambiente WSL2 não conseguia alcançar a máquina virtual Debian através da rede de gerenciamento Host-Only, impedindo a administração por SSH.

#### Sintoma

Uma tentativa de conexão SSH originada no WSL2 retornou:

ssh: connect to host 192.168.56.10 port 22: No route to host

A validação inicial por ICMP apresentou perda total de pacotes:

3 packets transmitted, 0 received

#### Hipótese

O WSL2 opera em seu próprio ambiente de rede virtualizado e utiliza um gateway virtual para comunicação com o host Windows.

A tabela de rotas do WSL2 não possuía uma rota para a sub-rede Host-Only do VirtualBox:

192.168.56.0/24

Consequentemente, o tráfego destinado à rede de gerenciamento do Cyber Range não conseguia ser encaminhado corretamente a partir do ambiente WSL2.

#### Investigação e Diagnóstico

A tabela de rotas existente foi inspecionada:

ip route show

Em seguida, o gateway padrão foi testado:

ping -c 2 $(ip route show default | awk '{print $3}')

As respostas bem-sucedidas confirmaram que a comunicação entre o WSL2 e o gateway do host Windows estava operacional.

Isso restringiu a investigação ao roteamento além do gateway padrão do WSL2 em direção à rede Host-Only do VirtualBox.

#### Causa Raiz

A tabela de roteamento do Linux no WSL2 não possuía uma rota para a sub-rede 192.168.56.0/24.

A ausência dessa rota impedia o encaminhamento do tráfego em direção à interface do host Windows responsável por fornecer acesso à rede Host-Only do VirtualBox.

#### Remediação e Ações Corretivas

O gateway de trânsito do host Windows visível a partir do WSL2 foi identificado dinamicamente:

GW_WIN=$(ip route show default | awk '{print $3}')

Em seguida, foi adicionada a rota para a sub-rede de gerenciamento do Cyber Range:

sudo ip route add 192.168.56.0/24 via "$GW_WIN"

Essa operação criou o caminho de encaminhamento necessário do WSL2 para o host Windows e, posteriormente, para a rede Host-Only do VirtualBox.

#### Validação - Camada 3

A alcançabilidade ICMP foi testada novamente:

ping -c 3 192.168.56.10

Resultado:

* 0% de perda de pacotes.
* Latência de ida e volta inferior a 1 ms durante a validação observada.

Isso confirmou a alcançabilidade na Camada 3 entre o cliente WSL2 e a máquina virtual Debian.

#### Validação - Serviço de Aplicação SSH

Em seguida, foi testada a conexão administrativa SSH:

ssh srv-linux-base "whoami && hostname"

Resultado:

* Conexão TCP com a porta 22 estabelecida com sucesso.
* Autenticação SSH baseada em chave concluída com sucesso.
* Nenhuma solicitação interativa de senha apresentada.
* Execução remota de comando realizada com sucesso.
* Hostname remoto esperado retornado.

#### Resultado Final

O caminho de gerenciamento entre WSL2 e o Cyber Range tornou-se operacional, permitindo acesso administrativo seguro ao srv-linux-base por meio de SSH.

#### Consideração Operacional

A rota foi adicionada dinamicamente por meio do iproute2.

Portanto, caso nenhum mecanismo adicional de persistência tenha sido configurado, essa rota deve ser considerada uma dependência operacional que poderá precisar ser recriada após a reinicialização do WSL2 ou alterações relevantes na rede.

O mecanismo de persistência deverá ser documentado separadamente caso seja implementado em uma futura iteração do Cyber Range.

---

## 3. Resumo das Validações entre os Incidentes

Os três incidentes foram solucionados individualmente e posteriormente validados.

| Validação                          | Resultado Esperado             | Resultado Observado        | Status |
| ---------------------------------- | ------------------------------ | -------------------------- | ------ |
| Boot headless                      | Acesso direto ao TTY           | Sucesso                    | PASS   |
| Baseline de RAM                    | Redução do consumo do servidor | Abaixo de 180 MB observado | PASS   |
| Configuração enp0s8                | 192.168.56.10/24               | Endereço atribuído         | PASS   |
| Reinicialização do serviço de rede | Serviço sem falha              | Sucesso                    | PASS   |
| Rota WSL2                          | Rota para 192.168.56.0/24      | Rota operacional           | PASS   |
| ICMP                               | VM alcançável                  | 0% de perda                | PASS   |
| TCP/22                             | Conectividade administrativa   | Sucesso                    | PASS   |
| Autenticação SSH                   | Sem solicitação de senha       | Sucesso                    | PASS   |
| Golden Snapshot                    | Baseline recuperável           | Criado                     | PASS   |

---

## 4. Lições de Engenharia

O processo de troubleshooting produziu os seguintes aprendizados operacionais:

1. Uma falha durante a inicialização gráfica não significa necessariamente falha do sistema operacional. A inicialização com multi-user.target permitiu isolar o problema e determinar que o sistema base permanecia funcional.
2. A arquitetura do servidor deve estar alinhada aos requisitos operacionais. A remoção de componentes gráficos desnecessários reduziu complexidade e superfície de ataque.
3. Os logs do sistema devem ser analisados antes de realizar alterações cegas na configuração de rede.
4. O parser do ifupdown forneceu evidência direta do erro de sintaxe responsável pela falha do serviço de rede.
5. WSL2 e VirtualBox podem introduzir múltiplas camadas independentes de virtualização de rede, que precisam ser consideradas no projeto de conectividade do laboratório.
6. A comunicação bem-sucedida com o gateway padrão do WSL2 não comprova automaticamente a alcançabilidade de todas as redes acessíveis pelo host Windows.
7. Uma rota estática pode restaurar rapidamente a conectividade, mas sua persistência precisa ser tratada separadamente.
8. A validação de rede deve avançar das camadas inferiores para os serviços de nível superior:

   * Roteamento
   * ICMP
   * TCP
   * Autenticação SSH
   * Execução remota de comandos
9. Uma evidência de troubleshooting é mais forte quando documenta tanto o estado inicial de falha quanto o estado posterior à correção.

---

## 5. Avaliação Técnica Final

O LAB-00 foi convertido com sucesso de um estado inicial de instalação instável para uma fundação funcional, segmentada e orientada a servidores para o Cyber Range.

O ambiente final fornece:

* Servidor Debian 12 Minimal headless.
* Duas interfaces de rede virtuais com funções distintas.
* Sub-rede Host-Only dedicada ao gerenciamento.
* Acesso administrativo baseado em SSH.
* Autenticação por chaves Ed25519.
* Permissões restritas para arquivos SSH.
* Redução de componentes gráficos e overhead de serviços.
* Baseline de recuperação conhecido e funcional no VirtualBox.
* Procedimentos de troubleshooting e resultados de validação documentados.

A principal consideração operacional remanescente é a persistência da rota estática do WSL2, que deverá ser tratada ou explicitamente documentada como dependência antes que o Cyber Range evolua para uma arquitetura mais complexa.
