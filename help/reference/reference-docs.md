---
title: Campos de acción y widget
description: Definiciones de los campos de metadatos de acción, parámetros, widgets, CSP y permisos en aplicaciones LLM de Adobe.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '606'
ht-degree: 5%

---


# Campos de acción y widget {#action-widget-configuration}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta página para buscar campos en el editor de acciones. Para ver el recorrido de creación completo, consulte [Crear una acción desde cero](/help/guides/create-action.md).

## Parámetros de acción

Los parámetros de entrada son los valores que la plataforma LLM envía al controlador de acciones. El modelo los extrae del mensaje del usuario y los asigna a estos campos.

| Propiedad | Descripción |
|----------|-------------|
| **Nombre** | El identificador del parámetro (por ejemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` o `Boolean` |
| **Descripción** | Una explicación legible en lenguaje natural: la plataforma LLM utiliza esto para extraer el valor correcto |
| **Requerido** | Si se selecciona, el modelo debe proporcionar este parámetro antes de invocar la acción |

### Parámetros de archivo

Los parámetros de archivo son nombres de campos de entrada configurados en el editor de acciones. Cuando un usuario carga un archivo, el host proporciona un objeto de archivo para esos argumentos, que suelen incluir `download_url` y `file_id`.

## Campos de metadatos

### Información básica

| Campo | Requerido | Descripción |
|-------|----------|-------------|
| **Nombre de la acción** | Sí | Nombre para mostrar de la acción (por ejemplo, *Buscar productos*) |
| **Descripción** | Sí | Explicación de lo que hace la acción: la plataforma LLM utiliza esto para decidir cuándo invocarlo |

Después de la creación, el editor también muestra un **identificador de código** inmutable. Asigna la acción a `actions/<code-identifier>/index.js` en el repositorio del controlador.

### Anotaciones

Sugerencias opcionales que describen el comportamiento de la acción:

| Anotación | Descripción |
|------------|-------------|
| **Sugerencia destructiva** | La acción modifica o elimina datos |
| **Idempotente** | Llamar a la acción varias veces con los mismos argumentos tiene el mismo resultado |
| **Sugerencia para abrir el mundo** | La acción interactúa con sistemas externos |
| **Sugerencia de solo lectura** | La acción solo lee datos, nunca escribe |

### Metadatos de OpenAI

| Campo | Longitud máxima | Descripción |
|-------|------------|-------------|
| **Invocando texto de estado** | 64 caracteres | Mensaje mostrado en la plataforma LLM mientras se ejecuta la acción (por ejemplo, *Cargando productos ...* ) |
| **Texto de estado invocado** | 64 caracteres | Mensaje mostrado después de que se complete la acción (por ejemplo, *Productos cargados...* ) |
| **Descripción del widget** | 512 caracteres | Se asigna a `_meta["openai/widgetDescription"]`; resume el componente procesado para el modelo y reduce la narración repetida |

La descripción de la acción controla cuándo el modelo selecciona la acción. La descripción del widget explica lo que muestra el componente después de que se procese.

### Visibilidad

| Conmutar | Descripción |
|--------|-------------|
| **Exponer a modelo de IA** | El modelo de IA puede invocar la acción durante las conversaciones |
| **Mostrar como widget en la superficie de la aplicación** | La acción procesa un widget visual en la aplicación |

### Análisis

| Campo | Descripción |
|-------|-------------|
| **Recopilar intención del usuario** | Recopila un resumen de la conversación que llevó a la acción para Analytics |

## Campos de widget

### Información del widget

| Campo | Descripción |
|-------|-------------|
| **Tipo** | Tecnología de widget: actualmente **[!UICONTROL EDS]** |
| **Dominio de widget (origen de zona protegida)** | Origen donde se aloja el widget; debe ser único por aplicación |
| **Prefiere borde** | Si se selecciona, el widget se procesa dentro de una tarjeta con borde en la plataforma LLM |

### URL de plantilla

| Campo | Descripción |
|-------|-------------|
| **[!UICONTROL URL de script]** | URL HTTPS para el punto de entrada EDS `scripts/aem-embed.js`. Compartido entre acciones en el mismo proyecto EDS |
| **URL del widget** | URL HTTPS para la página EDS representada por esta acción. Las acciones generadas lo configuran automáticamente |

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

API de hardware y explorador a las que el widget puede acceder. Estos se asignan a la directiva de permisos de iframe.

| Permiso | Descripción |
|------------|-------------|
| **Cámara** | Acceder a la cámara del dispositivo |
| **Micrófono** | Acceder al micrófono del dispositivo |
| **Geolocalización** | Acceso a la ubicación del usuario |
| **Portapapeles** | Leer o escribir en el portapapeles |

