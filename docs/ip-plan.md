# Plano de Endereçamento IP — Network Lab

Documentação completa do esquema de endereçamento utilizado na simulação.

---

## Visão geral

| Sub-rede | Endereço       | Máscara       | Hosts utilizáveis | Finalidade       |
|----------|----------------|---------------|-------------------|------------------|
| Rede A   | 192.168.0.0/24 | 255.255.255.0 | 254               | Departamento A   |
| Rede B   | 192.168.1.0/24 | 255.255.255.0 | 254               | Departamento B   |
| Rede C   | 192.168.2.0/24 | 255.255.255.0 | 254               | Departamento C   |
| Rede D   | 192.168.3.0/24 | 255.255.255.0 | 254               | Departamento D   |
| WAN      | 10.0.0.0/30    | 255.255.255.252 | 2               | Link R0 ↔ R1    |

---

## Roteadores

| Dispositivo | Interface  | IP Address    | Sub-rede |
|-------------|------------|---------------|----------|
| Router 0    | Gb0/0      | 192.168.0.1   | Rede A   |
| Router 0    | Gb0/1      | 192.168.1.1   | Rede B   |
| Router 0    | Gb0/2      | 10.0.0.1      | WAN      |
| Router 1    | Gb0/0      | 192.168.2.1   | Rede C   |
| Router 1    | Gb0/1      | 192.168.3.1   | Rede D   |
| Router 1    | Gb0/2      | 10.0.0.2      | WAN      |

---

## Servidores (IP estático)

| Servidor   | IP Address    | Gateway     | Função       |
|------------|---------------|-------------|--------------|
| Servidor A | 192.168.0.10  | 192.168.0.1 | DHCP Rede A  |
| Servidor B | 192.168.1.10  | 192.168.1.1 | DHCP Rede B  |
| Servidor C | 192.168.2.10  | 192.168.2.1 | DHCP Rede C  |
| Servidor D | 192.168.3.10  | 192.168.3.1 | DHCP Rede D  |

---

## Pools DHCP

| Pool   | Start IP      | End IP        | Gateway     | DNS           |
|--------|---------------|---------------|-------------|---------------|
| RedeA  | 192.168.0.101 | 192.168.0.200 | 192.168.0.1 | 192.168.0.10  |
| RedeB  | 192.168.1.101 | 192.168.1.200 | 192.168.1.1 | 192.168.1.10  |
| RedeC  | 192.168.2.101 | 192.168.2.200 | 192.168.2.1 | 192.168.2.10  |
| RedeD  | 192.168.3.101 | 192.168.3.200 | 192.168.3.1 | 192.168.3.10  |

> IPs .1 a .100 reservados para infraestrutura (gateways, servidores, equipamentos).  
> IPs .101 a .200 distribuídos automaticamente via DHCP para os PCs.

---

## PCs (IP via DHCP)

| Dispositivo | Sub-rede | IP Atribuído (exemplo) | Gateway     |
|-------------|----------|------------------------|-------------|
| PC A1       | Rede A   | 192.168.0.101          | 192.168.0.1 |
| PC A2       | Rede A   | 192.168.0.102          | 192.168.0.1 |
| PC A3       | Rede A   | 192.168.0.103          | 192.168.0.1 |
| PC A4       | Rede A   | 192.168.0.104          | 192.168.0.1 |
| PC B1       | Rede B   | 192.168.1.101          | 192.168.1.1 |
| PC B2       | Rede B   | 192.168.1.102          | 192.168.1.1 |
| PC B3       | Rede B   | 192.168.1.103          | 192.168.1.1 |
| PC B4       | Rede B   | 192.168.1.104          | 192.168.1.1 |
| PC C1       | Rede C   | 192.168.2.101          | 192.168.2.1 |
| PC C2       | Rede C   | 192.168.2.102          | 192.168.2.1 |
| PC C3       | Rede C   | 192.168.2.103          | 192.168.2.1 |
| PC C4       | Rede C   | 192.168.2.104          | 192.168.2.1 |
| PC D1       | Rede D   | 192.168.3.101          | 192.168.3.1 |
| PC D2       | Rede D   | 192.168.3.102          | 192.168.3.1 |
| PC D3       | Rede D   | 192.168.3.103          | 192.168.3.1 |
| PC D4       | Rede D   | 192.168.3.104          | 192.168.3.1 |

---

## Rotas estáticas configuradas

### Router 0
```
ip route 192.168.2.0 255.255.255.0 10.0.0.2
ip route 192.168.3.0 255.255.255.0 10.0.0.2
```

### Router 1
```
ip route 192.168.0.0 255.255.255.0 10.0.0.1
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

---

## Por que /30 no link WAN?

A máscara `/30` (255.255.255.252) reserva apenas 2 endereços utilizáveis por sub-rede:

| Endereço   | Função              |
|------------|---------------------|
| 10.0.0.0   | Endereço de rede    |
| 10.0.0.1   | Router 0 (Fa0/1)    |
| 10.0.0.2   | Router 1 (Fa0/1)    |
| 10.0.0.3   | Broadcast           |

Usar /30 em links ponto a ponto é uma boa prática de redes — evita desperdiçar um bloco /24 inteiro para uma conexão que só precisa de 2 IPs.
