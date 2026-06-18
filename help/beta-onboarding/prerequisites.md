---
title: Requisitos previos para las aplicaciones LLM de Adobe
description: Lo que debe configurar antes de su sesión de incorporación de Adobe LLM Apps Beta.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '539'
ht-degree: 2%

---


# Requisitos previos para las aplicaciones LLM de Adobe {#prerequisites-for-adobe-llm-apps}

Antes de la sesión de incorporación con Adobe, confirme que dispone de lo siguiente. Cuando sea posible, ejecute los pasos de verificación a continuación: los resultados le indican quién debe estar en la sala, no si puede continuar.

## Adobe Developer Console

Necesita acceso a [Adobe Developer Console](https://developer.adobe.com/console) con el rol de **Desarrollador** (o **Administrador del sistema**) en su organización Adobe IMS. Asegúrese de que su organización tenga acceso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Para verificarlo, ve a [developer.adobe.com/console](https://developer.adobe.com/console). Si ve la pantalla Inicio rápido, los permisos están correctamente configurados.

![Adobe Developer Console: pantalla de inicio rápido que confirma el acceso de desarrollador](/help/assets/overview/dev-console-access-granted.png)

Si en su lugar ve un mensaje **Acceso restringido**, no tiene la función Desarrollador. Invite al administrador de la organización IMS a la sesión de incorporación.

![Adobe Developer Console — Mensaje de acceso restringido](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

Necesita una cuenta de [!DNL GitHub] con los siguientes permisos en su organización:

- **Crear repositorios**: debe crear dos repositorios en su organización: uno para el código de la aplicación y otro para el proyecto EDS. Para verificarlo, ve a [github.com/new](https://github.com/new); si puedes seleccionar tu organización en la lista desplegable **Propietario**, tienes el permiso.

  ![Nuevo menú desplegable del propietario del repositorio de GitHub que muestra la selección de la organización](/help/assets/overview/github-repo-owner-dropdown.png)

- **Instalar [!DNL GitHub] aplicaciones**: necesita los permisos adecuados para instalar una aplicación de [!DNL GitHub] en su organización. Consulte [Requisitos para instalar una aplicación de GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

**Compruebe sus permisos antes de la sesión de incorporación**

Ejecute esta comprobación rápida antes de reunirse con Adobe. El resultado indica quién debe estar en la sala, no si puede continuar.

1. Vaya a [github.com/new](https://github.com/new), seleccione su organización como propietaria y cree un repositorio con el nombre `llm-apps-test`.
2. Vaya a la página de instalación del [Comprobador de permisos para aplicaciones LLM de Adobe](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new) e instale la aplicación solo para el repositorio `llm-apps-test`.

| Resultado | Lo que significa | Acción |
|---|---|---|
| Ambos pasos se realizan correctamente | Tiene los permisos necesarios | Está listo para la sesión de incorporación |
| El paso 2 muestra **Solicitud** en lugar de **Instalar** | No cuenta con permiso para instalar aplicaciones de [!DNL GitHub] | Invite a su administrador de organización [!DNL GitHub] a la reunión de incorporación |

Una vez finalizado, elimine el repositorio `llm-apps-test` y desinstale la aplicación de comprobación de permisos de la configuración de su organización.

## AEM Sites con [!DNL Edge Delivery Services]

Los widgets de acción están hospedados en **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. Su organización necesita una licencia de AEM Sites que incluya [!DNL Edge Delivery Services]. Debe tener la función **Admin** en su organización de EDS.

Para verificarlo, ve a la [herramienta de administración de usuarios de EDS](https://tools.aem.live/tools/user-admin/index.html), escribe el nombre de tu organización, deja **Sitio** en blanco y haz clic en **Buscar usuarios**. Busque su cuenta en la lista y confirme que muestra el distintivo **admin**.

![Herramienta de administración de usuarios de EDS que muestra un usuario con el rol de administrador](/help/assets/overview/eds-user-admin.png)

Si todavía no tiene una organización EDS, no es necesario realizar ninguna acción: se creará una durante el proceso de incorporación.

## Plataforma LLM (para pruebas)

Para probar la aplicación implementada, necesita un nivel de suscripción compatible que permita aplicaciones MCP personalizadas y **modo de desarrollador** habilitado. Por ejemplo, [!DNL ChatGPT] requiere una suscripción a **Pro**, **Business** o **Enterprise / Edu**.
