# 📦 Bloque 1 – Preparación del disco de datos (VM2)

## 1. Crear punto de montaje

```bash
mkdir -p /mnt/data/opensearch
```

## 2. Verificar UUID

```bash
blkid /dev/sdb
```

Ejemplo de salida:
```
/dev/sdb: UUID="3f2f2571-e60f-49be-a051-c91c227ad2ed" TYPE="xfs"
```

## 3. Editar `/etc/fstab`

```bash
nano /etc/fstab
```

Agregar:
```
UUID=3f2f2571-e60f-49be-a051-c91c227ad2ed /mnt/data/opensearch xfs defaults 0 0
```

## 4. Montar el disco

```bash
mount -a
```

## 5. Validar montaje

```bash
df -h | grep opensearch
```

## 6. Crear usuario `opensearch`

```bash
useradd opensearch
```

## 7. Asignar permisos

```bash
chown -R opensearch:opensearch /mnt/data/opensearch
```