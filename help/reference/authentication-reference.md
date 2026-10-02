---
title: Referencia de autenticación
description: Definiciones de campos, requisitos de tokens, puntos finales de detección, API de controladores y comportamiento de la plataforma LLM para la autenticación de usuarios finales en aplicaciones LLM de Adobe.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1260'
ht-degree: 2%
---

# Referencia de autenticación {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta página para buscar campos de autenticación y contratos. Para ver el recorrido de configuración, consulte [Autenticar usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md).

## Configuración de autenticación {#authentication-settings}

Se encuentra en **[!UICONTROL Configuración]** > **[!UICONTROL Autenticación]**. Cada campo se almacena por entorno: el selector **[!UICONTROL Workspace]** selecciona cuál está editando y el guardado nunca afecta al otro.

| Campo | Requerido | Descripción |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | Qué entorno se aplica a esta configuración: **[!UICONTROL Fase]** o **[!UICONTROL Producción]** |
| **[!UICONTROL Habilitar autenticación]** | — | Interruptor maestro. Cuando está desactivada, cada acción es pública independientemente de su modo de autenticación |
| **[!UICONTROL Emisor]** | Sí | La dirección URL del emisor del proveedor de identidad y la notificación esperada de `iss`. Debe ser HTTPS. También se publica como servidor de autorización de esta aplicación. Un proveedor de identidad por aplicación |
| **[!UICONTROL Ámbitos admitidos]** | No | El conjunto completo de ámbitos que pueden requerir las acciones de esta aplicación. Publicado en plataformas LLM como ámbitos compatibles con la aplicación |
| **[!UICONTROL URI JWKS]** | No | Avanzado. La URL HTTPS del conjunto de claves de firma. Solo es necesario cuando difiere de lo que anuncian los metadatos del servidor de autorización |

### Reglas de validación

| Regla | Efecto |
|------|--------|
| **[!UICONTROL El emisor]** está vacío mientras que **[!UICONTROL Habilitar autenticación]** está activado | Guardar está bloqueado |
| **[!UICONTROL El emisor]** o **[!UICONTROL URI JWKS]** no es una URL HTTPS | Guardar está bloqueado |
| Una acción requiere que falte un ámbito de **[!UICONTROL ámbitos admitidos]** | Guardar está bloqueado hasta que agregue el ámbito o lo quite de la acción |
| Se ha eliminado un ámbito de **[!UICONTROL ámbitos admitidos]** | Se elimina de todas las acciones que lo requerían, inmediatamente, sin esperar a que se guarde |
| **[!UICONTROL Ámbitos admitidos]** está vacío | No se puede conceder ningún ámbito, por lo que se elimina cualquier ámbito que ya esté en una acción. No se muestra ninguna advertencia en este caso |
| `offline_access` aparece en **[!UICONTROL Ámbitos admitidos]** o en una acción | Se elimina cuando la aplicación se implementa, independientemente del uso de mayúsculas y minúsculas o de los espacios en blanco circundantes, por lo que la página de configuración puede mostrar un ámbito que la aplicación implementada no tiene. `offline_access` solicita un token de actualización de su servidor de autorización en lugar de conceder acceso a esta aplicación, por lo que no es un ámbito que esta aplicación anuncia. No es necesario que lo enumere: la plataforma LLM lo solicita directamente desde el servidor de autorización |

Las barras finales de **[!UICONTROL Issuer]** están normalizadas y la comparación de `iss` tolera la diferencia: un proveedor que siempre emite una barra final sigue validando.

## Modos de autenticación {#auth-modes}

Definir por acción en **[!UICONTROL Configuración por acción]**.

| Modo | Token obligatorio | El controlador recibe la identidad | Anunciado a la plataforma como |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Ninguno]** | No | Solo cuando el llamador proporciona un token válido | `noauth` |
| **[!UICONTROL Requerido]** | Sí, con todos los ámbitos enumerados | Siempre | `oauth2` |
| **[!UICONTROL Opcional]** | No | Cuando hay un token válido | `noauth` y `oauth2` |

