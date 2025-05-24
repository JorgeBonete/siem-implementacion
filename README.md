# Wazuh – Implementación de SIEM

Este repositorio documenta paso a paso la implementación de un sistema SIEM basado en Wazuh 4.10 + OpenSearch 2.19 sobre Rocky Linux 9, como parte de un proyecto orientado a instituciones financieras.

## 📌 Objetivo del proyecto
Implementar un entorno de monitoreo y detección de eventos de seguridad que incluye:
- Wazuh Manager y Dashboard
- OpenSearch distribuido
- Reglas personalizadas y alertas por correo
- Integración con Active Directory
- Visualización gráfica
- Configuración de Suricata como NIDS

## 🧱 Arquitectura
| VM               | Componentes                                                          |
|------------------|----------------------------------------------------------------------|
| `wazuh-server`   | Wazuh Manager + Wazuh Dashboard + OpenSearch (coordinating node)     |
| `wazuh-agent1`   | OpenSearch (data node) + Suricata + Wazuh Agent                      |

## 📂 Estructura del repositorio
