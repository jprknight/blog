---
title: Ansible 02cache
---

Proxy file sent to machines by Ansible. Points all Ubuntu servers to the Squid Deb Proxy.

```

Acquire::http::Proxy "http://192.168.1.20:8000";
Acquire::https::Proxy "http://192.168.1.20:8000";

```
