---
title: Cómo se conecta una aplicación
description: Comprenda las tres partes de una aplicación LLM, cómo funcionan en una conversación y dónde realizar cambios.
source-git-commit: af83670aebde1e6f73585c89a891bc7f5e92fb3a
workflow-type: tm+mt
source-wordcount: '573'
ht-degree: 0%
---

# Cómo se conecta una aplicación {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Una **aplicación LLM** incorpora los servicios de tu marca en una conversación de IA. Por ejemplo, un cliente solicita recomendaciones de café, la aplicación encuentra productos coincidentes y el asistente muestra los resultados.

**En esta guía:** Aprenda qué hace cada parte de la aplicación, dónde realizar cambios y cómo funcionan juntas esas partes cuando alguien usa la aplicación.

## Las tres partes de una aplicación

Una aplicación puede agrupar varias acciones relacionadas, como *Examinar productos*, *Comparar productos* y *Buscar una tienda*. Cada acción tiene su propia configuración, controlador y widget opcional. Para agregar una acción no es necesario otro par de repositorios.

Una aplicación con widgets interactivos consta de tres partes: configuración de acciones, código de controlador y código de widget. Usted administra la configuración en [!DNL LLM Apps]. El controlador y el código del widget se encuentran en dos repositorios [!DNL GitHub] independientes.

```text
┌───────────────────────┐   ┌───────────────────────┐   ┌───────────────────────┐
│ 1. LLM Apps UI        │   │ 2. Handler repository │   │ 3. Widget repository  │
│                       │   │                       │   │                       │
│ Action descriptions   │   │ Connects to your APIs │   │ Widget layout         │
│ Required information  │   │ Runs the task         │   │ Branding and styles   │
│ When to use an action │   │ Returns results       │   │ Interactive controls  │
└───────────────────────┘   └───────────────────────┘   └───────────────────────┘
            │                           │                           │
            └───────────────────────────┼───────────────────────────┘
                                        ▼
                    ┌───────────────────────────────────────┐
                    │ Your app in an AI conversation        │
                    │ The action runs and the widget        │
                    │ shows the result.                     │
                    └───────────────────────────────────────┘
```

- **La configuración de acción describe la tarea.** Indican al asistente cuándo utilizar una acción y qué información necesita. Para la búsqueda de productos, esa información puede ser una categoría o un término de búsqueda.
- **El controlador realiza el trabajo.** Obtiene datos de los sistemas o realiza una tarea. Un controlador de búsqueda de productos, por ejemplo, recupera los productos coincidentes del catálogo.
- **El widget presenta el resultado.** Controla lo que el cliente ve e interactúa con, como tarjetas de producto con nombres, precios y botones.

Se recomienda añadir un widget a cada acción. Un widget le permite controlar el aspecto de los resultados y la interacción de los clientes con ellos, en lugar de dejar la presentación al asistente de IA. También se admiten respuestas de solo texto.

## Dónde realizar cambios

| Qué desea cambiar | Dónde se puede editar |
|---|---|
| Cuando el asistente utiliza una acción o qué información solicita la acción | [Configuración de acción en aplicaciones LLM](/help/reference/reference-docs.md) |
| De dónde provienen los resultados o qué sucede cuando se ejecuta una acción | [Código de controlador en el repositorio de controladores](/help/guides/customize-handler.md) |
| Aspecto de los resultados, incluido el diseño, la marca y las interacciones | [Código de widget en el repositorio de widget](/help/guides/widgets.md) |

Mantenga las tres partes consistentes. Por ejemplo, si cambia la información que devuelve un controlador, actualice el widget para mostrar el nuevo resultado.

## Qué sucede en una conversación

Después de la implementación, su aplicación tendrá una URL de conexión llamada **URL del servidor MCP**. Añada esta URL a una plataforma de IA compatible para que el asistente pueda descubrir y utilizar las acciones de la aplicación.

Para una aplicación con una acción de búsqueda de productos y un widget:

1. Un cliente pregunta al asistente *Mostrarme cápsulas de café.*
2. El asistente utiliza la descripción de la acción para elegir la acción de búsqueda de productos.
3. El controlador obtiene los productos coincidentes del catálogo.
4. El widget muestra los productos como tarjetas en la conversación.
5. El cliente explora los resultados o hace una pregunta de seguimiento.

El cliente permanece en la conversación mientras su aplicación proporciona la información o el servicio. La compatibilidad con widgets depende de la plataforma LLM.

## Aplicar y probar los cambios

Publique los cambios de los widgets e implemente cambios de las acciones antes de probar la experiencia actualizada. Siga [Implementar su aplicación](/help/guides/deploy-your-app.md) para implementarla en Fase y probar la aplicación en una plataforma LLM compatible.

Las acciones generadas automáticamente comienzan con datos de ejemplo. Conecte los controladores a los sistemas reales y pruebe la aplicación antes de ponerla a disposición de los clientes.
