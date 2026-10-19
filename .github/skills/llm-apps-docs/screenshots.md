---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1109'
ht-degree: 0%
---
# Procedimiento de captura de pantalla impulsado por agente

Capture la interfaz de usuario en ejecución con la automatización del explorador. El usuario proporciona lo siguiente
autenticación y aprueba cambios de recursos; el agente navega, captura,
sanea e integra las imágenes. No requiere un ensamblado manualmente
paquete de captura de pantalla antes de empezar.

## Elija el entorno

Utilice la URL que proporcione el usuario. De lo contrario, utilice Producción:

`https://experience.adobe.com/llm-apps`

Registre la fecha de captura, el entorno y el estado observado en
[screen-manifest.md](screenshot-manifest.md). Una captura local o de fase no
Compruebe el comportamiento de producción. Comparar la interfaz de usuario de con la implementación correspondiente y
informar de los conflictos en lugar de presentar un flujo no verificado como publicado.

## Apertura y autenticación del explorador

1. Compruebe que la IU solicitada se esté ejecutando. No inicie un servidor duplicado.
2. Utilice la automatización del explorador disponible. Reutilizar una sesión del explorador solo cuando su
la propiedad y el ámbito de acceso son claros.
3. Si la automatización no se puede adjuntar, abra un explorador independiente bajo el control del agente.
Para Chrome, utilice un perfil de sesión dedicado fuera del repositorio y enlácelo
depuración remota a `127.0.0.1` en un puerto local no utilizado. Nunca copie el del usuario
perfil personal del explorador o exponer el puerto de depuración a la red.
4. Compruebe que el explorador se está ejecutando y que su punto final de automatización responde.
5. Abra la aplicación y haga una pausa para que el usuario se autentique. El usuario gestiona Adobe,
GitHub y credenciales de plataforma LLM, MFA, claves de paso, captchas, organización
selección y consentimiento privilegiado.
6. Reanudar después de que el usuario confirme la autenticación. Con el dramaturgo, adjuntar a través de
   `chromium.connect_over_cdp()` al usar una sesión de depuración de Chrome controlada.
7. Busque el marco de la aplicación por su origen o contenido visible. Unified Shell puede colocar
la aplicación dentro de un iframe; no se debe suponer un índice de fotograma fijo.

Nunca lea, imprima, exporte ni confirme credenciales, tokens, cookies ni exploradores
almacenamiento. No llame a `storage_state()` ni inspeccione los encabezados de autenticación. Deje que el
el explorador gestiona el inicio de sesión sin extraer su estado.

## Inspeccione y navegue con seguridad

- Prefiera localizadores semánticos como `get_by_role()` y `get_by_label()`. Inspeccionar
el árbol de accesibilidad y el texto visible antes de elegir un control.
- Compruebe las etiquetas exactas de la IU en la pantalla representada. El cuadro de diálogo de creación
el editor de acciones guardadas tiene diferentes pestañas; no inferir una de la otra.
- Reutilice una aplicación solo de documentación aprobada siempre que sea posible. Use un público,
sitio web no confidencial y contenido de accesorios reconocible.
- Rellene los formularios no guardados con valores de ejemplo solo después de confirmar que el formulario
no guarda automáticamente. Cancele o descarte esos cambios después de la captura y, a continuación, verifique
Compruebe que la configuración guardada permanece sin cambios.
- Preguntar antes de crear aplicaciones o repositorios, iniciar la generación de acciones,
validar un repositorio que puede recibir archivos de plantillas, guardar la configuración,
agregar o eliminar variables, combinar solicitudes de extracción, implementar, eliminar o
archivar recursos o cambiar el acceso al repositorio.
- Tratar **Marcar como revisado** como una mutación del repositorio porque puede combinar la extracción
solicitudes. Abrir la pantalla de revisión no autoriza la aprobación.
- Mantenga el acceso a la aplicación de GitHub limitado a los repositorios de accesorios. Solicitar
aprobación del administrador cuando sea necesario.
- No apruebe acciones ni implemente una aplicación simplemente para obtener una captura de pantalla opcional.
Capture estados accesibles e informe del estado que falta.

Cuando se necesiten nuevos recursos, confirme la organización de destino, el sitio web o
opción de infraestructura, programa de AEM y nombre de la sujeción antes de crearlos. Para
Infraestructura gestionada por Adobe, permita que la plataforma cree los repositorios. Para el
ruta del repositorio propio, utilice dos repositorios vacíos y obtenga la aprobación antes de
la validación los inicializa.

## Captura de estados de IU estables

