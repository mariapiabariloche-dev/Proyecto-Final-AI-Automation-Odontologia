# Manual Operativo de Datos
## Asistente IA para Consultorio Odontológico

Este documento describe cómo se estructuran y transfieren los datos dentro del ecosistema de automatización.

El sistema utiliza **Notion** como base de conocimiento y como registro operativo de consultas, mientras que **Make** procesa la información recibida desde Telegram y distribuye los resultados hacia Notion, Telegram y Gmail.

---

## 1. Base de conocimiento en Notion

La base de conocimiento contiene información autorizada del consultorio que puede ser utilizada por el AI Agent para responder consultas administrativas.

### Campos principales

| Campo | Función |
|---|---|
| ID | Identificador del registro |
| Categoría | Tipo de información |
| Tema / intención | Tema al que pertenece la información |
| Pregunta ejemplo | Ejemplo de consulta |
| Respuesta autorizada | Información validada que puede utilizar la IA |
| Prioridad | Nivel de prioridad |
| Requiere humano | Indica si el tema necesita revisión |
| Activo | Determina si el registro puede utilizarse |
| Observaciones | Información adicional |
| Última actualización | Fecha de revisión del dato |

La IA utiliza esta base mediante una lógica RAG para recuperar información antes de generar una respuesta.

---

## 2. Base de consultas en Notion

La base de consultas funciona como registro central del sistema.

Guarda tanto consultas resueltas automáticamente como consultas críticas derivadas al odontólogo.

### Campos principales

| Campo | Función |
|---|---|
| ID Consulta | Identificador de la consulta |
| Paciente | Nombre recibido desde Telegram |
| Telegram ID | Identificador del chat |
| Mensaje original | Consulta enviada por el paciente |
| Categoría | Clasificación generada por IA |
| Prioridad | BAJA, MEDIA o ALTA |
| Resumen IA | Resumen de la consulta |
| Respuesta enviada | Respuesta enviada automáticamente |
| Respuesta sugerida IA | Propuesta para revisión del odontólogo |
| Requiere humano | Indica si necesita intervención profesional |
| Estado | Estado actual de la consulta |
| Decisión humana | Resultado de la revisión profesional |
| Respuesta aprobada | Respuesta validada por el odontólogo |
| Revisor humano | Profesional que realizó la revisión |
| Fecha ingreso | Fecha de recepción |
| Fecha resolución | Fecha de finalización |
| Fecha decisión | Fecha de intervención humana |
| Observaciones | Información adicional |

---

## 3. Estados utilizados

Los estados permiten conocer en qué etapa se encuentra cada consulta.

Se utilizan:

- `Pendiente de aprobación`
- `Aprobado por Humano`
- `Rechazado por Humano`
- `Finalizado`

En las consultas no críticas el flujo registra directamente el estado:

`Finalizado`

En las consultas críticas el flujo registra:

`Pendiente de aprobación`

y el odontólogo puede actualizar posteriormente la decisión.

---

## 4. Relación lógica entre los datos

El sistema organiza la información de la siguiente manera:

```text
Base de conocimiento
        │
        │ información autorizada
        ▼
     AI Agent
        │
        ▼
Consulta del paciente
        │
        ▼
Base de consultas

La base de conocimiento contiene la información que la IA puede consultar.

La base de consultas almacena el resultado de cada interacción.

De esta forma se separa:

la información estable del consultorio;
de los registros operativos generados por los pacientes.
5. Esquema JSON de salida de la IA

El AI Agent genera una salida estructurada utilizada por Make.

{
  "requiere_humano": false,
  "categoria": "",
  "prioridad": "",
  "resumen": "",
  "respuesta_paciente": "",
  "respuesta_sugerida_odontologo": ""
}
Descripción
Campo JSON	Uso
requiere_humano	Define qué ruta toma el Router
categoria	Clasifica la consulta
prioridad	Determina el nivel de importancia
resumen	Resume el mensaje
respuesta_paciente	Se utiliza en la ruta automática
respuesta_sugerida_odontologo	Se utiliza en la ruta crítica
6. Transferencia de datos entre integraciones
Telegram → Make

Telegram envía dinámicamente:

mensaje del paciente;
ID del mensaje;
ID del chat;
nombre del usuario.
Make → AI Agent

El AI Agent recibe el texto del mensaje de Telegram.

También utiliza el Telegram Chat ID como identificador de conversación.

AI Agent → Notion RAG

La IA consulta la base de conocimiento cuando necesita información autorizada.

Puede recuperar datos relacionados con:

horarios;
ubicación;
obras sociales;
tratamientos generales;
precios autorizados;
políticas de turnos.
AI Agent → JSON

La respuesta de la IA se estructura para que Make pueda utilizar sus campos de manera independiente.

JSON → Router

El Router utiliza:

requiere_humano

para decidir el camino.

Si requiere_humano = false

El sistema:

responde al paciente por Telegram;
registra la consulta en Notion;
guarda el estado Finalizado.
Si requiere_humano = true

El sistema:

no responde automáticamente;
registra la consulta como Pendiente de aprobación;
guarda la respuesta sugerida;
envía una notificación al odontólogo mediante Gmail.
7. Datos de error

Cuando ocurre un error en el AI Agent, Make deriva la ejecución a Google Sheets.

El registro de error incluye información como:

ID del error;
fecha;
Telegram ID;
usuario;
módulo;
tipo de error;
mensaje;
acción;
cantidad de reintentos;
estado.

Esto permite mantener trazabilidad técnica.

8. Criterio de minimización de datos

El sistema utiliza únicamente la información necesaria para gestionar la consulta.

No se requiere almacenar:

diagnósticos;
historia clínica;
documentación médica;
medicamentos;
información clínica innecesaria.

El objetivo de la automatización es administrativo y de clasificación inicial.

9. Resumen del flujo de datos
Telegram
   │
   ▼
Make
   │
   ▼
AI Agent
   │
   ├── Notion Base de conocimiento
   │
   ▼
JSON estructurado
   │
   ▼
Router
   │
   ├── No crítico → Telegram + Notion
   │
   └── Crítico → Notion + Gmail + revisión humana

Si falla IA:
AI Agent → Google Sheets → Break / Retry

Esta estructura permite mantener los datos organizados, trazables y separados según su función dentro del sistema.
