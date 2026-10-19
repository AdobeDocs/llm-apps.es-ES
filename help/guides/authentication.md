---
title: Autentique usuarios finales con su propio proveedor de identidad
description: Active la autenticación de usuario final para su aplicación LLM de Adobe para que una plataforma LLM admitida firme el usuario con su proveedor de identidad antes de llamar a las acciones protegidas.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# Autentique usuarios finales con su propio proveedor de identidad {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

De forma predeterminada, cada acción de la aplicación es pública. Cualquier plataforma LLM con la URL de su servidor MCP puede llamar a esas acciones, y su controlador no puede identificar al usuario final.

Active la autenticación cuando una acción necesite saber qué usuario final solicita, por ejemplo, devolver sus pedidos, derechos o detalles de cuenta. La plataforma LLM inicia sesión con **su** proveedor de identidad (IdP), envía el token de acceso resultante con cada llamada y su controlador recibe la identidad verificada.

**En esta guía:** Aprenda a conectar a su proveedor de identidad y a elegir qué acciones requieren el inicio de sesión. Utilice la identidad verificada en sus controladores, implemente la aplicación y pruebe las acciones de inicio de sesión y protegidas.

La configuración de la autenticación es una tarea avanzada. Complete primero [Configuración de infraestructura](/help/guides/create-app.md), [Crear acciones](/help/guides/creating-actions.md) y [Implementar su aplicación](/help/guides/deploy-your-app.md).

## Funcionamiento

Traes tu propio proveedor de identidad. La aplicación implementada es solo un **servidor de recursos** de OAuth 2.1; verifica los tokens que emitió el servidor de autorización. Nunca emite tokens y [!DNL Adobe] nunca almacena su ID de cliente o secreto de cliente.

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

Configure la autenticación **por entorno**. **[!UICONTROL Fase]** y **[!UICONTROL Producción]** tienen configuraciones independientes. Compruebe la configuración con un inquilino de IdP de desarrollo antes de habilitar la autenticación en **[!UICONTROL Production]**.

## Antes de empezar

