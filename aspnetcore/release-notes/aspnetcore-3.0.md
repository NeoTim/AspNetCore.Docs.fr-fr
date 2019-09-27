---
title: Nouveautés de ASP.NET Core 3,0
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: aspnetcore-3.0
ms.openlocfilehash: c1b61fee7264b972c70dbfa8f1461e33e3645746
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317657"
---
# <a name="whats-new-in-aspnet-core-30"></a>Nouveautés de ASP.NET Core 3,0

Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 3,0 avec des liens vers la documentation appropriée.

## <a name="blazor"></a>Blazor

Éblouissant est une nouvelle infrastructure dans ASP.NET Core pour la création d’une interface utilisateur Web interactive côté client avec .NET :

* Créez des interfaces utilisateur interactives riches à l’aide de C# plutôt que JavaScript.
* Partagez la logique d’application côté serveur et côté client écrite dans .NET.
* Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.

Scénarios pris en charge pour le Framework éblouissant :

* Composants de l’interface utilisateur réutilisables (composants Razor)
* Routage côté client
* Dispositions des composants
* Prise en charge de l’injection de dépendances
* Formulaires et validation
* Créer des bibliothèques de composants avec des bibliothèques de classes Razor
* Interopérabilité JavaScript

Pour plus d'informations, consultez <xref:blazor/index>.

### <a name="blazor-server"></a>Serveur Blazor

Blazor dissocie la logique de rendu de composant de la manière dont les mises à jour de l’interface utilisateur sont appliquées. Le serveur éblouissant fournit la prise en charge de l’hébergement des composants Razor sur le serveur dans une application ASP.NET Core. Les mises à jour de l’interface utilisateur sont gérées par le biais d’une connexion SignalR. Le serveur éblouissant est pris en charge dans ASP.NET Core 3,0.

### <a name="blazor-webassembly-preview"></a>Webassembly éblouissant (version préliminaire)

Les applications éblouissantes peuvent également être exécutées directement dans le navigateur à l’aide d’un Runtime .NET basé sur webassembly. Le webassembly éblouissant est en version préliminaire et *n’est pas* pris en charge dans ASP.net Core 3,0. Le webassembly éblouissant sera pris en charge dans une prochaine version de ASP.NET Core.

### <a name="razor-components"></a>Composants Razor

Les applications éblouissantes sont générées à partir de composants. Les composants sont des blocs autonomes de l’interface utilisateur (IU), tels qu’une page, une boîte de dialogue ou un formulaire. Les composants sont des classes .NET normales qui définissent la logique de rendu de l’interface utilisateur et les gestionnaires d’événements côté client. Vous pouvez créer des applications Web riches et interactives sans JavaScript.

Les composants de éblouissant sont généralement créés à l’aide de syntaxe Razor, un mélange naturel de C#code HTML et. Les composants Razor sont similaires aux vues Razor Pages et MVC en ce sens qu’ils utilisent tous deux Razor. Contrairement aux pages et aux vues, qui sont basées sur un modèle de requête-réponse, les composants sont utilisés spécifiquement pour gérer la composition de l’interface utilisateur.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Est un Framework RPC (Remote Procedure Call, appel de procédure distante) très performant et populaire.
* Offre une approche consignes strictes Contract-First pour le développement d’API.
* Utilise des technologies modernes telles que :

  * HTTP/2 pour le transport.
  * Mémoires tampons de protocole en tant que langage de description d’interface.
  * Format de sérialisation binaire.
* Fournit des fonctionnalités telles que :

  * Authentication
  * Streaming bidirectionnel et contrôle de flux.
  * Annulation et délais d’attente.

la fonctionnalité gRPC dans ASP.NET Core 3,0 comprend les éléments suivants :

