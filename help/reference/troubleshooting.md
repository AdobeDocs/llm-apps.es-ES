---
title: Solucionar problemas de aplicaciones LLM de Adobe
description: Resuelva problemas comunes de infraestructura, generación de acciones, controlador, widget, implementación, autenticación y conexión a la plataforma LLM.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1350'
ht-degree: 0%
---

# Resolución de problemas {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Comience con el síntoma observable. No comparta credenciales, tokens, direcciones URL de MCP privadas ni resultados de controladores confidenciales durante la solución de problemas.

## Creación de aplicaciones y generación de acciones

| Síntoma | Qué debe probar |
|---------|-------------|
| La configuración de la infraestructura gestionada de Adobe falla | Lea el error en **Error en la instalación** y después seleccione **Reintentar**. Si la página no puede comprobar el progreso, seleccione **Comprobar de nuevo**; la instalación continúa en segundo plano |
| No hay ningún programa de AEM disponible | Solicite a su administrador acceso al programa. Si no se pudo cargar, seleccione **Reintentar cargar los programas** |
| Los nuevos repositorios no aparecen | Use **Conectar otro repositorio** para conceder acceso a la aplicación GitHub de aplicaciones LLM de Adobe, volver al asistente y seleccionar **Actualizar organizaciones y repositorios** |
| Error de validación del repositorio de acciones o widgets | Utilice un proyecto existente compatible o un repositorio vacío. Use repositorios separados para acciones y widgets, luego seleccione **Validar repositorio** nuevamente |
| No se ha detectado la sincronización de código AEM | Instale AEM Code Sync para el repositorio de widgets, añádase como administrador del sitio en su página de confirmación, vuelva al asistente y seleccione **Validar la instalación de AEM Code Sync** |
| La creación de la aplicación no generó acciones | La creación de aplicaciones solo configura la infraestructura. En la página de la aplicación, selecciona **Generar acciones automáticamente**, ingresa **Tu sitio web** y selecciona **Generar mis acciones**. Ver [Crear acciones](/help/guides/creating-actions.md) |
| Las acciones siguen generándose | La interfaz de usuario calcula hasta 30 minutos para la generación. Siga el progreso en la página Acciones o abandone la página y vuelva más tarde |
| Error en informes de generación de acciones | Confirme que el sitio web es público a través de HTTPS y que el acceso al repositorio sigue siendo válido para sus propios repositorios. Póngase en contacto con el equipo de Beta y envíele el mensaje de error visible si el problema persiste |

## Acciones y controladores

| Síntoma | Qué debe probar |
|---------|-------------|
| No se invoca la acción | Habilite el complemento o conector de la aplicación en la conversación. Confirme **Exponer al modelo de IA** en la configuración de la acción y el conmutador **Visibilidad** de la acción en la página de la aplicación están activados. Mejore la descripción y vuelva a implementar los cambios de metadatos |
| Respuesta vacía o de error | Ejecute `npm test` y, a continuación, llame al controlador con el Inspector de MCP o `curl`. Ver [desarrollo y prueba de controladores locales](/help/reference/development.md) |
| El controlador funciona localmente, pero no después de la implementación | Compruebe la última confirmación insertada, confirme la configuración del tiempo de ejecución y verifique que el identificador del código de acción coincida con `actions/<code-identifier>/index.js` |
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

## Autenticación {#authentication}

Se aplica cuando **[!UICONTROL Habilitar autenticación]** está activado. Ver [Autenticar usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md).

