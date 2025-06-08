# Bloque 2 – Validación técnica y configuración de puertos (Fase 2)

## 1. Validar funcionamiento del servicio

### Revisar logs del manager
```bash
tail -n 50 /var/ossec/logs/ossec.log
```

### Verificar procesos activos
```bash
ps aux | grep wazuh
```

## 2. Abrir puertos requeridos para Wazuh Manager

### Firewalld
```bash
firewall-cmd --permanent --add-port=1514/tcp
firewall-cmd --permanent --add-port=1515/tcp
firewall-cmd --reload
```

### SELinux (si es necesario)
```bash
semanage port -a -t syslogd_port_t -p tcp 1514
semanage port -a -t syslogd_port_t -p tcp 1515
```

## 3. Verificar puertos en escucha
```bash
ss -tuln | grep 151
```

## 4. Verificar accesibilidad desde otra VM (ejemplo desde VM2)
```bash
nc -zv <IP_VM1> 1514
nc -zv <IP_VM1> 1515
```

## Resultado esperado

- Wazuh Manager escucha correctamente en los puertos 1514 y 1515.
- Logs limpios, sin errores de servicio.
- Listo para agregar agentes.
