# FASE 1 – BLOQUE 2: Instalación de OpenSearch Coordinating Node (VM1)

## 🎯 Objetivo del bloque
Instalar OpenSearch en su versión 2.19.2 como nodo coordinador (sin roles de data ni master) para integrarlo con Wazuh y el dashboard.

---

## 📋 Requisitos previos

- Sistema operativo: Rocky Linux 9
- Conectividad a internet (temporal para descarga)
- DNS configurado (manual)
- Wazuh Manager ya instalado
- Puerto 9200 disponible
- Java incluido con paquete OpenSearch

---

## ⚙️ Pasos realizados

### 1. Descargar paquete OpenSearch 2.19.2
```bash
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.19.2/opensearch-2.19.2-linux-x64.rpm
```

### 2. Instalar OpenSearch (sin firma GPG)
```bash
rpm -ivh --nosignature opensearch-2.19.2-linux-x64.rpm
```

> Se omitió la firma GPG por incompatibilidad con SHA-1 en Rocky Linux 9.

### 3. Deshabilitar OpenSearch Security Plugin
```bash
nano /etc/opensearch/opensearch.yml
```

Se agregó la línea:
```yaml
plugins.security.disabled: true
```

### 4. Configurar nodo coordinador
También en `opensearch.yml` se añadió:

```yaml
node.name: node-coordinator-1
node.roles: []
network.host: 0.0.0.0
discovery.seed_hosts: ["localhost"]
cluster.name: wazuh-cluster
```

### 5. Reducir consumo de memoria (opcional)
```bash
nano /etc/opensearch/jvm.options
```
Cambios aplicados:
```
-Xms1g
-Xmx1g
```

### 6. Iniciar servicio
```bash
systemctl daemon-reexec
systemctl enable opensearch --now
```

---

## ✅ Validaciones

- Servicio activo:
```bash
systemctl status opensearch
```

- Acceso sin errores:
```bash
curl http://localhost:9200
```

Respuesta esperada:
```json
{
  "name" : "node-coordinator-1",
  "cluster_name" : "wazuh-cluster",
  ...
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```

---

## 🔒 Consideraciones futuras – Habilitación de SSL

Actualmente, OpenSearch está operativo con el plugin de seguridad desactivado para facilitar la conexión inicial del Wazuh Dashboard.

Para ambientes productivos o regulados, se deberá:

- Rehabilitar el plugin `opensearch-security`
- Configurar certificados SSL válidos (autofirmados o emitidos por CA)
- Habilitar HTTPS en el puerto 9200
- Aplicar autenticación basada en usuarios y roles

> Esta tarea se documentará en una fase posterior.

---

## 📍 Estado final del bloque

✔️ OpenSearch instalado correctamente  
✔️ Nodo configurado como coordinating_only  
✔️ Seguridad deshabilitada temporalmente  
✔️ Listo para enlazarse con el dashboard en el Bloque 3  

---

## 🗂️ Máquina utilizada: `VM1`