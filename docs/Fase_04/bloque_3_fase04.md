# 🔐 Bloque 3 – Generación de certificados válidos para VM2

## 1. Crear directorio

```bash
mkdir -p /etc/opensearch/certs
cd /etc/opensearch/certs
```

## 2. Crear archivo `san.cfg`

```ini
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no

[req_distinguished_name]
CN = wazuh.vm2.local

[v3_req]
subjectAltName = @alt_names

[alt_names]
DNS.1 = wazuh.vm2.local
DNS.2 = node2
IP.1 = 192.168.68.115
```

## 3. Generar clave y CSR

```bash
openssl genrsa -out vm2.key 2048
openssl req -new -key vm2.key -out vm2.csr -config san.cfg
```

## 4. Firmar con CA ya existente (copiada de VM1)

```bash
openssl x509 -req -in vm2.csr \
-CA rootCA.pem -CAkey rootCA.key -CAcreateserial \
-out vm2.crt -days 365 -sha256 -extensions v3_req -extfile san.cfg
```

## 5. Validar archivos generados

```bash
ls -l /etc/opensearch/certs/
```

Archivos requeridos:
- `vm2.crt`
- `vm2.key`
- `rootCA.pem`

## 6. Asignar permisos

```bash
chown -R opensearch:opensearch /etc/opensearch/certs
```