| Síntoma | Qué debe probar |
|---------|-------------|
| Las acciones siguen siendo públicas después de guardar la configuración | Vuelva a implementar la aplicación en ese entorno. Los cambios de autenticación se aplicarán en la siguiente implementación |
| La configuración parece incorrecta después de cambiar de entorno | Confirme que el selector **[!UICONTROL Workspace]** muestra el entorno que pretendía. **[!UICONTROL Fase]** y **[!UICONTROL Producción]** se configuran de forma independiente |
| El inicio de sesión no se inicia | Confirme que ha implementado la aplicación después de habilitar la autenticación y que la acción usa **[!UICONTROL Obligatorio]**. Una acción **[!UICONTROL Optional]** solo pregunta si su controlador solicita el inicio de sesión |
| La plataforma envía al usuario a una página de inicio de sesión incorrecta | Compruebe que **[!UICONTROL Issuer]** coincide exactamente con la dirección URL del emisor del proveedor de identidad y que se puede acceder a ella a través de HTTPS público |
| El inicio de sesión se ha realizado correctamente, pero todas las llamadas se rechazan | Confirme que el proveedor de identidad emite tokens cuya audiencia es la URL del servidor MCP de la aplicación para ese entorno y que el token es un JWT firmado con un algoritmo asimétrico. Ver [requisitos de token](/help/reference/authentication-reference.md#token-requirements) |
| El proveedor de identidad nunca recibe tráfico | Los extremos de descubrimiento, autorización y token de su proveedor deben estar accesibles a través de HTTPS público. Compruebe si hay un cortafuegos, una WAF o una lista de permitidos IP delante; su aplicación puede estar accesible mientras que su proveedor no lo esté |
| El proveedor de identidad se niega a emitir un token para la audiencia solicitada | Algunos proveedores solo emiten tokens para un recurso registrado con ellos. Confirme que la URL del servidor MCP de la aplicación está registrada como identificador de recurso en su proveedor de identidad. |
| No se solicita un ámbito agregado recientemente al iniciar sesión | Las plataformas LLM almacenan en caché los metadatos publicados de la aplicación durante unos minutos. Espere y vuelva a intentar iniciar sesión |
| Se le pide al usuario que vuelva a iniciar sesión para un ámbito | La acción requiere un ámbito que el token no lleva. Agregue el ámbito a la concesión del cliente en su proveedor de identidad o elimínelo de la acción |
| El usuario recibe varias solicitudes de inicio de sesión | Controlador que devuelve `extra.challengeAuth()` en cada llamada sin comprobar la identidad de la persona que llama solicita volver a iniciar sesión después de la autenticación. Solicitar autenticación solo cuando falte la identidad que necesita la acción |
| El inicio de sesión se rechaza antes de que el usuario llegue a su proveedor de identidad | El URI de redireccionamiento enviado por la plataforma LLM no está registrado en el servidor de autorización. Algunas plataformas emiten una diferente para cada conector, por lo que debe registrar el valor que se muestra en la pantalla de configuración del conector |
| Guardar está bloqueado con un mensaje de ámbito no admitido | Agregue el ámbito a **[!UICONTROL Ámbitos admitidos]** o elimínelo de la acción que lo requiera |
| Una acción pública sigue solicitando el inicio de sesión en [!DNL Claude] | Compruebe que el conector use **[!UICONTROL Iniciar sesión cuando sea necesario]**, no **[!UICONTROL Iniciar sesión ahora]**. Para una aplicación implementada más antigua, actualice el tiempo de ejecución y vuelva a implementarla. Una aplicación mixta en tiempo de ejecución actual deja llamadas anónimas a las acciones públicas |
| Una acción **[!UICONTROL Optional]** devuelve el texto de inicio de sesión, pero no el mensaje de inicio de sesión Claude | `extra.challengeAuth()` es un desafío de resultados de herramientas de ChatGPT. Para una puerta de inicio de sesión portátil, use una acción **[!UICONTROL Requerida]** para que el motor en tiempo de ejecución devuelva el desafío HTTP que Claude espera |
| La aplicación solicita el inicio de sesión aunque todas las acciones sean **[!UICONTROL Ninguna]** | Desactive **[!UICONTROL Habilitar autenticación]** e implemente. Mientras está activada, la aplicación anuncia un servidor de autorización incluso cuando no se activa ninguna acción |

No pegue tokens de acceso, conjuntos de reclamaciones o secretos de cliente del proveedor de identidad en una solicitud de asistencia.

## Complementos de ChatGPT

| Síntoma | Qué debe probar |
|---------|-------------|
| El complemento no aparece | Abra [chatgpt.com/plugins](https://chatgpt.com/plugins), confirme que su cuenta o área de trabajo permite servidores MCP personalizados e instale o conecte el complemento resultante |
| Error al crear el complemento | Vuelva a copiar la URL completa del servidor HTTPS MCP desde **Probar la aplicación** y elija el método de autenticación que coincida con la aplicación. Use **Sin autenticación** solo para una aplicación pública, no como solución para un error de OAuth |
| El complemento se conecta pero no puede invocar acciones | Confirme que el complemento está adjunto al chat, que las acciones se exponen al modelo y que se implementa la versión más reciente |
| El complemento utiliza un entorno incorrecto | Edite o vuelva a crear el complemento con la URL del servidor MCP de fase o producción deseada |
| El complemento sigue mostrando metadatos de acciones antiguas | Implemente los cambios, seleccione **Actualizar** en la conexión ChatGPT y, a continuación, inicie una nueva conversación |

Si el problema persiste, registre el nombre de la aplicación, el entorno, el paso en el que se ha producido un error, la hora y el mensaje de error visible antes de ponerse en contacto con el equipo de Beta. No incluya secretos ni datos confidenciales de clientes.
