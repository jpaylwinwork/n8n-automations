# N8N Automations

Automatizaciones construidas con N8N para eliminar trabajo manual repetitivo en operaciones, finanzas y marketing. Todos los flows usan LLMs como capa de razonamiento sobre datos no estructurados.

---

## Stack

- **N8N** — orquestación de flujos y conectores
- **Claude API** — extracción de datos, síntesis y razonamiento sobre documentos
- **Google Sheets** — fuente de datos estructurados y estado persistente
- **Gmail / Slack** — triggers y notificaciones
- **Claude Code** — para diseñar y construir flows desde la terminal

---

## Automatizaciones Construidas

### Procesamiento de Facturas con LLM
Recepción de facturas PDF por email → extracción de datos con Claude (RUT, monto, fecha, concepto) → validación contra planilla de proveedores aprobados → notificación automática al equipo de finanzas o alerta de excepción para revisión manual.

### Benchmark Semanal de Competencia
Monitoreo automático semanal de pricing, blog y actividad de marketing de competidores → síntesis con LLM → resumen ejecutivo enviado al equipo de producto y marketing cada lunes.

---

## Claude Code + N8N

El workflow actual combina Claude Code con N8N para construir automatizaciones rápidamente:

```
Idea
  ↓
Claude Code → diseña arquitectura, escribe prompts, genera JSON del workflow
  ↓
N8N → importar → ajustar credenciales → activar
  ↓
Live en minutos
```

---

## Principios de Diseño

1. **El humano solo ve excepciones** — el sistema solo interrumpe cuando algo necesita decisión humana
2. **LLM para razonamiento, no para datos** — los datos críticos se validan contra fuentes estructuradas antes de actuar
3. **Falla de forma visible** — si un nodo falla, notifica en lugar de fallar silenciosamente
