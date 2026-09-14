# Projeto de Redes 2026.2 — Novo Bloco do CIn/UFPE

Disciplina: CIN0143 — Introdução aos Sistemas Distribuídos e Redes de Computadores  
Grupo 5 / ID de Rede: 224

---

## 1. Objetivo

Projetar e implementar, no Cisco Packet Tracer, a infraestrutura de rede do
Novo Bloco do Centro de Informática da UFPE.

A solução deverá atender 111 hosts, utilizando apenas um roteador de borda para
roteamento interno e acesso à infraestrutura da RNP.

O projeto deverá contemplar:

- segmentação por VLAN;
- conectividade cabeada e wireless;
- VLSM;
- DHCP;
- Router on a Stick;
- roteamento entre VLANs;
- NAT;
- conectividade com a RNP/Internet;
- gerenciamento wireless através de WLC.

---

## 2. Blocos de Endereçamento

### Rede interna

Bloco disponibilizado para o Grupo 224:

192.168.224.0/24

Esse bloco será posteriormente subdividido através de VLSM.

### Enlace com a RNP

Rede:

2.2.2.0/30

RNP:

2.2.2.1/30

Roteador do Novo Bloco:

2.2.2.2/30

O enlace entre o roteador do Novo Bloco e a RNP será realizado utilizando
fibra óptica single-mode.

### DNS externo

200.200.200.2

---

## 3. Redes Lógicas

A infraestrutura será dividida em quatro VLANs.

| Rede | Hosts | Cabeados | Wireless | Infraestrutura adicional |
|---|---:|---:|---:|---|
| CIN_UFPE | 40 | 20 | 20 | 1 AP |
| GUESTS | 45 | 0 | 45 | 1 AP |
| GERÊNCIA | 10 | 10 | 0 | WLC |
| GRAD_SECRETO | 16 | 16 | 0 | — |
| **Total** | **111** | **46** | **65** | |

A rede GUESTS será exclusivamente wireless.

A rede CIN_UFPE será híbrida, possuindo 20 clientes cabeados e 20 clientes
wireless.

---

## 4. Equipamentos

| Equipamento | Modelo | Quantidade | Função |
|---|---|---:|---|
| Roteador de borda | Cisco 2911 | 1 | WAN, NAT e roteamento inter-VLAN |
| Switch Core | Cisco 3560-24PS | 1 | Distribuição central da rede |
| Switch de acesso | Cisco 2960-24TT | 2 | Conexão dos hosts cabeados |
| Wireless LAN Controller | WLC-3504 | 1 | Gerenciamento dos APs |
| Access Point | Cisco 3702i | 2 | Redes wireless CIN_UFPE e GUESTS |
| Hosts cabeados | PC-PT | 46 | Clientes Ethernet |
| Hosts wireless | Laptop-PT | 65 | Clientes Wi-Fi |

Não será utilizado Server-PT inicialmente.

O serviço DHCP será planejado posteriormente utilizando um dos equipamentos
permitidos pelo projeto.

---

## 5. Arquitetura Física

Topologia planejada:

                         RNP
                          |
                    Fibra Single Mode
                          |
                      R-BLOCO
                    Cisco 2911
                          |
                          | Trunk 802.1Q
                          |
                      SW-CORE
                    Cisco 3560
                   /      |      \
                  /       |       \
              Trunk    WLC-CIN    Trunk
                /                   \
       SW-ACESSO-01             SW-ACESSO-02
          2960                      2960
          /  \                      /  \
       Hosts AP                  Hosts AP
             CIN                      GUESTS

O roteador R-BLOCO será o único equipamento responsável pelo roteamento entre
as VLANs, através da estratégia Router on a Stick.

O Switch Core não realizará roteamento através de SVIs.

---

## 6. Distribuição dos Hosts Cabeados

### SW-ACESSO-01

| Portas | Rede | Quantidade |
|---|---|---:|
| Fa0/1–Fa0/16 | GRAD_SECRETO | 16 |
| Fa0/17–Fa0/24 | CIN_UFPE | 8 |
| Gi0/1 | Uplink para SW-CORE | 1 |
| Gi0/2 | AP | 1 |

Total de portas FastEthernet utilizadas: 24/24.

### SW-ACESSO-02

| Portas | Rede | Quantidade |
|---|---|---:|
| Fa0/1–Fa0/10 | GERÊNCIA | 10 |
| Fa0/11–Fa0/22 | CIN_UFPE | 12 |
| Fa0/23–Fa0/24 | Reserva física | 2 |
| Gi0/1 | Uplink para SW-CORE | 1 |
| Gi0/2 | AP | 1 |

Total de portas FastEthernet utilizadas: 22/24.

### Capacidade física

46 portas FastEthernet serão utilizadas para hosts cabeados.

48 portas FastEthernet estão disponíveis nos dois switches.

Portanto:

48 - 46 = 2 portas físicas livres

A margem de crescimento de 25% exigida pelo projeto aplica-se ao
endereçamento IP e não à quantidade de portas Ethernet disponíveis.

---

