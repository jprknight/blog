Earlier in 2024 I upgraded my Proxmox host to 8.2 and ran into the issues mentioned here: <https://forum.proxmox.com/threads/no-networking-after-upgrade-to-8-2.145727/>, where my **Network Interface Card** name changed upon a reboot after the update. At that time I took the quick fix route of updating the NIC name and moving on.

Over the holidays I was out of town and was monitoring my home with cameras when a unexpected power loss made the host power cycle. Some additional updates or a kernel update must have changed the NIC name on my again.

At this point I found <https://pve.proxmox.com/pve-docs/pve-admin-guide.html#network_override_device_names> and followed the guidance to create a unique NIC name matched against the MAC address of the NIC I am using on the host.



My situation is complicated by the fact I have multiple NICs in my Proxmox host. A built in NIC (unused), and a quad port NIC, which I am using one port.

In order to stop NIC name updates taking effect on planned or unplanned reboots I did the following.

* Created the new file **/etc/systemd/network/10-enlan0.link** with the content below:

```shell
[Match]
MACAddress=00:26:cc:dd:ee:9b
Type=ether

[Link]
Name=enlan0
```

* Update configuration with:

```shell
update-initramfs -u -k all
```

* Rebooted the host for this change to take effect. This made enlan0 appear when running 'ip a'.

* Edited the /etc/network/interfaces file to the below. My main and only change here was pointing bridge-ports to enlan0.

```shell
auto lo
iface lo inet loopback

iface eno1 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.75
    bridge-ports enlan0
    bridge-stp off
    bridge-fd 0
```

* Ran service networking restart.

My subsequent kernel update and host reboot worked flawlessly. No more sudden loss of network connectivity.