El controlador de una acción **[!UICONTROL Required]** nunca se ejecuta sin un token válido con el ámbito correcto. El controlador de una acción **[!UICONTROL Optional]** siempre se ejecuta y puede solicitar el inicio de sesión con `extra.challengeAuth()`.

**[!UICONTROL Obligatorio]** es, por lo tanto, el único modo que rechaza los llamadores no autenticados. Una aplicación está completamente cerrada solamente cuando cada una de sus acciones es **[!UICONTROL Requerida]**; una sola acción **[!UICONTROL Ninguna]** u **[!UICONTROL Opcional]** hace que la aplicación se mezcle, porque las llamadas anónimas se realizan correctamente para al menos una acción.

Los modos de autenticación solo están vigentes cuando **[!UICONTROL Habilitar autenticación]** está activado. Los cambios surtirán efecto en la próxima implementación de la aplicación.

Al alternar ese conmutador, se reescriben los modos de acción previa:

| Cambiar cambio | Efecto en los modos de acción previa |
|---------------|----------------------------|
| De apagado a activado | Cada acción de **[!UICONTROL None]** se convierte en **[!UICONTROL Requerida]**. Acciones ya **[!UICONTROL requeridas]** o **[!UICONTROL opcionales]** mantener su modo |
| Activado a desactivado | El modo y los ámbitos de cada acción se borran para ese entorno. La configuración no se restaura si vuelve a activar el interruptor |

Cambiar **[!UICONTROL Workspace]** nunca reescribe los modos, sino que carga la configuración guardada del otro entorno tal cual.

**[!UICONTROL Habilitar autenticación]** en con cada acción establecida en **[!UICONTROL Ninguna]** es una combinación válida pero inerte: nunca se rechaza ninguna llamada, pero la aplicación sigue publicando su servidor de autorización para la detección. Desactive el conmutador para que la aplicación sea completamente pública.

