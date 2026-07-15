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

## 🏗️ Despliegue de la arquitectura (AWS Cloud)

┌─────────────────────────────────────────────────────────────────────────────────┐
│                                                                                 │
│                                   AWS CLOUD                                     │
│                                                                                 │
│   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│   │   INSTANCE 1    │  │   INSTANCE 2    │  │   INSTANCE 3    │                 │
│   │                 │  │                 │  │                 │                 │
│   │   WAZUH SIEM    │  │   SPLUNK        │  │   SPLUNK SOAR   │                 │
│   │   (Ubuntu)      │  │   Enterprise    │  │   (Amazon Linux)│                 │
│   │                 │  │   (Ubuntu)      │  │                 │                 │
│   │  • Manager      │  │  • Indexer      │  │  • Playbooks    │                 │
│   │  • Indexer      │  │  • Search Head  │  │  • Assets       │                 │
│   │  • Dashboard    │  │  • HEC          │  │  • Connectors   │                 │
│   └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
│                                                                                 │
│   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                 │
│   │   INSTANCE 4    │  │   INSTANCE 5    │  │   INSTANCE 6    │                 │
│   │                 │  │                 │  │                 │                 │
│   │  MITRE CALDERA  │  │   WINDOWS       │  │   JUPYTER       │                 │
│   │   (Ubuntu)      │  │   SERVER 2025   │  │   + VOILÀ       │                 │
│   │                 │  │                 │  │   (Ubuntu)      │                 │
│   │  • Server       │  │  • Sysmon       │  │                 │                 │
│   │  • Agents       │  │  • Wazuh Agent  │  │  • ML Model     │                 │
│   │  • Operations   │  │  • Splunk UF    │  │  • Dashboard    │                 │
│   └─────────────────┘  └─────────────────┘  └─────────────────┘                 │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘

## 📌 Nota
Versión: 1.0
Fecha: Julio 2026
Tema: Sistemas Expertos en Centros de Operaciones de Seguridad (SOC)

