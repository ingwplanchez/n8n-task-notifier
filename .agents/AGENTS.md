# AGENTS.md — n8n Task Notifier Project

Reglas de comportamiento del agente para el workspace `n8n-task-notifier`.
Estas reglas se aplican a **toda** tarea relacionada con este proyecto.

---

## 1. Skills Obligatorias (cargar antes de cualquier acción n8n)

Invocar en este orden antes de la primera llamada MCP:

| Orden | Skill | Cuándo |
|---|---|---|
| 1 | `using-n8n-mcp-skills` | Siempre. Es el router. |
| 2 | `n8n-mcp-tools-expert` | Antes de cualquier herramienta MCP. |
| 3 | `n8n-workflow-patterns` | Al diseñar o modificar un workflow. |
| 4 | `n8n-node-configuration` | Al configurar cualquier nodo. |
| 5 | `n8n-error-handling` | Al wiring conexiones o añadir nodos fallibles. |
| 6 | `n8n-expression-syntax` | Al escribir `{{ }}` o referenciar datos entre nodos. |

---

## 2. Convenciones de Nomenclatura

### Workflows
- Formato: `[Proyecto] — [Función]`
- Ejemplos:
  - `Task Notifier — Daily Summary` (workflow principal)
  - `Task Notifier — Error Handler` (workflow de error)

### Nodos
- Usar nombres descriptivos, nunca el nombre por defecto (`Google Sheets`, `Gmail1`).
- Formato: `[Acción] [Objeto]`
- Ejemplos: `Obtener Tareas`, `Filtrar Pendientes`, `Generar Resumen Gemini`, `Enviar Correo Diario`

---

## 3. Política de Credenciales (NON-NEGOTIABLE)

- **NUNCA** emitir un bloque `credentials` con ID placeholder.
- Si el ID es desconocido → **omitir** el bloque `credentials` por completo.
- Usar `n8n_manage_credentials({ action: "list" })` para descubrir IDs reales.

### Credenciales del proyecto

| Nodo | Tipo |
|---|---|
| Google Sheets | `googleSheetsOAuth2Api` |
| Google Gemini | `googleGeminiApi` |
| Gmail | `gmailOAuth2` |

---

## 4. Protocolo de Validación (orden obligatorio)

`
1. get_node(standard)          → leer schema live antes de configurar
2. validate_workflow(JSON)     → validar ANTES de n8n_create_workflow
3. n8n_create_workflow         → crear en la instancia
4. n8n_validate_workflow(id)   → validar el workflow desplegado
5. n8n_get_workflow(id)        → verificar connections + onError por nodo
6. [Con aprobación usuario]    → n8n_test_workflow (efectos reales)
7. activateWorkflow            → activar solo tras pasar todos los pasos
`

---

## 5. Error Handling (checklist para workflows scheduled)

- [ ] Nodos de red: `retryOnFail: true`, `maxTries: 3`, `waitBetweenTries: 5000`
- [ ] Gmail final: `onError: "continueErrorOutput"` + `main[1]` wired
- [ ] Existe workflow separado `Task Notifier — Error Handler`
- [ ] Vínculo Error Workflow asignado manualmente en la UI (Workflow Settings → Error Workflow)

---

## 6. Datos del Proyecto

- **Archivo Sheets:** `Gestion_Tareas_Productividad`
- **Hoja:** `Tareas_Pendientes`
- **Columnas:** id, titulo, descripcion, fecha_limite, prioridad, estado, categoria
- **Filtro:** `estado != "Completado"`
- **Cron:** `0 7 * * *` (07:00 AM America/Caracas)
- **Gemini Model:** `gemini-1.5-flash`
- **Asunto email:** `[Resumen de Tareas] Diarias - YYYY-MM-DD`

---

## 7. Archivos del Proyecto

| Archivo | Propósito |
|---|---|
| `SPECS.md` | Especificaciones originales |
| `docs/technical_guide.md` | Guía técnica |
| `docs/agents_guide.md` | Historial configuración entorno |
| `workflows/task_notifier_workflow.json` | JSON local del workflow |
| `.agents/AGENTS.md` | Este archivo |
| `.agents/skills/` | Skills del workspace |

---

## 8. MCP Server

- **Server:** `n8n-mcp` v2.63.1
- **URL:** `http://host.docker.internal:5678`
- **Estado:** Conectado y operativo
