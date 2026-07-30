---
title: Información general sobre las aplicaciones LLM de Adobe
description: Aprenda qué es Adobe LLM Apps, cómo funciona y lo que necesita para empezar.
source-git-commit: 1d677c4e21963d1b126abb6287fccedfc1933c1a
workflow-type: tm+mt
source-wordcount: '938'
ht-degree: 2%

---


# Aplicaciones LLM de Adobe: Información general {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

## ¿Qué es [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] permite que su marca ofrezca acciones útiles (como la detección de productos, comprobaciones de disponibilidad o reservas de servicios) dentro de asistentes de IA como [!DNL ChatGPT].

[!DNL LLM Apps] está disponible en [experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/).

## Qué puede hacer con [!DNL LLM Apps]

- **Crear acciones LLM de propiedad de la marca**: defina los flujos comerciales específicos que desea activar dentro de los asistentes de IA (por ejemplo, *Programar una prueba*, *Comparar productos*, *Reservar un servicio*).
- **Crear widgets LLM interactivos**: cree componentes de la interfaz de usuario visual (tarjetas de producto, formularios de reserva, localizadores de tiendas) administrados como componentes de AEM en su repositorio [!DNL GitHub].
- **Mantener el control centralizado de la marca**: los autores y desarrolladores conservan el control total sobre todo el contenido, las copias y los elementos visuales expuestos dentro de la plataforma LLM, con aprobaciones administradas a través de AEM.
- **Implementar en ensayo y producción**: Una canalización de implementación controlada le permite probar la experiencia en un entorno de ensayo antes de pasar a producción.
- **Controlar la visibilidad en el nivel de acción**: después de la implementación, las acciones individuales se pueden activar y desactivar sin volver a implementar toda la aplicación.
- **Mida lo que impulsa las decisiones**: Recuentos de déclencheur de acción superficial, tasas de éxito, tasas de abandono, indicadores de usuario principales y puntuaciones de visibilidad de Analytics integrado (con tecnología de Adobe Customer Journey Analytics).

## Por qué [!DNL LLM Apps] importa

Las interacciones de LLM son fundamentalmente diferentes de la búsqueda tradicional. La sesión LLM promedio dura cuatro veces más que una sesión de búsqueda tradicional. Más del 40% de los consumidores dependen de las herramientas de IA para tomar decisiones de compra complejas. Sin [!DNL LLM Apps], podría ganar la mención pero perder al cliente. [!DNL LLM Apps] garantiza que su marca no solo sea visible sino que se pueda procesar en el momento exacto en que un usuario esté listo para decidir.

## Conceptos clave {#key-concepts}

### Aplicación LLM

El asistente de marca con el que los usuarios interactúan dentro de [!DNL ChatGPT] u otras plataformas LLM. Agrupa todas las acciones e implementa como una sola unidad.

### Acción {#actions}

Una funcionalidad que ofrece tu aplicación, como *Buscar un distribuidor* o *Examinar productos*. La plataforma LLM invoca una acción cuando una solicitud coincide con su descripción. Los metadatos de la acción se administran en [!DNL LLM Apps], mientras que su controlador es el código del repositorio [!DNL GitHub].

### Controlador de acciones

Función del lado del servidor que se ejecuta cuando se invoca una acción. Puede validar la entrada, llamar a las API y devolver texto más datos estructurados.

### Widget {#widgets-eds}

La respuesta visual que se muestra con la respuesta de LLM, como una tarjeta, un carrusel o una tabla. Los widgets generados son bloques de un repositorio [!DNL Edge Delivery Services] (EDS) de su propiedad.

### servidor MCP

El extremo expuesto después de la implementación. Una plataforma LLM admitida se conecta a este extremo para descubrir e invocar sus acciones.

## Funcionamiento

El diagrama siguiente muestra cómo encajan las piezas: desde definir una aplicación en la IU hasta ver los resultados en directo en la plataforma LLM.

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Requisitos {#requirements}

Complete todos los requisitos siguientes antes de crear una aplicación.

### Adobe Developer Console

Su organización de IMS de Adobe debe tener acceso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/). Necesita el rol de **Desarrollador** o **Administrador del sistema**.

