
# Fase 3 – Configuración de OpenSearch con Seguridad (SSL) en VM1

## 🧱 Instalación de OpenSearch en VM1

```bash
# Importar la clave GPG de OpenSearch
rpm --import https://packages.opensearch.org/GPG-KEY-opensearch

# Crear el repositorio
cat > /etc/yum.repos.d/opensearch.repo << EOF
[opensearch]
name=OpenSearch repository
baseurl=https://packages.opensearch.org/releases/bundle/opensearch/2.x/2.19.0/
gpgcheck=1
gpgkey=https://packages.opensearch.org/GPG-KEY-opensearch
enabled=1
autorefresh=1
type=rpm-md
EOF

# Instalar OpenSearch
dnf install opensearch -y
```

## 🔄 Habilitar y arrancar el servicio

```bash
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable opensearch.service
systemctl start opensearch.service
```

---

## 🔧 Arquitectura recordada
- **VM1 (192.168.68.114)**: Wazuh Manager + Dashboard + OpenSearch como nodo **coordinador (coordinating_only)**.
- **VM2 (192.168.68.115)**: OpenSearch como **nodo de datos** (aún sin configurar).

---

## 📁 Configuración del archivo `opensearch.yml`

```yaml
cluster.name: wazuh-cluster
node.name: node-coordinator-1
path.data: /var/lib/opensearch
path.logs: /var/log/opensearch
network.host: 0.0.0.0

discovery.seed_hosts: ["192.168.68.115"]
cluster.initial_master_nodes: ["node-data-1"]

# Este nodo es sólo coordinador
node.roles: []

# === CONFIGURACIÓN BÁSICA DE TLS ===
plugins.security.ssl.transport.enforce_hostname_verification: false

plugins.security.ssl.http.enabled: true
plugins.security.ssl.http.pemkey_filepath: /etc/opensearch/certs/node.key
plugins.security.ssl.http.pemcert_filepath: /etc/opensearch/certs/node.crt
plugins.security.ssl.http.pemtrustedcas_filepath: /etc/opensearch/certs/rootCA.pem

plugins.security.ssl.transport.enabled: true
plugins.security.ssl.transport.pemkey_filepath: /etc/opensearch/certs/node.key
plugins.security.ssl.transport.pemcert_filepath: /etc/opensearch/certs/node.crt
plugins.security.ssl.transport.pemtrustedcas_filepath: /etc/opensearch/certs/rootCA.pem

plugins.security.authcz.admin_dn:
  - "CN=wazuh.vm1.local,O=COOPSI,C=EC"
```

---

## 🔐 Certificados TLS usados
- **node.key / node.crt**: Certificado y clave del nodo, con CN: `wazuh.vm1.local`
- **rootCA.pem**: Autoridad certificadora utilizada para firmar
- **fullchain.pem**: Utilizado en pruebas con `securityadmin.sh` como `-cacert`

### Verificación del certificado
```bash
openssl x509 -in /etc/opensearch/certs/node.crt -noout -text | grep -A1 "Subject:"
```
Resultado:
```
Subject: C=EC, O=COOPSI, CN=wazuh.vm1.local
```

---

## 🛠️ Problemas detectados y soluciones aplicadas

### 🔁 Duplicación de claves en `opensearch.yml`
- Error de arranque por claves duplicadas `path.data` y `path.logs`. Solucionado limpiando las entradas duplicadas.

### ❌ Error de conexión en `securityadmin.sh`
```
SSLHandshakeException: Received fatal alert: certificate_unknown
```
Causado por:
- El certificado no estaba registrado como `admin_dn`.

✅ Solución: agregar al `opensearch.yml`:
```yaml
plugins.security.authcz.admin_dn:
  - "CN=wazuh.vm1.local,O=COOPSI,C=EC"
```

### ⛔ Timeout al conectar con el cluster (porque VM2 no está configurada)
```
Cannot retrieve cluster state due to: 30,000 milliseconds timeout
```
✔️ Se decidió **no ejecutar** `securityadmin.sh` hasta configurar VM2.

---

## 📌 Estado actual
- OpenSearch en VM1 instalado, con configuración de nodo coordinador + SSL.
- No se ha ejecutado `securityadmin.sh`.
- Certificados válidos y funcionales.
- Se procederá a la configuración de **VM2 como nodo de datos** en la Fase 4.
