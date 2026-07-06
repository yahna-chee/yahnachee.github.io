# Sistema experto de triage SOC — Phishing + Unauthorized Access / Elevation of Privilege

**Asignatura:** IA Aplicada a la Ciberseguridad — UTP, FISC, Licenciatura en Ciberseguridad
**Tema 6:** Sistemas expertos en Centros de Operaciones de Seguridad (SOC): entrenamiento, generación y automatización de reglas expertas

## 1. Alcance del proyecto

Este proyecto demuestra un sistema experto de triage de alertas que combina dos categorías de incidente:

- **Phishing** (MITRE ATT&CK T1566 — Phishing, T1204.002 — User Execution: Malicious File)
- **Unauthorized Access / Elevation of Privilege** (MITRE ATT&CK T1078 — Valid Accounts, T1548 — Abuse Elevation Control Mechanism)

Se eligió esta combinación porque ambas categorías cuentan con datasets públicos etiquetados y actuales,
reglas Sigma documentadas, mapeo directo a MITRE ATT&CK, y conectan con la bibliografía ya recolectada para
el tema (AACT — Automated Alert Classification and Triage; LLMs for Security Operations Centers: A
Comprehensive Survey; severity-based triage con kill chain attack graphs).

## 2. Arquitectura

```
PhiUSIIL (phishing URLs)  ---\
                               >---  Wazuh + reglas Sigma (T1566 / T1548 / T1078)
LANL auth + redteam        --/                |
                                               v
                                  Modelo de triage unificado (fusiona ambas señales)
                                        /                        \
                                       v                          v
                              Asistente LLM (apoyo SOC)      Shuffle (SOAR)
                                                                   |
                                                                   v
                                                     Respuesta automatizada
                                                (bloquear cuenta, aislar host)
```

## 3. Datasets

### Phishing — PhiUSIIL Phishing URL Dataset
- Fuente: UCI Machine Learning Repository. Prasad, A. & Chandra, S. (2024).
- Enlace: https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset
- 134,850 URLs legítimas y 100,945 URLs de phishing, con features extraídas del código fuente y de la URL.
- Se eligió sobre el clásico UCI-327 (2012, 11,055 muestras) porque es de 2024 — cumple el requisito de
  "últimos 5 años" de la guía — y refleja patrones de phishing más recientes.
- Acceso directo en Python vía `ucimlrepo` (ver notebook 01).

### Unauthorized Access / Elevation of Privilege — LANL Comprehensive, Multi-Source Cyber-Security Events
- Fuente: Los Alamos National Laboratory. Kent, A. (2015/2016).
- Enlace: https://csr.lanl.gov/data/cyber1/
- 58 días de logs de autenticación, procesos, DNS y flujo de red de una red corporativa real, con un archivo
  `redteam.txt` de verdad de terreno (ground truth) que marca eventos de compromiso de credenciales y
  movimiento lateral — exactamente lo que necesitamos para "unauthorized access / elevation of privilege".
- **Importante:** el dataset completo pesa ~87 GB. Para este proyecto se recomienda descargar únicamente
  `auth.txt.gz` (o un subconjunto de los primeros N millones de líneas) junto con `redteam.txt.gz`, y
  documentar explícitamente en el informe que se trabajó con una muestra — esto es una práctica aceptada y
  común en la literatura que usa este dataset (ver referencias en el notebook 02).

## 4. Reglas Sigma y mapeo MITRE ATT&CK

Ver carpeta `sigma_rules/`. Se incluyen dos reglas originales escritas para este proyecto, inspiradas en
patrones documentados públicamente:

- `phishing_office_macro_execution.yml` — detecta procesos de Office generando shells o scripts hijos,
  patrón clásico de payload de phishing (T1566.001, T1204.002).
- `privesc_suspicious_elevation.yml` — detecta intentos de elevación de privilegios vía herramientas de
  utilidad abusadas (T1548.001, T1548.002), inspirado en técnicas documentadas en el repositorio oficial de
  Sigma (ver referencia dentro del archivo).

Para explorar el repositorio completo y reglas ya probadas por la comunidad: https://github.com/SigmaHQ/sigma

## 5. Estructura de notebooks

| Notebook | Contenido |
|---|---|
| `01_phishing_detection.ipynb` | Carga PhiUSIIL, feature importance, entrenamiento XGBoost, métricas, guardado del modelo |
| `02_privilege_escalation_detection.ipynb` | Carga LANL (auth + redteam), feature engineering por usuario/ventana de tiempo, entrenamiento con balanceo de clases, métricas, guardado del modelo |
| `03_triage_fusion_soar_demo.ipynb` | Carga ambos modelos, define severidad unificada, simula alertas entrantes, dispara respuesta vía webhook de Shuffle, dashboard con `ipywidgets` para servir con Voilà |

## 6. Entorno de ejecución (sin consumir recursos locales)

- **Desarrollo y entrenamiento:** Kaggle Notebooks o Google Colab (ambos gratuitos, con GPU). PhiUSIIL se
  descarga directo con `ucimlrepo`; LANL requiere descarga manual del subconjunto elegido y subirlo como
  "Dataset" en Kaggle o a Google Drive en Colab.
- **Demo del día del examen:** publica este repositorio en https://mybinder.org/ y sirve
  `03_triage_fusion_soar_demo.ipynb` con Voilà (`voila 03_triage_fusion_soar_demo.ipynb`) para tener un
  dashboard limpio sin código visible.
- **SOAR:** despliega Shuffle localmente con Docker solo el día de la demo (no necesitas tenerlo corriendo
  todo el semestre) — https://shuffler.io/docs

## 7. Instalación local (opcional, solo si no usas Kaggle/Colab)

```bash
python -m venv venv
source venv/bin/activate       # En Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

## 8. Checklist antes de entregar

- [ ] Notebooks ejecutados de principio a fin con salidas visibles.
- [ ] Gráficos de feature importance, matriz de confusión y métricas exportados para el PPTX.
- [ ] Referencias de este README trasladadas a Zotero en formato APA 7 (mínimo 5 artículos indexados, máximo
      2 referencias web con fecha de consulta).
- [ ] `requirements.txt` verificado con `pip freeze > requirements.txt` tras ejecutar todo.
- [ ] Reglas Sigma y su mapeo MITRE ATT&CK explicados en la Sección 4 de la sustentación.
- [ ] Declaración de uso de IA generativa incluida en el informe, según Sección XII de la guía del examen.
