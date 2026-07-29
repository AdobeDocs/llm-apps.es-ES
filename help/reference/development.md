---
title: Desarrollo y prueba de controladores locales
description: Estructura del proyecto del controlador, comandos del servidor local, pruebas de MCP y pruebas de unidad para aplicaciones LLM de Adobe.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

---


# Desarrollo y prueba del controlador local {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta referencia al desarrollar controladores localmente. Para el contrato de resultados del controlador, vea [Personalizar un controlador generado](/help/guides/customize-handler.md).

## Requisitos

- Node.js 24 o posterior.
- npm.
- Un clon local del repositorio del controlador vinculado.

## Estructura del proyecto

El repositorio vinculado sigue este diseño:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Puntos clave:

- **`entry.js`** es el punto de entrada del Webpack. En el momento de la compilación, detecta cada archivo de `actions/*/index.js` y lo agrupa en un único archivo de `dist/index.js`. No modificar.
- Se ignora a **`actions.json`**. La canalización de implementación lo escribe automáticamente a partir de los metadatos de la acción en [!DNL LLM Apps].
- **Pruebas** en vivo bajo `test/actions/`, **no** dentro de `actions/`. Webpack agrupa todo lo que hay bajo `actions/` en el artefacto implementado; las pruebas de co-ubicación las enviarían a [!DNL Adobe I/O Runtime].

## Desarrollo local

Puede desarrollar y probar controladores localmente sin credenciales de Adobe:

```bash
npm install
npm run dev:local
```

Esto genera el proyecto con Webpack e inicia un servidor HTTP Node.js sin formato en `http://localhost:9080`. El servidor detecta automáticamente los archivos del controlador bajo `actions/` y los registra como herramientas de MCP.

### Comportamiento de metadatos locales

La IU actual no proporciona una descarga de `actions.json`. Puede ejecutar el servidor local sin este archivo; detecta los controladores bajo `actions/` y los registra con metadatos mínimos.

Sin `actions.json`, los argumentos de acción local no se validan en el esquema de entrada de la interfaz de usuario. Las pruebas de unidad e integración usan `test/fixtures/actions.json` para los metadatos representativos.

### Prueba con rizo

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### Probar con el Inspector MCP

```bash
npx @modelcontextprotocol/inspector
```

Establezca **Tipo de transporte** en `streamable-http` y **URL** en `http://localhost:9080`.

## Pruebas

Las pruebas unitarias del controlador están activas en `test/actions/` y reflejan el diseño de `actions/`:

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

Ejecutar pruebas con:

```bash
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

Una vez superadas las pruebas locales, inserta los cambios y sigue [Implementar cambios](/help/guides/deploy-your-app.md).

