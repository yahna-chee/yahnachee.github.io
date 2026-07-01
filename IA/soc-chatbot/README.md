# 📘 ASIGNATURA: INTELIGENCIA ARTIFICIAL APLICADA A CIBERSEGURIDAD
# 🛡️ PARCIAL 3: SOC Chatbot de IA v2

## 📌 Descripción del Proyecto
Este proyecto implementa un Sistema de Consulta de Documentación para Centros de Operaciones de Seguridad (SOC) utilizando técnicas de Generación Aumentada por Recuperación (RAG). El sistema permite a analistas de seguridad y estudiantes consultar información técnica de manuales y guías de ciberseguridad mediante un chatbot conversacional.

## 🎯 Objetivo de Aprendizaje
Demostrar la aplicación práctica de modelos de Inteligencia Artificial (IA) y Aprendizaje Automatizado en el ámbito de la ciberseguridad, específicamente en la automatización de consultas de documentación técnica para Centros de Operaciones de Seguridad (SOC).

## 🚀 Características Principales
<table>
  <thead>
    <tr>
      <th>🔧 Característica</th>
      <th>📝 Descripción</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>🤖 Modelo LLM</strong></td>
      <td>Llama 3.2 (3B) optimizado para GPU T4 de Google Colab</td>
    </tr>
    <tr>
      <td><strong>📚 Embeddings</strong></td>
      <td><code>all-MiniLM-L6-v2</code> para búsqueda semántica eficiente</td>
    </tr>
    <tr>
      <td><strong>💾 Vector Store</strong></td>
      <td>ChromaDB para almacenamiento y recuperación de documentos</td>
    </tr>
    <tr>
      <td><strong>🌐 Interfaz Web</strong></td>
      <td>Interfaz simple y funcional accesible por navegador</td>
    </tr>
    <tr>
      <td><strong>⚡ GPU Acceleration</strong></td>
      <td>Optimizado para ejecución en GPU T4 de Colab</td>
    </tr>
    <tr>
      <td><strong>📄 Procesamiento PDF</strong></td>
      <td>Extracción automática de texto y chunking inteligente</td>
    </tr>
    <tr>
      <td><strong>🔍 RAG Pipeline</strong></td>
      <td>Recuperación de contexto y generación de respuestas</td>
    </tr>
  </tbody>
</table>

## 🛠️ Tecnologías Utilizadas
<img width="2801" height="917" alt="deepseek_mermaid_20260630_c58d40" src="https://github.com/user-attachments/assets/630aa8f0-e593-4724-804d-f613ff3bc77c" />

<div style="display: flex; flex-wrap: wrap; gap: 10px;">
<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>🧠 LLM</strong><br>
    Llama 3.2 (3B)
</div>
  
<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>⚙️ Framework</strong><br>
    Ollama 0.30.8+
</div>

<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>📚 Embeddings</strong><br>
    Sentence Transformers 2.2.2
</div>

<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>🗄️ Vector DB</strong><br>
    ChromaDB 0.5.0
</div>

<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>🌐 Web Server</strong><br>
    Flask 3.0.0
</div>

<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>📄 PDF Processing</strong><br>
    PyPDF 3.17.4
</div>

<div style="background: #1a2a4a; padding: 10px; border-radius: 8px; border: 1px solid #1e3a5f;">
    <strong>☁️ Runtime</strong><br>
    Google Colab (GPU T4)
</div>

</div>

## 📂 Estructura del Proyecto
```bash
IA/
├── soc-chatbot/
│   └── capturas/                    # 📸 Capturas de pantalla de la interfaz, consultas y resultados del chatbot
│   └── docs/                        # 📄 Documentos PDF fuente (9 playbooks de https://www.incidentresponse.com/playbooks/)            
│       ├── 01_malware_outbreak_playbook.pdf
│       ├── 02_phishing_playbook.pdf
│       ├── 03_data_theft_playbook.pdf
│       ├── 04_virus_outlook_playbook.pdf
│       ├── 05_denial_of_service_playbook.pdf
│       ├── 06_unauthorized_access_playbook.pdf
│       ├── 07_elevation_of_privilege_playbook.pdf
│       ├── 08_root_access_playbook.pdf
│       └── 09_improper_usage_playbook.pdf 
│   └── evidencias/                  # 🧪 Evidencias técnicas de pruebas y validación del sistema
├── README.md                        # 📘 Documentación del proyecto
└── soc_chatbot_colab.ipynb          # 🐍 Código principal para Colab
```

