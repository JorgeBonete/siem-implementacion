# ✅ Fase 01: 

📦 Bloque 1 -Actualización del sistema y herramientas esenciales

✏️ Tareas
- Ejecutar actualización general:
  🐚 bash
  * sudo dnf update -y && sudo dnf upgrade -y
  
- Instalar herramientas necesarias:
  🐚 bash
  * sudo dnf install -y curl wget git vim net-tools unzip tar lsof htop epel-release
  
- Verificar conectividad a Internet:
  🐚 bash
  * ping -c 4 google.com

🟢 Validación
- Confirmar que el sistema está actualizado (`dnf check-update` sin resultados).
- Verificar que los comandos `curl`, `wget`, `git`, etc. funcionan correctamente.
