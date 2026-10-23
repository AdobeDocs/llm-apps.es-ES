---
title: Implemente la aplicación
description: Aprenda a implementar la aplicación LLM de Adobe en el ensayo y la producción mediante la interfaz de usuario de las aplicaciones LLM.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '444'
ht-degree: 0%
---

# Implemente la aplicación {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Cuando haya revisado las acciones generadas o insertado su propio código de controlador en el repositorio vinculado, implemente la aplicación desde [!DNL LLM Apps]. Para las acciones generadas, revise y combine el trabajo generado antes de implementarlo.

**En esta guía:** Aprenda a implementar su aplicación en Stage, compruebe el resultado de la implementación y copie la URL de su servidor MCP para realizar pruebas. La implementación en Producción después de la prueba se realiza correctamente.

## Inicio de la implementación

Abra la página Detalles de la aplicación y seleccione **[!UICONTROL Implementar]**.

Seleccione el entorno de destino y luego seleccione **[!UICONTROL Implementar]**.

Si los controladores utilizan [variables de aplicación](/help/guides/app-variables.md), configúrelas para el entorno de destino antes de implementarlas. Las variables agregadas, actualizadas o eliminadas tienen efecto en esta implementación; Fase y Producción tienen valores independientes.

![Implementar — seleccione el entorno de destino](/help/assets/guide-onboarding-agent/deploy-stage.png)

La implementación se ejecuta en cuatro pasos:

1. **Preparando** — recupera la configuración necesaria para implementar la aplicación.
2. **Iniciar implementación**: inicia el proceso de implementación en segundo plano.
3. **Generar aplicación**: instala dependencias y genera el código de repositorio más reciente.
4. **Publicar** — publica la aplicación en [!DNL Adobe I/O Runtime].

>[!NOTE]
>
>Si una acción tiene metadatos en la interfaz de usuario pero no hay ningún archivo de controlador coincidente en el repositorio, la implementación sigue registrando la acción. Las invocaciones utilizan un controlador de código auxiliar predeterminado hasta que se agrega el código real.

## Después de una implementación correcta

Cuando se completen todos los pasos, el cuadro de diálogo mostrará **Implementación correcta**.

Para cerrar el cuadro de diálogo, seleccione **Cerrar**. En la página Detalles de la aplicación, desplácese hasta **[!UICONTROL Probar la aplicación]**:

![Detalle de la aplicación: copie la URL del servidor MCP](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Cada entorno implementado muestra una URL de servidor MCP. Seleccione **[!UICONTROL Copiar URL]** y utilícelo para crear un complemento o conector en la plataforma LLM de destino.

La sección **Historial de implementaciones** muestra las últimas 10 implementaciones:

![Historial de implementación](/help/assets/guide-deploy/deployment-history.png)

Cada fila muestra el destino **Entorno** (Fase o Producción), **Estado** (Correcto o Fallido) y **Implementado en la fecha**. Utilice la tabla para realizar un seguimiento de las fechas de implementación y comprobar que la implementación más reciente se haya realizado correctamente.

## Implementar cambios de configuración

Los cambios en los metadatos de acciones, los modificadores **[!UICONTROL Visibilidad]** de la página de la aplicación, la autenticación, las variables de la aplicación y la configuración de Analytics se aplicarán en la siguiente implementación en ese entorno. Guardar una configuración no actualiza por sí solo el servidor MCP implementado.

Compruebe el entorno de destino cada vez. La implementación en fase no implementa los mismos cambios en Producción.

Si la aplicación no cumple los requisitos de tiempo de ejecución de Analytics o SDK, [Actualice una aplicación existente](/help/guides/upgrade-existing-app.md) antes de volver a implementarla.

## Siguiente paso

- [Probar la aplicación implementada como un complemento de ChatGPT](/help/guides/test-in-chatgpt.md).
- [Probar la aplicación implementada como un conector Claude](/help/guides/test-in-claude.md).
- [Envíe los eventos de su aplicación a Adobe Analytics o Customer Journey Analytics](/help/guides/analytics-setup.md).