## 7. Infraestrutura Wireless

Serão utilizados dois Access Points.

### AP-CIN_UFPE

SSID:

CIN_UFPE

Atenderá:

20 clientes wireless.

O AP será conectado a uma porta pertencente à VLAN CIN_UFPE.

### AP-GUESTS

SSID:

GUESTS

Atenderá:

45 clientes wireless.

O AP será conectado a uma porta pertencente à VLAN GUESTS.

Cada AP propagará somente um SSID.

Os APs Cisco 3702i serão alimentados através de Power Adapter no Packet
Tracer, permitindo utilização das interfaces Gigabit disponíveis nos switches de
acesso.

---

## 8. Wireless LAN Controller

Será utilizado um:

WLC-3504

O WLC será conectado à infraestrutura central e receberá endereço pertencente
à rede GERÊNCIA.

Ele será responsável pelo gerenciamento da infraestrutura wireless.

---

## 9. Segmentação

Serão criadas quatro VLANs:

- CIN_UFPE
- GUESTS
- GERÊNCIA
- GRAD_SECRETO

Os enlaces entre:

SW-ACESSO-01 ↔ SW-CORE

SW-ACESSO-02 ↔ SW-CORE

SW-CORE ↔ R-BLOCO

serão enlaces trunk 802.1Q.

As portas destinadas aos dispositivos finais serão configuradas como portas
Access pertencentes às respectivas VLANs.

---

## 10. Roteamento

Será utilizada a estratégia Router on a Stick.

O R-BLOCO possuirá uma subinterface para cada VLAN.

Conceitualmente:

R-BLOCO
├── CIN_UFPE
├── GUESTS
├── GERÊNCIA
└── GRAD_SECRETO

Cada subinterface atuará como gateway da respectiva sub-rede.

O endereçamento das subinterfaces será definido após o cálculo de VLSM.

---

## 11. VLSM

O bloco:

192.168.224.0/24

será dividido considerando:

1. quantidade atual de hosts;
2. margem obrigatória de 25%;
3. minimização do desperdício de endereços;
4. reserva dos 10 primeiros endereços utilizáveis das redes CIN_UFPE e
   GRAD_SECRETO para serviços estáticos.

O cálculo completo será desenvolvido na etapa de Design de Endereçamento.

---

## 12. DHCP

O uso de DHCP será obrigatório para os clientes.

As pools serão criadas após a definição do plano VLSM.

Os endereços reservados para serviços estáticos serão excluídos das pools
correspondentes.

Todos os hosts utilizarão como DNS:

200.200.200.2

---

## 13. NAT e Conectividade Externa

O R-BLOCO utilizará:

2.2.2.2/30

como endereço externo.

Será configurado NAT para permitir a comunicação das redes privadas internas
com a infraestrutura externa através do endereço público fornecido pela RNP.

Somente as faixas internas efetivamente utilizadas deverão ser traduzidas.

---

## 14. Etapas do Projeto

### E1 — Design Físico e Topologia

- topologia física;
- equipamentos;
- enlaces;
- portas Access e Trunk;
- justificativa da quantidade de switches.

### E2 — Design de Endereçamento

- cálculo de VLSM;
- máscara;
- network;
- broadcast;
- intervalo utilizável;
- gateway;
- reservas.

### E3 — Segmentação

- criação das VLANs;
- configuração de portas Access;
- trunks;
- DHCP;
- wireless.

### E4 — Roteamento e Conectividade

- Router on a Stick;
- subinterfaces;
- roteamento;
- rota para a RNP;
- NAT;
- testes de conectividade.

### E5 — Consolidação

- validação completa;
- testes;
- documentação;
- arquivo final .pkt;
- relatório final.

---

## 15. Próximos Passos

1. Adicionar e conectar o roteador R-BLOCO à infraestrutura da RNP.

2. Finalizar a topologia física e a distribuição dos equipamentos.

3. Adicionar os hosts cabeados e wireless ao projeto.

4. Definir o plano de endereçamento utilizando VLSM sobre o bloco
   192.168.224.0/24.

5. Definir os gateways e os intervalos de endereços reservados para serviços
   estáticos.

6. Criar e configurar as VLANs CIN_UFPE, GUESTS, GERÊNCIA e GRAD_SECRETO.

7. Configurar as portas Access e os enlaces Trunk entre os switches e o
   roteador.

8. Configurar o serviço DHCP para todas as sub-redes.

9. Configurar o WLC, os Access Points e as WLANs CIN_UFPE e GUESTS.

10. Implementar o Router on a Stick no R-BLOCO.

11. Configurar a conectividade com a RNP e a rota necessária para acesso
    externo.

12. Configurar o NAT utilizando o endereço 2.2.2.2/30.

13. Validar a comunicação entre hosts de VLANs diferentes.

14. Validar o acesso dos hosts à rede externa e ao servidor DNS
    200.200.200.2.

15. Realizar testes de troubleshooting e documentar os resultados obtidos.

16. Consolidar as configurações e evidências no relatório final e no arquivo
    .pkt.
