# Scenariusze i walidacje

---

## 1. Wykrycie urządzeń sieciowych
**Narzędzie:** `nmap`, `arp-scan`  
**Cel:** Identyfikacja hostów w VLAN-ach.  

**Komenda przykładowa:**
```bash
nmap -sn 192.168.20.0/24
arp-scan --interface=eth0 192.168.20.0/24
```

**Wyniki:**

### KALI 1  
![Schemat](images/1.1-kali1.png)  
![Schemat](images/1.2-kali1.png)  

### KALI 2  
![Schemat](images/1.1-kali2.png)  
![Schemat](images/1.2-kali2.png)  

---

## 2. Wykrycie uruchomionych usług sieciowych
**Narzędzie:** `nmap`  
**Cel:** Wykrycie usług na serwerach DMZ.  

**Komenda:**
```bash
nmap -sV 192.168.40.10
```

**Wyniki:**

### KALI 1  
![Schemat](images/2.1-kali1.png)  
![Schemat](images/2.2-kali1.png)  

### KALI 2  
![Schemat](images/2-kali2.png)  

---

## 3. Atak man-in-the-middle – ARP spoofing
**Narzędzie:** `arpspoof`, `ettercap`  
**Cel:** Przechwycenie ruchu między hostem a bramą.  

**Komenda:**
```bash
arpspoof -i eth0 -t 192.168.20.10 192.168.20.254
```

**Wyniki:**

### KALI 1  
![Schemat](images/3-kali1.png)  

### KALI 2  
![Schemat](images/3-kali2.png)  

---

## 4. Atak man-in-the-middle – Ping flood
**Narzędzie:** `hping3`  
**Cel:** Zalanie hosta pakietami ICMP.  

**Komenda:**
```bash
hping3 --icmp --flood 192.168.20.10
```

**Wyniki:**

### KALI 1  
![Schemat](images/4-kali1.png)  

### KALI 2  
![Schemat](images/4-kali2.png)  

---

## 5. Atak odmowy usługi (DoS) – TCP SYN Flood
**Narzędzie:** `hping3`  
**Cel:** Wywołanie przeciążenia serwera.  

**Komenda:**
```bash
hping3 -S --flood -p 80 192.168.40.10
```

**Wyniki:**

### KALI 1  
![Schemat](images/5-kali1.png)  

### KALI 2  
![Schemat](images/5-kali2.png)  

---

## 6. Atak typu „Ping of Death”
**Narzędzie:** `ping`  
**Cel:** Wysyłanie nadmiarowych pakietów ICMP.  

**Komenda:**
```bash
ping -s 65500 192.168.20.10
```

**Wyniki:**

### KALI 1  
![Schemat](images/6-kali1.png)  

### KALI 2  
![Schemat](images/6-kali2.png)  

---

## 7. Wykrywanie podatności serwera przy użyciu Nessus
**Narzędzie:** `Nessus`  
**Cel:** Skanowanie podatności serwera DMZ.  

**Kroki:**
- Uruchom Nessus w Kali.
- Dodaj cel: `192.168.40.10`.
- Wykonaj pełny skan podatności.

---