Para comprobar tu acceso, abre [Adobe Developer Console](https://developer.adobe.com/console). La pantalla Inicio rápido confirma que tiene el acceso requerido.

![Adobe Developer Console: pantalla de inicio rápido que confirma el acceso de desarrollador](/help/assets/overview/dev-console-access-granted.png)

Si ve **Acceso restringido**, póngase en contacto con el administrador de la organización IMS y solicite la función Desarrollador.

![Adobe Developer Console — Mensaje de acceso restringido](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

Necesita una cuenta de [!DNL GitHub] que **pueda** hacer lo siguiente. Esta es una comprobación de permisos; aún no ha instalado nada:

- Cree dos repositorios en la cuenta u organización que será la propietaria de la aplicación.
- Instale [!DNL GitHub] aplicaciones más adelante en el proceso de instalación o tenga un administrador de organización que pueda aprobarlas.

Para comprobar el acceso al repositorio, abra [github.com/new](https://github.com/new) y confirme que la cuenta u organización deseada aparece en **Propietario**.

![GitHub — seleccione un propietario de repositorio](/help/assets/overview/github-repo-owner-dropdown.png)

Para los repositorios de propiedad de la organización, es posible que un administrador de la organización tenga que aprobar las aplicaciones de [!DNL GitHub].

>[!NOTE]
>
>Es una comprobación de permisos, no un paso de configuración. Aún no instale ninguna aplicación de [!DNL GitHub]: [Cree su primera aplicación automáticamente](/help/guides/create-app.md) le guiará por la instalación de cada una, con ámbitos de los repositorios exactos que cree, en el punto en el que sea necesario.

### Sitio web

Necesita un sitio web HTTPS público que represente los productos, servicios o tareas que la aplicación debe admitir. La plataforma analiza este sitio web para proponer acciones y crear datos de muestra representativos.

No utilice un sitio web que exponga información confidencial o de acceso controlado.

### [!DNL ChatGPT] o [!DNL Claude] para pruebas

Para completar el tutorial de introducción, use un plan [!DNL ChatGPT] compatible con el modo de desarrollador habilitado o un plan [!DNL Claude] compatible con conectores personalizados habilitados. Los administradores de Workspace u organización pueden restringir el acceso. Ver [Prueba en ChatGPT](/help/guides/test-in-chatgpt.md#plan-requirements) o [Prueba en Claude](/help/guides/test-in-claude.md#plan-requirements).

## Elige tu recorrido {#choose-your-journey}

### &#x200B;1. Cree e inicie su primera aplicación

Empiece con [Cree e inicie su primera aplicación](/help/guides/create-app.md). Este recorrido comienza con dos repositorios vacíos y termina con una aplicación lista para la producción probada como complemento en una plataforma LLM compatible como [!DNL ChatGPT].

### &#x200B;2. Personalizar la aplicación generada

Elija este recorrido cuando la plataforma cree la aplicación automáticamente y desee reemplazar el comportamiento de ejemplo:

1. [Personalice los controladores generados](/help/guides/customize-handler.md) para conectar sus API y definir los datos devueltos por cada acción.
2. [Personalice los widgets generados](/help/guides/widgets.md) para usar esos datos y aplicar sus interacciones y diseño.

### &#x200B;3. Añadir una nueva acción desde cero

Elija [Agregar una nueva acción desde cero](/help/guides/create-action.md) para definir nuevos metadatos, escribir el controlador, conectar un widget, probar e implementar la acción.

### &#x200B;4. Conectar un proyecto EDS existente

Elija [Conectar un proyecto EDS existente](/help/guides/bring-your-own-eds.md) cuando ya tenga un sitio EDS o no haya creado la aplicación automáticamente.

Cada recorrido usa el paso compartido de [implementación](/help/guides/deploy-your-app.md), luego [prueba del complemento ChatGPT](/help/guides/test-in-chatgpt.md) o [prueba del conector Claude](/help/guides/test-in-claude.md).

