---
title: Crear una acción desde cero
description: Defina metadatos de acción, implemente su controlador, conecte un widget EDS, pruébelo e impleméntelo con aplicaciones LLM de Adobe.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1137'
ht-degree: 0%

---


# Crear una acción desde cero {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Esta guía supone una familiaridad básica con Adobe Edge Delivery Services (EDS). Si es nuevo en EDS, lea primero el [tutorial para desarrolladores de EDS](https://www.aem.live/developer/tutorial) y [Exploración de bloques](https://www.aem.live/docs/exploring-blocks) para conocer los aspectos básicos — bloques, la función `decorate` y la estructura del proyecto EDS — antes de conectar un widget.

Utilice esta guía para añadir una capacidad que la plataforma no ha creado. Definirá la acción en [!DNL LLM Apps], escribirá su controlador en el repositorio vinculado, agregará un widget si es necesario, lo probará e implementará.

**Recorrido:** Planifique la acción → crear sus metadatos → escribir el controlador → conectar el widget → probar localmente → implementar y probar el complemento.

Para tu primera aplicación, comienza con [Crea tu primera aplicación automáticamente](/help/guides/create-app.md).

## Antes de empezar

Necesita:

- Una aplicación LLM existente.
- Un repositorio de controladores vinculado.
- El repositorio clonado localmente con sus dependencias instaladas.
- Un proyecto EDS si la acción muestra un widget.
- Una API o fuente de datos clara para los resultados de producción.

## Planificar la acción

Una acción debe realizar una tarea de borrado de usuario. Antes de abrir la interfaz de usuario de, defina:

- **Intención**: lo que el usuario intenta lograr.
- **Descripción**: cuándo la plataforma LLM debe seleccionar esta acción.
- **Entradas**: la información mínima requerida del usuario.
- **Resultado**: el texto y los datos estructurados devueltos por el controlador.
- **Comportamiento**: si la acción lee datos, cambia datos o llama a sistemas externos.
- **Widget**: indica si el resultado necesita una interfaz visual.

Por ejemplo, una acción **Buscar productos** podría usar:

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

Mantenga las tareas relacionadas pero diferentes separadas. La búsqueda de productos y la compra de productos no deben ser una acción única, ya que tienen diferentes entradas, riesgos y requisitos de confirmación.

## Creación de metadatos de acción

Abra la aplicación, seleccione **[!UICONTROL Acciones]** y luego seleccione **[!UICONTROL Crear acción]**.

El editor contiene las fichas **[!UICONTROL Acción]** y **[!UICONTROL Metadatos de widget]**.

### Introducir información básica

![Crear acción — información básica](/help/assets/guide-create-action/action-basic-info.png)

Escriba

- **[!UICONTROL Nombre de acción]**: un nombre corto de tarea, como *Buscar productos*.
- **[!UICONTROL Descripción]** — explica cuándo usar la acción y qué devuelve.

Una descripción útil es específica:

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

Evite descripciones vagas como *Obtiene información del producto*. La plataforma LLM utiliza la descripción para elegir entre acciones.

### Seleccionar anotaciones

Las anotaciones describen el comportamiento de la acción:

- **Sugerencia destructiva**: la acción puede eliminar o cambiar datos de forma permanente.
- **Idempotente (los mismos argumentos = sin efecto adicional)** — repetir la misma solicitud tiene el mismo efecto.
- **Open world hint**: la acción se comunica con sistemas externos.
- **Sugerencia de solo lectura**: la acción no cambia los datos.

Seleccione solo las anotaciones que sean verdaderas. Por ejemplo, la búsqueda de productos suele ser de solo lectura, idempotente y de mundo abierto.

### Añadir metadatos de OpenAI

Introduzca los mensajes cortos que se muestran mientras se ejecuta la acción y después de que se complete:

```text
Invoking: Searching products...
Invoked: Products found
```

Para acciones con widgets, agregue **[!UICONTROL Descripción del widget]**. Esto es diferente a la descripción de la acción:

- **Descripción de la acción** ayuda al modelo a decidir cuándo invocar la acción.
- **La descripción del widget** se asigna a `_meta["openai/widgetDescription"]` y resume lo que muestra el componente procesado, lo que reduce la narración repetida.

[!DNL LLM Apps] aplica esto como metadatos de componente. No lo devuelva desde el controlador.

### Configuración de visibilidad

- **[!UICONTROL Exponer al modelo de IA]** permite que el modelo seleccione la acción.
- **[!UICONTROL Mostrar como widget en la superficie de la aplicación]** muestra el widget configurado.

Deshabilite la visibilidad del widget cuando la acción devuelva solo texto.

### Añadir parámetros de entrada

Agregue un parámetro para cada valor que acepte el controlador. Todos los parámetros necesitan:

- **Nombre**: la clave recibida por el controlador.
- **Tipo** — Cadena, Número, Entero o Booleano.
- **Descripción**: cómo el modelo debe extraer el valor.
- **Requerido**: si la acción se puede ejecutar sin ella.

Para **Buscar productos**:

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

