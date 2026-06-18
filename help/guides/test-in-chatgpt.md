---
title: Probar en ChatGPT
description: Aprenda a añadir la aplicación LLM de Adobe implementada a ChatGPT y pruébela en una conversación real.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '804'
ht-degree: 2%

---


# Probar en [!DNL ChatGPT]

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Esta guía utiliza [!DNL ChatGPT] como ejemplo. Los pasos generales: registrar una URL de servidor MCP y probar en una conversación, se aplican también a otras plataformas LLM, aunque el flujo de configuración y la interfaz de usuario variarán.

Después de una implementación correcta con [!DNL Adobe LLM Apps], la aplicación se está ejecutando en [!DNL Adobe I/O Runtime] y expone una dirección URL de servidor MCP. Esta guía muestra cómo agregarla a [!DNL ChatGPT] y probarla en una conversación real.

## Requisitos del plan

La adición de aplicaciones de desarrollador personalizadas a [!DNL ChatGPT] se rige por los niveles de suscripción de OpenAI; no se trata de una limitación de [!DNL LLM Apps], sino de cómo OpenAI administra actualmente el acceso a las aplicaciones de MCP personalizadas.

| [!DNL ChatGPT] plan | Aplicaciones MCP personalizadas |
|--------------|-----------------|
| Gratis | No disponible |
| Ir | No disponible |
| Más | No disponible |
| Pro | Disponible |
| Negocios | Disponible |
| Enterprise/Edu | Disponible |

>[!NOTE]
>
>Si tienes un plan gratuito, de ida o de plus, **no podrás agregar tu aplicación implementada** a [!DNL ChatGPT]. Actualice a **Pro** o pídale al administrador de su organización que lo habilite en un espacio de trabajo de **empresa** o **empresa**.

## Habilitar modo de desarrollador

Para agregar una aplicación MCP personalizada, debe tener **modo de desarrollador** habilitado en su cuenta de [!DNL ChatGPT]. Seguir
Siga los pasos a continuación para verificarlo y habilitarlo.

### Abrir configuración

Haz clic en el avatar de tu perfil en la esquina inferior izquierda y luego haz clic en **[!UICONTROL Configuración]**.

![ChatGPT — Menú de configuración](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### Navegar a aplicaciones

En el cuadro de diálogo Configuración, seleccione **[!UICONTROL Aplicaciones]** en la barra lateral izquierda. Haga clic en **[!UICONTROL Configuración avanzada]** en la parte inferior.

![ChatGPT — Configuración de aplicaciones](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### Activar el modo de desarrollador

Asegúrese de que la opción **[!UICONTROL Modo de desarrollador]** esté activada (azul). Esto le permite registrar direcciones URL de servidor MCP personalizadas y no verificadas.

>[!NOTE]
>
>El modo de desarrollador está etiquetado como *Riesgo elevado* porque permite aplicaciones que no han sido revisadas por OpenAI. [!DNL ChatGPT] deshabilita automáticamente la memoria para las conversaciones que usan aplicaciones en modo de desarrollador.

![ChatGPT — Modo de desarrollador habilitado](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## Agregar su aplicación a [!DNL ChatGPT]

### Copiar la URL del servidor MCP

Vaya a la página **Detalles de la aplicación** en [!DNL LLM Apps] y busque la sección **[!UICONTROL Probar la aplicación]**. Copie la dirección URL **Staging** o **Production**; tiene el siguiente aspecto:

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### Abrir la página de aplicaciones

En [!DNL ChatGPT], ve a **[!UICONTROL Configuración] → [!UICONTROL Aplicaciones]**.

![ChatGPT — Página de aplicaciones](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### Crear una aplicación nueva

Haga clic en **[!UICONTROL Crear aplicación]** en la fila Configuración avanzada.

![ChatGPT — cuadro de diálogo Crear aplicación](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

Complete lo siguiente:

| Campo | Valor |
|-------|-------|
| **Icono** | Opcional: cargar un archivo PNG de 128 x 128 (10 KB como máximo) |
| **Nombre** | Un nombre para mostrar en la aplicación (por ejemplo, *Mi aplicación con marca*) |
| **Descripción** | Breve descripción de lo que hace la aplicación |
| **URL del servidor MCP** | Pegar la dirección URL de [!DNL LLM Apps] |
| **[!UICONTROL Autenticación]** | Seleccionar *Sin autenticación* |

Marque la casilla de verificación **Entiendo y deseo continuar**, esto reconoce que el servidor MCP
OpenAI no ha revisado y haz clic en **Crear**.

### Verifique que la aplicación esté habilitada

Después de la creación, la aplicación aparecerá en **[!UICONTROL Aplicaciones habilitadas]** con un distintivo de **[!UICONTROL DEV]**, lo que confirma que está activa.

>[!NOTE]
>
>Su aplicación también aparece en **Borradores**, que son aplicaciones privadas que creó en el modo de desarrollador y que solo son visibles para su cuenta.

La aplicación ya está lista para utilizarse en [!DNL ChatGPT] conversaciones.

![ChatGPT — aplicación habilitada](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## Prueba en una conversación

Una vez habilitada la aplicación, inicie una nueva conversación en [!DNL ChatGPT]. Antes de hacer una pregunta, adjunte la aplicación mediante uno de estos dos métodos.

### Opción 1: seleccione en el menú

Haga clic en el botón **+** en la entrada del chat y luego en **Más** para expandir la lista completa de herramientas disponibles. Seleccione la aplicación de la lista para adjuntarla a la conversación actual.

![ChatGPT — seleccionar aplicación del menú](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### Opción 2: @mention de uso

Escriba **@** en la entrada de chat y seleccione su aplicación en la lista desplegable. Esto adjunta la aplicación en línea y puede seguir escribiendo la pregunta en el mismo mensaje.

>[!NOTE]
>
>Si usas **@mention** por segunda vez en la misma aplicación, se anulará su selección y se eliminará de la conversación.

![ChatGPT — @mention la aplicación](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

Una vez seleccionada, la aplicación se adjunta en línea y puede escribir su pregunta en el mismo mensaje:

![ChatGPT — aplicación adjunta vía @mention](/help/assets/guide-test-chatgpt/chatgpt-mention.png)

### Ver el resultado

Una vez adjunta la aplicación, escriba una pregunta alineada con una de las acciones configuradas; por ejemplo, *&quot;Muéstreme sus productos.&quot;* [!DNL ChatGPT] lo hace coincidir con la acción relevante, extrae los parámetros de entrada, llama al controlador en [!DNL Adobe I/O Runtime] y procesa el resultado:

![ChatGPT — resultado de la acción](/help/assets/guide-test-chatgpt/chatgpt-response.png)

La respuesta incluye:

- **El widget EDS**: un componente de interfaz de usuario enriquecido con imágenes, clasificaciones y botones de acción.
- **La respuesta de texto** — debajo del widget, [!DNL ChatGPT] usa el `content` devuelto por su controlador
formular un resumen de los resultados en lenguaje natural.
- **Indicador de estado** — el *texto de estado invocado* que configuró en el cuadro de diálogo Crear acción.

## Siguientes pasos

- **Agregar más acciones**: defina acciones adicionales en la interfaz de usuario, escriba sus controladores y vuelva a implementar.
- **Implementar en producción**: si ha probado en Fase, implemente en Producción para la experiencia en directo.
- **Compartir con su equipo**: use **Copiar URL** en la página Detalles de la aplicación para compartir la URL del servidor MCP con sus compañeros.

