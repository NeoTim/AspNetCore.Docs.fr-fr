---
title: Authentification et autorisation dans gRPC pour ASP.NET Core
author: jamesnk
description: Découvrez comment utiliser l’authentification et l’autorisation dans gRPC pour ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 258b34113f3c3d9ef2031a43295ea5806b1e22ff
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880693"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="9a331-103">Authentification et autorisation dans gRPC pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a331-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="9a331-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9a331-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9a331-105">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9a331-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="9a331-106">Authentifier les utilisateurs appelant un service gRPC</span><span class="sxs-lookup"><span data-stu-id="9a331-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="9a331-107">gRPC peut être utilisé avec [l’authentification ASP.net Core](xref:security/authentication/identity) pour associer un utilisateur à chaque appel.</span><span class="sxs-lookup"><span data-stu-id="9a331-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="9a331-108">Voici un exemple de `Startup.Configure` qui utilise l’authentification gRPC et ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="9a331-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="9a331-109">L’ordre dans lequel vous inscrivez l’intergiciel (middleware) d’authentification ASP.NET Core est important.</span><span class="sxs-lookup"><span data-stu-id="9a331-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="9a331-110">Appelez toujours `UseAuthentication` et `UseAuthorization` après `UseRouting` et avant `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="9a331-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="9a331-111">Le mécanisme d’authentification utilisé par votre application pendant un appel doit être configuré.</span><span class="sxs-lookup"><span data-stu-id="9a331-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="9a331-112">La configuration de l’authentification est ajoutée à `Startup.ConfigureServices` et sera différente selon le mécanisme d’authentification utilisé par votre application.</span><span class="sxs-lookup"><span data-stu-id="9a331-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="9a331-113">Pour obtenir des exemples d’utilisation de la sécurisation des applications ASP.NET Core, consultez [exemples d’authentification](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="9a331-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="9a331-114">Une fois l’authentification configurée, l’utilisateur est accessible dans une méthode de service gRPC via l' `ServerCallContext`.</span><span class="sxs-lookup"><span data-stu-id="9a331-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="9a331-115">Authentification du jeton du porteur</span><span class="sxs-lookup"><span data-stu-id="9a331-115">Bearer token authentication</span></span>

<span data-ttu-id="9a331-116">Le client peut fournir un jeton d’accès pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="9a331-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="9a331-117">Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9a331-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="9a331-118">Sur le serveur, l’authentification du jeton du porteur est configurée à l’aide de l'[intergiciel (middleware) du porteur JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="9a331-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="9a331-119">Dans le client .NET gRPC, le jeton peut être envoyé avec des appels comme un en-tête :</span><span class="sxs-lookup"><span data-stu-id="9a331-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="9a331-120">La configuration d' `ChannelCredentials` sur un canal est une autre façon d’envoyer le jeton au service avec des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="9a331-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="9a331-121">Les informations d’identification sont exécutées chaque fois qu’un appel gRPC est effectué, ce qui évite d’avoir à écrire du code à plusieurs endroits pour passer le jeton vous-même.</span><span class="sxs-lookup"><span data-stu-id="9a331-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="9a331-122">Les informations d’identification dans l’exemple suivant configure le canal pour envoyer le jeton avec chaque appel gRPC :</span><span class="sxs-lookup"><span data-stu-id="9a331-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="9a331-123">Authentification du certificat du client</span><span class="sxs-lookup"><span data-stu-id="9a331-123">Client certificate authentication</span></span>

<span data-ttu-id="9a331-124">Un client peut également fournir un certificat client pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="9a331-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="9a331-125">L' [authentification par certificat](https://tools.ietf.org/html/rfc5246#section-7.4.4) se produit au niveau du TLS, à long terme avant qu’il ne soit ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="9a331-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="9a331-126">Lorsque la demande entre ASP.NET Core, le [package d’authentification du certificat client](xref:security/authentication/certauth) vous permet de résoudre le certificat en `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="9a331-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="9a331-127">L’hôte doit être configuré pour accepter les certificats clients.</span><span class="sxs-lookup"><span data-stu-id="9a331-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="9a331-128">Pour plus d’informations sur l’acceptation des certificats clients dans Kestrel, IIS et Azure, consultez [configurer votre hôte pour exiger des certificats](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .</span><span class="sxs-lookup"><span data-stu-id="9a331-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="9a331-129">Dans le client .NET gRPC, le certificat client est ajouté à `HttpClientHandler` qui est ensuite utilisé pour créer le client gRPC :</span><span class="sxs-lookup"><span data-stu-id="9a331-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="9a331-130">Autres mécanismes d’authentification</span><span class="sxs-lookup"><span data-stu-id="9a331-130">Other authentication mechanisms</span></span>

<span data-ttu-id="9a331-131">Un grand nombre ASP.NET Core mécanismes d’authentification pris en charge fonctionnent avec gRPC :</span><span class="sxs-lookup"><span data-stu-id="9a331-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="9a331-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="9a331-132">Azure Active Directory</span></span>
* <span data-ttu-id="9a331-133">Certificat client</span><span class="sxs-lookup"><span data-stu-id="9a331-133">Client Certificate</span></span>
* <span data-ttu-id="9a331-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="9a331-134">IdentityServer</span></span>
* <span data-ttu-id="9a331-135">Jeton JWT</span><span class="sxs-lookup"><span data-stu-id="9a331-135">JWT Token</span></span>
* <span data-ttu-id="9a331-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="9a331-136">OAuth 2.0</span></span>
* <span data-ttu-id="9a331-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="9a331-137">OpenID Connect</span></span>
* <span data-ttu-id="9a331-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="9a331-138">WS-Federation</span></span>

<span data-ttu-id="9a331-139">Pour plus d’informations sur la configuration de l’authentification sur le serveur, consultez [ASP.net Core l’authentification](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="9a331-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="9a331-140">La configuration du client gRPC pour utiliser l’authentification dépend du mécanisme d’authentification que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="9a331-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="9a331-141">Les exemples précédents du jeton de porteur et du certificat client illustrent deux façons de configurer le client gRPC pour envoyer des métadonnées d’authentification avec des appels gRPC :</span><span class="sxs-lookup"><span data-stu-id="9a331-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="9a331-142">Les clients gRPC fortement typés utilisent `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="9a331-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="9a331-143">L’authentification peut être configurée sur [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou en ajoutant des instances [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personnalisées au `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="9a331-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="9a331-144">Chaque appel gRPC a un argument `CallOptions` facultatif.</span><span class="sxs-lookup"><span data-stu-id="9a331-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="9a331-145">Les en-têtes personnalisés peuvent être envoyés à l’aide de la collection d’en-têtes de l’option.</span><span class="sxs-lookup"><span data-stu-id="9a331-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="9a331-146">L’authentification Windows (NTLM/Kerberos/Negotiate) ne peut pas être utilisée avec gRPC.</span><span class="sxs-lookup"><span data-stu-id="9a331-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="9a331-147">gRPC requiert HTTP/2 et HTTP/2 ne prend pas en charge l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="9a331-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="9a331-148">Autoriser les utilisateurs à accéder aux services et aux méthodes de service</span><span class="sxs-lookup"><span data-stu-id="9a331-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="9a331-149">Par défaut, toutes les méthodes d’un service peuvent être appelées par des utilisateurs non authentifiés.</span><span class="sxs-lookup"><span data-stu-id="9a331-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="9a331-150">Pour exiger une authentification, appliquez l’attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) au service :</span><span class="sxs-lookup"><span data-stu-id="9a331-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="9a331-151">Vous pouvez utiliser les arguments de constructeur et les propriétés de l’attribut `[Authorize]` pour limiter l’accès uniquement aux utilisateurs correspondant à des [stratégies d’autorisation](xref:security/authorization/policies)spécifiques.</span><span class="sxs-lookup"><span data-stu-id="9a331-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="9a331-152">Par exemple, si vous avez une stratégie d’autorisation personnalisée appelée `MyAuthorizationPolicy`, assurez-vous que seuls les utilisateurs qui correspondent à cette stratégie peuvent accéder au service à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="9a331-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="9a331-153">L’attribut `[Authorize]` peut également être appliqué aux méthodes de service individuelles.</span><span class="sxs-lookup"><span data-stu-id="9a331-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="9a331-154">Si l’utilisateur actuel ne correspond pas aux stratégies appliquées à la **fois** à la méthode et à la classe, une erreur est retournée à l’appelant :</span><span class="sxs-lookup"><span data-stu-id="9a331-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="9a331-155">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9a331-155">Additional resources</span></span>

* [<span data-ttu-id="9a331-156">Authentification du jeton du porteur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a331-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="9a331-157">Configurer l’authentification par certificat client dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a331-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
