---
name: llm-apps-docs
description: Cree, actualice, revise y valide documentación pública y capturas de pantalla de las aplicaciones LLM de Adobe. Utilícelo siempre que edite artículos de llm-apps.en, su TDC de Experience League, la guía del agente de incorporación, los documentos de los widgets EDS, la guía de preparación para la producción o las capturas de pantalla de documentación.
source-git-commit: ca0d8f49a295e6465f2e9b20809e69436bfa93d5
workflow-type: tm+mt
source-wordcount: '716'
ht-degree: 0%

---


# Documentación de aplicaciones LLM

Cree documentación pública verificable y orientada a tareas para aplicaciones LLM de Adobe.

## Orden de veracidad de Source

Compruebe las notificaciones de productos en este orden:

1. IU de producción actual en `https://experience.adobe.com/#/@llmapps/llm-apps/`
2. Implementación actual de la IU y la API disponible en el espacio de trabajo
3. Comportamiento actual de SDK público y plantillas
4. Documentación pública existente

Si la producción entra en conflicto con el origen o los planes, documente la producción e informe de la discrepancia. No publique un flujo de trabajo próximo como el que está disponible actualmente.

## Iniciar cada tarea

1. Leer `help/main-toc/TOC.md`.
2. Lea el artículo objetivo y los artículos directamente relacionados.
3. Clasifique el contenido mediante [content-model.md](content-model.md).
4. Identifique las etiquetas, direcciones URL, comandos y contratos de la interfaz de usuario que requieran verificación.
5. Mantenga la muestra y la terminología completas en todas las páginas.

## Creación de reglas

- Usuarios nuevos de posibles clientes a través del agente de incorporación.
- Organizar la navegación alrededor de los recorridos y resultados del usuario, no de los temas de implementación.
- Establezca la secuencia de recorrido cerca del principio de cada guía y proporcione el siguiente paso compartido.
- Use **Agente de incorporación** para la funcionalidad del producto y la copia exacta de la interfaz de usuario, como **[!UICONTROL Generar mi aplicación automáticamente]** para los controles.
- Explicar un concepto técnico cuando el usuario lo encuentra por primera vez; vincular a un concepto más profundo o material de referencia.
- Mantenga los tutoriales lineales, guías de procedimientos centrados en tareas y páginas de referencia objetivas.
- Incluya solo la información que el lector necesita para la tarea actual; prefiera frases cortas y directas.
- Utilice una aplicación representativa en todo el recorrido.
- Distingue el andamiaje generado de la integración lista para la producción.
- Evite nombres de trabajadores internos, campos de base de datos, tickets de implementación y detalles de canalización inestables.
- No duplique las tablas de campo entre las guías; vincule a la referencia.
- Conservar el frontmatter y las directivas de Experience League: `[!DNL]`, ``, `[!IMPORTANT]`, `[!NOTE]` y `[!TIP]`.
- Use vínculos internos relativos a la raíz: `/help/...`.
- Utilice mayúsculas y minúsculas en los títulos y encabezados a menos que una etiqueta de producto requiera lo contrario.
- Utilice un texto alternativo de imagen descriptivo que explique la pantalla y el estado.

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

Para imágenes nuevas o actualizadas, siga [screenshots.md](screenshots.md) y [screenshot-manifest.md](screenshot-manifest.md).

El flujo de trabajo predeterminado utiliza un paquete de captura de producción creado por el usuario:

1. Busque capturas de pantalla en `docs-captures/<run-id>/` o utilice la carpeta proporcionada por el usuario.
2. Haga un inventario e inspeccione visualmente cada archivo PNG, JPEG y WebP; no confíe solo en su nombre de archivo.
3. Haga coincidir las capturas de pantalla con los estados de manifiesto mediante contenido de IU visible.
4. Antes de cambiar la documentación, informe de las capturas que faltan, duplicadas, ambiguas, antiguas o inseguras.
5. Conservar las capturas de origen sin cambios.
6. Cree copias finales saneadas con los nombres de archivo del manifiesto estable bajo `help/assets/`.
7. Actualice el tutorial y las guías relacionadas para que coincidan con el flujo de trabajo de producción real capturado.
8. Añada texto alternativo preciso y ejecute la validación de la documentación.

La captura del explorador guiada por el agente sigue siendo una alternativa opcional. No almacene el estado ni las credenciales del explorador y no ejecute la captura de pantalla de producción-mutación en CI.

Cuando se le pida &quot;actualizar documentos a partir de capturas de pantalla&quot;:

- Trate la carpeta de captura seleccionada explícitamente más reciente como origen.
- Pregunte solo cuando el flujo de la aplicación o la asignación de capturas de pantalla sean genuinamente ambiguos.
- Nunca confirme carpetas de captura sin procesar.
- Nunca elimine ni modifique las capturas de origen sin una aprobación explícita.
- Si no se puede eliminar la información confidencial sin ocultar la tarea, solicite una recuperación segura.

## Crear un archivo de revisión

Genere un sitio de HTML y un archivo ZIP que se puedan compartir sin conexión:

```bash
node .cursor/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

La compilación se escribe junto al repositorio, no dentro de él. Solo incluye
artículos publicados y activos saneados, convierte directivas de Experience League
para su revisión sin conexión, y observa que su estilo no es la experiencia final
Procesamiento de Liga.

## Validate

Ejecutar:

```bash
python3 .cursor/skills/llm-apps-docs/scripts/validate_docs.py
```

Corrija todos los artículos internos que faltan, los recursos que faltan, las rutas relativas a la raíz no válidas y los campos de frontmatter que faltan antes de entregarse.

Compruebe también:

- Las etiquetas y capturas de pantalla de IU coinciden con Producción.
- Los ejemplos de URL de scripts y widgets coinciden en las guías y la referencia.
- Los comandos coinciden con la plantilla actual.
- Las páginas nuevas se vinculan desde el índice.
- El flujo de trabajo de validación de artículos de Adobe pasa cuando está disponible.

## Referencias de soporte

- [Modelo de contenido y terminología](content-model.md)
- [Procedimiento de captura de pantalla Producción](screenshots.md)
- [Manifiesto de captura de pantalla](screenshot-manifest.md)
