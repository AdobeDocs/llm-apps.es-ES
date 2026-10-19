---
title: Personalización de un widget EDS generado
description: Comprenda y personalice el widget de Edge Delivery Services creado automáticamente por las aplicaciones LLM de Adobe.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# Personalización de un widget generado {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Esta guía supone una familiaridad básica con Adobe Edge Delivery Services (EDS). Si es nuevo en EDS, lea el [tutorial para desarrolladores de EDS](https://www.aem.live/developer/tutorial) y [Exploración de bloques](https://www.aem.live/docs/exploring-blocks) antes de personalizar un widget. Estos recursos cubren bloques, la función `decorate` y la estructura del proyecto EDS.

Después de la generación correcta, la plataforma proporciona un widget EDS vinculado a la acción en [!DNL LLM Apps]. El widget procesa datos representativos para la vista previa y lee el resultado del controlador cuando se conecta a una plataforma LLM compatible. Revise el diseño y el comportamiento generados antes de personalizar el widget.

Comience por probar el widget generado. A continuación, personalice su contrato de datos, su interacción y su diseño visual.

**En esta guía:** Aprenda a encontrar el widget generado y a adaptarlo a los datos de su acción y al diseño de su marca. A continuación, previsualice los cambios localmente y pruébelos en la aplicación implementada.

## Búsqueda del widget generado

Abra el repositorio EDS conectado durante la [configuración de la infraestructura](/help/guides/create-app.md). Si utiliza una infraestructura administrada de Adobe, solicite acceso al código antes de continuar. Cada widget generado es un bloque EDS:

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- El archivo JavaScript lee el resultado de la acción y crea la interfaz.
- El archivo CSS controla el diseño, el comportamiento interactivo y el diseño visual.
- La solicitud de extracción generada muestra los archivos exactos creados para la acción.

El slug usa guiones: una acción con el identificador de código `search_products` usa un bloque como `blocks/search-products/`. La página de widget generada suele ser `/eds-widgets/search-products-demo`; use la **[!UICONTROL URL de widget]** real en los metadatos de la acción.

La plataforma también configura las URL de los widgets y los archivos SDK compatibles. No es necesario crear un segundo proyecto EDS ni volver a introducir las URL de los widgets para personalizar un widget generado.

## Cómo conecta el SDK de aplicaciones LLM el widget

El paquete `@adobe/llmapps-sdk` conecta el widget EDS al host LLM. Sus puntos de entrada en el repositorio EDS generado son:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

`aem-embed.js` establece la conexión con el host, carga la página EDS y llama al bloque:

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

La incrustación recupera el contenido `.plain.html` de la página del widget e importa sus bloques. No importe SDK en el bloque. La incrustación proporciona automáticamente `bridge`; la carga normal de la página EDS no la proporciona. El puente permite al widget:

- Lea el resultado del controlador con `bridge.toolResult`.
- Aplicar estilo de host con `bridge.applyHostStyles()`.
- Continuar la conversación con `bridge.sendMessage()`.
- Invocar otra acción con `bridge.callTool()`.
- Mantener su tamaño sincronizado con `bridge.autoResize()`.

Esta guía describe los métodos comunes de puente. Consulte el paquete [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) para obtener la API completa.

## Comprensión del contrato de datos

El controlador de acciones devuelve `structuredContent` y el bloque lo lee de `bridge.toolResult`.

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

En este ejemplo, `PREVIEW_RESULT` es el objeto de datos de ejemplo local del bloque. Los widgets generados actualmente utilizan `structuredContent.arguments` y `structuredContent.records`; revise los archivos generados para ver si tienen la forma exacta. Nunca reemplace un resultado en directo vacío con registros de vista previa.

`bridge.toolResult` se resuelve solo con el primer resultado. Utilice `bridge.onToolResult(callback)` si un widget permanece montado y debe gestionar resultados posteriores.

Cuando cambie `structuredContent`, actualice el controlador y el widget juntos. No dé por hecho que los datos estructurados están ocultos en el modelo; forman parte del resultado de la acción del host. Consulte [Personalizar un controlador generado](/help/guides/customize-handler.md) para obtener el contrato de devolución completo.

## Procesar datos externos de forma segura

Trate la salida del controlador como datos que no son de confianza. Prefiera las API de DOM como `textContent` en lugar de insertar valores de respuesta en `innerHTML`.

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

