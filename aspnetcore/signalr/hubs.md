---
title: Utiliser des hubs dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment utiliser des hubs dans ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963802"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="e164c-103">Utiliser des hubs dans SignalR pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e164c-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="e164c-104">Par [Rachel appel](https://twitter.com/rachelappel) et [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="e164c-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="e164c-105">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e164c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-opno-locsignalr-hub"></a><span data-ttu-id="e164c-106">Qu’est-ce qu’un hub SignalR</span><span class="sxs-lookup"><span data-stu-id="e164c-106">What is a SignalR hub</span></span>

<span data-ttu-id="e164c-107">L’API SignalR hubs vous permet d’appeler des méthodes sur des clients connectés à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="e164c-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="e164c-108">Dans le code serveur, vous définissez des méthodes qui sont appelées par le client.</span><span class="sxs-lookup"><span data-stu-id="e164c-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="e164c-109">Dans le code client, vous définissez des méthodes qui sont appelées à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="e164c-109">In the client code, you define methods that are called from the server.</span></span> SignalR<span data-ttu-id="e164c-110"> s’occupe de tout en arrière-plan qui rend possible les communications de client à serveur et de serveur à client en temps réel.</span><span class="sxs-lookup"><span data-stu-id="e164c-110"> takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-opno-locsignalr-hubs"></a><span data-ttu-id="e164c-111">Configurer SignalR hubs</span><span class="sxs-lookup"><span data-stu-id="e164c-111">Configure SignalR hubs</span></span>

<span data-ttu-id="e164c-112">L’intergiciel SignalR nécessite des services qui sont configurés en appelant `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="e164c-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e164c-113">Quand vous ajoutez des fonctionnalités de SignalR à une application ASP.NET Core, le programme d’installation SignalR des itinéraires en appelant `endpoint.MapHub` dans le rappel `Startup.Configure` de la méthode `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="e164c-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e164c-114">Lorsque vous ajoutez des fonctionnalités de SignalR à une application ASP.NET Core, le programme d’installation SignalR des itinéraires en appelant `app.UseSignalR` dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e164c-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="e164c-115">Créer et utiliser des hubs</span><span class="sxs-lookup"><span data-stu-id="e164c-115">Create and use hubs</span></span>

<span data-ttu-id="e164c-116">Créez un concentrateur en déclarant une classe qui hérite de `Hub`et ajoutez des méthodes publiques à celle-ci.</span><span class="sxs-lookup"><span data-stu-id="e164c-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="e164c-117">Les clients peuvent appeler des méthodes qui sont définies en tant que `public`.</span><span class="sxs-lookup"><span data-stu-id="e164c-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="e164c-118">Vous pouvez spécifier un type de retour et des paramètres, y compris des types complexes et des tableaux, comme C# vous le feriez dans n’importe quelle méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> SignalR<span data-ttu-id="e164c-119"> gère la sérialisation et la désérialisation d’objets et de tableaux complexes dans vos paramètres et valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="e164c-119"> handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="e164c-120">Les concentrateurs sont temporaires :</span><span class="sxs-lookup"><span data-stu-id="e164c-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="e164c-121">Ne stockez pas l’État dans une propriété de la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="e164c-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="e164c-122">Chaque appel de méthode de concentrateur est exécuté sur une nouvelle instance de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="e164c-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="e164c-123">Utilisez `await` lors de l’appel de méthodes asynchrones qui dépendent du concentrateur qui reste actif.</span><span class="sxs-lookup"><span data-stu-id="e164c-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="e164c-124">Par exemple, une méthode comme `Clients.All.SendAsync(...)` peut échouer si elle est appelée sans `await` et que la méthode de concentrateur se termine avant la fin de `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="e164c-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="e164c-125">Objet de contexte</span><span class="sxs-lookup"><span data-stu-id="e164c-125">The Context object</span></span>

