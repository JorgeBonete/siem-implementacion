
# Guía de Despliegue — Wazuh Manager (Fase 03)

> **Versión probada:** Wazuh 4.12.0 sobre Rocky Linux 9  
> **Objetivo:** Instalar el Manager, habilitar HTTPS y m‑TLS, y dejar la API operativa en el puerto **55000**.

---

## 1 · Instalación base

```bash
dnf install -y wazuh-manager
systemctl enable --now wazuh-manager   # arranque inicial
```

---

## 2 · Certificado del **servidor**

| Fichero | Descripción |
|---------|-------------|
| `certificado.pem` | Wildcard Sectigo `*.coopsi.fin.ec` (EKU *serverAuth*) |
| `wazuh-key.pem`   | Clave privada del wildcard |

1. Copiar ambos a `/var/ossec/api/configuration/ssl/`  
2. Permisos:

```bash
chown wazuh:wazuh /var/ossec/api/configuration/ssl/{certificado.pem,wazuh-key.pem}
chmod 640        /var/ossec/api/configuration/ssl/*.pem
```

---

## 3 · Crear CA interna y certificado **clientAuth**

```bash
cd /etc/pki/tls/wazuh

# CA raíz (5 años)
openssl genrsa -out rootCA.key 4096
openssl req -x509 -new -key rootCA.key -days 1825   -subj "/C=EC/O=COOPSI/CN=COOPSI-Root-2025"   -out rootCA.pem

# Certificado de cliente (2 años)
openssl genrsa -out api-client.key 4096
openssl req -new -key api-client.key   -subj "/C=EC/O=COOPSI/CN=client-api"   -out api-client.csr
printf "extendedKeyUsage = clientAuth\n" > ext.cnf
openssl x509 -req -in api-client.csr -CA rootCA.pem -CAkey rootCA.key   -CAcreateserial -days 730 -sha256 -extfile ext.cnf   -out api-client.crt
```

Copiar `rootCA.pem` a la carpeta SSL del Manager:

```bash
cp rootCA.pem /var/ossec/api/configuration/ssl/
chown wazuh:wazuh /var/ossec/api/configuration/ssl/rootCA.pem
chmod 640        /var/ossec/api/configuration/ssl/rootCA.pem
```

---

## 4 · Configuración `api.yaml`

```yaml
host: ['0.0.0.0', '::']
port: 55000

https:
  enabled: yes
  key:  wazuh-key.pem
  cert: certificado.pem
  use_ca: yes        # ← m‑TLS
  ca:   rootCA.pem

logs:
  level: info
```

> **Importante:** solo nombres de archivo; sin rutas ni espacios extras.

Valida YAML:

```bash
python - <<'PY'
import yaml, pathlib; yaml.safe_load(pathlib.Path('/var/ossec/api/configuration/api.yaml').read_text()); print('YAML OK')
PY
```

---

## 5 · Contexto SELinux (si está en *enforcing*)

```bash
semanage fcontext -a -t cert_t '/var/ossec/api/configuration/ssl(/.*)?'
restorecon -Rv /var/ossec/api/configuration/ssl
```

---

## 6 · Reinicio y prueba

```bash
systemctl restart wazuh-manager
sleep 5

curl -kv --cert /etc/pki/tls/wazuh/certs/api-client.crt            --key  /etc/pki/tls/wazuh/certs/api-client.key            https://localhost:55000/ | head
# → banner JSON o HTTP/1.1 401 Unauthorized
```

---

## 7 · Solución de problemas rápidos

| Error | Causa / Fix |
|-------|-------------|
| `No such file or directory` al arrancar | Nombre con espacios/CR; archivo fuera de `/ssl`. |
| APIError 2000 “parameters not expected” | Campos obsoletos (`ssl_protocol`, `ssl_ciphers`) o rutas absolutas. |
| Wrapper se auto‑llama | Editar `/var/ossec/bin/wazuh-apid` y fijar `WPYTHON_BIN`. |
| `401 Unauthorized` tras handshake | m‑TLS OK; añade token API en las peticiones. |

---

## 8 · Checklist final antes de Filebeat

- `systemctl status wazuh-manager` → **active (running)**
- `ss -plnt | grep 55000` → puerto en LISTEN
- `curl` con client cert devuelve banner JSON / 401

Con esto el Wazuh Manager queda listo para la Fase 03 (Filebeat → Indexer → Dashboard).

---
