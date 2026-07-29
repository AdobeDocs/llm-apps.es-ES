---
title: Implemente la aplicación
description: Aprenda a implementar la aplicación LLM de Adobe en el ensayo y la producción mediante la interfaz de usuario de las aplicaciones LLM.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '309'
ht-degree: 0%

---


# Implemente su aplicación {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Una vez que haya escrito el código del controlador y lo haya insertado en el repositorio vinculado, puede implementar la aplicación desde la interfaz de usuario de [!DNL LLM Apps].

Este es un paso compartido para cada recorrido. Después de la implementación, continúe [probando el complemento ChatGPT](/help/guides/test-in-chatgpt.md).

## Inicio de la implementación

Abra la página Detalles de la aplicación y seleccione **[!UICONTROL Implementar]**.

Seleccione el entorno de destino y luego seleccione **[!UICONTROL Implementar]**.

![Implementar — seleccione el entorno de destino](/help/assets/guide-onboarding-agent/deploy-stage.png)

La implementación se ejecuta en cuatro pasos:

1. **Preparando** — recupera la configuración necesaria para implementar la aplicación.
2. **Iniciar implementación**: inicia el proceso de implementación en segundo plano.
3. **Generar aplicación**: instala dependencias y genera el código de repositorio más reciente.
4. **Publicar** — publica la aplicación en [!DNL Adobe I/O Runtime].

![Implementar — canalización de implementación en ejecución](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>Si una acción tiene metadatos en la interfaz de usuario pero no hay ningún archivo de controlador coincidente en el repositorio, se seguirá registrando. Las invocaciones utilizan un controlador de código auxiliar predeterminado hasta que se agrega el código real.

## Después de una implementación correcta

Cuando se completen todos los pasos, el cuadro de diálogo mostrará **Implementación correcta**.

![Implementación: implementación correcta](/help/assets/guide-onboarding-agent/deploy-successful.png)

Haga clic en **Cerrar** para cerrar el cuadro de diálogo. Desplácese hacia abajo hasta la sección **[!UICONTROL Probar la aplicación]** de la página Detalles de la aplicación:

![Detalle de la aplicación: copie la URL del servidor MCP](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Cada entorno implementado muestra una URL de servidor MCP. Seleccione **[!UICONTROL Copiar URL]** y utilícelo para crear un complemento en la plataforma LLM de destino.

La sección **Historial de implementaciones** muestra las últimas 10 implementaciones:

![Historial de implementación](/help/assets/guide-deploy/deployment-history.png)

Cada fila muestra el destino **Entorno** (Fase o Producción), **Estado** (Correcto o Fallido) y **Implementado en la fecha**. Puede utilizar esta tabla para realizar un seguimiento de cuándo se produjeron las implementaciones y comprobar que la variable
implementación más reciente correcta.

## Siguiente paso

[Probar la aplicación implementada como un complemento de ChatGPT](/help/guides/test-in-chatgpt.md).

