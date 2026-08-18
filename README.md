# wazuh-sysmon-soc-homelab
Lab de SOC práctico utilizando Wazuh SIEM y Sysmon en Windows 10 para recolección de telemetría avanzada, detección de ejecuciones y Threat Hunting.

# SOC Homelab: Wazuh, Sysmon y Threat Hunting

Laboratorio práctico orientado a **Security Operations Center (SOC)** para la recolección de telemetría, monitoreo de endpoints, análisis de eventos y detección de actividad potencialmente maliciosa.

El entorno integra **Wazuh SIEM/XDR** con **Sysmon** sobre un endpoint Windows 10, permitiendo centralizar eventos de seguridad y realizar investigaciones mediante el análisis de procesos, archivos y comandos ejecutados.

---

## Descripción

El objetivo principal del laboratorio fue construir un entorno controlado que permita simular un flujo básico de trabajo de un analista SOC:

```text
Endpoint Windows
       │
       │ Sysmon + Wazuh Agent
       ▼
Wazuh Manager / SIEM
       │
       ▼
Recolección y correlación de eventos
       │
       ▼
Threat Hunting
       │
       ▼
Análisis de indicadores
```

Durante el laboratorio se configuró la recolección de eventos de Windows mediante **Sysmon**, se integraron dichos eventos con Wazuh y posteriormente se realizaron actividades de búsqueda y análisis desde la consola del SIEM.

---

## Arquitectura del laboratorio

El entorno fue desplegado utilizando máquinas virtuales dentro de **Oracle VirtualBox**.

| Componente | Sistema | Dirección IP | Función |
|---|---|---:|---|
| Wazuh Server | Linux | `10.0.2.6` | SIEM / Manager |
| Windows Endpoint | Windows 10 | `10.0.2.5` | Endpoint monitoreado |
| Kali Linux | Kali Linux | `10.0.2.7` | Simulación de actividad |

### Topología

![Configuración de red](./nat_network_setup.png)

La arquitectura permite generar actividad desde un entorno controlado y observar cómo los eventos son recolectados y procesados por el servidor Wazuh.

---

# 1. Despliegue de Wazuh

Se realizó el despliegue del servidor Wazuh en un entorno virtualizado.

El servidor funciona como punto central para la recepción, procesamiento y análisis de los eventos generados por los endpoints monitoreados.

![Wazuh Server](./wazuh_server_running.png)

### Componentes utilizados

- Wazuh Manager
- Wazuh Agent
- Wazuh Dashboard
- Oracle VirtualBox
- Linux

---

# 2. Configuración del endpoint Windows

Se preparó una máquina virtual con **Windows 10** como endpoint de laboratorio.

Este equipo representa un activo dentro de una infraestructura empresarial que será monitoreado desde el SOC.

![Windows 10 Endpoint](./Windows10-SOC-Victim.png)

Posteriormente se instaló el agente de Wazuh para permitir el envío de eventos hacia el servidor.

![Wazuh Agent Instalado](./wazuh_agent_installed.png)

Una vez completado el registro, se verificó desde la consola que el agente se encontrara correctamente conectado y en estado **Active**.

![Wazuh Agent Conectado](./wazuh_agent_connected.png)

---

# 3. Integración de Sysmon

Para obtener mayor visibilidad sobre la actividad del endpoint se instaló **Sysmon (System Monitor)**.

Sysmon permite registrar eventos relacionados con procesos, creación de archivos, conexiones de red y otras actividades relevantes para investigaciones de seguridad.

![Sysmon Instalado](./sysmon_installed.png)

### Integración con Wazuh

Se configuró el agente de Wazuh para recopilar eventos provenientes del canal:

```text
Microsoft-Windows-Sysmon/Operational
```

Esto permite centralizar en Wazuh la telemetría generada por Sysmon.

![Integración Sysmon con Wazuh](./sysmon_wazuh_integration.png)

---

# 4. Simulación de actividad y generación de eventos

Una vez configurado el entorno, se generó actividad controlada sobre el endpoint Windows con el objetivo de validar la capacidad de detección y recolección de eventos.

Se utilizó PowerShell para ejecutar un archivo de prueba y generar eventos observables mediante Sysmon.

### Respuesta de Microsoft Defender

Durante la simulación también se observó la respuesta del mecanismo de protección integrado de Windows.

![Respuesta de Microsoft Defender](./defender_blocking_threat.png)

### Ejecución mediante PowerShell

Se realizó la ejecución controlada del proceso desde PowerShell para generar telemetría que posteriormente pudiera ser investigada desde Wazuh.

![Ejecución mediante PowerShell](./process_execution_powershell.png)

---

# 5. Threat Hunting en Wazuh

Con los eventos disponibles en el SIEM se realizó una búsqueda orientada a identificar actividad relacionada con el archivo utilizado durante la simulación.

Se utilizó el filtro:

```text
*factura.exe*
```

El objetivo fue localizar los eventos asociados y analizar la información disponible sobre la ejecución del proceso.

### Detección del evento

Wazuh identificó la creación del proceso y generó una alerta asociada a la actividad observada.

En el análisis se pudo revisar información como:

