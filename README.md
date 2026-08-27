# Proyecto Final – AI Automation
## Asistente IA para Consultorio Odontológico

Proyecto final del curso de **AI Automation**.

El objetivo es automatizar la recepción y clasificación de consultas de pacientes de un consultorio odontológico, utilizando Inteligencia Artificial, RAG, memoria operativa, Human-in-the-Loop y manejo de errores.

---

## 1. Caso de uso

El sistema recibe consultas mediante **Telegram**.

Luego:

1. Make recibe el mensaje.
2. El **Make AI Agent** analiza y clasifica la consulta.
3. El agente consulta información autorizada almacenada en **Notion** mediante lógica RAG.
4. La respuesta de la IA se estructura en JSON.
5. Un Router determina si la consulta puede resolverse automáticamente o requiere intervención humana.

---

## 2. Arquitectura general

### Ruta no crítica

`Telegram → Make → AI Agent → RAG Notion → JSON → Router → Telegram → Notion`

Las consultas administrativas o de baja criticidad pueden responderse automáticamente.

La consulta queda registrada en Notion con estado:

`Finalizado`

### Ruta crítica

`Telegram → Make → AI Agent → RAG Notion → JSON → Router → Notion → Gmail → Odontólogo`

Cuando:

`requiere_humano = true`

el sistema:

- no responde automáticamente al paciente;
- registra la consulta en Notion;
- guarda una respuesta sugerida por IA;
- establece el estado `Pendiente de aprobación`;
- notifica al odontólogo mediante Gmail.

El profesional realiza la revisión y toma la decisión final.

---

## 3. Human-in-the-Loop

Las consultas clasificadas como críticas no generan una respuesta automática al paciente.

El sistema registra la consulta en Notion con estado:

`Pendiente de aprobación`

y almacena la respuesta sugerida por la IA.

Luego se notifica al odontólogo mediante Gmail para que revise el caso y continúe la atención por el canal que considere apropiado.

De esta manera, ninguna decisión clínica sensible queda exclusivamente a cargo de la Inteligencia Artificial.

---

## 4. Inteligencia Artificial y RAG

El proyecto utiliza **Make AI Agent**.

El agente recibe dinámicamente el mensaje proveniente de Telegram y genera una salida estructurada.

Además utiliza una base de conocimiento en Notion para recuperar información administrativa autorizada.

La IA no debe:

- realizar diagnósticos;
- indicar medicamentos;
- inventar información;
- tomar decisiones clínicas;
- responder automáticamente ante casos sensibles.

---

## 5. Salida estructurada

El AI Agent genera un JSON con la siguiente estructura:

```json
{
  "requiere_humano": false,
  "categoria": "",
  "prioridad": "",
  "resumen": "",
  "respuesta_paciente": "",
  "respuesta_sugerida_odontologo": ""
}El campo requiere_humano determina qué ruta ejecuta el Router.

6. Base de datos y memoria operativa

Notion se utiliza como:

base de conocimiento;
registro operativo de consultas;
seguimiento de estado;
soporte del proceso Human-in-the-Loop.

Entre los datos registrados se encuentran:

ID de consulta;
paciente;
Telegram ID;
mensaje original;
categoría;
prioridad;
resumen IA;
respuesta enviada;
respuesta sugerida;
requiere humano;
estado;
decisión humana;
fechas;
observaciones.
7. Manejo de errores

El AI Agent cuenta con una ruta específica de Error Handling.

Si se produce una falla:

AI Agent → Google Sheets → Break / Retry

El sistema:

registra el error en Google Sheets;
ejecuta Break;
permite reintentos controlados.

Configuración utilizada:

máximo de reintentos: 3
intervalo: 15 minutos

Esto permite mantener trazabilidad y resiliencia frente a errores temporales.

8. Seguridad

El sistema incorpora:

minimización de datos;
variables dinámicas;
credenciales almacenadas dentro de Make;
no publicación de API Keys ni tokens;
RAG con información autorizada;
supervisión humana para casos críticos;
registro técnico de errores;
filtros para separar rutas.

El sistema no está diseñado para almacenar historias clínicas completas ni sustituir la intervención profesional.

9. Dashboard de control

Se construyó un Dashboard en Notion utilizando vistas vinculadas de la base original de consultas.

KPIs obtenidos durante las pruebas:

KPI	Resultado
Total de consultas procesadas	11
Consultas finalizadas	6
Consultas derivadas a humano	2
Consultas pendientes	3
Consultas de prioridad alta	4
Errores registrados	1
Tasa de error del sistema	8,3 %

La tasa de error se calculó como:

1 error / 12 ejecuciones × 100 = 8,3 %

10. Entregables
01 – Arquitectura

Carpeta:

01_Arquitectura/

Incluye el diagrama completo del ecosistema de automatización.

02 – Datos

Carpeta:

02_Datos/

Incluye el Manual Operativo de Datos, estructuras principales y esquema JSON.

03 – Costos

Carpeta:

03_Costos/

Incluye la matriz de costos y la justificación de las herramientas utilizadas.

04 – Seguridad y Resiliencia

Carpeta:

04_Seguridad_Resiliencia/

Incluye las medidas de seguridad, HITL, manejo de errores y resiliencia.

05 – Dashboard

Carpeta:

05_Dashboard/

Incluye:

documentación del dashboard;
KPIs;
evidencia visual en PDF;
enlace público a Notion.
11. Blueprint

El blueprint actualizado del escenario se encuentra en:

Blueprint/Make_Blueprint_Asistente_Odontologico.json

12. Evidencias

La carpeta:

Evidencias/

incluye evidencia de:

escenario completo;
ruta no crítica;
ruta crítica;
derivación humana;
Error Handler;
Break / Retry;
Dashboard y KPIs.
13. Tecnologías utilizadas
Make
Telegram Bot
Make AI Agent
Notion
Gmail
Google Sheets
GitHub
14. Alcance

Este proyecto es un prototipo funcional orientado a la automatización de consultas iniciales de un consultorio odontológico.

No sustituye la evaluación profesional.

Las consultas críticas son derivadas al odontólogo para revisión humana.

15. Conclusión

El proyecto integra automatización, Inteligencia Artificial, RAG, memoria operativa, Human-in-the-Loop, manejo de errores y monitoreo mediante KPIs.

La arquitectura permite automatizar consultas administrativas simples y mantener intervención humana en situaciones sensibles, priorizando trazabilidad, seguridad y control.

## Demo del proyecto final

[Ver video demo en Google Drive] https://drive.google.com/file/d/1OXwtsVtOcsqJxuAY1ucko6_c20mnF3jm/view?usp=drive_link
