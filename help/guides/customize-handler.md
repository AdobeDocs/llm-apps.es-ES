---
title: Personalizar un controlador de acciones generado
description: Comprenda el contrato del controlador de aplicaciones LLM de Adobe, reemplace los datos de muestra generados y mantenga la salida del controlador alineada con su widget.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# Personalizar un controlador generado {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

La plataforma crea un controlador de trabajo para cada acción generada. El controlador devuelve inicialmente datos de ejemplo para probar la experiencia completa.

**En esta guía:** Aprenda a encontrar el controlador generado, reemplazar sus datos de ejemplo por llamadas a los sistemas y mantener su respuesta compatible con el widget. A continuación, pruebe e implemente sus cambios.

## Buscar el controlador generado

Abra el repositorio de controladores conectado durante la [configuración de la infraestructura](/help/guides/create-app.md). Si utiliza una infraestructura administrada de Adobe, solicite acceso al código antes de continuar.

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

Las pruebas coincidentes se encuentran en un directorio independiente:

```text
test/
└── actions/
    └── <action-slug>.test.js
```

Utilice la solicitud de extracción generada para localizar los archivos exactos. El slug de carpeta usa guiones: la plataforma genera una acción con el identificador de código `search_products` en `actions/search-products/`. El motor en tiempo de ejecución reconoce el formulario con guiones, así como una carpeta que coincide exactamente con el identificador.

Edite el `index.js` generado y los datos de ejemplo asociados. No cambie archivos de tiempo de ejecución como `entry.js`.

## Contrato de controlador

Cada controlador exporta una función asincrónica:

```javascript
module.exports = async (args, extra) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

Para una acción con un esquema de entrada, la función recibe un objeto `args` y un contexto de tiempo de ejecución opcional, `extra`, y devuelve un objeto de resultado. Use `extra` para los ayudantes de tiempo de ejecución, como `getVariable(extra, 'PRODUCT_API_ORIGIN')`; las variables de aplicación no se exponen automáticamente a través de `process.env`. Consulte [Configurar variables y secretos de la aplicación](/help/guides/app-variables.md).

### Entrada: `args`

`args` contiene los parámetros definidos para la acción en [!DNL LLM Apps].

Para una acción con `category` y `query` parámetros:

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

Con los metadatos de acción, el motor en tiempo de ejecución convierte el esquema de entrada declarado en validación para las reglas admitidas, incluidos los campos obligatorios, los tipos primitivos, las enumeraciones y los elementos de matriz. El motor en tiempo de ejecución no realiza la validación completa del esquema JSON. Compruebe restricciones como longitudes de cadena, intervalos numéricos y propiedades de objeto anidadas en el controlador.

La detección local sin metadatos de esquema no equivale a la implementación: el motor en tiempo de ejecución no pasa argumentos de acción en el formulario `(args, extra)` normal. Utilice pruebas de controlador directo o metadatos `actions.json` locales representativos para probar argumentos. Consulte [Desarrollo y prueba de controladores locales](/help/reference/development.md).

### Salida: `content`

Devolver siempre `content`. Es una matriz de partes de contenido leídas por la plataforma LLM y por hosts que no muestran widgets.

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

Mantenga la respuesta de texto concisa. No incluya credenciales, errores internos ni datos que el usuario no tenga autorización para ver.

### Salida: `structuredContent`

Devolver `structuredContent` cuando la acción tenga un widget. Debe ser un objeto sin formato, no una matriz vacía.

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent` forma parte del resultado de la acción MCP entregado al host. No dé por hecho que está oculta al modelo o al usuario. Devuelva solo los datos autorizados que requiera la experiencia, nunca las credenciales ni la configuración del servidor privado.

Para una acción de solo texto, devolver `structuredContent` es opcional.

## El contrato controlador-widget

El controlador y el widget comparten un contrato: la forma de `structuredContent`.

| Salida de controlador | Función |
|---|---|
| `content` | Respuesta legible por modelo y reserva de solo texto |
| `structuredContent` | Datos entregados al host y leídos por el widget a través de `bridge.toolResult` |

El widget lee el resultado del controlador del puente SDK de aplicaciones LLM:

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

Los controladores generados actualmente utilizan un objeto que contiene `arguments` y `records`. Las aplicaciones existentes pueden utilizar una forma diferente, por lo que debe comprobar los archivos generados antes de editarlos. Al cambiar el nombre o el tipo de un campo, el widget se puede romper. Actualice el controlador, el widget y las pruebas juntos.

## Reemplazar datos de ejemplo

Los controladores generados cargan registros representativos de `data.json`. Reemplace la búsqueda de datos de ejemplo por una llamada del lado del servidor al sistema. El siguiente ejemplo utiliza una API pública de catálogo HTTPS y lee su origen no confidencial desde una variable de aplicación. Adapte la validación de respuestas a la API.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

Mantenga el acceso a la red protegida en el controlador. Nunca coloque credenciales de API en un widget de JavaScript o control de código fuente. Para las solicitudes realizadas en nombre de un usuario que inició sesión, consulte [Autenticar usuarios finales](/help/guides/authentication.md).

Para obtener la configuración no confidencial que necesita su controlador, como una dirección URL de servicio, vea [Configurar variables y secretos de la aplicación](/help/guides/app-variables.md). Configure las variables por separado para cada entorno. Los cambios de variables tienen efecto en la siguiente implementación. La interfaz de usuario actual solo admite la adición de variables; no utilice variables para almacenar credenciales de API.

## Gestión de estados esperados

Conservar una forma de salida predecible para cada resultado.

### Resultados encontrados

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### Sin resultados

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

Una forma de resultado predecible permite que el widget represente un estado vacío sin tener dudas sobre si `records` existe.

Para los errores del servicio, devuelva un resultado de error seguro con `isError: true`, como en el ejemplo. Compruebe `result.isError` en el widget para que la solicitud errónea no se presente como una búsqueda vacía correcta. No exponga los seguimientos de pila, las credenciales, los hosts internos ni los cuerpos de respuesta de flujo ascendente.

## Prueba del contrato

Actualice las pruebas generadas cada vez que cambie el controlador. Cubierta:

- Argumentos válidos y no válidos.
- Estados de resultados y sin resultados.
- Errores y tiempos de espera de API.
- Respuestas de API mal formadas.
- Presencia de `content` en cada resultado.
- Un objeto sin formato para `structuredContent`.
- La forma esperada por el widget.

Ejecute el grupo de pruebas configurado del repositorio:

```bash
npm test
```

Si una prueba generada importa `node:test` en lugar de Jest, ejecútela con `node --test test/actions/<action-slug>.test.js` y compruebe que su ruta de importación se resuelve desde `test/actions/`. Utilice el ejecutor de pruebas adecuado para el archivo; el comando `npm test` de la repetidor ejecuta Jest.

Para pruebas de MCP local, vea [Desarrollo y prueba de controladores locales](/help/reference/development.md).

## Implementación del cambio

1. Confirme e inserte los cambios del controlador.
2. Si cambia la forma de los datos, actualice y presione el widget.
3. [Implementar la aplicación](/help/guides/deploy-your-app.md) en Fase.
4. Realizar pruebas en una plataforma LLM compatible. Ver [Prueba en ChatGPT](/help/guides/test-in-chatgpt.md) o [Prueba en Claude](/help/guides/test-in-claude.md).
5. Una vez superadas las pruebas en Fase, implemente en Producción.

A continuación, consulte [Personalizar un widget generado](/help/guides/widgets.md).
