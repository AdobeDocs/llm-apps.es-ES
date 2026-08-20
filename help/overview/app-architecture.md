---
title: Cómo se conecta una aplicación
description: Una mirada más cercana a cómo las piezas que posee (metadatos de acción, código de controlador y widgets) se juntan en una aplicación LLM que se ejecuta, en el momento de la compilación y en el tiempo de ejecución.
source-git-commit: e066f66b37914e2f747176e865e26dcc074bff20
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# Cómo se conecta una aplicación {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

## En una frase

Una aplicación **LLM** es un conjunto de **acciones** (cada una de ellas una herramienta expuesta a lo largo del modelo **Protocolo de contexto** o **MCP**) que publica en un único extremo. Un host de chat
como [!DNL ChatGPT] detecta esas herramientas, las llama en mitad de la conversación y las procesa
un **widget interactivo** con el resultado: justo dentro del chat.

## Todo el cableado, construye → corre

**Diagrama 1 — tiempo de compilación.** Usted posee tres superficies separadas; la plataforma se fusiona
en una aplicación implementable.

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **IU de aplicaciones LLM**: donde crea, edita y administra la definición de cada acción:
su **identificador de código** (un slug fijo que configuró una vez aquí, por ejemplo `my_action`, que
une esta misma acción en la interfaz de usuario, el controlador y el widget),
descripción, esquema de entrada, opción de widget y indicadores de CSP/visibilidad. No hay código.
- **Repositorio del controlador de acciones**: el repositorio del lado del servidor (andamiaje de nuestra plantilla)
donde se escribe la lógica empresarial. Cada función de controlador devuelve dos cosas:
  `content` (texto sin formato leído por *LLM*) y `structuredContent` (objeto de datos
  el *widget* lee).
- **Repositorio de widgets**: el repositorio EDS en el que cada widget vive como un bloque y obtiene
publicado en una URL pública `*.aem.page`. Cada bloque utiliza
  [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk), el
puente entre el widget y el host/servidor. Implementa las **aplicaciones MCP
especificación** (el protocolo subyacente) detrás de una API simple y
abstrae el host LLM en sí, por lo que el mismo widget funciona sin modificaciones en
  [!DNL ChatGPT], [!DNL Claude], Gemini o cualquier otro host MCP.

**Diagrama 2 — tiempo de ejecución.** Qué sucede en cada mensaje que envía el usuario una vez
que un servidor está activo. Se muestra con [!DNL ChatGPT] como el host de ejemplo:
la misma secuencia se reproduce para cualquier host MCP, como [!DNL Claude].

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