<span data-ttu-id="e164c-126">La classe `Hub` a une propriété `Context` qui contient les propriétés suivantes avec des informations sur la connexion :</span><span class="sxs-lookup"><span data-stu-id="e164c-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="e164c-127">Property</span><span class="sxs-lookup"><span data-stu-id="e164c-127">Property</span></span> | <span data-ttu-id="e164c-128">Description</span><span class="sxs-lookup"><span data-stu-id="e164c-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="e164c-129">Obtient l’ID unique de la connexion, assignée par SignalR.</span><span class="sxs-lookup"><span data-stu-id="e164c-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="e164c-130">Il existe un ID de connexion pour chaque connexion.</span><span class="sxs-lookup"><span data-stu-id="e164c-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="e164c-131">Obtient l' [identificateur](xref:signalr/groups)de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e164c-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="e164c-132">Par défaut, SignalR utilise le `ClaimTypes.NameIdentifier` à partir de la `ClaimsPrincipal` associée à la connexion en tant qu’identificateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e164c-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="e164c-133">Obtient le `ClaimsPrincipal` associé à l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="e164c-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="e164c-134">Obtient une collection clé/valeur qui peut être utilisée pour partager des données dans l’étendue de cette connexion.</span><span class="sxs-lookup"><span data-stu-id="e164c-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="e164c-135">Les données peuvent être stockées dans cette collection et conservées pour la connexion entre différents appels de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="e164c-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="e164c-136">Obtient la collection de fonctionnalités disponibles sur la connexion.</span><span class="sxs-lookup"><span data-stu-id="e164c-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="e164c-137">Pour le moment, cette collection n’est pas nécessaire dans la plupart des scénarios. elle n’est donc pas encore documentée en détail.</span><span class="sxs-lookup"><span data-stu-id="e164c-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="e164c-138">Obtient un `CancellationToken` qui notifie lorsque la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="e164c-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="e164c-139">`Hub.Context` contient également les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e164c-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="e164c-140">Méthode</span><span class="sxs-lookup"><span data-stu-id="e164c-140">Method</span></span> | <span data-ttu-id="e164c-141">Description</span><span class="sxs-lookup"><span data-stu-id="e164c-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="e164c-142">Retourne le `HttpContext` pour la connexion, ou `null` si la connexion n’est pas associée à une requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="e164c-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="e164c-143">Pour les connexions HTTP, vous pouvez utiliser cette méthode pour obtenir des informations telles que les en-têtes HTTP et les chaînes de requête.</span><span class="sxs-lookup"><span data-stu-id="e164c-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="e164c-144">Abandonne la connexion.</span><span class="sxs-lookup"><span data-stu-id="e164c-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="e164c-145">Objet clients</span><span class="sxs-lookup"><span data-stu-id="e164c-145">The Clients object</span></span>

<span data-ttu-id="e164c-146">La classe `Hub` a une propriété `Clients` qui contient les propriétés suivantes pour la communication entre le serveur et le client :</span><span class="sxs-lookup"><span data-stu-id="e164c-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="e164c-147">Property</span><span class="sxs-lookup"><span data-stu-id="e164c-147">Property</span></span> | <span data-ttu-id="e164c-148">Description</span><span class="sxs-lookup"><span data-stu-id="e164c-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="e164c-149">Appelle une méthode sur tous les clients connectés</span><span class="sxs-lookup"><span data-stu-id="e164c-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="e164c-150">Appelle une méthode sur le client qui a appelé la méthode de concentrateur</span><span class="sxs-lookup"><span data-stu-id="e164c-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="e164c-151">Appelle une méthode sur tous les clients connectés, à l’exception du client qui a appelé la méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="e164c-152">`Hub.Clients` contient également les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e164c-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="e164c-153">Méthode</span><span class="sxs-lookup"><span data-stu-id="e164c-153">Method</span></span> | <span data-ttu-id="e164c-154">Description</span><span class="sxs-lookup"><span data-stu-id="e164c-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="e164c-155">Appelle une méthode sur tous les clients connectés, à l’exception des connexions spécifiées</span><span class="sxs-lookup"><span data-stu-id="e164c-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="e164c-156">Appelle une méthode sur un client connecté spécifique</span><span class="sxs-lookup"><span data-stu-id="e164c-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="e164c-157">Appelle une méthode sur des clients connectés spécifiques</span><span class="sxs-lookup"><span data-stu-id="e164c-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="e164c-158">Appelle une méthode sur toutes les connexions dans le groupe spécifié</span><span class="sxs-lookup"><span data-stu-id="e164c-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="e164c-159">Appelle une méthode sur toutes les connexions dans le groupe spécifié, à l’exception des connexions spécifiées.</span><span class="sxs-lookup"><span data-stu-id="e164c-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="e164c-160">Appelle une méthode sur plusieurs groupes de connexions</span><span class="sxs-lookup"><span data-stu-id="e164c-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="e164c-161">Appelle une méthode sur un groupe de connexions, à l’exclusion du client qui a appelé la méthode de concentrateur</span><span class="sxs-lookup"><span data-stu-id="e164c-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="e164c-162">Appelle une méthode sur toutes les connexions associées à un utilisateur spécifique</span><span class="sxs-lookup"><span data-stu-id="e164c-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="e164c-163">Appelle une méthode sur toutes les connexions associées aux utilisateurs spécifiés</span><span class="sxs-lookup"><span data-stu-id="e164c-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="e164c-164">Chaque propriété ou méthode dans les tables précédentes retourne un objet avec une méthode `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="e164c-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="e164c-165">La méthode `SendAsync` vous permet de fournir le nom et les paramètres de la méthode cliente à appeler.</span><span class="sxs-lookup"><span data-stu-id="e164c-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="e164c-166">Envoyer des messages aux clients</span><span class="sxs-lookup"><span data-stu-id="e164c-166">Send messages to clients</span></span>

<span data-ttu-id="e164c-167">Pour effectuer des appels à des clients spécifiques, utilisez les propriétés de l’objet `Clients`.</span><span class="sxs-lookup"><span data-stu-id="e164c-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="e164c-168">Dans l’exemple suivant, il existe trois méthodes de concentrateur :</span><span class="sxs-lookup"><span data-stu-id="e164c-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="e164c-169">`SendMessage` envoie un message à tous les clients connectés à l’aide de `Clients.All`.</span><span class="sxs-lookup"><span data-stu-id="e164c-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="e164c-170">`SendMessageToCaller` renvoie un message à l’appelant, à l’aide de `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="e164c-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="e164c-171">`SendMessageToGroups` envoie un message à tous les clients du groupe `SignalR Users`.</span><span class="sxs-lookup"><span data-stu-id="e164c-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="e164c-172">Hubs fortement typés</span><span class="sxs-lookup"><span data-stu-id="e164c-172">Strongly typed hubs</span></span>

