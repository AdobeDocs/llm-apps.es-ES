---
title: Documentación de referencia para aplicaciones LLM de Adobe
description: Referencia de nivel de campo para la configuración de acciones en la IU de aplicaciones LLM de Adobe.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 6%

---


# Material de referencia {#reference-material}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Esta sección proporciona una referencia de nivel de campo para la configuración de acciones en la interfaz de usuario de [!DNL Adobe LLM Apps].

## Parámetros de acción

Los parámetros de entrada son los valores que la plataforma LLM ([!DNL ChatGPT], Claude) envía al controlador de acciones. El modelo los extrae del mensaje del usuario y los asigna automáticamente a estos campos.

| Propiedad | Descripción |
|----------|-------------|
| **Nombre** | El identificador del parámetro (por ejemplo, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` o `Boolean` |
| **Descripción** | Una explicación legible en lenguaje natural: la plataforma LLM utiliza esto para extraer el valor correcto |
| **Requerido** | Si se selecciona, el modelo debe proporcionar este parámetro antes de invocar la acción |

### Parámetros de archivo

Los parámetros de archivo llevan objetos de archivo con las propiedades `download_url` y `file_id`. Defina nombres de campos de entrada que deban recibir datos de archivo cuando un usuario cargue un archivo en la conversación.

## Campos de metadatos

### Información básica

| Campo | Requerido | Descripción |
|-------|----------|-------------|
| **Nombre de la acción** | Sí | Identificador de la acción (por ejemplo, *Buscar productos*) |
| **Descripción** | Sí | Explicación de lo que hace la acción: la plataforma LLM utiliza esto para decidir cuándo invocarlo |

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

### Visibilidad

| Conmutar | Descripción |
|--------|-------------|
| **Exponer a modelo de IA** | El modelo de IA puede invocar la acción durante las conversaciones |
| **Mostrar como widget en la superficie de la aplicación** | La acción procesa un widget visual en la aplicación |

### Información del widget

| Campo | Descripción |
|-------|-------------|
| **Tipo** | Tecnología de widget: actualmente **[!UICONTROL EDS]** |
| **Dominio de widget (origen de zona protegida)** | Origen donde se aloja el widget; debe ser único por aplicación |
| **Prefiere borde** | Si se selecciona, el widget se procesa dentro de una tarjeta con borde en la plataforma LLM |

### URL de plantilla

| Campo | Descripción |
|-------|-------------|
| **[!UICONTROL URL de script]** | Script de punto de entrada — `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`. Compartido entre todas las acciones |
| **URL de incrustación de widget** | Página EDS para esta acción: `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`. Únicos por acción |

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

