---
title: Actualizar una aplicación existente
description: Actualice el tiempo de ejecución de una aplicación LLM de Adobe anterior y EDS SDK antes de habilitar Adobe Analytics o Customer Journey Analytics.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# Actualizar una aplicación existente {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta guía solo si su aplicación utiliza `@adobe/llm-apps-runtime` anterior a la versión 1.2.0 o si sus widgets EDS utilizan la SDK de aplicaciones LLM anterior a la versión 0.1.13. Las aplicaciones que ya cumplan ambos requisitos pueden continuar directamente a [Configurar Adobe Analytics y Customer Journey Analytics](/help/guides/analytics-setup.md).

**En esta guía:** Aprenda a actualizar y comprobar el tiempo de ejecución y la SDK de EDS de una aplicación anterior. A continuación, vuelva a la guía de configuración de Analytics para habilitar Analytics y verificar la recopilación.

## Antes de empezar {#before-you-begin}

Necesita acceso al repositorio de controladores y, en el caso de las aplicaciones con widgets EDS, al repositorio EDS vinculado a su aplicación. Si su aplicación utiliza una infraestructura administrada de Adobe, solicite acceso al código antes de realizar las actualizaciones. Consulte [Usar la infraestructura administrada de Adobe](/help/guides/create-app.md#adobe-managed-infrastructure).

No es necesario crear otra aplicación. Actualice solo los componentes que estén por debajo de las versiones compatibles. Puede reutilizar la configuración existente de Adobe que cumpla los [requisitos de instalación de Analytics](/help/guides/analytics-setup.md#setup-checklist).

En el caso de las aplicaciones con widgets EDS, publique y compruebe primero la actualización de SDK y, a continuación, combine la actualización en tiempo de ejecución y vuelva a implementar la aplicación. Las dos actualizaciones del repositorio se pueden preparar de forma independiente, pero ambas deben estar disponibles antes de probar la implementación habilitada para análisis.

## Comprobar y actualizar el repositorio de acciones de aplicaciones LLM {#upgrade-runtime}

1. Abra el repositorio de controladores vinculado a la aplicación e instale sus dependencias:

   ```bash
   npm install
   ```

1. Compruebe el tiempo de ejecución instalado:

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. Si el tiempo de ejecución es anterior a 1.2.0, instale la versión actual:

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   Confirme que la versión instalada sea la 1.2.0 o posterior. Conserve una versión compatible posterior si la aplicación ya utiliza una.

1. Confirme que la dependencia de tiempo de ejecución y su versión resuelta cumplen los requisitos mínimos. La versión propia de la aplicación es independiente de la dependencia del tiempo de ejecución.
1. Ejecute las comprobaciones del repositorio. Para la plantilla de aplicaciones LLM:

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   Investigue los errores antes de implementar. Si una comprobación ya falla en la versión original, registre esa línea base por separado de cualquier error introducido por la actualización. Una compilación local correcta no verifica la colección de análisis.

1. Confirme la actualización de dependencia y su archivo de bloqueo y, a continuación, inserte y combine la actualización en la rama predeterminada del repositorio del controlador, normalmente `main`. La implementación clona esa rama. Una actualización que solo existe localmente o en una rama de características sin combinar no se incluye en la implementación.

   Cuando existe un archivo de bloqueo, la implementación instala las dependencias con `npm ci`, que utiliza las versiones registradas. La reimplementación no actualiza el tiempo de ejecución automáticamente. Antes de volver a implementar, confirme la dependencia actualizada y la versión resuelta en la rama predeterminada en GitHub.

## Compruebe y actualice el repositorio del widget de aplicaciones LLM {#upgrade-sdk}

Para las aplicaciones con widgets EDS, compruebe el SDK en el repositorio EDS, así como el tiempo de ejecución del servidor. Al actualizar un repositorio no se actualiza el otro.

Algunos repositorios EDS incluyen scripts de SDK copiados sin una dependencia npm. El explorador carga los scripts servidos directamente, por lo que la ausencia de una entrada de paquete no significa que falte el SDK. Actualice la distribución de SDK copiada mediante el proceso de actualización de SDK del proyecto. No es necesario agregar una dependencia npm persistente en un proyecto que mantenga SDK como scripts copiados.

Para proyectos que administran SDK a través de npm:

1. Desde la raíz del repositorio de EDS, instale el SDK actual y confirme su versión:

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   Utilice 0.1.13 o posterior. Mantener una versión compatible posterior si el proyecto ya utiliza una.

1. Compruebe que la instalación ha actualizado la distribución de SDK servida. El vínculo de instalación sobrescribe los scripts de SDK copiados, por lo que debe revisar las personalizaciones locales antes de confirmarlas. Al actualizar sólo la declaración de dependencia no se actualizan los scripts a los que sirve EDS. Consulte [Instalar el SDK de aplicaciones LLM](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk).
1. Ejecute las comprobaciones del repositorio de EDS, como `npm run lint`, y compruebe que un widget existente sigue procesando y respondiendo a las interacciones.
1. Confirme la distribución de SDK actualizada junto con las actualizaciones de dependencia y archivo de bloqueo. EDS sirve los scripts copiados, no el paquete npm instalado directamente.

Después de usar cualquiera de los métodos de actualización, inserta y combina la actualización en la rama EDS que usa la **[!UICONTROL URL de script]** de la acción. Compruebe que la dirección URL sirve al SDK actualizado y que sus bibliotecas de compatibilidad se cargan correctamente. Si la dirección URL utiliza el sitio activo de EDS, complete los pasos de publicación para ese sitio.

## Siguiente paso {#next-step}

Volver a [Configurar Adobe Analytics y Customer Journey Analytics](/help/guides/analytics-setup.md). Todas las aplicaciones deben habilitar Analytics e implementarse para recopilar eventos; la actualización del tiempo de ejecución o de SDK por sí sola no habilita la recopilación.

Después de guardar la configuración de Analytics, implemente en Fase y [compruebe los nuevos eventos de interacción de widget y llamada de acción](/help/guides/analytics-setup.md#verify) antes de implementar en Producción. Confirme que la acción y el widget siguen funcionando y que una sola selección de vínculo produce un evento de interacción de widget.

Analytics comienza a recopilar nuevas interacciones después de la implementación habilitada para Analytics. La actualización no reconstruye las interacciones que se produjeron antes de habilitar la recopilación.
