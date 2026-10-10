<div align="center">

# 🏢 Enterprise Network Infrastructure Simulation
### Projeto de Infraestrutura, Subnetting CIDR /27 e Segmentação por VLANs

![Status](https://img.shields.io/badge/Status-Concluído-238636?style=for-the-badge&logo=checkmarx&logoColor=white)
![Cisco](https://img.shields.io/badge/Cisco_Packet_Tracer-7.x%20|%208.x-2ea44f?style=for-the-badge&logo=cisco&logoColor=white)
![Network](https://img.shields.io/badge/Redes-VLAN%20%26%20Subnetting-00875a?style=for-the-badge&logo=wireshark&logoColor=white)
![License](https://img.shields.io/badge/Licença-MIT-1b5e20?style=for-the-badge)

</div>

---

## 📌 Visão Geral do Projeto

Este projeto consiste no planejamento e implementação da arquitetura de rede local (LAN) para a empresa fictícia **Super Tech**, dimensionada para suportar operações corporativas departamentais com alta segurança, controle de broadcast e otimização do espaço de endereçamento IPv4.

O projeto foi modelado e validado no ambiente de simulação **Cisco Packet Tracer**, contemplando:
- Topologia física e lógica em estrela;
- Subnetting de rede Classe C com máscara customizada `/27`;
- Segmentação departamental e divisão de domínios de broadcast via **VLANs**;
- Uso de switches gerenciáveis **Cisco Catalyst 2950-24**;
- Política mista de endereçamento: **IPs Estáticos** para setores críticos e servidores, e **DHCP Dinâmico** para estações operacionais.

---

## 🏢 Cenário Corporativo & Requisitos

A empresa é composta por 4 departamentos estratégicos, totalizando **96 dispositivos finais**:

| Departamento | Estações de Trabalho | Servidores Dedicados | Impressoras de Rede | Total de Hosts | Política de IP |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Engenharia** | 20 | 2 | 2 | 24 hosts | Estático |
| **TI Interno** | 20 | 2 | 2 | 24 hosts | Estático |
| **Compras** | 20 | 2 | 2 | 24 hosts | Dinâmico (DHCP) |
| **Infraestrutura** | 20 | 2 | 2 | 24 hosts | Dinâmico (DHCP) |

---

## 📐 Engenharia de Endereçamento (Subnetting /27)

Para atender à demanda estrita de **24 hosts utilizáveis por departamento** sem desperdício de endereços, foi aplicada a técnica de subnetting em uma rede IPv4 de Classe C base (`192.168.10.0/24`):

- **Fórmula de Hosts:** $2^5 - 2 = 30$ hosts disponíveis (atende com folga aos 24 hosts exigidos).
- **Bits de Rede emprestados:** 3 bits ($2^3 = 8$ sub-redes possíveis).
- **Máscara de Sub-rede:** `255.255.255.224` (notação CIDR `/27`).
- **Incremento de Bloco:** 32 endereços por sub-rede.

### Tabela de Roteamento e Alocação de IPs

| Sub-rede | Departamento | ID da Sub-rede | 1º IP Válido | Último IP Válido | Endereço de Broadcast |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **Sub-rede 1** | Engenharia | `192.168.10.0/27` | `192.168.10.1` | `192.168.10.30` | `192.168.10.31` |
| **Sub-rede 2** | Compras | `192.168.10.32/27` | `192.168.10.33` | `192.168.10.62` | `192.168.10.63` |
| **Sub-rede 3** | TI Interno | `192.168.10.64/27` | `192.168.10.65` | `192.168.10.94` | `192.168.10.95` |
| **Sub-rede 4** | Infraestrutura | `192.168.10.96/27` | `192.168.10.97` | `192.168.10.126` | `192.168.10.127` |

---

## 🔌 Arquitetura Lógica e Configuração de VLANs

Cada departamento é centralizado em um switch **Cisco Catalyst 2950-24**, com as 24 portas FastEthernet divididas fisicamente e logicamente em duas VLANs distintas de 12 portas para isolamento de tráfego:

```text
        +----------------------------------------------------+
        |         Switch Cisco Catalyst 2950-24              |
        +-------------------------+--------------------------+
        | Portas Fa0/1 até Fa0/12 | Portas Fa0/13 até Fa0/24 |
        |         VLAN 1          |          VLAN 2          |
        +-------------------------+--------------------------+
        |  10 Estações            |  10 Estações             |
        |  01 Impressora          |  01 Impressora           |
        |  01 Servidor            |  01 Servidor             |
        +-------------------------+--------------------------+
```

### Script de Configuração Cisco IOS (Exemplo de Comutação)

```bash
# Entrando no modo de configuração global
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW-DEPT-01

# Configuração da VLAN 1 (Portas 1 a 12)
Switch(config)# interface range fastEthernet 0/1 - 12
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 1
Switch(config-if-range)# no shutdown
Switch(config-if-range)# exit

# Criação e Configuração da VLAN 2 (Portas 13 a 24)
Switch(config)# vlan 2
Switch(config-vlan)# name VLAN_SUBGRUPO_B
Switch(config-vlan)# exit

Switch(config)# interface range fastEthernet 0/13 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 2
Switch(config-if-range)# no shutdown
Switch(config-if-range)# exit

# Salvando as configurações na NVRAM
Switch# copy running-config startup-config
```

---

## 🛠️ Tecnologias e Habilidades Aplicadas

- **Simulação de Redes:** Cisco Packet Tracer.
- **Protocolos & Serviços:** IPv4, TCP/UDP, ICMP (Ping), ARP, DHCP Server/Client, VLAN (IEEE 802.1Q).
- **Hardwares de Rede:** Switches Cisco Catalyst 2950-24, Patch Panels lógicos, Estações Desktop e Servidores de rede.
- **Diagnóstico e Testes:** Resolução de conflitos de IP, verificação de convergência de enlaces, testes de latência e comunicação ICMP dentro e fora do domínio de broadcast.

---

## 🚀 Como Executar o Projeto

1. Baixe e instale o [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (versão 7.x ou superior).
2. Clone este repositório:
   ```bash
   git clone [https://github.com/Weslley-Ribeiro/enterprise-network-infrastructure.git](https://github.com/Weslley-Ribeiro/enterprise-network-infrastructure.git)
   ```
3. Abra o arquivo de simulação corporativa localizado na pasta raiz ou em `/topology`:
   - Abra o Cisco Packet Tracer e navegue em `File > Open > super_tech_topology.pkt`.
4. Verifique a tabela de roteamento e realize testes de ping entre as estações e servidores correspondentes.

---

## 📄 Documentação Complementar

Os relatórios técnicos, cálculos matemáticos das sub-redes e evidências de testes práticos de homologação estão documentados e disponíveis no diretório [`/docs`](./docs).
