# Plan de Implementación: Task Notifier — Daily Summary

Este plan detalla el diseño e implementación del flujo automatizado en n8n que extrae tareas pendientes desde Google Sheets, genera un resumen inteligente vía Gemini y notifica el resultado por Gmail diariamente.

## Estructura de Datos para Google Sheets

Para configurar correctamente la fuente de datos, debes crear un archivo de Google Sheets con las siguientes especificaciones:

* **Nombre del Archivo:** `Gestion_Tareas_Productividad`
* **Nombre de la Hoja:** `Tareas_Pendientes`
* **Columnas (Primera Fila):**
  `id`, `titulo`, `descripcion`, `fecha_limite`, `prioridad`, `estado`, `categoria`

### Dataset de Ejemplo (Copiar y Pegar en la Hoja)

| id | titulo | descripcion | fecha_limite | prioridad | estado | categoria |
|---|---|---|---|---|---|---|
| 1 | Calificar Evaluaciones Computación | Revisar y asentar notas del primer parcial en la plataforma de la UNEXPO. | 2026-07-09 | Alta | Pendiente | UNEXPO |
| 2 | Grabar Sección 4 de Curso Python | Producir y editar 3 nuevos videos para el curso de "+100 Ejercicios Resueltos". | 2026-07-10 | Media | En Progreso | Udemy |
| 3 | Revisar Pipeline de Automatización | Optimizar manejo de excepciones en scripts de persistencia locales. | 2026-07-12 | Baja | Pendiente | Personal |

---

## Cambios Propuestos

### 1. Backend / Automatización (n8n)

#### [MODIFY] [task_notifier_workflow.json](workflows/task_notifier_workflow.json)
Actualizaremos el JSON local con la configuración exacta de los siguientes nodos:
1. **Schedule Trigger:** Configurado a las `07:00 AM America/Caracas`.
2. **Google Sheets Node:**
   * Operación `Read / Get Many`.
   * Filtro nativo o nodo `Filter` secundario aplicando la regla `estado != "Completado"`.
3. **Google Gemini Node:**
   * Configuración de `gemini-1.5-flash`.
   * Prompt especializado para resumen ejecutivo con HTML semántico.
4. **Gmail Node:**
   * Operación `Send Email`.
   * Asunto dinámico: `[Resumen de Tareas] Diarias - {{ $now.format('yyyy-MM-dd') }}`.
   * Cuerpo HTML mapeando el output de Gemini.

#### [NEW] `Task Notifier — Error Handler` (Workflow Separado)
Crearemos un workflow de error separado en la instancia para capturar excepciones inesperadas del flujo principal y notificar en caso de fallos críticos.

---

## Plan de Verificación

### Pruebas Manuales y Flujo de Trabajo MCP
Para desplegar y verificar el workflow de manera segura, seguiremos estrictamente el protocolo de `AGENTS.md`:

1. **Obtención de Esquemas (get_node):** Inspeccionar los nodos de Google Sheets, Gemini y Gmail en vivo para asegurar compatibilidad de parámetros.
2. **Validación Local (validate_workflow):** Validar la estructura del JSON local.
3. **Despliegue del Workflow (n8n_create_workflow):** Crear el flujo en la instancia local de n8n.
4. **Validación del Flujo Desplegado (n8n_validate_workflow):** Validar en la instancia.
5. **Verificación de Conexiones (n8n_get_workflow):** Revisar que todas las conexiones entre nodos y la política de recuperación de errores (`onError`) estén activas.
6. **Ejecución de Prueba (n8n_test_workflow):** Realizar una ejecución manual supervisada con datos de prueba reales antes del pase a producción.
7. **Activación de Workflow:** Encendido final del workflow programado.
