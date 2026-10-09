---
source-git-commit: 41bd4b6239171c7a3af7dc6349eaa3cbb880449c
workflow-type: tm+mt
source-wordcount: '1279'
ht-degree: 0%
---
# Manifiesto de captura de pantalla

Capturar bandeja de entrada: `docs-captures/<YYYY-MM-DD>/`

Capture solo puntos de comprobación que ayuden materialmente al usuario a tomar una decisión o verificar el estado.

Los nombres de archivo de Source no necesitan coincidir con los nombres de archivo finales. La aptitud asigna capturas de pantalla por estado de IU visible, conserva los archivos sin procesar y crea copias saneadas con los nombres que aparecen a continuación.

Cada guía siguiente declara su propio directorio de salida. Utilice el de la sección a la que pertenece la captura.

# Guía de incorporación

Directorio de salida: `help/assets/guide-onboarding-agent/`

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
- Incluir: **Agregar <plugin> a ChatGPT **y** Connect **.
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

# Guía de autenticación

Directorio de salida: `help/assets/guide-authentication/`

Referido por [authentication.md](../../../help/guides/authentication.md).

El paso **[!UICONTROL Copiar el identificador de recurso]** vuelve a utilizar la guía de incorporación
`app-mcp-url.png`. No volver a capturarlo.

Todas las capturas de esta sección muestran la configuración de seguridad. Máscara antes de guardar:

- La dirección URL **[!UICONTROL Issuer]** y cualquier nombre de host que identifique al proveedor de identidad o a su proveedor.
- La dirección URL completa del servidor MCP, dondequiera que aparezca.
- Identificadores de inquilino, cliente y organización.
- Nombre de la cuenta, avatar y correo electrónico.

Utilice valores de marcador de posición neutros donde un campo deba permanecer legible; por ejemplo, un emisor de
`https://auth.example.com`. Los nombres de ámbito deben leerse como ejemplos genéricos como `orders:read`.

## Capturas requeridas

### `auth-core-settings.png`

- Estado: **[!UICONTROL Configuración]** > **[!UICONTROL Autenticación]** con **[!UICONTROL Habilitar autenticación]** activada y **[!UICONTROL Configuración principal]** completada.
- Incluya: el selector **[!UICONTROL Workspace]** que muestra **[!UICONTROL Fase]**, **[!UICONTROL Habilitar autenticación]** en su propio estado, **[!UICONTROL Emisor]** y **[!UICONTROL Ámbitos admitidos]** que contienen al menos dos ámbitos.
- Incluya el control **[!UICONTROL Advanced settings]** contraído, para que el lector pueda ver que **[!UICONTROL JWKS URI]** es opcional y dónde se encuentra.
- Máscara: el nombre del host del emisor.
- Texto alternativo: `Authentication — enable authentication and complete the core settings`

Capturado 25-08-2026. Recortado para soltar el lienzo vacío; no se necesita máscara, porque
**[!UICONTROL El emisor]** se estableció en `https://auth.example.com` en el producto antes del
captura. Prefiero eso a editar la imagen después. **[!UICONTROL Ámbitos admitidos]** suspensiones
un ámbito (`read:all`); dos ilustrarían mejor el campo, pero no vale la pena
volver a capturar por sí solo.

### `auth-per-action.png`

- Estado: **[!UICONTROL Configuración por acción]** después de habilitar la autenticación, con los modos mezclados deliberadamente.
- Incluir: al menos tres acciones, una por modo — **[!UICONTROL Ninguno]**, **[!UICONTROL Obligatorio]** y **[!UICONTROL Opcional]** — y la columna **[!UICONTROL Ámbitos]** rellenada en las cerradas.
- Incluir: **[!UICONTROL Requerir autenticación en todas las acciones]**, idealmente en su estado indeterminado, que es lo que produce una configuración mixta.
- Utilice solo nombres de acción de sujeción.
- Texto alternativo: `Authentication — set an auth mode and scopes for each action`

Capturado 25-08-2026. Solo recortado, nada que ocultar. Muestra los tres modos, a rellenado
**[!UICONTROL Ámbitos]** celda y **[!UICONTROL Requiere autenticación en todas las acciones]** en su
estado indeterminado, con `Test Action 1/2/3` como nombres de sujeción.

Recortar **dentro** del borde del contenedor del panel de configuración (una regla de 1px de altura completa se coloca en cada uno)
lado de la captura, y dejando cualquiera de los dos en el marco se lee como una línea perdida por el borde de la
imagen.

La advertencia del propio producto acerca de [!DNL Claude] que aplica la autenticación por conector fue
**no se observa en esta ficha en dos rondas de captura**, por lo que no es obligatorio aquí. El
La guía indica ese comportamiento en prosa en su lugar. Si la advertencia no existe en una versión posterior,
capturarlo como `auth-claude-warning.png` y agregar una entrada.

### `chatgpt-authentication-mode.png`

- Estado: el cuadro de diálogo **[!UICONTROL Nuevo complemento]** con la lista desplegable **[!UICONTROL Autenticación]** abierta.
- Incluir: los tres valores — **[!UICONTROL No Auth]**, **[!UICONTROL Mixed]** y **[!UICONTROL OAuth]** — para que la tabla de asignación de la guía pueda comprobarse con el control real.
- Máscara: la dirección URL del servidor MCP y cualquier identificador de conector de la dirección URL del explorador.
- Texto alternativo: `ChatGPT — select the authentication mode for the plugin`

