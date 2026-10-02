---
title: Autentique usuarios finales con su propio proveedor de identidad
description: Active la autenticación de usuario final para su aplicación LLM de Adobe para que una plataforma LLM admitida firme el usuario con su proveedor de identidad antes de llamar a las acciones protegidas.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# Autenticar usuarios finales con su propio proveedor de identidad {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

De forma predeterminada, cada acción de la aplicación es pública: cualquier plataforma LLM que tenga la URL del servidor MCP puede llamarla y el controlador no puede identificar al usuario final.

Active la autenticación cuando una acción necesite saber qué usuario final solicita, por ejemplo, devolver sus pedidos, derechos o detalles de cuenta. La plataforma LLM inicia sesión con **su** proveedor de identidad (IdP), envía el token de acceso resultante con cada llamada y su controlador recibe la identidad verificada.

**Recorrido:** Copie el identificador de recurso → configure su proveedor de identidad → activar la autenticación → establecer un modo de autenticación para cada acción → implementar → leer la identidad en su controlador → probar la aplicación protegida.

Se trata de una rama avanzada, que no forma parte del recorrido de primera ejecución. Complete [Crea tu primera aplicación automáticamente](/help/guides/create-app.md) y [Implementa tu aplicación](/help/guides/deploy-your-app.md) primero.

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

La autenticación está configurada **por entorno**. **[!UICONTROL Fase]** y **[!UICONTROL Producción]** contienen configuraciones independientes, de modo que puede verificar la configuración con un inquilino de IdP de desarrollo antes de habilitarla en **[!UICONTROL Producción]**.

## Antes de empezar

