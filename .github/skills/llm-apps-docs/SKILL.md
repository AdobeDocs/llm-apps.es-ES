---
name: llm-apps-docs
description: Cree, revise y valide documentación de aplicaciones LLM de Adobe, aplique sus estándares editoriales y capture capturas de pantalla de la interfaz de usuario actuales mediante la automatización del explorador. Artículos, navegación, capturas de pantalla y paquetes de revisión sin conexión de llm-apps.en.
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '1380'
ht-degree: 0%
---

# Documentación de aplicaciones LLM

Cree documentación pública verificable y orientada a tareas para aplicaciones LLM de Adobe.

Esta aptitud de copiloto local del repositorio se encuentra en `.github/skills/llm-apps-docs/`.

## Orden de veracidad de Source

Utilice la interfaz de usuario y el entorno en ejecución que especifique el usuario. De lo contrario, el valor predeterminado es
Producción en `https://experience.adobe.com/llm-apps`.
Compruebe las notificaciones de productos en este orden para ese entorno:

1. La interfaz de usuario actual, observada mediante la automatización del explorador.
2. La IU e implementación de API coincidentes en el espacio de trabajo.
3. SDK público actual, tiempo de ejecución y repeticiones.
4. Documentación pública existente

Registre la fecha de captura y el entorno de origen en el manifiesto de captura de pantalla. No hacer
presentar observaciones locales o de fase como comportamiento de producción verificado. Si la interfaz de usuario,
implementación, y las directrices publicadas no coinciden, notificar la discrepancia y describir
solo el comportamiento que respaldan las pruebas disponibles.

## Iniciar cada tarea

1. Leer `help/main-toc/TOC.md`.
2. Lea el artículo objetivo y los artículos directamente relacionados.
3. Clasifique el contenido mediante [content-model.md](content-model.md).
4. Identifique las etiquetas, direcciones URL, comandos y contratos de la interfaz de usuario que requieran verificación.
5. Mantenga la muestra y la terminología completas en todas las páginas.
6. Cuando la tarea necesite verificación de la interfaz de usuario o capturas de pantalla, inspeccione la interfaz de usuario en ejecución y capture las imágenes usted mismo. Pida al usuario que autentique o apruebe los cambios de recursos, no que combine un paquete de capturas de pantalla.

## Creación de reglas

- Dirija a los usuarios por primera vez a través de **Configuración de infraestructura**, luego **Crear acciones**. El asistente para crear aplicaciones configura solo la infraestructura; **[!UICONTROL Generar acciones automáticamente]** y **[!UICONTROL Generar mis acciones]** iniciará la generación de acciones basada en el sitio web más tarde, desde la página de la aplicación. Use **infraestructura administrada de Adobe** para la opción con la etiqueta **[!UICONTROL Permítanos manejarla]** en la interfaz de usuario.
- Describa la infraestructura gestionada por Adobe de forma neutral a medida que Adobe crea y gestiona la infraestructura. No lo etiquete como una opción de prueba, de configuración temporal o que no sea solo de producción, y no haga declaraciones no confirmadas sobre la idoneidad de la producción.
- Organizar la navegación alrededor de los recorridos y resultados del usuario, no de los temas de implementación.
- Cerca del principio de cada guía, use **En esta guía:** seguido de una o dos frases en lenguaje sencillo que expliquen la tarea y el paso siguiente. Preferir &quot;Aprenda a...&quot; por encima de las promesas en el futuro. No utilice **Recorrido:** etiquetas o listas de pasos separadas por flechas.
- No utilice nombres de código internos (por ejemplo, &quot;Agente de incorporación&quot;) en documentos de cliente; esa capacidad nunca se expone en la interfaz de usuario del producto. Descríbalo genéricamente (por ejemplo, &quot;la plataforma&quot;) y usa la copia exacta de la interfaz de usuario actual, como **[!UICONTROL Generar mis acciones]** para los controles.
- [!DNL Adobe LLM Apps] no depende de la plataforma: su servidor MCP funciona con cualquier plataforma LLM admitida, no solo con [!DNL ChatGPT]. No diga afirmaciones generales o ilustrativas como si [!DNL ChatGPT] fuera el único destino (por ejemplo, prefiera &quot;una plataforma LLM compatible como [!DNL ChatGPT]&quot; en lugar de &quot;ChatGPT&quot; solo). Solo asigne un nombre explícito a [!DNL ChatGPT] en el contenido que sea genuino y específico actualmente de [!DNL ChatGPT]: la [Guía de Test in ChatGPT](/help/guides/test-in-chatgpt.md) dedicada, sus vínculos cruzados directos/pasos de procedimientos y la referencia específica de [!DNL ChatGPT] para la resolución de problemas del contenido.
- Explicar un concepto técnico cuando el usuario lo encuentra por primera vez; vincular a un concepto más profundo o material de referencia.
- Mantenga los tutoriales lineales, guías de procedimientos centrados en tareas y páginas de referencia objetivas.
- Incluya solo la información que el lector necesita para la tarea actual; prefiera frases cortas y directas.
- Utilice una aplicación representativa en todo el recorrido.
- Distingue el andamiaje generado de la integración lista para la producción.
- Evite nombres de trabajadores internos, campos de base de datos, tickets de implementación y detalles de canalización inestables.
- No duplique las tablas de campo entre las guías; vincule a la referencia.
- Conservar el frontmatter y las directivas de Experience League: `[!DNL]`, &grave;&grave;, `[!IMPORTANT]`, `[!NOTE]` y `[!TIP]`.
- Use vínculos internos relativos a la raíz: `/help/...`.
- Utilice mayúsculas y minúsculas en los títulos y encabezados a menos que una etiqueta de producto requiera lo contrario.
- Utilice un texto alternativo de imagen descriptivo que explique la pantalla y el estado.

