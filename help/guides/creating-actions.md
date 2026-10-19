---
title: Creación de acciones
description: Genere acciones de aplicación LLM de Adobe desde un sitio web público, siga el progreso de generación, revise el trabajo generado o elija la creación manual de acciones.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '938'
ht-degree: 0%
---

# Creación de acciones {#creating-actions}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Las acciones son las capacidades que ofrece la aplicación dentro de una conversación LLM, como encontrar productos, comprobar la disponibilidad o reservar un servicio. El controlador de cada acción realiza la tarea y un widget opcional presenta el resultado.

Después de [configurar la infraestructura](/help/guides/create-app.md), elija cómo crear acciones desde la página de la aplicación. La generación automática está disponible tanto con la infraestructura administrada de Adobe como con sus propios repositorios.

**En esta guía:** Aprenda a generar acciones a partir del sitio web o a crearlas usted mismo. Para la generación automática, inicie el proceso y revise los resultados antes de implementar y probar la aplicación.

## Elija cómo generar acciones

Abra la aplicación. Si Adobe sigue preparando su infraestructura, espere a que finalice la instalación.

En la sección **[!UICONTROL Acciones]**, **[!UICONTROL ¿Cómo desea que se generen las acciones?]** ofrece dos opciones:

| Opción | Úselo cuando |
|---|---|
| **[!UICONTROL Generar acciones automáticamente]** | Desea que la plataforma analice el sitio web y genere acciones sugeridas, código de controlador, pruebas y widgets. Este es el punto de partida recomendado. |
| **[!UICONTROL Yo mismo crearé acciones]** | Desea definir cada acción y proporcionar su implementación usted mismo. |

Al elegir la infraestructura en el asistente para crear aplicaciones no se selecciona ninguna de estas opciones.

## Generación de acciones desde un sitio web {#generate-from-website}

1. Seleccionar **[!UICONTROL acciones de compilación automáticamente]**.
2. En **[!UICONTROL Su sitio web]**, escriba un sitio web HTTPS público que represente los productos, servicios o tareas de los clientes de su marca. Este ejemplo utiliza `https://frescopa.coffee/`.
3. Seleccione **[!UICONTROL Generar mis acciones]**.

![Acciones: generación automática seleccionada con el sitio web público introducido antes de iniciar](/help/assets/guide-creating-actions/build-choice.png)

Utilice un sitio web que esté autorizado a analizar. No proporcione un sitio que exponga información confidencial o de acceso controlado.

Al seleccionar **[!UICONTROL Generar mis acciones]** se inicia la generación; al introducir la dirección URL por sí sola, no se inicia. No es necesario volver a crear la aplicación ni abrir de nuevo el asistente de infraestructura.

### Seguir progreso de generación

La página de la aplicación muestra **[!UICONTROL Se están generando acciones]**. Seleccione **[!UICONTROL Ir a acciones]** o abrir **[!UICONTROL acciones]** desde la navegación para seguir el progreso.

La interfaz de usuario calcula hasta 30 minutos para la generación. Salga de la página y vuelva más tarde si es necesario.

![Acciones: se está ejecutando la generación, con un vínculo para seguir el progreso en la página Acciones](/help/assets/guide-creating-actions/generating.png)

Durante la generación, la plataforma:

1. Analiza el sitio web e identifica las tareas del cliente para la aplicación.
2. Crea metadatos de acción sugeridos, incluidas descripciones y parámetros de entrada.
3. Genera controladores y pruebas del lado del servidor en el repositorio de acciones.
4. Genera widgets en el repositorio EDS.
5. Prepara las acciones generadas para su revisión.

Mientras la plataforma analiza el sitio web, la página Acciones muestra **[!UICONTROL Descubriendo acciones para tu experiencia conversacional]**. Las acciones sugeridas aparecen a medida que la plataforma las descubre; es posible que sus vistas previas de código y widget aún se estén generando. Espere a que finalice la generación antes de revisar cada acción.

>[!IMPORTANT]
>
>Los controladores generados inicialmente utilizan datos de ejemplo representativos. El análisis del sitio web no conecta automáticamente la aplicación a las API de producción, el inventario activo, los servicios de pago o los sistemas de reserva.

