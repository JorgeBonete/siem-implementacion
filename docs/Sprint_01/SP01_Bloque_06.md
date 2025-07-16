# 💾 SPRINT 1: TAREA 6 - Aprovisionamiento de Discos

Esta tarea se enfoca en la identificación, particionado, formateo y montaje persistente de un nuevo disco en las máquinas virtuales (`nodo-coordinadorwz` y/o `nodo-datoswz`) que requieran almacenamiento adicional para los componentes del SIEM (como bases de datos, logs, etc.).  
Se recomienda realizar este proceso en **`nodo-datoswz`** si es allí donde se alojarán los datos principales del SIEM.

---

## ⚙️ Pasos de Configuración del Disco

### 1️⃣ Identificar el Nuevo Disco

```bash
lsblk
```

✅ Salida esperada (fragmento):

```
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0  100G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   99G  0 part /
sdb           8:16   0  200G  0 disk   <-- Este es el nuevo disco
```

---

### 2️⃣ Crear Partición GPT con `gdisk`

```bash
sudo gdisk /dev/sdb
```

Dentro de `gdisk`:

- `o` → nueva tabla GPT → confirma con `y`  
- `n` → nueva partición  
  - Presiona Enter en todos los valores por defecto  
- `w` → escribir y salir → confirma con `y`

Verifica:

```bash
lsblk
```

✅ Debe aparecer `/dev/sdb1`.

---

### 3️⃣ Formatear con XFS

```bash
sudo mkfs.xfs -f /dev/sdb1
```

---

### 4️⃣ Crear Punto de Montaje

```bash
sudo mkdir -p /opt/siemdata
sudo chmod 755 /opt/siemdata
```

---

### 5️⃣ Montar Temporalmente

```bash
sudo mount /dev/sdb1 /opt/siemdata
df -h /opt/siemdata
```

✅ Debe mostrar algo como:

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb1       197G  2.6G  194G   2% /opt/siemdata
```

---

### 6️⃣ Configurar Montaje Persistente en `/etc/fstab`

1. Obtener UUID:

```bash
sudo blkid /dev/sdb1
```

2. Editar `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Añadir línea al final (reemplaza UUID):

```
UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX /opt/siemdata xfs defaults,nofail 0 0
```

3. Probar:

```bash
sudo mount -a
df -h /opt/siemdata
```

---

### 7️⃣ Ajustar Permisos

```bash
sudo chown -R admin:admin /opt/siemdata
```

---

✅ **Listo. El disco está aprovisionado, montado de forma persistente y listo para usarse en producción.**