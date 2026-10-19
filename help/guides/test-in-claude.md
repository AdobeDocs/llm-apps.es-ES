---
title: Pruebe su aplicación LLM como conector Claude
description: Cree un conector Claude desde la URL de su servidor MCP de aplicaciones LLM de Adobe y pruébelo en una conversación.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%
---

# Probar la aplicación LLM como conector [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Después de la implementación, la aplicación LLM expone la dirección URL de un servidor MCP. Agregue esta dirección URL a [!DNL Claude] como conector personalizado y, a continuación, pruebe las acciones y widgets generados.

**En esta guía:** Aprenda a agregar la aplicación implementada como conector de [!DNL Claude], elegir su comportamiento de inicio de sesión y probar sus acciones y widgets en una conversación.

Para una aplicación con acciones públicas y protegidas, [!DNL Claude] admite el inicio de sesión diferido hasta que llama a una acción protegida. Una aplicación cuyas acciones requieran autenticación debe iniciar sesión antes de utilizarla. Ver [Autenticar usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md).

## Requisitos del plan

Los conectores personalizados que utilizan MCP remoto están disponibles para los planes Free, Pro, Max, Team y Enterprise. Las cuentas gratuitas admiten un conector personalizado. En Equipo y Empresa, un propietario autorizado agrega el conector para la organización antes de que los miembros se conecten a ella. Los roles personalizados de empresa también pueden conceder ese permiso.

Consulte las [instrucciones actuales del conector personalizado de Claude](https://claude.com/docs/connectors/custom/add-unlisted) para obtener los controles del plan y la organización. El punto final MCP remoto debe ser accesible desde la infraestructura de Claude, no solo desde su navegador local.

## Copiar la URL del servidor MCP

En [!DNL LLM Apps]:

1. Abra la página Detalles de la aplicación.
2. Buscar **[!UICONTROL Probar la aplicación]**.
3. En **[!UICONTROL Entorno de ensayo]**, seleccione **[!UICONTROL Copiar URL]**.

## Añadir el conector personalizado

1. Abra [Personalizar > Conectores](https://claude.ai/customize/connectors). Para un conector para toda la organización, un propietario autorizado abre [Configuración de la organización > Conectores](https://claude.ai/admin-settings/connectors).
2. Seleccione **[!UICONTROL Agregar conector personalizado]** o **[!UICONTROL Agregar]** > **[!UICONTROL Personalizado]** > **[!UICONTROL Web]** para el flujo de organización.
3. Introduzca el nombre del conector si se solicita y pegue la dirección URL completa del servidor HTTPS MCP.
4. Si el cuadro de diálogo tiene varios pasos, seleccione **[!UICONTROL Continuar]** y revise la configuración de autenticación detectada:
   - **[!UICONTROL No se ha iniciado sesión]** en una aplicación pública con la autenticación deshabilitada.
   - **[!UICONTROL Inicia sesión cuando sea necesario]** para una aplicación con acciones públicas y protegidas.
   - **[!UICONTROL Inicie sesión ahora]** para solicitar el inicio de sesión antes de usar el conector.
5. Para OAuth, elija un método de identidad de cliente compatible con el proveedor de identidad. Si utiliza un cliente preregistrado, introduzca sus detalles únicamente en la configuración de OAuth del conector, no en la URL o los metadatos de acción.
6. Seleccione **[!UICONTROL Añadir]**. Para un conector de organización, cada miembro selecciona **[!UICONTROL Connect]** de su propia lista de conectores.

Algunas cuentas muestran un cuadro de diálogo de una sola pantalla con **[!UICONTROL configuración avanzada]**, mientras que otras muestran un flujo de varios pasos. Use las [instrucciones actuales de la plataforma](https://claude.com/docs/connectors/custom/add-unlisted#choose-authentication-settings) si el diseño es distinto.

>[!NOTE]
>
>Utilice únicamente conectores de desarrolladores en los que confíe. Anthropic no controla qué herramientas ponen a disposición los desarrolladores o garantizan que esas herramientas funcionen según lo previsto o no cambien.

## Permitir las herramientas generadas

[!DNL Claude] enumera cada acción generada en **[!UICONTROL Permisos de herramientas]** en la página del conector. Las nuevas herramientas tienen el valor predeterminado **[!UICONTROL Necesita aprobación]**, lo que le pedirá que apruebe cada llamada durante la prueba.

Mantenga las solicitudes de aprobación habilitadas para acciones que cambien datos o realicen operaciones confidenciales. Use **[!UICONTROL Permitir siempre]** solo para acciones específicas de confianza y bajo riesgo cuando sea apropiado para su prueba.

## Compruebe el conector

1. Iniciar una nueva conversación.
2. Seleccione **+** en el cuadro de mensaje (o escriba `/`), coloque el puntero sobre **[!UICONTROL Conectores]** y encienda el conector que agregó para esta conversación.
3. Formule una pregunta que coincida con una de las acciones generadas. Por ejemplo: *Muéstreme un poco de café.*

Compruebe que:

- [!DNL Claude] invoca la acción esperada.
- El widget muestra los datos de muestra esperados.
- La respuesta del texto coincide con el widget.
- Los controles de widget funcionan según lo esperado.

Para la autenticación mixta, llame primero a una acción pública sin iniciar sesión. A continuación, llame a una acción protegida, complete la solicitud de inicio de sesión y confirme que la misma solicitud se realiza correctamente posteriormente. Pruebe también los permisos que faltan y la entrada no válida.

Después de cambiar la aplicación, vuelva a implementarla y repita las pruebas afectadas. Si necesita cambiar la configuración de autenticación guardada de un conector, [!DNL Claude] puede requerir que quite y vuelva a agregar el conector.

## Siguientes pasos

- [Personalizar los widgets generados](/help/guides/widgets.md).
- [Crear una acción desde cero](/help/guides/create-action.md).