* [GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; un framework ASP.net Core pour l’hébergement des services GRPC. gRPC sur ASP.NET Core s’intègre aux fonctionnalités de ASP.NET Core standard, telles que la journalisation, l’injection de dépendances, l’authentification et l’autorisation.
* [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; , client GRPC pour .net Core, qui s’appuie sur `HttpClient`ce qui vous est familier.
* [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; GRPC intégration du client `HttpClientFactory`avec.

Pour plus d'informations, consultez <xref:grpc/index>.

## <a name="signalr"></a>SignalR

Consultez [mettre à jour le code signalr](xref:migration/22-to-30#signalr) pour obtenir des instructions de migration. Signalr utilise `System.Text.Json` désormais pour sérialiser/désérialiser les messages JSON. Consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) pour obtenir des instructions `Newtonsoft.Json`sur la restauration du sérialiseur.

Dans les clients JavaScript et .NET pour Signalr, la prise en charge a été ajoutée pour la reconnexion automatique. Par défaut, le client tente de se reconnecter immédiatement, puis de réessayer au bout de 2, 10 et 30 secondes si nécessaire. Si le client se reconnecte avec succès, il reçoit un nouvel ID de connexion. La reconnexion automatique est opt-in :

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Les intervalles de reconnexion peuvent être spécifiés en passant un tableau de durées basées sur la milliseconde :

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Une implémentation personnalisée peut être passée pour le contrôle total des intervalles de reconnexion.

Si la reconnexion échoue après le dernier intervalle de reconnexion :

* Le client considère que la connexion est hors connexion.
* Le client cesse de tenter de se reconnecter.

Lors des tentatives de reconnexion, mettez à jour l’interface utilisateur de l’application pour notifier l’utilisateur que la reconnexion est tentée.

Pour fournir des commentaires sur l’interface utilisateur lorsque la connexion est interrompue, l’API du client Signalr a été développée pour inclure les gestionnaires d’événements suivants :

* `onreconnecting`:  Offre aux développeurs la possibilité de désactiver l’interface utilisateur ou de laisser les utilisateurs savoir que l’application est hors connexion.
* `onreconnected`: Offre aux développeurs la possibilité de mettre à jour l’interface utilisateur une fois la connexion rétablie.

Le code suivant utilise `onreconnecting` pour mettre à jour l’interface utilisateur lors de la tentative de connexion :

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Le code suivant utilise `onreconnected` pour mettre à jour l’interface utilisateur sur la connexion :

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

Signalr 3,0 et versions ultérieures fournit une ressource personnalisée aux gestionnaires d’autorisations lorsqu’une méthode de concentrateur requiert une autorisation. La ressource est une instance de `HubInvocationContext`. Le `HubInvocationContext` comprend les éléments suivants :

* `HubCallerContext`
* Nom de la méthode de concentrateur appelée.
* Arguments de la méthode de concentrateur.

Prenons l’exemple suivant d’une application de salle de conversation permettant à plusieurs entreprises de se connecter via Azure Active Directory. Toute personne disposant d’un compte Microsoft peut se connecter à chat, mais seuls les membres de l’organisation propriétaire peuvent interdire les utilisateurs ou afficher les historiques de conversation des utilisateurs. L’application peut restreindre certaines fonctionnalités d’utilisateurs spécifiques.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Dans le code précédent, `DomainRestrictedRequirement` sert de personnalisé. `IAuthorizationRequirement` Étant donné `HubInvocationContext` que le paramètre de ressource est passé, la logique interne peut :

* Inspectez le contexte dans lequel le Hub est appelé.
* Prendre des décisions pour permettre à l’utilisateur d’exécuter des méthodes de concentrateur individuelles.

Les méthodes de concentrateur individuelles peuvent être décorées avec le nom de la stratégie vérifiée par le code au moment de l’exécution. À mesure que les clients tentent d’appeler des méthodes `DomainRestrictedRequirement` de concentrateur individuelles, le gestionnaire exécute et contrôle l’accès aux méthodes. En fonction de la façon `DomainRestrictedRequirement` dont les contrôles accèdent à :

* Tous les utilisateurs connectés peuvent appeler la `SendMessage` méthode.
* Seuls les utilisateurs qui se sont connectés avec `@jabbr.net` une adresse de messagerie peuvent afficher les historiques des utilisateurs.
* Seuls `bob42@jabbr.net` les utilisateurs peuvent être interdits dans la salle de conversation.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

La création `DomainRestricted` de la stratégie peut impliquer :

* Dans *Startup.cs*, ajout de la nouvelle stratégie.
* Fournissez la `DomainRestrictedRequirement` spécification personnalisée sous la forme d’un paramètre.
* Inscription `DomainRestricted` auprès de l’intergiciel d’autorisation.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Les concentrateurs signalr utilisent le [routage des points de terminaison](xref:fundamentals/routing). La connexion du concentrateur signalr a été précédemment effectuée explicitement :

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Dans la version précédente, les développeurs devaient relier les contrôleurs, les pages Razor et les hubs à différents emplacements. La connexion explicite produit une série de segments de routage quasiment identiques :

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Les concentrateurs signalr 3,0 peuvent être routés via le routage de point de terminaison. Avec le routage de point de terminaison, tous les routages peuvent généralement être configurés dans `UseRouting`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core Signalr 3,0 ajouté :

Diffusion en continu entre le client et le serveur. Avec la diffusion en continu client à serveur, les méthodes côté serveur peuvent prendre des instances d' `IAsyncEnumerable<T>` un `ChannelReader<T>`ou d’un. Dans l’exemple C# suivant, la `UploadStream` méthode sur le Hub reçoit un flux de chaînes du client :

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Les applications clientes .NET peuvent passer `IAsyncEnumerable<T>` une `ChannelReader<T>` instance ou en `stream` tant qu’argument `UploadStream` de la méthode de concentrateur ci-dessus.

Une fois `for` que la boucle est terminée et que la fonction locale se termine, l’exécution du flux est envoyée :

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Les applications `Subject` clientes JavaScript utilisent signalr (ou [objet RxJS](https://rxjs.dev/api/index/class/Subject)) pour l' `stream` argument de la `UploadStream` méthode de concentrateur ci-dessus.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Le code JavaScript peut utiliser la `subject.next` méthode pour gérer les chaînes à mesure qu’elles sont capturées et prêtes à être envoyées au serveur.

```javascript
subject.next("example");
subject.complete();
```

À l’aide de code comme les deux extraits de code précédents, vous pouvez créer des expériences de diffusion en continu en temps réel.

### <a name="new-json-serialization"></a>Nouvelle sérialisation JSON

ASP.net Core 3,0 utilise <xref:System.Text.Json> désormais par défaut pour la sérialisation JSON :

* Lit et écrit JSON de manière asynchrone.
* Est optimisé pour le texte UTF-8.
* En général, performances `Newtonsoft.Json`supérieures à.

Pour ajouter Json.NET à ASP.NET Core 3,0, consultez [Ajouter la prise en charge du format JSON basé sur Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nouvelles directives Razor

La liste suivante contient les nouvelles directives Razor :

* [@attribute](xref:mvc/views/razor#attribute)&ndash; La`@attribute` directive applique l’attribut donné à la classe de la page ou de la vue générée. Par exemple, `@attribute [Authorize]`.
* [@implements](xref:mvc/views/razor#implements)&ndash; La`@implements` directive implémente une interface pour la classe générée. Par exemple, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 prend en charge l’authentification et l’autorisation pour les API Web et SPAs

[IdentityServer4](https://identityserver.io) est un Framework OpenID Connect et OAuth 2,0 pour ASP.net Core 3,0. IdentityServer4 active les fonctionnalités de sécurité suivantes :

* Authentification en tant que service (AaaS)
* Authentification unique (SSO) sur plusieurs types d’applications
* Contrôle d’accès pour les API
* Passerelle de Fédération

Pour plus d’informations, consultez [Bienvenue dans IdentityServer4](http://docs.identityserver.io/en/latest/index.html).

## <a name="certificate-and-kerberos-authentication"></a>Certificat et authentification Kerberos

L’authentification par certificat requiert :

* Configuration du serveur pour accepter les certificats.
* Ajout de l’intergiciel (middleware `Startup.Configure`) d’authentification dans.
* Ajout du service d’authentification par `Startup.ConfigureServices`certificat dans.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Les options d’authentification par certificat incluent la possibilité d’effectuer les opérations suivantes :

* Accepter des certificats auto-signés.
* Vérifiez la révocation des certificats.
* Vérifiez que le certificat offerts contient les indicateurs d’utilisation appropriés.

Un principal d’utilisateur par défaut est construit à partir des propriétés du certificat. Le principal d’utilisateur contient un événement qui permet d’ajouter ou de remplacer le principal. Pour plus d'informations, consultez <xref:security/authentication/certauth>.

[L’authentification Windows](/windows-server/security/windows-authentication/windows-authentication-overview) a été étendue sur Linux et MacOS. Dans les versions précédentes, l’authentification Windows était limitée à [IIS](xref:host-and-deploy/iis/index) et [HttpSys](xref:fundamentals/servers/httpsys). Dans ASP.NET Core 3,0, [Kestrel](xref:fundamentals/servers/kestrel) a la possibilité d’utiliser Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)et [NTLM sur Windows](/windows-server/security/kerberos/ntlm-overview), Linux et MacOS pour les hôtes joints à un domaine Windows. La prise en charge Kestrel de ces schémas d’authentification est assurée par le package [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) . Comme pour les autres services d’authentification, configurez l’authentification pour l’ensemble de l’application, puis configurez le service :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Configuration requise pour l’ordinateur hôte :

* Les hôtes Windows doivent avoir des [noms de principal du service](/windows/win32/ad/service-principal-names) (SPN) ajoutés au compte d’utilisateur qui héberge l’application.
* Les ordinateurs Linux et macOS doivent être joints au domaine.
  * Les noms de principal du service doivent être créés pour le processus Web.
  * Les [fichiers keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) doivent être générés et configurés sur l’ordinateur hôte.

Pour plus d'informations, consultez <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Modifications du modèle

Les modèles d’interface utilisateur Web (Razor Pages, MVC avec contrôleur et vues) ont les éléments suivants supprimés :

* L’interface utilisateur de consentement du cookie n’est plus incluse. Pour activer la fonctionnalité de consentement de cookie dans une application générée par un modèle <xref:security/gdpr>ASP.net Core 3,0, consultez.
* Les scripts et les ressources statiques associées sont désormais référencés en tant que fichiers locaux au lieu d’utiliser CDN. Pour plus d’informations, consultez [les scripts et les ressources statiques associées sont désormais référencés en tant que fichiers locaux au lieu d’utiliser CDN en fonction de l’environnement actuel (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).

Modèle angulaire mis à jour pour utiliser le 8 angulaire.

Le modèle de bibliothèque de classes Razor (RCL) est par défaut le développement de composants Razor par défaut. Une nouvelle option de modèle dans Visual Studio fournit la prise en charge des modèles pour les pages et les vues. Lorsque vous créez un RCL à partir du modèle dans une interface de commande `-support-pages-and-views` , transmettez l’option (`dotnet new razorclasslib -support-pages-and-views`).

## <a name="generic-host"></a>Hôte générique

Les modèles ASP.NET Core 3,0 utilisent <xref:fundamentals/host/generic-host>. Versions précédentes utilisées <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. L’utilisation de l’hôte générique .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core () permet une meilleure intégration des applications ASP.net core avec d’autres scénarios de serveur qui ne sont pas spécifiques au Web. Pour plus d’informations, consultez [HostBuilder remplace WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configuration de l’hôte

Avant la sortie de ASP.net Core 3,0, les variables d’environnement précédées `ASPNETCORE_` de ont été chargées pour la configuration d’hôte de l’hôte Web. Dans 3,0, `AddEnvironmentVariables` est utilisé pour charger les variables d’environnement `DOTNET_` précédées de pour `CreateDefaultBuilder`la configuration de l’hôte avec.

### <a name="changes-to-startup-contructor-injection"></a>Modifications apportées à l’injection de constructeur de démarrage

L’hôte générique prend en charge uniquement les types `Startup` suivants pour l’injection de constructeur :

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tous les services peuvent toujours être injectés directement comme arguments de `Startup.Configure` la méthode. Pour plus d’informations, consultez l' [hôte générique limite l’injection de constructeur de démarrage (ASPNET/announcements #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* La configuration Kestrel a été mise à jour pour la migration vers l’hôte générique. Dans 3,0, Kestrel est configuré sur le générateur d’hôte Web fourni `ConfigureWebHostDefaults`par.
* Les adaptateurs de connexion ont été supprimés de Kestrel et remplacés par un intergiciel de connexion, qui est similaire à l’intergiciel HTTP dans le pipeline ASP.NET Core, mais pour les connexions de niveau inférieur.
* La couche de transport Kestrel a été exposée en tant qu' `Connections.Abstractions`interface publique dans.
* L’ambiguïté entre les en-têtes et les codes de fin a été résolue en déplaçant les en-têtes de fin vers une nouvelle collection.
* Les API d’e/s `HttpReqeuest.Body.Read`synchrones, telles que, sont une source commune de privation de thread conduisant à des blocages d’application. Dans 3,0, `AllowSynchronousIO` est désactivé par défaut.

Pour plus d'informations, consultez <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 activé par défaut

HTTP/2 est activé par défaut dans Kestrel pour les points de terminaison HTTPs. La prise en charge de HTTP/2 pour IIS ou HTTP. sys est activée lorsqu’elle est prise en charge par le système d’exploitation.

## <a name="eventcounters-on-request"></a>EventCounters à la demande

L’EventSource d’hébergement `Microsoft.AspNetCore.Hosting`,, émet les nouveaux <xref:System.Diagnostics.Tracing.EventCounter> types suivants en rapport avec les demandes entrantes :

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Routage du point de terminaison

Le routage des points de terminaison, qui permet aux frameworks (par exemple, MVC) de fonctionner correctement avec l’intergiciel, est amélioré :

* L’ordre des intergiciels et des points de terminaison est configurable dans le pipeline de traitement `Startup.Configure`des requêtes de.
* Les points de terminaison et l’intergiciel (middleware) sont bien adaptés à d’autres technologies basées sur les ASP.NET Core, telles que les contrôles d’intégrité.
* Les points de terminaison peuvent implémenter une stratégie, telle que CORS ou Authorization, dans l’intergiciel et le MVC.
* Les filtres et les attributs peuvent être placés sur les méthodes dans les contrôleurs.

Pour plus d'informations, consultez <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Contrôles d’intégrité

Les contrôles d’intégrité utilisent le routage de point de terminaison avec l’hôte générique. Dans `Startup.Configure`, appelez `MapHealthChecks` sur le générateur de points de terminaison avec l’URL de point de terminaison ou le chemin d’accès relatif :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Les points de terminaison de contrôle d’intégrité peuvent :

* Spécifiez un ou plusieurs hôtes/ports autorisés.
* Exiger une autorisation.
* Exiger CORS.

Pour plus d’informations, consultez les articles suivants :

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Canaux sur HttpContext

Il est maintenant possible de lire le corps de la demande et d’écrire le corps <xref:System.IO.Pipelines> de la réponse à l’aide de l’API. La clé publique du signataire doit être fournie à la classe <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`la propriété fournit <xref:System.IO.Pipelines.PipeReader> un qui peut être utilisé pour lire le corps de la requête. La clé publique du signataire doit être fournie à la classe <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`la propriété fournit <xref:System.IO.Pipelines.PipeWriter> un qui peut être utilisé pour écrire le corps de la réponse. `HttpRequest.BodyReader`est un analogue du `HttpRequest.Body` flux. `HttpResponse.BodyWriter`est un analogue du `HttpResponse.Body` flux.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Amélioration des rapports d’erreurs dans IIS

Les erreurs de démarrage lors de l’hébergement d’applications ASP.NET Core dans IIS génèrent désormais des données de diagnostic plus riches. Ces erreurs sont signalées dans le journal des événements Windows avec des traces de pile, le cas échéant. En outre, tous les avertissements, erreurs et exceptions non gérées sont consignés dans le journal des événements Windows.

## <a name="worker-service-and-worker-sdk"></a>Service Worker et SDK Worker

.NET Core 3,0 introduit le nouveau modèle d’application de service Worker. Ce modèle fournit un point de départ pour l’écriture de services à long terme dans .NET Core.

Pour plus d'informations, voir :

* [Les Workers .NET Core en tant que services Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Améliorations des intergiciels-en-têtes transférés

Dans les versions précédentes de ASP.net Core, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> l' <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> appel de et était problématique lors du déploiement sur un serveur Linux Azure ou derrière un proxy inverse autre qu’IIS. Le correctif pour les versions précédentes est documenté dans [transférer le schéma pour les proxys inversés Linux et non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Ce scénario est résolu dans ASP.NET Core 3,0. L’hôte active l' [intergiciel d’en-tête transféré](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) lorsque `ASPNETCORE_FORWARDEDHEADERS_ENABLED` la variable d' `true`environnement a la valeur. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`a la valeur `true` dans nos images de conteneur.

## <a name="performance-improvements"></a>Amélioration des performances

ASP.NET Core 3,0 comprend de nombreuses améliorations qui réduisent l’utilisation de la mémoire et améliorent le débit :

* Réduction de l’utilisation de la mémoire lors de l’utilisation du conteneur d’injection de dépendances intégré pour les services délimités.
* Réduction des allocations dans l’infrastructure, y compris les scénarios de middleware et le routage.
* Réduction de l’utilisation de la mémoire pour les connexions WebSocket.
* Amélioration de la réduction de la mémoire et du débit pour les connexions HTTPs.
* Nouveau sérialiseur JSON optimisé et entièrement asynchrone.
* Réduction de l’utilisation de la mémoire et des améliorations du débit dans l’analyse de formulaire.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3,0 s’exécute uniquement sur .NET Core 3,0

À partir de ASP.NET Core 3,0, .NET Framework n’est plus une version cible de .NET Framework prise en charge. Les projets ciblant .NET Framework peuvent continuer de façon entièrement prise en charge à l’aide de la [version .net Core 2,1 LTS](https://www.microsoft.com/net/download/dotnet-core/2.1). La plupart des packages associés à ASP.NET Core 2.1. x seront pris en charge indéfiniment au-delà de la période de 3 ans LTS pour .NET Core 2,1.

Pour plus d’informations sur la migration, consultez [porter votre code d' .NET Framework vers .net Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Utiliser le ASP.NET Core Framework partagé

L’infrastructure partagée ASP.net Core 3,0, contenue dans le [AspNetCore de Microsoft. app](xref:fundamentals/metapackage-app), ne nécessite plus un `<PackageReference />` élément explicite dans le fichier projet. Le Framework partagé est automatiquement référencé lors de l’utilisation `Microsoft.NET.Sdk.Web` du kit de développement logiciel (SDK) dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assemblys supprimés de la ASP.NET Core Framework partagé

Les assemblys les plus notables supprimés de l’infrastructure partagée ASP.NET Core 3,0 sont les suivants :

* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net). Pour ajouter Json.NET à ASP.NET Core 3,0, consultez [Ajouter la prise en charge du format JSON basé sur Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.net Core 3,0 introduit `System.Text.Json` pour la lecture et l’écriture de JSON. Pour plus d’informations, consultez [nouvelle SÉRIALISATION JSON](#new-json-serialization) dans ce document.
* [Entity Framework Core](/ef/core/)

Pour obtenir la liste complète des assemblys supprimés de l’infrastructure partagée, consultez [assemblys en cours de suppression de Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755). Pour plus d’informations sur la motivation de cette modification, consultez [modifications critiques apportées à Microsoft. AspNetCore. app dans 3,0](https://github.com/aspnet/Announcements/issues/325) et [un premier aperçu des modifications apportées à ASP.net Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
