# Handbook — Asistente Editorial

## Inicio

Al arrancar, anunciar y leer:

1. 🔎 Leyendo contexto: `handbook/index.md`
2. 🔎 Leyendo contexto: `handbook/roles/default.md`
3. 🔎 Leyendo contexto: rol referenciado por `handbook/roles/default.md`
4. 🔎 Leyendo contexto: `handbook/skills/default.md`

Tras el inicio, cargar contexto adicional solo cuando sea relevante:

- Consultar `handbook/memory/` cuando puedan aplicar decisiones estables sobre el libro
- Consultar `handbook/output/` cuando la tarea continúe trabajo previo
- Cargar skills adicionales solo cuando la tarea actual lo requiera

## Estructura

1. `roles/` → perspectivas y enfoques editoriales
2. `skills/` → capacidades de oficio reutilizables
3. `memory/` → decisiones estables: estructura finalizada, arcos de personaje confirmados, beats de trama cerrados
4. `output/` → trabajo en curso: capítulos en progreso, preguntas estructurales abiertas, notas de sesión

## Enrutamiento

- Si el autor dice "continúa" o hace referencia a trabajo previo, listar `handbook/output/`
- Si la tarea encaja con un registro existente, leerlo y continuar desde "Estado Actual"
- Si la tarea es nueva y va a continuar, crear un registro en `handbook/output/`
- Si la tarea es una pregunta puntual, no crear registro
- Cuando un skill sea claramente necesario, cargarlo desde `handbook/skills/`
- Cuando se necesite una perspectiva editorial diferente, cargarla desde `handbook/roles/`

## Organización del Almacenamiento

Bajo `handbook/output/`, organizar por tipo:

- `novela/` — el libro principal: estructura general, plan de capítulos, preguntas abiertas
- `arcos/` — arcos individuales de personaje o relación en progreso
- `escenas/` — notas a nivel de escena: ubicación, función, qué necesita hacer la escena

Bajo `handbook/memory/`, guardar decisiones estables:

- `personajes.md` — perfiles de personaje finalizados
- `estructura.md` — estructura de capítulos/actos cerrada
- `mundo.md` — escenario, tono, reglas del mundo narrativo
- `temas.md` — los temas centrales del libro (no la trama — de qué trata el libro)

## Trazabilidad de Actividad

Al enrutar, anunciar:

🧭 Enrutando: `<motivo>` → `<acción>`

Luego continuar con lecturas de ficheros, carga de skills, análisis y actualizaciones de output como se define en `AGENTS.md`.

## Referencias

- `roles/default.md`
- `skills/default.md`
- `memory/`
- `output/`
