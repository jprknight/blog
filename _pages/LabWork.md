---
permalink: /LabWork/
title: "What's Going On In My Lab"
toc: true
---

## Lab Host

A single machine with a 1TB SSD and a four port Intel NIC running Proxmox VE.

HP Thin Client running PFSense firewall.

## Virtual Machines

My distribution of choice is Ubuntu server.

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

## Notes

### Shinobi (n.n.n.30)

Published to the internet on <https://catcams.contoso.com> via a CloudFlare tunnel. Agent running outbound.

### Minecraft  (n.n.n.40)

I'm terrible at creating worlds and then wiping them, deleting them, or losing them. This is my persistent, personal Minecraft survival server.

### Docker  (n.n.n.25)

I'm never a fan of multi purposing a 'server', but some of these services only come in Docker form.

#### AdGuard-Sync

Syncs changes between the two AdGuard instances running.

#### Organizr

Great dashboard for all my network services. Includes SSL certificate and IP based links. Has plugins for AdGuard and Uptime Kuma which is a nice bonus.

#### Uptime Kuma

Monitoring tool for all network services and devices.

#### Vault Warden

Self hosted password manager. Backing up data to cloud storage with rclone.

### Nginx Reverse Proxy  (n.n.n.80)

Using "Let's Encrypt" to provide trusted, third party SSL certificates on network services.

### Squid Deb Proxy (n.n.n.20)

Saves bandwidth downloading update packages only once. I tried other deb proxy services and ran into issues. So far I haven't experienced any with this solution.

### NetBoot.xyz (n.n.n.70)

PFSense advertises this service within DHCP settings. Using here and there for testing purposes. I tend to build VMs from ISOs.

### Ansible (n.n.n.60)

Automate things like setting timezones, Squid Deb Proxy etc on all machines.

### AdGuard (n.n.n.108, n.n.n.109, VIP on n.n.n.110)

Ad blocker, provide local DNS resolution / redirection to Nginx for SSL certificates. 

KeepAlived creating a VIP for DNS, setup for round robin.

There's no 'high availability' here since I am running both VMs on the same host. I just wanted to play with creating a Virtual IP Address (VIP) and failover.

### TestingSandbox

A test box to break and throw away if things go really sideways with it. No actual services I plan to use live here.
