# OpenVPN Setup Script
A simple bash script to easily set up and manage OpenVPN servers on unix-based systems (Arch Linux, Debian, Ubuntu, Raspberry Pi OS)

## Features
- One command installation
- Cross-platform
- Automatic firewall configuration
- Easy client management
- Mobile support
- Secure defaults
- Automatic network detection 
- PKI management

## Requirements
- Root access
- Active internet connection

## Installation
```bash
git clone https://github.com/bashcrumb/openvpn-setup.git
cd openvpn-setup
chmod +x setup.sh
```

## Usage
```bash
# install
sudo ./setup.sh init

# add clients
sudo ./setup.sh add-client laptop
sudo ./setup.sh add-client phone --mobile

# manage
sudo ./setup.sh status
sudo ./setup.sh list-clients
sudo ./setup.sh revoke-client old-device
```

## Initial Setup
The `init` command will:

* Detect your operating system and install packages
* Initialize PKI (public key infasctructure)
* Generate server certs
* Configure firewall rules
* Enable IP forwarding
* Start OpenVPN service

## Configuration
The server configuration is located at `/etc/openvpn/server.conf` and includes:

* Security: AES-256-GCM cipher, SHA256 auth, TLS 1.2 minimum
* tls-crypt: Additional encryption layer for control channel
* Compression: LZO (can be disabled)
* Logging: Status and connection logs
* Privileges: Runs as nobody after initialization
* CRL: Cert revocation list support

### Client Configuration
Generated client configs include:

* Embedded CA cert
* Embedded client cert and key
* Embedded tls-crypt key
* Matching cipher and auth settings
* Automatic DNS configuration
* Gateway redirect (full tunnel VPN)

### DNS Options
During setup, you can choose DNS servers:

* 1.1.1.1 - Cloudflare DNS (default, privacy-focused)
* 8.8.8.8 - Google
* 9.9.9.9 - Quad9 DNS (security-focused)
* Custom DNS server
* Your routers DNS (for local network access)

### Split Tunnel vs Full Tunnel
By default, all client traffic goes through the VPN (full tunnel). To change this:

Edit `/etc/openvpn/server.conf` and comment out:
```
push "redirect-gateway def1 bypass-dhcp"
```

Then add specific routes you want to push:
```
push "route 192.168.1.0 255.255.255.0"
```

Restart the service:
```
sudo systemctl restart openvpn@server
```

### Firewall Configuration
The script automatically detects and configures your firewall:

* iptables: Creates NAT masq rule, saves with netfilter-persistent(Debian) or iptables.service(Arch)
* nftables: Creates dedicated openvpn table with masq rule

### Port Forwarding
If your server is behind NAT, forward UDP port 1194(or chosen port) to your server's internal IP.

## Acknowledgments
* OpenVPN project
* easy-rsa PKI management
* The OpenVPN community
