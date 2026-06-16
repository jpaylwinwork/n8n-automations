# Automatización: Benchmark Semanal de Competencia

## El Problema

Seguir la actividad de marketing de competidores era completamente manual: alguien del equipo tenía que revisar los sitios web, redes sociales y newsletters de 5–8 competidores cada semana, tomar notas, y armar un resumen para el equipo de producto y marketing.

Proceso manual: ~3 horas por semana. Inconsistente — cuando había más trabajo, simplemente no se hacía.

## La Solución

Flow semanal en N8N que recopila señales de marketing de competidores y genera un resumen ejecutivo automático.

```
[Schedule: lunes 8am]
    ↓
[Para cada competidor en lista]
    ├── [HTTP Request] → Scraping de página de precios / landing pages
    ├── [RSS Feed] → Nuevos posts en blog corporativo
    └── [HTTP Request] → Últimos posts públicos en LinkedIn (via API)
    ↓
[Agregar todos los cambios detectados]
    ↓
[LLM — Claude] → Resume cambios, detecta patrones, señala movimientos relevantes
    ↓
[Slack / Email] → Resumen semanal al equipo de producto y marketing
```

## Competidores Monitoreados

Para cada competidor el flow revisaba:
- **Página de precios** — cambios en planes, features incluidos, pricing
- **Blog** — nuevos artículos publicados (título + resumen)
- **LinkedIn** — campañas activas, nuevos contenidos, cambios en messaging

## Prompt de Síntesis (LLM)

```
Eres un analista de inteligencia competitiva.
A continuación tienes los cambios detectados esta semana en los competidores de [Empresa].
Para cada competidor, identifica:
1. Cambios en pricing o producto
2. Nuevas iniciativas de marketing o contenido
3. Cambios en messaging o posicionamiento

Luego genera un párrafo de "Lo más importante esta semana" con los 2-3 movimientos
más relevantes para nuestra estrategia.

Sé conciso. Si no hubo cambios relevantes en un competidor, dilo en una línea.
```

## Output Ejemplo

```
📊 BENCHMARK SEMANAL — Semana del 14 al 20 de abril

🔴 LO MÁS IMPORTANTE
• Competidor A bajó el precio de su plan Pro de $49 a $39/mes — tercer bajada en 6 meses.
  Posible presión por churn o táctica de adquisición agresiva antes de fin de trimestre.
• Competidor B publicó 3 artículos sobre [tema X] en una semana — indicio de campaña
  de contenido coordinada apuntando al mismo segmento que nosotros.

📌 POR COMPETIDOR
[Competidor A] Cambio de precio en plan Pro...
[Competidor B] Nuevos posts: "Título 1", "Título 2", "Título 3"...
[Competidor C] Sin cambios relevantes esta semana.
```

## Resultado

- Tiempo del equipo: de 3 horas semanales → **0 horas** (revisión del resumen: 5 minutos)
- Cobertura: pasó de revisar 3 competidores de forma irregular → **8 competidores cada lunes sin falta**
- Impacto: detectamos un cambio de pricing de un competidor clave 4 días antes de una reunión de board donde ese dato fue relevante

## Lo Que Aprendí

- El scraping de páginas de precios es frágil — los sitios cambian su HTML con frecuencia. RSS feeds y APIs son mucho más estables
- El valor no está en recopilar más información sino en reducirla: el LLM que sintetiza 20 páginas en 5 bullets es lo que hace que el equipo realmente lo lea
- Agregar un campo "cambio vs semana anterior" requirió guardar el estado previo en Google Sheets — ese fue el mayor trabajo de ingeniería del flow
