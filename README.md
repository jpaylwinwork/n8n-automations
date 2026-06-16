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

### Resumen Semanal de Newsletters de Producto
Agregación automática de los newsletters de producto más relevantes (Lenny's, Shreyas, etc.) → Claude extrae los artículos más importantes de la semana → resumen consolidado enviado cada lunes. Reemplaza revisar decenas de emails individualmente por un solo digest con lo que realmente vale leer.

### PM Job Tracker — LinkedIn Chile
**17 nodos · Corre todos los lunes a las 9am**

Flow completo de búsqueda, filtrado y enriquecimiento de ofertas de producto en Chile.

```
Schedule (lunes 9am)
  → Apify [linkedin-jobs-scraper] — busca "Product Manager" en Santiago, última semana
  → Limit — top 15 resultados
  → Aggregate — agrupa en un solo item
  → AI Agent (GPT-4o) — lee mi perfil y selecciona los 5 roles más relevantes,
                         con campo "whyRelevant" por cada uno
  → Split Out — separa los 5 en items individuales
  → Loop (batch 1) — por cada oferta:
      ├── Proxycurl — busca contactos HR / Recruiter / Talent en la empresa
      └── Proxycurl — busca PMs, Heads of Product, VPs, CPOs (hasta 2)
  → Aggregate — consolida las 5 tarjetas enriquecidas
  → AI Agent (GPT-4o) — genera email HTML con tarjetas, contactos,
                         links de LinkedIn y mensaje de outreach sugerido por empresa
  → Gmail — envía a jp.aylwin.work@gmail.com
```

**Stack:** N8N · Apify · Proxycurl · GPT-4o · Gmail

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
