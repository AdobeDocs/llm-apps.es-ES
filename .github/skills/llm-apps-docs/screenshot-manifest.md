---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1052'
ht-degree: 1%
---
# Manifiesto de captura de pantalla

Capture estos estados con la automatización del explorador mediante
[screenshots.md](screenshots.md). Seleccionar los puntos de comprobación relevantes para el artículo;
este manifiesto no autoriza la creación, generación, aprobación o
implementación.

Las capturas sin procesar permanecen en `docs-captures/<run-id>/`. Publicar solo revisado visualmente,
PNG saneados bajo `help/assets/`. Mantenga los nombres de archivo por debajo de estables cuando
actualizando imágenes.

## Configuración de infraestructura

Artículo: `/help/guides/create-app.md`

Directorio de salida: `help/assets/guide-infrastructure/`

| Nombre del archivo | Estado y contexto útil | Texto alternativo sugerido |
|---|---|---|
| `setup-choice.png` | Introducción a las dos opciones de infraestructura visibles | Cree su aplicación LLM: elija una infraestructura administrada de Adobe o sus propios repositorios |
| `app-details.png` | Nombre de la aplicación, programa de AEM, región de Analytics y advertencia de configuración inmutable | Detalles de la aplicación: introduzca un nombre, seleccione un programa de AEM y elija una región de Analytics |
| `action-repository.png` | Ruta del repositorio propio con GitHub seleccionado e instrucciones de conexión del repositorio | Configuración de la infraestructura de acciones: conectar un repositorio de acciones vacío o compatible |
| `provisioning.png` | Configuración administrada de Adobe aprobada en curso | Configuración de la aplicación: Adobe está preparando la infraestructura |

Excluir detalles de cuenta, ID de organización, nombres de programas reales, repositorio
identificadores e ID de aplicaciones. Utilice campos vacíos o un nombre de sujeción neutro donde
posible. La validación del repositorio puede inicializar el código, por lo que debe obtener la aprobación antes de
captura de un resultado de validación.

## Crear acciones y revisar el trabajo generado

Artículo: `/help/guides/creating-actions.md`

Directorio de salida: `help/assets/guide-creating-actions/`

| Nombre del archivo | Estado y contexto útil | Texto alternativo sugerido |
|---|---|---|
| `build-choice.png` | Acciones de compilación seleccionadas automáticamente, se ha introducido un sitio web público y un control de inicio estable si se incluyen | Acciones: elija la generación automática e introduzca un sitio web público |
| `generating.png` | Generación en ejecución, tiempo estimado y Ir a acciones | Acciones: se está ejecutando la generación |
| `actions-ready-for-review.png` | Fijar nombres de acciones, revisar distintivos y revisar controles | Acciones: las acciones generadas están listas para su revisión y no se implementan |
| `generated-action-review.png` | Detalles del widget LLM, detalles de la acción, Analytics, un aviso de solicitud de extracción generado y Marcar como revisado | Acción generada: inspeccionar el widget y el controlador antes de la aprobación |

Enmascarar vínculos de solicitud de extracción y repositorio privado. Recortar o eliminar contenido personal generado
datos de contacto cuando el artículo solo necesite el estado de revisión. No seleccionar
**Marcar como revisado** sin aprobación.

Capture el estado revisado de todas las acciones solo cuando la aprobación esté en ámbito y la variable
el estado realmente existe. No aprobar el trabajo generado para satisfacer una captura de pantalla
lista de comprobación.

## Crear una acción desde cero

Artículo: `/help/guides/create-action.md`

Directorio de salida: `help/assets/guide-create-action/`

| Nombre del archivo | Estado y contexto útil | Texto alternativo sugerido |
|---|---|---|
| `action-basic-info.png` | Buscar productos, su identificador derivado, una descripción útil y anotaciones de solo lectura | Crear nueva acción: información básica y anotaciones |
| `action-analytics-user-intent.png` | Recopilar la intención del usuario y las instrucciones predeterminadas de resumen anónimo | Crear nueva acción: revisar la colección de intenciones del usuario |
| `widget-metadata.png` | Tipo de bloque de Edge Delivery Services y un ejemplo de origen de zona protegida seguro | Crear nueva acción: tipo de widget y dominio de zona protegida |
| `widget-template-urls.png` | Ejemplo de script y URL de widget | Crear nueva acción: URL de scripts y widgets |
| `widget-permissions.png` | Permisos opcionales del explorador, con los permisos no utilizados desactivados | Crear nueva acción: permisos de explorador |
| `widget-csp.png` | Controles de dominio de recurso, conexión, trama, redirección y URI base | Crear nueva acción: dominios de política de seguridad de contenido |

El cuadro de diálogo de creación tiene las fichas **Acción** y **Metadatos de widget**. El editor guardado
tiene **detalles del widget LLM**, **detalles de acción** y **fichas de Analytics**. Capturar el
diseño que utiliza realmente el procedimiento. Rellene campos de ejemplo sin guardar y, a continuación,
descarte las ediciones.

## Autenticación

Artículo: `/help/guides/authentication.md`

Directorio de salida: `help/assets/guide-authentication/`