Valide las direcciones URL antes de asignarlas a `href` o `src` y permita solamente los protocolos requeridos por la experiencia.

## Uso del puente host

La incrustación pasa un puente a `decorate(block, bridge)`. Proteja las llamadas interactivas con `bridge?.isConnected`, de modo que el bloque también se procesa durante la previsualización directa de EDS. Un objeto de puente puede existir en modo independiente sin estar conectado; la espera de `toolResult` en modo independiente nunca se resuelve. La vista previa de aplicaciones LLM también establece `bridge.hostContext.preview` para permitir que el bloque elija datos de ejemplo.

### Aplicar estilos de host

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

El método aplica las variables CSS de host y los estilos de fuente disponibles. La aplicación de estilos de host es opcional: los widgets generados pueden utilizar sus propios tokens de diseño en su lugar. La incrustación mantiene las clases `.light` y `.dark` en su cuerpo sombreado. Use `bridge.hostContext.theme` y `bridge.onContextChange(callback)` cuando el bloque también necesite reaccionar a los cambios de tema.

### Enviar un mensaje de seguimiento

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

Use `sendMessage` para continuar la conversación después de una interacción.

### Invocar una acción directamente

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

Use `callTool` para una interacción explícita que necesite un resultado de acción. Pase el identificador de código de la acción de destino real, no su nombre de carpeta de bloque ni su nombre para mostrar; `search_products` es un ejemplo. El host debe admitir llamadas de acción y la acción de destino debe permitir la visibilidad de la aplicación. La llamada a una acción no vuelve a procesar automáticamente el widget actual. Pasar solo valores validados y gestionar llamadas rechazadas sin exponer detalles internos.

Si es necesario cambiar la visibilidad de la acción de destino, edite solo esa acción en **[!UICONTROL Detalles de la acción]**, guarde el cambio y vuelva a implementarlo en el entorno que está probando.

### Mantener sincronizado el tamaño del widget

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

Para permitir que el host responda a los cambios de contenido, llame a `autoResize` después del procesamiento inicial. El método devuelve una función de limpieza que detiene la observación cuando se elimina el widget.

## Previsualice los cambios

Los bloques generados incluyen datos de muestra para la vista previa directa cuando el puente falta, está desconectado o marcado como vista previa. Mantenga la reserva de datos de ejemplo separada de los estados en directo vacío y de error.

Para previsualizar el proyecto EDS localmente:

```bash
npm install -g @adobe/aem-cli
aem up
```

Abra la ruta desde la URL del widget de la acción en el servidor local, por ejemplo `http://localhost:3000/eds-widgets/search-products-demo`, en lugar de solo la raíz del sitio. Verificar:

- Estados vacío, de carga, de éxito y de error.
- Texto largo y campos opcionales que faltan.
- Navegación por teclado y enfoque visible.
- Temas claros y oscuros.
- Diseños estrechos y anchos.

La previsualización directa de EDS no prueba el protocolo de enlace del host ni los datos de acciones activas. Publique los cambios de EDS y pruebe con `structuredContent` activo en la plataforma LLM admitida.

## Publicación de la personalización

1. Confirme e inserte los cambios de EDS.
2. Compruebe que la página del widget y los scripts y estilos modificados están disponibles en las URL de EDS configuradas. Publicar cambios de contenido a través del flujo de trabajo de publicación EDS.
3. Si ha cambiado el controlador o los metadatos de la acción, inserte esos cambios e implemente la aplicación en Fase.
4. Pruebe la acción y el widget en una plataforma LLM compatible. Ver [Prueba en ChatGPT](/help/guides/test-in-chatgpt.md) o [Prueba en Claude](/help/guides/test-in-claude.md).
5. Una vez completada la prueba, implemente los cambios de controladores y metadatos en Producción según sea necesario.

La publicación EDS es independiente de la implementación de aplicaciones. Si Fase y Producción utilizan las mismas URL de EDS, los cambios de widget en esas URL afectarán a ambos entornos; la implementación en Fase no crea una copia de widget aislada.

## Otras configuraciones de EDS

Si creó acciones por su cuenta o desea integrar un sitio EDS compatible existente, consulte [Traer su propio proyecto EDS](/help/guides/bring-your-own-eds.md).
