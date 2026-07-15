\## Pipeline de emulación, ingesta y orquestación del SOC (flujo operacional)



El ecosistema diseñado realiza análisis predictivo y simula un ciclo completo de vida de un incidente en un SOC moderno. El flujo operacional representado en la arquitectura se desglosa bajo los siguientes comentarios de diseño e ingeniería:



1\. \*\*Fase de ejecución y ataque (emulación controlada):\*\*

&#x20;  \* El ciclo inicia con \*\*MITRE Caldera\*\*, plataforma utilizada para ejecutar tácticas y técnicas adversarias de manera automatizada. Al emular vectores como phishing o escalación de privilegios directamente en el host objetivo, se elimina la aleatoriedad y se asegura que las amenazas sigan la taxonomía estándar de la industria.



2\. \*\*Captura y generación de telemetría (endpoints):\*\*

&#x20;  \* La actividad maliciosa es interceptada localmente a nivel de sistema operativo mediante \*\*Windows Event Viewer\*\* enriquecido con \*\*Sysmon\*\* (registrando Event IDs críticos como la creación de procesos `ID 1` o conexiones de red `ID 3`). Esto garantiza una visibilidad granular y cruda de los artefactos antes de ser transmitidos.



3\. \*\*Ingesta híbrida y correlación (SIEM):\*\*

&#x20;  \* El pipeline está diseñado para ser agnóstico y tolerante a entornos empresariales mixtos. La telemetría se bifurca mediante dos mecanismos de transporte:

&#x20;    \* \*\*Wazuh Agent ➔ Wazuh Manager:\*\* Para el procesamiento basado en software de código abierto, ejecutando reglas de correlación activa y alertas estáticas en tiempo real.

&#x20;    \* \*\*Splunk Universal Forwarder ➔ Splunk Enterprise:\*\* Para entornos corporativos de alta indexación, permitiendo búsquedas avanzadas mediante queries \*\*SPL (Splunk Processing Language)\*\*, generación de alertas y el despacho inmediato de cargas útiles informativas hacia el orquestador a través de \*\*Webhooks\*\*.



4\. \*\*Respuesta y contención automatizada (SOAR):\*\*

&#x20;  \* El eslabón final del flujo se consolida en el entorno \*\*Splunk SOAR\*\*. Al recibir la alerta enriquecida, el sistema activa de manera autónoma un \*\*Playbook de contención automática\*\* para mitigar la amenaza en milisegundos (aislamiento de red, revocación de credenciales o bloqueo de IPs), cerrando el ciclo de respuesta ante incidentes sin requerir intervención manual constante del analista.



\### Diagrama de arquitectura de telemetría y orquestación