- Un proveedor de identidad OAuth 2.1 u OpenID Connect que emite **tokens de acceso JWT** firmados con un algoritmo asimétrico. No se admiten tokens opacos ni tokens firmados por HMAC. Consulte [Requisitos de token](/help/reference/authentication-reference.md#token-requirements).
- Acceso de administrador a ese proveedor de identidad para registrar una API y un cliente.
- La aplicación se implementó al menos una vez en el entorno que está configurando. La URL del servidor MCP implementado es el valor al que deben vincularse los tokens.

## Copiar el identificador de recurso

El **identificador de recurso** de su aplicación es la URL de su servidor MCP. Cada token de acceso que el proveedor de identidad emita para la aplicación debe nombrar esa URL exacta como su audiencia. Este enlace de audiencia evita el uso de un token emitido para otro servicio.

1. Abra la página Detalles de la aplicación.
2. Desplácese hasta **[!UICONTROL Probar la aplicación]**.
3. En el entorno que está configurando, seleccione **[!UICONTROL Copiar URL]**.

![Detalle de la aplicación: copie la URL del servidor MCP de ensayo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Mantenga la URL del servidor MCP copiada para la configuración del proveedor de identidad en el siguiente paso. Pegue la dirección URL en lugar de volver a escribirla. La comprobación de audiencia requiere una coincidencia de cadena exacta, incluida la ruta. Una sola diferencia de caracteres hace que cada token falle en la validación.

>[!NOTE]
>
>Cada entorno tiene su propia URL de servidor MCP y, por lo tanto, su propia audiencia. Configure **[!UICONTROL Fase]** y **[!UICONTROL Producción]** por separado.

## Configuración del proveedor de identidad

Los pasos varían según el proveedor. Configure los cuatro elementos siguientes.

1. **Registre su aplicación como una API (recurso).** Establezca su identificador (el valor que el proveedor pone en la notificación `aud` del token) en la URL del servidor MCP copiada. Los proveedores suelen etiquetar este campo *Identificador* o *Audiencia*. Use un identificador único de la aplicación, no un valor genérico como `api`, para evitar el uso de tokens emitidos para otro servicio.
2. **Defina los ámbitos** que protegen sus acciones, por ejemplo `orders:read` o `profile:read`. Utilice un ámbito por permiso significativo para que cada acción solicite únicamente el acceso que necesita.
3. **PKCE de soporte.** Las plataformas LLM envían un `code_challenge` con `code_challenge_method=S256` en cada solicitud de autorización, por lo que el servidor de autorización debe admitir S256 PKCE y anunciar `"code_challenge_methods_supported": ["S256"]` en sus metadatos.
4. **Elija cómo la plataforma LLM identifica a su cliente OAuth.** Utilice documentos de metadatos de ID de cliente (CIMD) si su proveedor admite los requisitos de la plataforma, el registro dinámico de clientes (DCR) o un cliente OAuth preregistrado. Proporcione un ID de cliente y un secreto solo cuando el método elegido lo requiera. Registre el URI de redireccionamiento exacto documentado o mostrado por la plataforma; para las superficies hospedadas de [!DNL Claude], que es `https://claude.ai/api/mcp/auth_callback`. [!DNL ChatGPT] puede usar una llamada de retorno estable o específica del conector según las capacidades anunciadas del proveedor, así que copie su URI mostrado en lugar de adivinar. Consulte la [documentación de autenticación de ChatGPT](https://developers.openai.com/plugins/build/auth) actual y la [documentación de autenticación Claude](https://claude.com/docs/connectors/building/authentication).

>[!IMPORTANT]
>
>El emisor del proveedor de identidad, JWKS, la autorización y los extremos de token deben estar accesibles a través de HTTPS público. Tanto la plataforma LLM como la aplicación implementada recuperan metadatos directamente del proveedor. Una VPN, una lista de permitidos IP, un cortafuegos o un cortafuegos de aplicaciones web que bloquee esas solicitudes impide el inicio de sesión, incluso si la propia aplicación sigue siendo accesible.

## Activar autenticación

1. En el panel de navegación izquierdo, seleccione **[!UICONTROL Configuración]** y, a continuación, abra la pestaña **[!UICONTROL Autenticación]**.
2. En **[!UICONTROL Workspace]**, elija **[!UICONTROL Fase]** o **[!UICONTROL Producción]**.
3. Activar **[!UICONTROL Habilitar autenticación]**.
4. En **[!UICONTROL Configuración principal]**, escriba:
   - **[!UICONTROL Emisor]**: la dirección URL del emisor HTTPS de su proveedor de identidad, que también es el valor que pone en la notificación `iss` de cada token. La plataforma requiere esta dirección URL y publica el emisor como servidor de autorización de la aplicación para la detección de clientes LLM. Cada aplicación admite un proveedor de identidad.
   - **[!UICONTROL Ámbitos admitidos]**: se permite que todas las acciones de esta aplicación requieran cada ámbito. Refleje los ámbitos definidos en el proveedor de identidad.
5. **[!UICONTROL Configuración avanzada]** es opcional. Establezca **[!UICONTROL JWKS URI]** solo cuando las claves de firma no estén en el lugar donde los metadatos del servidor de autorización las anuncian; de lo contrario, la aplicación las detecta automáticamente.
6. Seleccione **[!UICONTROL Guardar]**.

![Autenticación: habilitar la autenticación y completar la configuración principal](/help/assets/guide-authentication/auth-core-settings.png)

Para conocer los requisitos de campo, consulte [Configuración de autenticación](/help/reference/authentication-reference.md#authentication-settings).

## Elija un modo de autenticación para cada acción

Cuando activa **[!UICONTROL Habilitar autenticación]**, cada acción establecida actualmente en **[!UICONTROL Ninguno]** cambia a **[!UICONTROL Requerido]**. En **[!UICONTROL Configuración por acción]**, revise esa asignación y establezca el modo que necesita cada acción:

| Modo | Comportamiento |
|------|----------|
| **[!UICONTROL Ninguno]** | Público. Los llamadores no necesitan un token. |
| **[!UICONTROL Requerido]** | Protegido. Los llamadores necesitan un token válido que lleve todos los ámbitos enumerados. El tiempo de ejecución desafía a los llamadores no autenticados a iniciar sesión. |
| **[!UICONTROL Opcional]** | Se puede llamar de forma anónima, pero la acción también anuncia que admite el inicio de sesión. El controlador decide por llamada si se ofrece un resultado genérico o si se solicita al usuario que inicie sesión para obtener uno personalizado. |

![Autenticación: establecer un modo de autenticación y ámbitos para cada acción](/help/assets/guide-authentication/auth-per-action.png)

Las acciones ya establecidas en **[!UICONTROL Obligatorio]** o **[!UICONTROL Opcional]** mantienen su modo existente.

Para una acción **[!UICONTROL Requerida]** o **[!UICONTROL Opcional]**, agregue los **[!UICONTROL Ámbitos]** que necesita. Todos los ámbitos deben aparecer en **[!UICONTROL Ámbitos admitidos]**, de modo que la aplicación anuncie todos los permisos necesarios a las plataformas LLM. La plataforma bloquea el guardado hasta que se resuelve cualquier discrepancia en el ámbito.

**[!UICONTROL Ámbitos admitidos]** controla los ámbitos disponibles. Al quitar un ámbito de esa lista, se quita inmediatamente el ámbito de todas las acciones que lo requieran. Agregue cada ámbito a la lista antes de asignar el ámbito a una acción.

Si se selecciona **[!UICONTROL Requerir autenticación en todas las acciones]**, se cambian **[!UICONTROL Ninguna]** acciones a **[!UICONTROL Requerido]**, pero se conservan las acciones ya establecidas en **[!UICONTROL Opcional]**. Para requerir el inicio de sesión en cada acción, cambie las **[!UICONTROL filas opcionales]** restantes a **[!UICONTROL Requeridas]** de forma individual. Al desactivar la casilla de verificación, todas las acciones se devuelven a **[!UICONTROL None]**.

Seleccione **[!UICONTROL Guardar]** cuando haya terminado. Los cambios de modo y ámbito de autenticación se guardan junto con la configuración de nivel de aplicación.

>[!IMPORTANT]
>
>Al desactivar **[!UICONTROL Habilitar autenticación]** se borran el modo de autenticación y los ámbitos de cada acción para el entorno seleccionado. La plataforma no conserva esos ajustes. Volver a activar la autenticación establece todas las acciones en **[!UICONTROL Requerido]**.

>[!NOTE]
>
>Al establecer cada acción en **[!UICONTROL None]** no se deshabilita la autenticación. Ninguna acción requiere el inicio de sesión en ese estado, pero la aplicación sigue anunciando el servidor de autorización a las plataformas LLM. Un cliente puede ofrecer un inicio de sesión que no conceda acceso adicional. Para que la aplicación sea completamente pública, desactiva **[!UICONTROL Habilitar autenticación]** e implementa.

[!DNL ChatGPT] admite modos de autenticación mixtos en una aplicación y aplica el modo de cada acción de forma individual. Las acciones públicas no solicitan el inicio de sesión; las acciones protegidas solicitan el inicio de sesión según sea necesario.

[!DNL Claude] también admite aplicaciones mixtas mediante **autenticación diferida**. Con **[!UICONTROL Iniciar sesión cuando sea necesario]** seleccionado durante la configuración del conector, las acciones públicas se pueden usar de forma anónima y la plataforma solicita iniciar sesión cuando se llama a una acción **[!UICONTROL Requerida]**. Una aplicación cuyas acciones sean todas **[!UICONTROL Requeridas]** requiere inicio de sesión antes de usar. Consulte [Comportamiento de la plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para ver la distinción entre una acción necesaria y un controlador opcional que solicita el inicio de sesión.

>[!NOTE]
>
>El tiempo de ejecución actual admite este comportamiento de autenticación mixta, verificado con la versión 1.1.5. Las aplicaciones implementadas anteriormente pueden requerir una actualización y una reimplementación en tiempo de ejecución. Algunas compilaciones de interfaz de usuario siguen mostrando una advertencia heredada de que Claude requiere autenticación para todo el conector. Compruebe el tiempo de ejecución implementado y use la opción **[!UICONTROL Iniciar sesión cuando sea necesario]** actual de Claude, en lugar de depender de la advertencia.

## Implementación del cambio

Los cambios de autenticación se aplicarán en la próxima implementación de esta aplicación. **Vuelva a implementar la aplicación** en el entorno que configuró. Ver [Implementar tu aplicación](/help/guides/deploy-your-app.md).

La URL del servidor MCP no cambia. Actualice los metadatos de la conexión y compruebe que sus opciones de autenticación coinciden con la nueva configuración de la aplicación. Una plataforma puede requerir que vuelva a conectarse o que vuelva a crear la conexión cuando cambie su método de autenticación.

## Leer la identidad en el controlador

Una identidad verificada llega al controlador como el segundo argumento cada vez que el llamador envía un token válido, independientemente del modo de autenticación de la acción. Una acción **[!UICONTROL Opcional]** puede usar esa identidad para personalizar los resultados cuando estén disponibles y devolver un resultado anónimo en caso contrario.

Use `getAuthenticatedUser` para leer al usuario que inició sesión:

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

El motor en tiempo de ejecución verifica el token por usted. Para una acción **[!UICONTROL Required]**, bloquea todas las llamadas que carecen de un token válido con los ámbitos enumerados. El controlador sólo se ejecuta para un llamador autorizado. Use `hasScope` para elegir el comportamiento del controlador en función de un permiso; por ejemplo, en una acción **[!UICONTROL Opcional]**.

Para solicitar el inicio de sesión durante una conversación, devuelva `extra.challengeAuth()` del controlador. El asistente solo está disponible en **[!UICONTROL acciones opcionales]**:

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

Use un parámetro de entrada explícito, como `signIn` en este ejemplo, para decidir cuándo solicitar autenticación. No deduzca esa decisión de la redacción del usuario.

Establezca `error` para que coincida con la condición notificada. Use `invalid_token` cuando el llamador no tenga una sesión válida y necesite iniciar sesión, como en el ejemplo anterior. Use `insufficient_scope` cuando el llamador haya iniciado sesión, pero el token no tiene el ámbito requerido. Cada plataforma LLM elige la redacción rápida y cómo responder al código de error. Envíe el código que describa con precisión la condición.

Solicite la autenticación solo cuando falte la identidad requerida, como lo hace la comprobación `!extra.authInfo` en este ejemplo. El inicio de sesión no resuelve un desafío incondicional: el controlador solicita de nuevo la autenticación en cada llamada.

>[!NOTE]
>
>`extra.challengeAuth()` devuelve un desafío de resultado de herramienta para [!DNL ChatGPT]. [!DNL Claude] necesita en su lugar un desafío de autenticación HTTP antes de que se ejecute el controlador. Para una operación que requiera el inicio de sesión en todas las plataformas compatibles, use una acción **[!UICONTROL Requerido]**. Mantenga una acción separada **[!UICONTROL None]** para la experiencia pública si es necesario.

Mantener el lado del servidor de identidad. Pase solamente los datos que necesita el widget a `structuredContent`. Nunca incluya un token de acceso en `structuredContent`. Vea [Personalizar un controlador generado](/help/guides/customize-handler.md).

Para obtener el contrato completo, consulte [API de autenticación de controlador](/help/reference/authentication-reference.md#handler-auth-api).

## Prueba de la aplicación protegida

El complemento o conector existente utiliza la configuración actualizada después de la implementación. Para crear una nueva conexión, siga las instrucciones de la plataforma:

### [!DNL ChatGPT]

En el cuadro de diálogo **[!UICONTROL Nuevo complemento]**, configure **[!UICONTROL Autenticación]** para que coincida con la forma en que configuró las acciones de la aplicación:

| Acciones de la aplicación | Seleccionar |
|--------------------|--------|
| Todo está establecido en **[!UICONTROL Ninguno]** | **[!UICONTROL Sin autenticación]** |
| Todo configurado en **[!UICONTROL Obligatorio]** | **[!UICONTROL OAuth]** |
| Cualquier otra combinación | **[!UICONTROL Mixto]** |

Una acción **[!UICONTROL Optional]** siempre acepta llamadas anónimas, por lo que una aplicación que contenga una nunca estará completamente cerrada: elija **[!UICONTROL Mixta]** aunque cada acción esté establecida en **[!UICONTROL Opcional]**. Solo **[!UICONTROL obligatorio]** rechaza los llamadores no autenticados.

Consulte [Probar el complemento ChatGPT](/help/guides/test-in-chatgpt.md) para ver el resto del cuadro de diálogo.

### [!DNL Claude]

Añada el conector personalizado y revise su configuración de autenticación. Para una aplicación mixta pública/protegida, elige **[!UICONTROL Iniciar sesión cuando sea necesario]** cuando esté disponible. Para solicitar el inicio de sesión antes de usar el conector, seleccione **[!UICONTROL Iniciar sesión ahora]**. El cuadro de diálogo varía según la cuenta u organización; consulte [Probar el conector Claude](/help/guides/test-in-claude.md).

### Verificar

- La plataforma le redirige a la página de inicio de sesión de su propio proveedor de identidad.
- Una acción protegida devuelve datos específicos del usuario después de iniciar sesión.
- Una acción protegida le pedirá que inicie sesión cuando haya cerrado la sesión.
- En una aplicación mixta, una acción establecida en **[!UICONTROL None]** sigue respondiendo sin iniciar sesión. En [!DNL Claude], compruebe la acción pública con **[!UICONTROL Inicie sesión cuando sea necesario]**.
- Después de iniciar sesión, vuelva a intentar la acción protegida y confirme que el motor en tiempo de ejecución aplica los permisos esperados.

Si el inicio de sesión no se inicia o se rechaza un token, consulte [Solución de problemas](/help/reference/troubleshooting.md#authentication).

## Guía de seguridad

- Conceda el ámbito más limitado que cada acción necesita. No reutilice un ámbito amplio en cada acción.
- Mantenga el cliente en secreto en su proveedor de identidad y en la configuración del conector de la plataforma LLM. Nunca lo ponga en metadatos de acción, código de controlador, JavaScript de widget o control de código fuente.
- Trate las notificaciones de tokens como entrada desde un sistema externo. Valide los valores de `authInfo.extra` antes de usar esos valores en una consulta.
- Autorizar y autenticar. Un token válido prueba la identidad del usuario, no su permiso para leer un registro determinado. Compruebe la propiedad en el controlador antes de devolver los datos.
- No registre tokens, conjuntos de notificaciones completos ni identificadores de usuario.
- Devolver errores seguros. No muestre respuestas del proveedor de identidad ascendente ni trazos de pila al usuario.
- Configure y verifique **[!UICONTROL Stage]** con un inquilino de proveedor de identidad que no sea de producción antes de habilitar la autenticación en **[!UICONTROL Production]**.

## Siguientes pasos

- [Referencia de autenticación](/help/reference/authentication-reference.md): campos, requisitos de token y comportamiento de la plataforma.
- [Personalizar un controlador generado](/help/guides/customize-handler.md): llame a una API ascendente protegida desde un controlador.
- [Implemente su aplicación](/help/guides/deploy-your-app.md).
