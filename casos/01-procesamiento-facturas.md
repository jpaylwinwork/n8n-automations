# Automatización: Procesamiento de Facturas con LLM

## El Problema

El equipo de finanzas recibía facturas de proveedores por email en distintos formatos (PDF, imagen escaneada, HTML). El proceso manual era:

1. Alguien del equipo abría el mail
2. Leía la factura y extraía los datos manualmente (RUT, monto, fecha, concepto)
3. Los cruzaba contra una planilla de Excel con proveedores y contratos aprobados
4. Si todo cuadraba, reenviaba al equipo contable para cargar al sistema

Tiempo promedio por factura: 8–12 minutos. Volumen: ~40 facturas por semana. Tasa de error manual: ~15%.

## La Solución

Flow en N8N que automatiza el proceso completo end-to-end.

```
[Gmail] → [Filtro: asunto/remitente] → [Extraer adjunto PDF]
    ↓
[LLM — Claude] → Extrae: RUT emisor, monto neto, IVA, total, fecha, concepto
    ↓
[Google Sheets] → Busca RUT en planilla de proveedores aprobados
    ↓
[Lógica condicional]
    ├── ✅ Proveedor aprobado + monto dentro de rango → Email a contabilidad con datos estructurados
    └── ⚠️  Proveedor desconocido o monto fuera de rango → Alerta a jefe de finanzas para revisión manual
```

## Nodos Clave

| Nodo | Herramienta | Qué hace |
|------|------------|---------|
| Trigger | Gmail (IMAP) | Detecta mails nuevos con adjunto PDF en carpeta "Facturas" |
| Extracción | Claude API | Prompt estructurado que devuelve JSON con campos de la factura |
| Validación | Google Sheets | Busca RUT en columna de proveedores aprobados y lee límite de monto |
| Notificación | Gmail / Slack | Email formateado a contabilidad o alerta de excepción |

## Prompt de Extracción (LLM)

```
Eres un asistente de procesamiento de facturas chilenas.
Extrae del siguiente documento los siguientes campos en formato JSON:
{
  "rut_emisor": "",
  "razon_social": "",
  "numero_factura": "",
  "fecha_emision": "YYYY-MM-DD",
  "monto_neto": 0,
  "iva": 0,
  "monto_total": 0,
  "concepto": ""
}
Si no encuentras algún campo, devuelve null. No inventes datos.
```

## Resultado

- Tiempo de procesamiento por factura: de 10 min → **45 segundos**
- Tasa de error: de 15% → **< 2%** (solo errores en facturas con mala calidad de escaneo)
- Reducción de carga manual del equipo de finanzas: **~6 horas semanales**
- Excepciones que requieren revisión humana: ~8% del volumen

## Lo Que Aprendí

- Los LLMs son muy buenos extrayendo datos estructurados de documentos semi-estructurados, pero necesitan instrucciones explícitas sobre qué hacer cuando falta información (no inventar)
- Agregar un paso de validación de schema JSON antes de escribir en Sheets evita errores silenciosos
- El 8% de excepciones reveló que varios proveedores no estaban dados de alta — la automatización hizo visible un problema de datos que existía antes pero nadie veía
