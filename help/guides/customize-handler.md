---
title: Personalizar un controlador de acciones generado
description: Comprenda el contrato del controlador de aplicaciones LLM de Adobe, reemplace los datos de muestra generados y mantenga la salida del controlador alineada con su widget.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '541'
ht-degree: 0%

---


# Personalizar un controlador generado {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

La plataforma crea un controlador de trabajo para cada acción generada. El controlador devuelve inicialmente datos de ejemplo para que pueda probar la experiencia completa.

Utilice esta guía para comprender el contrato del controlador y reemplazar los datos de ejemplo con las API o fuentes de datos.

**Recorrido:** Busque el controlador generado → comprender sus entradas y el resultado → conectar el sistema → mantener el contrato del widget alineado → probar e implementar.

## Buscar el controlador generado

Abra el repositorio de controladores seleccionado durante la incorporación:

```text
actions/
└── <action-name>/
    └── index.js
```

Las pruebas coincidentes se almacenan por separado:

```text
test/
└── actions/
    └── <action-name>.test.js
```

Edite el `index.js` generado. No cambie archivos de tiempo de ejecución como `entry.js`.

## Contrato de controlador

Cada controlador exporta una función asincrónica:

```javascript
module.exports = async (args) => {
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

La función recibe un objeto `args` y devuelve un objeto result.

### Entrada: `args`

`args` contiene los parámetros definidos para la acción en [!DNL LLM Apps].

Para una acción con `category` y `query` parámetros:

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

El motor en tiempo de ejecución valida el esquema de entrada cuando los metadatos de la acción incluyen `inputSchema`, como lo hace después de la implementación. La detección de controladores locales sin `actions.json` no aplica la validación de esquemas. El controlador siempre debe aplicar reglas empresariales como valores admitidos, longitudes máximas y combinaciones permitidas.

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

Tenga esta respuesta concisa. No incluya credenciales, errores internos ni datos que el usuario no tenga autorización para ver.

### Salida: `structuredContent`

Devolver `structuredContent` cuando la acción tenga un widget. Debe ser un objeto sin formato, no una matriz vacía.

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent` se envía al widget, no al LLM. Devuelve solo los campos requeridos por la interfaz.

Para una acción de sólo texto, `structuredContent` se puede omitir.

## El contrato controlador-widget

El controlador y el widget comparten un contrato: la forma de `structuredContent`.

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

El widget lee el resultado del controlador del puente SDK de aplicaciones LLM:

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

Si el controlador devuelve:

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

el widget debe leer `structuredContent.products` y `structuredContent.total`.

Al cambiar el nombre o el tipo de un campo, el widget se puede romper. Actualice el controlador, el widget y las pruebas juntos.

## Reemplazar datos de ejemplo

Los controladores generados generalmente contienen una matriz de muestra en memoria. Reemplace esa búsqueda de datos con una llamada del lado del servidor al sistema.

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

Mantenga el acceso a la red protegida en el controlador. Nunca coloque credenciales de API en un widget de JavaScript o control de código fuente.

## Gestión de estados esperados

Conservar una forma de salida predecible para cada resultado.

### Resultados encontrados

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### Sin resultados

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

El widget ahora puede procesar un estado vacío sin saber si `products` existe.

En el caso de errores de servicio, devuelva o lance un error seguro sin exponer los seguimientos de pila, tokens, hosts internos o cuerpos de respuesta ascendentes.

## Prueba del contrato

Actualice las pruebas generadas cada vez que cambie el controlador. Cubierta:

- Argumentos válidos y no válidos.
- Estados de resultados y sin resultados.
- Errores y tiempos de espera de API.
- Respuestas de API mal formadas.
- `content` siempre está presente.
- `structuredContent` es un objeto sin formato.
- La forma esperada por el widget.

Ejecutar:

```bash
npm test
```

Para pruebas de MCP local, vea [Desarrollo y prueba de controladores locales](/help/reference/development.md).

## Implementación del cambio

1. Confirme e inserte los cambios del controlador.
2. Si la forma de los datos ha cambiado, actualice y presione el widget.
3. [Implementar la aplicación](/help/guides/deploy-your-app.md) en Fase.
4. [Probar el complemento ChatGPT](/help/guides/test-in-chatgpt.md).
5. Una vez que Fase se haya realizado correctamente, implemente en Producción.

A continuación, consulte [Personalizar un widget generado](/help/guides/widgets.md).