## Estándares editoriales

- Dirigirse al lector directamente y usar instrucciones imperativas: &quot;Seleccionar Guardar&quot;, no &quot;Puede seleccionar Guardar&quot;.
- Coloque el objetivo antes de la acción cuando el propósito necesite explicación: &quot;Para agregar un parámetro, seleccione Agregar&quot;. Evite las cláusulas de propósito redundantes.
- Prefiere la voz activa y el tiempo presente. Asigne un nombre al actor cuando esto sea importante: &quot;La plataforma genera el controlador&quot;.
- Reemplace referencias ambiguas como &quot;this&quot;, &quot;these&quot; o &quot;it&quot; por un sustantivo específico cuando la referencia no sea clara.
- Guarde una idea principal por frase. Reduzca las frases largas y divida las cláusulas incrustadas cuando el detalle adicional oscurezca la tarea. Conservar las condiciones y advertencias necesarias.
- Reduzca las cadenas preposicionales y las frases denominativas densas. Prefiera &quot;configurar variables de aplicación para Stage&quot; en lugar de &quot;realizar la configuración de las variables para la aplicación en Stage&quot;.
- Utilice palabras sencillas en lugar de expresiones idiomáticas, lenguaje figurativo o frases innecesarias. Explique términos técnicos desconocidos cuando el lector los encuentre por primera vez.
- Compruebe el orden de las palabras, la estructura de las oraciones, las palabras que faltan o están duplicadas, la puntuación y el uso de mayúsculas en el inicio de la oración. Mantenga los elementos de la lista gramaticalmente paralelos.
- Haga coincidir los nombres de productos oficiales con las mayúsculas: Adobe LLM Apps, Adobe I/O Runtime, Edge Delivery Services, GitHub, ChatGPT, Claude y Model Context Protocol (MCP). Conservar las etiquetas exactas de la interfaz de usuario y la ortografía del código incluso cuando difieran de las convenciones en prosa.
- Use un lenguaje inclusivo y respetuoso. Evite los estereotipos, los supuestos de género, las metáforas estigmatizantes y las palabras que suponen que cada lector tiene las mismas habilidades o experiencia. Utilice términos como &quot;lista de permitidos&quot; y &quot;lista de bloqueados&quot; cuando sea preciso; no cambie el nombre de las API literales, ramas, comandos ni identificadores de código para una preferencia editorial.
- Mantenga los verbos modales cuando expresen la opcionalidad, las capacidades, los permisos, las condiciones, la incertidumbre o la variación del host reales. No convertir una estimación en una garantía o una recomendación en un requisito.
- Conservar las etiquetas, los comandos, los ejemplos de código, los identificadores, los contratos técnicos y la copia aprobada por el módulo de administración durante una fase editorial.
- Utilice conclusiones automatizadas como señales de revisión, no sustituciones obligatorias. Busque la frase marcada, corrija el problema real o mantenga una redacción significativa con un motivo. Una categoría gramatical genérica o una puntuación acumulada no son suficientes para identificar un error específico. No solicite una puntuación mejorada sin ejecutar el mismo evaluador.

