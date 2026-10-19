---
title: Pruebe su aplicación LLM como complemento de ChatGPT
description: Cree un complemento ChatGPT a partir de la URL de su servidor MCP de aplicaciones LLM de Adobe y pruébelo en una conversación.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '546'
ht-degree: 0%
---

# Probar su aplicación LLM como un complemento de [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Después de la implementación, la aplicación LLM expone la dirección URL de un servidor MCP. Agregue esta dirección URL a [!DNL ChatGPT] como complemento y, a continuación, pruebe las acciones y widgets generados.

**En esta guía:** Aprenda a conectar la aplicación implementada a [!DNL ChatGPT] y a probar sus acciones y widgets en una conversación. Actualice la conexión después de cambiar la aplicación.

## Requisitos del plan

Utilice una cuenta y un espacio de trabajo que permitan agregar servidores MCP personalizados. Los controles de disponibilidad y administrador varían según la cuenta y el espacio de trabajo. Consulte la documentación actual de OpenAI [Conéctese y pruebe su complemento](https://developers.openai.com/plugins/deploy/connect-chatgpt).

## Comprobación del acceso a los servidores MCP personalizados

Abra [Complementos de ChatGPT](https://chatgpt.com/plugins) y compruebe que su cuenta permite agregar un servidor MCP personalizado. Si su cuenta requiere el modo de desarrollador, habilite ese modo con los controles de su cuenta. Si falta la opción de servidor MCP personalizado o está bloqueada, pregunte al administrador del espacio de trabajo.

## Copiar la URL del servidor MCP

En [!DNL LLM Apps]:

1. Abra la página Detalles de la aplicación.
2. Buscar **[!UICONTROL Probar la aplicación]**.
3. En **[!UICONTROL Entorno de ensayo]**, seleccione **[!UICONTROL Copiar URL]**.

## Creación del complemento

1. Abra [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. Seleccione **+**, luego **[!UICONTROL Agregar servidor MCP personalizado]**.
3. Configure la conexión:
   - **[!UICONTROL Nombre]**: el nombre del complemento.
   - **[!UICONTROL Descripción]**: opcional.
   - Elija el método de conexión de servidor público e introduzca la URL completa del servidor HTTPS MCP de su aplicación.
   - Elija el método de autenticación que coincida con su aplicación. En el caso de una aplicación pública con autenticación deshabilitada, use **[!UICONTROL No Auth]** donde se ofrezca.

   >[!NOTE]
   >
   >Si ha activado la autenticación de usuario final, seleccione **[!UICONTROL OAuth]** cuando cada acción esté configurada en **[!UICONTROL Requerida]** o **[!UICONTROL Mixta]** para una combinación de acciones públicas y protegidas, donde se ofrecen esas opciones. Ver [Autenticar usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md).

4. Seleccione **[!UICONTROL Entiendo y deseo continuar]**.
5. Seleccione **[!UICONTROL Crear como complemento]**.
6. Revise las herramientas y los metadatos detectados y, a continuación, instale o conecte el complemento resultante. Complete el inicio de sesión si la aplicación lo requiere.

OpenAI es propietario de la interfaz de usuario de configuración y su diseño puede variar según la cuenta. Siga las [instrucciones de conexión](https://developers.openai.com/plugins/deploy/connect-chatgpt) actuales si las etiquetas mostradas difieren.

## Prueba del complemento

1. Iniciar una nueva conversación.
2. Escriba `@` en el cuadro de mensaje y seleccione el complemento instalado.
3. Formule una pregunta que coincida con una de las acciones generadas. Por ejemplo: *Muéstreme un poco de café.*

![Respuesta ilustrativa de ChatGPT — una acción de Frescopa devuelve tarjetas de producto en la conversación](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Compruebe que:

- [!DNL ChatGPT] invoca la acción esperada.
- El widget muestra los datos de muestra esperados.
- La respuesta del texto coincide con el widget.
- Los controles de widget funcionan según lo esperado.

Compruebe también la entrada no válida, los resultados vacíos, los mensajes no relacionados que no llaman a la aplicación y las acciones que cambian datos. Revise cada mensaje de confirmación y autenticación en lugar de aprobar todas las solicitudes por adelantado.

## Actualizar después de cambios en la aplicación

Después de cambiar las descripciones de las acciones, las entradas, la autenticación o la configuración del widget:

1. Implemente la aplicación actualizada en el mismo entorno.
2. Abra su conexión en [Complementos de ChatGPT](https://chatgpt.com/plugins) y seleccione **[!UICONTROL Actualizar]**.
3. Compruebe que las acciones anunciadas hayan cambiado.
4. Inicie una nueva conversación y repita las pruebas afectadas.

## Siguientes pasos

- [Personalizar los widgets generados](/help/guides/widgets.md).
- [Crear una acción desde cero](/help/guides/create-action.md).
