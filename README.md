
---

# 🧠 Documentação dos Cenários de Rede (R1, R2, R3)

## 📌 Cenário 1

### 🔵 R1 (2 interfaces - rede local e conexão com R2)

```bash
# /etc/network/interfaces.d/r1
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.1.1/24

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.0.1/30
```

### 🔵 R2 (3 interfaces - conecta R1 e R3)

```bash
# /etc/network/interfaces.d/r2
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.2/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.2.1/24

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.5/30
```

### 🔵 R3 (2 interfaces - rede local e conexão com R2)

```bash
# /etc/network/interfaces.d/r3
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.6/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.3.1/24

# Rotas estáticas
post-up ip route add 192.168.1.0/24 via 192.168.0.5 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---

## 📌 Cenário 2

### 🔵 R1 (3 interfaces - rede local + conexões R2 e R3)

```bash
# /etc/network/interfaces.d/r1
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.1.1/24

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.0.1/30

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.9/30
```

### 🔵 R2 (3 interfaces - conecta R1 e R3)

```bash
# /etc/network/interfaces.d/r2
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.2/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.2.1/24

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.5/30

# Rotas
post-up ip route add 192.168.1.0/24 via 192.168.0.1 src 192.168.2.1
post-up ip route add 192.168.3.0/24 via 192.168.0.6 src 192.168.2.1
```

### 🔵 R3 (3 interfaces - conecta R1 e R2)

```bash
# /etc/network/interfaces.d/r3
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.6/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.3.1/24

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.10/30

# Rotas
post-up ip route add 192.168.1.0/24 via 192.168.0.9 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---

## 📌 Cenário 3

### 🔵 R1 (3 interfaces - rede local + R2 + R3)

```bash
# /etc/network/interfaces.d/r1
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.1.1/24

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.0.1/30

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.9/30
```

### 🔵 R2 (4 interfaces - interna + R1 + R3 + NAT via DHCP)

```bash
# /etc/network/interfaces.d/r2
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.2/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.2.1/24

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.5/30

allow-hotplug enp0s10
iface enp0s10 inet dhcp

# Rotas
post-up ip route add 192.168.1.0/24 via 192.168.0.1 src 192.168.2.1
post-up ip route add 192.168.3.0/24 via 192.168.0.6 src 192.168.2.1
```

### 🔵 R3 (3 interfaces - interna + R2 + R1)

```bash
# /etc/network/interfaces.d/r3
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.0.6/30

allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.3.1/24

allow-hotplug enp0s9
iface enp0s9 inet static
    address 192.168.0.10/30

# Rotas
post-up ip route add 192.168.1.0/24 via 192.168.0.9 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---

## 🔧 Habilitar o roteamento IP

Em todos os roteadores:

```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```

---

## 💡 Observações finais

* Sempre verifique as rotas com `ip route show`.
* Para testar conectividade entre redes, use `ping` e `traceroute`.
* Use `tcpdump` se necessário para depuração.
* O NAT pode ser configurado em R2 com:

```bash
iptables -t nat -A POSTROUTING -o enp0s10 -j MASQUERADE
```

---


---

## ✅ CENÁRIO 1 – Rotas completas

### 🔵 R1

* Conhece:

  * 192.168.1.0/24 (direta)
  * 192.168.0.0/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.2.0/24 via 192.168.0.2 src 192.168.1.1
post-up ip route add 192.168.3.0/24 via 192.168.0.2 src 192.168.1.1
```

### 🔵 R2

* Conhece:

  * 192.168.0.2/30 (direta)
  * 192.168.2.0/24 (direta)
  * 192.168.0.5/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.1 src 192.168.2.1
post-up ip route add 192.168.3.0/24 via 192.168.0.6 src 192.168.2.1
```

### 🔵 R3

* Conhece:

  * 192.168.0.6/30 (direta)
  * 192.168.3.0/24 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.5 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---

## ✅ CENÁRIO 2 – Rotas completas

### 🔵 R1

* Conhece:

  * 192.168.1.0/24 (direta)
  * 192.168.0.0/30 (para R2) (direta)
  * 192.168.0.8/30 (para R3) (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.2.0/24 via 192.168.0.2 src 192.168.1.1
post-up ip route add 192.168.3.0/24 via 192.168.0.10 src 192.168.1.1
```

### 🔵 R2

* Conhece:

  * 192.168.0.2/30 (direta)
  * 192.168.2.0/24 (direta)
  * 192.168.0.5/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.1 src 192.168.2.1
post-up ip route add 192.168.3.0/24 via 192.168.0.6 src 192.168.2.1
```

### 🔵 R3

* Conhece:

  * 192.168.0.6/30 (direta)
  * 192.168.3.0/24 (direta)
  * 192.168.0.10/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.9 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---

## ✅ CENÁRIO 3 – Rotas completas

### 🔵 R1

* Conhece:

  * 192.168.1.0/24 (direta)
  * 192.168.0.0/30 (para R2) (direta)
  * 192.168.0.8/30 (para R3) (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.2.0/24 via 192.168.0.2 src 192.168.1.1
post-up ip route add 192.168.3.0/24 via 192.168.0.10 src 192.168.1.1
```

### 🔵 R2 (com NAT por DHCP em enp0s10)

* Conhece:

  * 192.168.0.2/30 (direta)
  * 192.168.2.0/24 (direta)
  * 192.168.0.5/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.1 src 192.168.2.1
post-up ip route add 192.168.3.0/24 via 192.168.0.6 src 192.168.2.1
```

### 🔵 R3

* Conhece:

  * 192.168.0.6/30 (direta)
  * 192.168.3.0/24 (direta)
  * 192.168.0.10/30 (direta)
* **Rotas necessárias:**

```bash
post-up ip route add 192.168.1.0/24 via 192.168.0.9 src 192.168.3.1
post-up ip route add 192.168.2.0/24 via 192.168.0.5 src 192.168.3.1
```

---
