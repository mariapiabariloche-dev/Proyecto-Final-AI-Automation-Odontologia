# Dashboard de Control
## Asistente IA para Consultorio Odontológico

El Dashboard de Control permite monitorear el funcionamiento general del sistema utilizando los registros almacenados en Notion y el log de errores registrado en Google Sheets.

---

## 1. Objetivo

El panel permite visualizar rápidamente:

- cantidad total de consultas procesadas;
- consultas finalizadas;
- casos derivados a revisión humana;
- consultas pendientes;
- consultas de prioridad alta;
- errores registrados;
- tasa de error del sistema.

---

## 2. KPIs principales

Los indicadores obtenidos durante las pruebas del sistema son:

| KPI | Resultado |
|---|---:|
| Total de consultas procesadas | 11 |
| Consultas finalizadas | 6 |
| Consultas derivadas a humano | 2 |
| Consultas pendientes | 3 |
| Consultas de prioridad alta | 4 |
| Errores registrados | 1 |
| Tasa de error del sistema | 8,3 % |

---

## 3. Fórmula de tasa de error

La tasa de error se calculó mediante:

`Tasa de error = (Cantidad de ejecuciones con error / Total de ejecuciones) × 100`

Durante las pruebas se registraron:

- 11 consultas procesadas correctamente;
- 1 ejecución con error;
- 12 ejecuciones totales.

Por lo tanto:

`1 / 12 × 100 = 8,3 %`

---

## 4. Fuentes de datos

El Dashboard utiliza dos fuentes principales:

### Notion

La base de consultas permite identificar:

- ID de consulta;
- paciente;
- mensaje original;
- categoría;
- prioridad;
- necesidad de intervención humana;
- estado;
- resumen generado por IA;
- respuesta enviada;
- respuesta sugerida;
- fechas y observaciones.

### Google Sheets

El archivo de log de errores permite registrar fallos técnicos ocurridos durante la ejecución del escenario.

Entre los datos almacenados se encuentran:

- ID del error;
- fecha y hora;
- Telegram ID;
- paciente;
- módulo afectado;
- tipo de error;
- código;
- detalle del error.

---

## 5. Vistas utilizadas en Notion

Para construir el Dashboard se utilizó una vista vinculada de la base original de consultas.

Se configuraron las siguientes vistas y filtros:

### Todas las consultas

Muestra el total de consultas registradas en la base.

**Resultado: 11 consultas**

### Consultas finalizadas

Filtro aplicado:

`Estado = Finalizado`

**Resultado: 6 consultas**

### Requiere humano

Filtro aplicado:

`Requiere humano = Marcada`

**Resultado: 2 consultas**

### Prioridad alta

Filtro aplicado:

`Prioridad = ALTA`

**Resultado: 4 consultas**

### Pendientes

Filtro aplicado:

`Estado = Pendiente`

**Resultado: 3 consultas**

Estas vistas permiten utilizar la misma fuente de datos y mostrar diferentes indicadores sin crear bases adicionales.

---

## 6. Tasa de error del sistema

El sistema registró un error real durante una prueba controlada del Error Handler.

El error fue almacenado automáticamente en Google Sheets.

Resultado:

**Tasa de error del sistema: 8,3 % — 1 error en 12 ejecuciones**

---

## 7. Evidencia visual

Las capturas del Dashboard y de las vistas filtradas se encuentran documentadas en:

`Dashboard_KPIs_Asistente_Odontologico.pdf`

El archivo contiene evidencia de:

- total de consultas;
- consultas finalizadas;
- consultas derivadas a humano;
- prioridad alta;
- consultas pendientes;
- cálculo de tasa de error.

---

## 8. Enlace público al Dashboard

Dashboard público en Notion:

https://app.notion.com/p/3bf3659a0ca3801d95a8c0edbdf95951?v=9a1125e6b51e4f5dbf2ddf815f6d428c

---

## Conclusión

El Dashboard permite monitorear de forma simple el funcionamiento del asistente odontológico.

A partir de una única base de consultas en Notion se utilizan vistas filtradas para identificar los principales KPIs del sistema.

El registro de errores en Google Sheets complementa el monitoreo y permite calcular la tasa de error.

De esta manera se obtiene una visión general del funcionamiento operativo, la intervención humana y la estabilidad técnica del ecosistema de automatización.
