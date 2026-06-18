---
title: Implemente su aplicación
description: Aprenda a implementar la aplicación LLM de Adobe en el ensayo y la producción mediante la interfaz de usuario de las aplicaciones LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '359'
ht-degree: 0%

---


# Implemente su aplicación

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Una vez que haya escrito el código del controlador y lo haya insertado en el repositorio vinculado, puede implementar la aplicación desde la interfaz de usuario de [!DNL LLM Apps].

## Inicio de la implementación

Vaya a la página Detalles de la aplicación. Haga clic en el botón **[!UICONTROL Implementar]** en la esquina superior derecha:

![Detalles de la aplicación — lista para implementar](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Esto abre el cuadro de diálogo de implementación. Seleccione el entorno de destino en la lista desplegable:

![Cuadro de diálogo de implementación — seleccionar entorno de destino](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

Haga clic en **[!UICONTROL Implementar]** para iniciar la canalización. Los cuatro pasos son:

1. **Recopilar credenciales**: lee metadatos de la aplicación, genera un token [!DNL GitHub] y recupera credenciales de tiempo de ejecución de la API de la consola.
2. **Canalización de compilación de Déclencheur**: envía todos los parámetros a la canalización de compilación.
3. **Clonar y compilar**: la canalización clona el repositorio, genera `actions.json` a partir de los metadatos de la interfaz de usuario, ejecuta `npm install` y el Webpack para producir `dist/index.js`.
4. **Implementar en tiempo de ejecución**: implementa el paquete en el espacio de nombres [!DNL Adobe I/O Runtime] de la aplicación.

Una vez iniciada, la canalización se ejecuta automáticamente y muestra el progreso en tiempo real:

![Implementar canalización en ejecución](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>Si una acción tiene metadatos en la interfaz de usuario pero no hay ningún archivo de controlador coincidente en el repositorio, se seguirá registrando. Las invocaciones utilizan un controlador de código auxiliar predeterminado hasta que se agrega el código real.

## Después de una implementación correcta

Cuando se completan todos los pasos, el cuadro de diálogo muestra una confirmación de **Implementación correcta** con la URL implementada y los detalles del artefacto:

![Implementación correcta](/help/assets/guide-deploy/app-detail-deploy-finish.png)

Haga clic en **Cerrar** para cerrar el cuadro de diálogo. Desplácese hacia abajo hasta la sección **[!UICONTROL Probar la aplicación]** de la página Detalles de la aplicación:

![Probar la aplicación — direcciones URL implementadas](/help/assets/guide-deploy/test-app-deployed.png)

Cada entorno (**Staging** y **Production**) muestra la URL del servidor MCP en [!DNL Adobe I/O Runtime]. Dirección URL que proporciona a la plataforma LLM al registrar la aplicación. Haga clic en **Copiar URL** para copiarlo en el portapapeles.

La sección **Historial de implementación** a continuación mantiene un registro completo de cada implementación en entornos:

![Historial de implementación](/help/assets/guide-deploy/deployment-history.png)

Cada fila muestra el destino **Entorno** (Fase o Producción), **Estado** (Correcto o Fallido) y **Implementado en la fecha**. Puede utilizar esta tabla para realizar un seguimiento de cuándo se produjeron las implementaciones y comprobar que la variable
implementación más reciente correcta.

