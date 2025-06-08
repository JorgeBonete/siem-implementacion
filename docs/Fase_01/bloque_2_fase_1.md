# 🔐 Bloque 2: Hardenización del sistema

## Tareas
- Deshabilitar acceso SSH como root:
  Editar `/etc/ssh/sshd_config`:
  ```
  PermitRootLogin no
  PasswordAuthentication no
  ```
  Luego reiniciar servicio:
  ```bash
  sudo systemctl restart sshd
  ```
- Crear usuario administrador:
  ```bash
  sudo adduser adminuser
  sudo usermod -aG wheel adminuser
  ```
- Cambiar el puerto SSH (ej. 2222):
  En `/etc/ssh/sshd_config`:
  ```
  Port 2222
  ```
  Aplicar permiso en SELinux para permitir el nuevo puerto:
  ```bash
  sudo dnf install -y policycoreutils-python-utils
  sudo semanage port -a -t ssh_port_t -p tcp 2222
  ```
  Luego reiniciar `sshd`:
  ```bash
  sudo systemctl restart sshd
  ```
- Instalar y configurar fail2ban:
  ```bash
  sudo dnf install -y fail2ban
  sudo systemctl enable --now fail2ban
  ```
  Crear archivo `/etc/fail2ban/jail.local` con:
  ```ini
  [sshd]
  enabled = true
  port = 2222
  filter = sshd
  logpath = /var/log/secure
  maxretry = 3
  ```
- Activar firewall:
  ```bash
  sudo systemctl enable --now firewalld
  sudo firewall-cmd --permanent --add-port=2222/tcp
  sudo firewall-cmd --reload
  ```

## Validación
- Verificar que solo el usuario `adminuser` puede conectarse por SSH.
- Comprobar que el nuevo puerto está activo:
  ```bash
  sudo ss -tuln | grep 2222
  ```
- Confirmar fail2ban activo:
  ```bash
  sudo fail2ban-client status sshd
  ```
