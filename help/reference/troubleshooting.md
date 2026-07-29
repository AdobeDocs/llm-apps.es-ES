---
title: Solucionar problemas de aplicaciones LLM de Adobe
description: Resuelva problemas comunes de repositorio, incorporación, controlador, widget, implementación y complemento de ChatGPT.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '632'
ht-degree: 0%

---


# Resolución de problemas {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Comience con el síntoma que pueda ver. No comparta credenciales, tokens, direcciones URL de MCP privadas ni resultados de controladores confidenciales durante la solución de problemas.

## Creación e incorporación de aplicaciones

| Síntoma | Qué debe probar |
|---------|-------------|
| Los nuevos repositorios no aparecen | Seleccione **Administrar repositorios en GitHub**, conceda acceso a la aplicación GitHub de aplicaciones LLM de Adobe a ambos repositorios, vuelva al cuadro de diálogo y actualice las listas |
| El repositorio EDS requiere la sincronización de código AEM | Instale la sincronización de código de AEM para el repositorio EDS y vuelva al cuadro de diálogo Crear aplicación LLM |
| La validación de EDS indica que no es administrador | Seleccione **Abrir el administrador de AEM Live**, añádase como administrador del sitio de EDS y actualice el repositorio |
| La incorporación sigue generándose | Se tardan aproximadamente 15 minutos. Puede salir de la página y volver más tarde |
| Error de informes de incorporación | Confirme que ambos repositorios son accesibles y que el sitio web es público a través de HTTPS y, a continuación, póngase en contacto con el equipo de Beta con el mensaje de error visible |

## Acciones y controladores

| Síntoma | Qué debe probar |
|---------|-------------|
| No se invoca la acción | Adjunte el complemento ChatGPT, confirme que **Exponer al modelo de IA** está habilitado, mejore la descripción de la acción y vuelva a implementar los cambios de metadatos |
| Respuesta vacía o de error | Ejecute `npm test` y, a continuación, llame al controlador con el Inspector de MCP o `curl`. Ver [desarrollo y prueba de controladores locales](/help/reference/development.md) |
| El controlador funciona localmente, pero no después de la implementación | Confirme que se insertó la última confirmación, que la configuración de tiempo de ejecución está presente y que el identificador del código de acción coincide con `actions/<code-identifier>/index.js` |
| La acción generada no se puede marcar como revisada | Confirme la generación del controlador y el widget correctamente. Compruebe las solicitudes de extracción generadas en caso de conflictos de combinación, vuelva a cargar la acción y seleccione **Marcar como revisado** de nuevo |

## Widgets

| Síntoma | Qué debe probar |
|---------|-------------|
| El widget no se procesa | Compruebe la URL del script, la URL del widget, HTTPS, la publicación EDS, los dominios CSP y los encabezados CORS |
| El widget se procesa pero no muestra datos | Llame al controlador con el Inspector MCP y compare su forma `structuredContent` con los campos leídos de `bridge.toolResult` |
| El widget funciona en la vista previa directa pero no en ChatGPT | La vista previa directa puede utilizar datos de ejemplo. Pruebe el resultado del controlador implementado y compruebe que CORS y CSP permiten el origen EDS |
| La solicitud del explorador está bloqueada | Añada solo el origen requerido al campo CSP correcto y vuelva a implementar |
| El Editor de encabezados HTTP no puede guardar la configuración | Use el [Servicio de configuración de AEM](https://aem.live/docs/config-service-setup) o pídale al administrador de EDS que inicialice la configuración de los encabezados del sitio |

No registre valores `bridge.toolResult` completos cuando puedan contener datos personales o confidenciales.

## Implementación

| Síntoma | Qué debe probar |
|---------|-------------|
| Error de implementación durante **Preparación** | Compruebe que el repositorio del controlador está vinculado y que el acceso a Adobe Developer Console sigue siendo válido |
| La implementación falla durante **Generar aplicación** | Ejecutar `npm install`, `npm test` y `npm run build` localmente. Corrija los errores de dependencia, sintaxis o prueba e inserte los cambios |
| La implementación se realiza correctamente, pero faltan cambios | Confirme que la confirmación esperada se insertó y se volvió a implementar en el mismo entorno |
| La acción permanece **No implementada** | Implementar de nuevo después de revisar la acción o cambiar sus metadatos |

## Complementos de ChatGPT

| Síntoma | Qué debe probar |
|---------|-------------|
| El complemento no aparece | Habilite el modo de desarrollador, abra [chatgpt.com/plugins](https://chatgpt.com/plugins), compruebe que el complemento existe y seleccione **Conectar** |
| Error al crear el complemento | Confirme que el modo de desarrollador está habilitado, copie de nuevo la URL del servidor MCP de **Pruebe la aplicación** y use la **URL del servidor** con **Sin autenticación** |
| El complemento se conecta pero no puede invocar acciones | Confirme que el complemento está adjunto al chat, que las acciones se exponen al modelo y que se implementa la versión más reciente |
| El complemento utiliza un entorno incorrecto | Edite o vuelva a crear el complemento con la URL del servidor MCP de fase o producción deseada |

Si el problema persiste, registre el nombre de la aplicación, el entorno, el paso en el que se ha producido un error, la hora y el mensaje de error visible antes de ponerse en contacto con el equipo de Beta. No incluya secretos ni datos confidenciales de clientes.