## Narrativa protegida aprobada por el Primer Ministro

En `help/overview/overview.md`, estas secciones están aprobadas por el PM:

- **Lo que puedes hacer con las aplicaciones LLM**
- **Por qué las aplicaciones LLM importan**

Conservar sus encabezados, puntos de viñeta, redacción, orden y afirmaciones literalmente.
No los acorte, reescriba, reorganice ni elimine durante la documentación general
actualizaciones. Cambie cualquiera de las secciones solo cuando el usuario la solicite explícitamente y
confirma que la nueva copia está aprobada por el módulo de administración.

## Requisitos de seguridad

- Nunca incluya credenciales, tokens, direcciones URL privadas, datos personales, nombres de host internos ni identificadores de cliente.
- Mostrar secretos cargados desde la configuración administrada, nunca codificados.
- Requiere HTTPS para servicios externos.
- Validar respuestas de entrada y de subida externas.
- Procesar valores externos con API DOM seguras; no se recomienda interpolarlos en `innerHTML`.
- Recomendar los permisos menos privilegiados de la aplicación, la API, el CSP, CORS y el explorador de GitHub.
- Utilice errores seguros de cara al usuario y evite registrar datos confidenciales.

## Flujo de trabajo de captura

Obtenga capturas de pantalla con la automatización del explorador disponible. Seguir
[screenshots.md](screenshots.md) para la configuración del navegador, autenticación, navegación segura,
comprobaciones de estabilidad, desinfección y revisión de imágenes. Uso
[screenshot-manifest.md](screenshot-manifest.md) para los puntos de comprobación y nombres de archivo actuales.

1. Abra la interfaz de usuario en ejecución solicitada y haga una pausa para que el usuario se autentique.
2. Reutilice un accesorio de documentación aprobado e inspeccione los estados de interfaz de usuario necesarios.
3. Capturar capturas de pantalla estables en el directorio `docs-captures/<run-id>/` omitido. No espere a que el usuario cree un paquete de captura.
4. Recortar o enmascarar copias seguras para `help/assets/` y, a continuación, inspeccionar visualmente cada imagen final.
5. Haga coincidir el artículo, las etiquetas exactas de la interfaz de usuario y el texto alternativo con el estado observado.
6. Valide los vínculos y los recursos e inspeccione el sitio de revisión procesado.

Pregunte antes de crear aplicaciones o repositorios, iniciar la generación y validar un
repositorio que puede recibir archivos de plantillas, guardar configuraciones, combinar archivos generados
solicitudes de extracción, implementación, eliminación o cambio del acceso. Mantener autenticación y
consentimiento privilegiado con el usuario. Nunca inspeccionar ni exportar credenciales, tokens o
cookies o almacenamiento del explorador, y nunca ejecute un flujo de captura mutante sin aprobación.

Si la automatización, el acceso o un estado requerido no están disponibles, notifique el
bloqueador. No invente una captura de pantalla, reutilice silenciosamente una imagen obsoleta o presente
verificación solo de origen como una nueva captura de la IU. Conservar las capturas de origen existentes.

## Crear un archivo de revisión

Genere un sitio de HTML y un archivo ZIP que se puedan compartir sin conexión:

```bash
node .github/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

La compilación se escribe junto al repositorio, no dentro de él. Solo incluye
artículos publicados y activos saneados, convierte directivas de Experience League
para su revisión sin conexión, y observa que su estilo no es la experiencia final
Procesamiento de Liga.

## Validate

Ejecutar:

```bash
python3 .github/skills/llm-apps-docs/scripts/validate_docs.py
```

Corrija todos los artículos internos que faltan, los recursos que faltan, las rutas relativas a la raíz no válidas y los campos de frontmatter que faltan antes de entregarse.

Compruebe también:

- Las etiquetas de interfaz de usuario y las capturas de pantalla coinciden con el entorno de origen registrado.
- Los ejemplos de URL de scripts y widgets coinciden en las guías y la referencia.
- Los comandos coinciden con la plantilla actual.
- Las páginas nuevas se vinculan desde el índice a menos que se marquen `hidefromtoc: true` intencionalmente.
- El flujo de trabajo de validación de artículos de Adobe pasa cuando está disponible.

## Referencias de soporte

- [Modelo de contenido y terminología](content-model.md)
- [Procedimiento de captura de pantalla impulsado por agente](screenshots.md)
- [Manifiesto de captura de pantalla](screenshot-manifest.md)
