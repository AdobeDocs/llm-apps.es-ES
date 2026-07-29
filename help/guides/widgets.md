---
title: Personalización de un widget EDS generado
description: Comprenda y personalice el widget de Edge Delivery Services creado por el agente de incorporación de aplicaciones LLM de Adobe.
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '650'
ht-degree: 0%

---


# Personalizar un widget generado {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Esta guía supone una familiaridad básica con Adobe Edge Delivery Services (EDS). Si es nuevo en EDS, lea primero el [tutorial para desarrolladores de EDS](https://www.aem.live/developer/tutorial) y [Exploración de bloques](https://www.aem.live/docs/exploring-blocks) para conocer los aspectos básicos: los bloques, la función `decorate` y la estructura del proyecto EDS, antes de personalizar un widget.

El agente de incorporación crea un widget EDS para cada acción generada. El widget ya recibe el resultado de la acción, procesa datos de muestra, aplica el estilo de host y está vinculado a la acción en [!DNL LLM Apps].

Comience por probar el widget generado. A continuación, personalice su contrato de datos, su interacción y su diseño visual.

**Recorrido:** Busque el bloque generado → alinear su contrato de datos → personalizar de forma segura → previsualizar localmente → implementar y probar.

## Búsqueda del widget generado

Abra el repositorio EDS seleccionado al crear la aplicación. Cada widget generado es un bloque EDS:

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- El archivo JavaScript lee el resultado de la acción y crea la interfaz.
- El archivo CSS controla el diseño, el comportamiento interactivo y el diseño visual.
- La solicitud de extracción generada muestra los archivos exactos creados para la acción.

El agente de incorporación también configura las URL del widget y los archivos de SDK compatibles. No es necesario crear un segundo proyecto EDS ni volver a introducir esos valores para personalizar un widget generado.

## Cómo conecta el SDK de aplicaciones LLM el widget

El paquete `@adobe/llmapps-sdk` conecta el widget EDS al host LLM. El repositorio EDS generado incluye:

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

No importe SDK en el bloque. Se proporcionó el(la) `bridge` conectado(a) automáticamente. Permite al widget:

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

Cuando cambie `structuredContent`, actualice el controlador y el widget juntos. Consulte [Personalizar un controlador generado](/help/guides/customize-handler.md) para obtener el contrato de devolución completo.

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

Valide las direcciones URL antes de asignarlas a `href` o `src` y permita solamente los protocolos requeridos por la experiencia.

## Uso del puente host

EDS pasa un puente conectado a `decorate(block, bridge)`. Guarde las llamadas de puente para que el bloque también se procese durante la previsualización directa de EDS.

### Aplicar estilos de host

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

Esto aplica la tipografía del host y las variables de tema. El widget CSS debe admitir temas host claros y oscuros.

### Enviar un mensaje de seguimiento

```javascript
await bridge.sendMessage('Show me similar products.');
```

Use `sendMessage` cuando una interacción deba continuar la conversación.

### Llamar a otra acción

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

Use `callTool` para una interacción explícita que necesite otro resultado de acción. Pasar solo valores validados y controlar errores sin exponer detalles internos.

### Mantener sincronizado el tamaño del widget

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

Llame a `autoResize` después del procesamiento inicial para que el host pueda responder a los cambios de contenido.

## Previsualice los cambios

Los bloques generados deben incluir datos de ejemplo para la vista previa directa cuando `bridge` no esté disponible.

Para previsualizar el proyecto EDS localmente:

```bash
npm install -g @adobe/aem-cli
aem up
```

Abra la página del widget generado en `http://localhost:3000`. Verificar:

- Estados vacío, de carga, de éxito y de error.
- Texto largo y campos opcionales que faltan.
- Navegación por teclado y enfoque visible.
- Temas claros y oscuros.
- Diseños estrechos y anchos.

A continuación, implemente la aplicación para ensayo y prueba con `structuredContent` en directo en la plataforma LLM.

## Publicación de la personalización

1. Confirme e inserte los cambios de EDS.
2. Si ha cambiado la forma de datos, confirme y presione los cambios del controlador coincidente.
3. Implemente la aplicación para el ensayo.
4. Pruebe la acción y el widget en [!DNL ChatGPT].
5. Promocione la versión verificada en producción.

## Otras configuraciones de EDS

Si no ha utilizado el agente de incorporación o desea integrar un sitio EDS existente, consulte [Traer su propio proyecto EDS](/help/guides/bring-your-own-eds.md).
