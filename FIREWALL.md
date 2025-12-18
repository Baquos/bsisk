# Konfiguracja Firewall

## Założenia

| FROM \ TO        | ADM (V10) | USERS (V20) | DMZ (V40) | KALI (V50) | INTERNET (V30) |
|-----------------|-----------|-------------|-----------|------------|----------------|
| **ADM (V10)**   | —         | ✔           | ✔         | ✔          | ✔              |
| **USERS (V20)** | ✖         | —           | ✔*        | ✖          | ✔              |
| **DMZ (V40)**   | ✖         | ✖           | —         | ✖          | ✔              |
| **KALI (V50)**  | ✖         | ✖           | ✖         | —          | ✔              |
| **INTERNET (V30)** | ✖      | ✖           | ✖         | ✖          | —              |


## Objaśnienie ✔*
- USERS → DMZ (✔*)
- ✔ ICMP (ping) do 192.168.40.10
- ✔ TCP 80, 443 do 192.168.40.10
- ✖ wszystko inne w DMZ


## Jak to czytać (ważne)
- Tabela pokazuje ruch inicjowany
- Odpowiedzi są dozwolone automatycznie (ZBF = stateful)
- Brak ptaszka = brak policy → DROP
- INTERNET nigdy nie inicjuje połączeń do środka


## Konfiguracja urządzeń

### R1 &R3
```cisco
ip access-list extended VLAN20-IN
 permit icmp 192.168.20.0 0.0.0.255 host 192.168.40.10 echo
 permit icmp host 192.168.40.10 192.168.20.0 0.0.0.255 echo-reply
 permit tcp 192.168.20.0 0.0.0.255 host 192.168.40.10 eq 80
 permit tcp 192.168.20.0 0.0.0.255 host 192.168.40.10 eq 443
 deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
 deny ip 192.168.20.0 0.0.0.255 192.168.50.0 0.0.0.255
 permit ip 192.168.20.0 0.0.0.255 any

interface GigabitEthernet6/0.20
 ip access-group VLAN20-IN in
```

### R2 & R5
```cisco
ip access-list extended VLAN40-IN
 deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
 deny ip 192.168.40.0 0.0.0.255 192.168.50.0 0.0.0.255
 permit ip 192.168.40.0 0.0.0.255 any

interface GigabitEthernet6/0.40
 ip access-group VLAN40-IN in

ip access-list extended VLAN50-IN
 deny ip 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.255
 deny ip 192.168.50.0 0.0.0.255 192.168.40.0 0.0.0.255
 permit ip 192.168.50.0 0.0.0.255 any

interface GigabitEthernet6/0.50
 ip access-group VLAN50-IN in

```

