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
- 1 Access Point

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
| Fa0/9  | Access Point | 50 - Visitantes |
| Fa0/24 | Roteador | Trunk |

## Plano de endereçamento IP

| VLAN | Nome | Rede | Gateway |
|---|---|---|---|
| 10 | TI | 192.168.10.0/24 | 192.168.10.1 |
| 20 | RH | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Financeiro | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Impressoras | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Visitantes | 192.168.50.0/24 | 192.168.50.1 |

O endereçamento foi planejado utilizando redes /24, com um gateway próprio para cada VLAN.

O servidor DNS utiliza o endereço `192.168.10.2` e está conectado à VLAN 10.

## DHCP

O roteador Cisco 1941 foi configurado como servidor DHCP para distribuir automaticamente as configurações de rede aos dispositivos das VLANs.

Foram configurados pools DHCP para:

- VLAN 10 — TI
- VLAN 20 — RH
- VLAN 30 — Financeiro
- VLAN 50 — Visitantes

Cada pool fornece:

- Endereço IP
- Máscara de sub-rede
- Gateway padrão
- Servidor DNS

Nas redes que utilizam DHCP, os endereços de .1 a .16 foram reservados por meio de exclusões DHCP para evitar que fossem distribuídos automaticamente.

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
| 50 | VISITANTES | Dispositivos de visitantes |

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
| G0/0.50 | 50 - Visitantes | 192.168.50.1 |

A interface Fa0/24 do switch foi configurada como trunk para transportar as VLANs até o roteador.

Foram realizados testes de comunicação entre diferentes VLANs, confirmando o funcionamento do roteamento.

## Rede de visitantes

Foi criada uma rede Wi-Fi dedicada para dispositivos de visitantes, utilizando a VLAN 50.

A rede utiliza:

- SSID: `Visitantes`
- VLAN: 50
- Rede: `192.168.50.0/24`
- Gateway: `192.168.50.1`
- DHCP: habilitado

Um Access Point foi conectado à porta Fa0/9 do switch, configurada como porta de acesso da VLAN 50.

Um notebook foi utilizado para simular um dispositivo visitante e recebeu automaticamente o endereço `192.168.50.17` por meio do DHCP.

A conectividade com o gateway `192.168.50.1` foi validada com sucesso.

## Testes de conectividade

Foram realizados testes de conectividade para validar o funcionamento da infraestrutura.

### Conectividade entre VLANs

Foram realizados testes de comunicação entre dispositivos de diferentes VLANs utilizando o comando `ping`.

Os testes confirmaram o funcionamento do roteamento entre VLANs.

### Teste de DNS

Foi testada a resolução do nome `pc1.empresa.local` a partir de diferentes VLANs.

O nome foi corretamente resolvido para `192.168.10.17`, confirmando o funcionamento do serviço DNS.

### Teste da rede de visitantes

Foi utilizado um notebook para simular um dispositivo conectado à rede Wi-Fi de visitantes.

O dispositivo recebeu automaticamente:

- IP: `192.168.50.17`
- Máscara: `255.255.255.0`
- Gateway: `192.168.50.1`

O acesso ao gateway foi testado com `ping`, apresentando 0% de perda.

Também foi verificada a comunicação entre a VLAN 50 e a VLAN 10. O teste foi bem-sucedido, demonstrando que o roteamento entre as redes está ativo antes da implementação das políticas de restrição por ACL.

### Teste de conectividade com o servidor DNS

Foi testada a comunicação com o servidor DNS `192.168.10.2` a partir dos dispositivos da rede.

O servidor respondeu aos testes de conectividade, confirmando a comunicação com a VLAN 10.## Status do projeto

Até o momento, foram implementados e testados:

- Endereçamento IP
- DHCP
- DNS
- VLANs
- Segmentação de rede
- Trunk 802.1Q
- Router-on-a-Stick
- Roteamento entre VLANs
- Rede Wi-Fi para visitantes
- DHCP para a rede de visitantes
- Testes de conectividade

### Próximas etapas

- Implementar políticas de acesso utilizando ACLs
- Restringir o acesso da VLAN 50 às redes internas
- Permitir os serviços necessários para os visitantes
- Realizar novos testes de conectividade e segurança
