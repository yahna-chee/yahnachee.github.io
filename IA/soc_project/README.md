# Pipeline de emulación, ingesta y orquestación del SOC (flujo operacional)

El ecosistema diseñado realiza análisis predictivo y simula un ciclo completo de vida de un incidente en un SOC moderno. El flujo operacional representado en la arquitectura se desglosa bajo los siguientes comentarios de diseño e ingeniería:

---

## 🔄 1. Fase de ejecución y ataque (emulación controlada)

El ciclo inicia con **MITRE Caldera**, plataforma utilizada para ejecutar tácticas y técnicas adversarias de manera automatizada. Al emular vectores como phishing o escalación de privilegios directamente en el host objetivo, se elimina la aleatoriedad y se asegura que las amenazas sigan la taxonomía estándar de la industria.

---

## 📡 2. Captura y generación de telemetría (endpoints)

La actividad maliciosa es interceptada localmente a nivel de sistema operativo mediante **Windows Event Viewer** enriquecido con **Sysmon** (registrando Event IDs críticos como la creación de procesos `ID 1` o conexiones de red `ID 3`). Esto garantiza una visibilidad granular y cruda de los artefactos antes de ser transmitidos.

---

## 🔍 3. Ingesta híbrida y correlación (SIEM)

El pipeline está diseñado para ser agnóstico y tolerante a entornos empresariales mixtos. La telemetría se bifurca mediante dos mecanismos de transporte:

| Mecanismo | Función |
|-----------|---------|
| **Wazuh Agent → Wazuh Manager** | Procesamiento basado en software de código abierto, ejecutando reglas de correlación activa y alertas estáticas en tiempo real. |
| **Splunk Universal Forwarder → Splunk Enterprise** | Entornos corporativos de alta indexación, permitiendo búsquedas avanzadas mediante queries **SPL (Splunk Processing Language)**, generación de alertas y el despacho inmediato de cargas útiles informativas hacia el orquestador a través de **Webhooks**. |

---

## ⚡ 4. Respuesta y contención automatizada (SOAR)

El eslabón final del flujo se consolida en el entorno **Splunk SOAR**. Al recibir la alerta enriquecida, el sistema activa de manera autónoma un **Playbook de contención automática** para mitigar la amenaza en milisegundos (aislamiento de red, revocación de credenciales o bloqueo de IPs), cerrando el ciclo de respuesta ante incidentes sin requerir intervención manual constante del analista.

---

## 🗺️ Diagrama de arquitectura de telemetría y orquestación
<img width="1933" height="2209" alt="deepseek_mermaid_20260715_16611b" src="https://github.com/user-attachments/assets/04c03acd-2d02-4877-855f-796f14d6fec4" />

## 🏗️ Despliegue de la arquitectura

Arquitectura desplegada sobre **AWS Cloud**, distribuida en 5 instancias independientes:

| Instancia | Servicio | Sistema operativo | Componentes |
|---|---|---|---|
| 1 | **MITRE Caldera** | Ubuntu | Server · Agents · Operations |
| 2 | **Windows Server 2025** | Windows | Sysmon · Wazuh Agent · Splunk UF |
| 3 | **Wazuh SIEM** | Ubuntu | Manager · Indexer · Dashboard |
| 4 | **Splunk Enterprise** | Ubuntu | Indexer · Search Head · HEC |
| 5 | **Splunk SOAR** | Amazon Linux | Playbooks · Assets · Connectors |
| * | **Jupyter Notebook** | Windows | Modelo ML · Dashboard |

**Flujo general:** el tráfico y eventos generados/simulados en la Instancia 2 (Windows Server, vía Sysmon y Splunk UF) se envían a Wazuh (Instancia 3) y Splunk (Instancia 4) para correlación; MITRE Caldera (Instancia 1) simula técnicas de ataque (TTPs) sobre el entorno; las alertas resultantes alimentan el modelo de triage servido desde Jupyter Notebook / Google Colab (*opcional); y las acciones de respuesta se orquestan vía Splunk SOAR (Instancia 5).

## 📌 Nota
- Versión: 1.0
- Fecha: Julio 2026
- Tema: Sistemas Expertos en Centros de Operaciones de Seguridad (SOC)

