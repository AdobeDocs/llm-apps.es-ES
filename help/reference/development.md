---
title: Desarrollo y prueba de controladores locales
description: Estructura del proyecto del controlador, comandos del servidor local, pruebas de MCP y pruebas de unidad para aplicaciones LLM de Adobe.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
---

# Desarrollo y prueba de controladores locales {#development}

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

Para infraestructuras gestionadas por Adobe, solicite acceso al repositorio antes de seguir esta referencia. La ejecución de controladores locales no requiere credenciales de Adobe, pero los servicios ascendentes protegidos siguen precisando su propio acceso autorizado.

## Estructura del proyecto

La plantilla del controlador actual sigue este diseño:

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

- **`entry.js`** es el punto de entrada del Webpack. En el momento de la compilación, detecta cada archivo de `actions/*/index.js` y los agrupa en un único archivo de `dist/index.js`. No modifique el punto de entrada.
- Se ignora a **`actions.json`**. La canalización de implementación lo escribe automáticamente a partir de los metadatos de la acción en [!DNL LLM Apps].
- **Pruebas** en vivo bajo `test/actions/`, **no** dentro de `actions/`. La compilación detecta los puntos de entrada del controlador y agrupa sus importaciones; mantenga las pruebas fuera de ese árbol de detección.
- **Las rutas de acción** utilizan el identificador de código o su forma dividida con guiones. Por ejemplo, `search_products` se resuelve en `actions/search_products/index.js` o, para el código generado, `actions/search-products/index.js`. El nombre para mostrar no es la clave de búsqueda.
- **Los datos de soporte**, como un `data.json` generado, se incluyen en el paquete cuando el controlador los importa.

## Desarrollo local

Para desarrollar y probar controladores localmente sin credenciales de Adobe, ejecute:

```bash
npm install
npm run dev:local
```

El comando de desarrollo crea el proyecto con Webpack e inicia un servidor HTTP Node.js sin formato en `http://localhost:9080`. Detenga y vuelva a ejecutar `npm run dev:local` después de cambiar el controlador o los metadatos locales; el comando no inspecciona los archivos.

Para utilizar un puerto diferente:

```bash
PORT=9081 npm run dev:local
```

### Comportamiento de metadatos locales

La interfaz de usuario actual no proporciona una descarga de `actions.json`, aunque el README de plantillas y el banner de inicio en tiempo de ejecución mencionen uno. El servidor local también se inicia sin un archivo rellenado: la compilación crea `{"actions":[]}` cuando falta el archivo y el motor en tiempo de ejecución vuelve a la detección de controladores.

La detección es útil para enumerar controladores, pero no es un modo completo de prueba de argumentos. Sin un esquema de entrada, MCP SDK llama al controlador con contexto de tiempo de ejecución como primer argumento en lugar de usar la firma `(args, extra)` normal. SDK no reenvía los argumentos de acción proporcionados.

Para las llamadas MCP con argumentos, cree un **solo local** `actions.json` con metadatos representativos que coincidan con el editor de acciones. Para el controlador `echo` de plantillas:

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

Cuando la matriz `actions` no está vacía, el motor en tiempo de ejecución registra únicamente las acciones nombradas en la matriz. Use el identificador de código de cada acción como `name`. Incluso una acción sin parámetros necesita un esquema de objeto con `"properties": {}` para conservar la firma normal del controlador.

Los metadatos locales no actualizan la interfaz de usuario ni la configuración de implementación. El motor en tiempo de ejecución valida los tipos admitidos, los campos requeridos, las enumeraciones y los elementos de matriz, pero no todas las restricciones de esquema JSON; valide las longitudes, los intervalos, los objetos anidados y las reglas de negocio en el controlador. Las pruebas de integración de plantillas utilizan `test/fixtures/actions.json` como metadatos representativos.

### Variables de aplicación locales

Las variables de aplicación llegan al controlador a través de `extra.variables`, no de `process.env`. Léalas con `getVariable(extra, name)` de `@adobe/llm-apps-runtime`. Para simular una configuración no confidencial después de la generación:

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

Proporcione el manifiesto JSON y el valor con nombre. Incluya el controlador en el `actions.json` local con un esquema de entrada al probar su contrato `(args, extra)` normal. Los indicadores de la línea de comandos no cargan la configuración de la aplicación desde la plataforma. Consulte [Configurar variables y secretos de la aplicación](/help/guides/app-variables.md) para obtener la configuración de implementación específica del entorno.

### Prueba con rizo

Después de agregar los metadatos locales de `echo` anteriores, genere e inicie el servidor y ejecute:

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
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

Las pruebas de unidad del controlador están activas en `test/actions/` y llaman directamente a la función exportada, sin validación de esquema MCP:

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
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

Algunas pruebas generadas utilizan el ejecutor de pruebas integrado del nodo en lugar de Jest. Compruebe las importaciones de prueba y utilice el ejecutor coincidente, por ejemplo `node --test test/actions/search-products.test.js` para un archivo que importe `node:test`. Compruebe las importaciones relativas generadas después de colocar el archivo en `test/actions/`.

Las pruebas unitarias por sí solas no verifican los metadatos, la validación MCP, la carga de widgets ni el comportamiento del host. Pruebe el extremo local con metadatos representativos y, a continuación, pruebe la aplicación implementada en una plataforma LLM compatible.

Una vez superadas las pruebas locales, inserta los cambios y sigue [Implementar cambios](/help/guides/deploy-your-app.md).
