---
title: Integración de Beta para aplicaciones LLM de Adobe
description: Introducción a Adobe LLM Aplicaciones como participante del programa de Beta.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '1551'
ht-degree: 0%

---


# Incorporación de Beta {#beta-onboarding}

>[!IMPORTANT]
>
>**Descargo de responsabilidad:** Esta es una versión beta de [!DNL LLM Apps]. Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final de la aplicación o del producto.

>[!NOTE]
>
>Antes de empezar, asegúrese de que se cumplan todos los [requisitos previos](/help/beta-onboarding/prerequisites.md).

Como participante del programa de Beta, recibirá un correo electrónico con dos archivos zip y una referencia de configuración de la aplicación. Siga los pasos a continuación para activar la aplicación.

## Antes de empezar

Antes de sumergirse en los pasos, familiarícese con los conceptos clave utilizados en esta guía. Le ahorrará tiempo y ayudará a que todo encaje en su lugar.

**Aplicación LLM**: su asistente de marca con el que los usuarios interactúan dentro de [!DNL ChatGPT] u otras plataformas LLM.

**Acción**: una funcionalidad que ofrece tu aplicación. Por ejemplo, &quot;Buscar un distribuidor&quot; o &quot;Examinar productos&quot;. El LLM invoca cada acción cuando el usuario hace una pregunta relevante.

**Controlador de acciones**: el código que se ejecuta cuando se invoca una acción. Puede llamar a sus API, recuperar datos activos o devolver datos estáticos. Los controladores de muestra proporcionados por Adobe devuelven datos codificados para que pueda verificar la configuración de principio a fin antes de conectar su backend real.

**Widget** (la respuesta visual mostrada al usuario): una tarjeta, un carrusel, una tabla o cualquier interfaz de usuario personalizada representada junto a la respuesta de texto de LLM.

**Referencia de configuración de la aplicación**: un archivo que proporciona Adobe y que indica exactamente lo que debe especificarse para cada acción al configurar la aplicación.


## Paso 1: Insertar los archivos proporcionados en [!DNL GitHub]

Adobe proporciona dos archivos zip por correo electrónico:

- **Código de aplicación** (`<project-name>.zip`): los controladores de acciones que se ejecutan en [!DNL Adobe I/O Runtime] y alimentan la lógica de la aplicación. Los implementará tal cual para que la aplicación funcione de extremo a extremo y los actualizará más tarde para conectar su servidor real.
- **Proyecto EDS** (`<project-name>-eds.zip`): el código de front-end para los widgets. Adobe los ha creado previamente para usted; esta es su base de código para poseer, personalizar y diseñar para que coincida con su marca.

Cree **dos nuevos repositorios vacíos** en [!DNL GitHub] (uno por archivo), luego descomprima cada archivo y envíelo. Se recomienda nombrar cada repositorio después del archivo zip correspondiente: `<project-name>` para el código de la aplicación y `<project-name>-eds` para el proyecto EDS.

`<your-github-org>` hace referencia a su nombre de usuario personal [!DNL GitHub] o a una organización [!DNL GitHub], independientemente de la cuenta que sea propietaria de los repositorios.

**Repositorio de código de aplicación**: descomprima el archivo, inicialice un repositorio de Git local y envíelo a [!DNL GitHub]:

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**Repositorio EDS** — repita los mismos pasos para el archivo EDS, señalando al segundo repositorio:

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## Paso 2: Crear una aplicación LLM

Vaya a [experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/) y haga clic en **[!UICONTROL Crear aplicación LLM]**.

![Página de aplicaciones — aún no se han creado aplicaciones](/help/assets/guide-create-app/first-load.png)

Rellene los **[!UICONTROL detalles de la aplicación]** con los valores de la sección **[!UICONTROL detalles de la aplicación]** de la referencia de configuración de la aplicación:

- **[!UICONTROL Nombre de aplicación LLM]**
- **[!UICONTROL Descripción de la aplicación LLM]**
- **[!UICONTROL Su sitio web]**

![Cuadro de diálogo Crear aplicación](/help/assets/guide-create-app/app-details-1.png)

En **[!UICONTROL Región de datos de Analytics]**, seleccione la región donde se almacenarán los datos de Analytics. Este(a) **no se puede cambiar** después de crear la aplicación.

>[!IMPORTANT]
>
>La región de datos de análisis no se puede cambiar una vez creada la aplicación.

