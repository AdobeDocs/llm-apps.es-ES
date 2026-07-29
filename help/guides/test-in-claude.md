---
title: Pruebe su aplicación LLM como conector Claude
description: Cree un conector Claude desde la URL de su servidor MCP de aplicaciones LLM de Adobe y pruébelo en una conversación.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 1%

---


# Probar su aplicación LLM como conector [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Después de la implementación, la aplicación LLM expone la dirección URL de un servidor MCP. Agregue esta dirección URL a [!DNL Claude] como conector personalizado y, a continuación, pruebe las acciones y widgets generados.

Este es el paso de verificación final después de crear, personalizar o ampliar una aplicación.

## Requisitos del plan

Los conectores personalizados que utilizan MCP remoto están disponibles en los planes [!DNL Claude], [!DNL Claude] Desktop y Cowork para Free, Pro, Max, Team y Enterprise. Las cuentas de plan gratuito están limitadas a un conector personalizado. Para las organizaciones Equipo y Empresa, un Propietario o Propietario principal debe habilitar los conectores antes de que otros miembros puedan utilizarlos.

## Copiar la URL del servidor MCP

En [!DNL LLM Apps]:

1. Abra la página Detalles de la aplicación.
2. Buscar **[!UICONTROL Probar la aplicación]**.
3. En **[!UICONTROL Entorno de ensayo]**, seleccione **[!UICONTROL Copiar URL]**.

## Añadir el conector personalizado

1. Abra [claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors). Esto abre directamente el cuadro de diálogo **[!UICONTROL Agregar conector personalizado]**.
2. Escriba
   - **[!UICONTROL Nombre]**: el nombre del conector.
   - **[!UICONTROL URL del servidor MCP remoto]**: la URL del servidor MCP que copió.
3. Seleccione **[!UICONTROL Añadir]**.

   ![Claude — Agregar diálogo de conector personalizado](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>Utilice únicamente conectores de desarrolladores en los que confíe. Anthropic no controla qué herramientas ponen a disposición los desarrolladores y no puede verificar que funcionarán según lo previsto o que no cambiarán.

## Permitir las herramientas generadas

Cada acción generada se enumera en **[!UICONTROL Permisos de herramientas]** en la página del conector. De manera predeterminada, las nuevas herramientas se establecen en **[!UICONTROL Necesita aprobación]**, lo que le solicita que apruebe cada llamada durante la prueba.

Establezca cada herramienta (o todo el grupo de **[!UICONTROL herramientas interactivas]**) en **[!UICONTROL Permitir siempre]**, de modo que las pruebas no se interrumpan con las solicitudes de aprobación.

![Claude — establece los permisos de la herramienta para Permitir siempre](/help/assets/guide-test-claude/claude-tool-permissions.png)

## Compruebe el conector

1. Iniciar una nueva conversación.
2. Seleccione **+** en el cuadro de mensaje (o escriba `/`), coloque el puntero sobre **[!UICONTROL Conectores]** y encienda el conector que agregó para esta conversación.

   ![Claude — habilita el conector para la conversación](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. Formule una pregunta que coincida con una de las acciones generadas. Por ejemplo: *Muéstreme un poco de café.*

Compruebe que:

- [!DNL Claude] invoca la acción esperada.
- El widget muestra los datos de muestra esperados.
- La respuesta del texto coincide con el widget.
- Los controles de widget funcionan según lo esperado.

## Siguientes pasos

- [Personalizar los widgets generados](/help/guides/widgets.md).
- [Crear una acción desde cero](/help/guides/create-action.md).
