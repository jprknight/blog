---
title: Vault Warden Cron
---

Runs on the Docker server for Vault Warden. Runs under root cron.

```

0 0 * * * /home/user/VaultWarden/vw-data/backup.sh

```

Run the rclone command against the user cron which is setup to with keys to use rclone.

```

0 1 * * * rclone copy /home/user/backups/vaultwarden backupvw:Backups

```
