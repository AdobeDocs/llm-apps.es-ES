---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '387'
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

Tutorial principal: `help/guides/create-app.md`.

### Concepto

Explica cómo se relacionan las piezas sin convertirse en un procedimiento o catálogo de campos.

- Céntrese en modelos mentales y límites de propiedad.
- Utilice un pequeño diagrama cuando mejore la comprensión.
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
- **Agente de incorporación** — capacidad que crea el andamio inicial.
- **Crear mi aplicación** — sección IU en el cuadro de diálogo Crear aplicación.
- **Crear mi aplicación automáticamente**: etiqueta de casilla de verificación exacta.
- **Acción**: capacidad expuesta a la plataforma LLM.
- **Metadatos de acción**: nombre, descripción, esquema, anotaciones, visibilidad y configuración de widget almacenados por las aplicaciones LLM.
- **Controlador de acciones**: función del lado del servidor en el repositorio del controlador.
- **Repositorio de controladores**: repositorio que contiene controladores y pruebas. Use la etiqueta de interfaz de usuario **Repositorio de plantillas** solo cuando describa ese control.
- **Repositorio de EDS**: el repositorio que contiene bloques de widgets y contenido.
- **Widget** — respuesta visual representada en la plataforma LLM.
- **URL del servidor MCP**: extremo implementado registrado con una plataforma LLM.
- **Complemento ChatGPT**: la integración de ChatGPT creada a partir de la URL de un servidor MCP.
- **Ensayo** y **Producción**: entornos de implementación.

Evite cambiar entre &quot;herramienta&quot; y &quot;acción&quot; en prosa orientada al usuario a menos que se explique el detalle de un protocolo MCP.

## Recorrido de lector recomendado

1. Información general y requisitos previos.
2. Cree una aplicación con el agente de incorporación.
3. Revise las acciones generadas.
4. Implemente para pruebas el complemento ChatGPT.
5. Personalice los controladores y widgets generados.
6. Implemente la aplicación personalizada en Producción.

Crear una acción desde cero y traer un proyecto EDS son ramas avanzadas, no el recorrido predeterminado de primera ejecución.
