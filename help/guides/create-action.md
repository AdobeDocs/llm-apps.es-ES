---
title: Crear una acción desde cero
description: Defina metadatos de acción, implemente su controlador, conecte un widget EDS, pruébelo e impleméntelo con aplicaciones LLM de Adobe.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '1476'
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

Añada una capacidad que la plataforma no haya creado. Defina la acción en [!DNL LLM Apps], escriba su controlador en el repositorio vinculado y agregue un widget si es necesario.

**En esta guía:** Aprenda a definir una acción, implementar su controlador y conectar un widget opcional. Pruebe la acción localmente, implemente la aplicación e intente la acción en una plataforma LLM compatible.

Para tu primera aplicación, comienza con [Configuración de infraestructura](/help/guides/create-app.md) y luego [Crea acciones](/help/guides/creating-actions.md). La guía Crear acciones explica la generación automática a partir de un sitio web y la creación manual.

## Antes de empezar

Necesita:

- Una aplicación LLM existente.
- Un repositorio de controladores vinculado.
- Un clon local del repositorio con sus dependencias instaladas.
- Un proyecto EDS si la acción muestra un widget.
- Una API o fuente de datos clara para los resultados de producción.

## Planificar la acción

Diseñe cada acción en torno a una tarea de usuario clara. Antes de abrir la interfaz de usuario de, defina:

- **Intención**: lo que el usuario intenta lograr.
- **Descripción** — cuándo seleccionar la acción.
- **Entradas**: la información mínima requerida del usuario.
- **Resultado**: el texto y los datos estructurados devueltos por el controlador.
- **Comportamiento**: si la acción lee datos, cambia datos o llama a sistemas externos.
- **Widget**: indica si el resultado necesita una interfaz visual.

Por ejemplo, use este plan para una acción de **Buscar productos**:

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

Mantenga las tareas relacionadas pero diferentes separadas. Utilice acciones independientes para la búsqueda de productos y la compra de productos porque las tareas tienen diferentes entradas, riesgos y requisitos de confirmación.

## Creación de metadatos de acción

Abra la aplicación, seleccione **[!UICONTROL Acciones]** y luego seleccione **[!UICONTROL Crear acción]**.

El cuadro de diálogo de creación contiene las fichas **[!UICONTROL Acción]** y **[!UICONTROL Metadatos de widget]**. Expanda cada sección para configurar sus campos.

### Introducir información básica

![Crear acción — información básica](/help/assets/guide-create-action/action-basic-info.png)

Escriba

- **[!UICONTROL Nombre de acción]**: un nombre corto de tarea, como *Buscar productos*.
- **[!UICONTROL Descripción]** — explica cuándo usar la acción y qué devuelve.

El cuadro de diálogo deriva un **[!UICONTROL identificador de código]** del nombre, como `search-products`. El identificador se fija después de la creación y debe ser único dentro de la aplicación. Utilice el identificador exacto de la carpeta de controladores.

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

Para acciones con widgets, agregue **[!UICONTROL Descripción del widget]**. La descripción del widget difiere de la descripción de la acción:

- **Descripción de la acción** ayuda al modelo a decidir cuándo invocar la acción.
- **La descripción del widget** se asigna a `_meta["openai/widgetDescription"]` y resume lo que muestra el componente procesado, lo que reduce la narración repetida.

[!DNL LLM Apps] aplica la descripción del widget como metadatos de componente. No devuelva la descripción desde el controlador.

### Configuración de visibilidad

- **[!UICONTROL Exponer al modelo de IA]** permite que el modelo seleccione la acción.
- **[!UICONTROL Mostrar como widget en la superficie de la aplicación]** muestra el widget configurado.

Para una acción de solo texto, también selecciona **[!UICONTROL Sin widget]** en **[!UICONTROL Metadatos de widget]** > **[!UICONTROL Tipo]**. Los controles de visibilidad no eligen el tipo de widget de la acción.

### Añadir parámetros de entrada

Agregue un parámetro para cada valor que acepte el controlador. Todos los parámetros necesitan:

- **Nombre**: la clave que recibe el controlador.
- **Tipo** — Cadena, Número, Entero o Booleano.
- **Descripción**: cómo extraer el valor.
- **Requerido**: si la acción requiere el valor.

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

### Configurar Analytics {#configure-analytics}

Habilite **[!UICONTROL Recopilar la intención del usuario]** cuando quiera que Analytics incluya un resumen de la conversación que provocó la acción.

Si configura su propio [!DNL Adobe Analytics] o [!DNL Customer Journey Analytics], la intención del usuario también se enviará allí. Ver [Configurar Adobe Analytics y Customer Journey Analytics](/help/guides/analytics-setup.md).

La recopilación de intención de usuario está habilitada de forma predeterminada. Revise **[!UICONTROL Descripción de la intención del usuario]**, que contiene las instrucciones para solicitar el resumen. Limite las instrucciones a intenciones no confidenciales y anónimas y describa la recopilación de datos en la política de privacidad. La descripción acepta hasta 2000 caracteres.

