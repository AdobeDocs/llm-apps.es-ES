---
title: Referencia de autenticación
description: Definiciones de campos, requisitos de tokens, puntos finales de detección, API de controladores y comportamiento de la plataforma LLM para la autenticación de usuarios finales en aplicaciones LLM de Adobe.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1428'
ht-degree: 2%
---

# Referencia de autenticación {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] se encuentra actualmente en Beta.
>
>Las funciones, los flujos de trabajo y la interfaz de usuario que se muestran aquí no representan necesariamente el estado final del producto. Para unirse a Beta, envíe un correo electrónico a llm-apps-beta@adobe.com.

Utilice esta página para buscar campos de autenticación y contratos. Para obtener instrucciones de configuración, consulte [Autenticar usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md).

## Configuración de autenticación {#authentication-settings}

Abra **[!UICONTROL Configuración]** > **[!UICONTROL Autenticación]**. Cada entorno almacena su propia configuración. Utilice el selector **[!UICONTROL Workspace]** para seleccionar el entorno que desea editar; guardar la configuración nunca afectará al otro entorno.

| Campo | Requerido | Descripción |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | Entorno para la configuración de autenticación: **[!UICONTROL Stage]** o **[!UICONTROL Production]** |
| **[!UICONTROL Habilitar autenticación]** | — | Interruptor maestro. Cuando está desactivada, cada acción es pública independientemente de su modo de autenticación |
| **[!UICONTROL Emisor]** | Sí | La URL de emisor de su proveedor de identidad y la notificación esperada de `iss`. Debe ser HTTPS. También se publica como servidor de autorización de esta aplicación. Un proveedor de identidad por aplicación |
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

Las barras finales de **[!UICONTROL Issuer]** están normalizadas. La comparación `iss` tolera la diferencia, por lo que los tokens de un proveedor que siempre incluye una barra diagonal siguen validándose.

## Modos de autenticación {#auth-modes}

Definir por acción en **[!UICONTROL Configuración por acción]**.

| Modo | Token obligatorio | El controlador recibe la identidad | Anunciado a la plataforma como |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Ninguno]** | No | Solo cuando el llamador proporciona un token válido | `noauth` |
| **[!UICONTROL Requerido]** | Sí, con todos los ámbitos enumerados | Siempre | `oauth2` |
| **[!UICONTROL Opcional]** | No | Cuando hay un token válido | `noauth` y `oauth2` |

El controlador de una acción **[!UICONTROL Required]** nunca se ejecuta sin un token válido con el ámbito correcto. El controlador de una acción **[!UICONTROL Optional]** siempre se ejecuta y puede solicitar el inicio de sesión con `extra.challengeAuth()`.

**[!UICONTROL Requerido]** es, por lo tanto, el único modo que rechaza a los llamadores no autenticados. Una aplicación solo rechaza todas las llamadas anónimas cuando cada acción es **[!UICONTROL Requerida]**. Si alguna acción es **[!UICONTROL None]** o **[!UICONTROL Optional]**, la aplicación usa autenticación mixta porque al menos una acción acepta llamadas anónimas.

Los modos de autenticación solo surten efecto cuando **[!UICONTROL Habilitar autenticación]** está activada. Los cambios surtirán efecto en la próxima implementación de la aplicación.

Al cambiar el conmutador de autenticación, también se reescriben los modos de acción previa:

| Cambiar cambio | Efecto en los modos de acción previa |
|---------------|----------------------------|
| De apagado a activado | Cada acción de **[!UICONTROL None]** se convierte en **[!UICONTROL Requerida]**. Acciones ya **[!UICONTROL requeridas]** o **[!UICONTROL opcionales]** mantener su modo |
| Activado a desactivado | El modo y los ámbitos de cada acción se borran para ese entorno. La configuración no se restaura si vuelve a activar el interruptor |

Cambiar **[!UICONTROL Workspace]** nunca reescribe los modos. El selector carga la configuración guardada del otro entorno sin cambiarla.

