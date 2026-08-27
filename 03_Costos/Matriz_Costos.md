# Matriz de Costos y Decisión Tecnológica

Este documento compara las principales alternativas consideradas para el ecosistema de automatización y justifica las decisiones utilizadas en el proyecto.

## 1. Orquestador

| Opción | Costo relativo | Ventajas | Desventajas | Decisión |
|---|---:|---|---|---|
| Make | Bajo / medio | Interfaz visual, rápida implementación, manejo simple de routers y errores | Puede aumentar el costo con muchas operaciones | Elegido |
| n8n | Bajo / medio | Muy flexible, potente y autohospedable | Requiere mayor configuración técnica | No elegido para este prototipo |

### Justificación

Se utilizó **Make** porque permite construir y demostrar el flujo completo de forma visual, integrar Telegram, Notion, Gmail, Google Sheets y el AI Agent dentro de un mismo escenario y simplificar el manejo de errores.

---

## 2. Base de datos

| Opción | Costo relativo | Ventajas | Desventajas | Decisión |
|---|---:|---|---|---|
| Notion | Bajo | Fácil de administrar, sirve como base de conocimiento y registro operativo | Menor capacidad transaccional que una base especializada | Elegido |
| Airtable | Bajo / medio | Muy buena estructura de bases y relaciones | Puede requerir un plan pago al crecer | Alternativa |

### Justificación

Se eligió **Notion** porque permite mantener en un mismo entorno la base de conocimiento del consultorio y el registro de consultas.

---

## 3. Procesamiento con IA

| Tipo de modelo | Costo relativo | Uso recomendado | Decisión |
|---|---:|---|---|
| Modelo liviano / económico | Bajo | Clasificación, resumen, respuestas breves y extracción estructurada | Recomendado para este proyecto |
| Modelo de mayor razonamiento | Medio / alto | Análisis complejos o documentos extensos | No necesario para este caso |
| Procesamiento por lotes / Batches | Bajo por volumen | Procesos masivos no urgentes | No aplica al flujo en tiempo real |

### Justificación

El proceso necesita principalmente:

- interpretar mensajes cortos;
- clasificar consultas;
- generar resúmenes;
- completar una estructura JSON;
- producir respuestas administrativas breves.

Por este motivo, un modelo liviano es suficiente y permite reducir el costo por ejecución.

No es necesario utilizar un modelo de razonamiento avanzado para cada consulta.

---

## 4. RAG y optimización de contexto

La información administrativa se almacena en Notion y se consulta mediante lógica RAG.

Esto permite evitar incluir toda la información del consultorio dentro de cada prompt.

### Beneficios

- menor cantidad de contexto;
- reducción del consumo de tokens;
- información más fácil de actualizar;
- menor riesgo de respuestas basadas en datos desactualizados.

---

## 5. Optimización implementada

El proyecto incorpora las siguientes medidas de ahorro:

- Trigger mediante webhook de Telegram.
- El escenario se ejecuta únicamente cuando llega una consulta.
- Uso de un AI Agent para tareas puntuales.
- Longitud de salida limitada.
- Historial conversacional limitado.
- RAG para recuperar solo información necesaria.
- Router para ejecutar únicamente la ruta correspondiente.
- Reintentos controlados ante errores.

---

## 6. Estimación cualitativa de costos

| Componente | Frecuencia | Impacto estimado |
|---|---|---|
| Telegram | Por consulta | Muy bajo |
| Make | Por operaciones ejecutadas | Bajo / medio |
| IA | Por consulta procesada | Bajo |
| Notion | Consultas y registros | Bajo |
| Gmail | Solo casos críticos | Muy bajo |
| Google Sheets | Solo ante errores | Muy bajo |

La mayor parte del consumo se concentra en el procesamiento mediante IA y en las operaciones ejecutadas por Make.

---

## 7. Estrategia de escalabilidad

Si el volumen de consultas creciera significativamente, se podrían aplicar medidas adicionales:

- reducir aún más el contexto enviado al modelo;
- utilizar modelos más económicos para clasificación;
- separar procesos masivos no urgentes;
- utilizar Batches para tareas fuera de tiempo real;
- migrar a n8n autohospedado si el costo por operaciones de Make se vuelve significativo.

---

## 8. Decisión final

Para este prototipo se priorizó una combinación de:

**Make + Notion + Make AI Agent + Telegram + Gmail + Google Sheets**

La elección busca equilibrar:

- simplicidad;
- costo;
- velocidad de implementación;
- mantenimiento;
- seguridad;
- capacidad de demostración.

El sistema utiliza herramientas de bajo costo para tareas simples y reserva la intervención humana para los casos críticos.