Si la generación informa de un error, lee el error visible y comprueba las [instrucciones para la solución de problemas](/help/reference/troubleshooting.md). No apruebe una acción simplemente para evitar un error de generación.

## Revisar las acciones generadas {#review-generated-actions}

Revise cada acción sugerida antes de la implementación. Confirme que representa una tarea útil y que el comportamiento generado coincide con lo que desea que haga la aplicación.

![Acciones: las acciones de Frescopa generadas están listas para su revisión y no se han implementado](/help/assets/guide-creating-actions/actions-ready-for-review.png)

1. En la página **[!UICONTROL Acciones]**, seleccione **[!UICONTROL Revisar]** para una acción que esté lista.
2. Revise la descripción de la acción, los parámetros de entrada, las anotaciones, la visibilidad y el controlador generado.
3. Revise la configuración y previsualización del widget, si la acción tiene un widget.
4. Inspeccione el código y las pruebas generados, incluidas las solicitudes de extracción vinculadas. Compruebe qué es el comportamiento de muestra y qué debe reemplazarse con integraciones reales.
5. Cuando la acción esté lista, seleccione **[!UICONTROL Marcar como revisado]**.
6. Vuelva a la lista de acciones y repita para las acciones restantes.

El editor de acciones guardadas separa **[!UICONTROL LLM Widget details]**, **[!UICONTROL Action details]** y **[!UICONTROL Analytics]**. Revise las solicitudes de extracción de widget y controlador en sus respectivas pestañas.

![Acción generada: fichas del editor actual y Marcar como revisada, con el vínculo del repositorio oculto](/help/assets/guide-creating-actions/generated-action-review.png)

Al seleccionar **[!UICONTROL Marcar como revisado]**, se combinan las solicitudes de extracción generadas por la acción si no quedan bloqueadores de combinación. Si una solicitud de extracción abierta bloquea la revisión, resuelva el bloqueador y vuelva a intentarlo. No apruebe una implementación con errores no resueltos de combinación o generación de código.

Después de revisar todas las acciones generadas, la página Acciones muestra **[!UICONTROL Todas las acciones son revisadas!]**. Para volver a la aplicación, seleccione **[!UICONTROL Ir a la página de la aplicación]**.

La revisión no es una implementación. Las acciones no estarán disponibles a través del servidor MCP de la aplicación hasta que implemente la aplicación.

## Cree acciones usted mismo {#manual-actions}

Si prefiere no generar acciones desde un sitio web:

1. En la opción de creación de acciones de la página de la aplicación, selecciona **[!UICONTROL Yo mismo crearé acciones]**.
2. Seleccione **[!UICONTROL Crearé mis acciones]**.
3. En la página Acciones, seleccione **[!UICONTROL Crear acción]**.
4. Siga [Crear una acción desde cero](/help/guides/create-action.md) para definir los metadatos, implementar un controlador y conectar un widget si es necesario.

La infraestructura sigue siendo la misma. La elección de la creación manual no crea código de controlador ni conecta automáticamente los sistemas empresariales.

Añada acciones manuales después de revisar las acciones generadas automáticamente según sea necesario.

## Implementación y prueba

Después de revisar el trabajo generado:

1. [Implementar la aplicación](/help/guides/deploy-your-app.md) en **[!UICONTROL Fase]**.
2. Pruébelo en [ChatGPT](/help/guides/test-in-chatgpt.md) o [Claude](/help/guides/test-in-claude.md), utilizando la URL del servidor MCP de ensayo.
3. Mensajes de prueba que coinciden con cada acción y mensajes no relacionados que no llaman a esa acción. Compruebe la respuesta del texto y las interacciones del widget.

Antes de poner la aplicación a disposición de los clientes, [personalice los controladores](/help/guides/customize-handler.md) para conectar las API y [personalizar los widgets](/help/guides/widgets.md). Pruebe la aplicación en Fase para confirmar que funciona según lo esperado. Implementar en producción solo después de que la prueba se haya realizado correctamente.
