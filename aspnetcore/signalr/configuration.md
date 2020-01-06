---
title: Configuration de la SignalR ASP.NET Core
author: bradygaster
description: Découvrez comment configurer des applications ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358110"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a>Configuration de la SignalR ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole possède des options de configuration de la sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol` peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`. La méthode `AddJsonProtocol` prend un délégué qui reçoit un objet `options`. La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un objet <xref:System.Text.Json.JsonSerializerOptions> `System.Text.Json` qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour. Pour plus d’informations, consultez la [Documentation System. Text. JSON](/dotnet/api/system.text.json).

Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Dans le client .NET, la même méthode d’extension de `AddJsonProtocol` existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace de noms `Microsoft.Extensions.DependencyInjection` doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.

### <a name="switch-to-newtonsoftjson"></a>Basculer vers Newtonsoft. JSON

Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json`, consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Pour plus d’informations, consultez [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des SignalR hubs :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondes | Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle. Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté. La valeur recommandée est le double de la valeur `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé un message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte. Lorsque vous modifiez `KeepAliveInterval`, modifiez le paramètre `ServerTimeout` / `serverTimeoutInMilliseconds` sur le client. La valeur recommandée `ServerTimeout` / `serverTimeoutInMilliseconds` est le double de la valeur `KeepAliveInterval`.  |
| `SupportedProtocols` | Tous les protocoles installés | Les protocoles pris en charge par ce hub. Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour certains hubs. |
| `EnableDetailedErrors` | `false` | Si `true`, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur. La valeur par défaut est `false`, car ces messages d’exception peuvent contenir des informations sensibles. |
| `StreamBufferCapacity` | `10` | Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client. Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.|
| `MaximumReceiveMessageSize` | 32 Ko | Taille maximale d’un seul message de concentrateur entrant. |

Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l'appel `AddSignalR` dans `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options d'un Hub spécifique remplacent les options globales fournies dans la méthode `AddSignalR` et peuvent être configurées à l’aide de : <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options de configuration HTTP avancées

