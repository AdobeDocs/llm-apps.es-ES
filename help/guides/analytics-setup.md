---
title: Configuración de Adobe Analytics y Customer Journey Analytics
description: Reenvíe eventos de aplicaciones LLM de Adobe a su propio grupo de informes de Adobe Analytics y a la vista de datos de Customer Journey Analytics a través de un flujo de datos de Adobe Experience Platform.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# Configuración de Adobe Analytics y Customer Journey Analytics {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Envíe los eventos de su aplicación a [!DNL Adobe Analytics], [!DNL Customer Journey Analytics] o ambos. [!DNL LLM Apps] reenvía los eventos al Edge Network [!DNL Adobe Experience Platform] a través de un conjunto de datos de su propiedad. La secuencia de datos enruta los eventos a cada servicio que agrega a ella.

- **[!DNL Adobe Analytics]** informa de qué acciones llama la plataforma LLM, qué widget vincula a las personas seleccionadas y la intención detrás de cada llamada.
- **[!DNL Customer Journey Analytics]** analiza los mismos eventos por persona, en las vistas de datos que genere en los datos de [!DNL Experience Platform].

Esta configuración es independiente de los análisis integrados en [!DNL LLM Apps].

**En esta guía:** Aprenda qué configuración [!DNL LLM Apps] requiere en [!DNL Experience Platform], [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics], con vínculos al procedimiento de Experience League para cada pantalla de producto. A continuación, habilite Analytics en la aplicación, implemente en Fase y verifique los datos antes de realizar la implementación en Producción.

## Configurar lista de comprobación {#setup-checklist}

Complete cada paso marcado como **Requerido** para los servicios que use. Un paso omitido rara vez muestra un error. En su lugar, los datos no llegan.

