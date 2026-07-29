---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# Manifiesto de captura de pantalla de incorporación

Capturar bandeja de entrada: `docs-captures/<YYYY-MM-DD>/`

Directorio de salida: `help/assets/guide-onboarding-agent/`

Capture solo puntos de comprobación que ayuden materialmente al usuario a tomar una decisión o verificar el estado.

Los nombres de archivo de Source no necesitan coincidir con los nombres de archivo finales. La aptitud asigna capturas de pantalla por estado de IU visible, conserva los archivos sin procesar y crea copias saneadas con los nombres que aparecen a continuación.

## Capturas requeridas

### `app-details-onboarding.png`

- Estado: Nombre de la aplicación, región de análisis y **Generar mi aplicación automáticamente** seleccionada.
- Incluir: Detalles de la aplicación, región de análisis y el principio de Generar mi aplicación.
- Texto alternativo: `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- Estado: Repositorio EDS vacío inicializado con plantillas de AEM; se requiere AEM Code Sync.
- Incluir: mensaje de validación del repositorio de EDS y vínculo de instalación.
- Texto alternativo: `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- Estado: AEM Code Sync instalado, pero el usuario actual no es administrador del sitio de EDS.
- Incluir: el mensaje de validación completo y **Abrir AEM Live Admin**.
- Texto alternativo: `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- Estado: la página Acciones mientras la incorporación está activa.
- Include: mensaje de progreso y pasos de generación.
- Texto alternativo: `Actions — generating recommendations`

### `actions-ready-for-review.png`

- Estado: lista de acciones generada después de la incorporación termina y antes de la aprobación.
- Incluir: nombres de acción, estado generado/de revisión y control de revisión.
- Usar solo contenido de sujeción.
- Texto alternativo: `Actions — generated actions ready for review`

### `generated-action-review.png`

- Estado: acción generada por un representante.
- Incluir: navegación de metadatos de acción y widget, resultado de generación de controladores y **Marcar como revisado**.
- Máscara: propietario del repositorio si es necesario.
- Texto alternativo: `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- Estado: se han revisado todas las acciones generadas.
- Incluir: **Todas las acciones se han revisado**, distintivos de acción y **Ir a la página de la aplicación**.
- Texto alternativo: `Actions — all generated actions reviewed`

### `deploy-stage.png`

- Estado: cuadro de diálogo de implementación antes de iniciar.
- Incluir: entorno de destino de ensayo e **Implementación**.
- Texto alternativo: `Deploy — select the Stage environment`

### `deploy-running.png`

- Estado: Canalización de implementación en ejecución.
- Incluye: pasos de preparación, inicio, compilación y publicación.
- Texto alternativo: `Deploy — deployment pipeline running`

### `deploy-successful.png`

- Estado: implementación de ensayo correcta.
- Include: entorno y estado de éxito.
- Máscara: área de nombres de tiempo de ejecución, URL de MCP completa, ID, marcas de tiempo si se identifican.
- Texto alternativo: `Deploy — successful staging deployment`

### `app-mcp-url.png`

- Estado: Probar la sección de la aplicación después de la implementación.
- Incluir: Entorno de ensayo, **Copiar URL** e historial de implementación correcto.
- Máscara: la dirección URL del servidor MCP.
- Texto alternativo: `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- Estado: página de complementos de ChatGPT.
- Incluir: pestaña Plugins, botón Buscar y crear.
- Texto alternativo: `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- Cuadro de diálogo Estado: Nuevo complemento.
- Incluye: nombre, descripción, URL del servidor, autenticación, confirmación y Crear.
- Máscara: la dirección URL del servidor MCP.
- Texto alternativo: `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- Estado: Confirmación después de la creación del complemento.
- Incluir: **Agregar <plugin> a ChatGPT &#x200B;** y**&#x200B; Connect &#x200B;**.
- Máscara: URL del explorador e identificadores de conector.
- Texto alternativo: `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- Estado: Complemento de sujeción invocado en ChatGPT.
- Incluye: aplicación adjunta, widget generado y respuesta de texto.
- Excluir: historial de conversaciones, nombre de cuenta y aplicaciones no relacionadas.
- Texto alternativo: `ChatGPT — generated LLM App plugin response`

## Capturas opcionales

Añada una captura solo cuando la prosa no pueda explicar la decisión claramente:

- Selección de acceso al repositorio de la aplicación de GitHub.
- Error al incorporar el estado para la solución de problemas.
- Cargar icono de complemento.

No agregue capturas de pantalla para listas de campos estáticos que ya estén limpias en prose.
