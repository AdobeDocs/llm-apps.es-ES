---
title: Configuración de variables y secretos de aplicaciones
description: Añada variables específicas del entorno a la aplicación de aplicaciones LLM de Adobe, léalas en un controlador de acciones, implemente y solucione problemas comunes.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1170'
ht-degree: 1%
---

# Configuración de variables y secretos de aplicaciones {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice variables y secretos para configurar la aplicación sin codificar los valores en sus controladores de acciones. Por ejemplo, configure la URL de la API del catálogo de productos para utilizar un servicio de prueba en Fase y el servicio activo en Producción sin cambiar el código del controlador.

Las variables contienen configuraciones no confidenciales. Los secretos están destinados a valores confidenciales, como claves de API y tokens de acceso.

>[!IMPORTANT]
>
>Actualmente, la interfaz de usuario solo admite **variables**. Se prevé compatibilidad secreta para una versión futura. Hasta entonces, **NO almacene** contraseñas, claves de API, tokens de acceso u otra información confidencial **en las variables**: la tabla de configuración muestra los valores de las variables y permite a los usuarios copiarlos.

**En esta guía:** Aprenda a agregar una variable de aplicación y leerla en un controlador de acciones. Implemente la aplicación y compruebe que el controlador utiliza el valor esperado.

## Antes de empezar {#before-you-begin}

Necesita:

- **Acceso al repositorio de controladores de su aplicación** para actualizar los controladores que necesitan leer variables.
- **`@adobe/llm-apps-runtime`1.1.0 o posterior** en ese repositorio. Compruebe la versión antes de utilizar el asistente que aparece a continuación.

Para comprobar la versión, ejecute el siguiente comando en el repositorio del controlador:

```bash
npm ls @adobe/llm-apps-runtime
```

Si la versión es anterior a la 1.1.0, actualícela, confirme y envíe `package.json` y `package-lock.json`:

```bash
npm install @adobe/llm-apps-runtime@latest
```

## Administración de variables {#manage-variables}

Cada entorno **[!UICONTROL Stage]** o **[!UICONTROL Production]** tiene sus propias variables. Compruebe **[!UICONTROL Workspace]** antes de agregar, actualizar o eliminar una variable. Los cambios surtirán efecto la próxima vez que implemente la aplicación en ese entorno.

### Agregar una variable {#add-variable-to-app}

Este ejemplo utiliza la variable no confidencial `GREETING_PREFIX` con el valor `Good day` para invalidar el saludo predeterminado del controlador de `Hello`. Agregar una variable **no** cambia una acción automáticamente: el controlador **debe** leer la variable.

#### Paso 1: Agregar una variable en la interfaz de usuario {#add-a-variable}

1. Abra la aplicación y seleccione **[!UICONTROL Configuración]** en el panel de navegación izquierdo.
2. Abra la ficha **[!UICONTROL Variables y secretos]**.
3. En **[!UICONTROL Workspace]**, seleccione **[!UICONTROL Fase]** o **[!UICONTROL Producción]**.
4. Seleccione **[!UICONTROL Añadir]**.

   ![Variables y secretos: espacio de trabajo de ensayo vacío con el botón Agregar](/help/assets/guide-app-variables/variables-empty.png)

5. En el cuadro de diálogo, introduzca:
   - **[!UICONTROL Nombre]**: `GREETING_PREFIX`.
   - **[!UICONTROL Tipo]**: deje **[!UICONTROL Variable]** seleccionada.
   - **[!UICONTROL Valor]**: `Good day`.

   ![Agregar variable o secreto — GREETING_PREFIX establecido en Buen día](/help/assets/guide-app-variables/add-variable-dialog.png)

6. Para guardar la variable, seleccione **[!UICONTROL Agregar]**.

La variable aparece en la tabla con sus fechas **[!UICONTROL Nombre]**, **[!UICONTROL Tipo]**, **[!UICONTROL Valor]** y **[!UICONTROL Última actualización]**. Para copiar el valor, utilice el control de copia adyacente.

![Variables y secretos — GREETING_PREFIX guardado en el espacio de trabajo de ensayo](/help/assets/guide-app-variables/variable-added.png)

>[!IMPORTANT]
>
>Al guardar, se agrega la variable a la configuración del entorno seleccionado. No actualiza **not** la aplicación implementada hasta que la implementa de nuevo.

#### Paso 2: Leer la variable en el controlador {#use-a-variable}

En el repositorio del controlador, abra `actions/<action-name>/index.js`. Asegúrese de que el controlador acepta un segundo argumento, `extra`, y lea la variable con `getVariable`:

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

Con el valor `Good day`, una llamada con `name` establecida en `Ada` devuelve `Good day, Ada!` en lugar del valor predeterminado `Hello, Ada!`. Si posteriormente cambia el valor a `Howdy`, el saludo cambiará después de volver a implementar, sin que se modifique ningún otro controlador.

El nombre que aparece en su código **debe** coincidir exactamente con el nombre que aparece en la interfaz de usuario. Si la variable no está establecida, `getVariable` devuelve `undefined`. Elija un valor predeterminado adecuado, como en el ejemplo, o devuelva un error claro si la acción requiere la configuración.

>[!NOTE]
>
>Las variables solo están disponibles en los controladores de acciones de su aplicación; no están **disponibles** automáticamente para los widgets.

Cuando los cambios del controlador estén listos, confírmelos e insértelos en el repositorio de controladores de la aplicación. La siguiente implementación utiliza el código insertado más reciente. Para obtener más información sobre cómo editar controladores, vea [Personalizar un controlador generado](/help/guides/customize-handler.md).

