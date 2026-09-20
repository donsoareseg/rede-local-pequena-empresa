# Rede Local de Pequena Empresa

Laboratório de infraestrutura de rede desenvolvido no Cisco Packet Tracer, com o objetivo de simular a implementação e configuração de uma rede para uma pequena empresa.

## Objetivo

Construir, configurar e testar uma infraestrutura de rede segmentada, aplicando conceitos de endereçamento IP, DHCP, DNS, VLANs, roteamento entre VLANs e controle de acesso por meio de ACLs.

## Topologia da rede

A infraestrutura é composta por:

- 1 roteador Cisco 1941
- 1 switch Cisco 2960
- 6 computadores
- 1 servidor DNS
- 1 impressora de rede

O roteador e o switch utilizam uma conexão trunk para permitir o transporte das VLANs entre os equipamentos.

### Distribuição das portas

| Porta do switch | Dispositivo | VLAN |
|---|---|---|
| Fa0/1 | PC1 | 10 - TI |
| Fa0/2 | PC2 | 10 - TI |
| Fa0/3 | PC3 | 20 - RH |
| Fa0/4 | PC4 | 20 - RH |
| Fa0/5 | PC5 | 30 - Financeiro |
| Fa0/6 | PC6 | 30 - Financeiro |
| Fa0/7 | Impressora | 40 - Impressoras |
| Fa0/8 | Servidor DNS | 10 - TI |
| Fa0/24 | Roteador | Trunk |

## Plano de endereçamento IP

| VLAN | Nome | Rede | Gateway |
|---|---|---|---|
| 10 | TI | 192.168.10.0/24 | 192.168.10.1 |
| 20 | RH | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Financeiro | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Impressoras | 192.168.40.0/24 | 192.168.40.1 |

O endereçamento foi planejado utilizando redes /24, com um gateway próprio para cada VLAN.

## DHCP

O roteador Cisco 1941 foi configurado como servidor DHCP para distribuir automaticamente as configurações de rede aos dispositivos das VLANs.

Foram configurados pools DHCP para:

- VLAN 10 — TI
- VLAN 20 — RH
- VLAN 30 — Financeiro

Cada pool fornece:

- Endereço IP
- Máscara de sub-rede
- Gateway padrão
- Servidor DNS

Os endereços de `.1` a `.16` de cada rede foram reservados por meio de exclusões DHCP para evitar que fossem distribuídos automaticamente.

## DNS

Foi configurado um servidor DNS na rede para permitir a resolução de nomes internos.

**Servidor DNS:**
- IP: `192.168.10.2`
- VLAN: 10 - TI

Foi criado o registro:

`pc1.empresa.local` → `192.168.10.17`

A resolução de nomes foi testada a partir de diferentes VLANs, confirmando o funcionamento do DNS em conjunto com o roteamento entre VLANs.

## VLANs e segmentação

A rede foi segmentada utilizando VLANs para separar logicamente os diferentes setores e recursos da empresa.

| VLAN | Nome | Finalidade |
|---|---|---|
| 10 | TI | Computadores e servidor de TI |
| 20 | RH | Computadores do setor de Recursos Humanos |
| 30 | FINANCEIRO | Computadores do setor Financeiro |
| 40 | IMPRESSORAS | Impressora de rede |

A segmentação permite separar os dispositivos em diferentes domínios de broadcast e organizar a infraestrutura de acordo com suas funções.

As VLANs foram configuradas como portas de acesso no switch, enquanto a interface conectada ao roteador foi configurada como trunk para transportar o tráfego das diferentes VLANs.

## Router-on-a-Stick

Foi utilizado o método Router-on-a-Stick para permitir o roteamento entre as diferentes VLANs utilizando uma única interface física do roteador.

A interface GigabitEthernet0/0 do roteador foi dividida em subinterfaces, cada uma associada a uma VLAN:

| Subinterface | VLAN | Gateway |
|---|---|---|
| G0/0.10 | 10 - TI | 192.168.10.1 |
| G0/0.20 | 20 - RH | 192.168.20.1 |
| G0/0.30 | 30 - Financeiro | 192.168.30.1 |
| G0/0.40 | 40 - Impressoras | 192.168.40.1 |

A interface Fa0/24 do switch foi configurada como trunk para transportar as VLANs até o roteador.

Foram realizados testes de comunicação entre diferentes VLANs, confirmando o funcionamento do roteamento.

### Teste de conectividade com o servidor DNS

Foi testada a comunicação entre os dispositivos da rede e o servidor DNS.

O servidor respondeu aos testes de conectividade, confirmando a comunicação com a VLAN 10 e o funcionamento da infraestrutura de rede.

## Status do projeto

Até o momento, foram implementados e testados:

- Endereçamento IP
- DHCP
- DNS
- VLANs
- Segmentação de rede
- Trunk 802.1Q
- Router-on-a-Stick
- Roteamento entre VLANs
- Testes de conectividade

### Próximas etapas

- Criar a VLAN 50 para visitantes
- Configurar DHCP para a rede de visitantes
- Implementar políticas de acesso utilizando ACLs
- Realizar novos testes de conectividade e segurança
O servidor DNS utiliza o endereço `192.168.10.2` e está conectado à VLAN 10.
