---
title: Información general sobre las aplicaciones LLM de Adobe
description: Descubra qué son las aplicaciones LLM de Adobe, cómo funcionan y cómo empezar a crear su aplicación.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '621'
ht-degree: 1%
---

# Aplicaciones LLM de Adobe: Información general {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

## ¿Qué es [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] permite que su marca ofrezca acciones útiles (como la detección de productos, las comprobaciones de disponibilidad o las reservas de servicios) en asistentes de IA admitidos, como [!DNL ChatGPT] y [!DNL Claude].

[!DNL LLM Apps] está disponible en [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).

## Qué puede hacer con [!DNL LLM Apps]

- **Crear acciones LLM de propiedad de la marca**: defina los flujos comerciales específicos que desea activar dentro de los asistentes de IA (por ejemplo, *Programar una prueba*, *Comparar productos*, *Reservar un servicio*).
- **Crear widgets LLM interactivos**: cree componentes de la interfaz de usuario visual (tarjetas de producto, formularios de reserva, localizadores de tiendas) administrados como componentes de AEM en su repositorio [!DNL GitHub].
- **Mantener el control centralizado de la marca**: los autores y desarrolladores conservan el control total sobre todo el contenido, las copias y los elementos visuales expuestos dentro de la plataforma LLM, con aprobaciones administradas a través de AEM.
- **Implementar en ensayo y producción**: Una canalización de implementación controlada le permite probar la experiencia en un entorno de ensayo antes de pasar a producción.
- **Controlar la visibilidad en el nivel de acción**: habilite o deshabilite acciones individuales y, a continuación, implemente la aplicación para aplicar el cambio.
- **Mida lo que impulsa las decisiones**: Recuentos de déclencheur de acción superficial, tasas de éxito, tasas de abandono, indicadores de usuario principales y puntuaciones de visibilidad de Analytics integrado (con tecnología de Adobe Customer Journey Analytics).

## Por qué [!DNL LLM Apps] importa

Las interacciones de LLM son fundamentalmente diferentes de la búsqueda tradicional. La sesión LLM promedio dura cuatro veces más que una sesión de búsqueda tradicional. Más del 40% de los consumidores dependen de las herramientas de IA para tomar decisiones de compra complejas. Sin [!DNL LLM Apps], podría ganar la mención pero perder al cliente. [!DNL LLM Apps] garantiza que su marca no solo sea visible sino que se pueda procesar en el momento exacto en que un usuario esté listo para decidir.

## Conceptos clave {#key-concepts}

### Aplicación LLM

El asistente de marca con el que los usuarios interactúan dentro de [!DNL ChatGPT] u otras plataformas LLM. Agrupa todas las acciones e implementa como una sola unidad.

### Acción {#actions}

Una funcionalidad que ofrece tu aplicación, como *Buscar un distribuidor* o *Examinar productos*. La plataforma LLM invoca una acción cuando una solicitud coincide con su descripción. [!DNL LLM Apps] administra los metadatos de la acción y el repositorio [!DNL GitHub] almacena el código del controlador.

### Controlador de acciones

Función del lado del servidor que se ejecuta cuando la plataforma LLM invoca una acción. Utilice el controlador para validar la entrada, llamar a las API y devolver texto más datos estructurados.

### Widget {#widgets-eds}

La respuesta visual que se muestra con la respuesta de LLM, como una tarjeta, un carrusel o una tabla. Los widgets generados son bloques de un repositorio [!DNL Edge Delivery Services] (EDS). Con la infraestructura gestionada por Adobe, Adobe prepara y aloja el repositorio para una versión de prueba. También puede conectar su propio repositorio.

### servidor MCP

El extremo expuesto después de la implementación. Una plataforma LLM admitida se conecta a este extremo para descubrir e invocar sus acciones.

## Funcionamiento

Usted define las capacidades de su marca en [!DNL LLM Apps]. La plataforma hace que estas funcionalidades estén disponibles como acciones, y un asistente de IA utiliza las acciones para responder a las solicitudes de los clientes en la conversación.

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

Para obtener detalles técnicos sobre qué generar y cómo funcionan juntos los componentes, consulte [Cómo se conecta una aplicación](/help/guides/app-architecture.md).

## Introducción {#choose-your-journey}

La creación de una aplicación y sus acciones son pasos independientes:

1. [Configurar la infraestructura](/help/guides/create-app.md). Use la infraestructura administrada de Adobe para una versión de prueba o proporcione dos repositorios vacíos de [!DNL GitHub] mediante el asistente para crear aplicaciones. La plataforma inicializa una para los controladores de acciones y otra para los widgets.
2. [Cree sus acciones](/help/guides/creating-actions.md). Pida a la plataforma que analice su sitio web y genere acciones, o que las cree usted mismo.
3. [Implementar y probar la aplicación](/help/guides/deploy-your-app.md). Revise el trabajo generado, impleméntelo en Fase y pruébelo en una plataforma LLM compatible.

La generación automática le proporciona un punto de partida con el comportamiento de ejemplo. Antes de poner la aplicación a disposición de los clientes, [conecta tus controladores a tus sistemas](/help/guides/customize-handler.md) y [personaliza los widgets](/help/guides/widgets.md).
