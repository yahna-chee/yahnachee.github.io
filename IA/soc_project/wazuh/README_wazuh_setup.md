# Configuración de Wazuh para Phishing + Unauthorized Access / Elevation of Privilege

Documentación oficial de referencia: https://documentation.wazuh.com/current/index.html
Despliegue en Docker: https://documentation.wazuh.com/current/deployment-options/docker/index.html

## 1. Despliegue rápido con Docker

```bash
git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.0
cd wazuh-docker/single-node
docker compose -f generate-indexer-certs.yml run --rm generator
docker compose up -d
```

Accede al dashboard en `https://localhost` (usuario/clave por defecto en la documentación oficial arriba).

## 2. Fuentes de log necesarias

Para esta combinación de casos necesitas dos flujos de eventos distintos:

### Phishing
- **Sysmon** en los endpoints Windows (Event ID 1 — Process Creation) para capturar el patrón
  "Office → cmd/powershell/mshta" que detecta `sigma_rules/phishing_office_macro_execution.yml`.
- Instala el agente Wazuh en el endpoint y habilita el módulo `windows_eventchannel` apuntando al canal
  `Microsoft-Windows-Sysmon/Operational`.

### Unauthorized Access / Elevation of Privilege
- Logs de autenticación (`auth.log` en Linux, o el canal de seguridad de Windows Event ID 4624/4625/4672
  para logons y asignación de privilegios especiales).
- Para simular el escenario del dataset LANL en un entorno de laboratorio, puedes reproducir eventos de
  autenticación sintéticos con el formato `auth.log` de Linux y alimentar el agente Wazuh vía `localfile`.

## 3. Convertir las reglas Sigma a reglas Wazuh

Wazuh no interpreta YAML de Sigma de forma nativa — hay que convertirlo con `sigma-cli` (motor pySigma) al
backend de OpenSearch/Wazuh, o traducirlo manualmente a XML de decoders/reglas. Ejemplo con `sigma-cli`:

```bash
pip install sigma-cli
sigma plugin install wazuh
sigma convert -t wazuh sigma_rules/phishing_office_macro_execution.yml
sigma convert -t wazuh sigma_rules/privesc_suspicious_elevation.yml
```

Esto genera la consulta/regla equivalente que puedes insertar en `local_rules_snippet.xml` (ver ejemplo en
este mismo directorio) o cargar como reglas personalizadas en `/var/ossec/etc/rules/local_rules.xml` dentro
del contenedor del manager.

## 4. Integración con el modelo de ML y con Shuffle

1. Wazuh genera la alerta cuando coincide con la regla convertida.
2. Un script (o el módulo de integraciones de Wazuh, `integrations/custom-*`) reenvía la alerta en JSON al
   endpoint de tu API de triage (el modelo entrenado en los notebooks 01/02, servido con FastAPI o similar).
3. El modelo devuelve la severidad; ese resultado se envía al webhook de Shuffle definido en el
   notebook 03 (`SHUFFLE_WEBHOOK_URL`), que ejecuta el playbook de respuesta correspondiente.

Consulta la documentación de integraciones de Wazuh para el paso 2:
https://documentation.wazuh.com/current/user-manual/manager/integration-with-external-apis.html