| Nombre del archivo | Estado y contexto útil | Texto alternativo sugerido |
|---|---|---|
| `auth-core-settings.png` | Fase, autenticación habilitada, `https://auth.example.com`, `catalog:read`, `profile:read` y configuración avanzada | Autenticación: habilitar la autenticación y completar la configuración principal |
| `auth-per-action.png` | Acciones de sujeción que muestran Ninguno, Obligatorio, Opcional y un ámbito asignado | Autenticación: elija un modo de autenticación y ámbitos para cada acción |

Utilice la configuración sin guardar y deséchela después. Verificar que la autenticación guardada
permanece sin cambios. El paso de identificador de recursos reutiliza
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

Si la interfaz de usuario muestra una advertencia que entra en conflicto con el tiempo de ejecución implementado o el actual
comportamiento de la plataforma, documente la discrepancia. Es adecuado un recorte solo de tabla
al enseñar modos, pero no oculte una advertencia relevante sin explicarlo.

## Variables de aplicación

Artículo: `/help/guides/app-variables.md`

Directorio de salida: `help/assets/guide-app-variables/`

Use `GREETING_PREFIX` con `Good day` como valor de ejemplo y `Howdy` como
valor de sustitución. Nunca capture un valor de configuración real.

| Nombre del archivo | Estado y contexto útil | Texto alternativo sugerido |
|---|---|---|
| `variables-empty.png` | Espacio de trabajo de fase vacío, selector de Workspace y Agregar | Variables y secretos: espacio de trabajo de fase vacío |
| `add-variable-dialog.png` | Variable GREETING_PREFIX no guardada y aviso secreto de soporte actual | Añadir variable o secreto: introduzca GREETING_PREFIX y Good day |
| `variable-added.png` | Variable de sujeción aprobada con controles de nombre, tipo, valor, marca de tiempo y fila | Variables y secretos: GREETING_PREFIX en el espacio de trabajo de Stage |
| `update-variable-dialog.png` | Valor actual Buen día y valor nuevo sin guardar | Actualizar GREETING_PREFIX - cambiar Buenos días a Howdy |
| `delete-variable-dialog.png` | Cuadro de diálogo de confirmación sin confirmar la eliminación | Eliminar GREETING_PREFIX: inspeccionar la confirmación de eliminación |

Abrir un cuadro de diálogo no autoriza a guardar o eliminar. Reutilizar una existente segura
variable de sujeción o tarea antes de crear una. Conservar una imagen anterior solo después de
comprobar que sus campos y controles siguen coincidiendo con la interfaz de usuario actual.

## Implementación y pruebas

Artículo: `/help/guides/deploy-your-app.md`

| Directorio de salida | Nombre del archivo | Estado y contexto útil |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | Ensayo del entorno de destino y control Deploy antes de iniciar |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | Actual: Probar la tarjeta de la aplicación, las etiquetas de entorno y Copiar URL; enmascarar cada URL de tiempo de ejecución |
| `help/assets/guide-deploy/` | `deployment-history.png` | Columnas de entorno, estado y fecha de implementación sin identificadores de aplicación |

Utilice una implementación existente de solo lectura para la tarjeta de prueba y el historial cuando se le permita.
No inicie una implementación para obtener esos estados a menos que el usuario lo apruebe.
Capturar los cuadros de diálogo de ejecución o de implementación correcta solo cuando ese estado sea
pertinente y autorizado.

Para `/help/guides/test-in-chatgpt.md`, se conservó
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png` ilustra un producto
respuesta. No verifica la interfaz de usuario de configuración actual. Nueva configuración de ChatGPT o Claude
las capturas de pantalla requieren acceso autenticado actual; de lo contrario, utilice el proveedor oficial
y omita las imágenes no verificadas.

## Procedencia de captura existente

La ejecución de captura del 19/10/2026 utilizó la interfaz de usuario local solicitada por el usuario, con la IU/API coincidente
comprobaciones de origen. No establece la paridad de producción.

- Capturas sin procesar: `docs-captures/2026-10-19/` y su subdirectorio `full-review/`.
- Las imágenes públicas anteriores o reemplazadas permanecen en los subdirectorios `retired/` locales.
- La sujeción de la documentación alcanzó la fase de revisión de la acción generada. Sin acción
aprobaciones, combinaciones de solicitudes de extracción, implementaciones o autenticación/variable guardada
los cambios formaban parte de esa ejecución de captura.
- Las imágenes de fila, actualización y eliminación de variables de una ejecución anterior se conservaron después de
comprobar sus controles con la implementación actual.
- No se han vuelto a capturar las pantallas de configuración de terceros. Las capturas de pantalla de configuración obsoletas eran
eliminado en lugar de presentado como actual.

Para cada nueva actualización, actualice la fecha, el entorno de origen, los estados verificados,
desinfección y los límites de verificación restantes. No copiar la procedencia antigua
adelante como prueba de una nueva captura.

### Eliminación del interruptor del editor de acciones, 2026-10-19

Se actualizó `guide-creating-actions/generated-action-review.png` de la ejecución
IU local después de quitar el conmutador Habilitado del editor. Visibilidad de la página de la aplicación
el interruptor sigue siendo el control de activación/desactivación de la acción; configuración de exposición del modelo y el widget
permanecer en el editor. El vínculo del repositorio está enmascarado y la navegación de la cuenta es
excluido. No se guardó, aprobó, alternó ni implementó ninguna acción durante esta actualización.
Captura sin procesar: `docs-captures/2026-10-19/remove-enabled/`.
