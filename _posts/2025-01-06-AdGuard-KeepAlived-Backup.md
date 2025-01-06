---
title: AdGuard KeepAlived Backup
permalink: /AdGuard-KeepAlived-Backup/
---

```

# https://www.pentestpartners.com/security-blog/how-to-use-keepalived-for-high-availability-and-load-balancing/
# https://technotim.live/posts/keepalived-ha-loadbalancer/


# sudo nano /etc/keepalived/keepalived.conf


# VRRP instance
vrrp_instance VI4SM {
        state BACKUP   # (optional) initial state for this server
        interface ens18 # interface where VRRP traffic will exist
        advert_int 5   # interval between sync
        virtual_router_id 110 # unique identifier per VRRP instance (same across all servers on the instance)
        priority 90   # server priority - higher number == higher priority


        # authentication for VRRP messages
        authentication {
                #auth_type PASS # simple authentication (plain)
                auth_type AH    # good authentication
                auth_pass changeme # password
        }
        virtual_ipaddress {
                192.168.1.110/24 dev ens18 # Virtual IP address and interface assignment
        }
    # notice that track_script does not apply to this setup, at least not in the same way
}


# Virtual server configured to listen on the VRRP Virtual IP
# This section instructs keepalive to configure the kernel's Virtual Server subsystem
virtual_server 192.168.1.110 53 {
        delay_loop 60 # time between checks, in seconds
        lb_algo rr    # load balancing algorithm
        lb_kind DR    # load balancing type: Direct Routing in this case
        protocol TCP
        persistence_timeout 60 # client to real-server mappings will be maintained for at least 60 seconds after a connection is terminated


        # Real Server A
        real_server 192.168.1.108 53 {
                weight 100 # used to influence the load balancing algorithm
                # real server check mechanism (this can be changed to other types of checking including custom scripts)
                # in this case, if a TCP connection cannot be established to the real server ip:port, then it is
                # removed from the pool
                TCP_CHECK {
                        connect_timeout 6
                }
        }


        # Real Server B
        real_server 192.168.1.109 53 {
                weight 100
                TCP_CHECK {
                        connect_timeout 6
                }
        }
}

```
