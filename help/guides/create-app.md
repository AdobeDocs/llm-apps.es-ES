---
title: Cree su primera aplicación LLM automáticamente
description: Cree una aplicación LLM de Adobe desde el sitio web, revise las acciones generadas, impleméntelo y pruébelo en una plataforma LLM compatible como ChatGPT.
source-git-commit: f91bb73a39cc5aacf44979ee55dd0ab5f69d4c81
workflow-type: tm+mt
source-wordcount: '1272'
ht-degree: 0%

---


# Cree Su Primera Aplicación Automáticamente {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

La plataforma convierte su sitio web en una aplicación completamente funcional. Propone acciones, escribe código de controlador y prueba, crea widgets EDS y envía los archivos generados a dos repositorios de [!DNL GitHub] de su propiedad.

Se tardan aproximadamente 15 minutos en la generación. Al final de este tutorial, tendrá una aplicación implementada que puede probar en una plataforma LLM compatible como [!DNL ChatGPT].

**Recorrido:** Confirme los requisitos → crear dos repositorios → crear la aplicación → revisar las acciones generadas → implementar en Fase → probar el complemento → conectar sistemas de producción.

## Antes de empezar

Complete todos los [requisitos de aplicaciones LLM](/help/overview/overview.md#requirements) antes de comenzar este tutorial.

Este tutorial crea una aplicación LLM para [Frescopa Coffee](https://frescopa.coffee/).

## Crear dos repositorios vacíos

La plataforma necesita dos repositorios vacíos. Cree ambas en la misma cuenta u organización de [!DNL GitHub]:

- **Repositorio de controladores**: almacena controladores de acciones y pruebas. Por ejemplo, `my-brand-llm-app`.
- **Repositorio de EDS**: almacena estilos y bloques de widgets generados. Por ejemplo, `my-brand-llm-app-eds`.

Vaya a [github.com/new](https://github.com/new) para cada repositorio.

No inicialice ningún repositorio con README, `.gitignore` o una licencia. La plataforma prepara la estructura de proyecto necesaria.

>[!TIP]
>
>Utilice nombres de repositorios que identifiquen la aplicación y el propósito de cada repositorio. Esto facilita su reconocimiento en el cuadro de diálogo de creación de aplicaciones.

## Inicie la aplicación

1. Abra [Aplicaciones LLM de Adobe](https://experience.adobe.com/#/@llmapps/llm-apps/) y seleccione **[!UICONTROL Crear aplicación]**.
2. Escriba **[!UICONTROL LLM App Name]** y una descripción opcional.
3. Seleccione la **[!UICONTROL región de Analytics]**.

   >[!IMPORTANT]
   >
   >La región de Analytics no se puede cambiar una vez creada la aplicación.

4. En **[!UICONTROL Generar mi aplicación]**, seleccione **[!UICONTROL Generar mi aplicación automáticamente]**.
5. En **[!UICONTROL su sitio web]**, ingrese la dirección URL de su sitio web, incluido el protocolo `https://`. La plataforma analiza este sitio para determinar acciones útiles y resultados de muestra representativos.

![Crear aplicación LLM: detalles de la aplicación y crear mi aplicación habilitada](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## Dar acceso a [!DNL LLM Apps] a los repositorios

La aplicación Adobe LLM Apps [!DNL GitHub] proporciona acceso a [!DNL LLM Apps] a los repositorios que seleccione.

>[!NOTE]
>
>La conexión de una organización [!DNL GitHub] es una configuración única. Si la organización ya aparece en el cuadro de diálogo, use **[!UICONTROL Administrar repositorios en GitHub]** en lugar de volver a conectarla.

### Organización conectada

Si la aplicación Adobe LLM Apps [!DNL GitHub] ya estaba instalada antes de crear los repositorios:

1. Seleccione la organización conectada.
2. Seleccione **[!UICONTROL Administrar repositorios en GitHub]**.
3. Agregue los dos repositorios a la instalación de la aplicación [!DNL GitHub] existente.
4. Vuelva a [!DNL LLM Apps] y actualice las listas de repositorios.

### Solo la primera conexión

Si la organización no aparece en el cuadro de diálogo:

1. Seleccione **[!UICONTROL Conectar una organización de GitHub]**.
2. Instalar la aplicación Adobe LLM Apps [!DNL GitHub].
3. Elija **[!UICONTROL Solo seleccione repositorios]** y seleccione los dos repositorios.
4. Vuelva al cuadro de diálogo Crear aplicación LLM.

Si no puede instalar o actualizar la aplicación [!DNL GitHub], pregunte al administrador de la organización.

## Selección de los repositorios

1. En **[!UICONTROL Repositorio de plantillas]**, seleccione la organización y el repositorio de controladores vacío.
2. En **[!UICONTROL Repositorio EDS]**, seleccione la organización y el repositorio EDS vacío.

   ![Crear mi aplicación: seleccione la organización de GitHub, el repositorio de plantillas y el repositorio de EDS](/help/assets/guide-onboarding-agent/repos-selected.png)

3. En **[!UICONTROL Términos y condiciones]**, marque **[!UICONTROL Acepto los Términos de Adobe Developer]**.
4. Seleccione **[!UICONTROL Crear aplicación]**.

## Completar la configuración de EDS

Cuando el repositorio EDS seleccionado está vacío, [!DNL LLM Apps] lo inicializa con la plantilla de AEM. A continuación, el cuadro de diálogo le pedirá que instale AEM Code Sync antes de intentar crear la aplicación de nuevo.

1. En el mensaje debajo del repositorio EDS, seleccione **[!UICONTROL Instalar sincronización de código de AEM]**.
2. En [!DNL GitHub], instale Sincronización de código de AEM y asígnele acceso al repositorio de EDS.

   En la página de confirmación **Sincronización de código de AEM registrada**, en **[!UICONTROL Usuarios del sitio]**, seleccione **[!UICONTROL + Agregar usuario]** y agregue la dirección de correo electrónico que usa para iniciar sesión en [!DNL LLM Apps] con el rol **[!UICONTROL admin]**. Luego selecciona **[!UICONTROL Finalizar configuración]** en la parte inferior de la página.

   ![Sincronización de código de AEM registrada: agréguese como usuario del sitio con la función de administrador](/help/assets/guide-onboarding-agent/aem-code-sync-site-users-admin.png)

3. Vuelva al cuadro de diálogo Crear aplicación LLM.

![Crear aplicación LLM: se ha inicializado el repositorio EDS vacío y se requiere la sincronización de código AEM](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

Debe ser administrador del sitio de EDS. Si el cuadro de diálogo indica que no es administrador:

![Crear aplicación LLM — Se requiere acceso de administrador de EDS](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. Seleccione **[!UICONTROL Abrir el administrador de AEM Live]**.
2. Agréguese como administrador del sitio de EDS haciendo clic en el botón **[!UICONTROL + Agregar usuario(s)]**.

   ![Crear aplicación LLM — Añadirlo como administrador de EDS](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. Vuelva a [!DNL LLM Apps], actualice el repositorio de EDS y seleccione **[!UICONTROL Crear aplicación]** de nuevo.

Una vez superadas las comprobaciones del repositorio y del administrador, [!DNL LLM Apps] crea la aplicación y comienza a generar acciones.

## Esperar a la generación de acciones

Vaya a la página **[!UICONTROL Acciones]**, desde la izquierda. La página Acciones muestra **Descubriendo acciones para tu experiencia de conversación** mientras el agente analiza el sitio web y genera la aplicación. Generalmente tarda aproximadamente 15 minutos. Puede salir de esta página y volver más tarde.

![Acciones — generando recomendaciones](/help/assets/guide-onboarding-agent/actions-generating.png)

Durante la generación, [!DNL LLM Apps]:

1. Analiza el sitio web e identifica intenciones de cliente útiles.
2. Crea metadatos de acción, incluidas descripciones y parámetros de entrada.
3. Genera un controlador y prueba cada acción en el repositorio del controlador.
4. Genera un widget EDS para cada acción en el repositorio EDS.
5. Prepara las acciones para su revisión.

Los controladores generados utilizan inicialmente datos de ejemplo derivados del sitio web. Muestran la experiencia completa, pero no se conectan a los sistemas de producción.

## Revisar las acciones generadas

Cuando termina la generación, la página Acciones muestra las acciones generadas y las vistas previas de los widgets. Cada acción tiene una **[!UICONTROL acción generada por IA, necesita revisión]**.

![Acciones — acciones generadas listas para revisión](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

Para cada acción:

1. Seleccione **[!UICONTROL Revisar]**.
2. Revise el nombre, la descripción, los parámetros, las anotaciones, el controlador generado y el widget.
3. Seleccione **[!UICONTROL Marcar como revisado]**. Esto combina las solicitudes de extracción generadas.
4. Vuelva a la página Acciones y repita los pasos para las acciones restantes.

![Acción generada — lista para marcar como revisada](/help/assets/guide-onboarding-agent/generated-action-review.png)

Cuando se revisen todas las acciones, seleccione **[!UICONTROL Ir a la página de la aplicación]**.

![Acciones — todas las acciones generadas revisadas](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>El código generado es un punto de partida suyo. Puede cambiar los metadatos de las acciones, los controladores, las pruebas, el widget de JavaScript y los estilos de los widgets después de la revisión.

## Implemente la aplicación

1. Vuelva a la página Detalles de la aplicación.
2. Seleccione **[!UICONTROL Implementar]**.
3. Seleccione **[!UICONTROL Stage]** como entorno de destino.
4. Seleccione **[!UICONTROL Implementar]**.

![Implementar — seleccione el entorno de ensayo](/help/assets/guide-onboarding-agent/deploy-stage.png)

Espere mientras [!DNL LLM Apps] prepara, compila y publica la aplicación.

![Implementar — canalización de implementación en ejecución](/help/assets/guide-onboarding-agent/deploy-running.png)

![Implementación: implementación de ensayo correcta](/help/assets/guide-onboarding-agent/deploy-successful.png)

Después de la implementación, la sección **[!UICONTROL Probar la aplicación]** muestra la dirección URL del servidor MCP de ensayo. Seleccione **[!UICONTROL Copiar URL]**.

![Detalle de la aplicación: copie la URL del servidor MCP de ensayo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## Probar en [!DNL ChatGPT]

Seguir [prueba en ChatGPT](/help/guides/test-in-chatgpt.md) para crear un complemento con la URL del servidor MCP de ensayo.

Formule una pregunta que coincida con una de las acciones generadas. Compruebe que:

- [!DNL ChatGPT] selecciona la acción esperada.
- El widget procesa y contiene los datos de muestra esperados.
- Los controles de widget producen el comportamiento de seguimiento esperado.
- La respuesta del texto resume con precisión el resultado.

![ChatGPT — respuesta generada del complemento de la aplicación LLM](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Ahora tiene una aplicación integral completamente funcional y en funcionamiento.

## Preparar la aplicación para la producción

La aplicación generada utiliza datos de ejemplo. Antes de usarlo con los clientes:

1. **Conecte sus sistemas** — [personalice cada controlador generado](/help/guides/customize-handler.md) para reemplazar los datos de ejemplo con llamadas a sus API o fuentes de datos.
2. **Proteger credenciales**: almacene las direcciones URL y credenciales de la API en la configuración de tiempo de ejecución administrada, nunca en el código fuente ni en el widget JavaScript.
3. **Validar datos**: valide argumentos de acción y respuestas de API, agregue tiempos de espera de solicitud y devuelva mensajes de error seguros.
4. **Actualice los widgets**: mantenga cada widget alineado con el `structuredContent` de su controlador y, a continuación, aplique los requisitos de marca y accesibilidad. Ver [Personalizar un widget generado](/help/guides/widgets.md).
5. **Pruebe los controladores**: cubra la entrada válida, la entrada no válida, los resultados vacíos, los errores de API y la forma de datos esperada por el widget.
6. **Verificar en fase**: vuelva a implementar y pruebe cada acción a través del complemento [!DNL ChatGPT].
7. **Implementar en producción**: después de que la prueba de fase se haya realizado correctamente, implemente en Producción y cree o actualice el complemento con la URL del servidor MCP de producción.

Para agregar una capacidad que la plataforma no creó, vea [Crear una acción desde cero](/help/guides/create-action.md).

