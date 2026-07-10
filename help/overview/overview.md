---
title: Información general sobre las aplicaciones LLM de Adobe
description: Aprenda qué es Adobe LLM Apps, cómo funciona y lo que necesita para empezar.
source-git-commit: 344c5457eb79a19b1dae823732a1cd9866dcd9dc
workflow-type: tm+mt
source-wordcount: '831'
ht-degree: 2%

---


# Aplicaciones LLM de Adobe: Información general {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

## ¿Qué es [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] permite que su marca exponga acciones clave, como la detección de productos, las comprobaciones de disponibilidad o las reservas de servicios, directamente dentro de asistentes de IA como [!DNL ChatGPT] o Claude. En lugar de ser mencionada pasivamente en respuestas generadas por IA, su marca puede guiar a los clientes a través de flujos comerciales reales sin que abandonen la conversación.

[!DNL LLM Apps] está disponible en [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).

## Qué puede hacer con [!DNL LLM Apps]

- **Crear acciones LLM de propiedad de la marca**: defina los flujos comerciales específicos que desea activar dentro de los asistentes de IA (por ejemplo, *Programar una prueba*, *Comparar productos*, *Reservar un servicio*).
- **Crear widgets LLM interactivos**: cree componentes de la interfaz de usuario visual (tarjetas de producto, formularios de reserva, localizadores de tiendas) administrados como componentes de AEM en su repositorio [!DNL GitHub].
- **Mantener el control centralizado de la marca**: los autores y desarrolladores conservan el control total sobre todo el contenido, las copias y los elementos visuales expuestos dentro de la plataforma LLM, con aprobaciones administradas a través de AEM.
- **Implementar en ensayo y producción**: Una canalización de implementación controlada le permite probar la experiencia en un entorno de ensayo antes de pasar a producción.
- **Controlar la visibilidad en el nivel de acción**: después de la implementación, las acciones individuales se pueden activar y desactivar sin volver a implementar toda la aplicación.
- **Mida lo que impulsa las decisiones**: Recuentos de déclencheur de acción superficial, tasas de éxito, tasas de abandono, indicadores de usuario principales y puntuaciones de visibilidad de Analytics integrado (con tecnología de Adobe Customer Journey Analytics).

## Por qué [!DNL LLM Apps] importa

Las interacciones de LLM son fundamentalmente diferentes de la búsqueda tradicional. La sesión promedio [!DNL ChatGPT] dura cuatro veces más que una sesión de búsqueda tradicional. Más del 40% de los consumidores dependen de las herramientas de IA para tomar decisiones de compra complejas. Sin [!DNL LLM Apps], podría ganar la mención pero perder al cliente. [!DNL LLM Apps] garantiza que su marca no solo sea visible sino que se pueda procesar en el momento exacto en que un usuario esté listo para decidir.

## Conceptos clave

**Aplicación LLM**: su asistente de marca con el que los usuarios interactúan dentro de [!DNL ChatGPT] u otras plataformas LLM. Agrupa todas las acciones e implementa como una sola unidad.

**Acción**: una funcionalidad que ofrece tu aplicación. Por ejemplo, &quot;Buscar un distribuidor&quot; o &quot;Examinar productos&quot;. El LLM invoca cada acción cuando el usuario hace una pregunta relevante. Cada acción consta de dos partes: metadatos (nombre, descripción, parámetros) administrados en la interfaz de usuario de [!DNL LLM Apps] y un controlador (su código) en [!DNL GitHub].

**Controlador de acciones**: el código que se ejecuta cuando se invoca una acción. Puede llamar a sus API, recuperar datos activos o devolver datos estáticos. Los controladores se encuentran en su repositorio [!DNL GitHub] en `actions/<name>/index.js`.

**Widget** (la respuesta visual mostrada al usuario): una tarjeta, un carrusel, una tabla o cualquier interfaz de usuario personalizada representada junto a la respuesta de texto de LLM. Los widgets son páginas de HTML alojadas en un sitio [!DNL Edge Delivery Services] (EDS).

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

## Requisitos previos

### Adobe Developer Console

Necesita acceso a [Adobe Developer Console](https://developer.adobe.com/console) con el rol de **Desarrollador** (o **Administrador del sistema**) en su organización Adobe IMS. Asegúrese de que su organización tenga acceso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Para verificarlo, ve a [developer.adobe.com/console](https://developer.adobe.com/console). Si ve la pantalla Inicio rápido, los permisos están correctamente configurados.

![Adobe Developer Console: pantalla de inicio rápido que confirma el acceso de desarrollador](/help/assets/overview/dev-console-access-granted.png)

Si en su lugar ve un mensaje **Acceso restringido**, no tiene la función Desarrollador. Póngase en contacto con el administrador de su organización IMS para solicitar acceso.

![Adobe Developer Console — Mensaje de acceso restringido](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

Necesita una cuenta de [!DNL GitHub] con los siguientes permisos en su organización:

- **Crear repositorios**: debe crear dos repositorios en su organización: uno para el código de la aplicación y otro para el proyecto EDS. Para verificarlo, ve a [github.com/new](https://github.com/new); si puedes seleccionar tu organización en la lista desplegable **Propietario**, tienes el permiso.

  ![Nuevo menú desplegable del propietario del repositorio de GitHub que muestra la selección de la organización](/help/assets/overview/github-repo-owner-dropdown.png)

- **Instalar [!DNL GitHub] aplicaciones**: necesita los permisos adecuados para instalar una aplicación de [!DNL GitHub] en su organización. Consulte [Requisitos para instalar una aplicación de GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

### AEM Sites con [!DNL Edge Delivery Services]

Los widgets de acción están hospedados en **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. Su organización necesita una licencia de AEM Sites que incluya [!DNL Edge Delivery Services]. Debe tener la función **Admin** en su organización de EDS.

Para verificarlo, ve a la [herramienta de administración de usuarios de EDS](https://tools.aem.live/tools/user-admin/index.html), escribe el nombre de tu organización, deja **Sitio** en blanco y haz clic en **Buscar usuarios**. Busque su cuenta en la lista y confirme que muestra el distintivo **admin**.

![Herramienta de administración de usuarios de EDS que muestra un usuario con el rol de administrador](/help/assets/overview/eds-user-admin.png)

### Plataforma LLM (para pruebas)

Para probar la aplicación implementada, necesita un nivel de suscripción compatible que permita aplicaciones MCP personalizadas y **modo de desarrollador** habilitado. Por ejemplo, [!DNL ChatGPT] requiere una suscripción a **Pro**, **Business** o **Enterprise / Edu**.

## Introducción

Teniendo en cuenta un caso de uso, [crea una aplicación](/help/guides/create-app.md) para empezar a crear e implementar tu experiencia [!DNL LLM Apps].

