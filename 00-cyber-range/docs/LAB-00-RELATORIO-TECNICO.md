# LAB-00: Fundação do Cyber Range e Acesso Administrativo Seguro

## 1. Identificação do Laboratório
- Categoria: Ciberseguranca / Infraestrutura / Seguranca de Redes
- Objetivo: Construção da infraestrutura computacional isolada para experimentacao controlada e acesso administrativo seguro.
- Ambiente: Windows 11 Host, WSL2, Oracle VirtualBox 7, Debian 12 Minimal (Headless).
- Status: Concluido
- Data da Conclusao: 03/09/2026
- Versao: 1.0

## 2. Sumário Executivo
O LAB-00 estabeleceu a fundacao computacional do Cyber Range corporativo. O ambiente foi provisionado com Debian 12 sem interface grafica, minimizando o consumo de memoria RAM para patamares abaixo de 200 MB e reduzindo a superficie de ataque operacional do sistema operacional base. Foram implementadas duas interfaces de rede com atribuicoes logicas distintas e o canal administrativo seguro via SSH foi configurado no cliente local sob o subsistema WSL2 com chave assimetrica baseada na curva de Edwards Ed25519. Por fim, foi registrado o snapshot imutavel de baseline para viabilizar procedimentos de rollback deterministico em experimentos subsequentes.

## 3. Escopo e Topologia Resumida
O escopo contemplou o provisionamento da primeira maquina virtual servidora (srv-linux-base), segmentacao da conectividade e eliminacao de pacotes e servicos desnecessarios de interface visual. Os detalhes completos de diagramacao e isolamento de fronteiras estao documentados nos arquivos complementares docs/arquitetura.md e docs/configuracao-rede.md.
- Interface enp0s3: Modo NAT no Hypervisor para saida controlada a internet e atualizacao de repositorios.
- Interface enp0s8: Modo Host-Only no Hypervisor com endereco estatico 192.168.56.10/24 dedicado ao plano de gerencia do Cyber Range.

## 4. Configuracao do Acesso Administrativo Seguro
O acesso a gerencia do ativo foi estruturado no cliente OpenSSH do WSL2. A autenticacao interativa por senha foi substituida por par de chaves assimetricas dedicadas sem compartilhamento com o host Windows:
- Algoritmo Criptografico: Ed25519 (256 bits, imune a ataques de temporizacao).
- Permissoes POSIX no Cliente: 600 para chave privada e arquivo de configuracao; 644 para chave publica.
- Resolucao de Host: Definido alias srv-linux-base apontando para o IP 192.168.56.10 na porta padrao 22/TCP com a diretiva IdentitiesOnly ativada.

## 5. Validacao Tecnica e Registro de Evidencias

### Evidencia 01: Configuracao e Chaves do Cliente SSH
- Arquivo Anexo: evidence/01-ssh-client-config.png
- O que comprova: A presenca do bloco Host no arquivo de configuracao SSH, a geracao da chave criptografica Ed25519 dedicada e o controle estrito de permissoes em conformidade com o padrao de operacoes Unix.
- Competencia Demonstrada: Gerenciamento de credenciais criptograficas e configuracao de clientes SSH para administracao remota.

### Evidencia 02: Conectividade L3/L7 e Autenticacao Direta
- Arquivo Anexo: evidence/02-ssh-connectivity-validation.png
- O que comprova: A alcancabilidade na Camada 3 (zero perdas em requisicoes ICMP Echo), comunicacao bidirecional entre WSL e VM pela interface Host-Only, resolucao do alias e execucao de comandos administrativos remotos sem solicitacao de senha.
- Competencia Demonstrada: Troubleshooting e validacao de redes TCP/IP, administracao remota segura e operacao de terminais Linux.

### Evidencia 03: Baseline Operacional e Golden Snapshot
- Arquivo Anexo: evidence/03-golden-snapshot.png
- O que comprova: O registro formal do snapshot imutavel GOLDEN_IMAGE_BASE_CLI no VirtualBox, garantindo idempotencia, capacidade de recuperacao deterministica de desastres e preservacao do estado limpo do ambiente.
- Competencia Demonstrada: Gestao de infraestrutura virtualizada, criacao de baselines de seguranca e engenharia de resiliencia operacional.

## 6. Sintese do Troubleshooting Executado
Durante a execucao do laboratorio foram identificados e solucionados tres pontos de falha que se tornaram aprendizados operacionais:
1. Conflito de GPU e Trava do Servidor Grafico: O carregamento padrao do GNOME causou travamento visual. Resolvido mediante alteracao temporaria de parametros no GNU GRUB (systemd.unit=multi-user.target), remocao definitiva dos pacotes graficos com purge e direcionamento do systemd para multi-user.target.
2. Falha de Sintaxe no Arquivo de Interfaces: O servico networking.service falhou durante a inicializacao da interface enp0s8. A causa foi isolada via journalctl e corrigida com a declaracao padronizada do bloco static sem diretiva de default gateway.
3. Roteamento Inter-Ambientes WSL2 para Host-Only: O cliente SSH no WSL apresentou erro de rota inexistente para a faixa 192.168.56.0/24 em funcao do isolamento NAT do subsistema. Resolvido mediante adicao de rota estatica no kernel do Linux direcionando o trafego para o gateway do host Windows. Os logs detalhados estao disponiveis em docs/troubleshooting.md.

## 7. Competencias Demonstradas
- Administracao de Servidores Linux (Debian Minimal, gerenciamento de pacotes, systemd, configuracao POSIX).
- Engenharia de Redes e Protocolos (Dual-NIC, segmentacao NAT vs Host-Only, sub-redes IPv4, roteamento estatico e ICMP).
- Seguranca Ofensiva e Defensiva Basica (Hardening com reducao de superficie de ataque, autenticacao por chave Ed25519 e minimizacao de servicos).
- Infraestrutura e SecOps (Virtualizacao de Tipo 2, governanca de snapshots para testes destrutivos e idempotencia de baselines).

## 8. Proximos Passos
Com o ambiente base congelado e operacional, o proximo ciclo de engenharia consistira na execucao do LAB-01, focado em analise de trafego de rede, disseccao do handshake TCP de 3 vias e captura de pacotes via tcpdump e Wireshark.
