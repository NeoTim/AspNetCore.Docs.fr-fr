---
title: Différences entre SignalR et ASP.NET Core SignalR
author: bradygaster
description: Différences entre SignalR et ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: cca9a0cb0c46fc25eb5d1f7127d31fd3ab92f0b4
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880361"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>Différences entre ASP.NET SignalR et ASP.NET Core SignalR

ASP.NET Core SignalR n’est pas compatible avec les clients ou les serveurs pour ASP.NET SignalR. Cet article détaille les fonctionnalités qui ont été supprimées ou modifiées dans ASP.NET Core SignalR.

## <a name="how-to-identify-the-opno-locsignalr-version"></a>Comment identifier la version de SignalR

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Package NuGet de serveur | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Aucun. Inclus dans l’infrastructure partagée [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Packages NuGet client | [Microsoft. AspNet.SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore.SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Package NPM du client JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Client Java | [Dépôt github](https://github.com/SignalR/java-client) (déconseillé)  | Package Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Type d’application serveur | ASP.NET (System. Web) ou auto-hôte OWIN | ASP.NET Core |
| Plateformes serveur prises en charge | .NET Framework 4,5 ou version ultérieure | .NET Core 3,0 ou version ultérieure |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Package NuGet de serveur | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Packages NuGet client | [Microsoft. AspNet.SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore.SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Package NPM du client JavaScript | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Client Java | [Dépôt github](https://github.com/SignalR/java-client) (déconseillé)  | Package Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Type d’application serveur | ASP.NET (System. Web) ou auto-hôte OWIN | ASP.NET Core |
| Plateformes serveur prises en charge | .NET Framework 4,5 ou version ultérieure | .NET Framework 4.6.1 ou ultérieur<br>.NET Core 2,1 ou version ultérieure |

::: moniker-end

## <a name="feature-differences"></a>Différences de fonctionnalités

### <a name="automatic-reconnects"></a>Reconnexions automatiques

::: moniker range=">= aspnetcore-3.0"

Dans ASP.NET SignalR:

* Par défaut, SignalR tente de se reconnecter au serveur si la connexion est abandonnée. 

Dans ASP.NET Core SignalR:

* Les reconnexions automatiques s’abonnent à la fois avec le [client .net](xref:signalr/dotnet-client#automatically-reconnect) et le [client JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Avant ASP.NET Core 3,0, SignalR ne prend pas en charge les reconnexions automatiques. Si le client est déconnecté, l’utilisateur doit démarrer explicitement une nouvelle connexion pour se reconnecter. Dans ASP.NET SignalR, SignalR tente de se reconnecter au serveur si la connexion est abandonnée.

::: moniker-end

### <a name="protocol-support"></a>Prise en charge de protocole

ASP.NET Core SignalR prend en charge JSON, ainsi qu’un nouveau protocole binaire basé sur [MessagePack](xref:signalr/messagepackhubprotocol). En outre, des protocoles personnalisés peuvent être créés.

### <a name="transports"></a>Transports

Le transport de trame éternel n’est pas pris en charge dans ASP.NET Core SignalR.

## <a name="differences-on-the-server"></a>Différences sur le serveur

Les ASP.NET Core SignalR bibliothèques côté serveur sont incluses dans [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), qui est utilisé dans le modèle d' **application Web ASP.net Core** pour les projets Razor et MVC.

ASP.NET Core SignalR est un intergiciel (middleware) ASP.NET Core. Elle doit être configurée en appelant <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> dans `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Pour configurer le routage, mappez les itinéraires aux hubs à l’intérieur de l’appel de méthode <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> dans la méthode `Startup.Configure`.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Pour configurer le routage, mappez les itinéraires aux hubs à l’intérieur de l’appel de méthode <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> dans la méthode `Startup.Configure`.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sessions rémanentes

Le modèle ScaleOut pour ASP.NET SignalR permet aux clients de se reconnecter et d’envoyer des messages à n’importe quel serveur de la batterie. Dans ASP.NET Core SignalR, le client doit interagir avec le même serveur pendant la durée de la connexion. Pour les ScaleOut utilisant des ReDim, cela signifie que les sessions rémanentes sont requises. Pour ScaleOut à l’aide d' [Azure SignalR service](/azure/azure-signalr/), les sessions rémanentes ne sont pas nécessaires, car le service gère les connexions aux clients.

### <a name="single-hub-per-connection"></a>Concentrateur unique par connexion

Dans ASP.NET Core SignalR, le modèle de connexion a été simplifié. Les connexions sont apportées directement à un hun unique, plutôt qu’à une connexion unique utilisée pour partager l’accès à plusieurs hubs.

### <a name="streaming"></a>Diffusion en continu

ASP.NET Core SignalR prend désormais en charge la [diffusion en continu des données](xref:signalr/streaming) du concentrateur vers le client.

### <a name="state"></a>État

La possibilité de passer un état arbitraire entre les clients et le Hub (souvent appelé `HubState`) a été supprimée, ainsi que la prise en charge des messages de progression. Il n’existe aucun équivalent de proxy de Hub pour le moment.

### <a name="persistentconnection-removal"></a>Suppression de PersistentConnection

Dans ASP.NET Core SignalR, la classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) a été supprimée.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core a intégré l’injection de dépendances dans le Framework. Les services peuvent utiliser DI pour accéder à [HubContext](xref:signalr/hubcontext). L’objet `GlobalHost` utilisé dans ASP.NET SignalR pour obtenir un `HubContext` n’existe pas dans ASP.NET Core SignalR.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR ne prend pas en charge les modules `HubPipeline`.

## <a name="differences-on-the-client"></a>Différences sur le client

### <a name="typescript"></a>TypeScript

Le client ASP.NET Core SignalR est écrit dans la [machine à écrire](https://www.typescriptlang.org/). Vous pouvez écrire en JavaScript ou à la machine à écrire lors de l’utilisation du [client JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npm"></a>Le client JavaScript est hébergé sur NPM

::: moniker range=">= aspnetcore-3.0"

Dans les versions de ASP.NET, le client JavaScript a été obtenu via un package NuGet dans Visual Studio. Dans les versions ASP.NET Core, le package NPM [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) contient les bibliothèques JavaScript. Ce package n’est pas inclus dans le modèle **Application Web ASP.NET Core**. Utiliser npm pour obtenir et installer le package npm `@microsoft/signalr`.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Dans les versions de ASP.NET, le client JavaScript a été obtenu via un package NuGet dans Visual Studio. Dans les versions ASP.NET Core, le package NPM [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) contient les bibliothèques JavaScript. Ce package n’est pas inclus dans le modèle **Application Web ASP.NET Core**. Utiliser npm pour obtenir et installer le package npm `@aspnet/signalr`.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

La dépendance sur jQuery a été supprimée, mais les projets peuvent toujours utiliser jQuery.

### <a name="internet-explorer-support"></a>Prise en charge d’Internet Explorer

ASP.NET Core SignalR nécessite Microsoft Internet Explorer 11 ou version ultérieure (ASP.NET SignalR pris en charge par Microsoft Internet Explorer 8 et versions ultérieures).

### <a name="javascript-client-method-syntax"></a>Syntaxe de méthode du client JavaScript

::: moniker range=">= aspnetcore-3.0"

La syntaxe JavaScript a été modifiée par rapport à la version ASP.NET de SignalR. Au lieu d’utiliser l’objet `$connection`, créez une connexion en utilisant l'API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder).

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Utilisez la méthode [on](/javascript/api/@microsoft/signalr/HubConnection#on) pour spécifier les méthodes clientes que le hub peut appeler.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

La syntaxe JavaScript a été modifiée par rapport à la version ASP.NET de SignalR. Au lieu d’utiliser l’objet `$connection`, créez une connexion en utilisant l'API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder).

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Utilisez la méthode [on](/javascript/api/@aspnet/signalr/HubConnection#on) pour spécifier les méthodes clientes que le hub peut appeler.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Après avoir créé la méthode du client, démarrez la connexion au hub. Chaînez une méthode [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pour loguer ou gérer les erreurs.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Proxies Hub

::: moniker range=">= aspnetcore-3.0"

Les serveurs proxy de hub ne sont plus générés automatiquement. Au lieu de cela, le nom de méthode est passé dans l'[appel](/javascript/api/@microsoft/signalr/hubconnection#invoke) à l'API sous forme de chaîne.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Les serveurs proxy de hub ne sont plus générés automatiquement. Au lieu de cela, le nom de méthode est passé dans l'[appel](/javascript/api/@aspnet/signalr/hubconnection#invoke) à l'API sous forme de chaîne.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET et autres clients

[Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Le package NuGet client contient les bibliothèques clientes .net pour ASP.NET Core SignalR.

Utilisez la <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> pour créer et générer une instance d’une connexion à un concentrateur.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Différences ScaleOut

ASP.NET SignalR prend en charge les SQL Server et les ReDim. ASP.NET Core SignalR prend en charge Azure SignalR service et les ReDim.

### <a name="aspnet"></a>ASP.NET

* [SignalR ScaleOut avec Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR ScaleOut avec Redims](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR ScaleOut avec SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Service SignalR Azure](/azure/azure-signalr/)
* [Fond de panier ReDim](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Hubs](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
* [Client .NET](xref:signalr/dotnet-client)
* [Plateformes prises en charge](xref:signalr/supported-platforms)
