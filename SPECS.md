# CONTEXTO Y ROL
Actúa como un Ingeniero de Automatización Senior y Experto en Arquitectura Integration Platform as a Service (iPaaS). Tu objetivo es diseñar e implementar un flujo de trabajo (workflow) robusto y de nivel de producción en la plataforma n8n, destinado a la orquestación, extracción y notificación automatizada de tareas diarias pendientes.

# OBJETIVOS DEL SISTEMA
Desarrollar un flujo programado en n8n que extraiga de forma asíncrona un conjunto de datos desde una hoja de cálculo en Google Sheets, procese y filtre la información mediante el ecosistema de IA de Gemini para estructurar un resumen ejecutivo, y despache la notificación final a través de la API de Gmail.

# ESPECIFICACIONES DE LA INFRAESTRUCTURA DE DATOS (GOOGLE SHEETS)
La persistencia de los datos iniciales se gestionará bajo la siguiente arquitectura de archivo:

* **Nombre del Archivo:** `Gestion_Tareas_Productividad`
* **Nombre de la Hoja (Sheet Name):** `Tareas_Pendientes`
* **Estructura de la Tabla (Esquema de Datos):**
  La tabla debe contar con las siguientes columnas tipadas explícitamente:
  * `id` (Integer / Auto-incremental): Identificador único de la tarea.
  * `titulo` (String): Nombre descriptivo de la actividad.
  * `descripcion` (String): Detalles y especificaciones adicionales.
  * `fecha_limite` (Date [YYYY-MM-DD]): Fecha de vencimiento de la tarea.
  * `prioridad` (Enum [Alta, Media, Baja]): Nivel de criticidad.
  * `estado` (Enum [Pendiente, En Progreso, Completado]): Estado actual del ciclo de vida.
  * `categoria` (String): Clasificación del ámbito (ej. UNEXPO, Udemy, Personal).

* **Dataset de Ejemplo (Pruebas de Carga/Validación):**
  | id | titulo | descripcion | fecha_limite | prioridad | estado | categoria |
  |----|--------|-------------|--------------|-----------|--------|-----------|
  | 1  | Calificar Evaluaciones Computación | Revisar y asentar notas del primer parcial en la plataforma de la UNEXPO. | 2026-07-09 | Alta | Pendiente | UNEXPO |
  | 2  | Grabar Sección 4 de Curso Python | Producir y editar 3 nuevos videos para el curso de "+100 Ejercicios Resueltos". | 2026-07-10 | Media | En Progreso | Udemy |
  | 3  | Revisar Pipeline de Automatización | Optimizar manejo de excepciones en scripts de persistencia locales. | 2026-07-12 | Baja | Pendiente | Personal |

# REQUISITOS TÉCNICOS Y ARQUITECTURA DEL FLUJO EN N8N
El workflow debe estructurarse de manera modular desacoplada utilizando los siguientes nodos e integraciones:

1. **Nodo de Activación (Schedule Trigger):** Configurar una tarea cronometrada (Cron Expression) para ejecutarse diariamente en una hora fija específica (ej. 07:00 AM America/Caracas), garantizando la predictibilidad del flujo.
2. **Nodo de Extracción (Google Sheets Node):** Operación `Read / Get Many` conectada a la hoja `Tareas_Pendientes`. Se debe aplicar un filtro en la consulta (Query) o mediante un nodo intermedio (Filter Node) para recuperar únicamente los registros cuyo campo `estado` sea estrictamente diferente de "Completado".
3. **Nodo de Inteligencia Artificial / Procesamiento (Gemini Node):** Pasar la colección de datos JSON recuperada a un modelo avanzado de Gemini (ej. Gemini 1.5 Pro/Flash). El prompt interno del modelo debe exigirle:
   * Consolidar y clasificar la información sin pérdida de datos.
   * Generar un resumen ejecutivo con tono formal y asertivo.
   * Estructurar el output utilizando un formato limpio y jerárquico.
4. **Nodo de Despacho (Gmail Node):** Operación `Send Email`. El cuerpo del correo debe inyectar dinámicamente el output del nodo de Gemini. El asunto debe incluir variables temporales dinámicas en formato `[Resumen de Tareas] Diarias - YYYY-MM-DD`.

# RESTRICCIONES Y MANEJO DE EXCEPCIONES
* **Complejidad Temporal:** El procesamiento de los nodos intermedios debe configurarse para manejar eficientemente lotes de datos (Batch Processing) para evitar el estrangulamiento de cuotas de las APIs (Rate Limiting).
* **Idempotencia y Tolerancia a Fallos:** Configurar la política de reintentos (Retry On Failure) en los nodos de Google Sheets y Gmail para mitigar errores de red intermitentes.
* **Seguridad:** No quemar credenciales en texto plano (Hardcoding); se deben utilizar las abstracciones de credenciales nativas e integradas de n8n.

# FORMATO DE SALIDA SUGERIDO PARA EL CORREO (HTML)
El entregable final enviado por Gmail debe estructurarse mediante código HTML semántico, utilizando etiquetas de encabezado `<h1>`, `<h2>`, y listas `<ul>`/`<ol>` para destacar las prioridades "Alta" en secciones superiores bien delimitadas, asegurando una legibilidad óptima en cualquier cliente de correo.