- Regla que generó la alerta.
- Nivel de severidad.
- Proceso ejecutado.
- Usuario asociado.
- Identificador del proceso.
- Ruta del archivo.
- Información relacionada con el evento Sysmon.

![Alerta de Wazuh](./wazuh_sysmon_alert_details.png)

---

# 6. Análisis del evento en formato JSON

Como parte del proceso de investigación se inspeccionó el payload completo del evento en formato JSON.

Este análisis permitió revisar con mayor detalle la información recopilada por Sysmon y procesada por Wazuh.

Entre los datos relevantes se encuentran:

- Nombre del proceso.
- PID.
- Ruta de ejecución.
- Usuario.
- Árbol de procesos.
- Información temporal del evento.
- Datos adicionales proporcionados por Sysmon.

![Detalles del evento Sysmon](./sysmon_json_event_details.png)

Este tipo de información resulta especialmente útil durante el **triage** de alertas, ya que permite pasar de una alerta general a un análisis más detallado de la actividad observada.

---

# 7. Flujo de análisis SOC

El laboratorio permitió practicar un flujo de investigación similar al utilizado en un entorno SOC:

```text
Generación de actividad
        │
        ▼
Sysmon registra el evento
        │
        ▼
Wazuh Agent recopila la información
        │
        ▼
Wazuh Manager procesa el evento
        │
        ▼
Reglas de detección
        │
        ▼
Generación de alerta
        │
        ▼
Threat Hunting
        │
        ▼
Análisis del evento
        │
        ▼
Triage e investigación
```

Este flujo representa las principales etapas trabajadas durante el laboratorio: **telemetría, detección, búsqueda y análisis**.

---

# 8. Habilidades demostradas

### SIEM / XDR

- Despliegue de Wazuh.
- Registro y administración de agentes.
- Recolección centralizada de eventos.
- Consulta y análisis de alertas.
- Investigación desde el dashboard.

### Endpoint Security

- Instalación y configuración de Sysmon.
- Recolección de eventos de Windows.
- Análisis de creación de procesos.
- Análisis de ejecución mediante PowerShell.
- Revisión de eventos relacionados con archivos.

### Threat Hunting

- Búsqueda de eventos mediante filtros.
- Identificación de actividad asociada a un archivo.
- Análisis de alertas.
- Inspección de eventos en formato JSON.
- Identificación de indicadores relevantes para investigación.

### Infraestructura

- Oracle VirtualBox.
- Windows 10.
- Linux.
- Redes virtualizadas.
- Comunicación entre agentes y servidor SIEM.

---

# 9. Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| **Wazuh** | SIEM / XDR y monitoreo de endpoints |
| **Sysmon** | Generación de telemetría avanzada de Windows |
| **Windows 10** | Endpoint monitoreado |
| **Kali Linux** | Simulación de actividad |
| **PowerShell** | Generación controlada de eventos |
| **Oracle VirtualBox** | Virtualización del laboratorio |
| **JSON** | Análisis estructurado de eventos |

---

# 10. Evidencias del laboratorio

Las evidencias utilizadas durante el laboratorio se encuentran en el repositorio:

```text
/
├── README.md
│
├── 00-soc-dashboard.png
├── 01-trigger-config.png
├── 02-script-config.png
├── 03-telegram-alert.png
├── 04-action-log.png
├── 05-terminal-evidence.png
│
├── defender_blocking_threat.png
├── nat_network_setup.png
├── process_execution_powershell.png
├── sysmon_installed.png
├── sysmon_json_event_details.png
├── sysmon_wazuh_integration.png
├── wazuh_agent_connected.png
├── wazuh_agent_installed.png
├── wazuh_server_running.png
├── wazuh_sysmon_alert_details.png
└── Windows10-SOC-Victim.png
```

---

# 11. Conclusiones

Este laboratorio permitió construir y analizar un entorno SOC funcional basado en **Wazuh y Sysmon**, trabajando sobre un endpoint Windows dentro de una infraestructura virtualizada.

Durante el desarrollo se practicaron tareas relacionadas con:

- Implementación de un SIEM.
- Registro y monitoreo de endpoints.
- Recolección de telemetría mediante Sysmon.
- Generación controlada de eventos.
- Detección mediante reglas de Wazuh.
- Threat Hunting.
- Análisis de procesos y eventos.
- Inspección de información estructurada en JSON.
- Triage de alertas de seguridad.

El proyecto permitió comprender de forma práctica cómo un analista SOC puede pasar desde la **generación de un evento en un endpoint hasta su detección, investigación y análisis dentro de un SIEM**.

---

## Disclaimer

Este laboratorio fue desarrollado exclusivamente con fines educativos y de práctica en ciberseguridad.

Toda la actividad fue realizada dentro de un entorno virtualizado y controlado, utilizando sistemas y archivos destinados a la simulación de eventos de seguridad.

---

## Autor

**Agustín Garibay**

Estudiante de Tecnicatura Universitaria en Seguridad Informática  
Universidad Católica de Salta (UCASAL)

**Enfoque:** Ciberseguridad defensiva · SOC · SIEM · Threat Hunting
