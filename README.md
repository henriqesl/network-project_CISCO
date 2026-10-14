# 🌐 Arquitetura de Redes: Novo Bloco do CIn/UFPE

> **Projeto — Disciplina:** CIN0143 — Introdução aos Sistemas Distribuídos e Redes de Computadores  
> **Instituição:** Universidade Federal de Pernambuco (UFPE)  
> **Grupo 5 / ID de Rede:** 224

## 📌 Sobre o Projeto
Este repositório contém a simulação completa e a documentação da infraestrutura de redes desenvolvida para um hipotético Novo Bloco do Centro de Informática (CIn) da UFPE. O projeto foi integralmente desenhado, configurado e validado utilizando o **Cisco Packet Tracer**.

O objetivo principal foi atuar como analistas de infraestrutura para projetar uma rede segmentada, escalável e segura capaz de suportar mais de 100 dispositivos (cabeados e sem fio), garantindo o roteamento interno (Inter-VLAN) e a saída controlada para a infraestrutura da RNP (Internet).

### 🛠️ Tecnologias e Protocolos Implementados
* **Segmentação Lógica:** VLANs, Trunks (802.1Q) e portas de Acesso.
* **Redundância:** EtherChannel com protocolo LACP.
* **Endereçamento e Otimização:** IPv4, VLSM (Variable Length Subnet Mask) e Servidor DHCP (com Option 43).
* **Roteamento:** Router-on-a-Stick (ROAS) e Rota Estática Padrão.
* **Segurança e Borda:** NAT Overload (PAT) para acesso à internet.
* **Redes Sem Fio (WLAN):** Controladora WLC, Access Points Lightweight (CAPWAP), AP Groups e FlexConnect Local Switching.

---

## 🏗️ Arquitetura da Rede (Topologia Lógica e Física)

A topologia foi construída baseada no modelo hierárquico em **Estrela Estendida**, dividida em: Borda (WAN), Núcleo (Core) e Acesso.

### Segmentação (VLANs) e Capacidade
O endereçamento partiu do bloco principal `192.168.224.0/24`, que foi meticulosamente subdividido (VLSM) para atender à demanda atual com uma margem garantida de 25% para expansões futuras, minimizando o desperdício de IPs.

| VLAN ID | Rede | Hosts Alvos | Perfil de Conexão | Função Principal |
| :---: | :--- | :---: | :--- | :--- |
| **20** | `CIN_UFPE` | 40 | Híbrida (20 Cabeados / 20 Wi-Fi) | Acesso institucional padrão |
| **10** | `GUESTS` | 45 | Exclusivamente Wireless | Visitantes do centro |
| **40** | `GERÊNCIA` | 10 | Exclusivamente Cabeada | Administração e WLC |
| **30** | `GRAD_SECRETO` | 16 | Exclusivamente Cabeada | Laboratórios de graduação |
| | **Total** | **111** | **(46 Cabeados / 65 Wireless)** | |

---

## ⚙️ Principais Desafios de Configuração (Troubleshooting)

Durante a fase de testes e consolidação, lidamos com comportamentos realistas simulados pelo Packet Tracer, aplicando resolução de problemas em diferentes camadas:

1. **Roteamento Inter-VLAN (ROAS):** Todo o roteamento interno foi centralizado no roteador de borda (R-BLOCO). As subinterfaces lógicas assumiram o papel de *Default Gateway* para cada respectiva VLAN.
2. **Saturação de Rádio (Wireless):** Simulação de alta densidade na VLAN GUESTS (45 clientes). Observamos na prática a limitação de associação simultânea aos Access Points (CSMA/CA), comprovando a saturação realista de clientes em um único AP.
3. **Provisionamento de WLC e CAPWAP:** O tráfego dos APs (VLAN 10 e 20) perdia conectividade com os gateways lógicos ao passar pela controladora (VLAN 40). O isolamento foi resolvido através da ativação do recurso **FlexConnect Local Switching** nas WLANs, mantendo o controle centralizado na WLC (SSID/Senha), mas forçando os APs a entregarem os pacotes diretamente na VLAN local do switch.
4. **Isolamento de SSIDs:** Utilização de **AP Groups** customizados na WLC para garantir que cada equipamento de rádio propagasse apenas a rede da sua zona física, evitando poluição de espectro.

---

## 💻 Inventário de Equipamentos (Cisco)

* **01 Roteador de Borda** (Cisco 2911): Responsável pelo ROAS, NAT (via IP `2.2.2.2/30`) e enlace de fibra single-mode com a RNP.
* **01 Switch Core** (Cisco 3560): Distribuição central da rede local.
* **02 Switches de Acesso** (Cisco 2960): Concentradores de camada 2 para a conexão física dos hosts e APs.
* **01 Wireless LAN Controller** (WLC-3504): Gerenciamento centralizado das políticas sem fio.
* **02 Access Points** (Cisco 3702i): Rádios para distribuição do Wi-Fi corporativo.

---

## 🚀 Como testar este projeto

1. Faça o download e instale o **Cisco Packet Tracer**.
2. Clone este repositório ou baixe o arquivo de topologia final: `Projeto_Redes_NovoBloco_G5.pkt`.
3. Abra o arquivo `.pkt` no simulador.
4. Utilize o botão **Fast Forward Time (>>)** na barra inferior do Packet Tracer repetidas vezes para adiantar o processo de inicialização (Boot dos switches, convergência do Spanning Tree e descoberta da WLC via Option 43).
5. Valide as configurações abrindo o *Command Prompt* de qualquer PC da rede e executando testes de conectividade como: `ping 200.200.200.2` (Servidor DNS Externo).

---

## 👥 Equipe Analista (Grupo 5)
* [João Vitor](https://github.com/vitorlins0)
* [Henrique Lima](https://github.com/henriqesl)
* [Gabriel Nóbrega](https://github.com/vitorlins0)
* [Helington Willamy](https://github.com/helington)
* [Lucas Fernandes](https://github.com/LucasFernandesCS)
* [Luis Miguel](https://github.com/luismiguuel)

> Consulte o arquivo `GabrielNobrega_HenriqueLima_Helington_LucasFernandes_LuisMiguel_JoaoVitor.pdf` neste repositório para conferir o design detalhado de sub-redes, topologia e a documentação dos testes executados.