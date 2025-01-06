---
title: AdGuardSync Docker Stack
---

AdGuardSync is deployed on Docker with a stack.

Requires variables to be set for origin_url, origin_username, origin_password, replic_url, replica_username, and replica_password.

```

---
version: "2.1"
services:
  adguardhome-sync:
    image: quay.io/bakito/adguardhome-sync
    container_name: adguardhome-sync
    command: run
    environment:
      - ORIGIN_URL=${origin_url} #change as necessary
      - ORIGIN_USERNAME=${origin_username} #change as necessary
      - ORIGIN_PASSWORD=${origin_password} #change as necessary
      - REPLICA_URL=${replic_url} #change as necessary
      - REPLICA_USERNAME=${replica_username} #change as necessary
      - REPLICA_PASSWORD=${replica_password} #change as necessary
      - CRON=*/1 * * * * # run every 1 minute
      - RUNONSTART=true
    ports:
      - 8080:8080 #change as necessary
    restart: unless-stopped    
    
    
################
#
# Original Source: 
# https://github.com/bakito/adguardhome-sync
#
################

```