## ⚙️ Funcionamiento del Sistema
1. Procesamiento de documentos (RAG)
PDF → Extracción de texto → Chunking (300 palabras) → Embeddings → ChromaDB

2. Consulta del usuario
Pregunta → Embeddings → Búsqueda en ChromaDB (top_k=2) → Contexto → Llama 3.2 → Respuesta

3. Optimizaciones implementadas
- **Parámetros de inferencia ajustados:**
  - `temperature: 0.1` (respuestas precisas y deterministas)
  - `num_predict: 180` (respuestas concisas para reducir latencia)
  - `num_ctx: 2048` (ventana de contexto compacta para GPU)
- **Aceleración por hardware:**
  - GPU T4 de Google Colab
  - Paralelización de consultas
- **Interfaz de usuario:**
  - Acceso por navegador web
  - Diseño responsive y minimalista
  - Feedback visual de procesamiento

## 🚀 Instalación y Ejecución
- **Opción 1: Google Colab (recomendada)**
# 1. Abrir Google Colab:
     - Ve a Google Colab
     - Crea un nuevo notebook
# 2. Configurar GPU:
     - Entorno de ejecución → Cambiar tipo de entorno
     - Seleccionar GPU (T4)
# 3. Ejecutar el código:
     - Copia y pega el código completo en una celda
     - Ejecuta la celda (Shift + Enter)
# 4. Acceder al chatbot:
     - Al final de la ejecución, se generará un enlace público
     - Haz clic en el enlace o cópialo en tu navegador

- **Opción 2: Local (requiere GPU NVIDIA)**
# 1. Instalar Ollama
curl -fsSL https://ollama.com/install.sh | sh

# 2. Descargar el modelo
ollama pull llama3.2

# 3. Instalar dependencias Python
pip install chromadb sentence-transformers pypdf flask

# 4. Clonar repositorio
git clone https://github.com/yahna-chee/yahnachee.github.io/tree/main/IA/soc-chatbot

# 5. Ejecutar
python soc_chatbot_local.py

## 📊 Evaluación y Resultados
| Métrica | Valor | Descripción |
|:--------|:------|:------------|
| **⏱️ Tiempo de respuesta** | ~2-3 segundos | Con GPU T4 de Google Colab |
| **🎯 Precisión** | Alta | Basada en recuperación exacta de documentos |
| **📝 Tokens por consulta** | ~180 | Respuestas concisas y directas |
| **📄 Documentos indexados** | 9 playbooks | ~50+ chunks por documento |

## 🧪 Consultas de Prueba
| # | Pregunta | Respuesta Esperada | Fuente |
|---|----------|-------------------|--------|
| 1 | *"¿Cuáles son las fases de respuesta a incidentes?"* | Preparación, Identificación, Contención, Erradicación, Recuperación, Lecciones Aprendidas | `01_malware_outbreak_playbook.pdf` |
| 2 | *"¿Qué es un playbook de respuesta?"* | Procedimiento estructurado para manejar incidentes de seguridad | `02_phishing_playbook.pdf` |
| 3 | *"¿Cómo se usa MITRE ATT&CK en un SOC?"* | Framework de conocimiento de tácticas y técnicas de adversarios | `06_unauthorized_access_playbook.pdf` |

## 🔧 Personalización
# Cambiar modelo
# En el código, modificar la línea 33:
MODELO_LLM = "llama3.2"  # Opciones: "phi3:mini", "tinyllama", "llama3.2"

# Agregar documentos
- Colocar los PDFs en la carpeta IA/soc-chatbot/docs/
- Ejecutar nuevamente el código
- El sistema indexará automáticamente los nuevos documentos

# Ajustar parámetros
``bash
# En la función consultar(), línea 92-95:
"options": {
    "temperature": 0.1,      # 0.0-1.0 (menos= más determinista)
    "num_predict": 180,      # Máximo de tokens de respuesta
    "num_ctx": 2048          # Ventana de contexto (máximo 4096)
}

