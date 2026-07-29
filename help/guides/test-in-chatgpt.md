---
title: Pruebe su aplicación LLM como complemento de ChatGPT
description: Cree un complemento ChatGPT a partir de la URL de su servidor MCP de aplicaciones LLM de Adobe y pruébelo en una conversación.
source-git-commit: b7199fbb387d91a5c77deac47a2bc883381931c1
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 1%

---


# Probar su aplicación LLM como un complemento de [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Después de la implementación, la aplicación LLM expone la dirección URL de un servidor MCP. Agregue esta dirección URL a [!DNL ChatGPT] como complemento y, a continuación, pruebe las acciones y widgets generados.

Este es el paso de verificación final después de crear, personalizar o ampliar una aplicación.

## Requisitos del plan

El modo de desarrollador está disponible en la web para cuentas de Pro, Plus, Business, Enterprise y Education. Los administradores de Workspace pueden restringir el acceso.

## Habilitar modo de desarrollador

En [!DNL ChatGPT]:

1. Abra **[!UICONTROL Configuración] → [!UICONTROL Seguridad e inicio de sesión]**.
2. Activar **[!UICONTROL modo de desarrollador]**.

El botón &quot;+&quot; de la página Plugins crea complementos respaldados por MCP sólo después de activar el modo de desarrollador. Consulte [Modo de desarrollador de ChatGPT](https://developers.openai.com/api/docs/guides/developer-mode).

## Copiar la URL del servidor MCP

En [!DNL LLM Apps]:

1. Abra la página Detalles de la aplicación.
2. Buscar **[!UICONTROL Probar la aplicación]**.
3. En **[!UICONTROL Entorno de ensayo]**, seleccione **[!UICONTROL Copiar URL]**.

## Creación del complemento

1. Abra [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. En la ficha **[!UICONTROL Plugins]**, seleccione **+** junto al campo de búsqueda.

   ![ChatGPT — página de complementos](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. En **[!UICONTROL Nuevo complemento]**, escriba:
   - **[!UICONTROL Nombre]**: el nombre del complemento.
   - **[!UICONTROL Descripción]**: opcional.
   - **[!UICONTROL Conexión]** — seleccione **[!UICONTROL URL del servidor]** y pegue la URL del servidor MCP.
   - **[!UICONTROL Autenticación]** — seleccione **[!UICONTROL Sin autenticación]**.
4. Seleccione **[!UICONTROL Entiendo y deseo continuar]**.
5. Seleccione **[!UICONTROL Crear]**.

   ![ChatGPT — crear un complemento con la URL del servidor MCP](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)

6. En el cuadro de diálogo de confirmación, seleccione **[!UICONTROL Conectar]**.

   ![ChatGPT — conectar el nuevo complemento](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## Prueba del complemento

1. Iniciar una nueva conversación.
2. En el menú Más, elija **[!UICONTROL Modo de desarrollador]** y seleccione el complemento.
3. Formule una pregunta que coincida con una de las acciones generadas. Por ejemplo: *Muéstreme un poco de café.*

![ChatGPT — respuesta generada del complemento de la aplicación LLM](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Compruebe que:

- [!DNL ChatGPT] invoca la acción esperada.
- El widget muestra los datos de muestra esperados.
- La respuesta del texto coincide con el widget.
- Los controles de widget funcionan según lo esperado.

## Siguientes pasos

- [Personalizar los widgets generados](/help/guides/widgets.md).
- [Crear una acción desde cero](/help/guides/create-action.md).
