---
title: Solución de problemas para aplicaciones LLM de Adobe
description: Soluciones para problemas comunes al crear, implementar y probar aplicaciones LLM de Adobe.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '439'
ht-degree: 0%

---


# Resolución de problemas {#troubleshooting}

>[!IMPORTANT]
>
>**Descargo de responsabilidad:** Esta es una versión beta de [!DNL LLM Apps]. Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final de la aplicación o del producto.

## Problemas comunes

| Síntoma | Posible causa | Qué debe probar |
|---------|----------------|-------------|
| La aplicación no aparece en la plataforma LLM | La suscripción a la plataforma LLM no admite aplicaciones MCP personalizadas o el modo de desarrollador no está habilitado | Verifique que su plan admita aplicaciones MCP personalizadas. Habilitar el modo de desarrollador en **Configuración → aplicaciones → Configuración avanzada** |
| Error &quot;No se pudo conectar&quot; en la plataforma LLM | La URL del servidor MCP es incorrecta o la implementación ha fallado | Compruebe la URL desde la página Detalles de la aplicación. Compruebe si hay errores en el historial de implementación |
| No se invoca la acción | La plataforma LLM no pudo hacer coincidir la pregunta del usuario con su acción | Use `@YourApp` para invocarlo explícitamente. Mejore la descripción de la acción para ayudar al modelo a coincidir con la intención |
| El widget no se procesa | Las direcciones URL de los widgets EDS o los dominios CSP están mal configurados | Compruebe la URL de script y la URL de incrustación de widget en el cuadro de diálogo Crear acción. Compruebe que el recurso CSP y los dominios de conexión incluyen su origen EDS |
| Respuesta vacía o de error | El controlador tiene un error o falta | Realice la prueba localmente con `npm start` primero. Ver [desarrollo local](/help/reference/development.md#local-development) |
| El widget se carga pero no muestra datos | La forma `structuredContent` no coincide con lo que espera el bloque | Registre `bridge.toolResult` en la función `decorate` del bloque y compárelo con la salida del controlador |
| La implementación falla en &quot;Clonar y generar&quot; | `npm install` o error de compilación del Webpack en su repositorio | Ejecute `npm install && npm run build` localmente para reproducir el error |
| La implementación falla en &quot;Recopilar credenciales&quot; | Repositorio no vinculado o proyecto de Developer Console mal configurado | Compruebe que el repositorio está vinculado en la página de configuración de los detalles de la aplicación |
| Error de CORS al cargar el widget | Faltan `access-control-allow-origin` encabezados en el sitio EDS | Configurar encabezados CORS mediante `admin.hlx.page` |
| El editor de encabezados HTTP devuelve `404 Error updating config: config not found` al guardar los encabezados CORS | Falta una sección `headers` en la configuración del sitio | Consulte [Inicializar la sección de encabezados de configuración del sitio EDS](#initialize-the-eds-site-config-headers-section) a continuación |
| El widget se procesa en la vista previa pero no en la plataforma LLM | El bloque vuelve a los datos de muestra en el modo de vista previa, pero falla con los datos activos | Probar con `structuredContent` real utilizando el Inspector MCP o curl |

## Inicialice la sección Encabezados de configuración del sitio de EDS

Si el Editor de encabezados HTTP devuelve `404 Error updating config: config not found`, a la configuración del sitio le falta una sección `headers`. Arreglarlo manualmente:

1. Vaya a [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html), ingrese su organización y sitio y haga clic en **[!UICONTROL Buscar]**.
2. Abra DevTools del explorador (pestaña Red) y copie el valor del encabezado `x-auth-token` de la solicitud de recuperación.
3. Recupere la configuración actual del sitio:

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. Abra `config.json` y agregue `"headers": {}` al objeto JSON.
5. VUELVA A PUBLICAR la configuración actualizada:

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. Vuelva a cargar el Editor de encabezados y guarde el encabezado `Access-Control-Allow-Origin` de forma normal.

