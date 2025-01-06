---
permalink: /homelab/
title: "What's Going On In My Lab"
toc: true
toc_sticky: true
---

I put this together to document what's running in my home lab, how it's setup, and lessons learned. All so things are easier for me to recall as needed.

The majority of these services are not available from the internet. The one service which is exposed to the internet is running Fail2Ban to ban IP addresses which create too many failed sign in attempts. It's not Vault Warden, my personal taste that's more risk than I am willing to take. The trade off I am happy to make is, I can only update passwords when on my local network.

## Hardware

A single HP Z230 machine with an i7-4770, 32GB RAM, a 1TB SSD and a four port Intel NIC running Proxmox VE.

HP Thin Client, with a low profile four port Intel NIC, running PFSense firewall.

## Virtual Machines

My distribution of choice is Ubuntu server.

Each VM has WebMin installed for some easy web gui management if needed, along with SSH enabled. One action which is very easy through WebMin is to setup automatic updates.

- Shinobi for monitoring IP cameras.
- Minecraft Java Edition.
- Docker
  - Portainer
  - AdGauardHome-Sync
  - Organzr
  - Uptime Kuma
  - Vault Warden
- Nginx Reverse Proxy
- Squid Deb Proxy
- NetBoot.xyz
- Ansible
- AdGuard1
- AdGuard2
- TestingSandbox

## Hardware Notes

### Proxmox VE Host (n.n.n.50)

Community Edition VE server. Setup enlan0 to match the MAC address of the connected NIC port.

### PFSense (n.n.n.75)

One thing I have noticed is connecting to consoles doesn't work for me when connecting to my Proxmox host with a SSL/HTTPS DNS name. So I tend to connect to the IP address on HTTP instead.

#### Firewall Rules

- Anti-Lockout Rule (added by PFSense).
- Allow alias for Power Users to get out anywhere on the internet on any port.
- Block blacklist alias for devices not allowed out to the internet. IP cameras aren't allowed to 'phone home'.
- Allow only typical web ports (80 and 443) for guest devices.
- Allow typical web, email, ftp / ssh, gaming ports for other, known devices.
- Allow specific devices out to the internet on specific ports. Not 53.
- DNS: High level design is to only allow DNS lookups through local name server, disallow services and devices using internet based name servers.
  - Allow specific name servers (AdGuard) to make DNS calls out to the internet.
  - NAT redirect all others to internal DNS VIP (AdGuard).
  - Block DNS over TLS.
  - Block common DNS over HTTPS.
- NAT redirect any outbound NTP requests to PFSense.

#### DHCP

DHCP pool is from n.n.n.226 to n.n.n.254. Added static mappings for each known device on the network. The DHCP pool is reserved for new or guest devices. A firewall rule only allows guest devices out to the internet on 80 and 443.

#### DNS

DHCP server advertises AdGuard VIP as the DNS service on the network.

#### NetBoot

DHCP server advertises NetBoot.xyz as the PXI boot service on the network.

#### NTP

DHCP server advertises PFSense as the NTP service on the network.

## VM Notes

### Shinobi (n.n.n.30)

Published to the internet with a CloudFlare tunnel.

### Minecraft  (n.n.n.40)

I'm terrible at creating worlds and then wiping them, deleting them, or losing them. This is my persistent, personal Minecraft survival server.

### Docker  (n.n.n.25)

I'm never a fan of multi purposing a 'server', but some of these services only come in Docker form.

#### Portainer

Port 9443.

Manage Docker in a web gui.

#### AdGuard-Sync

Port 8080.

Syncs changes between the two AdGuard instances running.

#### Organizr

Port 80.

Great dashboard for all my network services. Includes SSL certificate and IP based links. Has plugins for AdGuard and Uptime Kuma which is a nice bonus.

#### Uptime Kuma

Port 3001.

Monitoring tool for all network services and devices.

#### Vault Warden

Port 8090.

Self hosted password manager. Backing up data to cloud storage with rclone.

### Nginx Reverse Proxy  (n.n.n.80)

Using "Let's Encrypt" to provide trusted, third party SSL certificates on network services. Setup with the "Offline" approach, where the instructions prompt to take a secret key to another machine with a web browser to complete the authentication / authorization.

Multiple network services (service.contoso.com) are directed to this service to serve up as trusted SSL sites when accessed in a web browser.

### Squid Deb Proxy (n.n.n.20)

Saves bandwidth downloading update packages only once. I tried other deb proxy services and ran into issues. So far I haven't experienced any with this solution.

### NetBoot.xyz (n.n.n.70)

PFSense advertises this service within DHCP settings. Using here and there for testing purposes. I tend to build VMs from ISOs.

### Ansible (n.n.n.60)

Automate things like setting timezones, Squid Deb Proxy etc on all machines.

/etc/ansible/files
/etc/ansible/playbooks

### AdGuard (n.n.n.108, n.n.n.109, VIP on n.n.n.110)

Ad blocker, provide local DNS resolution / redirection to Nginx for SSL certificates. Multiple network services (service.contoso.com) are directed Nginx by AdGuard through local DNS records to serve up as trusted SSL sites when accessed in a web browser.

KeepAlived creating a VIP for DNS, setup for round robin.

There's no 'high availability' here since I am running both VMs on the same host. I just wanted to play with creating a Virtual IP Address (VIP) and failover.

AdGuard KeepAlived Master Config: <https://jprknight.github.io/blog/AdGuard-KeepAlived-Master/>

AdGuard KeepAlived Backup Config: <https://jprknight.github.io/blog/AdGuard-KeepAlived-Backup/>

### TestingSandbox (n.n.n.45)

A test box to break and throw away if things go really sideways with it. No actual services I plan to use live here.
