---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '701'
ht-degree: 0%
---
# Modelo de contenido y terminología

## Tipos de contenido

### Tutorial

Enseña a un nuevo usuario a través de un recorrido completo y correcto.

- Indique el resultado y los requisitos previos.
- Utilice una aplicación de ejemplo y una secuencia.
- Explicar solo los conceptos necesarios en cada paso.
- Termine con un resultado de trabajo y borre los pasos siguientes.

Recorrido de primera ejecución: `help/guides/create-app.md` (configuración de infraestructura), luego `help/guides/creating-actions.md` (generación y revisión de acciones).

### Concepto

Explica cómo se relacionan las piezas sin convertirse en un procedimiento o catálogo de campos.

- Céntrese en modelos mentales y límites de propiedad.
- Explique lo que hace cada parte, cómo lo experimenta el usuario y dónde realizar cambios.
- Mantenga las herramientas de generación, las rutas de acceso a archivos, las llamadas de protocolo y los contratos de código en las guías técnicas o referencias vinculadas, no en la descripción general.
- Utilice un pequeño diagrama cuando mejore la comprensión.
- Conservar los diagramas útiles existentes al simplificar una página; simplificar las etiquetas en lugar de eliminar la explicación visual.
- Vínculo a tutoriales, guías de procedimientos y referencias.

### Guía de procedimientos

Ayuda a un usuario informado a completar una tarea.

- Comience con el resultado deseado.
- Incluya solo los requisitos previos específicos de la tarea.
- Preferir una ruta recomendada.
- Vínculo de referencia para campos exhaustivos.

Ejemplos: crear una acción desde cero, personalizar un widget, traer un proyecto EDS, implementar y probar.

### Referencia

Proporciona información objetiva que los usuarios consultan mientras trabajan.

- Organizar por la superficie de producto o código.
- Defina cada campo, contrato, comando, límite y estado con precisión.
- Evite la narración del tutorial y los ejemplos repetidos.

### Resolución de problemas

Comienza por un síntoma observable.

- Describir las causas probables.
- Dé pasos de diagnóstico seguros.
- Evite pedir a los usuarios que revelen credenciales o registros confidenciales.

## Terminología canónica

- **Aplicaciones LLM de Adobe**: nombre completo del producto en la primera mención.
- **Aplicación LLM**: una aplicación administrada por el producto.
- **Infraestructura administrada de Adobe**: infraestructura de prueba creada y alojada por Adobe; seleccione **Permítanos manejarla** en el asistente para crear aplicaciones.
- **Lo configuraré yo mismo**: opción de asistente para conectar sus propios repositorios, no es un requisito para escribir acciones manualmente. La ruta predeterminada comienza con dos repositorios vacíos que la plataforma inicializa; los proyectos existentes compatibles son opcionales.
- **Generar acciones automáticamente**: opción de creación de acciones recomendada en la página de la aplicación después de configurar la infraestructura.
- **Generar mis acciones**: botón que inicia la generación de acciones basadas en el sitio web.
- **Acción**: capacidad expuesta a la plataforma LLM.
- **Metadatos de acción**: nombre, descripción, esquema, anotaciones, visibilidad y configuración de widget almacenados por las aplicaciones LLM.
- **Visibilidad**: el conmutador de activación/desactivación de la acción de la página de la aplicación. Controla si la siguiente implementación incluye la acción. El editor de acciones no tiene un conmutador habilitado independiente; la configuración de exposición del modelo y el widget tiene un propósito diferente.
- **Controlador de acciones**: función del lado del servidor en el repositorio del controlador.
- **Identificador de código**: identificador de acción inmutable utilizado en las llamadas MCP, distinto del nombre para mostrar. Los nombres de controladores y carpetas de bloques generados utilizan un formulario con guiones; el motor en tiempo de ejecución también resuelve guiones bajos en el identificador en guiones en las rutas de controladores.
- **Repositorio de controladores**: repositorio que contiene controladores y pruebas, seleccionado durante la **configuración de la infraestructura de acción** al conectar sus propios repositorios.
- **Repositorio de EDS**: el repositorio que contiene bloques de widgets y contenido.
- **Widget** — respuesta visual representada en la plataforma LLM.
- **`structuredContent`**: datos estructurados de resultados de acciones que usa el widget. No lo describa como privado, invisible para el modelo o sin token; forma parte del resultado de MCP que se entrega al host.
- **URL del servidor MCP**: extremo implementado registrado con una plataforma LLM.
- **Complemento ChatGPT**: la integración de ChatGPT creada a partir de la URL de un servidor MCP.
- **Ensayo** y **Producción**: entornos de implementación.

Evite cambiar entre &quot;herramienta&quot; y &quot;acción&quot; en prosa orientada al usuario a menos que se explique el detalle de un protocolo MCP.

El producto no depende de la plataforma: su servidor MCP funciona con cualquier plataforma LLM admitida, no solo con ChatGPT. Utilice &quot;una plataforma LLM compatible como ChatGPT&quot; (o similar) para reclamaciones generales o ilustrativas. Asigne un nombre a ChatGPT solo en el contenido que sea genuinamente específico de ChatGPT hoy en día: la guía Test in ChatGPT, sus vínculos cruzados directos y la referencia específica de ChatGPT para la resolución de problemas del contenido.

## Secuencia de lector recomendada

1. Información general.
2. Cree una aplicación con una infraestructura administrada de Adobe o sus propios repositorios.
3. Inicie la generación de acciones basadas en sitios web desde la página de la aplicación y revise las acciones generadas.
4. Implementar en fase y probar en una plataforma LLM compatible.
5. Personalice los controladores y widgets generados.
6. Implemente la aplicación personalizada en Producción.

Crear una acción desde cero y traer un proyecto EDS son ramas avanzadas, no el recorrido predeterminado de primera ejecución.

La guía de BYO EDS está oculta actualmente del índice con `hidefromtoc: true`.
Conservar ese estado a menos que el usuario pida restaurar la entrada de navegación.
