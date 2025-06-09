# 📦 Bloque 2 – Instalación de OpenSearch 2.19.2 en VM2 (nodo de datos)

## 1. Descargar RPM

```bash
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.19.2/opensearch-2.19.2-linux-x64.rpm
```

## 2. Instalar

```bash
rpm -ivh opensearch-2.19.2-linux-x64.rpm
```

## ⚠️ Advertencias comunes

- Puede aparecer:
```
Header V4 RSA/SHA512 Signature, key ID 9310d3fc: NOKEY
...
ERROR: Something went wrong during demo configuration installation
...
```
- No afecta si vas a usar certificados propios.

## 3. Verifica que se haya creado el servicio:

```bash
systemctl status opensearch
```