| Paso | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. Crear el área de nombres de identidad &#x200B;](#identity-namespace) | Necesario | Necesario |
| [2. Crear el esquema &#x200B;](#schema) | Necesario | Necesario |
| [3. Crear el conjunto de datos &#x200B;](#dataset) | No se necesita | Requerido |
| [4. Preparar el grupo de informes](#report-suite) | Requerido | No se necesita |
| [5. Crear la secuencia de datos &#x200B;](#datastream) | Necesario | Necesario |
| [6. Habilitar Analytics e implementar en fase](#enable-analytics) | Necesario | Necesario |
| [7. Crear la conexión y la vista de datos &#x200B;](#cja) | No se necesita | Requerido |
| [8. Verificar los datos e implementar en el entorno de producción &#x200B;](#verify) | Necesario | Necesario |

Cree el área de nombres, el esquema, el conjunto de datos y el conjunto de datos en la misma zona protegida de [!DNL Experience Platform]. Utilice la zona protegida y la secuencia de datos de los que planea informar en Producción. Después de guardar la secuencia de datos en la aplicación, póngase en contacto con el equipo de aplicaciones LLM para cambiarla.

## Antes de empezar {#before-you-begin}

Necesita:

- **Un tiempo de ejecución y SDK compatibles.** El repositorio del controlador debe usar `@adobe/llm-apps-runtime` 1.2.0 o posterior. Para los widgets de EDS, utilice la versión LLM Apps SDK 0.1.13 o posterior. Para actualizar una aplicación anterior a la última versión en tiempo de ejecución y a SDK antes de habilitar Analytics, sigue [Actualizar una aplicación existente](/help/guides/upgrade-existing-app.md). Las aplicaciones que cumplen estos requisitos no necesitan una actualización.
- **Los grupos de campos agénticos.** Su organización debe estar habilitada para los grupos de campos Información de agente y Evento de conversación. Si estos grupos de campos no aparecen al agregar grupos de campos a un esquema, póngase en contacto con el equipo de cuenta de Adobe. Ver [Implementar perspectivas de conversación](https://experienceleague.adobe.com/es/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets). Las llamadas de acción se registran en el campo `agenticExperience.tools` de Información de agente, que se encuentra en Beta en [!DNL Experience Platform].
- **[!DNL Experience Platform]permisos** para crear áreas de nombres, esquemas, conjuntos de datos y flujos de datos de identidad en su zona protegida.
- **Para el permiso [!DNL Adobe Analytics]:** para administrar grupos de informes y reglas de procesamiento. Los administradores de productos tienen ambas de forma predeterminada.
- **Para [!DNL Customer Journey Analytics]:** acceso de administrador de producto para crear conexiones y vistas de datos.

## Lo que envía su aplicación {#what-your-app-sends}

Después de habilitar Analytics e implementar, la aplicación envía dos tipos de eventos:

| Evento | `eventType` | Enviado cuando | Incluye |
|---|---|---|---|
| Llamada de acción | `mcp.tool_call` | La plataforma LLM llama a una de las acciones de su aplicación, incluidas las llamadas que generan errores o devuelven un mensaje de inicio de sesión. | El identificador de código de la acción y si la llamada ha fallado. La intención del usuario, cuando la acción la recopila y la plataforma proporciona una. Cuando la plataforma los proporciona: el nombre de la plataforma, el ID de conversación, el ID de usuario de la plataforma, la configuración regional y la organización, y la ubicación aproximada del usuario. |
| Interacción de widget | `web.webinteraction.linkClicks` | Alguien selecciona un vínculo o un botón en un widget de Edge Delivery Services. | Etiqueta del vínculo o botón, de hasta 100 caracteres. Cuando la llamada de acción lo proporciona, su ID de conversación y su ID de turno. Adobe Web SDK también agrega la dirección URL del marco del widget, el tamaño de la pantalla y de la ventana del explorador, la zona horaria local y su propia versión. |

En [!DNL Adobe Analytics], cada llamada de acción cuenta como una vista de página de una página llamada `mcp:<code identifier>`, y cada interacción de widget cuenta como un clic en vínculo. Ver [tipos de eventos de Edge Network en Adobe Analytics](https://experienceleague.adobe.com/es/docs/analytics/implementation/aep-edge/hit-types).

Cada llamada de acción y cada interacción de widget es una visita de [!DNL Adobe Analytics] y un evento en su conjunto de datos de [!DNL Experience Platform]. En una aplicación con mucho tráfico, compruebe que el contrato cubre este volumen antes de activar Analytics.

Tenga en cuenta estos detalles cuando lea los informes:

- **Las llamadas con errores incluyen mensajes de inicio de sesión.** Si algunas acciones requieren el inicio de sesión, se espera una tasa de errores más alta para ellas.
- **Algunas llamadas rechazadas no se registran.** Se rechaza una llamada antes de que se ejecute la acción y no envía ningún evento cuando una plataforma distinta de [!DNL ChatGPT] llama a una acción que requiere el inicio de sesión sin un inicio de sesión válido o sin el ámbito requerido. En aplicaciones en las que cada acción requiere inicio de sesión, cualquier llamada que no tenga inicio de sesión se rechaza del mismo modo.
- **A menudo falta la intención del usuario.** La plataforma decide si se debe suministrar, por lo que muchas llamadas no tienen ninguna y aparecen como **No especificado**.
- **Los detalles de la plataforma varían.** Actualmente, [!DNL ChatGPT] los proporciona a todos. Para [!DNL Claude], solo se registra el nombre de la plataforma.
- **Las etiquetas de widget provienen de la página.** Para establecer una etiqueta estable, agregue un atributo `data-analytics` al vínculo o botón. De lo contrario, se utiliza su texto visible.

Para elegir qué acciones recopilan la intención del usuario, consulte [Configurar análisis](/help/guides/create-action.md#configure-analytics).

>[!NOTE]
>
>Los eventos pueden incluir un ID de usuario de plataforma y una ubicación aproximada. Revise la configuración de la política de privacidad y de control de datos antes de habilitar Analytics.

### Cómo los eventos identifican a las personas {#identities}

| Evento | Identidad principal | Espacio de nombres |
|---|---|---|
| Llamada de acción | El ID de usuario de la plataforma LLM, cuando la plataforma proporciona uno | `MCPHOSTUSER` |
| Interacción de widget | Un Experience Cloud ID (ECID) que Edge Network emite durante la llamada de acción. El identificador `MCPHOSTUSER` se incluye como identidad secundaria cuando está disponible. | `ECID` |

Algunas plataformas, como [!DNL Claude], no proporcionan un identificador de usuario. Las llamadas de acción desde esas plataformas no llevan ninguna identidad, por lo que [!DNL Customer Journey Analytics] las omite.

>[!NOTE]
>
>Cada llamada de acción obtiene un nuevo ECID y una interacción de widget utiliza el ECID de la llamada de acción que mostró el widget. Como resultado:
>
>- [!DNL Customer Journey Analytics] cuenta las llamadas de acción de un usuario como una persona, pero cuenta sus interacciones de widget como una persona diferente para cada llamada de acción.
>- [!DNL Adobe Analytics] puede contar cada llamada de acción como un visitante nuevo.

## Paso 1: Crear el área de nombres de identidad {#identity-namespace}

Se requiere **para:** [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics].

Las llamadas de acción identifican a las personas en el área de nombres `MCPHOSTUSER`, por lo que el área de nombres debe existir en su zona protegida. Cree un área de nombres personalizada con esta configuración:

| Configuración | Valor |
|---|---|
| Nombre para mostrar | `MCP host user` u otro nombre que elija |
| Símbolo de identidad | `MCPHOSTUSER`, exactamente como se muestra |
| Tipo de identidad | **[!UICONTROL ID individual entre dispositivos]** |

Para ver el procedimiento, vea [Crear áreas de nombres personalizadas](https://experienceleague.adobe.com/es/docs/experience-platform/identity/features/namespaces#create-namespaces).

## Paso 2: Crear el esquema {#schema}

Se requiere **para:** [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics].

Tanto la secuencia de datos como el conjunto de datos utilizan este esquema. Cree un esquema con esta configuración:

| Configuración | Valor |
|---|---|
| Tipo de esquema | **[!UICONTROL Estándar]**, creado con la opción **[!UICONTROL Manual]** |
| Clase base | **[!UICONTROL Evento de experiencia]** |
| Grupos de campos | Información de la agencia, evento de conversación, detalles web, detalles del entorno y detalles de implementación |
| Nombre para mostrar | Cualquier nombre, por ejemplo `AEM LLM Apps` |

>[!IMPORTANT]
>
>Añada los cinco grupos de campos. [!DNL LLM Apps] requiere información de agente y evento de conversación para las llamadas de acción. Los otros tres contienen los campos página, vínculo, dispositivo, entorno e implementación que los eventos también incluyen. Si falta un grupo de campos, [!DNL Experience Platform] puede soltar esos campos o rechazar el evento.

No necesita el grupo de campos Adobe Analytics ExperienceEvent. La intención del usuario alcanza [!DNL Adobe Analytics] a través de una regla de procesamiento. Consulte [Paso 4](#report-suite).

Para ver el procedimiento, consulte [Crear un nuevo esquema](https://experienceleague.adobe.com/es/docs/experience-platform/xdm/ui/resources/schemas#create) y [Agregar grupos de campos](https://experienceleague.adobe.com/es/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups).

## Paso 3: crear el conjunto de datos {#dataset}

**Requerido para:** [!DNL Customer Journey Analytics].

El conjunto de datos almacena los eventos de su aplicación en [!DNL Experience Platform]. Cree un conjunto de datos con esta configuración:

| Configuración | Valor |
|---|---|
| Método de creación | **[!UICONTROL Crear conjunto de datos a partir del esquema]** |
| Esquema | El esquema de [Paso 2](#schema) |
| Nombre | Cualquier nombre, por ejemplo `AEM LLM Apps` |

Para ver el procedimiento, consulte [Crear un conjunto de datos](https://experienceleague.adobe.com/es/docs/experience-platform/catalog/datasets/user-guide#create).

## Paso 4: Preparar el grupo de informes {#report-suite}

**Requerido para:** [!DNL Adobe Analytics].

[!DNL Adobe Analytics] asigna automáticamente el nombre de página y los campos de vínculo, por lo que las páginas `mcp:<code identifier>` y los clics en el vínculo del widget no necesitan ninguna configuración adicional. La intención del usuario necesita un eVar y una regla de procesamiento.

### Crear o elegir un grupo de informes {#create-report-suite}

Cree un grupo de informes o elija uno existente. Observe el ID del grupo de informes completo, incluido el prefijo que agrega [!DNL Adobe Analytics]. Necesita el identificador en [Paso 5](#datastream).

Para ver el procedimiento, consulte [Crear un grupo de informes](https://experienceleague.adobe.com/es/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite).

### Habilitar la intención de usuario en eVar {#user-intent-evar}

En las variables de conversión del grupo de informes, habilite una eVar con la siguiente configuración:

| Configuración | Valor |
|---|---|
| eVar | EVAR3 |
| Nombre | `User Intent` |
| Tipo | **[!UICONTROL Cadena de texto]** |
| Asignación | **[!UICONTROL Más reciente (último)]** |
| Caduca después de | **[!UICONTROL Visita]** |
| Estado | **[!UICONTROL Habilitado]** |

Si el grupo de informes ya utiliza eVar3, elija otro eVar que no utilice y utilice ese eVar en la regla de procesamiento.

Para ver el procedimiento, consulte [Agregar o editar variables de conversión](https://experienceleague.adobe.com/es/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables).

### Creación de la regla de procesamiento {#processing-rule}

[!DNL Adobe Analytics] recibe la intención del usuario como una variable de datos de contexto, no como una eVar. Para saber por qué, vea [Asignación implícita](https://experienceleague.adobe.com/es/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping). Cree una regla de procesamiento que copie la intención del usuario en eVar:

- **Condición:** la variable de datos de contexto `a.x.conversation.prompt.raw.0.purpose` es igual a `intents`.
- **Acción:** sobrescribe el valor de **Intención de usuario (eVar3)** con la variable de datos de contexto `a.x.conversation.prompt.raw.0.text`.

Sin esta regla, la dimensión **Intención de usuario** permanece vacía. La regla solo se aplica a los datos recopilados después de guardarla. Las interacciones de widgets no tienen intención de usuario, por lo que la regla no las cambia.

Otros campos, como si la llamada falló o el nombre de la plataforma, también llegan como variables de datos de contexto. Para informar sobre ellos en [!DNL Adobe Analytics], cree más reglas de procesamiento del mismo modo.

Para ver el procedimiento, consulte [Información general sobre las reglas de procesamiento](https://experienceleague.adobe.com/es/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview) y [Casos de uso de reglas de procesamiento](https://experienceleague.adobe.com/es/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases).

## Paso 5: Crear la secuencia de datos {#datastream}

Se requiere **para:** [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics].

El conjunto de datos recibe los eventos de la aplicación y los reenvía a cada servicio que agregue.

### Creación de la secuencia de datos {#create-datastream}

Cree una secuencia de datos con esta configuración:

| Configuración | Valor |
|---|---|
| Nombre | Cualquier nombre, por ejemplo `AEM LLM Apps` |
| Esquema de asignación | El esquema de [Paso 2](#schema) |

Cree la secuencia de datos en el mismo entorno limitado que el esquema. No se puede cambiar la zona protegida de un conjunto de datos después de crearlo.

Para ver el procedimiento, vea [Crear un conjunto de datos](https://experienceleague.adobe.com/es/docs/experience-platform/datastreams/configure#create).

### Añadir los servicios {#add-services}

Añada un servicio para cada destino:

| Servicio | Configuración | Necesario para |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL Habilitado]**. En **[!UICONTROL ID del grupo de informes]**, ingrese el ID desde [Paso 4](#report-suite). | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL Habilitado]**. En **[!UICONTROL Conjunto de datos de evento]**, seleccione el conjunto de datos de [Paso 3](#dataset). Deje el resto de opciones borradas. | [!DNL Customer Journey Analytics] |

![Agregar servicio - Adobe Analytics habilitado con un ejemplo de ID de grupo de informes](/help/assets/guide-analytics/datastream-analytics-service.png)

![servicio Adobe Experience Platform: el conjunto de datos de evento seleccionado y las demás opciones borradas](/help/assets/guide-analytics/datastream-platform-service.png)

Para ver el procedimiento, consulte [Agregar servicios a un conjunto de datos](https://experienceleague.adobe.com/es/docs/experience-platform/datastreams/configure#add-services), [Configuración de Adobe Analytics](https://experienceleague.adobe.com/es/docs/experience-platform/datastreams/configure#analytics) y [Configuración de Adobe Experience Platform](https://experienceleague.adobe.com/es/docs/experience-platform/datastreams/configure#aep).

A continuación, copie el **[!UICONTROL ID de secuencia de datos]** de los detalles de la secuencia de datos. Lo necesita en [Paso 6](#enable-analytics).

![Detalles del flujo de datos: servicios de Adobe Analytics y Adobe Experience Platform habilitados, con el ejemplo ID del flujo de datos y el esquema de asignación en el panel lateral](/help/assets/guide-analytics/datastream-services.png)

## Paso 6: Habilitar Analytics e implementarlo en Fase {#enable-analytics}

Se requiere **para:** [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics].

>[!IMPORTANT]
>
>Después de guardar, la secuencia de datos es de solo lectura en la configuración de la aplicación. Póngase en contacto con el equipo de aplicaciones LLM para cambiarlo o para desactivar Analytics. Compruebe el ID de la secuencia de datos antes de guardar. Aún puede cambiar el grupo de informes y el conjunto de datos en el propio conjunto de datos.

1. Abra la aplicación y seleccione **[!UICONTROL Configuración]** en el panel de navegación izquierdo.
2. Seleccione la ficha **[!UICONTROL Analytics]**.
3. Activar **[!UICONTROL Habilitar Analytics]**.
4. En **[!UICONTROL ID de secuencia de datos]**, pegue el ID de [Paso 5](#datastream).
5. En **[!UICONTROL Requisitos]**, seleccione **[!UICONTROL Ver detalles]** y confirme que su aplicación cumple todos los requisitos.

   ![Configuración de la aplicación, ficha Analytics: habilitar Analytics activado, campo ID de secuencia de datos vacío y lista de requisitos expandida](/help/assets/guide-analytics/analytics-settings.png)

6. Seleccione **[!UICONTROL Guardar cambios]**.
7. [Implemente su aplicación](/help/guides/deploy-your-app.md) en Fase. La configuración se aplica en la siguiente implementación en cada entorno.

>[!NOTE]
>
>La secuencia de datos se aplica a toda la aplicación. Fase y Producción envían eventos al mismo conjunto de datos y los eventos no registran qué entorno los envió. Las llamadas de prueba en Fase aparecen en los mismos informes que el tráfico de producción.

## Paso 7: Crear la conexión y la vista de datos {#cja}

**Requerido para:** [!DNL Customer Journey Analytics].

### Creación de la conexión {#connection}

Cree una conexión y agregue el conjunto de datos desde [Paso 3](#dataset) con esta configuración:

| Configuración | Valor |
|---|---|
| Zona protegida | La zona protegida que contiene el conjunto de datos |
| Promedio del número de eventos diarios | Intervalo que coincide con el volumen esperado |
| Conjunto de datos | El conjunto de datos del [Paso 3](#dataset) |
| ID de persona | **[!UICONTROL Mapa de identidad]** |
| Utilizar espacio de nombres de identidad principal | Seleccionado |
| Marca de tiempo | **[!UICONTROL Marca de tiempo]** |
| Tipo de fuente de datos | **[!UICONTROL Otros]** |
| Descripción de fuente de datos | Cualquier descripción, por ejemplo `LLM Apps events` |
| Importar datos nuevos | **[!UICONTROL Importar todos los datos nuevos]** activados |
| Relleno del conjunto de datos | **[!UICONTROL Rellena todos los datos existentes]** activados, si el conjunto de datos ya contiene eventos que deseas analizar |

>[!IMPORTANT]
>
>Activar **[!UICONTROL Importar todos los datos nuevos]**. Esta configuración crea una conexión continua, por lo que los nuevos eventos estarán disponibles en Workspace automáticamente.

Con este ID de persona, [!DNL Customer Journey Analytics] usa la identidad principal de cada evento como persona y omite los eventos que no tienen identidad principal. Ver [Cómo los eventos identifican personas](#identities).

Para ver el procedimiento, consulte [Crear una conexión](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-connections/create-connection#create-connection), [Configuración del conjunto de datos de eventos](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings) y [Usar mapa de identidad como ID de persona](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-connections/create-connection#id-map).

### Creación de la vista de datos {#data-view}

Cree una vista de datos sobre la conexión con esta configuración:

| Configuración | Valor |
|---|---|
| Conexión | La conexión que acaba de crear |
| Nombre | Cualquier nombre, por ejemplo `LLM Apps` |
| Componentes | Las dimensiones y métricas sobre las que desea informar. Consulte la tabla siguiente. |

Añada estos campos de esquema como componentes:

| Informar sobre | Campo de esquema | Agregar como |
|---|---|---|
| Acción | `agenticExperience.tools.name` | Dimensión |
| Llamadas de acción fallidas | `agenticExperience.tools.failed` | Dimensión |
| Motivo del error | `agenticExperience.tools.errorReason` | Dimensión |
| Plataforma | `agenticExperience.agents.name` | Dimensión |
| Intento del usuario | `conversation.prompt.raw.text` | Dimensión |
| Conversación | `conversation.conversationID` | Dimensión |
| Etiqueta del widget | `web.webInteraction.name` | Dimensión |

Añada campos de texto, como la intención del usuario, como dimensiones. Un campo de texto agregado como métrica solo se puede contar y, al desglosarlo por sus valores, se muestra **No válido**.

Para ver el procedimiento, vea [Configurar una vista de datos](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-dataviews/create-dataview#configure) y [Componentes](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-dataviews/create-dataview#components).

## Paso 8: Verificar los datos e implementar en Producción {#verify}

Se requiere **para:** [!DNL Adobe Analytics] y [!DNL Customer Journey Analytics].

Llame a una acción en la aplicación Stage desde una plataforma LLM compatible y seleccione un vínculo en su widget. Ver [Prueba en ChatGPT](/help/guides/test-in-chatgpt.md) o [Prueba en Claude](/help/guides/test-in-claude.md). A continuación, compruebe cada servicio que ha configurado:

| Servicio | Dónde comprobar | Qué esperar |
|---|---|---|
| [!DNL Experience Platform] | **[!UICONTROL Actividad del conjunto de datos]** para el conjunto de datos del [Paso 3](#dataset) | Nuevos lotes ingeridos y sin lotes fallidos |
| [!DNL Adobe Analytics] | Proyecto de Workspace que usa el grupo de informes del [Paso 4](#report-suite), con una tabla de forma libre | **[!UICONTROL Página]** filas denominadas `mcp:<code identifier>`, **[!UICONTROL Vínculo personalizado]** filas para interacciones de widget y texto de intención en la dimensión **Intención de usuario** |
| [!DNL Customer Journey Analytics] | Un proyecto de Workspace que usa la vista de datos del [Paso 7](#cja) | **[!UICONTROL Eventos]** para tus llamadas de acción e interacciones de widget |

Cada servicio procesa los datos con cierto retraso, por lo que los nuevos eventos no aparecen inmediatamente.

Para generar los informes, consulte [Crear proyectos en Adobe Analytics](https://experienceleague.adobe.com/es/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects), [Descripción general de tablas de forma libre](https://experienceleague.adobe.com/es/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table) y [Crear proyectos en Customer Journey Analytics](https://experienceleague.adobe.com/es/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects).

Cuando los datos parezcan correctos, [implemente su aplicación](/help/guides/deploy-your-app.md) en Producción.

## Limitaciones {#limitations}

- **Las interacciones de widget se dividieron en muchas personas.** Cada llamada de acción obtiene un nuevo ECID, por lo que [!DNL Customer Journey Analytics] cuenta las interacciones de widgets de diferentes llamadas de acción como personas diferentes. Ver [Cómo los eventos identifican personas](#identities).
- **No hay ningún vínculo a su sitio web.** Cuando alguien sigue un vínculo de widget a su sitio web, la visita al sitio web no está vinculada a la conversación.
- **Informes de uso compartido de fase y producción.** Ambos entornos envían al mismo conjunto de datos y los eventos no registran qué entorno los envió.
- **Algunas plataformas no envían identidad.** Las llamadas de acción desde plataformas que no proporcionan un identificador de usuario, como [!DNL Claude], se omiten en [!DNL Customer Journey Analytics].
- **Un turno por llamada de acción.** Cada llamada de acción se registra como su propio turno de conversación, por lo que un mensaje que déclencheur varias acciones cuenta como varias vueltas.

## Resolución de problemas {#troubleshooting}

| Lo que ve | Qué comprobar |
|---|---|
| El conjunto de datos no recibe lotes | Ha implementado la aplicación después de guardar la configuración de Analytics en el entorno que está probando. El ID de la secuencia de datos es correcto. La secuencia de datos tiene el servicio **[!UICONTROL Adobe Experience Platform]** con su conjunto de datos. |
| Los lotes fallan | El esquema incluye los cinco grupos de campos de [Paso 2](#schema). |
| [!DNL Adobe Analytics] no tiene datos | La secuencia de datos tiene habilitado el servicio **[!UICONTROL Adobe Analytics]** con el ID de grupo de informes correcto. |
| **La página** muestra `mcp:<code identifier>`, pero **la intención del usuario** y los campos de acción están vacíos | El repositorio del controlador usa `@adobe/llm-apps-runtime` 1.2.0 o posterior, y se implementó después de la actualización. Consulte [Antes de comenzar](#before-you-begin). |
| **Intención de usuario** está vacío para algunas acciones | La regla de procesamiento de [Paso 4](#processing-rule) usa las variables de datos de contexto exactamente como se muestran, en minúsculas y con la eVar correcta. La acción tiene activada la opción **[!UICONTROL Recopilar intención del usuario]**. La regla solo se aplica a los datos recopilados después de guardarla. |
| [!DNL Customer Journey Analytics] muestra menos eventos que el conjunto de datos | **[!UICONTROL Importar todos los datos nuevos]** está activado. Los eventos sin identidad principal se omiten, por ejemplo las llamadas de acción desde plataformas que no proporcionan un ID de usuario. |
| No puede cambiar el ID de la secuencia de datos ni desactivar Analytics | Estos ajustes son de solo lectura en la Configuración de la aplicación después de la activación. Póngase en contacto con el equipo de aplicaciones LLM para obtener ayuda. Para enrutar los datos a otro sitio, cambie el grupo de informes o el conjunto de datos en el propio conjunto de datos. |

## Siguientes pasos {#whats-next}

- [Configuración de análisis para una acción](/help/guides/create-action.md#configure-analytics)
- [Implemente la aplicación](/help/guides/deploy-your-app.md)
