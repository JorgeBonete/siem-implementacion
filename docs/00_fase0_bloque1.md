
# FASE 0 – BLOQUE 1: Creación de Máquinas Virtuales (VMs)

## 🎯 Objetivo
Establecer las dos máquinas virtuales base con Rocky Linux 9 y conectividad entre ellas, como base para la arquitectura distribuida del SIEM.

---

## 🖥️ VM1 – wazuh-server
- Sistema: Rocky Linux 9.3
- CPU: 4 vCPU
- RAM: 8 GB
- Disco: 40 GB
- Red: NAT + Adaptador Interno
- IP estática: `192.168.100.120`

## 🖥️ VM2 – wazuh-agent1
- Sistema: Rocky Linux 9.3
- CPU: 4 vCPU
- RAM: 8 GB
- Disco base: 40 GB
- Disco adicional: 500 GB
- Red: NAT + Adaptador Interno
- IP estática: `192.168.100.121`

---

## ⚙️ Asignación de hostname
```bash
sudo hostnamectl set-hostname wazuh-server    # En VM1
sudo hostnamectl set-hostname wazuh-agent1    # En VM2
hostnamectl
```

---

## 🧩 Configuración /etc/hosts (en ambas VMs)
```bash
192.168.100.120  wazuh-server
192.168.100.121  wazuh-agent1
```

**¿Por qué es importante?**
Esto permite que los servicios se comuniquen por nombre en lugar de IP fija, facilitando el mantenimiento, la legibilidad de configuración y la resiliencia si se reasignan direcciones.

---

## ✅ Validación del Bloque 1
- [ ] Las VMs están creadas y accesibles
- [ ] Hostnames definidos correctamente
- [ ] Conectividad funcional entre ambas (`ping`)
- [ ] `/etc/hosts` configurado correctamente

---

✅ Continúa con el Bloque 2 de la Fase 0 para aplicar hardening básico y preparar los entornos.
