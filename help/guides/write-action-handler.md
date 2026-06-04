---
title: Escribir el controlador de acciones
description: Aprenda a escribir un controlador de acciones para la aplicación LLM de Adobe, incluido el contrato del controlador, structuredContent y un ejemplo práctico.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '714'
ht-degree: 0%

---


# Escribir el controlador de acciones

>[!IMPORTANT]
>
>**Descargo de responsabilidad:** Esta es una versión beta de [!DNL LLM Apps]. Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final de la aplicación o del producto.

Después de crear una acción en la interfaz de usuario, los metadatos se almacenan en la API [!DNL LLM Apps], pero aún no hay código detrás. Esta guía le explica cómo escribir la función de controlador que se ejecuta cuando una plataforma LLM (como [!DNL ChatGPT] o Claude) invoca su acción.

Para obtener detalles sobre diseño del proyecto, desarrollo local y pruebas, vea [Desarrollo](/help/reference/development.md).

## Contrato de desarrollador

Solo se escriben controladores. Todo lo demás (nombre de la acción, descripción, esquema de entrada, anotaciones, visibilidad del widget, permisos, CSP) se encuentra en la interfaz de usuario de [!DNL LLM Apps] y se entrega al motor en tiempo de ejecución automáticamente en el momento de la implementación. Nunca edita a mano los metadatos en el repositorio y nunca registra una herramienta en el código.

| Inquietud | Donde vive |
|---------|----------------|
| Metadatos (nombre, descripción, esquema, configuración del widget) | Interfaz de usuario [!DNL LLM Apps]: se guardó en la API |
| Código de controlador (la función que se ejecuta) | Su repositorio [!DNL GitHub] — `actions/<name>/index.js` |
| `actions.json` (instantánea de metadatos) | Escrito por la canalización de implementación; descargado de la interfaz de usuario para desarrollo local |

## Introducción

El repositorio vinculado necesita la estructura del proyecto para poder escribir controladores. Clone la **[plantilla de aplicaciones LLM de Adobe](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)** para comenzar con un punto de partida vacío.

Inserte el contenido en el repositorio que vinculó durante la creación de la aplicación (por ejemplo, `your-org/your-repo`).

Una vez que el código esté listo, ejecute:

```bash
npm install
```

Esto instala todas las dependencias, incluido [`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime), el motor en tiempo de ejecución que administra la comunicación del protocolo MCP, la detección de acciones y el enrutamiento de solicitudes. No interactúa directamente con el motor en tiempo de ejecución; `entry.js` lo consume en tiempo de compilación.

>[!TIP]
>
>Si usa [Código Claude](https://claude.ai/code) o [Cursor](https://cursor.com), la plantilla incluye una habilidad Claude lista para usar en `.claude/skills/llm-apps-action-author/`. Puede andamiar nuevas acciones, generar archivos de prueba, validar formas de controlador y guiarle a través del contrato de controlador, todo desde el editor. Para usarlo, pídale a Claude que *&quot;agregue una acción llamada search-products&quot;* y seguirá automáticamente las convenciones de proyecto correctas.

## Contrato de controlador

Un controlador es un solo archivo en `actions/<name>/index.js` que exporta una función asincrónica:

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

La función recibe los argumentos de entrada de la acción como un objeto sin formato: estos son los parámetros definidos en el cuadro de diálogo Crear acción. El servidor los valida con el esquema de entrada antes de llamar al controlador.

### `content` (obligatorio)

Una matriz de partes de contenido enviadas a los hosts de LLM y de solo texto. Esto es lo que la plataforma LLM lee para formular su respuesta.

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

Devolver siempre `content`: es la reserva universal para cualquier host.

### `structuredContent`

Objeto JavaScript sin formato enviado al widget. Estos datos tienen **coste cero del token**; los consume el bloque de widgets de EDS para representar una interfaz de usuario enriquecida como un carrusel de productos o un mapa.

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

La estructura depende de usted, debe coincidir con lo que el bloque de widgets EDS espera a través de `bridge.toolResult`.

>[!IMPORTANT]
>
>`structuredContent` debe ser un objeto sin formato, no una matriz sin formato.

### `_meta` (opcional)

Metadatos adicionales enviados junto con el resultado. La clave `openai/widgetDescription` indica a la plataforma LLM cómo presentar el widget:

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## Ejemplo: controlador de productos de búsqueda

Este es un ejemplo de controlador `search-products`. Acepta un filtro `category` opcional y un texto libre `query`, busca en un catálogo de productos y devuelve un resumen de texto para LLM y datos estructurados para el carrusel de widgets.

>[!NOTE]
>
>Este ejemplo utiliza una matriz de productos codificada para simplificar. En una aplicación real, normalmente llamaría a su propia API de producto o base de datos para recuperar resultados de forma dinámica.

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**Qué sucede durante la ejecución:**

1. Un usuario pregunta a la plataforma LLM *&quot;Muéstreme sus productos de café&quot;.*
2. La plataforma LLM coincide con la intención de *Buscar productos* y extrae `category`.
3. El servidor MCP llama a su controlador con `{ category: 'bagged-coffee' }`.
4. El controlador filtra el catálogo y devuelve `content` (resumen de texto para el LLM) + `structuredContent` (matriz de productos para el widget).
5. La plataforma LLM muestra la respuesta del texto y pasa los datos estructurados al widget EDS, que procesa un carrusel de productos.

## ¿Qué sucede si falta el controlador?

Si ha definido una acción en la interfaz de usuario pero aún no ha creado el archivo del controlador, la acción se seguirá registrando en el momento de la implementación. Las invocaciones utilizarán un controlador de código auxiliar predeterminado que devuelve contenido vacío hasta que se agregue el código real. Esto significa que puede definir primero todas las acciones en la interfaz de usuario e implementarlas gradualmente.

