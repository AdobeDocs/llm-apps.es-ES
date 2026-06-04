---
title: Crear una acción
description: Obtenga información sobre cómo definir una acción en la interfaz de usuario de aplicaciones LLM, incluidos metadatos, parámetros de entrada y configuración de widgets.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '868'
ht-degree: 1%

---


# Crear una acción

Esta guía le explica cómo definir una acción en la interfaz de usuario de [!DNL LLM Apps]. Para obtener información general sobre qué son las acciones y cómo funcionan, consulte [Conceptos principales](/help/overview/overview.md#actions).

## Abrir la página Acciones

Vaya a **[!UICONTROL Acciones]** en la barra lateral izquierda o haga clic en **Ir a Acciones** en la página Detalles de la aplicación. Si aún no existen acciones, la página muestra un estado vacío.

![Página de acciones — aún no hay acciones](/help/assets/guide-create-action/actions-empty.png)

Haga clic en **+ Crear acción** para abrir el cuadro de diálogo de pantalla completa.

## Tarjetas de acción

Cada acción aparece como una tarjeta que muestra lo siguiente:

- La acción **name** y **description**
- Una **imagen de vista previa del widget** — generada automáticamente a partir del widget, que muestra el aspecto de la salida de acción dentro de la plataforma LLM
- **Insignias**: tipo de widget (**[!UICONTROL EDS]**), estado de implementación (**No implementado**, **Implementado en ensayo**, **Implementado en producción**), **Cambios no implementado** cuando la acción se ha modificado desde la última implementación y recuento de parámetros
- Alternar **Visibilidad**: habilita o deshabilita la acción en el extremo activo sin volver a implementar.
- Un vínculo **Revisar** en la esquina superior derecha para abrir el editor de acciones

![Página de acciones — tarjetas de acción](/help/assets/guide-create-action/action-card.png)

Cuando se han modificado una o más acciones desde la última implementación, aparece el banner **Implementación necesaria** en la parte superior de la página Acciones. Vuelva a implementar la aplicación para aplicar los cambios.

## Pestaña Acción

El cuadro de diálogo tiene dos fichas: **Acción** y **[!UICONTROL Metadatos de widget]**.

### Información básica

![Crear acción — información básica](/help/assets/guide-create-action/action-basic-info.png)

- **Nombre de la acción** (obligatorio): el identificador de la acción (por ejemplo, *Buscar productos*).
- **Descripción** (obligatorio): una explicación clara de lo que hace la acción. La plataforma LLM utiliza esto para decidir cuándo invocar la acción. Por ejemplo: *Busque en el catálogo de productos por palabra clave. Devuelve productos coincidentes con nombre, categoría, imagen y precio.*
- **Anotaciones**: sugerencias opcionales que describen el comportamiento de la acción:

  | Anotación | Descripción |
  |-----------|-------------|
  | **Sugerencia destructiva** | La acción modifica o elimina datos |
  | **Idempotente** | Llamar a la acción varias veces con los mismos argumentos produce el mismo resultado |
  | **Sugerencia para abrir el mundo** | La acción interactúa con sistemas externos |
  | **Sugerencia de solo lectura** | La acción solo lee datos, nunca escribe |

  Consulte [Referencia: Campos de metadatos](/help/reference/reference-docs.md) para obtener detalles.

### Metadatos de OpenAI

- **Invocando texto de estado**: el mensaje mostrado en la plataforma LLM mientras se ejecuta la acción (máximo 64 caracteres). Ejemplo: *Cargando productos...*
- **Texto de estado invocado**: el mensaje que se muestra después de completarse la acción (máximo 64 caracteres). Ejemplo: *Productos cargados.*

### Parámetros de visibilidad y entrada

**Visibilidad** controla dónde está disponible la acción:

- **Exponer al modelo de IA**: el modelo de IA puede invocar la acción.
- **Mostrar como widget en la superficie de la aplicación** — la acción procesa un widget visual.

**Parámetros de entrada** son los valores que la plataforma LLM envía a su controlador. El modelo los extrae automáticamente del mensaje del usuario. Para *Buscar productos* definimos:

- **category** (cadena, opcional): filtro de categoría para reducir los resultados (por ejemplo, un tipo de producto o departamento).
- **consulta** (cadena, opcional): término de búsqueda de texto libre.

Cada parámetro tiene **Name**, **Type** (String, Number, Integer, Boolean), **Description** y una casilla de verificación **Obligatorio**. Haga clic en **+ Agregar** para agregar más parámetros.

Para obtener más información, vea [Referencia: parámetros de acción](/help/reference/reference-docs.md).

### Análisis

![Crear acción — intención de usuario de Analytics](/help/assets/guide-create-action/action-analytics-user-intent.png)

- **Intento del usuario** — cuando está habilitada, se le pide a [!DNL ChatGPT] que resuma la conversación que llevó a llamar a esta acción. Ese resumen se recopila y aparece en Analytics, lo que le ofrece insight sobre lo que los usuarios intentaban lograr cuando se activó la acción.

## Pestaña Metadatos del widget

Esta pestaña configura cómo se representa la respuesta visual de la acción en la plataforma LLM. Para obtener una explicación completa del funcionamiento de los widgets, consulte [Guía: Configurar el widget (EDS)](/help/guides/widgets.md).

![Crear acción — metadatos de widget](/help/assets/guide-create-action/widget-metadata.png)

### Información del widget

- **Tipo**: la tecnología de widget (actualmente **[!UICONTROL EDS]**).
- **Dominio del widget (origen de la zona protegida)**: el origen en el que está alojado el widget. Necesario para el envío de aplicaciones a OpenAI; debe ser único para cada aplicación.
- **Prefiere borde** — procesa el widget dentro de una tarjeta con borde.

### URL de plantilla

- **[!UICONTROL URL del script]**: el punto de entrada que arranca el widget, compartido en todas las acciones:
  `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`
- **URL de incrustación de widget** — la página EDS para esta acción específica:
  `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`

### Permisos

API de hardware y explorador a las que puede acceder el widget:

| Permiso | Descripción |
|-----------|-------------|
| **Cámara** | Acceder a la cámara del dispositivo |
| **Micrófono** | Acceder al micrófono del dispositivo |
| **Geolocalización** | Acceso a la ubicación del usuario |
| **Portapapeles** | Leer o escribir en el portapapeles |

### Configuración de CSP

![Crear acción — permisos y CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Controla los dominios externos con los que puede contactar el iframe del widget. Todos los dominios externos deben estar explícitamente incluidos en la lista de permitidos.

| Directiva | Descripción |
|-----------|-------------|
| **Dominios de recursos** | Dominios para recursos estáticos: imágenes, fuentes, scripts y estilos |
| **Conectar dominios** | Dominios con los que el widget puede contactar a través de `fetch`, `XHR` o `WebSocket` |
| **Dominios de trama** | Los orígenes se permiten para los iframes anidados; agregar entradas déclencheur una revisión de aplicación más estricta desde OpenAI |
| **Dominios de redireccionamiento** | Destinos de confianza para vínculos de redireccionamiento de `openExternal` ([!DNL ChatGPT] específicos) |
| **Dominios URI base** | La directiva CSP `base-uri` (solo SDK de aplicaciones MCP, no compatible con [!DNL ChatGPT]) |

Haga clic en **Crear nueva acción** para guardar.

## Después de crear una acción

La acción aparecerá como una tarjeta en la página Acciones:

![Página de acciones — acción creada](/help/assets/guide-create-action/actions-with-action.png)

Cada tarjeta muestra el nombre de la acción, la descripción, el distintivo de tipo (**[!UICONTROL EDS]**), el estado de implementación (**No implementado**) y el recuento de parámetros. Puede hacer clic en **...** para editarla o eliminarla, o bien hacer clic en **Revisar** para inspeccionar la configuración.

![Detalle de la aplicación — no implementado](/help/assets/guide-create-action/app-detail-not-deployed.png)

Los metadatos de la acción se han guardado, pero aún no se ha implementado ningún código. Para que la acción funcione, debe:

1. **Configurar el widget EDS**; consulte [Guía: Configurar el widget (EDS)](/help/guides/widgets.md).
2. **Escriba el controlador**; consulte [Guía: escriba el controlador de acciones](/help/guides/write-action-handler.md).
3. **[!UICONTROL Implementar]** — vea [Guía: Implementar su aplicación](/help/guides/deploy-your-app.md).

## Pasos siguientes

- [Guía: Configurar el widget (EDS)](/help/guides/widgets.md)