Utilice nombres de parámetros estables. Para cambiar un nombre también es necesario cambiar el controlador y sus pruebas.

### Configurar Analytics

Habilite **[!UICONTROL Recopilar la intención del usuario]** cuando quiera que Analytics incluya un resumen de la conversación que provocó la acción.

![Crear acción — análisis por intención de usuario](/help/assets/guide-create-action/action-analytics-user-intent.png)

Para ver las definiciones de campo completas, consulte [Campos de acción y widget](/help/reference/reference-docs.md).

## Configuración del widget

Omita esta sección para una acción de solo texto.

Abrir **[!UICONTROL metadatos de widget]**.

![Crear acción — metadatos de widget](/help/assets/guide-create-action/widget-metadata.png)

Configuración de:

- **Tipo** — seleccione EDS.
- **Dominio de widget**: el origen EDS que aloja el widget.
- **Prefiere borde** — solicita un contenedor con borde en el host.
- **URL de script**: el punto de entrada del widget EDS.
- **URL del widget**: la página EDS publicada para esta acción.

Las direcciones URL habituales son:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Conceda solo los permisos de explorador y los dominios CSP necesarios.

![Crear acción — permisos y CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Si el proyecto EDS o la página del widget aún no existen, complete [Traiga su propio proyecto EDS](/help/guides/bring-your-own-eds.md) y vuelva a la acción.

## Guardar la acción

Seleccione **[!UICONTROL Crear nueva acción]**. La acción aparece en la página Acciones con el distintivo **No implementado**.

En este punto, los metadatos existen, pero la acción todavía necesita un controlador.

## Implementar el controlador

Clone el repositorio de controladores vinculado e instale sus dependencias:

```bash
npm install
```

Crear:

```text
actions/
└── search-products/
    └── index.js
```

El nombre de la carpeta debe coincidir con el identificador de código de la acción que se muestra en el editor de acciones.

Para obtener el contrato de resultados completo y la relación controlador-widget, consulte [Personalizar un controlador generado](/help/guides/customize-handler.md).

### Contrato de controlador

Exporte una función asíncrona:

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

El controlador recibe los parámetros definidos en la interfaz de usuario.

### Devolver `content`

`content` es la reserva de texto leída por la plataforma LLM:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Siempre devuelve `content` útil, incluso cuando la acción tiene un widget.

### Devolver `structuredContent`

`structuredContent` es un objeto sin formato consumido por el widget:

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

La forma debe coincidir con lo que lee el bloque EDS de `bridge.toolResult`.

### Conexión de una API

Mantenga el acceso a la API protegida en el controlador del lado del servidor. Cargue la configuración desde el entorno de tiempo de ejecución y utilice un origen HTTPS fijo.

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

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
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

No coloque credenciales de API en el código fuente, metadatos de acción, JavaScript de widget, registros o errores de cara al usuario.

Para el código de producción, valide la respuesta ascendente completa antes de asignar los campos aprobados a `structuredContent`.

## Agregar pruebas de controlador

Cree la prueba coincidente:

```text
test/
└── actions/
    └── search-products.test.js
```

Probar al menos:

- Entrada válida.
- Falta entrada o no es válida.
- Resultados vacíos.
- Tiempo de espera o error de API.
- Datos de API mal formados.
- La forma `structuredContent` que esperaba el widget.

Ejecutar:

```bash
npm test
```

Para obtener información sobre el diseño del proyecto y las pruebas de MCP local, vea [Desarrollo y prueba de controladores locales](/help/reference/development.md).

## Prueba de la acción localmente

Ejecutar:

```bash
npm run dev:local
```

Sin un `actions.json` local, el servidor detecta el controlador con metadatos mínimos y sin validación de esquema de entrada.

Use el Inspector MCP o `curl` para:

1. Enumerar las acciones registradas.
2. Llame a la nueva acción con argumentos representativos.
3. Verificar `content` y `structuredContent`.
4. Prueba de solicitudes no válidas y vacías.

## Conexión y prueba del widget

Si la acción tiene un widget:

1. Haga que el widget lea `structuredContent` del controlador.
2. Procese valores externos con API DOM seguras como `textContent`.
3. Añada los estados de carga, vacío y error.
4. Previsualice la página EDS localmente.
5. Compruebe las URL de CSP, CORS y widget.

Consulte [Traer su propio proyecto EDS](/help/guides/bring-your-own-eds.md).

## Implementación y prueba

1. Confirme e inserte los cambios del controlador y el widget.
2. [Implementar la aplicación](/help/guides/deploy-your-app.md) en Fase.
3. [Probar el complemento ChatGPT](/help/guides/test-in-chatgpt.md).
4. Verify solicita que debe y no debe invocar la acción.
5. Una vez que Fase se haya realizado correctamente, implemente en Producción.

Si los metadatos existen sin un controlador coincidente, la implementación registra la acción con un código auxiliar predeterminado. Agregue el controlador antes de poner la acción a disposición de los usuarios.
- [Guía: Configurar el widget (EDS)](/help/guides/widgets.md)