## 🧠 Fundamentos Técnicos (Tema 6: Sistemas Expertos en SOC)
| Principio | Implementación en el Proyecto |
|:----------|:------------------------------|
| **📚 Base de Conocimiento** | 9 playbooks de respuesta a incidentes (PDFs) |
| **⚙️ Motor de Inferencia** | Modelo Llama 3.2 + Pipeline RAG |
| **💻 Interfaz de Usuario** | Flask Web + navegador (Colab tunnel) |
| **🔍 Explicabilidad** | Muestra las fuentes de información utilizadas |

### 🎯 Alineación con MITRE ATT&CK

| Táctica MITRE | Aplicación en el Proyecto |
|:--------------|:--------------------------|
| **T1071** | Uso de HTTP/HTTPS para comunicación |
| **T1048** | Prevención de exfiltración mediante consultas controladas |

### 📚 Relación con el Marco NIST

| Fase NIST | Playbook Asociado |
|:----------|:------------------|
| **1. Preparación** | Todos los playbooks |
| **2. Detección y Análisis** | `01-09` |
| **3. Contención** | `01, 02, 03, 05, 06` |
| **4. Erradicación** | `01, 03, 04, 07, 08` |
| **5. Recuperación** | `01, 02, 03, 05` |
| **6. Lecciones Aprendidas** | Todos los playbooks |

## 📚 Fuentes de Información
Los documentos utilizados en este proyecto provienen de:
# https://www.incidentresponse.com/playbooks/
- 9 playbooks de respuesta a incidentes
- Basados en el marco NIST (7 fases)
- Cobertura de patrones de ataque comunes

## 🤝 Contribución
Este proyecto fue desarrollado como parte del curso IA Aplicada a la Ciberseguridad.
- **Estudiante**: Yahna Chee

## 📊 Diagrama de Arquitectura
<img width="4254" height="2676" alt="deepseek_mermaid_20260630_45bc68" src="https://github.com/user-attachments/assets/63cf0ab6-2644-4e00-89cd-96a3ffb206d9" />

## 📊 Diagrama de Arquitectura

```mermaid
graph TB
    classDef usuario fill:#f9f,stroke:#333,stroke-width:2px;
    classDef colab fill:#bbf,stroke:#33f,stroke-width:2px;
    classDef rag fill:#bfb,stroke:#3a3,stroke-width:2px;
    classDef fuentes fill:#ffb,stroke:#aa3,stroke-width:2px;
    classDef ollama fill:#fbb,stroke:#a33,stroke-width:2px;

    subgraph "👤 USUARIO"
        A[Navegador Web]:::usuario
    end

    subgraph "🌐 COLAB - ENTORNO DE EJECUCIÓN"
        B[Flask Server<br>HTTP Requests]:::colab
        C[Sistema RAG]:::colab
        
        subgraph "🧠 SISTEMA RAG"
            D[1. Embeddings<br>all-MiniLM-L6-v2]:::rag
            E[2. Vector Store<br>ChromaDB<br>top_k=2]:::rag
            F[3. Contexto<br>Documentos recuperados]:::rag
            G[4. Generación<br>Llama 3.2 (GPU T4)]:::rag
        end
        
        subgraph "📚 FUENTES DE CONOCIMIENTO"
            H[PDFs<br>9 Playbooks]:::fuentes
            I[Text Extraction<br>PyPDF]:::fuentes
            J[Chunking<br>300 palabras]:::fuentes
        end
    end

    subgraph "🦙 OLLAMA"
        K[API Server<br>localhost:11434]:::ollama
        L[Modelo LLM<br>Llama 3.2]:::ollama
    end

    A -->|HTTP| B
    B -->|Consulta| C
    C --> D
    D -->|Embeddings| E
    E -->|top_k=2| F
    F -->|Contexto| G
    G -->|Prompt + Contexto| K
    K --> L
    L -->|Respuesta| G
    G -->|Respuesta final| B
    B -->|HTTP| A
    
    H -->|Procesamiento| I
    I --> J
    J --> D

## 🎓 Reflexión Académica
Este proyecto demuestra la aplicación práctica de técnicas de Inteligencia Artificial en el ámbito de la ciberseguridad, específicamente en la automatización de consultas de documentación técnica para SOC. La implementación de un sistema RAG con modelo Llama 3.2 optimizado para GPU muestra cómo la IA puede reducir significativamente los tiempos de respuesta y mejorar la eficiencia de los analistas de seguridad.

### 🛡️ SOC Chatbot de IA v2 | Parcial 3 - IA Aplicada a Ciberseguridad