Con **[!UICONTROL Habilitar autenticación]** activada y cada acción establecida en **[!UICONTROL Ninguna]**, la aplicación no requiere autenticación pero sigue publicando su servidor de autorización para la detección. Para que la aplicación sea completamente pública, apague el conmutador.

Una aplicación puede utilizar diferentes modos de autenticación para acciones individuales. Consulte [Comportamiento de la plataforma LLM](/help/reference/authentication-reference.md#platform-behavior) para ver cómo las aplica cada plataforma.

## Requisitos de token {#token-requirements}

El proveedor de identidad debe emitir tokens de acceso que cumplan todos los requisitos siguientes. El tiempo de ejecución trata un token que falla en cualquier comprobación como ausente: el llamador no está autenticado y una acción **[!UICONTROL Requerido]** desafía al llamador a iniciar sesión.

| Requisito | Detalle |
|-------------|--------|
| Formato | JWT firmado. No se admiten tokens opacos |
| Algoritmo de firma | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` o `PS512`. Los algoritmos HMAC como `HS256` se rechazan |
| `iss` | Debe coincidir con **[!UICONTROL Emisor]** |
| `aud` | Debe contener el identificador de recurso de la aplicación (la URL de su servidor MCP para ese entorno) |
| `exp` | Debe ser en el futuro |
| `scope` o `scp` | El motor en tiempo de ejecución lee una cadena `scope` delimitada por espacios o vuelve a una matriz de cadenas en `scp`. Utilice una de estas formas para ámbitos comprobados con los requisitos de cada acción |
| `sub` | El identificador de usuario que su controlador lee a través de `getAuthenticatedUser` |
| Transporte | `Authorization: Bearer <token>` encabezado de solicitud |

Cualquier notificación plana adicional que incluya su proveedor (por ejemplo, `tenant` o `email`) se pasará al controlador. Se sueltan los objetos anidados y se truncan los valores de cadena largos.

## Detección de proveedor de identidad {#discovery}

Su aplicación publica sus propios metadatos de recursos protegidos con [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) para que las plataformas LLM puedan localizar el servidor de autorización. No es necesario crear, alojar ni configurar los metadatos de recursos protegidos.

El proveedor de identidad debe proporcionar la siguiente información de detección:

| Requisito | Detalle |
|-------------|--------|
| Metadatos del servidor de autorización | El servidor de autorización debe publicar metadatos de detección para clientes LLM. A menos que se establezca el URI **[!UICONTROL JWKS]**, el tiempo de ejecución de esta aplicación busca específicamente los metadatos del servidor de autorización de OAuth [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) para localizar `jwks_uri`; no vuelve a la ruta de detección de OIDC |
| Un emisor con una ruta | El segmento conocido va antes de la ruta, no después de ella. Un emisor de `https://auth.example.com/oauth2/default` proporciona sus metadatos en `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| URL de clave de firma directa | Establezca **[!UICONTROL JWKS URI]** cuando las claves estén hospedadas en otro lugar o el proveedor exponga únicamente la detección de OIDC. Esto evita la detección en tiempo de ejecución de las claves, no la detección requerida por los clientes LM |

## API de autenticación de controlador {#handler-auth-api}

El paquete `@adobe/llm-apps-runtime` exporta los siguientes ayudantes. Cada asistente acepta `extra`, el segundo argumento que recibe el controlador.

| Ayudante | Devuelve |
|--------|---------|
| `getAuthenticatedUser(extra)` | Notificación `sub` del usuario que inició sesión, o `undefined` cuando la llamada no está autenticada |
| `hasScope(extra, scope)` | `true` cuando el token del llamador lleva `scope` |

La información de tokens comprobados está disponible en `extra.authInfo`, que es `undefined` para una llamada no autenticada.

| Propiedad | Descripción |
|----------|-------------|
| `authInfo.token` | El token de portador sin procesar. No lo registre ni lo devuelva al cliente |
| `authInfo.clientId` | La notificación `client_id` o `azp`, o `unknown` |
| `authInfo.scopes` | Matriz de ámbitos concedidos |
| `authInfo.expiresAt` | Caducidad del token, como notificación de `exp` |
| `authInfo.resource` | Identificador de recurso de la aplicación con el que se validó el token |
| `authInfo.extra` | `sub` más cualquier otra reclamación fija que su proveedor de identidad haya incluido |

`extra.challengeAuth(options)` solo está disponible en **[!UICONTROL acciones opcionales]**. Devuelva el resultado de su controlador para pedir al usuario que inicie sesión en lugar de devolver contenido.

El asistente devuelve un desafío de resultado de herramienta para [!DNL ChatGPT]. [!DNL Claude] requiere un desafío HTTP de nivel de transporte. El motor en tiempo de ejecución proporciona ese desafío para **[!UICONTROL Acciones necesarias]**, pero no a través del controlador de ayuda opcional.

| Opción | Descripción |
|--------|-------------|
| `error` | Un [código de error RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750) Bearer: `invalid_token`, `insufficient_scope` o `invalid_request`. Valores predeterminados de `insufficient_scope` |
| `errorDescription` | El mensaje mostrado al usuario. El valor predeterminado es una solicitud de inicio de sesión genérica |
| `scope` | Ámbitos delimitados por espacios para solicitar. Omítelo para permitir que la plataforma vuelva a los ámbitos admitidos de la aplicación |

>[!IMPORTANT]
>
>Siempre se establece `error` explícitamente. Use `invalid_token` para un llamador sin una sesión válida y `insufficient_scope` solo para un llamador cuyo token sea válido pero que no tenga el ámbito requerido. El motor en tiempo de ejecución pasa el valor a la plataforma LLM, que elige la redacción de la solicitud de inicio de sesión. Envíe el código que describa con precisión la condición en lugar del código con su petición de datos preferida.

## Comportamiento de la plataforma LLM {#platform-behavior}

Ambas plataformas admiten acciones públicas y protegidas en la misma aplicación, pero sus déclencheur de inicio de sesión difieren.

| Comportamiento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Autenticación mixta | Las acciones públicas funcionan de forma anónima; **[!UICONTROL Requerido]** acciones requieren el inicio de sesión | Con **[!UICONTROL Iniciar sesión cuando sea necesario]**, las acciones públicas funcionan de forma anónima y se inicia el inicio de sesión con una llamada protegida |
| Aplicación con cada acción **[!UICONTROL Requerida]** | Configuración de la autenticación OAuth | Requiere autenticación antes de usar |
| Configuración del conector | Use **[!UICONTROL Sin autenticación]** para una aplicación pública, **[!UICONTROL OAuth]** cuando cada acción sea **[!UICONTROL Requerida]** y **[!UICONTROL Mixta]** en caso contrario, donde se ofrezcan esas opciones | Revise la configuración detectada. Elija **[!UICONTROL Iniciar sesión cuando sea necesario]** para aplicaciones mixtas o **[!UICONTROL Iniciar sesión ahora]** para iniciar sesión por adelantado |
| Falta la identidad o el ámbito requerido | Runtime devuelve el desafío de autenticación de la plataforma | El motor en tiempo de ejecución devuelve HTTP 401 para la identidad que falta o no es válida, o HTTP 403 con un desafío de ámbito insuficiente para un token válido al que le falta un ámbito requerido |
| `extra.challengeAuth()` del controlador opcional | Puede iniciar la reautenticación | Una respuesta correcta de resultados de herramientas HTTP no inicia el inicio de sesión. Usar una acción **[!UICONTROL Requerida]** para una puerta de inicio de sesión portátil |

Consulte [Autenticación diferida de Claude](https://claude.com/docs/connectors/building/lazy-authentication) y [Autenticación ChatGPT](https://developers.openai.com/plugins/build/auth) para ver el comportamiento actual del cliente. La IU y la política del conector pueden variar según la cuenta y la organización.


## Relacionado

- [Autentique usuarios finales con su propio proveedor de identidad](/help/guides/authentication.md)
- [Campos de acción y widget](/help/reference/reference-docs.md)
- [Resolución de problemas](/help/reference/troubleshooting.md#authentication)
