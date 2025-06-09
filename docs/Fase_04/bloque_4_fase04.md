# ⚙️ Bloque 4 – Configuración del `opensearch.yml` como nodo de datos (VM2)

## Archivo: `/etc/opensearch/opensearch.yml`

```yaml
cluster.name: wazuh-cluster
node.name: node-data-1
node.roles: [data]

path.data: /mnt/data/opensearch
path.logs: /var/log/opensearch

network.host: 0.0.0.0

discovery.seed_hosts: ["192.168.68.114"]

plugins.security.ssl.transport.pemcert_filepath: /etc/opensearch/certs/vm2.crt
plugins.security.ssl.transport.pemkey_filepath: /etc/opensearch/certs/vm2.key
plugins.security.ssl.transport.pemtrustedcas_filepath: /etc/opensearch/certs/rootCA.pem
plugins.security.ssl.transport.enforce_hostname_verification: false

plugins.security.ssl.http.enabled: true
plugins.security.ssl.http.pemcert_filepath: /etc/opensearch/certs/vm2.crt
plugins.security.ssl.http.pemkey_filepath: /etc/opensearch/certs/vm2.key
plugins.security.ssl.http.pemtrustedcas_filepath: /etc/opensearch/certs/rootCA.pem

plugins.security.allow_unsafe_democertificates: false
plugins.security.allow_default_init_securityindex: true
plugins.security.authcz.admin_dn:
  - "CN=wazuh.vm1.local,O=COOPSI,C=EC"
```

## ✅ Validaciones

```bash
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable opensearch --now
journalctl -fu opensearch.service
```