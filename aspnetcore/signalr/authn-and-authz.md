---
title: Authentification et autorisation dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment utiliser l’authentification et l’autorisation dans ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 5a1e15ef46a3f89af3fbd3d505e7bd340c46e672
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963826"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="5720f-103">Authentification et autorisation dans ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="5720f-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5720f-104">Par [Andrew Stanton-infirmière](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5720f-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5720f-105">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5720f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a><span data-ttu-id="5720f-106">Authentifier les utilisateurs se connectant à un hub SignalR</span><span class="sxs-lookup"><span data-stu-id="5720f-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="5720f-107"> peut être utilisé avec [l’authentification ASP.net Core](xref:security/authentication/identity) pour associer un utilisateur à chaque connexion.</span><span class="sxs-lookup"><span data-stu-id="5720f-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="5720f-108">Dans un concentrateur, les données d’authentification sont accessibles à partir de la propriété [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="5720f-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="5720f-109">L’authentification permet au hub d’appeler des méthodes sur toutes les connexions associées à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="5720f-110">Pour plus d’informations, consultez [gérer les utilisateurs et les groupes dans SignalR](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="5720f-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="5720f-111">Plusieurs connexions peuvent être associées à un seul utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="5720f-112">Voici un exemple de `Startup.Configure` qui utilise l’authentification SignalR et ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="5720f-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="5720f-113">L’ordre dans lequel vous enregistrez le SignalR et ASP.NET Core l’intergiciel (middleware) d’authentification est important.</span><span class="sxs-lookup"><span data-stu-id="5720f-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5720f-114">Appelez toujours `UseAuthentication` avant `UseSignalR` afin que SignalR ait un utilisateur sur le `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5720f-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="5720f-115">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="5720f-115">Cookie authentication</span></span>

<span data-ttu-id="5720f-116">Dans une application basée sur un navigateur, l’authentification par cookie permet aux informations d’identification de l’utilisateur existant de circuler automatiquement vers SignalR connexions.</span><span class="sxs-lookup"><span data-stu-id="5720f-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="5720f-117">Lorsque vous utilisez le navigateur client, aucune configuration supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="5720f-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="5720f-118">Si l’utilisateur est connecté à votre application, la connexion SignalR hérite automatiquement de cette authentification.</span><span class="sxs-lookup"><span data-stu-id="5720f-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="5720f-119">Les cookies sont un moyen spécifique au navigateur d’envoyer des jetons d’accès, mais les clients sans navigateur peuvent les envoyer.</span><span class="sxs-lookup"><span data-stu-id="5720f-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="5720f-120">Lorsque vous utilisez le [client .net](xref:signalr/dotnet-client), la propriété `Cookies` peut être configurée dans l’appel `.WithUrl` pour fournir un cookie.</span><span class="sxs-lookup"><span data-stu-id="5720f-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="5720f-121">Toutefois, l’utilisation de l’authentification par cookie à partir du client .NET requiert que l’application fournisse une API pour échanger les données d’authentification d’un cookie.</span><span class="sxs-lookup"><span data-stu-id="5720f-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="5720f-122">Authentification du jeton du porteur</span><span class="sxs-lookup"><span data-stu-id="5720f-122">Bearer token authentication</span></span>

<span data-ttu-id="5720f-123">Le client peut fournir un jeton d’accès au lieu d’utiliser un cookie.</span><span class="sxs-lookup"><span data-stu-id="5720f-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="5720f-124">Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="5720f-125">Cette validation est effectuée uniquement lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="5720f-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="5720f-126">Pendant la durée de vie de la connexion, le serveur n’est pas automatiquement revalidé pour vérifier la révocation des jetons.</span><span class="sxs-lookup"><span data-stu-id="5720f-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="5720f-127">Sur le serveur, l’authentification du jeton du porteur est configurée à l’aide de l’intergiciel (middleware) du [porteur JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="5720f-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5720f-128">Dans le client JavaScript, le jeton peut être fourni à l’aide de l’option [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="5720f-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="5720f-129">Dans le client .NET, il existe une propriété [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) similaire qui peut être utilisée pour configurer le jeton :</span><span class="sxs-lookup"><span data-stu-id="5720f-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="5720f-130">La fonction de jeton d’accès que vous fournissez est appelée avant **chaque** requête HTTP effectuée par SignalR.</span><span class="sxs-lookup"><span data-stu-id="5720f-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="5720f-131">Si vous devez renouveler le jeton pour maintenir la connexion active (car elle peut expirer pendant la connexion), faites-le à partir de cette fonction et retournez le jeton mis à jour.</span><span class="sxs-lookup"><span data-stu-id="5720f-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="5720f-132">Dans les API Web standard, les jetons du porteur sont envoyés dans un en-tête HTTP.</span><span class="sxs-lookup"><span data-stu-id="5720f-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="5720f-133">Toutefois, SignalR ne peut pas définir ces en-têtes dans les navigateurs lors de l’utilisation de certains transports.</span><span class="sxs-lookup"><span data-stu-id="5720f-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="5720f-134">Lorsque vous utilisez des WebSockets et des événements envoyés par le serveur, le jeton est transmis sous la forme d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="5720f-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="5720f-135">Pour prendre en charge cela sur le serveur, une configuration supplémentaire est requise :</span><span class="sxs-lookup"><span data-stu-id="5720f-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="5720f-136">La chaîne de requête est utilisée sur les navigateurs lors de la connexion à WebSockets et aux événements envoyés par le serveur en raison des limitations de l’API du navigateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="5720f-137">Lors de l’utilisation de HTTPs, les valeurs de chaîne de requête sont sécurisées par la connexion TLS.</span><span class="sxs-lookup"><span data-stu-id="5720f-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="5720f-138">Toutefois, de nombreux serveurs consignent des valeurs de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="5720f-138">However, many servers log query string values.</span></span> <span data-ttu-id="5720f-139">Pour plus d’informations, consultez Considérations sur la [sécurité dans ASP.NET Core SignalR](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="5720f-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="5720f-140"> utilise des en-têtes pour transmettre des jetons dans des environnements qui les prennent en charge (tels que les clients .NET et Java).</span><span class="sxs-lookup"><span data-stu-id="5720f-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="5720f-141">Cookies et jetons de porteur</span><span class="sxs-lookup"><span data-stu-id="5720f-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="5720f-142">Les cookies sont spécifiques aux navigateurs.</span><span class="sxs-lookup"><span data-stu-id="5720f-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="5720f-143">Leur envoi à partir d’autres types de clients augmente la complexité par rapport à l’envoi des jetons de porteur.</span><span class="sxs-lookup"><span data-stu-id="5720f-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="5720f-144">Par conséquent, l’authentification des cookies n’est pas recommandée, sauf si l’application doit uniquement authentifier les utilisateurs à partir du navigateur client.</span><span class="sxs-lookup"><span data-stu-id="5720f-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="5720f-145">L’authentification par jeton du porteur est l’approche recommandée lors de l’utilisation de clients autres que le navigateur client.</span><span class="sxs-lookup"><span data-stu-id="5720f-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="5720f-146">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="5720f-146">Windows authentication</span></span>

<span data-ttu-id="5720f-147">Si [l’authentification Windows](xref:security/authentication/windowsauth) est configurée dans votre application, SignalR pouvez utiliser cette identité pour sécuriser les hubs.</span><span class="sxs-lookup"><span data-stu-id="5720f-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="5720f-148">Toutefois, pour envoyer des messages à des utilisateurs individuels, vous devez ajouter un fournisseur d’ID d’utilisateur personnalisé.</span><span class="sxs-lookup"><span data-stu-id="5720f-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="5720f-149">Le système d’authentification Windows ne fournit pas la revendication « identificateur de nom ».</span><span class="sxs-lookup"><span data-stu-id="5720f-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="5720f-150"> utilise la revendication pour déterminer le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="5720f-151">Ajoutez une nouvelle classe qui implémente `IUserIdProvider` et récupérez l’une des revendications de l’utilisateur à utiliser comme identificateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="5720f-152">Par exemple, pour utiliser la revendication « Name » (nom d’utilisateur Windows sous la forme `[Domain]\[Username]`), créez la classe suivante :</span><span class="sxs-lookup"><span data-stu-id="5720f-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="5720f-153">Au lieu de `ClaimTypes.Name`, vous pouvez utiliser n’importe quelle valeur de la `User` (par exemple, l’identificateur SID Windows, etc.).</span><span class="sxs-lookup"><span data-stu-id="5720f-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="5720f-154">La valeur que vous choisissez doit être unique parmi tous les utilisateurs de votre système.</span><span class="sxs-lookup"><span data-stu-id="5720f-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5720f-155">Dans le cas contraire, un message destiné à un utilisateur peut finir par passer à un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="5720f-156">Inscrivez ce composant dans votre méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5720f-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="5720f-157">Dans le client .NET, l’authentification Windows doit être activée en définissant la propriété [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="5720f-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="5720f-158">L’authentification Windows est prise en charge uniquement par le client navigateur lorsque vous utilisez Microsoft Internet Explorer ou Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="5720f-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="5720f-159">Utiliser des revendications pour personnaliser la gestion des identités</span><span class="sxs-lookup"><span data-stu-id="5720f-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="5720f-160">Une application qui authentifie les utilisateurs peut dériver SignalR ID utilisateur des revendications d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="5720f-161">Pour spécifier comment SignalR crée des ID d’utilisateur, implémentez `IUserIdProvider` et inscrivez l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="5720f-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="5720f-162">L’exemple de code montre comment utiliser les revendications pour sélectionner l’adresse e-mail de l’utilisateur comme propriété d’identification.</span><span class="sxs-lookup"><span data-stu-id="5720f-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="5720f-163">La valeur que vous choisissez doit être unique parmi tous les utilisateurs de votre système.</span><span class="sxs-lookup"><span data-stu-id="5720f-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5720f-164">Dans le cas contraire, un message destiné à un utilisateur peut finir par passer à un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="5720f-165">L’inscription du compte ajoute une revendication de type `ClaimsTypes.Email` à la base de données d’identité ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5720f-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="5720f-166">Inscrivez ce composant dans votre `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5720f-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="5720f-167">Autoriser les utilisateurs à accéder aux hubs et aux méthodes de concentrateur</span><span class="sxs-lookup"><span data-stu-id="5720f-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="5720f-168">Par défaut, toutes les méthodes d’un concentrateur peuvent être appelées par un utilisateur non authentifié.</span><span class="sxs-lookup"><span data-stu-id="5720f-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="5720f-169">Pour exiger une authentification, appliquez l’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) au hub :</span><span class="sxs-lookup"><span data-stu-id="5720f-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="5720f-170">Vous pouvez utiliser les arguments de constructeur et les propriétés de l’attribut `[Authorize]` pour limiter l’accès uniquement aux utilisateurs correspondant à des [stratégies d’autorisation](xref:security/authorization/policies)spécifiques.</span><span class="sxs-lookup"><span data-stu-id="5720f-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5720f-171">Par exemple, si vous avez une stratégie d’autorisation personnalisée appelée `MyAuthorizationPolicy` vous pouvez vous assurer que seuls les utilisateurs qui correspondent à cette stratégie peuvent accéder au hub à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="5720f-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="5720f-172">L’attribut `[Authorize]` peut également être appliqué à chaque méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5720f-173">Si l’utilisateur actuel ne correspond pas à la stratégie appliquée à la méthode, une erreur est retournée à l’appelant :</span><span class="sxs-lookup"><span data-stu-id="5720f-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="5720f-174">Utiliser des gestionnaires d’autorisations pour personnaliser l’autorisation de méthode de concentrateur</span><span class="sxs-lookup"><span data-stu-id="5720f-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="5720f-175"> fournit une ressource personnalisée aux gestionnaires d’autorisations lorsqu’une méthode de concentrateur requiert une autorisation.</span><span class="sxs-lookup"><span data-stu-id="5720f-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="5720f-176">La ressource est une instance de `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="5720f-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="5720f-177">Le `HubInvocationContext` comprend le `HubCallerContext`, le nom de la méthode de concentrateur appelée et les arguments de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="5720f-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="5720f-178">Prenons l’exemple d’une salle de conversation permettant à plusieurs entreprises de se connecter via Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5720f-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="5720f-179">Toute personne disposant d’un compte Microsoft peut se connecter à chat, mais seuls les membres de l’organisation propriétaire doivent être en mesure d’interdire les utilisateurs ou d’afficher les historiques de conversation des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="5720f-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="5720f-180">En outre, nous pouvons souhaiter restreindre certaines fonctionnalités de certains utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="5720f-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="5720f-181">L’utilisation des fonctionnalités mises à jour dans ASP.NET Core 3,0 est tout à fait possible.</span><span class="sxs-lookup"><span data-stu-id="5720f-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="5720f-182">Notez comment le `DomainRestrictedRequirement` sert de `IAuthorizationRequirement` personnalisé.</span><span class="sxs-lookup"><span data-stu-id="5720f-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="5720f-183">Maintenant que le paramètre de ressource `HubInvocationContext` est passé, la logique interne peut inspecter le contexte dans lequel le concentrateur est appelé et prendre des décisions pour permettre à l’utilisateur d’exécuter des méthodes de concentrateur individuelles.</span><span class="sxs-lookup"><span data-stu-id="5720f-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="5720f-184">Dans `Startup.ConfigureServices`, ajoutez la nouvelle stratégie, en fournissant la spécification d' `DomainRestrictedRequirement` personnalisée en tant que paramètre pour créer la stratégie de `DomainRestricted`.</span><span class="sxs-lookup"><span data-stu-id="5720f-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="5720f-185">Dans l’exemple précédent, la classe `DomainRestrictedRequirement` est à la fois une `IAuthorizationRequirement` et sa propre `AuthorizationHandler` pour cette spécification.</span><span class="sxs-lookup"><span data-stu-id="5720f-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="5720f-186">Il est acceptable de répartir ces deux composants dans des classes distinctes pour séparer les préoccupations.</span><span class="sxs-lookup"><span data-stu-id="5720f-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="5720f-187">L’une des avantages de l’approche de l’exemple est qu’il n’est pas nécessaire d’injecter les `AuthorizationHandler` au démarrage, car les exigences et le gestionnaire sont identiques.</span><span class="sxs-lookup"><span data-stu-id="5720f-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5720f-188">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5720f-188">Additional resources</span></span>

* [<span data-ttu-id="5720f-189">Authentification du jeton du porteur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5720f-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5720f-190">Autorisation basée sur les ressources</span><span class="sxs-lookup"><span data-stu-id="5720f-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
