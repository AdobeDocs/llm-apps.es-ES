---
title: Crear una aplicación
description: Aprenda a crear la primera aplicación LLM y vincularla al repositorio de GitHub.
source-git-commit: 344c5457eb79a19b1dae823732a1cd9866dcd9dc
workflow-type: tm+mt
source-wordcount: '720'
ht-degree: 0%

---


# Crear una aplicación

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Antes de empezar, asegúrese de que se cumplan todos los [requisitos previos](/help/overview/overview.md#prerequisites).

Esta guía lo acompañará en la creación de su primer(a) [!DNL Adobe LLM Apps], desde el estado vacío hasta un proyecto completamente configurado y vinculado a su repositorio [!DNL GitHub].

## Abrir [!DNL LLM Apps]

Vaya a [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps). Si todavía no se ha creado ninguna aplicación, verá la página de primera carga con un mensaje para crear la primera aplicación.

![Página de aplicaciones — aún no se han creado aplicaciones](/help/assets/guide-create-app/first-load.png)

La barra lateral izquierda le permite navegar entre **[!UICONTROL Aplicaciones]** y **[!UICONTROL Acciones]**. Haga clic en **[!UICONTROL Crear aplicación]** para comenzar.

## Rellene los detalles de la aplicación

El cuadro de diálogo Crear aplicación se abre a pantalla completa.

![Cuadro de diálogo Crear aplicación](/help/assets/guide-create-app/app-details-1.png)

Escriba lo siguiente:

- **[!UICONTROL Nombre de aplicación LLM]** (obligatorio): el nombre para mostrar de la aplicación. Solo se permiten letras, números y espacios.
- **[!UICONTROL Descripción de la aplicación LLM]**: una breve descripción de lo que hace la aplicación. Por ejemplo, *Ayuda a los usuarios a descubrir productos y reservar servicios a través de una plataforma LLM*.
- **[!UICONTROL Su sitio web]** (obligatorio): la dirección URL del sitio web de su marca. [!DNL LLM Apps] usa esto para crear acciones preconfiguradas automáticamente.

## Seleccione una región de datos de Analytics

Elija la región donde se almacenarán los datos de análisis de esta aplicación.

>[!IMPORTANT]
>
>La región de datos de análisis no se puede cambiar una vez creada la aplicación.

![Menú desplegable de región de datos de Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

El menú desplegable **Región de Analytics** tiene el valor predeterminado **Estados Unidos (EE.UU.)**. Las opciones disponibles son **Estados Unidos (EE.UU.)** y **Europa (UE)**. Seleccione la región que mejor se ajuste a los requisitos de residencia de datos antes de continuar.

## Vincular un repositorio [!DNL GitHub]

Debajo de los detalles de la aplicación, puede vincular un repositorio [!DNL GitHub]. Este repositorio es donde se encuentra el código del controlador de acciones: JavaScript funciona en una carpeta `actions/` que se ejecuta en [!DNL Adobe I/O Runtime] cuando la plataforma LLM invoca su aplicación.

Si es la primera vez que lo hace, no aparecerá ningún repositorio en la lista. Debe instalar la aplicación **[!DNL Adobe LLM Apps Link]** [!DNL GitHub] en su organización:

1. Haga clic en **Administrar repositorios en Github** en la parte inferior del cuadro de diálogo.
2. Se abrirá la página de la aplicación [!DNL Adobe LLM Apps Link] [!DNL GitHub] en una nueva pestaña.

   ![Vínculo de aplicaciones LLM de Adobe: página de instalación de la aplicación GitHub](/help/assets/guide-create-app/github-app-install.png)

3. Haga clic en **[!UICONTROL Instalar]** y seleccione su organización [!DNL GitHub].
4. En **[!UICONTROL Acceso al repositorio]**, elija **Seleccionar solo repositorios** y seleccione el repositorio que alojará el código de la aplicación.

   ![Vínculo de aplicaciones LLM de Adobe: acceso al repositorio](/help/assets/guide-create-app/github-repo-access.png)

5. Haga clic en **[!UICONTROL Guardar]**. Vuelva al cuadro de diálogo Crear aplicación: el repositorio aparecerá ahora en la lista desplegable **Seleccionar repositorio**.
6. Elija el repositorio que desee utilizar.

![Cuadro de diálogo Crear aplicación — repositorio vinculado](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>Puede omitir vincular un repositorio durante la creación de la aplicación y hacerlo más tarde desde la configuración de la aplicación. Sin embargo, no puede realizar la implementación hasta que haya un repositorio vinculado.

## Crear la aplicación

Haga clic en **[!UICONTROL Crear aplicación]**. Aparece una pantalla de carga mientras se crea el proyecto en Developer Console.

![Creando aplicación — cargando pantalla](/help/assets/guide-create-app/app-loading.png)

Una vez finalizado, se le redirigirá a la página **Detalles de la aplicación**.

## La página Detalles de la aplicación

La página Detalles de la aplicación es el sistema centralizado para administrar la aplicación.

![Página de detalles de la aplicación — secciones principales](/help/assets/guide-create-app/app-detail-top.png)

### Titular de aplicación

![Titular de la aplicación](/help/assets/guide-create-app/app-banner.png)

El banner de color de la parte superior muestra la aplicación seleccionada actualmente, incluido el avatar de la aplicación, el nombre, la descripción y un menú desplegable para cambiar entre aplicaciones. El titular permanece fijo en la parte superior cuando se desplaza.

### Título de página y acciones

![Titular de la aplicación](/help/assets/guide-create-app/page-title.png)

Debajo del banner puede ver el nombre de la aplicación como un encabezado, con los siguientes botones de acción:

- **...** (más acciones) — crear una aplicación nueva o eliminar la actual.
- **[!UICONTROL Configuración]**: configure el repositorio vinculado y otras opciones.
- **[!UICONTROL Implementar]**: implemente su aplicación en [!DNL Adobe I/O Runtime] (deshabilitada hasta que haya un repositorio vinculado).

### Tarjeta de información de aplicación

![Tarjeta de información de aplicación](/help/assets/guide-create-app/app-info-card.png)

Esta tarjeta resume los metadatos clave de su aplicación: nombre, descripción, distintivo de estado (**No implementado** o **Implementado**), ID de aplicación y fecha de creación. También muestra los dos repositorios vinculados:

- **Repositorio de controladores**: donde reside el código del controlador de acciones (JavaScript funciona en [!DNL Adobe I/O Runtime]).
- **repositorio EDS**: donde se encuentra la interfaz de usuario del widget (bloques y estilos servidos por [!DNL Edge Delivery Services]).

### Acciones, Probar la aplicación e Historial de implementación

![Página de detalles de la aplicación — secciones inferiores](/help/assets/guide-create-app/app-detail-bottom.png)

Debajo de la tarjeta de información encontrará tres secciones:

- **[!UICONTROL Acciones]**: enumera los controladores de acciones definidos para su aplicación. Haga clic en **Ir a acciones** para ir a la página de acciones.
- **[!UICONTROL Probar la aplicación]**: después de la implementación, muestra las direcciones URL del servidor MCP para los entornos de ensayo y producción.
- **Historial de implementación**: rastrea cada implementación en entornos con estado y fecha.

## Pasos siguientes

- [Guía: crear una acción](/help/guides/create-action.md) — definir una acción con la configuración de los metadatos y widgets.

