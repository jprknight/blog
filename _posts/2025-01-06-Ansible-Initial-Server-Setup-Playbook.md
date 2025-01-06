---
title: Ansible Initial Server Setup Playbook
---

Initial server setup ran with:

```

ansible-playbook /etc/ansible/playbooks/APB-ALL-Initial-Server-Setup.yaml --ask-become-pass

```

```

---
  - name: All Initial Server Setup.
    hosts: all
    become: true
    remote_user: jeremy
    become_method: sudo
    tasks:
      - name: Set the Squid Deb Proxy.
        copy:
          src: /etc/ansible/files/02cache
          dest: /etc/apt/apt.conf.d/02cache

      - name: Set timezone to America/New_York
        timezone:
          name: America/New_York

      - name: Install the Proxmox Qemu-Guest-Agent.
        apt:
          name:
          - qemu-guest-agent
          state: present

      - name: Remove Nala - Apt frontend.
        apt:
          name:
          - nala
          state: absent

      - name: Install NTP tools.
        apt:
          name:
          - ntpsec
          state: present

```