Utilisez `HttpConnectionDispatcherOptions` pour configurer des paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué à [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core SignalR:

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression. L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire. |
| `AuthorizationData` | Les données sont collectées automatiquement à partir des attributs `Authorize` appliqués à la classe de concentrateur. | Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur. |
| `TransportMaxBufferSize` | 32 Ko | Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression. L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Énumération d’indicateurs de bits des valeurs `HttpTransportType` qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport d’interrogation longue. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport d’interrogation longue propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `LongPolling` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique. Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment. |

Le transport WebSocket propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `WebSockets` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être utilisé pour définir l'en-tête `Sec-WebSocket-Protocol` sur une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client en entrée et doit retourner la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options du client

Les options du client peuvent être configurées sur le type de `HubConnectionBuilder` (disponible dans les clients .NET et JavaScript). Il est également disponible dans le client Java, mais la sous-classe `HttpHubConnectionBuilder` est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

La journalisation est configurée dans le client .NET à l’aide de la méthode `ConfigureLogging`. Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur. Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires. Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir la liste complète.

Par exemple, pour activer la journalisation de la Console, installez le package NuGet `Microsoft.Extensions.Logging.Console`. Appelez la méthode d’extension `AddConsole` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, il existe une méthode de `configureLogging` similaire. Fournissez une valeur de `LogLevel` indiquant le niveau minimal de messages du journal à produire. Les journaux sont écrits dans la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Au lieu d’une valeur `LogLevel`, vous pouvez également fournir une valeur de `string` représentant un nom de niveau de journal. Cela est utile lors de la configuration de la journalisation SignalR dans les environnements où vous n’avez pas accès aux constantes `LogLevel`.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Le tableau suivant répertorie les niveaux de journalisation disponibles. La valeur que vous fournissez à `configureLogging` définit le niveau de journalisation **minimale** qui sera enregistré. Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.

| Chaîne                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **ou** `information` | `LogLevel.Information` |
| `warn` **ou** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la méthode `configureLogging`.

Pour plus d’informations sur la journalisation, consultez la [documentationSignalR Diagnostics](xref:signalr/diagnostics).

Le client Java SignalR utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation. Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique. L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent être configurés dans l’appel de `WithUrl` (`withUrl` en JavaScript). Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports sont configurés en définissant le champ `transport` sur l’objet d’options fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Dans cette version du client Java, WebSockets est le seul transport disponible.

Dans le client Java, le transport est sélectionné à l’aide de la méthode `withTransport` sur le `HttpHubConnectionBuilder`. Le client Java utilise le transport WebSocket par défaut.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Le client Java SignalR ne prend pas encore en charge le transport de secours.

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification avec les demandes de SignalR, utilisez l’option `AccessTokenProvider` (`accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l’en-tête `Authorization` avec un type de `Bearer`). Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket). Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token`.

Dans le client .NET, l'option `AccessTokenProvider` peut être spécifiée à l’aide du délégué d’options de `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le champ `accessTokenFactory` sur l’objet d’options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client Java SignalR, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne](https://github.com/ReactiveX/RxJava) [unique\<RxJava>](https://reactivex.io/documentation/single.html). Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer les options de délai d’attente et de conservation des connexions

Des options supplémentaires pour configurer le comportement de conservation et de délai d’expiration sont disponibles sur l'objet `HubConnection` proprement dit :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `Closed` (`onclose` en JavaScript). Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `HandshakeTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `Closed` (`onclose` dans JavaScript). Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

Dans le Client .NET, les valeurs de délai d’attente sont spécifiées en tant que valeurs `TimeSpan`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onclose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onClose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `onClose`. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

Des options supplémentaires peuvent être configurées dans la méthode `WithUrl` (`withUrl` en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `SkipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `ClientCertificates` | Empty | Collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Empty | Collection de cookies HTTP à envoyer avec chaque requête HTTP. |
| `Credentials` | Empty | Informations d’identification à envoyer avec chaque requête HTTP. |
| `CloseTimeout` | 5 secondes | WebSocket uniquement. Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture. Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |
| `HttpMessageHandlerFactory` | `null` | Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre. Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle instance de `HttpMessageHandler`. **Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP. |
| `UseDefaultCredentials` | `false` | Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option JavaScript | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `skipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option Java | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `shouldSkipNegotiate` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |

---

Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être configurées avec les méthodes sur la `HttpHubConnectionBuilder` retournée à partir de la `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole possède des options de configuration de la sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans la méthode `Startup.ConfigureServices`. La méthode `AddJsonProtocol` prend un délégué qui reçoit un objet `options`. La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un objet JSON.net `JsonSerializerSettings` qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour. Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Dans le client .NET, la même méthode d’extension de `AddJsonProtocol` existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace de noms `Microsoft.Extensions.DependencyInjection` doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Pour plus d’informations, consultez [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des SignalR hubs :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondes | Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle. Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté. La valeur recommandée est le double de la valeur `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé un message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte. Lorsque vous modifiez `KeepAliveInterval`, modifiez le paramètre `ServerTimeout` / `serverTimeoutInMilliseconds` sur le client. La valeur recommandée `ServerTimeout` / `serverTimeoutInMilliseconds` est le double de la valeur `KeepAliveInterval`.  |
| `SupportedProtocols` | Tous les protocoles installés | Les protocoles pris en charge par ce hub. Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour certains hubs. |
| `EnableDetailedErrors` | `false` | Si `true`, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur. La valeur par défaut est `false`, car ces messages d’exception peuvent contenir des informations sensibles. |

Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l'appel `AddSignalR` dans `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options d'un Hub spécifique remplacent les options globales fournies dans la méthode `AddSignalR` et peuvent être configurées à l’aide de : <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options de configuration HTTP avancées

Utilisez `HttpConnectionDispatcherOptions` pour configurer des paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué à [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core SignalR:

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon. L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `AuthorizationData` | Les données sont collectées automatiquement à partir des attributs `Authorize` appliqués à la classe de concentrateur. | Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur. |
| `TransportMaxBufferSize` | 32 Ko | Le nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon. L'augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut affecter négativement la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Énumération d’indicateurs de bits des valeurs `HttpTransportType` qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport d’interrogation longue. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport d’interrogation longue propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `LongPolling` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique. Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment. |

Le transport WebSocket propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `WebSockets` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être utilisé pour définir l'en-tête `Sec-WebSocket-Protocol` sur une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client en entrée et doit retourner la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options du client

Les options du client peuvent être configurées sur le type de `HubConnectionBuilder` (disponible dans les clients .NET et JavaScript). Il est également disponible dans le client Java, mais la sous-classe `HttpHubConnectionBuilder` est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

La journalisation est configurée dans le client .NET à l’aide de la méthode `ConfigureLogging`. Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur. Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires. Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir la liste complète.

Par exemple, pour activer la journalisation de la Console, installez le package NuGet `Microsoft.Extensions.Logging.Console`. Appelez la méthode d’extension `AddConsole` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, il existe une méthode de `configureLogging` similaire. Fournissez une valeur de `LogLevel` indiquant le niveau minimal de messages du journal à produire. Les journaux sont écrits dans la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la méthode `configureLogging`.

Pour plus d’informations sur la journalisation, consultez la [documentationSignalR Diagnostics](xref:signalr/diagnostics).

Le client Java SignalR utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation. Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique. L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent être configurés dans l’appel de `WithUrl` (`withUrl` en JavaScript). Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports sont configurés en définissant le champ `transport` sur l’objet d’options fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Dans cette version du client Java, WebSockets est le seul transport disponible.

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification avec les demandes de SignalR, utilisez l’option `AccessTokenProvider` (`accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l’en-tête `Authorization` avec un type de `Bearer`). Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket). Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token`.

Dans le client .NET, l'option `AccessTokenProvider` peut être spécifiée à l’aide du délégué d’options de `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le champ `accessTokenFactory` sur l’objet d’options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client Java SignalR, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne](https://github.com/ReactiveX/RxJava) [unique\<RxJava>](https://reactivex.io/documentation/single.html). Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer les options de délai d’attente et de conservation des connexions

Des options supplémentaires pour configurer le comportement de conservation et de délai d’expiration sont disponibles sur l'objet `HubConnection` proprement dit :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `Closed` (`onclose` en JavaScript). Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `HandshakeTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `Closed` (`onclose` dans JavaScript). Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

Dans le Client .NET, les valeurs de délai d’attente sont spécifiées en tant que valeurs `TimeSpan`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onclose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onClose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `onClose`. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle. Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` défini sur le serveur, le serveur considère que le client est déconnecté. |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

Des options supplémentaires peuvent être configurées dans la méthode `WithUrl` (`withUrl` en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `SkipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `ClientCertificates` | Empty | Collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Empty | Collection de cookies HTTP à envoyer avec chaque requête HTTP. |
| `Credentials` | Empty | Informations d’identification à envoyer avec chaque requête HTTP. |
| `CloseTimeout` | 5 secondes | WebSocket uniquement. Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture. Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |
| `HttpMessageHandlerFactory` | `null` | Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre. Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle instance de `HttpMessageHandler`. **Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP. |
| `UseDefaultCredentials` | `false` | Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option JavaScript | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `skipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option Java | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `shouldSkipNegotiate` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |

---

Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être configurées avec les méthodes sur la `HttpHubConnectionBuilder` retournée à partir de la `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole possède des options de configuration de la sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans la méthode `Startup.ConfigureServices`. La méthode `AddJsonProtocol` prend un délégué qui reçoit un objet `options`. La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un objet JSON.net `JsonSerializerSettings` qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour. Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Dans le client .NET, la même méthode d’extension de `AddJsonProtocol` existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace de noms `Microsoft.Extensions.DependencyInjection` doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Pour plus d’informations, consultez [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des SignalR hubs :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé un message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte. Lorsque vous modifiez `KeepAliveInterval`, modifiez le paramètre `ServerTimeout` / `serverTimeoutInMilliseconds` sur le client. La valeur recommandée `ServerTimeout` / `serverTimeoutInMilliseconds` est le double de la valeur `KeepAliveInterval`.  |
| `SupportedProtocols` | Tous les protocoles installés | Les protocoles pris en charge par ce hub. Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour certains hubs. |
| `EnableDetailedErrors` | `false` | Si `true`, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur. La valeur par défaut est `false`, car ces messages d’exception peuvent contenir des informations sensibles. |

Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l'appel `AddSignalR` dans `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options d'un Hub spécifique remplacent les options globales fournies dans la méthode `AddSignalR` et peuvent être configurées à l’aide de : <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options de configuration HTTP avancées

Utilisez `HttpConnectionDispatcherOptions` pour configurer des paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué à [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core SignalR:

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon. L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `AuthorizationData` | Les données sont collectées automatiquement à partir des attributs `Authorize` appliqués à la classe de concentrateur. | Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur. |
| `TransportMaxBufferSize` | 32 Ko | Le nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon. L'augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut affecter négativement la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Énumération d’indicateurs de bits des valeurs `HttpTransportType` qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport d’interrogation longue. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport d’interrogation longue propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `LongPolling` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique. Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment. |

Le transport WebSocket propose des options supplémentaires qui peuvent être configurées à l’aide de la propriété `WebSockets` :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être utilisé pour définir l'en-tête `Sec-WebSocket-Protocol` sur une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client en entrée et doit retourner la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options du client

Les options du client peuvent être configurées sur le type de `HubConnectionBuilder` (disponible dans les clients .NET et JavaScript). Il est également disponible dans le client Java, mais la sous-classe `HttpHubConnectionBuilder` est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

La journalisation est configurée dans le client .NET à l’aide de la méthode `ConfigureLogging`. Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur. Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires. Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir la liste complète.

Par exemple, pour activer la journalisation de la Console, installez le package NuGet `Microsoft.Extensions.Logging.Console`. Appelez la méthode d’extension `AddConsole` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, il existe une méthode de `configureLogging` similaire. Fournissez une valeur de `LogLevel` indiquant le niveau minimal de messages du journal à produire. Les journaux sont écrits dans la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la méthode `configureLogging`.

Pour plus d’informations sur la journalisation, consultez la [documentationSignalR Diagnostics](xref:signalr/diagnostics).

Le client Java SignalR utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation. Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique. L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent être configurés dans l’appel de `WithUrl` (`withUrl` en JavaScript). Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports sont configurés en définissant le champ `transport` sur l’objet d’options fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification avec les demandes de SignalR, utilisez l’option `AccessTokenProvider` (`accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l’en-tête `Authorization` avec un type de `Bearer`). Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket). Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token`.

Dans le client .NET, l'option `AccessTokenProvider` peut être spécifiée à l’aide du délégué d’options de `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le champ `accessTokenFactory` sur l’objet d’options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client Java SignalR, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne](https://github.com/ReactiveX/RxJava) [unique\<RxJava>](https://reactivex.io/documentation/single.html). Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer les options de délai d’attente et de conservation des connexions

Des options supplémentaires pour configurer le comportement de conservation et de délai d’expiration sont disponibles sur l'objet `HubConnection` proprement dit :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `Closed` (`onclose` en JavaScript). Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |
| `HandshakeTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `Closed` (`onclose` dans JavaScript). Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Dans le Client .NET, les valeurs de délai d’attente sont spécifiées en tant que valeurs `TimeSpan`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onclose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’expiration de l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l’événement `onClose`. Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration. La valeur recommandée est un nombre au moins double de la valeur de `KeepAliveInterval` du serveur, afin de laisser le temps de recevoir les commandes ping. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai d’attente du protocole de transfert initial du serveur. Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l’événement `onClose`. Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau. Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocoleSignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

Des options supplémentaires peuvent être configurées dans la méthode `WithUrl` (`withUrl` en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `SkipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `ClientCertificates` | Empty | Collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Empty | Collection de cookies HTTP à envoyer avec chaque requête HTTP. |
| `Credentials` | Empty | Informations d’identification à envoyer avec chaque requête HTTP. |
| `CloseTimeout` | 5 secondes | WebSocket uniquement. Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture. Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |
| `HttpMessageHandlerFactory` | `null` | Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre. Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle instance de `HttpMessageHandler`. **Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP. |
| `UseDefaultCredentials` | `false` | Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option JavaScript | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `skipNegotiation` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option Java | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP. |
| `shouldSkipNegotiate` | `false` | Définissez cette valeur sur `true` pour ignorer l’étape de négociation. **Pris en charge uniquement lorsque le transport WebSocket est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure SignalR. |
| `withHeader` `withHeaders` | Empty | Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP. |

---

Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être configurées avec les méthodes sur la `HttpHubConnectionBuilder` retournée à partir de la `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end