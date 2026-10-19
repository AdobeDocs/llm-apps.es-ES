---
title: Configuración de infraestructura
description: Cree una aplicación LLM de Adobe con una infraestructura administrada de Adobe o proporcione dos repositorios vacíos de GitHub para que se inicialice la plataforma.
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '1337'
ht-degree: 0%
---

# Configuración de infraestructura {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

La infraestructura es la base técnica de su aplicación: los repositorios que almacenan su código y los servicios que ejecutan sus controladores de acciones y entregan sus widgets a los usuarios.

Como se explica en la [descripción general](/help/overview/overview.md#key-concepts), un **controlador de acciones** realiza una tarea, como encontrar productos, mientras que un **widget** presenta el resultado como una experiencia interactiva en la conversación.

[!DNL LLM Apps] almacena el código fuente del controlador y el widget en dos repositorios de [!DNL GitHub] independientes:

- **Repositorio de acciones**: contiene los controladores del lado del servidor y sus pruebas.
- **Repositorio de widgets**: contiene el código y los estilos para los widgets interactivos.

Por ejemplo, un controlador de búsqueda de productos recupera los productos coincidentes y su widget los muestra como tarjetas de producto. Los repositorios almacenan ese código; Adobe I/O Runtime ejecuta los controladores implementados y Edge Delivery Services sirve los widgets.

El asistente de configuración prepara estos repositorios y conecta los servicios que necesita su aplicación. Recomendamos comenzar con **infraestructura administrada de Adobe**, la forma más rápida de empezar. Adobe crea y aloja los repositorios para los controladores de acciones y widgets. No es necesario que configure los repositorios usted mismo.

También puede proporcionar dos repositorios [!DNL GitHub] vacíos en su propia cuenta u organización. La plataforma los inicializa con los archivos de proyecto necesarios; no es necesario preparar previamente la acción o el código del widget.

**En esta guía:** Aprenda a elegir una opción de infraestructura y a crear su aplicación con el asistente de configuración. Después de la configuración, continúa [Creando acciones](/help/guides/creating-actions.md).

Al crear la aplicación, **no** inicia la generación de acciones. Inicie la generación de acciones por separado desde la página de la aplicación.

## Abra el asistente Crear aplicación

Abra [Aplicaciones LLM de Adobe](https://experience.adobe.com/llm-apps). En la página de bienvenida, seleccione **[!UICONTROL Comenzar]**. Si ya tiene una aplicación abierta, use el selector de aplicaciones y seleccione **[!UICONTROL Crear nueva aplicación]**.

El asistente de **[!UICONTROL Crear tu aplicación LLM]** comienza con **[!UICONTROL Introducción]**. Elija cómo configurar el entorno técnico.

![Cree su aplicación LLM; elija entre una infraestructura administrada por Adobe y conectar sus propios repositorios](/help/assets/guide-infrastructure/setup-choice.png)

| Opción de infraestructura | Seleccione en el asistente | Lo que significa |
|---|---|---|
| **Infraestructura administrada de Adobe** | **[!UICONTROL Permítanos manejarlo]** | Adobe crea y administra los repositorios y los servicios conectados para la aplicación. El acceso al código está disponible bajo petición. |
| **Sus propios repositorios** | **[!UICONTROL Lo configuraré yo mismo]** | Proporcione dos repositorios de GitHub vacíos y conceda acceso a través del asistente. La plataforma inicializa una para los controladores de acciones y otra para los widgets. |

Ambas opciones admiten la generación automática de acciones más adelante. Ninguna de las opciones requiere que escriba las acciones manualmente.

## Uso de la infraestructura gestionada por Adobe {#adobe-managed-infrastructure}

Utilice esta opción para permitir que Adobe configure y administre los repositorios y los servicios conectados de su aplicación. No necesita crear repositorios ni instalar aplicaciones de [!DNL GitHub] usted mismo.

1. Seleccione **[!UICONTROL Permítanos manejarlo]**.
2. Seleccione **[!UICONTROL Siguiente paso]**.
3. [Detalles de la aplicación](#app-details) completos.
4. Seleccione **[!UICONTROL Crear aplicación]**.

La página de la aplicación muestra **[!UICONTROL Configurando la aplicación]** mientras Adobe prepara los repositorios y conecta el sitio del widget. La interfaz de usuario indica que la instalación suele tardar entre 1 y 2 minutos; las acciones, las pruebas y la implementación siguen sin estar disponibles hasta que la instalación esté lista.

![Configuración de la aplicación — Adobe está preparando la infraestructura de la aplicación](/help/assets/guide-infrastructure/provisioning.png)

Si la instalación falla, lea el error y seleccione **[!UICONTROL Reintentar]**. Si la página no puede comprobar el progreso, la instalación continúa en segundo plano; seleccione **[!UICONTROL Verificar de nuevo]** para volver a conectarse.

Cuando finalice la instalación, continúa con [Crear acciones](/help/guides/creating-actions.md).

## Conectar sus propios repositorios {#your-own-repositories}

Seleccione **[!UICONTROL Lo configuraré yo mismo]** y luego **[!UICONTROL Siguiente paso]**. Esta ruta tiene cuatro pasos de asistente:

**Introducción a → configuración de la infraestructura de acción → a la configuración de la infraestructura de widget → detalles de la aplicación.**

Necesita acceso a los repositorios seleccionados y permiso para instalar o configurar las aplicaciones de [!DNL GitHub] necesarias. Para los repositorios de propiedad de la organización, es posible que un administrador tenga que aprobar el acceso.

Cree dos repositorios vacíos: uno para los controladores de acciones y otro para los widgets. No se inicialice con README, licencia o `.gitignore`. La plataforma agrega la estructura de proyecto necesaria al validar cada repositorio en el asistente.

Si ya tiene proyectos EDS y de acción compatibles, puede conectarlos en su lugar, pero no se requiere el código existente.

>[!NOTE]
>
>Para un repositorio vacío, la validación escribe los archivos de proyecto iniciales. Elija los repositorios destinados a esta aplicación antes de seleccionar **[!UICONTROL Validar repositorio]**.

### Configuración de la infraestructura de acción

El repositorio de acciones contiene los controladores del lado del servidor y las pruebas que se ejecutan cuando una plataforma LLM invoca una acción.

1. En **[!UICONTROL Origen de acciones]**, seleccione **[!UICONTROL GitHub]**. Las otras fuentes de código no están disponibles actualmente.
2. Respuesta **[!UICONTROL ¿Ya tiene un repositorio de GitHub creado?]**:
   - Seleccione **[!UICONTROL Sí, usaré un repositorio ya creado]** para conectar un proyecto existente compatible o un repositorio vacío.
   - Seleccione **[!UICONTROL Aún no tengo un repositorio]** y luego **[!UICONTROL Crear repositorios en GitHub]** para crear un repositorio de acciones vacío. Vuelva al asistente más tarde.
3. Conecte el repositorio a la aplicación LLM de Adobe [!DNL GitHub]:
   - Si no hay ninguna organización conectada, seleccione **[!UICONTROL Instalar en GitHub]**.
   - Si una organización ya está conectada, seleccione **[!UICONTROL Conectar repositorio]** y agregue el repositorio a esa instalación.
4. El [!DNL GitHub], elija **[!UICONTROL Seleccionar solo repositorios]** y conceder acceso solo a los repositorios que usa esta aplicación.
5. Vuelva al asistente. En **[!UICONTROL Seleccionar repositorio]**, elija la **[!UICONTROL Organización]** y el **[!UICONTROL Repositorio]**.
6. Seleccione **[!UICONTROL Validar repositorio]**. La validación inicializa un repositorio vacío con la plantilla de aplicaciones LLM de Adobe. Un repositorio existente debe contener un proyecto de acciones compatible.
7. Una vez completada la validación, seleccione **[!UICONTROL Siguiente paso]**.

![Configuración de la infraestructura de acción: elija GitHub y conecte un repositorio de acción](/help/assets/guide-infrastructure/action-repository.png)

Si falta una organización o repositorio, use **[!UICONTROL Conectar otra organización]** o **[!UICONTROL Conectar otro repositorio]** en su selector. Después de cambiar el acceso en [!DNL GitHub], seleccione **[!UICONTROL Actualizar organizaciones y repositorios]**.

### Configuración de la infraestructura de widgets

Los widgets son respuestas visuales opcionales adjuntas a las acciones, como tarjetas de productos o formularios de reserva. El asistente conecta un repositorio de [!DNL Edge Delivery Services] (EDS) para hospedarlos.

1. En **[!UICONTROL Origen del widget]**, seleccione **[!UICONTROL Seleccionar el repositorio del widget manualmente]**.
2. En **[!UICONTROL Origen del código]**, seleccione **[!UICONTROL GitHub]**.
3. Elija si ya tiene un repositorio. Si no es así, selecciona **[!UICONTROL Crear repositorios en GitHub]**, crea un repositorio de widgets vacío y vuelve al asistente.
4. Instale o configure la aplicación LLM de Adobe [!DNL GitHub] para este repositorio, como hizo para el repositorio de acciones.
5. Seleccione su **[!UICONTROL organización]** y su **[!UICONTROL repositorio]**. No seleccione el repositorio de acciones.
6. Seleccione **[!UICONTROL Validar repositorio]**. La validación inicializa un repositorio vacío con la plantilla de AEM. Un repositorio existente debe ser un proyecto EDS compatible.
7. Seleccione **[!UICONTROL Instalar sincronización de código de AEM]** y conceder acceso de sincronización de código de AEM al repositorio de widgets.
8. En la página de confirmación de la instalación, utilice el asistente de instalación para agregarse como administrador del sitio de EDS.
9. Vuelva a [!DNL LLM Apps] y seleccione **[!UICONTROL Validar la instalación de sincronización de código de AEM]**.
10. Cuando aparezca **[!UICONTROL Sincronización de código de AEM instalada]**, seleccione **[!UICONTROL Siguiente paso]**.

>[!NOTE]
>
>El asistente marca **[!UICONTROL Usar Cloud Manager Edge Delivery Services]** como **[!UICONTROL Próximamente]** y no permite la selección. Utilice la opción manual widget-repositorio.

Si la validación no detecta la sincronización de código de AEM, confirme que la sincronización de código de AEM tiene acceso al repositorio de widgets seleccionado y que se ha añadido como administrador del sitio. A continuación, valide de nuevo.

## Introducir detalles de la aplicación {#app-details}

Ambas opciones de infraestructura finalizan con **[!UICONTROL detalles de la aplicación]**.

![Detalles de la aplicación: escriba un nombre de aplicación, seleccione un programa de AEM y elija la región de datos de Analytics](/help/assets/guide-infrastructure/app-details.png)

1. Escriba **[!UICONTROL LLM App Name]**. Use un nombre que identifique la marca o experiencia, como *Frescopa Coffee*.
2. Seleccione el **[!UICONTROL programa de AEM]** al que pertenece la aplicación. Si sólo tiene acceso a un programa, el asistente lo selecciona automáticamente.
3. En **[!UICONTROL Región de datos de Analytics]**, elija **[!UICONTROL Estados Unidos (EE.UU.)]** o **[!UICONTROL Europa (UE)]**.
4. Seleccione **[!UICONTROL Crear aplicación]**.

>[!IMPORTANT]
>
>La región de datos de AEM program and analytics no se puede cambiar una vez creada la aplicación. La región de análisis determina dónde se almacenan los datos de análisis de la aplicación.

Si no hay ningún programa de AEM disponible, solicite a su administrador acceso. Si la carga de los programas falla, seleccione **[!UICONTROL Reintentar cargar los programas]**.

El asistente abre la página de la aplicación después de crearla. Con la infraestructura gestionada por Adobe, espere a que finalice la configuración de la infraestructura antes de continuar.

## Siguiente: crear las acciones

La aplicación ahora tiene su infraestructura, pero aún no se han generado acciones. Continúe con [Crear acciones](/help/guides/creating-actions.md) para analizar un sitio web o elegir la creación manual de acciones.

Para ver la relación entre los metadatos de acciones, los controladores, los widgets y el servidor MCP implementado, consulte [Cómo se conecta una aplicación](/help/guides/app-architecture.md).
