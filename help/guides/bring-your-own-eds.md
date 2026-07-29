---
title: Traer su propio proyecto de Edge Delivery Services
description: Conecte un proyecto de servicios de entrega perimetral de Adobe existente a una acción de aplicaciones LLM de Adobe.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 3%

---


# Traer su propio proyecto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta guía cuando ya tenga un proyecto de Edge Delivery Services (EDS) o cuando haya creado una aplicación sin el agente de integración.

Si el agente de incorporación creó su widget, siga [Personalizar un widget generado](/help/guides/widgets.md) en su lugar. El proyecto generado ya incluye los archivos SDK, el bloque, el contenido y la configuración de acción que se describen aquí.

**Recorrido:** Prepare el proyecto EDS → instale el → de SDK para generar y publicar el bloque → configurar la acción → implementar y probar.

## Antes de empezar

Necesita:

- Hay instalado un repositorio EDS con [AEM Code Sync](https://github.com/apps/aem-code-sync).
- Permiso para agregar dependencias y crear bloques en ese repositorio.
- Permiso para configurar encabezados de respuesta para el sitio EDS.
- Una acción en [!DNL LLM Apps] con un controlador que devuelve `structuredContent`.

## Instalación del SDK de aplicaciones LLM

Desde la raíz del proyecto EDS:

```bash
npm install @adobe/llmapps-sdk
```

El paquete copia el punto de entrada del widget y la implementación del puente en el proyecto:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

La URL del script usada por la acción apunta a `scripts/aem-embed.js`.

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
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

Utilice API de DOM que codifican valores de texto. No concatene datos externos en HTML.

## Crear y publicar la página del widget

Cree una página EDS para el widget y añada el bloque a esa página. Publique la página.

La URL de la página activa se convierte en la URL del widget de la acción:

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

La ruta de la página no necesita coincidir con el nombre de la acción, pero una convención uniforme facilita el mantenimiento del proyecto.

## Configuración de CORS

El widget carga la página EDS además de scripts, estilos, bloques y medios en los orígenes. Configure el encabezado para el sitio de EDS:

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

Utilice el origen de host específico requerido por la plataforma LLM admitida. Use `*` solo cuando el widget sea intencionalmente público, no utilice solicitudes de origen cruzado con credenciales y los requisitos de seguridad lo permitan.

Para obtener detalles de configuración de EDS, consulte [Servicio de configuración](https://aem.live/docs/config-service-setup).

## Configurar la acción

En [!DNL LLM Apps], abra la acción y seleccione **[!UICONTROL Metadatos de widget]**.

Escriba

- **[!UICONTROL URL de script]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL URL del widget]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

Configure los dominios CSP y los permisos de explorador con el menor privilegio. Añada solo los orígenes y las capacidades que requiere el widget.

Para ver las definiciones de los campos, consulte [Campos de acción y widget](/help/reference/reference-docs.md).

## Prueba de la integración

1. Previsualice la página EDS directamente y compruebe su reserva de datos de ejemplo.
2. Pruebe el controlador localmente y compare su `structuredContent` con la forma esperada por el bloque.
3. Implemente la aplicación para el ensayo.
4. Invoque la acción desde [!DNL ChatGPT].
5. Compruebe los estados de carga, éxito, vacío y error.

Si la página funciona directamente pero no en la plataforma LLM, compruebe CORS, CSP, HTTPS y la forma `structuredContent`. Ver [Solución de problemas](/help/reference/troubleshooting.md).