![Crear acción — análisis por intención de usuario](/help/assets/guide-create-action/action-analytics-user-intent.png)

Para ver las definiciones de campo completas, consulte [Campos de acción y widget](/help/reference/reference-docs.md).

## Configuración del widget

Para una acción de solo texto, abre **[!UICONTROL Metadatos de widget]** y establece **[!UICONTROL Type]** en **[!UICONTROL Ningún widget]**. A continuación, omita los ajustes de widget restantes.

Abrir **[!UICONTROL metadatos de widget]**.

![Crear acción — metadatos de widget](/help/assets/guide-create-action/widget-metadata.png)

Configuración de:

- **[!UICONTROL Tipo]** — selecciona **[!UICONTROL bloque Edge Delivery Services]** para un widget interactivo.
- **[!UICONTROL Dominio de widget]**: el origen de la zona protegida del widget. La interfaz de usuario la identifica como necesaria para el envío de aplicaciones y única por aplicación; no es lo mismo que la URL de la página EDS.
- **Prefiere borde** — solicita un contenedor con borde en el host.

Expanda **[!UICONTROL URL de plantilla]** e introduzca:

- **[!UICONTROL URL de script]**: el punto de entrada del widget EDS.
- **[!UICONTROL URL del widget]**: la página EDS publicada para esta acción.

![Crear nueva acción: URL de scripts y widgets para un widget EDS](/help/assets/guide-create-action/widget-template-urls.png)

Las direcciones URL habituales son:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Conceda solo los permisos de explorador y los dominios CSP necesarios.

Expanda **[!UICONTROL Permisos (específicos de MCP)]** solo si el widget necesita funciones del explorador como la geolocalización. Deje los permisos sin utilizar desactivados.

![Crear nueva acción: permisos opcionales del explorador](/help/assets/guide-create-action/widget-permissions.png)

En **[!UICONTROL Configuración de CSP]**, agregue los orígenes necesarios para los recursos y las solicitudes de red. Es posible que un widget que carga recursos externos no se represente si su CSP está incompleto.

![Crear nueva acción: listas de dominios de la directiva de seguridad de contenido](/help/assets/guide-create-action/widget-csp.png)

Si el proyecto EDS o la página del widget aún no existen, complete [Traiga su propio proyecto EDS](/help/guides/bring-your-own-eds.md) y vuelva a la acción.

## Guardar la acción

Seleccione **[!UICONTROL Crear nueva acción]**. La acción aparece en la página Acciones con el distintivo **No implementado**.

Al guardar, se crean los metadatos, pero la acción sigue necesitando un controlador.

Cuando vuelva a abrir la acción guardada, el editor tiene **[!UICONTROL Detalles del widget LLM]**, **[!UICONTROL Detalles de la acción]** y **[!UICONTROL Analytics]** fichas. Para guardar las ediciones posteriores, seleccione **[!UICONTROL Guardar cambios]**. El cuadro de diálogo de creación y el editor de acciones guardadas tienen diferentes diseños.

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

La plataforma LLM lee `content` como texto de reserva:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Siempre devuelve `content` útil, incluso cuando la acción tiene un widget.

### Devolver `structuredContent`

El widget consume `structuredContent` como objeto sin formato:

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

Mantenga las llamadas de API en el controlador del lado del servidor. Este ejemplo llama a una API de producto pública. Configure el origen HTTPS de la API en una [variable de aplicación](/help/guides/app-variables.md) llamada `PRODUCT_API_ORIGIN`. El ejemplo acepta los parámetros `query` y `category` definidos anteriormente, requiere al menos un parámetro y no requiere credenciales.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '', category = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  const normalizedCategory = typeof category === 'string' ? category.trim() : '';
  if (typeof query !== 'string' || typeof category !== 'string'
      || (!normalizedQuery && !normalizedCategory)
      || normalizedQuery.length > 200 || normalizedCategory.length > 100) {
    return {
      isError: true,
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
  if (!apiOrigin) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(apiOrigin);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  if (normalizedQuery) {
    url.searchParams.set('query', normalizedQuery);
  }
  if (normalizedCategory) {
    url.searchParams.set('category', normalizedCategory);
  }

  const response = await fetch(url, {
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

No coloque credenciales de API en el código fuente, metadatos de acción, JavaScript de widget, registros o errores de cara al usuario. Actualmente, la interfaz de usuario **[!UICONTROL Variables y secretos]** solo admite variables no confidenciales, no secretos. No almacene un token de API en una variable. Si la API ascendente requiere credenciales, organice un mecanismo de credenciales del lado del servidor aprobado antes de conectarlo.

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
3. Pruebe la aplicación en [ChatGPT](/help/guides/test-in-chatgpt.md) o [Claude](/help/guides/test-in-claude.md).
4. Compruebe la selección de acciones con indicadores que coincidan con la acción y con indicadores no relacionados que no la llamen.
5. Una vez que Fase se haya realizado correctamente, implemente en Producción.

Si los metadatos existen sin un controlador coincidente, la implementación registra la acción con un código auxiliar predeterminado. Agregue el controlador antes de poner la acción a disposición de los usuarios.
