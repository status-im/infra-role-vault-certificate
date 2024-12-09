# Description

This role helps generate and store certificate in Hashicorp Vault.

# Usage

This role can be use for 2 purposes

## Generate Application CA

> !Alert! Due to an issue with the verification of certificate, it isn't recommended to used this part yet.
> Insted generate a Root CA with the command line or interface, and save the keys

The role fetch or generate a CA for the service.

### Process

```
    +-------------------+    Don't   +--------------+          +----------+          +---------------+          +----------------+          +-----------+
    |                   |    exist   |              |          |          |          |               |          |                |          |           |
    | Fetch Certificate |----------->| Generate CSR |--------->| Sign CSR |--------->| Add CRT as CA |--------->| Save CRT + Key |--------->| Write CRT |
    |     in Vault      |            |              |          |          |          |               |          |   in Vault     |          |  on host  |
    |                   |            +--------------+          +----------+          +---------------+          |                |          |           |
    +-------------------+                                                                                       +----------------+          +-----------+
                |                                                                                                                                  ^
                |  CRT + KEY already exist                                                                                                         |
                +----------------------------------------------------------------------------------------------------------------------------------+
```

### Configuration

```yaml
cert_common_name:           'application-ca'
cert_role_allow_domains:    'example.org'
cert_vault_storing_path:    '{{ env }}/{{ stage }}/service/root-ca
cert_info:
  organization: 'Org'
  unit:         'Unit'
  country:      'County'
cert_ca_ttl: '365d'
```



## Generate Client certificate

### Process

```
+-------------------+   Don't    +-------------+        +--------------+         +----------------+          +-----------------+
|                   |   exist    |             |        |              |         |                |          |                 |
| Fetch Certificate ------------>| Create Role | ------->| Generate CRT |-------->| Save CRT + Key |--------->| Write CRT + KEY |
|     in Vault      |            |             |        |              |         |   in Vault     |          |    on host      |
|                   |            |             |        |              |         |                |          |                 |
+-------------------+            +-------------+        +--------------+         +----------------+          +-----------------+
           |                                                                                                         ^
           |  CRT + KEY already exist                                                                                |
           +---------------------------------------------------------------------------------------------------------+
```

### Configuration

```yaml
cert_common_name: 'Client'
cert_domain_sans: 'example.org'
cert_ip_sans:     '192.168.1.0'
cert_vault_storing_path: '{{ env }}/{{ stage }}/service/hostname'


# Role Configuration
cert_role_name: 'application-role'
cert_role_issuer_name: 'Application'
cert_role_info:
  organization: 'Org'
  unit:         'Unit'
  country:      'County'
cert_role_max_ttl: '365d'
cert_role_allow_domains: 'example.org'
cert_role_allow_subdomains: True
```

