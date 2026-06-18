---
title: Configuración del widget (EDS)
description: Aprenda a configurar un proyecto de widget de Edge Delivery Services e implementar el contrato de bloque para procesar respuestas visuales dentro de plataformas LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '1226'
ht-degree: 1%

---


# Configuración del widget (EDS)

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Esta guía explica cómo crear un widget EDS de extremo a extremo: desde configurar la acción en la interfaz de usuario de [!DNL LLM Apps], hasta configurar el proyecto EDS y escribir el código de bloque que procesa los datos dentro de la plataforma LLM. Para obtener información general de alto nivel, consulte [Conceptos principales](/help/overview/overview.md#widgets-eds).

## El SDK [!DNL LLM Apps]

Todo comienza con el paquete [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm. SDK es la biblioteca de JavaScript que alimenta el canal de comunicación bidireccional entre el widget y el host LLM.

El SDK también envía `aem-embed.js`, el punto de entrada específico de EDS que conecta el SDK a la canalización de bloques estándar de EDS. Al `npm install @adobe/llmapps-sdk`, un script posterior a la instalación copia automáticamente dos archivos en el proyecto:

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

En proyectos EDS, **nunca usas SDK directamente en el código de bloque.** `aem-embed.js` crea y administra la conexión de SDK y pasa una instancia de `LLMApp` totalmente conectada al bloque como el argumento de `bridge` en `decorate(block, bridge)`. La API completa de SDK está disponible en `bridge`, no se necesita importación.

Si está generando un widget **sin EDS** (un paquete estándar o proyecto TypeScript), puede usar el SDK directamente:

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## Cómo encaja todo

Cuando la IA llama a la acción y el controlador devuelve `structuredContent`, la plataforma LLM procesa un widget interactivo en la conversación. Hay tres cosas que hacen que esto funcione juntos:

**La interfaz de usuario [!DNL LLM Apps]**: cuando crea una acción, escribe una **[!UICONTROL URL de script]** y una **[!UICONTROL URL de widget]** en la pestaña Metadatos de widget. La dirección URL del script apunta a `aem-embed.js`: el archivo que se envía con SDK y que se encuentra en el repositorio EDS en `scripts/llm-apps/aem-embed.js`. Esto indica a la plataforma LLM qué script se debe cargar cuando se invoca la acción.

**`aem-embed.js`**: la plataforma LLM carga este script en una superficie de widget de espacio aislado. `aem-embed.js` es un elemento personalizado de HTML (`<aem-embed>`) que actúa como punto de entrada compatible con EDS para el widget. Realiza el protocolo de enlace con el host LLM mediante SDK, suprime la canalización de páginas EDS normal (sin encabezado/pie de página), obtiene el contenido de la página EDS de la URL del widget, ejecuta la canalización de bloques EDS y entrega un objeto `bridge` activo a la función `decorate()` de cada bloque.

**Su código de bloque**: escribe un bloque EDS estándar que exporta una función `decorate(block, bridge)`. `bridge` es la instancia de SDK conectada; le proporciona el resultado estructurado de la acción y le permite enviar mensajes de vuelta a la conversación.

## Añadir a un proyecto EDS existente

Si ya tiene un proyecto EDS, solo hay dos pasos antes de que pueda empezar a escribir bloques.

1. Instalar `@adobe/llmapps-sdk`. El script posterior a la instalación copia `aem-embed.js` y `llmapps-sdk.js` en `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. Configure los encabezados CORS para que la plataforma LLM pueda cargar las páginas de widget y los scripts de origen diverso; consulte [Configurar encabezados CORS](#configure-cors-headers) a continuación.

A continuación, cree el bloque siguiendo el contrato [`decorate(block, bridge)`](#the-decorateblock-bridge-contract), cree la página del widget e introduzca las direcciones URL en el cuadro de diálogo Crear acción.

## Configuración de un nuevo proyecto EDS

### Creación del repositorio

1. Cree un nuevo repositorio [!DNL GitHub] basado en la plantilla [AEM ](https://github.com/adobe/aem-boilerplate).
2. Agregue la [aplicación GitHub de sincronización de código de AEM](https://github.com/apps/aem-code-sync) al repositorio.
3. Instale la CLI de AEM para el desarrollo local: `npm install -g @adobe/aem-cli`.
4. Instalar `@adobe/llmapps-sdk`. El script posterior a la instalación copia `aem-embed.js` y `llmapps-sdk.js` en `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

Para obtener una guía completa sobre los proyectos EDS, consulte el [tutorial para desarrolladores de AEM](https://www.aem.live/developer/tutorial) y la [estructura del proyecto](https://www.aem.live/developer/anatomy-of-a-project).

Una vez configurada, el sitio de EDS estará disponible en:

- **Vista previa:** `https://main--<repo>--<owner>.aem.page/`
- **Activo:** `https://main--<repo>--<owner>.aem.live/`

### Estructura del repositorio

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### Configuración de encabezados CORS

La plataforma LLM carga las páginas del widget EDS dentro de una superficie de widget sandbox. El sitio de EDS debe devolver encabezados correctos de `access-control-allow-origin` para que el host pueda recuperar el origen cruzado del contenido del widget.

Los encabezados se configuran a través del panel de administración de AEM en `admin.hlx.page` mediante el [servicio de configuración](https://aem.live/docs/config-service-setup). Añada encabezados de respuesta personalizados para las rutas en las que se encuentran las páginas de los widgets y los scripts de SDK:

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>Se acepta el uso de `*` como valor de origen para el contenido de widget público en el dominio `.aem.live`. Si su sitio contiene contenido protegido, limite el origen a dominios específicos.

### Creación de la página del widget

Cree una página en la herramienta de creación de EDS y añádale el bloque. La dirección URL de la página se convierte en la **[!UICONTROL dirección URL del widget]** que configuró en la acción; es decir, la única conexión entre la acción y el bloque. No hay ningún requisito de nomenclatura entre el bloque y el nombre de la acción.

Creación de ![EDS: bloque agregado a la página del widget](/help/assets/guide-widget/aem-author.png)

### Introduzca las direcciones URL en el cuadro de diálogo Crear acción

Después de configurar el repositorio EDS, vaya a **Metadatos de widget → URL de plantilla** al crear la acción:

**[!UICONTROL URL de script]** — apunta a `aem-embed.js` en su repositorio EDS. Este es el mismo valor para cada acción en el mismo proyecto EDS:

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL URL del widget]**: la URL de la página EDS que creó para este widget. Únicos por acción:

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

La plataforma LLM carga `aem-embed.js` desde la dirección URL del script. `aem-embed.js` entonces recupera `.plain.html` de la URL del widget para obtener el contenido del bloque.

## Flujo de datos

La ruta completa desde el controlador a un widget procesado:

1. **El controlador de acciones** devuelve `structuredContent`:

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. **LLM platform** abre una superficie de widget y carga `aem-embed.js` desde la dirección URL del script.

1. **`aem-embed.js`** se conecta al host a través de SDK, recupera `.plain.html` de la URL del widget, ejecuta la canalización de bloques EDS y llama a `decorate(block, bridge)` en el bloque.

1. **Su bloque** lee los datos de `bridge.toolResult` y procesa la interfaz de usuario.

1. **Interacción del usuario** déclencheur `bridge.sendMessage(...)` o `bridge.callTool(...)`, enviando un seguimiento a la conversación.

## El contrato `decorate(block, bridge)`

Cada bloque de widgets de EDS debe exportar una función predeterminada `decorate`. Esta es la firma de bloque EDS estándar, extendida con un segundo argumento: la `bridge` conectada, que es una instancia de SDK [`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) con la API completa disponible:

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

`bridge` solo está presente cuando se ejecuta dentro de la superficie del widget de plataforma LLM. Proteja siempre las llamadas de puente para que el bloque también se procese cuando se previsualice directamente en un explorador o en el servidor de desarrollo local.

### Procesamiento de datos desde el resultado de la acción

`bridge.toolResult` es una promesa que se resuelve con el resultado completo que devolvió su controlador, incluido `structuredContent`.

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### Aplicación del tema del host

Llame a `bridge.applyHostStyles()` al principio de `decorate` para insertar las fuentes y variables CSS del host (tema claro/oscuro, tipografía) en el widget. Esto mantiene el widget visualmente coherente con la IU de la plataforma LLM circundante.

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

Para reaccionar a los cambios de la temática durante la ejecución (por ejemplo, cuando el usuario cambia entre el modo claro y el oscuro):

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### Envío de un mensaje de seguimiento

`bridge.sendMessage(text)` inserta un mensaje de usuario en la conversación. Esta es la forma principal en que un widget déclencheur una mayor interacción de IA; por ejemplo, cuando un usuario hace clic en una tarjeta de producto para solicitar detalles.

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### Llamar a otra acción directamente

`bridge.callTool(name, args)` invoca otra acción desde el widget sin pasar por un mensaje de usuario. Útil para cargar datos relacionados bajo demanda.

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### Cambio de tamaño automático del widget

La plataforma LLM ajusta el tamaño del widget en función de lo que informe. Use `bridge.autoResize(element)` para mantener sincronizada la altura del widget a medida que cambia el contenido; usa un `ResizeObserver` internamente. Invoque después del procesamiento inicial:

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

O informe de un tamaño fijo manualmente:

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### Modo de vista previa y desarrollo local

Al obtener una vista previa de una página EDS directamente en un explorador o en el servidor de desarrollo local, `bridge` es `undefined`. Utilice el patrón de reserva de datos de ejemplo que se muestra arriba para que el bloque se procese inmediatamente sin un controlador activo.

Para iniciar un servidor de desarrollo local:

```bash
npm install -g @adobe/aem-cli
aem up
```

Se abrirá `http://localhost:3000`, donde podrá navegar hasta las páginas del widget y ver el procesamiento de bloques con datos de ejemplo. Los cambios en el bloque JS y CSS se reflejan inmediatamente.

## Pasos siguientes

- [Guía: Escribir el controlador de acciones](/help/guides/write-action-handler.md)

