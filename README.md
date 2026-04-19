# 🌐 Network Lab — Simulação de Rede Corporativa

Simulação completa de uma rede corporativa com **4 sub-redes independentes**, roteamento estático entre elas e distribuição automática de IPs via **DHCP**. Desenvolvido no **Cisco Packet Tracer** como projeto prático de redes no 1º ano de Engenharia de Computação no IFSP.

---

## 📋 Visão geral

O projeto simula o ambiente de rede de uma empresa com quatro departamentos isolados que precisam se comunicar. Cada departamento tem seu próprio servidor DHCP e quatro estações de trabalho (PCs), todos interligados por dois roteadores centrais com roteamento estático configurado manualmente.

**Objetivos alcançados:**
- Todos os 16 hosts conseguem se comunicar entre si (ping de qualquer PC para qualquer outro)
- IPs distribuídos automaticamente via DHCP em cada sub-rede
- Roteamento inter-sub-redes funcionando via roteadores Cisco

---

## 🗺️ Topologia

```
                        [ Internet / WAN ]
                         /              \
                    [Router 0]      [Router 1]
                   /         \    /          \
              [Sw A]       link 10.0.0.0/30   [Sw B]
              /                               \
         [Srv A]                           [Srv B]
        /  |  |  \                        /  |  |  \
     PC1 PC2 PC3 PC4                  PC1 PC2 PC3 PC4

              [Sw C]                   [Sw D]
              (R0)                     (R1)
         [Srv C]                           [Srv D]
        /  |  |  \                        /  |  |  \
     PC1 PC2 PC3 PC4                  PC1 PC2 PC3 PC4
```

---

## 📊 Endereçamento IP

### Sub-redes dos departamentos

| Rede | Endereço       | Máscara         | Gateway     | Servidor (DHCP) | Range DHCP           |
|------|----------------|-----------------|-------------|-----------------|----------------------|
| A    | 192.168.0.0/24 | 255.255.255.0   | 192.168.0.1 | 192.168.0.10    | .101 – .200          |
| B    | 192.168.1.0/24 | 255.255.255.0   | 192.168.1.1 | 192.168.1.10    | .101 – .200          |
| C    | 192.168.2.0/24 | 255.255.255.0   | 192.168.2.1 | 192.168.2.10    | .101 – .200          |
| D    | 192.168.3.0/24 | 255.255.255.0   | 192.168.3.1 | 192.168.3.10    | .101 – .200          |

### Link entre roteadores (WAN)

| Interface  | IP           | Roteador |
|------------|--------------|----------|
| R0 Fa0/1   | 10.0.0.1/30  | Router 0 |
| R1 Fa0/1   | 10.0.0.2/30  | Router 1 |

### Interfaces dos roteadores

| Roteador | Interface | IP            | Sub-rede |
|----------|-----------|---------------|----------|
| Router 0 | Fa0/0     | 192.168.0.1   | Rede A   |
| Router 0 | Fa1/0     | 192.168.1.1   | Rede B   |
| Router 0 | Fa0/1     | 10.0.0.1      | WAN      |
| Router 1 | Fa0/0     | 192.168.2.1   | Rede C   |
| Router 1 | Fa1/0     | 192.168.3.1   | Rede D   |
| Router 1 | Fa0/1     | 10.0.0.2      | WAN      |

---

## ⚙️ Configurações

### Router 0

```
enable
configure terminal
hostname Router0

! Interface para Rede A
interface FastEthernet0/0
 ip address 192.168.0.1 255.255.255.0
 no shutdown

! Interface para Rede B
interface FastEthernet1/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

! Interface WAN (link com Router 1)
interface FastEthernet0/1
 ip address 10.0.0.1 255.255.255.252
 no shutdown

! Rotas estáticas para as redes do Router 1
ip route 192.168.2.0 255.255.255.0 10.0.0.2
ip route 192.168.3.0 255.255.255.0 10.0.0.2

end
write memory
```

### Router 1

```
enable
configure terminal
hostname Router1

! Interface para Rede C
interface FastEthernet0/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown

! Interface para Rede D
interface FastEthernet1/0
 ip address 192.168.3.1 255.255.255.0
 no shutdown

! Interface WAN (link com Router 0)
interface FastEthernet0/1
 ip address 10.0.0.2 255.255.255.252
 no shutdown

! Rotas estáticas para as redes do Router 0
ip route 192.168.0.0 255.255.255.0 10.0.0.1
ip route 192.168.1.0 255.255.255.0 10.0.0.1

end
write memory
```

### Servidor DHCP — exemplo Servidor A (repita nas demais redes ajustando os IPs)

No Packet Tracer, acesse o Servidor A → aba **Services** → **DHCP**:

| Campo            | Valor           |
|------------------|-----------------|
| Pool Name        | RedeA           |
| Default Gateway  | 192.168.0.1     |
| DNS Server       | 192.168.0.10    |
| Start IP Address | 192.168.0.101   |
| Subnet Mask      | 255.255.255.0   |
| Max Users        | 100             |

Ativar o serviço DHCP e clicar em **Add**.

### Configuração dos PCs

Em cada PC → Desktop → **IP Configuration** → selecionar **DHCP**.  
O PC vai receber automaticamente o IP, máscara, gateway e DNS do servidor da sua rede.

---

## 🧪 Testes realizados

Após montar a topologia completa, os seguintes testes foram executados com sucesso via **ping** no Packet Tracer:

| Origem       | Destino      | Resultado |
|--------------|--------------|-----------|
| PC A1 (.101) | PC B1 (.101) | ✅ OK      |
| PC A1 (.101) | PC C1 (.101) | ✅ OK      |
| PC A1 (.101) | PC D1 (.101) | ✅ OK      |
| PC B2 (.102) | PC D3 (.103) | ✅ OK      |
| PC C4 (.104) | PC A2 (.102) | ✅ OK      |

Comunicação total entre todas as sub-redes confirmada.

---

## 🛠️ Tecnologias e protocolos

- **Cisco Packet Tracer** — simulador de redes
- **TCP/IP** — pilha de protocolos de rede
- **DHCP** — atribuição dinâmica de endereços IP
- **Roteamento estático** — `ip route` manual nos roteadores Cisco
- **Switching** — Cisco Catalyst (camada 2)
- **Sub-redes /24 e /30** — segmentação e endereçamento

---

## 📁 Arquivos

```
network-lab/
├── README.md              ← este arquivo
├── topology.png           ← screenshot da topologia no Packet Tracer
├── network-lab.pkt        ← arquivo do Packet Tracer (abrir com PT 8.x+)
└── docs/
    └── ip-plan.md         ← plano de endereçamento detalhado
```

> **Como abrir:** Baixe o Cisco Packet Tracer gratuitamente em [netacad.com](https://www.netacad.com) e abra o arquivo `network-lab.pkt`.

---

## 👤 Autor

**[Seu Nome]** — Estudante de Engenharia de Computação, IFSP Piracicaba  
GitHub: [@Rezecon](https://github.com/Rezecon)  
LinkedIn: [linkedin.com/in/seu-perfil](https://linkedin.com/in/seu-perfil)

---

## 📚 O que aprendi

- Como planejar e documentar um esquema de endereçamento IP antes de implementar
- Diferença entre roteamento estático e dinâmico, e quando usar cada um
- Como o DHCP elimina a configuração manual de IPs em redes maiores
- O conceito de link WAN /30 entre roteadores (só 2 hosts utilizáveis, sem desperdício)
- Verificação e troubleshooting de conectividade com ping e traceroute
