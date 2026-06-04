---
title: Desarrollo
description: Estructura del proyecto, flujo de trabajo de desarrollo local y configuración de pruebas para el código del controlador de aplicaciones LLM de Adobe.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '314'
ht-degree: 4%

---


# Desarrollo

>[!IMPORTANT]
>
>**Descargo de responsabilidad:** Esta es una versión beta de [!DNL LLM Apps]. Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final de la aplicación o del producto.

Esta sección describe la estructura del proyecto de controlador, el flujo de trabajo de desarrollo local y la configuración de pruebas. Para obtener el contrato de controlador y el código de ejemplo, vea [Escribir el controlador de acciones](/help/guides/write-action-handler.md).

## Estructura del proyecto

El repositorio vinculado sigue este diseño:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Puntos clave:

- **`entry.js`** es el punto de entrada del Webpack. En el momento de la compilación, detecta cada archivo de `actions/*/index.js` y lo agrupa en un único archivo de `dist/index.js`. No modificar.
- Se ignora a **`actions.json`**. Descárguelo desde la página Acciones de la interfaz de usuario para el desarrollo local. Para implementaciones, la canalización lo escribe automáticamente desde la API.
- **Pruebas** en vivo bajo `test/actions/`, **no** dentro de `actions/`. Webpack agrupa todo lo que hay bajo `actions/` en el artefacto implementado; las pruebas de co-ubicación las enviarían a [!DNL Adobe I/O Runtime].

## Desarrollo local

Puede desarrollar y probar controladores localmente sin credenciales de Adobe:

```bash
npm install
npm run dev:local
```

Esto genera el proyecto con Webpack e inicia un servidor HTTP Node.js sin formato en `http://localhost:9080`. El servidor detecta automáticamente los archivos del controlador bajo `actions/` y los registra como herramientas de MCP.

### Descargar `actions.json`

Para que el servidor local conozca los metadatos de la acción (nombre, descripción, esquema de entrada), descargue `actions.json` de la página Acciones en la interfaz de usuario de [!DNL LLM Apps] y colóquelo en la raíz del repositorio. Sin ella, el servidor detecta los controladores, pero los registra con metadatos mínimos.

También puede copiar `actions.example.json` a `actions.json` como punto de partida.

### Prueba con rizo

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### Probar con el Inspector MCP

```bash
npx @modelcontextprotocol/inspector
```

Establezca **Tipo de transporte** en `streamable-http` y **URL** en `http://localhost:9080`.

## Pruebas

Las pruebas unitarias del controlador están activas en `test/actions/` y reflejan el diseño de `actions/`:

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

Ejecutar pruebas con:

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## Implementación

No se genera ni implementa manualmente. Para ver un tutorial completo de la canalización de la implementación, consulte [Implementar su aplicación](/help/guides/deploy-your-app.md).

El flujo de trabajo diario es el siguiente:

| Paso | Acción |
|------|--------|
| &#x200B;1. Controlador de escritura o edición | `actions/<name>/index.js` |
| &#x200B;2. Descargar metadatos | Página Acciones → **Descargar actions.json** |
| &#x200B;3. Probar localmente | `npm run dev:local` |
| &#x200B;4. Código push | `git push` |
| &#x200B;5. Implementación | Página de detalles de la aplicación → **[!UICONTROL Implementar]** |