<span data-ttu-id="e164c-173">L’inconvénient de l’utilisation de `SendAsync` est qu’elle s’appuie sur une chaîne magique pour spécifier la méthode cliente à appeler.</span><span class="sxs-lookup"><span data-stu-id="e164c-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="e164c-174">Cela laisse le code ouvert aux erreurs d’exécution si le nom de la méthode est mal orthographié ou manquant dans le client.</span><span class="sxs-lookup"><span data-stu-id="e164c-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="e164c-175">Une alternative à l’utilisation de `SendAsync` consiste à taper fortement le `Hub` avec <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span><span class="sxs-lookup"><span data-stu-id="e164c-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="e164c-176">Dans l’exemple suivant, les méthodes clientes `ChatHub` ont été extraites dans une interface appelée `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="e164c-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="e164c-177">Cette interface peut être utilisée pour refactoriser l’exemple de `ChatHub` précédent.</span><span class="sxs-lookup"><span data-stu-id="e164c-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="e164c-178">L’utilisation de `Hub<IChatClient>` permet la vérification au moment de la compilation des méthodes clientes.</span><span class="sxs-lookup"><span data-stu-id="e164c-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="e164c-179">Cela permet d’éviter les problèmes causés par l’utilisation de chaînes magiques, dans la mesure où `Hub<T>` ne peut fournir l’accès qu’aux méthodes définies dans l’interface.</span><span class="sxs-lookup"><span data-stu-id="e164c-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="e164c-180">L’utilisation d’un `Hub<T>` fortement typé désactive la possibilité d’utiliser des `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="e164c-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="e164c-181">Toutes les méthodes définies sur l’interface peuvent toujours être définies comme asynchrones.</span><span class="sxs-lookup"><span data-stu-id="e164c-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="e164c-182">En fait, chacune de ces méthodes doit retourner une `Task`.</span><span class="sxs-lookup"><span data-stu-id="e164c-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="e164c-183">Étant donné qu’il s’agit d’une interface, n’utilisez pas le mot clé `async`.</span><span class="sxs-lookup"><span data-stu-id="e164c-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="e164c-184">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e164c-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="e164c-185">Le suffixe `Async` n’est pas supprimé du nom de la méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="e164c-186">À moins que votre méthode client ne soit définie avec `.on('MyMethodAsync')`, vous ne devez pas utiliser `MyMethodAsync` comme nom.</span><span class="sxs-lookup"><span data-stu-id="e164c-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="e164c-187">Modifier le nom d’une méthode de concentrateur</span><span class="sxs-lookup"><span data-stu-id="e164c-187">Change the name of a hub method</span></span>