![Menú desplegable de región de datos de Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

En **Repositorio**, seleccione la organización [!DNL GitHub] y el **repositorio de código de la aplicación** que insertó anteriormente.

>[!NOTE]
>
>Si es la primera vez que configura una aplicación, la organización [!DNL GitHub] aún no aparecerá en la lista. Haga clic en **[!UICONTROL Conectar otra organización de GitHub]** para vincular su organización y conceder acceso al repositorio.

![Cuadro de diálogo Crear aplicación — repositorio vinculado](/help/assets/guide-create-app/app-details-repo-linked.png)

Deje **[!UICONTROL Sugerir acciones automáticamente basándose en su sitio web]** sin marcar; configurará las acciones manualmente.

Acepte los **[!UICONTROL Términos de Adobe Developer]** y luego haga clic en **[!UICONTROL Crear aplicación]**.

![Creando aplicación — cargando pantalla](/help/assets/guide-create-app/app-loading.png)

![Página de detalles de la aplicación](/help/assets/guide-create-app/app-detail-top.png)


## Paso 3: Ponga en marcha sus widgets

En este paso configuró el proyecto EDS proporcionado por Adobe y lo publicó a través de [!DNL DA.live]: la capa de creación y CDN de Adobe. Cada documento publicado se convierte en el widget que se muestra al usuario cuando se invoca una acción.

### Paso 3.1: Conectar el repositorio EDS a [!DNL DA.live]

1. Vaya a [github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync). Si la aplicación aún no está instalada, haga clic en **[!UICONTROL Instalar]**. Si ya está instalado, haga clic en **[!UICONTROL Configurar]** y agregue `<your-eds-repo>` a la lista de repositorios a los que puede acceder.
2. Después de la instalación, aterriza en una página de confirmación **[!DNL AEM Code Sync]registered**. En **Qué sigue → Crear tu contenido**, haz clic en el vínculo [!DNL DA.live].
3. En la pantalla **Contenido de demostración**, seleccione **Ninguno** y haga clic en **Hacer algo maravilloso**.
4. Se le dirigirá a la vista de autor de [!DNL DA.live] de su sitio.

### Paso 3.2: crear un documento [!DNL DA.live] para cada acción

En [!DNL DA.live] debe crear **un documento por acción**. Una vez publicado, cada documento se convierte en el widget que se muestra al usuario cuando se invoca esa acción.

Para cada acción:

1. En [!DNL DA.live], cree un nuevo documento en la raíz del sitio y asígnele el nombre que se especifica en la referencia de configuración de la aplicación (consulte la sección **[!DNL DA.live]documentos**).
2. En el documento, use la barra lateral izquierda y haga clic en **[!UICONTROL Bloque]** para insertar un bloque nuevo.
3. Establezca el encabezado del bloque en el nombre del bloque especificado en la referencia de configuración de la aplicación (consulte la sección **[!DNL DA.live]documentos**).
4. Publique el documento con el botón **[!UICONTROL Publicar]** (el icono del avión de papel en la barra de herramientas superior).

Una vez publicado, se puede acceder a cada documento en `https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`. Esta URL es la que introducirá en el campo **[!UICONTROL URL del widget]** al configurar cada acción en el paso 4.


### Paso 3.3: Configuración de los encabezados CORS para el sitio EDS

Para permitir que las plataformas LLM carguen sus widgets de origen cruzado, debe agregar un encabezado `Access-Control-Allow-Origin` a su sitio EDS.

Vaya al **Editor de encabezados HTTP** en [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html).

1. Escriba su **Organización** (`<your-github-org>`) y **Sitio** (su nombre de repositorio EDS) y haga clic en **[!UICONTROL Buscar]**. Se le pedirá que autentique y autorice el acceso a su sitio.
2. En la ruta `/**`, haga clic en **[!UICONTROL Agregar encabezado]**.
3. Establezca el nombre del encabezado en `Access-Control-Allow-Origin` y el valor en `*`.
4. Haga clic en **[!UICONTROL Guardar]**.

Para obtener documentación completa sobre los encabezados HTTP personalizados en [!DNL AEM Edge Delivery Services], consulte [aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers).

Después de guardar los encabezados, almacene en déclencheur una sincronización de código para propagar los cambios a todos los archivos:

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## Paso 4: Agregar acciones

En la interfaz de usuario de [LLM Apps](https://experience.adobe.com/llm-apps/), abra la aplicación y vaya a **[!UICONTROL Actions]** en la barra lateral izquierda. Haga clic en **+** para crear una nueva acción. Repita el proceso con cada acción descrita en la referencia de configuración de la aplicación (consulte las secciones **Acción 1**, **Acción 2**, **Acción 3**).

![Página de acciones — aún no hay acciones](/help/assets/guide-create-action/actions-empty.png)

### Pestaña Acción

- **Nombre de acción** y **Descripción** — utilizados por las plataformas LLM para decidir cuándo invocar la acción. Use los valores exactos de la sección **Ficha de acciones** en la referencia de configuración de la aplicación.
- **Parámetros de entrada**: nombre, tipo y descripción para cada parámetro. Use los valores de la sección **Ficha de acciones** en la referencia de configuración de la aplicación.

![Crear acción — información básica](/help/assets/guide-create-action/action-basic-info.png)

### Pestaña Metadatos del widget

- **Tipo** — seleccione **[!UICONTROL EDS]**.

Expanda **[!UICONTROL Configuración de CSP]** y rellene:

- **[!UICONTROL CSP — Conectar dominios]** — usa los valores de la sección **ficha Metadatos de widget** en la referencia de configuración de la aplicación.
- **[!UICONTROL CSP — Dominios de recursos]** — utiliza los valores de la sección **ficha Metadatos de widget** en la referencia de configuración de la aplicación.

![Crear acción — metadatos de widget](/help/assets/guide-create-action/widget-metadata.png)

![Crear acción — permisos y CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### Pestaña Widget Builder

En **[!UICONTROL Origen del widget]**, seleccione **[!UICONTROL Usar un widget existente]** y rellene:

- **[!UICONTROL URL de script]**: usa el valor de la sección **Pestaña de metadatos de widget** en la referencia de configuración de la aplicación.
- **[!UICONTROL URL del widget]**: use el valor de la sección **Pestaña de metadatos del widget** en la referencia de configuración de la aplicación.

Haga clic en **[!UICONTROL Crear acción]**. La acción aparece como una tarjeta en la página Acciones con un distintivo **[!UICONTROL EDS]** y un recuento de parámetros.

![Página de acciones — acción creada](/help/assets/guide-create-action/actions-with-action.png)


## Paso 5: Implementación

Una vez configuradas todas las acciones, ve a la página Detalles de la aplicación y haz clic en **[!UICONTROL Implementar]** en la esquina superior derecha.

![Detalles de la aplicación — lista para implementar](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Seleccione el entorno de destino y haga clic en **[!UICONTROL Implementar]**. La canalización sigue cuatro pasos: preparar las credenciales, iniciar la implementación, crear la aplicación desde el repositorio y publicarla en [!DNL Adobe I/O Runtime].

![Implementar canalización en ejecución](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

Una vez completada, desplácese hasta la sección **[!UICONTROL Probar la aplicación]** de la página Detalles de la aplicación y copie la **[!UICONTROL URL del servidor MCP]**; necesitará que registre su aplicación en [!DNL ChatGPT].

![Implementación correcta](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![Probar la aplicación — direcciones URL implementadas](/help/assets/guide-deploy/test-app-deployed.png)


## Paso 6: Agregar la aplicación a [!DNL ChatGPT]

Para agregar aplicaciones personalizadas a [!DNL ChatGPT] se requiere una suscripción a **Pro**, **Business** o **Enterprise**. Los planes Gratis y Plus no admiten aplicaciones MCP personalizadas.

1. En [!DNL ChatGPT], haz clic en el avatar de tu perfil y ve a **[!UICONTROL Configuración]**.

   ![ChatGPT — Menú de configuración](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. Seleccione **[!UICONTROL Aplicaciones]** en la barra lateral, haga clic en **[!UICONTROL Configuración avanzada]** y habilite **[!UICONTROL Modo de desarrollador]**.

   ![ChatGPT — Modo de desarrollador habilitado](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. Vaya a **[!UICONTROL Configuración] → [!UICONTROL Aplicaciones]** y haga clic en **[!UICONTROL Crear aplicación]**.

   ![ChatGPT — cuadro de diálogo Crear aplicación](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. Pegue la **[!UICONTROL URL del servidor MCP]** copiada de [!DNL LLM Apps], establezca **[!UICONTROL Autenticación]** en *Sin autenticación*, marque la casilla de verificación de confirmación y haga clic en **Crear**.

Su aplicación aparece en **[!UICONTROL Aplicaciones habilitadas]** con un distintivo de **[!UICONTROL DEV]**.

![ChatGPT — aplicación habilitada](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

Inicie una nueva conversación, adjunte la aplicación con el botón **+** o escribiendo **@** seguido del nombre de la aplicación y haga una pregunta que coincida con una de las acciones configuradas.

![ChatGPT — seleccionar aplicación del menú](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — resultado de la acción](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## Siguientes pasos

La aplicación de ejemplo que ha implementado utiliza datos codificados. Para convertirla en una experiencia lista para la producción:

- **Conecte sus API**: actualice los controladores de acciones en su repositorio de código de aplicación para llamar a sus API, bases de datos o servicios reales. Cada controlador vive en `actions/<action-name>/index.js`.
- **Revise y perfeccione sus widgets**: abra su proyecto EDS, ajuste los estilos de bloque y el diseño para que coincidan con su marca y verifique que el widget se procese correctamente con los datos activos.
- **Volver a implementar**: una vez que se actualicen los controladores y widgets, inserta los cambios en [!DNL GitHub] y haz clic en **[!UICONTROL Implementar]** en la interfaz de usuario de [!DNL LLM Apps] para publicar la nueva versión.
- **Enviar para publicación**: cuando esté satisfecho con la experiencia, envíe su aplicación para que se revise a través del complemento [!DNL ChatGPT] o del proceso de publicación del conector. Adobe no controla este proceso. Consulte la documentación de la plataforma LLM para conocer los requisitos y plazos de envío.