```mermaid

graph TD

&#x20;   %% Estilos de nodos

&#x20;   classDef ataque fill:#ffcccc,stroke:#cc0000,stroke-width:2px,color:#000;

&#x20;   classDef endpoint fill:#e1f5fe,stroke:#0288d1,stroke-width:2px,color:#000;

&#x20;   classDef siem fill:#fff3e0,stroke:#f57c00,stroke-width:2px,color:#000;

&#x20;   classDef soar fill:#e8f5e9,stroke:#388e3c,stroke-width:2px,color:#000;



&#x20;   %% Flujo de la arquitectura

&#x20;   A\[MITRE Caldera <br><i>Ejecuta el ataque</i>] --> B\[Windows Event Viewer / Sysmon <br><i>Registra telemetría local: Event IDs 1, 3, etc.</i>]

&#x20;   

&#x20;   B --> C\[Wazuh Agent]

&#x20;   B --> D\[Splunk Universal Forwarder]

&#x20;   

&#x20;   C --> E\[Wazuh Manager <br><i>Reglas de correlación activa y alertas</i>]

&#x20;   

&#x20;   D --> F\[Splunk Enterprise <br><i>Query SPL, alertas y envío de Webhooks</i>]

&#x20;   F --> G\[Splunk SOAR <br><i>Playbook de contención automática</i>]



&#x20;   %% Aplicación de estilos

&#x20;   class A ataque;

&#x20;   class B endpoint;

&#x20;   class C,D,e,F siem;

&#x20;   class G soar;



\### Diagrama de flujo de los datos

┌─────────────────────────────────────────────────────────────────────────────────────┐

│                                                                                     │

│  ┌─────────────────────────────────────────────────────────────────────────────┐   │

│  │                        1. ATTACK EMULATION                                   │   │

│  │                                                                             │   │

│  │                     ┌─────────────────────────┐                             │   │

│  │                     │     MITRE CALDERA       │                             │   │

│  │                     │   (Adversary Emulation) │                             │   │

│  │                     └───────────┬─────────────┘                             │   │

│  │                                 │                                           │   │

│  │                      ┌──────────▼──────────┐                               │   │

│  │                      │   T1566 │ Phishing   │                               │   │

│  │                      │   T1068 │ Priv. Esc. │                               │   │

│  │                      │   T1110 │ Brute Force│                               │   │

│  │                      └──────────┬──────────┘                               │   │

│  └─────────────────────────────────┼───────────────────────────────────────────┘   │

│                                    │                                               │

│                                    ▼                                               │

│  ┌─────────────────────────────────────────────────────────────────────────────┐   │

│  │                        2. TELEMETRY (Windows Server 2025)                   │   │

│  │                                                                             │   │

│  │          ┌───────────────────────────────────────────────┐                 │   │

│  │          │              SYSMON + EVENT VIEWER            │                 │   │

│  │          │  ┌───────────────────────────────────────┐    │                 │   │

│  │          │  │ Event ID 1  │ Process Creation        │    │                 │   │

│  │          │  │ Event ID 3  │ Network Connection      │    │                 │   │

│  │          │  │ Event ID 7  │ Image Loaded            │    │                 │   │

│  │          │  │ Event ID 11│ File Creation            │    │                 │   │

│  │          │  │ Event ID 13│ Registry Modification    │    │                 │   │

│  │          │  └───────────────────────────────────────┘    │                 │   │

│  │          └───────────────────┬───────────────────────────┘                 │   │

│  │                              │                                             │   │

│  │           ┌──────────────────┼──────────────────┐                          │   │

│  │           │                  │                  │                          │   │

│  │           ▼                  ▼                  ▼                          │   │

│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐              │   │

│  │  │   WAZUH AGENT   │ │  SPLUNK FORWARDER│ │   RAW LOGS     │              │   │

│  │  │  (Event Forward)│ │ (Event Forward)  │ │   (Archive)    │              │   │

│  │  └────────┬────────┘ └────────┬────────┘ └─────────────────┘              │   │

│  └───────────┼───────────────────┼────────────────────────────────────────────┘   │

│              │                   │                                                │

│              ▼                   ▼                                                │

│  ┌─────────────────────────────────────────────────────────────────────────────┐   │

│  │                        3. SIEM \& CORRELATION                                │   │

│  │                                                                             │   │

│  │   ┌─────────────────────────┐          ┌─────────────────────────┐         │   │

│  │   │        WAZUH            │          │       SPLUNK            │         │   │

│  │   │   (Open Source SIEM)    │          │      Enterprise         │         │   │

│  │   │                         │          │                         │         │   │

│  │   │  ┌───────────────────┐  │          │  ┌───────────────────┐  │         │   │

│  │   │  │ Active Correlation│  │          │  │  SPL Queries      │  │         │   │

│  │   │  │ MITRE ATT\&CK Map  │  │          │  │  Saved Searches   │  │         │   │

│  │   │  │ Alert Generation  │  │          │  │  Alert Webhooks   │  │         │   │

│  │   │  └───────────────────┘  │          │  └───────────────────┘  │         │   │

│  │   └───────────┬─────────────┘          └───────────┬─────────────┘         │   │

│  └───────────────┼─────────────────────────────────────┼───────────────────────┘   │

│                  │                                     │                           │

│                  └─────────────────┬───────────────────┘                           │

│                                    │                                               │

│                                    ▼                                               │

│  ┌─────────────────────────────────────────────────────────────────────────────┐   │

│  │                        4. SOAR AUTOMATION                                  │   │

│  │                                                                             │   │

│  │                     ┌─────────────────────────┐                             │   │

│  │                     │     SPLUNK SOAR         │                             │   │

│  │                     │   (Playbook Engine)     │                             │   │

│  │                     └───────────┬─────────────┘                             │   │

│  │                                 │                                           │   │

│  │                ┌────────────────┼────────────────┐                          │   │

│  │                │                │                │                          │   │

│  │                ▼                ▼                ▼                          │   │

│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐              │   │

│  │  │  Investigate    │ │   Enrichment    │ │    Respond     │              │   │

│  │  │  IP Reputation  │ │   VirusTotal    │ │  Block IP      │              │   │

│  │  │  Query Logs     │ │   OTX / MISP    │ │  Isolate Host  │              │   │

│  │  │  Threat Intel   │ │   GeoIP Lookup  │ │  Notify Team   │              │   │

│  │  └─────────────────┘ └─────────────────┘ └─────────────────┘              │   │

│  └─────────────────────────────────────────────────────────────────────────────┘   │

│                                                                                     │

│  ══════════════════════════════════════════════════════════════════════════════════ │

│                                                                                     │

│  ┌─────────────────────────────────────────────────────────────────────────────┐   │

│  │                        5. DASHBOARD \& ML                                    │   │

│  │                                                                             │   │

│  │   ┌─────────────────────────────────────────────────────────────────────┐   │   │

│  │   │                    VOILÀ DASHBOARD + JUPYTER                       │   │   │

│  │   │                                                                     │   │   │

│  │   │  ┌─────────────────┐    ┌─────────────────┐                         │   │   │

│  │   │  │  MTTD / MTTR    │    │  Attack Summary  │                         │   │   │

│  │   │  │  📉 45s │ 120s  │    │  🔴 22 Attacks  │                         │   │   │

│  │   │  └─────────────────┘    └─────────────────┘                         │   │   │

│  │   │                                                                     │   │   │

│  │   │  ┌─────────────────┐    ┌─────────────────┐                         │   │   │

│  │   │  │  Precision      │    │  MITRE Mapping  │                         │   │   │

│  │   │  │  🎯 92% │ 65%   │    │  T1110  T1059   │                         │   │   │

│  │   │  └─────────────────┘    └─────────────────┘                         │   │   │

│  │   └─────────────────────────────────────────────────────────────────────┘   │   │

│  └─────────────────────────────────────────────────────────────────────────────┘   │

│                                                                                     │

└─────────────────────────────────────────────────────────────────────────────────────┘



\### Pipeline del modelo ML y dataset público

┌─────────────────────────────────────────────────────────────────────────────────┐

│                                                                                 │

│                          INPUT SOURCES                                          │

│                                                                                 │

│   ┌─────────────────────────┐         ┌─────────────────────────┐              │

│   │      KAGGLE DATASET     │         │   REAL ENVIRONMENT      │              │

│   │    (Microsoft GUIDE)    │         │     METRICS \& LOGS      │              │

│   │                         │         │                         │              │

│   │  • 1M+ Security Events  │         │  • Sysmon Events        │              │

│   │  • MITRE ATT\&CK Labels  │         │  • Wazuh Alerts         │              │

│   │  • TP/FP Classification │         │  • Splunk Data          │              │

│   └───────────┬─────────────┘         └───────────┬─────────────┘              │

│               │                                   │                             │

│               └───────────────┬───────────────────┘                             │

│                               │                                                 │

│                               ▼                                                 │

│   ┌─────────────────────────────────────────────────────────────────────────┐   │

│   │                                                                         │   │

│   │                    ML CLASSIFICATION MODEL                              │   │

│   │                        Random Forest                                    │   │

│   │                                                                         │   │

│   │   ┌─────────────────────────────────────────────────────────────────┐   │   │

│   │   │   Features: alert\_count, severity, source\_ip, dest\_ip, etc.   │   │   │

│   │   │   Target: is\_attack (True / False)                            │   │   │

│   │   └─────────────────────────────────────────────────────────────────┘   │   │

│   └───────────────────────────────┬─────────────────────────────────────────┘   │

│                                   │                                             │

│                                   ▼                                             │

│   ┌─────────────────────────────────────────────────────────────────────────┐   │

│   │                                                                         │   │

│   │                    EVALUATION METRICS                                   │   │

│   │                                                                         │   │

│   │   ┌─────────────────────────────────────────────────────────────────┐   │   │

│   │   │  Precision  │  Recall   │  F1-Score   │  Accuracy               │   │   │

│   │   │   92%       │   88%     │   0.90      │   94%                   │   │   │

│   │   └─────────────────────────────────────────────────────────────────┘   │   │

│   └─────────────────────────────────────────────────────────────────────────┘   │

│                                                                                 │

└─────────────────────────────────────────────────────────────────────────────────┘



\### Dashboard comparativo

┌─────────────────────────────────────────────────────────────────────────────────┐

│                                                                                 │

│                    📊 TRADITIONAL vs EXPERT SYSTEM                              │

│                                                                                 │

│   ┌─────────────────────────────────────────────────────────────────────────┐   │

│   │                                                                         │   │

│   │    Metric               Traditional          Expert System    Improve   │   │

│   │    ──────────────────────────────────────────────────────────────────    │   │

│   │    MTTD (sec)                120                   45          62% ✅    │   │

│   │    MTTR (sec)                300                   150         50% ✅    │   │

│   │    Precision (%)              65                   92          41% ✅    │   │

│   │    Recall (%)                 70                   88          26% ✅    │   │

│   │    F1-Score                  0.60                 0.88         47% ✅    │   │

│   │    FP Rate (%)                35                   8           77% ✅    │   │

│   │                                                                         │   │

│   └─────────────────────────────────────────────────────────────────────────┘   │

│                                                                                 │

│   ┌─────────────────────────────┐        ┌─────────────────────────────┐       │

│   │         MTTD COMPARISON     │        │      PRECISION COMPARISON   │       │

│   │                             │        │                             │       │

│   │  120s ████████████████████  │        │  92% ████████████████████   │       │

│   │   45s ██████                │        │  65% ██████████             │       │

│   │                             │        │                             │       │

│   │   Traditional   Expert      │        │   Expert      Traditional   │       │

│   └─────────────────────────────┘        └─────────────────────────────┘       │

│                                                                                 │

└─────────────────────────────────────────────────────────────────────────────────┘



\### Despliegue de la arquitectura

┌─────────────────────────────────────────────────────────────────────────────────┐

│                                                                                 │

│                              AWS CLOUD                                          │

│                                                                                 │

│   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                │

│   │   INSTANCE 1    │  │   INSTANCE 2    │  │   INSTANCE 3    │                │

│   │                 │  │                 │  │                 │                │

│   │   WAZUH SIEM    │  │   SPLUNK        │  │   SPLUNK SOAR   │                │

│   │   (Ubuntu)      │  │   Enterprise    │  │   (Amazon Linux)│                │

│   │                 │  │   (Ubuntu)      │  │                 │                │

│   │  • Manager      │  │  • Indexer     │  │  • Playbooks    │                │

│   │  • Indexer      │  │  • Search Head │  │  • Assets       │                │

│   │  • Dashboard    │  │  • HEC         │  │  • Connectors   │                │

│   └─────────────────┘  └─────────────────┘  └─────────────────┘                │

│                                                                                 │

│   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐                │

│   │   INSTANCE 4    │  │   INSTANCE 5    │  │   INSTANCE 6    │                │

│   │                 │  │                 │  │                 │                │

│   │   MITRE CALDERA │  │   WINDOWS       │  │   JUPYTER       │                │

│   │   (Ubuntu)      │  │   SERVER 2025   │  │   + VOILÀ       │                │

│   │                 │  │                 │  │   (Ubuntu)      │                │

│   │  • Server       │  │  • Sysmon      │  │                 │                │

│   │  • Agents       │  │  • Wazuh Agent │  │  • ML Model     │                │

│   │  • Operations   │  │  • Splunk UF   │  │  • Dashboard    │                │

│   └─────────────────┘  └─────────────────┘  └─────────────────┘                │

│                                                                                 │

└─────────────────────────────────────────────────────────────────────────────────┘



\### Nota:

Versión: 1.0 | Fecha: Julio 2026 | Tema 6