Los modos se pueden mezclar libremente en una aplicación. Consulte [Comportamiento de la plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para ver cómo las aplica cada plataforma.

## Requisitos de token {#token-requirements}

El proveedor de identidad debe emitir tokens de acceso que cumplan todos los requisitos siguientes. Un token que no supera cualquier comprobación se trata como ausente: el llamador no está autenticado y una acción **[!UICONTROL Requerido]** le desafía a iniciar sesión.

| Requisito | Detalle |
|-------------|--------|
| Formato | JWT firmado. No se admiten tokens opacos |
| Algoritmo de firma | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` o `PS512`. Los algoritmos HMAC como `HS256` se rechazan |
| `iss` | Debe coincidir con **[!UICONTROL Emisor]** |
| `aud` | Debe contener el identificador de recurso de la aplicación (la URL de su servidor MCP para ese entorno) |
| `exp` | Debe ser en el futuro |
| `scope` o `scp` | Cadena delimitada por espacios o matriz de cadenas. Proporciona los ámbitos comprobados en relación con los requisitos de cada acción |
| `sub` | El identificador de usuario que su controlador lee a través de `getAuthenticatedUser` |
| Transporte | `Authorization: Bearer <token>` encabezado de solicitud |

Cualquier notificación plana adicional que incluya su proveedor (por ejemplo, `tenant` o `email`) se pasará al controlador. Se sueltan los objetos anidados y se truncan los valores de cadena largos.

## Detección de proveedor de identidad {#discovery}

Su aplicación publica sus propios metadatos de recursos protegidos con [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) para que las plataformas LLM puedan localizar el servidor de autorización. No cree, aloje ni configure nada para él.

Debe proporcionar descubrimiento por su cuenta:

| Requisito | Detalle |
|-------------|--------|
| Metadatos del servidor de autorización | El emisor debe proporcionar sus propios metadatos [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) o detección de [!DNL OpenID Connect] en su ruta de acceso `/.well-known/`. La aplicación lo lee para localizar las claves de firma |
| Un emisor con una ruta | El segmento conocido va antes de la ruta, no después de ella. Un emisor de `https://auth.example.com/oauth2/default` proporciona sus metadatos en `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| Claves alojadas en otra parte | Establezca **[!UICONTROL JWKS URI]** cuando sus claves de firma no estén en el lugar donde los metadatos las anuncian |

## API de autenticación de controlador {#handler-auth-api}

Exportado desde `@adobe/llm-apps-runtime`. Cada asistente toma `extra`, el segundo argumento que recibe su controlador.

| Ayudante | Devuelve |
|--------|---------|
| `getAuthenticatedUser(extra)` | Notificación `sub` del usuario que inició sesión, o `undefined` cuando la llamada no está autenticada |
| `hasScope(extra, scope)` | `true` cuando el token del llamador lleva `scope` |

La información de token verificado sin procesar está en `extra.authInfo`, que es `undefined` para una llamada no autenticada.

| Propiedad | Descripción |
|----------|-------------|
| `authInfo.token` | El token de portador sin procesar. No lo registre ni lo devuelva al cliente |
| `authInfo.clientId` | La notificación `client_id` o `azp`, o `unknown` |
| `authInfo.scopes` | Matriz de ámbitos concedidos |
| `authInfo.expiresAt` | Caducidad del token, como notificación de `exp` |
| `authInfo.resource` | Identificador de recurso de la aplicación con el que se validó el token |
| `authInfo.extra` | `sub` más cualquier otra reclamación fija que su proveedor de identidad haya incluido |

`extra.challengeAuth(options)` solo está disponible en **[!UICONTROL acciones opcionales]**. Devuelva el resultado de su controlador para pedir al usuario que inicie sesión en lugar de devolver contenido.

| Opción | Descripción |
|--------|-------------|
| `error` | Un [código de error RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750) Bearer: `invalid_token`, `insufficient_scope` o `invalid_request`. Valores predeterminados de `insufficient_scope` |
| `errorDescription` | El mensaje mostrado al usuario. El valor predeterminado es una solicitud de inicio de sesión genérica |
| `scope` | Ámbitos delimitados por espacios para solicitar. Omítelo para permitir que la plataforma vuelva a los ámbitos admitidos de la aplicación |

>[!IMPORTANT]
>
>Siempre se establece `error` explícitamente. Use `invalid_token` para un llamador sin una sesión válida y `insufficient_scope` solo para un llamador cuyo token sea válido pero que no tenga el ámbito requerido. El valor se pasa a la plataforma LLM, que decide por sí misma cómo redactar el mensaje que muestra al usuario. Envíe el código que describa con precisión la condición en lugar del que prefiera.

## Comportamiento de la plataforma LLM {#platform-behavior}

La compatibilidad con la autenticación de acciones individuales varía según la plataforma. Configure lo mismo para ambos; la diferencia es lo que experimenta el usuario.

| Comportamiento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Granularidad | Por acción | Por conector |
| Autenticación mixta, en la que la aplicación no está completamente cerrada | Compatible. Solo se requiere el aviso de **[!UICONTROL Acciones necesarias]** para el inicio de sesión | No compatible. Todo el conector solicita el inicio de sesión, incluidas las acciones no cerradas |
| Configuración del conector | Establezca **[!UICONTROL Authentication]** en **[!UICONTROL No Auth]** cuando cada acción sea **[!UICONTROL None]**, **[!UICONTROL OAuth]** cuando cada acción sea **[!UICONTROL Requerida]** y **[!UICONTROL Mixta]** en caso contrario | No hay ninguna opción de autenticación que realizar; el inicio de sesión se inicia en **[!UICONTROL Connect]** |
| Volver a autenticar | Se pregunta en la conversación cuando se llama a una acción cerrada | Se ha solicitado el conector |


## Relacionado

- [Autentique usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md)
- [Campos de acción y widget](/help/reference/reference-docs.md)
- [Resolución de problemas](/help/reference/troubleshooting.md#authentication)