<span data-ttu-id="e164c-188">Par défaut, un nom de méthode de concentrateur de serveur est le nom de la méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="e164c-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="e164c-189">Toutefois, vous pouvez utiliser l’attribut [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) pour modifier cette valeur par défaut et spécifier manuellement un nom pour la méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="e164c-190">Le client doit utiliser ce nom au lieu du nom de la méthode .NET, lors de l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="e164c-191">Gérer les événements pour une connexion</span><span class="sxs-lookup"><span data-stu-id="e164c-191">Handle events for a connection</span></span>

<span data-ttu-id="e164c-192">L’API SignalR hubs fournit les méthodes virtuelles `OnConnectedAsync` et `OnDisconnectedAsync` pour gérer et suivre les connexions.</span><span class="sxs-lookup"><span data-stu-id="e164c-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="e164c-193">Substituez la méthode virtuelle `OnConnectedAsync` pour effectuer des actions lorsqu’un client se connecte au concentrateur, par exemple l’ajouter à un groupe.</span><span class="sxs-lookup"><span data-stu-id="e164c-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="e164c-194">Substituez la méthode virtuelle `OnDisconnectedAsync` pour effectuer des actions lorsqu’un client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="e164c-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="e164c-195">Si le client se déconnecte intentionnellement (en appelant `connection.stop()`, par exemple), le paramètre `exception` est `null`.</span><span class="sxs-lookup"><span data-stu-id="e164c-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="e164c-196">Toutefois, si le client est déconnecté en raison d’une erreur (par exemple, une défaillance du réseau), le paramètre `exception` contiendra une exception décrivant l’échec.</span><span class="sxs-lookup"><span data-stu-id="e164c-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="e164c-197">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="e164c-197">Handle errors</span></span>

<span data-ttu-id="e164c-198">Les exceptions levées dans vos méthodes de concentrateur sont envoyées au client qui a appelé la méthode.</span><span class="sxs-lookup"><span data-stu-id="e164c-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="e164c-199">Sur le client JavaScript, la méthode `invoke` retourne une [promesse JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="e164c-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="e164c-200">Lorsque le client reçoit une erreur avec un gestionnaire attaché à la promesse à l’aide de `catch`, il est appelé et transmis en tant qu’objet `Error` JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e164c-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="e164c-201">Si votre Hub lève une exception, les connexions ne sont pas fermées.</span><span class="sxs-lookup"><span data-stu-id="e164c-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="e164c-202">Par défaut, SignalR retourne un message d’erreur générique au client.</span><span class="sxs-lookup"><span data-stu-id="e164c-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="e164c-203">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e164c-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="e164c-204">Les exceptions inattendues contiennent souvent des informations sensibles, telles que le nom d’un serveur de base de données dans une exception déclenchée lorsque la connexion à la base de données échoue.</span><span class="sxs-lookup"><span data-stu-id="e164c-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> SignalR<span data-ttu-id="e164c-205"> n’expose pas ces messages d’erreur détaillés par défaut en tant que mesure de sécurité.</span><span class="sxs-lookup"><span data-stu-id="e164c-205"> doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="e164c-206">Pour plus d’informations sur la raison pour laquelle les détails de l’exception sont supprimés, consultez l' [article considérations](xref:signalr/security#exceptions) sur la sécurité.</span><span class="sxs-lookup"><span data-stu-id="e164c-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="e164c-207">Si vous avez une condition *exceptionnelle que vous souhaitez* propager au client, vous pouvez utiliser la classe `HubException`.</span><span class="sxs-lookup"><span data-stu-id="e164c-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="e164c-208">Si vous levez une `HubException` à partir de votre méthode de concentrateur **, SignalR enverra** l’intégralité du message au client, sans modification.</span><span class="sxs-lookup"><span data-stu-id="e164c-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR<span data-ttu-id="e164c-209"> envoie uniquement la propriété `Message` de l’exception au client.</span><span class="sxs-lookup"><span data-stu-id="e164c-209"> only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="e164c-210">La trace de la pile et les autres propriétés de l’exception ne sont pas disponibles pour le client.</span><span class="sxs-lookup"><span data-stu-id="e164c-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e164c-211">Ressources connexes</span><span class="sxs-lookup"><span data-stu-id="e164c-211">Related resources</span></span>

* <span data-ttu-id="e164c-212">[Présentation de ASP.NET Core SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="e164c-212">[Intro to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>
* [<span data-ttu-id="e164c-213">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="e164c-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="e164c-214">Publier sur Azure</span><span class="sxs-lookup"><span data-stu-id="e164c-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