#### Paso 3: Implementación y prueba {#deploy-and-verify}

1. [Implemente su aplicación](/help/guides/deploy-your-app.md) en el mismo entorno que seleccionó en **[!UICONTROL Workspace]**.
2. Llame a la acción desde una plataforma LLM compatible con `name` establecida en `Ada`. Ver [Probar el complemento ChatGPT](/help/guides/test-in-chatgpt.md) o [Probar el conector Claude](/help/guides/test-in-claude.md).
3. Confirme que la respuesta es `Good day, Ada!`. La respuesta comprueba que el controlador lee la variable configurada y anula el saludo predeterminado.

Para configurar el otro entorno, selecciónelo en **[!UICONTROL Workspace]**, repita la instalación con el valor apropiado y, a continuación, implemente y pruebe la aplicación allí.

>[!NOTE]
>
>Fase y producción tienen **configuraciones independientes**. Los cambios en un entorno **no** afectan al otro. Utilice el mismo nombre en ambos entornos si es necesario y elija el valor adecuado para cada uno.

>[!TIP]
>
>Para realizar pruebas locales antes de implementar, vea [Desarrollo y prueba del controlador local](/help/reference/development.md) y pase las variables al servidor local:
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### Actualización de una variable {#update-or-delete}

1. Seleccione el control de edición en la fila de la variable.
2. Revise el **[!UICONTROL valor actual]** de la variable e introduzca un **[!UICONTROL valor nuevo]**.

   ![Actualizar GREETING_PREFIX — cambiar el valor de Good day a Howdy](/help/assets/guide-app-variables/update-variable-dialog.png)

3. Seleccione **[!UICONTROL Actualizar]**.
4. Vuelva a implementar la aplicación en el mismo entorno y compruebe el comportamiento modificado.

Al actualizar una variable, solo se cambia su valor. Usted **no puede** cambiar el nombre de variables. Para utilizar un nombre diferente, elimine la variable existente, agregue una variable nueva y actualice el controlador para que lea el nuevo nombre.

### Eliminar una variable {#delete-a-variable}

1. Compruebe si alguna acción sigue requiriendo la variable. Si es necesario, actualice e inserte el controlador **first**.
2. Seleccione el control de eliminación en la fila de la variable. Para eliminar varias entradas, selecciona sus casillas y elige **[!UICONTROL Eliminar]**.
3. Revise los nombres en el cuadro de diálogo de confirmación y, a continuación, seleccione **[!UICONTROL Eliminar]**.

   ![Eliminar GREETING_PREFIX — confirmar eliminación permanente](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. Vuelva a implementar la aplicación en el mismo entorno.

>[!IMPORTANT]
>
>Usted **NO PUEDE** deshacer la eliminación. Compruebe que ha seleccionado la variable correcta. La aplicación implementada mantiene su configuración existente hasta la siguiente implementación. Después de esa implementación, los controladores **ya no** reciben la variable eliminada, por lo que una acción que requiera la variable puede fallar.

## Reglas y límites {#good-to-know}

| Elemento | Regla |
|------|------|
| Nombre | Hasta 64 caracteres: letras mayúsculas, dígitos y guiones bajos, sin comenzar por un dígito. **Debe** ser único para cada aplicación y entorno, y debe coincidir con el controlador. |
| Nombres reservados | Nombres que comienzan por `LLMA_` y `MCP_SERVER_URL`. |
| Value | **Requerido**, hasta 500 caracteres. La plataforma elimina los espacios iniciales y finales. |
| Límite | 50 variables por aplicación y entorno. |
| Visibilidad | Los valores de las variables son visibles y copiables. La compatibilidad con secretos planificada mantiene los valores guardados ocultos. |
| Cambios | Tener efecto en la siguiente implementación en el entorno seleccionado. |

## Resolución de problemas {#verify-configuration}

| Lo que ve | Qué hacer |
|--------------|------------|
| **[!UICONTROL Agregar]** está deshabilitado y la página muestra **[!UICONTROL límite de Workspace alcanzado]** | Elimine las variables que ya no necesite. |
| *Use solo letras mayúsculas, dígitos y guiones bajos* | Cambie el nombre, por ejemplo `API_BASE_URL`. |
| *Este nombre está reservado por la plataforma* | Elija un nombre que no comience por `LLMA_` y que no sea `MCP_SERVER_URL`. |
| *Ya existe una variable con este nombre* | Actualice la variable existente en su lugar. |
| *Esta variable se acaba de modificar* | Actualice la página e inténtelo de nuevo. |
| *No se pudieron cargar las variables* | Vuelva a cargar la página. Si el error persiste, compruebe que tiene acceso a la aplicación. |
| La acción no utiliza el nuevo valor | Compruebe que implementó **after** al guardar la variable y probó el mismo entorno. Confirme que insertó el cambio de controlador y que el nombre de la variable coincide con **exactamente**. **[!UICONTROL Última actualización]** muestra cuándo guardó el valor, no cuándo implementó la aplicación. |
| `getVariable is not a function` | La aplicación utiliza un tiempo de ejecución anterior a 1.1.0. Actualícelo como se describe en [Antes de comenzar](#before-you-begin) y luego impleméntelo. |
| La acción falla después de eliminar una variable | Vuelva a agregar la variable o actualice el controlador para que ya no la necesite e implemente. |

## Siguientes pasos {#whats-next}

- [Personalizar un controlador generado](/help/guides/customize-handler.md)
- [Implemente la aplicación](/help/guides/deploy-your-app.md)
