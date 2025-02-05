# VPN_WHITEFLY
VPN project on linux


 OpenVPN - Bezpieczny serwer VPN dla małej firmy

 Opis projektu
Projekt OpenVPN pozwala na konfigurację bezpiecznego serwera VPN, który umożliwia użytkownikom łączenie się z prywatną siecią zdalnie, przy zachowaniu pełnego bezpieczeństwa.

**Funkcje**
- Szyfrowane połączenie VPN (AES-256-CBC)
- Autoryzacja klientów przez certyfikaty TLS
- Możliwość zdalnego dostępu do zasobów firmowych
- Obsługa logów połączeń
- Prosty w użyciu klient VPN dla Windows/Linux



**Instalacja i konfiguracja**

1. Instalacja OpenVPN na serwerze (Linux)

sudo apt update && sudo apt install openvpn easy-rsa -y

2. Inicjalizacja PKI i generowanie kluczy

cd /etc/openvpn/server/easy-rsa
sudo ./easyrsa init-pki
sudo ./easyrsa build-ca
sudo ./easyrsa build-server-full server nopass
sudo openvpn --genkey --secret ta.key

3. Konfiguracja serwera

Edytuj plik /etc/openvpn/server.conf:

port 1194
proto udp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh.pem
auth SHA256
cipher AES-256-CBC
tls-auth ta.key 0

Uruchom serwer VPN:

sudo systemctl enable openvpn-server@server
sudo systemctl start openvpn-server@server


Konfiguracja klienta VPN

Plik client.ovpn powinien wyglądać tak:

client
dev tun
proto udp
remote YOUR_SERVER_IP 1194
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
auth SHA256
cipher AES-256-CBC
compress lz4-v2
tls-auth ta.key 1
ca ca.crt
cert client.crt
key client.key
verb 3

Zmień YOUR_SERVER_IP na adres IP serwera VPN

Uruchom klienta na Windows/Linux:

sudo openvpn --config client.ovpn

Sprawdź adres IP:

curl ifconfig.me


----------Schemat architektury VPN dla małej firmy--------------


+------------+      +-------------+       +------------+
| Klient VPN | ---> | Serwer VPN  | --->  | Sieć Firmy |
+------------+      +-------------+       +------------+
       │                     │                      │
       │                     │                      │
       ▼                     ▼                      ▼
+------------+      +-------------+       +------------+
|  Laptop   | ---> | Firewall     | --->  |  Serwer    |
|  Zdalny   |      | Firmowy      |       |  Plików   |
+------------+      +-------------+       +------------+


Słabe strony i sposoby zabezpieczenia
Brak dwuskładnikowego uwierzytelnienia
Rozwiązanie: Wdrożenie TOTP (Time-based One-Time Password) np. Google Authenticator.

Ataki brute-force na połączenie VPN
Rozwiązanie: Blokowanie prób logowania po kilku nieudanych próbach (fail2ban).

Przejęcie certyfikatów klientów
Rozwiązanie: Regularna rotacja certyfikatów i natychmiastowa blokada skompromitowanych kluczy.

Nieaktualne oprogramowanie
Rozwiązanie: Regularne aktualizacje OpenVPN i systemu operacyjnego.


Ten projekt VPN zapewnia bezpieczny dostęp do sieci firmowej, wykorzystując OpenVPN, szyfrowanie AES-256-CBC i autoryzację certyfikatową. Dzięki zastosowaniu protokołu SOCKS5 oraz logowania użytkowników możemy dodatkowo zwiększyć bezpieczeństwo.
