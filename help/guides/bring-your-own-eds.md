---
title: Traer su propio proyecto de Edge Delivery Services
description: Conecte un proyecto de servicios de entrega perimetral de Adobe existente a una acción de aplicaciones LLM de Adobe.
hidefromtoc: true
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# Trae tu propio proyecto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta guía para conectar un proyecto de Edge Delivery Services (EDS) existente compatible o para agregar un widget a una acción que haya creado usted mismo. Un proyecto existente es opcional. Si configura su propia infraestructura, la ruta predeterminada comienza con dos repositorios vacíos que la plataforma inicializa durante la [configuración de la infraestructura](/help/guides/create-app.md).

Si la plataforma creó el widget automáticamente, sigue [Personalizar un widget generado](/help/guides/widgets.md). El proyecto generado ya incluye los archivos SDK, el bloque, el contenido y la configuración de acción que se describen aquí.

**En esta guía:** Aprenda a preparar su proyecto EDS, instalar el SDK y crear y publicar un widget. A continuación, conecte el widget a una acción y pruébelo en la aplicación implementada.

## Antes de empezar

Necesita:

- Un repositorio EDS compatible con plantillas de AEM con [AEM Code Sync](https://github.com/apps/aem-code-sync) instalado y un origen de contenido configurado.
- Permiso para agregar dependencias y crear bloques en ese repositorio.
- Permiso para configurar encabezados de respuesta para el sitio EDS.
- Una acción en [!DNL LLM Apps] con un controlador que devuelve `structuredContent`.

Cuando vincula un repositorio existente, la plataforma comprueba `scripts/aem.js`, `blocks/`, `styles/styles.css` y `head.html`. La incrustación también importa `scripts/scripts.js` y llama a su función `decorateMain` exportada para preparar bloques. Mantener intacto el contrato de carga de bloques de EDS. Los archivos necesarios por sí solos no confirman que un proyecto muy personalizado se procese correctamente.

## Instalación del SDK de aplicaciones LLM

Desde la raíz del proyecto EDS:

```bash
npm install @adobe/llmapps-sdk
```

El vínculo posterior a la instalación del paquete copia el punto de entrada del widget, la implementación del puente y las bibliotecas de Adobe de soporte en el proyecto:

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

La dirección URL del script de la acción apunta a `scripts/aem-embed.js`, no a `scripts/llmapps-sdk.js`. Compruebe que la instalación ha copiado los archivos de SDK y, a continuación, confírmelos e insértelos en el proyecto. El vínculo sobrescribe los archivos copiados al instalar o actualizar SDK; no personalice los archivos SDK copiados.

## Creación del bloque de widgets

Cree un bloque para la acción:

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

Exporte la función EDS `decorate` estándar con el puente conectado como segundo argumento:

```javascript
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

Utilice API de DOM que codifican valores de texto. No concatene datos externos en HTML.

El ejemplo espera `structuredContent.records`. Haga coincidir los nombres de propiedad y los campos de registro devueltos por el controlador. Compruebe `bridge.isConnected`, no solo si existe un puente: `toolResult` nunca se resuelve en modo independiente. En la vista previa de aplicaciones LLM, utilice los datos de ejemplo del bloque cuando `bridge.hostContext.preview` indique el modo de vista previa.

## Crear y publicar la página del widget

Cree una página EDS para el widget y añada el bloque a esa página. Publique la página.

La URL de la página se convierte en la URL del widget de la acción. Por ejemplo, la configuración generada por defecto utiliza el origen de previsualización EDS:

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

Un origen activo de EDS como `https://main--<repo>--<owner>.aem.live` también funciona cuando la página y los recursos se publican allí. Utilice el origen del sitio real; los sitios EDS configurados pueden tener nombres de sitio y organización que difieran del repositorio y el propietario de GitHub.

Compruebe que anexar `.plain.html` a la dirección URL del widget devuelve el fragmento de página y que la página contiene el bloque `search-products`. La ruta de la página no necesita coincidir con el identificador de la acción, pero una convención uniforme facilita el mantenimiento del proyecto.

## Configuración de CORS

El widget carga la página EDS, los scripts, los estilos, los bloques y los medios en los orígenes. Configure el encabezado para el sitio de EDS:

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

Utilice el origen real de la zona protegida de widgets que requiere la plataforma LLM admitida, no solo el origen del sitio web de chat. Compruebe CORS en la respuesta `.plain.html` y en JavaScript, estilos, fuentes y medios importados.

La configuración automática de EDS de la plataforma configura `access-control-allow-origin: *` para la entrega de widgets públicos y puede reemplazar un valor existente más estrecho. Utilice esa directiva únicamente para recursos públicos intencionados sin solicitudes de origen cruzado con credenciales y cuando los requisitos de seguridad lo permitan. Si administra un sitio existente restringido, revise su directiva antes de conectarlo; no exponga el contenido privado del sitio para que un widget funcione.

Para obtener detalles de configuración de EDS, consulte [Servicio de configuración](https://aem.live/docs/config-service-setup).

## Configurar la acción

En [!DNL LLM Apps], abra la acción y seleccione **[!UICONTROL Detalles del widget LLM]**. En **[!UICONTROL Información de widget]**, establezca **[!UICONTROL Tipo de widget]** en **[!UICONTROL EDS]** y luego expanda **[!UICONTROL URL de plantilla]**.

Escriba

- **[!UICONTROL URL de script]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL URL del widget]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

Reemplace los ejemplos con el mismo origen del sitio de trabajo y la misma página que verificó anteriormente. Configure los dominios CSP y los permisos de explorador con el menor privilegio. El origen de EDS debe permitirse tanto para las conexiones, como la recuperación de fragmentos de página, como para los recursos, como los scripts de bloque y los estilos. Añada otros orígenes únicamente cuando el widget lo requiera.

Seleccione **[!UICONTROL Guardar cambios]** y vuelva a implementarlos en el entorno que está probando. La configuración del widget guardado no actualiza el servidor MCP en ejecución hasta la implementación.

Para ver las definiciones de los campos, consulte [Campos de acción y widget](/help/reference/reference-docs.md).

## Prueba de la integración

1. Previsualice la página EDS directamente y compruebe su reserva de datos de ejemplo.
2. Pruebe el controlador localmente y compare su `structuredContent` con la forma esperada por el bloque.
3. Implemente el controlador y los metadatos de acción en Fase. La publicación de contenido y código EDS es un paso independiente.
4. Invoque la acción desde una plataforma LLM compatible. Ver [Prueba en ChatGPT](/help/guides/test-in-chatgpt.md) o [Prueba en Claude](/help/guides/test-in-claude.md).
5. Compruebe los estados de carga, éxito, vacío y error.

Si la página funciona directamente pero no en la plataforma LLM, compruebe CORS, CSP, HTTPS y la forma `structuredContent`. Ver [Solución de problemas](/help/reference/troubleshooting.md).