- Un proveedor de identidad OAuth 2.1 u OpenID Connect que emite **tokens de acceso JWT** firmados con un algoritmo asimétrico. No se admiten tokens opacos ni tokens firmados por HMAC. Consulte [Requisitos de token](/help/reference/authentication-reference.md#token-requirements).
- Acceso de administrador a ese proveedor de identidad, para que pueda registrar una API y un cliente.
- La aplicación se implementó al menos una vez en el entorno que está configurando. La URL del servidor MCP implementado es el valor al que deben vincularse los tokens.

## Copiar el identificador de recurso

El **identificador de recurso** de su aplicación es la URL de su servidor MCP. Cada token de acceso que el proveedor de identidad emita para esta aplicación debe nombrar esa URL exacta como su audiencia; ese enlace es lo que impide que un token acuñado para otro servicio se reproduzca en la aplicación.

1. Abra la página Detalles de la aplicación.
2. Desplácese hasta **[!UICONTROL Probar la aplicación]**.
3. En el entorno que está configurando, seleccione **[!UICONTROL Copiar URL]**.

![Detalle de la aplicación: copie la URL del servidor MCP de ensayo](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Mantenga este valor: lo necesita en su proveedor de identidad en el siguiente paso. Pegue la dirección URL copiada en lugar de volver a escribirla. La comprobación de audiencia es una coincidencia de cadena exacta, incluido cualquier componente de ruta, por lo que una sola diferencia de caracteres hace que cada token falle en la validación.

>[!NOTE]
>
>Cada entorno tiene su propia URL de servidor MCP y, por lo tanto, su propia audiencia. Configure **[!UICONTROL Fase]** y **[!UICONTROL Producción]** por separado.

## Configuración del proveedor de identidad

Los pasos exactos difieren según el proveedor, pero cada proveedor necesita las mismas cuatro cosas.

1. **Registre su aplicación como una API (recurso).** Establezca su identificador (el valor que el proveedor pone en la notificación `aud` del token) en la dirección URL del servidor MCP que ha copiado. Los proveedores etiquetan este campo de varias formas, comúnmente *Identificador* o *Audiencia*. No utilice un valor genérico como `api`; el identificador debe ser único para esta aplicación o se puede reproducir un token emitido para otro servicio en relación con él.
2. **Defina los ámbitos** con los que desea cerrar acciones, por ejemplo `orders:read` o `profile:read`. Utilice un ámbito por permiso significativo, de modo que una acción solo pregunte qué necesita.
3. **PKCE de soporte.** Las plataformas LLM envían un `code_challenge` con `code_challenge_method=S256` en cada solicitud de autorización, por lo que el servidor de autorización debe admitir S256 PKCE y anunciar `"code_challenge_methods_supported": ["S256"]` en sus metadatos.
4. **Permitir que la plataforma LLM se registre como cliente.** Las plataformas LLM compatibles crean su propio cliente de OAuth con el servidor de autorización, por lo que habilite el registro de cliente dinámico si su proveedor lo ofrece. De lo contrario, cree un cliente público manualmente y proporcione su ID de cliente (y secreto, solo si el proveedor requiere autenticación de cliente confidencial) durante la configuración del conector en la plataforma. Registre el URI de redireccionamiento de los documentos de la plataforma; para las superficies hospedadas de [!DNL Claude], que es `https://claude.ai/api/mcp/auth_callback`. Algunas plataformas emiten un URI de redirección distinto para cada conector que cree el usuario, [!DNL ChatGPT] entre ellos, por lo que lea el valor de la pantalla de configuración del conector y regístrelo antes del primer inicio de sesión. Un URI de redirección no registrado hace que el servidor de autorización rechace directamente la solicitud de autorización.

>[!IMPORTANT]
>
>El emisor del proveedor de identidad, JWKS, la autorización y los extremos de token deben estar accesibles a través de HTTPS público. Tanto la plataforma LLM como la aplicación implementada recuperan metadatos del proveedor directamente, por lo que un proveedor de identidad detrás de una VPN o una lista de permitidos IP no puede completar el inicio de sesión. Un firewall o firewall de aplicaciones web frente a su proveedor es una causa común, y puede interrumpir el flujo incluso cuando la propia aplicación está accesible.

## Activar autenticación

1. En el panel de navegación izquierdo, seleccione **[!UICONTROL Configuración]** y, a continuación, abra la pestaña **[!UICONTROL Autenticación]**.
2. En **[!UICONTROL Workspace]**, elija **[!UICONTROL Fase]** o **[!UICONTROL Producción]**.
3. Activar **[!UICONTROL Habilitar autenticación]**.
4. En **[!UICONTROL Configuración principal]**, escriba:
   - **[!UICONTROL Emisor]**: la dirección URL del emisor de su proveedor de identidad, que también es el valor que pone en la notificación `iss` de cada token. Esto es obligatorio, debe ser HTTPS y también se publica como servidor de autorización de la aplicación para que las plataformas LLM puedan descubrir a dónde enviar a los usuarios. Solo se admite un proveedor de identidad por aplicación.
   - **[!UICONTROL Ámbitos admitidos]**: se permite que todas las acciones de esta aplicación requieran cada ámbito. Refleje los ámbitos definidos en el proveedor de identidad.
5. **[!UICONTROL Configuración avanzada]** es opcional. Establezca **[!UICONTROL JWKS URI]** solo cuando las claves de firma no estén en el lugar donde los metadatos del servidor de autorización las anuncian; de lo contrario, la aplicación las detecta automáticamente.
6. Seleccione **[!UICONTROL Guardar]**.

![Autenticación: habilitar la autenticación y completar la configuración principal](/help/assets/guide-authentication/auth-core-settings.png)

Para saber qué acepta cada campo, consulte [Configuración de autenticación](/help/reference/authentication-reference.md#authentication-settings).

## Elija un modo de autenticación para cada acción

Cuando activa **[!UICONTROL Habilitar autenticación]**, cada acción establecida actualmente en **[!UICONTROL Ninguno]** cambia a **[!UICONTROL Requerido]**. En **[!UICONTROL Configuración por acción]**, revise esa asignación y establezca el modo que necesita cada acción:

| Modo | Comportamiento |
|------|----------|
| **[!UICONTROL Ninguno]** | Público. Se puede llamar a la acción sin un token. |
| **[!UICONTROL Requerido]** | Cerrado. La acción solo se puede llamar con un token válido que incluya todos los ámbitos que se enumeran para ella. Los llamadores no autenticados deben iniciar sesión. |
| **[!UICONTROL Opcional]** | Se puede llamar de forma anónima, pero la acción también anuncia que admite el inicio de sesión. El controlador decide por llamada si se ofrece un resultado genérico o si se solicita al usuario que inicie sesión para obtener uno personalizado. |

![Autenticación: establecer un modo de autenticación y ámbitos para cada acción](/help/assets/guide-authentication/auth-per-action.png)

Las acciones ya establecidas en **[!UICONTROL Obligatorio]** o **[!UICONTROL Opcional]** mantienen su modo existente.

Para una acción **[!UICONTROL Requerida]** o **[!UICONTROL Opcional]**, agregue los **[!UICONTROL Ámbitos]** que necesita. Todos los ámbitos deben aparecer en **[!UICONTROL Ámbitos admitidos]** anteriormente; de lo contrario, la aplicación necesitaría un permiso para no anunciarse en plataformas LLM. Guardar se bloquea hasta que se resuelva la discrepancia.

**[!UICONTROL Ámbitos admitidos]** es la autoridad para esta lista. Si quita un ámbito, éste se quita de todas las acciones que lo requieran tan pronto como realice el cambio, por lo que primero debe agregar un ámbito allí y, a continuación, asignarlo a una acción.

**[!UICONTROL Requerir autenticación en todas las acciones]** establece cada acción en **[!UICONTROL Requerido]**. Borrarlo devuelve cada acción a **[!UICONTROL None]**.

Seleccione **[!UICONTROL Guardar]** cuando haya terminado. Los cambios de modo y ámbito de autenticación se guardan junto con la configuración de nivel de aplicación.

>[!IMPORTANT]
>
>Desactivar **[!UICONTROL Habilitar autenticación]** descarta esta configuración por acción para el entorno seleccionado: el modo y los ámbitos de cada acción se borran y no se recuerdan. Volver a activarlo comienza desde el **[!UICONTROL Requerido]**.

>[!NOTE]
>
>Al establecer cada acción en **[!UICONTROL None]** no se deshabilita la autenticación. No se rechaza ninguna llamada en ese estado, pero la aplicación sigue anunciando el servidor de autorización a plataformas LLM, de modo que un cliente puede ofrecer al usuario un inicio de sesión que no conceda acceso adicional. Para que la aplicación sea completamente pública, desactiva **[!UICONTROL Habilitar autenticación]** e implementa.

Se admiten los modos de combinación en una aplicación (algunas acciones públicas, otras cerradas) y [!DNL ChatGPT] aplica el modo de cada acción de forma individual: solo las acciones cerradas piden al usuario que inicie sesión.

>[!IMPORTANT]
>
>[!DNL Claude] es la excepción. Aplica autenticación por conector en lugar de por acción, por lo que si alguna acción de la aplicación está establecida en **[!UICONTROL Obligatoria]** o **[!UICONTROL Opcional]**, [!DNL Claude] pide al usuario que inicie sesión antes de usar el conector, incluidas las acciones establecidas en **[!UICONTROL Ninguna]**. Para que una acción se mantenga pública para [!DNL Claude] usuarios, debe alojarla en una aplicación independiente.

## Implementación del cambio

Los cambios de autenticación se aplicarán en la próxima implementación de esta aplicación. **Vuelva a implementar la aplicación** en el entorno que configuró. Ver [Implementar tu aplicación](/help/guides/deploy-your-app.md).

La URL del servidor MCP no cambia, por lo que cualquier complemento o conector que ya haya creado seguirá funcionando. Ahora está cerrado, por lo que se pide a los usuarios que inicien sesión la próxima vez que lo utilicen.

## Leer la identidad en el controlador

Una identidad verificada llega al controlador como segundo argumento. Está presente cada vez que el llamador envió un token válido, independientemente del modo de autenticación de la acción, por lo que una acción **[!UICONTROL Optional]** puede personalizar su resultado cuando un token está presente y devolver un resultado cuando no lo está.

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

No es necesario que verifique el token usted mismo. Para una acción **[!UICONTROL Required]**, el motor en tiempo de ejecución bloquea todas las llamadas que carecen de un token válido que contenga los ámbitos enumerados, por lo que el controlador solo se ejecuta para un llamador autorizado. Use `hasScope` cuando quiera bifurcar un permiso en lugar de depender de la puerta; por ejemplo, en una acción **[!UICONTROL Opcional]**.

Una acción **[!UICONTROL Opcional]** puede pedir al usuario que inicie sesión en mitad de la conversación devolviendo `extra.challengeAuth()`. Esto solo está disponible en **[!UICONTROL acciones opcionales]**:

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

Decida si desea escalar a partir de un parámetro de entrada explícito, como hace `signIn` aquí, en lugar de inspeccionar la redacción del usuario.

Establezca `error` para que coincida con la condición de la que está informando. Use `invalid_token` cuando el llamador no tenga una sesión válida y necesite iniciar sesión, como en el ejemplo anterior, y `insufficient_scope` cuando el llamador ya haya iniciado sesión, pero el token no tenga el ámbito que necesita la acción. La plataforma LLM elige la redacción del mensaje que ve el usuario y hasta qué punto esa redacción varía según este valor difiere según la plataforma, por lo que debe enviar el código que describe la condición con precisión.

Desafíelo únicamente cuando la identidad que necesita realmente no esté, como lo hace aquí la comprobación `!extra.authInfo`. Un controlador que desafía incondicionalmente no se puede satisfacer iniciando sesión, por lo que se le pide al usuario que vuelva a autenticarse en cada llamada.

>[!NOTE]
>
>En [!DNL ChatGPT], un inicio de sesión iniciado de esta forma pide al usuario que vuelva a conectar el conector en lugar de conceder un permiso adicional. El [!DNL Claude] el usuario inicia sesión antes de ejecutar cualquier acción, por lo que una acción nunca necesita generar una.

Mantener el lado del servidor de identidad. Pase solo lo que el widget necesita a `structuredContent` y nunca coloque el token de acceso allí. Consulte [Personalizar un controlador generado](/help/guides/customize-handler.md).

Para obtener el contrato completo, consulte [API de autenticación de controlador](/help/reference/authentication-reference.md#handler-auth-api).

## Prueba de la aplicación protegida

El complemento o conector existente recoge el cambio después de la implementación. Para configurar una desde cero:

### [!DNL ChatGPT]

En el cuadro de diálogo **[!UICONTROL Nuevo complemento]**, configure **[!UICONTROL Autenticación]** para que coincida con la forma en que configuró las acciones de la aplicación:

| Acciones de la aplicación | Seleccionar |
|--------------------|--------|
| Todo está establecido en **[!UICONTROL Ninguno]** | **[!UICONTROL Sin autenticación]** |
| Todo configurado en **[!UICONTROL Obligatorio]** | **[!UICONTROL OAuth]** |
| Cualquier otra combinación | **[!UICONTROL Mixto]** |

![ChatGPT — seleccione el modo de autenticación para el complemento](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

Una acción **[!UICONTROL Optional]** siempre acepta llamadas anónimas, por lo que una aplicación que contenga una nunca estará completamente cerrada: elija **[!UICONTROL Mixta]** aunque cada acción esté establecida en **[!UICONTROL Opcional]**. Solo **[!UICONTROL obligatorio]** rechaza los llamadores no autenticados.

Consulte [Probar el complemento ChatGPT](/help/guides/test-in-chatgpt.md) para ver el resto del cuadro de diálogo.

### [!DNL Claude]

Agregue el conector personalizado, luego seleccione **[!UICONTROL Conectar]** y complete el inicio de sesión que presenta su proveedor de identidad. No hay ninguna opción de autenticación que realizar: [!DNL Claude] cierra todo el conector cada vez que se activa una acción. Ver [Probar el conector Claude](/help/guides/test-in-claude.md).

### Verificar

- La plataforma le redirige a la página de inicio de sesión de su propio proveedor de identidad.
- Una acción protegida devuelve datos específicos del usuario después de iniciar sesión.
- Una acción protegida le pedirá que inicie sesión cuando haya cerrado la sesión.
- En [!DNL ChatGPT], una acción establecida en **[!UICONTROL None]** sigue respondiendo sin iniciar sesión. En [!DNL Claude], todo el conector está cerrado.

Si el inicio de sesión no se inicia o se rechaza un token, consulte [Solución de problemas](/help/reference/troubleshooting.md#authentication).

## Guía de seguridad

- Conceda el ámbito más limitado que cada acción necesita. No reutilice un ámbito amplio en cada acción.
- Mantenga el cliente en secreto en su proveedor de identidad y en la configuración del conector de la plataforma LLM. Nunca lo ponga en metadatos de acción, código de controlador, JavaScript de widget o control de código fuente.
- Trate las notificaciones de tokens como entrada desde un sistema externo. Valide todo lo que haya leído de `authInfo.extra` antes de utilizarlo en una consulta.
- Autorizar y autenticar. Un token válido prueba quién es el usuario, no que pueda ver un registro en particular: compruebe la propiedad en el controlador antes de devolver los datos.
- No registre tokens, conjuntos de notificaciones completos ni identificadores de usuario.
- Devolver errores seguros. No muestre respuestas del proveedor de identidad ascendente ni trazos de pila al usuario.
- Configure y verifique **[!UICONTROL Stage]** con un inquilino de proveedor de identidad que no sea de producción antes de habilitar la autenticación en **[!UICONTROL Production]**.

## Siguientes pasos

- [Referencia de autenticación](/help/reference/authentication-reference.md): campos, requisitos de token y comportamiento de la plataforma.
- [Personalizar un controlador generado](/help/guides/customize-handler.md): llame a una API ascendente protegida desde un controlador.
- [Implemente su aplicación](/help/guides/deploy-your-app.md).