1. Elija los puntos de comprobación relevantes en el manifiesto. Recopilar decisiones y
resultados comprobables, no cada clic.
2. Utilice una ventanilla móvil de escritorio que se ajuste al panel correspondiente, al zoom del 100 % y a la
tema predeterminado del producto.
3. Espere a que se establezca el contenido, las fuentes, los menús y el diseño de diálogo esperados.
Unified Shell cambia el tamaño de su iframe en torno a los cuadros de diálogo, por lo que solo es visible un encabezado
no prueba que la región de captura sea estable.
4. Confirme que la región de destino permanece en la misma posición y tamaño. Esperar a
carga de superposiciones, animaciones de salida, mensajes emergentes y transiciones desactivadas a activadas
para finalizar a menos que uno de esos estados sea el asunto de la imagen.
5. Aleja el enfoque de las entradas y cierra los menús no relacionados cuando no lo hace
cambiar el estado documentado.
6. Capturar la región completa más pequeña que incluya la tarea y
contexto. Prefiera un panel o una captura de pantalla de diálogo sobre una imagen de escritorio completa.
7. Con el dramaturgo, use capturas de pantalla del localizador y `animations='disabled'`, además de
máscaras de tiempo de captura para valores confidenciales. Inspeccione el PNG resultante; el
La API de captura de pantalla que se realiza correctamente no prueba que la región sea correcta.

No modifique nunca el DOM, ni vuelva a pintar las etiquetas, ni cambie el aspecto de un control para crear un
captura de pantalla: listo o exitoso.

## Almacenar y sanear imágenes

Guarde cada ejecución en el directorio Git-ignore:

```text
docs-captures/<run-id>/
```

Usar nombres de archivo descriptivos como `setup-choice.png`, `action-basic-info.png`,
y `generated-action-review.png`. Conservar las capturas de origen sin cambios. Mantener sin procesar
captura y revisa perfiles de explorador a partir de confirmaciones y archivos.

Cree copias finales en los directorios de salida del manifiesto:

- Recorte el navegador Chrome, la navegación de la cuenta, las aplicaciones no relacionadas y el lienzo vacío.
- Excluir los secretos de la captura por completo, incluidas las capturas sin procesar.
- Enmascarar nombres personales, avatares, identificadores de organización e instalación, privados
detalles del repositorio, áreas de nombres de tiempo de ejecución y direcciones URL completas del servidor MCP. Preferir sólido
máscaras neutras para desenfocar, lo que puede dejar valores recuperables.
- Use valores seguros como `https://auth.example.com`, `catalog:read`,
  `GREETING_PREFIX` y `Good day` en formularios de ejemplo no guardados.
- Excluir los datos de contacto generados y otros datos de muestra de aspecto personal cuando
ese contenido no es necesario para el paso.
- Conservar el estado real de la IU. Recortar o redactar contenido confidencial, no errores.
advertencias o funcionalidades que faltan y que el artículo debe explicar.
- Reutilice nombres de archivo públicos estables al actualizar un punto de comprobación existente.

No elimine capturas de origen, perfiles de explorador ni recursos de sujeción sin
aprobación. No elimine ningún perfil de explorador activo mientras se esté ejecutando el explorador.

## Revisar e integrar

Para cada imagen final:

1. Abra la imagen e inspect de manera visual.
2. Compruebe que el panel deseado, las etiquetas, el estado y los controles relevantes estén visibles.
3. Compruebe si hay texto recortado, regiones vacías o incorrectas, superposiciones de carga o transitorias
menús y detalles de la cuenta.
4. Confirme que el artículo describe el flujo de trabajo observado y que el texto alternativo
identifica la pantalla y el estado.
5. Actualice el manifiesto con el entorno de captura, la fecha y cualquier límite de
verificación.
6. Ejecute el validador de documentación y cree el sitio de revisión con los comandos
en `SKILL.md`.
7. Inspeccione el artículo procesado y confirme que cada imagen se carga en un tiempo legible
tamaño.

Para plataformas de terceros, utilice un explorador autenticado solo dentro del
ámbito aprobado. Si el acceso no está disponible, comprobar las instrucciones con los valores actuales
documentación oficial del proveedor, omitir capturas de pantalla de configuración no verificadas y estado
que esas pantallas no fueron capturadas. No presentar una configuración de terceros antigua
imagen como actual.

Si el usuario proporciona imágenes, inspecciónelas y compruébelas antes de volver a utilizarlas. Ellos
son material de origen adicional, no un requisito previo para el flujo de trabajo de captura.

## Bloqueadores de informes

Pausar para la autenticación, el consentimiento o un cambio de recurso no aprobado. Informe
errores de automatización, estados no disponibles, capturas no seguras, validación del repositorio
errores, errores de generación y discrepancias explícitamente entre la interfaz de usuario y el origen. Continuar con
trabajo verificado y sin mutaciones siempre que sea posible; nunca invente un estado exitoso o
reemplace silenciosamente las pruebas que faltan con una imagen antigua.
