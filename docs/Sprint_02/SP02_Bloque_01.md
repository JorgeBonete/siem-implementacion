
# SPRINT 2: Instalación del Core SIEM (Wazuh Manager & OpenSearch Indexer) con SSL

**Fechas:** 8 de Julio - 18 de Julio

**Objetivo:** Desplegar el núcleo de procesamiento y almacenamiento del SIEM, asegurando la comunicación segura entre ellos desde la primera conexión.

---

## 2.1. Instalación de Wazuh Manager 4.12.0 en `nodo-coordinadorwz` (VM1)

Esta sección detalla los pasos para instalar el Wazuh Manager en la máquina virtual designada como `nodo-coordinadorwz`. El Manager es el componente central de Wazuh, responsable de la recolección, análisis y gestión de los eventos de seguridad.

### **Pasos de Instalación:**

#### **Paso 1: Importar la Clave GPG de Wazuh** 🔑

Se importa la clave GPG pública de Wazuh. Esto garantiza la autenticidad e integridad de los paquetes que se descargarán del repositorio oficial.

```bash
sudo rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH
```

#### **Paso 2: Añadir el Repositorio de Wazuh 🌐**

Se crea el archivo de configuración del repositorio YUM para Wazuh.

```bash
sudo tee /etc/yum.repos.d/wazuh.repo <<EOF
[wazuh_repo]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=Wazuh Repository
baseurl=https://packages.wazuh.com/4.x/yum/
EOF
```

#### **Paso 3: Instalar el Wazuh Manager 📦**

```bash
sudo dnf install -y wazuh-manager
```

#### **Paso 4: Habilitar y Iniciar el Servicio Wazuh Manager ✅**

Habilitar el servicio:
```bash
sudo systemctl enable wazuh-manager
```

Iniciar el servicio:
```bash
sudo systemctl start wazuh-manager
```

Verificar el estado del servicio:
```bash
sudo systemctl status wazuh-manager
```

**Salida Esperada (fragmento):**
```
● wazuh-manager.service - Wazuh manager
    Loaded: loaded (/usr/lib/systemd/system/wazuh-manager.service; enabled; vendor preset: disabled)
    Active: active (running) since Fri 2025-07-05 HH:MM:SS -05; Xmin ago
    Docs: https://documentation.wazuh.com
    Process: XXXXX ExecStart=/usr/bin/wazuh-manager -d (code=exited, status=0/SUCCESS)
    Main PID: YYYYY (wazuh-manager)
```