Configúrelo de la misma manera que el `chatgpt-new-plugin.png` de la guía de incorporación: la tarjeta de diálogo con
un margen de la página aún visible a su alrededor, aproximadamente 40 píxeles a la izquierda y arriba. No recortar vaciado a
la tarjeta.

Se ha capturado el 25 de agosto de 2026, modo claro, para hacer coincidir todas las capturas de la documentación. El
El menú desplegable oculta el campo **[!UICONTROL URL del servidor]**, de modo que la URL de MCP no es legible, pero
su material translúcido permite que una imagen borrosa del contenido de ese campo se desangre junto a la
opciones. Las tres filas sin resaltar se volvieron a pintar con el relleno del panel y sus etiquetas
se ha vuelto a procesar, lo cual lo elimina. Verificar por muestreo, no por ojo: el sangrado es lo suficientemente débil como para
falta y es la URL del servidor MCP.

Tenga en cuenta que el control activo ofrece **cuatro** valores: **[!UICONTROL OAuth]**, **[!UICONTROL acceso
token/clave de API]**, **[!UICONTROL Sin autenticación]** y **[!UICONTROL Mixto]**. Asignación de la guía
Esta tabla abarca solo los tres a los que se pueden asignar los modos de autenticación de una aplicación, lo que es correcto, pero no lo son
describa la lista desplegable como si tuviera tres opciones.

## Capturas opcionales

Añádalo sólo si la prosa resulta insuficiente:

- `auth-scope-blocked.png` — **[!UICONTROL Guardar]** bloqueado porque una acción requiere que falte un ámbito de **[!UICONTROL Ámbitos admitidos]**. Útil para la entrada de resolución de problemas.
- El inicio de sesión en mitad de la conversación requiere una acción **[!UICONTROL Opcional]**. IU de propiedad de Platform que cambia con frecuencia y ya se describe en prosa.

No capture la página de inicio de sesión propia del proveedor de identidad. Identifica al proveedor, al que no da nombre esta documentación.

# Guía de variables de aplicación

Directorio de salida: `help/assets/guide-app-variables/`

[app-variables.md](../../../help/guides/app-variables.md) hace referencia a él.

Utilice la variable de sujeción `GREETING_PREFIX` con el valor `Good day`, en el área de trabajo **[!UICONTROL Fase]**. Los valores de las variables son visibles en la tabla, por lo que nunca capture una configuración real.

## Capturas requeridas

### `variables-empty.png`

- Estado: **[!UICONTROL Configuración]** > **[!UICONTROL Variables y secretos]** sin variables en **[!UICONTROL Fase]**.
- Incluye: la navegación de configuración, el selector **[!UICONTROL Workspace]** y **[!UICONTROL Add]**.
- Texto alternativo: `Variables & Secrets — empty Stage workspace with the Add button`

Registrado el 05-10-2026. Recortado para soltar el lienzo vacío; nada que ocultar.

### `add-variable-dialog.png`

- Estado: **[!UICONTROL Se ha completado el cuadro de diálogo Agregar variable o secreto]** antes de guardar.
- Incluir: los *secretos aún no se admiten* aviso, **[!UICONTROL Nombre]** `GREETING_PREFIX`, **[!UICONTROL Tipo]** **[!UICONTROL Variable]** y **[!UICONTROL Valor]** `Good day`.
- Texto alternativo: `Add Variable or Secret — GREETING_PREFIX set to Good day`

Registrado el 05-10-2026. Recortado debajo del cuadro de diálogo; nada que ocultar.

### `variable-added.png`

- Estado: la tabla de variables después de guardar, con una fila `GREETING_PREFIX`.
- Incluya: **[!UICONTROL Nombre]**, **[!UICONTROL Tipo]**, **[!UICONTROL Valor]**, **[!UICONTROL Última actualización]**, y los controles de copiar, editar y eliminar.
- Texto alternativo: `Variables & Secrets — GREETING_PREFIX saved in the Stage workspace`

Registrado el 05-10-2026. Recortado para soltar el lienzo vacío; nada que ocultar.

### `update-variable-dialog.png`

- Estado: **[!UICONTROL Actualizar el cuadro de diálogo GREETING_PREFIX]** con **[!UICONTROL Valor actual]** `Good day` y **[!UICONTROL Nuevo valor]** `Howdy`.
- Texto alternativo: `Update GREETING_PREFIX — change the value from Good day to Howdy`

Registrado el 05-10-2026. Se recortaron el título de la página recortada y la superposición vacía debajo del cuadro de diálogo; se pintó el símbolo de intercalación de texto después de `Howdy`. Nada que enmascarar.

### `delete-variable-dialog.png`

- Estado: **[!UICONTROL Eliminar GREETING_PREFIX?]** diálogo de confirmación.
- Texto alternativo: `Delete GREETING_PREFIX — confirm the permanent deletion`

Registrado el 05-10-2026. Se ha recortado la superposición vacía debajo del cuadro de diálogo; no hay nada que ocultar.
