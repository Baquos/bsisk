# Dokumentacja projektu BSiSK

## 1\. Informacje ogólne

Projekt BSiSK zakłada budowę sieci korporacyjnej z podziałem na VLAN-y, routowaniem dynamicznym OSPF oraz wdrożeniem polityk bezpieczeństwa opartych na ACL. Sieć obejmuje segmenty administracyjne, użytkowników, DMZ, segment testowy oraz sieć zarządzania.

## 2\. Używane VLAN-y

| Vlan | Nazwa | Adres IP | Opis |
| --- | --- | --- | --- |
| 10  | ADM | 192.168.10.0/24 | Sieć administracyjna, dostęp do wszystkich VLAN-ów |
| 20  | USERS | 192.168.20.0/24 | Sieć użytkowników, dostęp do Internetu |
| 30  | INTERNET | 192.168.30.0/24 | Segment wyjścia do Internetu, brama NAT |
| 40  | DMZ | 192.168.40.0/24 | Strefa DMZ dla serwerów publicznych |
| 50  | KALI | 192.168.50.0/24 | Segment testowy / pentesting |
| 100 | MGMT | 192.168.100.0/24 | Sieć zarządzania urządzeniami sieciowymi, dostęp tylko dla ADM |

## 3\. System adresowania połączeń P2P

Adresacja punkt–punkt (P2P) między routerami opiera się na schemacie:

- Pierwszy oktet (10) – stały, oznacza adresację prywatną klasy B
- Drugi oktet (AB) – numer routerów łączonych, np. połączenie R2-R3: 10.23.0.0/30
- Trzeci oktet (0) – stały, dla zachowania spójności schematu
- Czwarty oktet – adresacja urządzeń w połączeniu

Przykład: Połączenie między R1 a R2: 10.12.0.1/30 (R1), 10.12.0.2/30 (R2)

## 4\. Kontrola dostępu (ACL)

ACL określają, które sieci mają dostęp do innych VLAN-ów:

|VLAN źródłowy| Dostęp do VLAN  |
| --- | --- | 
|ADM |Wszystkie VLAN-y  |
|USERS |INTERNET  |
|DMZ |INTERNET  |
|KALI |INTERNET  |
|MGMT |Tylko ADM|

## 5\. Routing

W sieci BSiSK zastosowano dynamiczny routing z użyciem OSPF:

- Area 0.0.0.0 – backbone
- Każdy router wewnętrzny rozgłasza swoje sieci VLAN i połączenia P2P
- OpnSense pełni rolę bramy Internetowej i dodatkowo rozgłasza trasę domyślną do wszystkich routerów (default route)

Schemat routingu:

- Routery między VLAN-ami: OSPF
- Router brzegowy (OpnSense): OSPF + default route
- Segment MGMT: statyczne lub OSPF z filtrowaniem (tylko dostęp ADM)

## 6\. Bezpieczeństwo

- Sieć ADM pełny dostęp do wszystkich zasobów
- Segment MGMT dostępny wyłącznie dla ADM, z ACL i uwierzytelnieniem
- Segmenty USERS, DMZ i KALI mają ograniczony dostęp do Internetu
- Firewall na OpnSense zarządza regułami NAT i dostępem między VLAN-ami

Dodatkowe środki:

- Monitorowanie ruchu i logowanie połączeń w DMZ i KALI
- Separacja fizyczna lub logiczna segmentów w switchach L3
- Możliwość wprowadzenia polityk QoS dla VLAN-ów krytycznych

## 7\. Dodatkowe uwagi

- VLAN 100 (MGMT) może być użyty do zarządzania wszystkimi routerami i urządzeniami sieciowymi
- VLAN 50 (KALI) służy do testów penetracyjnych i nie powinien mieć dostępu do krytycznych VLAN-ów
- Regularne aktualizacje OSPF i firewall rules