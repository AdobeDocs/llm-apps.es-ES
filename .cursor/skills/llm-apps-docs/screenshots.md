---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '696'
ht-degree: 0%

---
# Procedimiento de captura de pantalla Producción

Utilice este procedimiento para capturar capturas de pantalla de documentación pública real de:

`https://experience.adobe.com/#/@llmapps/llm-apps/`

El flujo de trabajo preferido es la captura humana seguida de la ingesta asistida por el agente. El usuario decide qué estados de producción son relevantes; la aptitud los organiza, sanea e integra en la documentación.

## Capturar bandeja de entrada

Coloque cada captura en ejecución en:

```text
docs-captures/<YYYY-MM-DD>/
```

Git ignora el directorio. Las capturas de pantalla sin procesar deben permanecer locales y nunca deben confirmarse.

El usuario puede utilizar cualquier nombre de archivo, pero los nombres ordenados facilitan la revisión:

```text
01-create-app.png
02-connect-github.png
03-onboarding-enabled.png
04-generating-actions.png
05-review-actions.png
```

Un(a) `capture-notes.md` opcional puede describir estados que faltan, comportamiento inusual o el orden deseado.

## Límites de seguridad

- El usuario introduce las credenciales de Adobe, GitHub y LLM directamente en el explorador.
- Detener para MFA, claves de paso, captchas, selección de organización y consentimiento privilegiado.
- Nunca lea, imprima, guarde ni confirme tokens, cookies, almacenamiento del explorador ni credenciales.
- Utilice un sitio web público no confidencial y repositorios nuevos de solo documentación.
- Conceder acceso a las aplicaciones de GitHub solo a los dos repositorios utilizados por el accesorio.
- Pregunte antes de crear, implementar, eliminar, archivar o cambiar el acceso al repositorio.
- No capture información personal, ID de organización, ID de instalación de repositorios, tokens o URL de tiempo de ejecución completo.

## Nomenclatura de sujeción

Utilice nombres que identifiquen claramente los recursos de documentación desechables:

```text
App: LLM Apps Docs <YYYY-MM-DD>
Handler repo: llm-apps-docs-<YYYYMMDD>
EDS repo: llm-apps-docs-<YYYYMMDD>-eds
```

Antes de crear nada, confirme la organización de Adobe de destino, el propietario de GitHub, el sitio web público y los nombres de las sujeciones con el usuario.

Cree ambos repositorios vacíos y privados. No los inicialice con un archivo README, una licencia o `.gitignore`.

## Ajustes de captura

- Utilice una ventanilla móvil de escritorio lo suficientemente grande como para mostrar cuadros de diálogo completos sin Chrome del explorador.
- Mantenga el zoom al 100%.
- Utilice la temática del producto predeterminada a menos que el artículo enseñe específicamente temáticas.
- Capturar la región completa más pequeña que contiene la tarea y el contexto necesario.
- Evite los cursores, los menús abiertos no relacionados con el paso, los mensajes promocionales de acciones anteriores y los giros transitorios a menos que el control de número tenga el estado documentado.
- Utilice PNG.
- Mantenga los nombres de archivo estables; reemplace el contenido de la imagen en lugar de cambiar el nombre de los archivos durante las actualizaciones.

## Secuencia de captura recomendada

El usuario debe capturar los estados relevantes del manifiesto, incluidos:

1. Cree una aplicación antes de conectar GitHub.
2. Selección de acceso al repositorio de la aplicación de GitHub.
3. **Crear mi aplicación automáticamente** habilitada con ambos repositorios seleccionados.
4. Creación de aplicaciones o inicio automático de la generación de aplicaciones.
5. Acciones que se generan.
6. Acciones generadas listas para revisión.
7. Metadatos, controladores y widgets de una acción representativa.
8. Revisión por acción y estado revisado por todas las acciones.
9. Implementación de ensayo correcta.
10. Registro de la aplicación y un resultado representativo en la plataforma LLM.

Capture pantallas adicionales cuando expliquen una decisión, un error o un requisito previo reales. No capture cada clic.

## Flujo de trabajo de admisión de aptitudes

Cuando el usuario solicita actualizar la documentación desde una carpeta de captura:

1. Confirme el directorio de captura exacto.
2. Realice un inventario de todos los archivos PNG, JPEG y WebP e inspeccione visualmente cada imagen.
3. Generar una asignación a partir de archivos de origen a entradas en `screenshot-manifest.md`.
4. Compare etiquetas y secuencias de IU visibles con el tutorial existente.
5. Informe:
   - faltan estados obligatorios;
   - imágenes duplicadas o redundantes;
   - orden ambiguo;
   - capturas de pantalla antiguas;
   - información sensible;
   - Comportamiento de producción que entra en conflicto con los documentos.
6. No edite las capturas de origen.
7. Para cada imagen aceptada, cree una copia saneada con el nombre de archivo del manifiesto estable bajo `help/assets/guide-onboarding-agent/`.
8. Recortar solo cuando la IU circundante no agregue contexto útil.
9. Enmascarar valores confidenciales. Si el enmascaramiento seguro no es posible, pida una recaptura.
10. Actualice el artículo y el texto alternativo para que coincidan con el flujo de trabajo capturado.
11. Ejecute la validación de vínculos y recursos.
12. Deje la carpeta de captura en su lugar hasta que el usuario le pida explícitamente que la elimine.

## Captura guiada por el agente opcional

Si el usuario solicita al agente que dirija el explorador, utilice los mismos límites de manifiesto y seguridad. Pausar para la autenticación, el consentimiento privilegiado, los cambios del repositorio, la creación de aplicaciones, la implementación y la limpieza. Nunca ejecute este flujo de producción-mutación desatendido.

## Revisión de imagen

Para cada imagen:

- Haga que coincida con una entrada de manifiesto.
- Compruebe la copia de la IU en relación con el artículo.
- Recorte la navegación de la cuenta cuando no sea necesario.
- Enmascara nombres personales, avatares, identificadores de organización, identificadores de instalación de repositorios, áreas de nombres de tiempo de ejecución y aplicaciones no relacionadas.
- Confirme que no hay detalles de relleno automático, correo electrónico, token de acceso o repositorio privado del explorador visibles.
- Escriba texto alternativo que identifique la pantalla y el estado.

## Cuándo se debe detener

Detener e informar de un bloqueador cuando:

- La producción no coincide con el flujo de trabajo que se está documentando.
- El flujo de revisión difiere sustancialmente de la documentación publicada.
- La validación del repositorio rechaza el flujo de repositorio vacío deseado.
- La canalización de incorporación falla.
- Una acción con privilegios requiere un usuario o un administrador.
- Una captura de pantalla no se puede hacer segura sin ocultar información esencial para el paso.
