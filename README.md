# N8N Automations

Automatizaciones construidas con N8N para eliminar trabajo manual repetitivo en finanzas, operaciones y marketing. Todos los flows usan LLMs (Claude / GPT) como capa de razonamiento sobre datos no estructurados.

---

## Stack

- **N8N** — orquestación de flujos y conectores
- **Claude API** — extracción de datos, síntesis y razonamiento sobre documentos
- **Google Sheets** — fuente de datos estructurados y estado persistente entre ejecuciones
- **Gmail / Slack** — triggers y notificaciones
- **Claude Code** — para construir y depurar flows rápidamente desde la terminal

---

## Casos de Uso

### [01 — Procesamiento de Facturas con LLM](casos/01-procesamiento-facturas.md)
Automatización end-to-end del proceso de recepción y validación de facturas de proveedores.

**Flujo:** Email con PDF → Claude extrae datos → validación contra planilla de proveedores → notificación a contabilidad o alerta de excepción

**Resultado:** 10 min por factura → 45 segundos · 6 horas semanales liberadas · tasa de error de 15% → 2%

---

### [02 — Benchmark Semanal de Competencia](casos/02-benchmark-competencia.md)
Monitoreo automático semanal de pricing, contenido y marketing de 8 competidores con resumen ejecutivo generado por LLM.

**Flujo:** Schedule semanal → scraping de precios + RSS blogs + LinkedIn → Claude sintetiza cambios → resumen en Slack

**Resultado:** 3 horas semanales → 0 · cobertura de 3 competidores irregulares → 8 competidores cada lunes

---

## Claude Code + N8N

El workflow actual combina Claude Code con N8N para construir automatizaciones mucho más rápido:

```
Idea de automatización
        ↓
Claude Code: diseña la arquitectura del flow,
             escribe los prompts del LLM,
             genera el JSON del workflow de N8N
        ↓
N8N: importar JSON → ajustar credenciales → activar
        ↓
Live en minutos
```

Lo que antes tomaba medio día de configuración manual en N8N (definir nodos, conectar lógica, escribir prompts, depurar) ahora toma 20–30 minutos: Claude Code genera el esqueleto del flow y los prompts, y N8N lo ejecuta.

---

## Principios de Diseño

Cada automatización está construida con tres reglas:

1. **El humano solo ve excepciones** — si todo está bien, el sistema no interrumpe. Solo alerta cuando algo necesita decisión humana.

2. **LLM para razonamiento, no para datos** — el LLM interpreta documentos no estructurados y sintetiza información. Los datos críticos (montos, fechas, RUTs) se validan contra fuentes estructuradas antes de actuar.

3. **Falla de forma visible** — si un nodo falla (scraping caído, API con error), el flow notifica en lugar de fallar silenciosamente. Mejor una alerta ruidosa que datos incorrectos sin aviso.
