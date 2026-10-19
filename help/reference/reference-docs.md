---
title: Campos de acción y widget
description: Definiciones de los campos de metadatos de acción, parámetros, widgets, CSP y permisos en aplicaciones LLM de Adobe.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '890'
ht-degree: 3%
---

# Campos de acción y widget {#action-widget-configuration}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta página para buscar campos en el editor de acciones. Para ver los pasos de creación, consulte [Crear una acción desde cero](/help/guides/create-action.md).

## Buscar la configuración

El cuadro de diálogo **[!UICONTROL Crear nueva acción]** tiene las fichas **[!UICONTROL Acción]** y **[!UICONTROL Metadatos de widget]**.

Después de la creación, el editor de acciones guardadas utiliza tres pestañas:

| Pestaña | Configuración |
|---|---|
| **[!UICONTROL Detalles del widget LLM]** | Tipo de widget, dominio de zona protegida, descripción del widget, URL de plantilla y CSP |
| **[!UICONTROL Detalles de la acción]** | Identificador de código, descripción, parámetros de entrada y **[!UICONTROL configuración de LLM]**, incluidas anotaciones, texto de estado de invocación y visibilidad |
| **[!UICONTROL Análisis]** | Recopilación de intenciones del usuario e instrucciones |

Para guardar las ediciones, seleccione **[!UICONTROL Guardar cambios]**. Las acciones generadas que esperan revisión muestran **[!UICONTROL Marcar como revisado]**, que también administra las solicitudes de extracción generadas.

## Parámetros de acción

Los parámetros de entrada son los valores que la plataforma LLM envía al controlador de acciones. El modelo extrae los valores del mensaje del usuario y los asigna a los parámetros configurados.

