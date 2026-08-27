# Seguridad y Resiliencia
## Asistente IA para Consultorio Odontológico

Este documento describe las medidas implementadas para reducir riesgos, proteger la información y mantener la estabilidad del flujo ante errores.

---

## 1. Minimización de datos

El sistema utiliza únicamente la información necesaria para gestionar la consulta y mantener trazabilidad.

Los principales datos procesados son:

- nombre del usuario;
- Telegram ID;
- mensaje original;
- categoría;
- prioridad;
- resumen generado por IA;
- estado de la consulta;
- respuesta enviada o sugerida.

No se requiere almacenar información clínica adicional que no sea necesaria para el funcionamiento del asistente.

El sistema no está diseñado para gestionar historias clínicas completas.

---

## 2. Límites de la Inteligencia Artificial

El AI Agent tiene instrucciones explícitas para evitar decisiones clínicas automáticas.

La IA no debe:

- realizar diagnósticos;
- indicar medicamentos;
- realizar prescripciones;
- tomar decisiones clínicas;
- inventar información;
- responder automáticamente ante situaciones sensibles.

Cuando detecta una consulta crítica o información insuficiente, debe derivar el caso al odontólogo.

---

## 3. Human-in-the-Loop

El proyecto incorpora un punto de intervención humana para consultas críticas.

Cuando:

`requiere_humano = true`

el sistema:

1. no responde automáticamente al paciente;
2. registra la consulta en Notion;
3. establece el estado `Pendiente de aprobación`;
4. guarda el resumen generado por IA;
5. guarda una respuesta sugerida;
6. notifica al odontólogo mediante Gmail.

El profesional revisa la información y toma la decisión final.

De esta manera, ninguna decisión clínica sensible queda exclusivamente a cargo de la Inteligencia Artificial.

---

## 4. Registro y trazabilidad

Notion funciona como registro operativo del sistema.

Permite almacenar:

- consulta original;
- clasificación;
- prioridad;
- estado;
- respuesta enviada;
- respuesta sugerida;
- decisión humana;
- fechas;
- observaciones.

Esto permite conocer qué ocurrió con cada consulta.

---

## 5. Error Handling

El módulo de Inteligencia Artificial cuenta con una ruta específica de manejo de errores.

Si se produce un fallo en el AI Agent, Make deriva la ejecución hacia:

**Registrar error IA**

El error se almacena en Google Sheets.

---

## 6. Registro de errores

El log de errores guarda datos como:

- ID del error;
- fecha;
- Telegram ID;
- usuario;
- módulo afectado;
- tipo de error;
- mensaje del error;
- acción que se estaba ejecutando;
- cantidad de reintentos;
- estado.

Esto permite analizar fallos y mantener trazabilidad técnica.

---

## 7. Break y Retry

Después de registrar el error se utiliza una directiva:

**Break**

El sistema tiene configurada la siguiente política:

- máximo de reintentos: `3`
- intervalo entre reintentos: `15 minutos`

La lógica es:

```text
Falla del AI Agent
       │
       ▼
Registrar error en Google Sheets
       │
       ▼
Break
       │
       ▼
Retry automático


El objetivo es evitar perder una consulta ante una falla temporal de la IA o de una integración.

8. Variables dinámicas

Los datos de cada paciente son obtenidos dinámicamente desde Telegram.

Se utilizan variables como:

mensaje recibido;
ID del chat;
ID del mensaje;
nombre del usuario.

Esto evita cargar manualmente información de pacientes dentro del escenario.

9. Control mediante filtros

El Router utiliza el campo:

requiere_humano

para separar las consultas.

Ruta automática

requiere_humano = false

Permite responder automáticamente consultas administrativas.

Ruta crítica

requiere_humano = true

Impide la respuesta automática y deriva al odontólogo.

10. Protección de credenciales

Las credenciales utilizadas por las integraciones permanecen configuradas dentro de Make.

No se publican en el repositorio:

API Keys;
contraseñas;
tokens privados;
credenciales de conexión.

Las capturas y el video final deben evitar mostrar información sensible.

11. Prevención de respuestas incorrectas

El AI Agent tiene instrucciones para utilizar únicamente información disponible en la base de conocimiento.

Si no encuentra información suficiente:

no debe inventar;
debe considerar la consulta como caso que requiere supervisión humana.

Esto reduce el riesgo de respuestas administrativas incorrectas.

12. Prevención de bucles

El flujo principal se activa mediante un mensaje recibido en Telegram.

Las respuestas enviadas por el bot no vuelven a ingresar como consultas del mismo flujo.

Además, las rutas están separadas mediante filtros booleanos para evitar que una misma consulta ejecute simultáneamente ambas ramas.

13. Evidencias de resiliencia

Durante las pruebas se verificó:

ejecución correcta de la ruta no crítica;
ejecución correcta de la ruta crítica;
activación controlada del Error Handler;
registro del error en Google Sheets;
ejecución de Break;
configuración de Retry.

Las capturas correspondientes se encuentran en la carpeta:

Evidencias

14. Resumen de controles
Riesgo	Control implementado
Respuesta clínica automática	HITL
Información inventada	RAG + instrucciones del agente
Falla del AI Agent	Error Handler
Pérdida del error	Registro en Google Sheets
Falla temporal	Break + Retry
Datos manuales de pacientes	Variables dinámicas
Exposición de credenciales	Credenciales internas en Make
Procesamiento innecesario	Trigger por Telegram
Falta de trazabilidad	Registro en Notion
Conclusión

El sistema combina automatización con controles técnicos y humanos.

Las consultas administrativas pueden resolverse automáticamente, mientras que las situaciones sensibles son derivadas al odontólogo.

Las rutas de error, el registro técnico y los reintentos permiten mantener mayor estabilidad ante fallos temporales.

El objetivo es lograr un equilibrio entre eficiencia operativa, trazabilidad, seguridad y supervisión humana.
