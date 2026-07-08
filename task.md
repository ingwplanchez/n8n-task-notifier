# Tasks

- `[x]` Crear y actualizar la Guía Técnica (`docs/technical_guide.md`) con la arquitectura final y configuraciones.
- `[x]` Crear la hoja de cálculo en Google Sheets (`Gestion_Tareas_Productividad` -> `Tareas_Pendientes`) con los datos de ejemplo (Paso Manual del Usuario).
- `[x]` Validar credenciales de n8n (`googleSheetsOAuth2Api`, `googleGeminiApi`, `gmailOAuth2`) vía MCP.
- `[x]` Validar estructura JSON local del workflow principal (`validate_workflow` / `n8n_validate_workflow`).
- `[x]` Desplegar/actualizar el workflow principal en la instancia (`n8n_update_partial_workflow`).
- `[x]` Auditar el flujo desplegado (`n8n_validate_workflow`, `n8n_get_workflow`).
- `[x]` Crear y vincular el workflow de manejo de errores (`Task Notifier — Error Handler`).
- `[x]` Ejecución manual de prueba del workflow en la UI de n8n (Usuario).
- `[x]` Activación final y comprobación de programaciones cron.
