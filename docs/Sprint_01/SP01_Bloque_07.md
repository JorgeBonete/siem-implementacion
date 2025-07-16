
# SPRINT 1: TAREA 7 - Verificación y Preparación de los Archivos del Certificado Wildcard SSL en Ambas VMs 🔐

Esta tarea se enfoca en asegurar que los archivos del certificado wildcard SSL estén correctamente ubicados y preparados en ambas máquinas virtuales (`nodo-coordinadorwz` y `nodo-datoswz`). Estos certificados son esenciales para cifrar las comunicaciones entre los componentes del SIEM y garantizar la seguridad de los datos en tránsito. Este paso se realizó en **AMBAS VMs**.

## Pasos de Configuración: ⚙️

---

### Paso 1: Entender los Archivos del Certificado Wildcard 📄

Un certificado wildcard (`*.midominio.com`) generalmente consta de tres archivos principales que se generaron previamente por tu autoridad de certificación (CA) o herramienta:

- **Archivo de Clave Privada (.key o .pem):** Es la clave secreta que nunca debe ser compartida.  
  Ejemplo: `wildcard.midominio.com.key`

- **Archivo de Certificado (.crt o .pem):** Es tu certificado SSL para el dominio wildcard.  
  Ejemplo: `wildcard.midominio.com.crt`

- **Archivo de Cadena de Certificados (.ca-bundle, .crt, o .pem):** Contiene los certificados intermedios y/o el certificado raíz.  
  Ejemplo: `wildcard.midominio.com.ca-bundle`

---

### Paso 2: Copiar los Archivos del Certificado a las VMs 📁

Se copió de forma segura los archivos desde tu máquina local a una ubicación temporal en ambas VMs.

```bash
# Clave privada
scp -P 2222 /ruta/a/wildcard.midominio.com.key admin@192.168.1.10:/tmp/
scp -P 2222 /ruta/a/wildcard.midominio.com.key admin@192.168.1.20:/tmp/

# Certificado
scp -P 2222 /ruta/a/wildcard.midominio.com.crt admin@192.168.1.10:/tmp/
scp -P 2222 /ruta/a/wildcard.midominio.com.crt admin@192.168.1.20:/tmp/

# Cadena (si aplica)
scp -P 2222 /ruta/a/wildcard.midominio.com.ca-bundle admin@192.168.1.10:/tmp/
scp -P 2222 /ruta/a/wildcard.midominio.com.ca-bundle admin@192.168.1.20:/tmp/
```

---

### Paso 3: Crear un Directorio Seguro para los Certificados en las VMs 📁🔒

```bash
sudo mkdir -p /etc/pki/tls/private/siemcerts/
sudo chmod 700 /etc/pki/tls/private/siemcerts/
```

---

### Paso 4: Mover y Proteger los Archivos del Certificado en las VMs 🚚🛡️

```bash
# Mover archivos
sudo mv /tmp/wildcard.midominio.com.key /etc/pki/tls/private/siemcerts/
sudo mv /tmp/wildcard.midominio.com.crt /etc/pki/tls/private/siemcerts/
sudo mv /tmp/wildcard.midominio.com.ca-bundle /etc/pki/tls/private/siemcerts/  # si aplica

# Permisos
sudo chmod 400 /etc/pki/tls/private/siemcerts/wildcard.midominio.com.key
sudo chmod 644 /etc/pki/tls/private/siemcerts/wildcard.midominio.com.crt
sudo chmod 644 /etc/pki/tls/private/siemcerts/wildcard.midominio.com.ca-bundle  # si aplica
```

---

### Verificación Final ✅

```bash
ls -l /etc/pki/tls/private/siemcerts/
```

Salida esperada:

```text
-rw-r--r--. 1 root root YYY wildcard.midominio.com.crt
-r--------. 1 root root ZZZ wildcard.midominio.com.key
-rw-r--r--. 1 root root WWW wildcard.midominio.com.ca-bundle  # si aplica
```

*📝 Nota:* Asegúrate que los permisos son estrictos, especialmente para la clave privada.
