
# Dokumentacja projektu BSiSK – (OSPF + HSRP + pfSense)

> **Autorzy:**  
> Kacper Żbikowski  
> Michał Śledź  
> Oskar Trzeciak  

> **Opis:**  
> Projekt sieci korporacyjnej/kampusowej w GNS3 z redundancją L3 (OSPF, HSRP), segmentacją L2 (VLAN), oraz wyjściem do Internetu przez pfSense. Repozytorium zawiera konfiguracje urządzeń (R1–R5, S1–S2), projekt GNS3, testy bezpieczeństwa oraz diagramy.

---

![Schemat](images/scheamt.png)

---

## 1. Informacje ogólne

Projekt zakłada:
- Podział sieci na VLAN-y (ADM, USERS, DMZ, KALI, TRANSIT, MGMT).
- Dynamiczny routing OSPF w jednej arei (0.0.0.10).
- Redundancję bram w VLAN-ach przy użyciu HSRP.
- Polityki bezpieczeństwa oparte na ACL.
- Wyjście do Internetu przez pfSense (NAT + firewall).
- Testy penetracyjne w wydzielonym VLAN (KALI).

---

## 2. Architektura

### 2.1. Warstwa L3
- **Routing:** OSPF area `0.0.0.10` na R1–R5 i pfSense.
- **HSRP – redundancja bramy:**
  - VLAN10 (ADM): VIP `192.168.10.254` – Active: R1, Standby: R3.
  - VLAN20 (USERS): VIP `192.168.20.254` – Active: R1, Standby: R3.
  - VLAN40 (DMZ): VIP `192.168.40.254` – Active: R2, Standby: R5.
  - VLAN50 (KALI): VIP `192.168.50.254` – Active: R2, Standby: R5.
- **pfSense:**  
  - LAN: `192.168.30.1/24` (VLAN30, OSPF)  
  - WAN: `172.20.100.2` (NAT + Internet)  
  - Originate default route do OSPF.

### 2.2. Warstwa L2
- **S1:** Trunki na E0/0, E0/1; porty dostępowe E0/3 (VLAN10), E0/2 (VLAN20).
- **S2:** Trunki na E0/0, E0/1; porty dostępowe E0/3 (VLAN30/pfSense), E1/0 (VLAN40/DMZ), E0/2 (VLAN50/KALI).
- **Native VLAN:** 99 (jawnie ustawiony na trunkach; ograniczone allowed VLAN).

---

## 3. VLAN-y i adresacja

| VLAN | Nazwa       | Sieć              | Brama (HSRP VIP)     |
|------|------------|-------------------|----------------------|
| 10   | ADM        | 192.168.10.0/24  | 192.168.10.254       |
| 20   | USERS      | 192.168.20.0/24  | 192.168.20.254       |
| 30   | TRANSIT    | 192.168.30.0/24  | pfSense: 192.168.30.1|
| 40   | DMZ        | 192.168.40.0/24  | 192.168.40.254       |
| 50   | KALI       | 192.168.50.0/24  | 192.168.50.254       |
| 100  | MGMT       | 192.168.100.0/24 | Dostęp tylko dla ADM |

---

## 4. Urządzenia końcowe – adresacja

| Urządzenie      | VLAN | Adres IP         |
|-----------------|------|------------------|
| Administrator   | 10   | 192.168.10.10    |
| PC-01           | 20   | 192.168.20.10    |
| PC-02           | 20   | 192.168.20.11    |
| PC-03           | 20   | 192.168.20.12    |
| KALI 1          | 50   | 192.168.50.10    |
| Web Server      | 40   | 192.168.40.10    |
| Metasploit      | 40   | 192.168.40.11    |

---

## 5. System adresowania P2P

Adresacja punkt–punkt (P2P) między routerami:
- Schemat: `10.AB.0.x/30` (AB = numery routerów)
- Przykład: R1–R2 → `10.12.0.1/30` (R1), `10.12.0.2/30` (R2)

---

## 6. Routing
- OSPF area `0.0.0.10` dla wszystkich routerów i pfSense.
- pfSense rozgłasza trasę domyślną (default route).
- Routery rozgłaszają swoje VLAN-y i P2P.

---

## 7. Bezpieczeństwo
- ADM: pełny dostęp do wszystkich VLAN-ów.
- USERS, DMZ, KALI: dostęp tylko do Internetu.
- MGMT: dostęp wyłącznie dla ADM.
- ACL blokują ruch KALI → ADM/USERS.
- pfSense: NAT + firewall rules.
- Monitorowanie ruchu w DMZ i KALI.

---

## 8. Struktura repozytorium
```text
.
├── README.md
├── SECURITY_TESTS.md
├── configs/
│   ├── R1_configs_i2_startup-config.cfg
│   ├── R2_configs_i1_startup-config.cfg
│   ├── R3_configs_i3_startup-config.cfg
│   ├── R4_configs_i4_startup-config.cfg
│   ├── R5_configs_i5_startup-config.cfg
│   ├── S1_startup-config.cfg
│   └── S2_startup-config.cfg
├── gns3/
│   └── bsisk-bezpieczenstwo.gns3project
└── images/
       ├── schemat.png
```

---

## 9. Testy bezpieczeństwa
Szczegóły w pliku SECURITY_TESTS.md.

---

## 10. Troubleshooting

- `show standby brief `– HSRP status.
- `show ip ospf neighbor` – sąsiedzi OSPF.
- `show vlan brief` / `show interfaces trunk` – VLAN i trunk.
- **pfSense**: logi firewall, OSPF neighbors.

---

## 11. Licencja
Projekt na licencji MIT.