| Propiedad | Descripción |
|----------|-------------|
| **Nombre** | El identificador del parámetro (por ejemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` o `Boolean` |
| **Descripción** | Una explicación legible en lenguaje natural: la plataforma LLM utiliza la explicación para extraer el valor correcto |
| **Requerido** | Si se selecciona, el modelo debe proporcionar este parámetro antes de invocar la acción |

### Parámetros de archivo

Los parámetros de archivo son nombres de campos de entrada configurados en el editor de acciones. Cuando un usuario carga un archivo, el host proporciona un objeto de archivo para esos argumentos, que suelen incluir `download_url` y `file_id`.

## Campos de metadatos

### Información básica

| Campo | Requerido | Descripción |
|-------|----------|-------------|
| **Nombre de la acción** | Sí | Nombre para mostrar de la acción (por ejemplo, *Buscar productos*) |
| **Descripción** | Sí | Explicación de lo que hace la acción: la plataforma LLM utiliza la explicación para decidir cuándo invocar la acción |

El cuadro de diálogo de creación muestra una vista previa de un **identificador de código** inmutable derivado del nombre de la acción. Es único dentro de la aplicación y contiene hasta 63 caracteres, empezando por una letra minúscula y usando letras minúsculas, dígitos, guiones o guiones bajos. Asigna la acción a `actions/<code-identifier>/index.js` en el repositorio del controlador.

### Anotaciones

Sugerencias opcionales que describen el comportamiento de la acción:

| Anotación | Descripción |
|------------|-------------|
| **Sugerencia destructiva** | La acción puede eliminar o sobrescribir datos |
| **Idempotente (los mismos argumentos = sin efecto adicional)** | Repetir la misma solicitud no tiene ningún efecto adicional en el entorno; las solicitudes repetidas no necesariamente devuelven datos idénticos |
| **Sugerencia para abrir el mundo** | La acción interactúa con sistemas externos |
| **Sugerencia de solo lectura** | La acción solo lee datos, nunca escribe |

### Metadatos de OpenAI

| Campo | Longitud máxima | Descripción |
|-------|------------|-------------|
| **Invocando texto de estado** | 64 caracteres | Mensaje mostrado en la plataforma LLM mientras se ejecuta la acción (por ejemplo, *Cargando productos...*) |
| **Texto de estado invocado** | 64 caracteres | Mensaje mostrado tras completarse la acción (por ejemplo, *Productos cargados...*) |
| **Descripción del widget** | 512 caracteres | Se asigna a `_meta["openai/widgetDescription"]`; resume el componente procesado para el modelo y reduce la narración repetida |

La descripción de la acción controla cuándo el modelo selecciona la acción. La descripción del widget explica lo que muestra el componente después de que se procese.

### Visibilidad

Para activar o desactivar una acción, usa su conmutador **[!UICONTROL Visibilidad]** en la página de la aplicación. El conmutador controla si la siguiente implementación incluye la acción. El editor de acciones no tiene un interruptor de activación/desactivación independiente.

El siguiente modelo de control de configuración del editor de acciones y la exposición del widget, no el estado de activación/desactivación en el nivel de aplicación:

| Conmutar | Descripción |
|--------|-------------|
| **Exponer a modelo de IA** | El modelo de IA puede invocar la acción durante las conversaciones |
| **Mostrar como widget en la superficie de la aplicación** | La acción procesa un widget visual en la aplicación |

La configuración de exposición del modelo y el widget no reemplaza la selección de un tipo de widget.

### Análisis

| Campo | Descripción |
|-------|-------------|
| **Recopilar intención del usuario** | Habilitado de forma predeterminada. Solicita un resumen anónimo del objetivo que provocó la acción de Analytics |
| **Descripción de intención de usuario** | Instrucciones utilizadas para solicitar el resumen, hasta 2000 caracteres. La opción predeterminada excluye la información personal y confidencial. Revise cualquier personalización que infrinja su política de privacidad |

## Campos de widget

### Información del widget

| Campo | Descripción |
|-------|-------------|
| **Tipo / Tipo de widget** | En el cuadro de diálogo de creación, seleccione **[!UICONTROL bloque de Edge Delivery Services]** o **[!UICONTROL Ningún widget]**. El editor guardado etiqueta la opción de solo texto **[!UICONTROL Sin IU interactiva]** |
| **Dominio de widget** | Origen de zona protegida utilizado por el host. La interfaz de usuario la identifica como necesaria para el envío de aplicaciones y única por aplicación. Esto es independiente de la **URL del widget** de la página EDS |
| **Prefiere borde** | Solicita un contenedor con borde; el procesamiento depende del host. Disponible en el cuadro de diálogo de creación |

### URL de plantilla

| Campo | Descripción |
|-------|-------------|
| **[!UICONTROL URL de script]** | URL HTTPS para el punto de entrada EDS `scripts/aem-embed.js`. Compartido entre acciones en el mismo proyecto EDS |
| **URL del widget** | URL HTTPS para la página EDS representada por la acción. Las acciones generadas configuran la URL automáticamente |

## Configuración de CSP

La política de seguridad de contenido controla los dominios externos con los que puede contactar el widget iframe. Todos los dominios externos deben estar explícitamente incluidos en la lista de permitidos.

| Directiva | Descripción |
|-----------|-------------|
| **Dominios de recursos** | Dominios para recursos estáticos: imágenes, fuentes, scripts y estilos |
| **Conectar dominios** | Dominios con los que el widget puede contactar a través de `fetch`, `XHR` o `WebSocket` |
| **Dominios de trama** | Los orígenes se permiten para los iframes anidados; los déclencheur exigen una revisión más estricta de la aplicación |
| **Dominios de redireccionamiento** | Destinos de confianza para vínculos de redireccionamiento de `openExternal` ([!DNL ChatGPT] específicos) |
| **Dominios URI base** | La directiva CSP `base-uri` (solo aplicaciones MCP para SDK, no [!DNL ChatGPT]) |

## Permisos

Los permisos de widget rigen el acceso a las API de hardware y explorador. Los permisos se asignan a la directiva de permisos de iframe.

Los controles de permisos aparecen en **[!UICONTROL Permisos (específicos de MCP)]** en el cuadro de diálogo de creación. Conceder solo las capacidades que requiere el widget; también se aplica la compatibilidad del host y el consentimiento del usuario.

| Permiso | Descripción |
|------------|-------------|
| **Cámara** | Acceder a la cámara del dispositivo |
| **Micrófono** | Acceder al micrófono del dispositivo |
| **Geolocalización** | Acceso a la ubicación del usuario |
| **Portapapeles** | Leer o escribir en el portapapeles |
