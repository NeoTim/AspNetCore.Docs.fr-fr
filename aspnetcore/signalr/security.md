---
title: Considérations relatives à la sécurité dans ASP.NET Core Signalr
author: bradygaster
description: Découvrez comment utiliser l’authentification et les autorisations dans ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: a52db2ff51c55f7299d63aa3c7398f99727e0694
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746555"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Considérations relatives à la sécurité dans ASP.NET Core Signalr

Par [Andrew Stanton-infirmière](https://twitter.com/anurse)

Cet article fournit des informations sur la sécurisation de Signalr.

## <a name="cross-origin-resource-sharing"></a>Partage des ressources Cross-Origin

Le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/) peut être utilisé pour autoriser les connexions signalr entre origines dans le navigateur. Si le code JavaScript est hébergé sur un domaine différent de celui de l’application Signalr, l' [intergiciel (middleware) cors](xref:security/cors) doit être activé pour permettre au JavaScript de se connecter à l’application signalr. Autorisez les requêtes Cross-Origin uniquement à partir des domaines que vous approuvez ou contrôlez. Par exemple :

* Votre site est hébergé sur`http://www.example.com`
* Votre application Signalr est hébergée sur`http://signalr.example.com`

CORS doit être configuré dans l’application Signalr pour autoriser uniquement l’origine `www.example.com`.

Pour plus d’informations sur la configuration de CORS, consultez [activer les demandes Cross-Origin (cors)](xref:security/cors). Signalr **requiert** les stratégies cors suivantes :

* Autorisez les origines attendues spécifiques. L’autorisation d’une origine est possible, mais elle n’est **pas** sécurisée ou recommandée.
* Les méthodes `GET` http `POST` et doivent être autorisées.
* Les informations d’identification doivent être activées, même lorsque l’authentification n’est pas utilisée.

Par exemple, la stratégie cors suivante permet à un client de navigateur signalr `https://example.com` hébergé sur d’accéder à l’application signalr hébergée sur : `https://signalr.example.com`

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> Signalr n’est pas compatible avec la fonctionnalité CORS intégrée dans Azure App Service.

## <a name="websocket-origin-restriction"></a>Restriction d’origine WebSocket

::: moniker range=">= aspnetcore-2.2"

Les protections fournies par CORS ne s’appliquent pas aux WebSockets. Pour une restriction d’origine sur les WebSockets, consultez [restriction d’origine des WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Les protections fournies par CORS ne s’appliquent pas aux WebSockets. Les navigateurs **:**

* n’effectuent pas de requêtes préalables CORS ;
* respectent les restrictions spécifiées dans les en-têtes `Access-Control` quand ils effectuent des requêtes WebSocket.

Toutefois, les navigateurs envoient l’en-tête `Origin` au moment de l’émission des requêtes WebSocket. Les applications doivent être configurées de manière à valider ces en-têtes, le but étant de vérifier que seuls les WebSockets provenant des origines attendues sont autorisés.

Dans ASP.net Core 2,1 et versions ultérieures, la validation d’en-tête peut être obtenue à l’aide d’un intergiciel ( `Configure`middleware) personnalisé placé **précédemment `UseSignalR`et de l’intergiciel (middleware) d’authentification** dans :

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> L’en-tête `Origin` est contrôlé par le client et, comme l’en-tête `Referer`, peut être falsifié. Ces en-têtes ne doivent **pas** être utilisés en tant que mécanisme d’authentification.

::: moniker-end

## <a name="access-token-logging"></a>Journalisation des jetons d’accès

Lorsque vous utilisez des WebSockets ou des événements envoyés par le serveur, le client du navigateur envoie le jeton d’accès dans la chaîne de requête. La réception du jeton d’accès via la chaîne de requête est généralement aussi sécurisée que l’utilisation de l’en-tête standard `Authorization` . Vous devez toujours utiliser le protocole HTTPs pour garantir une connexion sécurisée de bout en bout entre le client et le serveur. De nombreux serveurs Web consignent l’URL pour chaque demande, y compris la chaîne de requête. La journalisation des URL peut enregistrer le jeton d’accès. ASP.NET Core enregistre l’URL pour chaque demande par défaut, qui inclut la chaîne de requête. Par exemple :

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Si vous avez des doutes quant à la `Microsoft.AspNetCore.Hosting` `Warning` journalisation de ces données avec les journaux de votre serveur, vous pouvez désactiver cette journalisation entièrement en configurant l’enregistreur d’événements au `Info` niveau ou au-dessus (ces messages sont écrits au niveau). Pour plus d’informations, consultez la documentation relative au [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) . Si vous souhaitez toujours enregistrer certaines informations de demande, vous pouvez [écrire un intergiciel (middleware](xref:fundamentals/middleware/write) ) pour enregistrer les données dont vous avez besoin `access_token` et filtrer la valeur de la chaîne de requête (le cas échéant).

## <a name="exceptions"></a>Exceptions

Les messages d’exception sont généralement considérés comme des données sensibles qui ne doivent pas être révélées à un client. Par défaut, Signalr n’envoie pas les détails d’une exception levée par une méthode de concentrateur au client. Au lieu de cela, le client reçoit un message générique indiquant qu’une erreur s’est produite. La remise de message d’exception au client peut être remplacée (par exemple, dans le développement ou [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)le test) par. Les messages d’exception ne doivent pas être exposés au client dans les applications de production.

## <a name="buffer-management"></a>Gestion des tampons

Signalr utilise des tampons par connexion pour gérer les messages entrants et sortants. Par défaut, Signalr limite ces tampons à 32 Ko. Le plus grand message qu’un client ou un serveur peut envoyer est de 32 Ko. La mémoire maximale consommée par une connexion pour les messages est de 32 Ko. Si vos messages sont toujours inférieurs à 32 Ko, vous pouvez réduire la limite, qui :

* Empêche un client d’envoyer un message plus volumineux.
* Le serveur n’a jamais besoin d’allouer de grandes mémoires tampons pour accepter des messages.

Si vos messages sont supérieurs à 32 Ko, vous pouvez augmenter la limite. L’amélioration de cette limite signifie :

* Le client peut forcer le serveur à allouer de grandes mémoires tampons de mémoire.
* L’allocation de serveurs de mémoires tampons de grande taille peut réduire le nombre de connexions simultanées.

Il existe des limites pour les messages entrants et sortants, qui peuvent [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) être configurés `MapHub`sur l’objet configuré dans :

* `ApplicationMaxBufferSize`représente le nombre maximal d’octets du client que le serveur met en mémoire tampon. Si le client tente d’envoyer un message d’une taille supérieure à cette limite, la connexion peut être fermée.
* `TransportMaxBufferSize`représente le nombre maximal d’octets que le serveur peut envoyer. Si le serveur tente d’envoyer un message (y compris les valeurs de retour des méthodes de concentrateur) supérieures à cette limite, une exception est levée.

La définition de la `0` limite à désactive la limite. La suppression de la limite permet à un client d’envoyer un message de toute taille. Les clients malveillants qui envoient des messages volumineux peuvent entraîner l’allocation de mémoire supplémentaire. L’utilisation excessive de la mémoire peut réduire de manière significative le nombre de connexions simultanées.
