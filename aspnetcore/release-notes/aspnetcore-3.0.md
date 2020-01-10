---
title: Nouveautés de ASP.NET Core 3,0
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 1dee9a7e1cc381547e7ece71f302f407223dc838
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829112"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="accde-103">Nouveautés de ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="accde-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="accde-104">Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 3,0 avec des liens vers la documentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="accde-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="accde-105"> est une nouvelle infrastructure dans ASP.NET Core pour la création d’une interface utilisateur Web interactive côté client avec .NET :</span><span class="sxs-lookup"><span data-stu-id="accde-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="accde-106">Créez des interfaces utilisateur interactives riches à l’aide de C# plutôt que JavaScript.</span><span class="sxs-lookup"><span data-stu-id="accde-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="accde-107">Partagez la logique d’application côté serveur et côté client écrite dans .NET.</span><span class="sxs-lookup"><span data-stu-id="accde-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="accde-108">Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="accde-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="accde-109">scénarios pris en charge par Blazor Framework :</span><span class="sxs-lookup"><span data-stu-id="accde-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="accde-110">Composants de l’interface utilisateur réutilisables (composants Razor)</span><span class="sxs-lookup"><span data-stu-id="accde-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="accde-111">Routage côté client</span><span class="sxs-lookup"><span data-stu-id="accde-111">Client-side routing</span></span>
* <span data-ttu-id="accde-112">Dispositions des composants</span><span class="sxs-lookup"><span data-stu-id="accde-112">Component layouts</span></span>
* <span data-ttu-id="accde-113">Prise en charge de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="accde-113">Support for dependency injection</span></span>
* <span data-ttu-id="accde-114">Formulaires et validation</span><span class="sxs-lookup"><span data-stu-id="accde-114">Forms and validation</span></span>
* <span data-ttu-id="accde-115">Créer des bibliothèques de composants avec des bibliothèques de classes Razor</span><span class="sxs-lookup"><span data-stu-id="accde-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="accde-116">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="accde-116">JavaScript interop</span></span>

<span data-ttu-id="accde-117">Pour plus d'informations, consultez <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="accde-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a><span data-ttu-id="accde-118">Serveur de Blazor</span><span class="sxs-lookup"><span data-stu-id="accde-118">Blazor Server</span></span>

Blazor<span data-ttu-id="accde-119"> dissocie la logique de rendu des composants de l’application des mises à jour de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="accde-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="accde-120"> Server prend en charge l’hébergement de composants Razor sur le serveur dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="accde-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="accde-121">Les mises à jour de l’interface utilisateur sont gérées via une connexion SignalR.</span><span class="sxs-lookup"><span data-stu-id="accde-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="accde-122"> Server est pris en charge dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="accde-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="accde-123"> webassembly (version préliminaire)</span><span class="sxs-lookup"><span data-stu-id="accde-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="accde-124"> applications peuvent également être exécutées directement dans le navigateur à l’aide d’un Runtime .NET basé sur webassembly.</span><span class="sxs-lookup"><span data-stu-id="accde-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="accde-125"> webassembly est en version préliminaire et *n’est pas* pris en charge dans ASP.net Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="accde-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="accde-126"> webassembly sera pris en charge dans une prochaine version de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="accde-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="accde-127">Composants Razor</span><span class="sxs-lookup"><span data-stu-id="accde-127">Razor components</span></span>

<span data-ttu-id="accde-128">les applications Blazor sont générées à partir de composants.</span><span class="sxs-lookup"><span data-stu-id="accde-128">Blazor apps are built from components.</span></span> <span data-ttu-id="accde-129">Les composants sont des blocs autonomes de l’interface utilisateur (IU), tels qu’une page, une boîte de dialogue ou un formulaire.</span><span class="sxs-lookup"><span data-stu-id="accde-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="accde-130">Les composants sont des classes .NET normales qui définissent la logique de rendu de l’interface utilisateur et les gestionnaires d’événements côté client.</span><span class="sxs-lookup"><span data-stu-id="accde-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="accde-131">Vous pouvez créer des applications Web riches et interactives sans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="accde-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="accde-132">Les composants de Blazor sont généralement créés à l’aide d’syntaxe Razor, un mélange naturel C#de code HTML et.</span><span class="sxs-lookup"><span data-stu-id="accde-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="accde-133">Les composants Razor sont similaires aux vues Razor Pages et MVC en ce sens qu’ils utilisent tous deux Razor.</span><span class="sxs-lookup"><span data-stu-id="accde-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="accde-134">Contrairement aux pages et aux vues, qui sont basées sur un modèle de requête-réponse, les composants sont utilisés spécifiquement pour gérer la composition de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="accde-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="accde-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="accde-135">gRPC</span></span>

<span data-ttu-id="accde-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="accde-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="accde-137">Est un Framework RPC (Remote Procedure Call, appel de procédure distante) très performant et populaire.</span><span class="sxs-lookup"><span data-stu-id="accde-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="accde-138">Offre une approche consignes strictes Contract-First pour le développement d’API.</span><span class="sxs-lookup"><span data-stu-id="accde-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="accde-139">Utilise des technologies modernes telles que :</span><span class="sxs-lookup"><span data-stu-id="accde-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="accde-140">HTTP/2 pour le transport.</span><span class="sxs-lookup"><span data-stu-id="accde-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="accde-141">Mémoires tampons de protocole en tant que langage de description d’interface.</span><span class="sxs-lookup"><span data-stu-id="accde-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="accde-142">Format de sérialisation binaire.</span><span class="sxs-lookup"><span data-stu-id="accde-142">Binary serialization format.</span></span>
* <span data-ttu-id="accde-143">Fournit des fonctionnalités telles que :</span><span class="sxs-lookup"><span data-stu-id="accde-143">Provides features such as:</span></span>

  * <span data-ttu-id="accde-144">Authentification</span><span class="sxs-lookup"><span data-stu-id="accde-144">Authentication</span></span>
  * <span data-ttu-id="accde-145">Streaming bidirectionnel et contrôle de flux.</span><span class="sxs-lookup"><span data-stu-id="accde-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="accde-146">Annulation et délais d’attente.</span><span class="sxs-lookup"><span data-stu-id="accde-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="accde-147">la fonctionnalité gRPC dans ASP.NET Core 3,0 comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="accde-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="accde-148">[GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; un framework ASP.net Core pour l’hébergement des services GRPC.</span><span class="sxs-lookup"><span data-stu-id="accde-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="accde-149">gRPC sur ASP.NET Core s’intègre aux fonctionnalités de ASP.NET Core standard, telles que la journalisation, l’injection de dépendances, l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="accde-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="accde-150">[GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; un client GRPC pour .net core qui s’appuie sur le `HttpClient`familier.</span><span class="sxs-lookup"><span data-stu-id="accde-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="accde-151">[GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; l’intégration du client Grpc à `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="accde-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="accde-152">Pour plus d'informations, consultez <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="accde-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="accde-153">Pour obtenir des instructions de migration, consultez [mettre à jour le code SignalR](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="accde-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="accde-154"> utilise désormais `System.Text.Json` pour sérialiser/désérialiser les messages JSON.</span><span class="sxs-lookup"><span data-stu-id="accde-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="accde-155">Consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) pour obtenir des instructions sur la restauration du sérialiseur basé sur le `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="accde-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="accde-156">Dans les clients JavaScript et .NET pour SignalR, la prise en charge a été ajoutée pour la reconnexion automatique.</span><span class="sxs-lookup"><span data-stu-id="accde-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="accde-157">Par défaut, le client tente de se reconnecter immédiatement, puis de réessayer au bout de 2, 10 et 30 secondes si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="accde-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="accde-158">Si le client se reconnecte avec succès, il reçoit un nouvel ID de connexion.</span><span class="sxs-lookup"><span data-stu-id="accde-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="accde-159">La reconnexion automatique est opt-in :</span><span class="sxs-lookup"><span data-stu-id="accde-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="accde-160">Les intervalles de reconnexion peuvent être spécifiés en passant un tableau de durées basées sur la milliseconde :</span><span class="sxs-lookup"><span data-stu-id="accde-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="accde-161">Une implémentation personnalisée peut être passée pour le contrôle total des intervalles de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="accde-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="accde-162">Si la reconnexion échoue après le dernier intervalle de reconnexion :</span><span class="sxs-lookup"><span data-stu-id="accde-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="accde-163">Le client considère que la connexion est hors connexion.</span><span class="sxs-lookup"><span data-stu-id="accde-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="accde-164">Le client cesse de tenter de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="accde-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="accde-165">Lors des tentatives de reconnexion, mettez à jour l’interface utilisateur de l’application pour notifier l’utilisateur que la reconnexion est tentée.</span><span class="sxs-lookup"><span data-stu-id="accde-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="accde-166">Pour fournir des commentaires sur l’interface utilisateur lorsque la connexion est interrompue, l’API cliente SignalR a été développée pour inclure les gestionnaires d’événements suivants :</span><span class="sxs-lookup"><span data-stu-id="accde-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="accde-167">`onreconnecting`: donne aux développeurs la possibilité de désactiver l’interface utilisateur ou de laisser les utilisateurs savoir que l’application est hors connexion.</span><span class="sxs-lookup"><span data-stu-id="accde-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="accde-168">`onreconnected`: donne aux développeurs la possibilité de mettre à jour l’interface utilisateur une fois la connexion rétablie.</span><span class="sxs-lookup"><span data-stu-id="accde-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="accde-169">Le code suivant utilise `onreconnecting` pour mettre à jour l’interface utilisateur lors de la tentative de connexion :</span><span class="sxs-lookup"><span data-stu-id="accde-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="accde-170">Le code suivant utilise `onreconnected` pour mettre à jour l’interface utilisateur sur la connexion :</span><span class="sxs-lookup"><span data-stu-id="accde-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="accde-171"> 3,0 et versions ultérieures fournissent une ressource personnalisée aux gestionnaires d’autorisations lorsqu’une méthode de concentrateur requiert une autorisation.</span><span class="sxs-lookup"><span data-stu-id="accde-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="accde-172">La ressource est une instance de `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="accde-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="accde-173">Le `HubInvocationContext` comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="accde-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="accde-174">Nom de la méthode de concentrateur appelée.</span><span class="sxs-lookup"><span data-stu-id="accde-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="accde-175">Arguments de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="accde-175">Arguments to the hub method.</span></span>

<span data-ttu-id="accde-176">Prenons l’exemple suivant d’une application de salle de conversation permettant à plusieurs entreprises de se connecter via Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="accde-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="accde-177">Toute personne disposant d’un compte Microsoft peut se connecter à chat, mais seuls les membres de l’organisation propriétaire peuvent interdire les utilisateurs ou afficher les historiques de conversation des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="accde-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="accde-178">L’application peut restreindre certaines fonctionnalités d’utilisateurs spécifiques.</span><span class="sxs-lookup"><span data-stu-id="accde-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="accde-179">Dans le code précédent, `DomainRestrictedRequirement` sert de `IAuthorizationRequirement`personnalisé.</span><span class="sxs-lookup"><span data-stu-id="accde-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="accde-180">Étant donné que le paramètre de ressource `HubInvocationContext` est passé, la logique interne peut :</span><span class="sxs-lookup"><span data-stu-id="accde-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="accde-181">Inspectez le contexte dans lequel le Hub est appelé.</span><span class="sxs-lookup"><span data-stu-id="accde-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="accde-182">Prendre des décisions pour permettre à l’utilisateur d’exécuter des méthodes de concentrateur individuelles.</span><span class="sxs-lookup"><span data-stu-id="accde-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="accde-183">Les méthodes de concentrateur individuelles peuvent être marquées avec le nom de la stratégie vérifiée par le code au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="accde-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="accde-184">À mesure que les clients tentent d’appeler des méthodes de concentrateur individuelles, le gestionnaire de `DomainRestrictedRequirement` exécute et contrôle l’accès aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="accde-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="accde-185">En fonction de la façon dont le `DomainRestrictedRequirement` contrôle l’accès :</span><span class="sxs-lookup"><span data-stu-id="accde-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="accde-186">Tous les utilisateurs connectés peuvent appeler la méthode `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="accde-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="accde-187">Seuls les utilisateurs qui se sont connectés avec une adresse de messagerie `@jabbr.net` peuvent afficher les historiques des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="accde-187">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="accde-188">Seuls les `bob42@jabbr.net` peuvent interdire les utilisateurs de la salle de conversation.</span><span class="sxs-lookup"><span data-stu-id="accde-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="accde-189">La création de la stratégie de `DomainRestricted` peut impliquer :</span><span class="sxs-lookup"><span data-stu-id="accde-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="accde-190">Dans *Startup.cs*, ajout de la nouvelle stratégie.</span><span class="sxs-lookup"><span data-stu-id="accde-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="accde-191">Fournissez la spécification de `DomainRestrictedRequirement` personnalisée comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="accde-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="accde-192">Inscription `DomainRestricted` avec l’intergiciel (middleware) d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="accde-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="accde-193"> hubs utilisent le [routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="accde-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="accde-194"> connexion au Hub a été précédemment effectuée explicitement :</span><span class="sxs-lookup"><span data-stu-id="accde-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="accde-195">Dans la version précédente, les développeurs devaient relier les contrôleurs, les pages Razor et les hubs à différents emplacements.</span><span class="sxs-lookup"><span data-stu-id="accde-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="accde-196">La connexion explicite produit une série de segments de routage quasiment identiques :</span><span class="sxs-lookup"><span data-stu-id="accde-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

<span data-ttu-id="accde-197">les hubs SignalR 3,0 peuvent être routés via le routage des points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="accde-197">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="accde-198">Avec le routage de point de terminaison, tous les routages peuvent généralement être configurés dans `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="accde-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="accde-199">ASP.NET Core 3,0 SignalR ajouté :</span><span class="sxs-lookup"><span data-stu-id="accde-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="accde-200">Diffusion en continu entre le client et le serveur.</span><span class="sxs-lookup"><span data-stu-id="accde-200">Client-to-server streaming.</span></span> <span data-ttu-id="accde-201">Avec la diffusion en continu client à serveur, les méthodes côté serveur peuvent prendre des instances d’une `IAsyncEnumerable<T>` ou d’un `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="accde-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="accde-202">Dans l’exemple C# suivant, la méthode `UploadStream` sur le Hub recevra un flux de chaînes du client :</span><span class="sxs-lookup"><span data-stu-id="accde-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="accde-203">Les applications clientes .NET peuvent passer une instance `IAsyncEnumerable<T>` ou `ChannelReader<T>` comme argument `stream` de la méthode `UploadStream` Hub ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="accde-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="accde-204">Une fois la `for` boucle terminée et la fonction locale quittée, l’exécution du flux est envoyée :</span><span class="sxs-lookup"><span data-stu-id="accde-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="accde-205">Les applications clientes JavaScript utilisent l' SignalR `Subject` (ou un [objet RxJS](https://rxjs.dev/api/index/class/Subject)) pour l’argument `stream` de la méthode `UploadStream` Hub ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="accde-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="accde-206">Le code JavaScript peut utiliser la méthode `subject.next` pour gérer les chaînes à mesure qu’elles sont capturées et prêtes à être envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="accde-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="accde-207">À l’aide de code comme les deux extraits de code précédents, vous pouvez créer des expériences de diffusion en continu en temps réel.</span><span class="sxs-lookup"><span data-stu-id="accde-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="accde-208">Nouvelle sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="accde-208">New JSON serialization</span></span>

<span data-ttu-id="accde-209">ASP.NET Core 3,0 utilise désormais <xref:System.Text.Json> par défaut pour la sérialisation JSON :</span><span class="sxs-lookup"><span data-stu-id="accde-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="accde-210">Lit et écrit JSON de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="accde-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="accde-211">Est optimisé pour le texte UTF-8.</span><span class="sxs-lookup"><span data-stu-id="accde-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="accde-212">En général, des performances supérieures à celles de `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="accde-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="accde-213">Pour ajouter Json.NET à ASP.NET Core 3,0, consultez [Ajouter la prise en charge du format JSON basé sur Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="accde-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="accde-214">Nouvelles directives Razor</span><span class="sxs-lookup"><span data-stu-id="accde-214">New Razor directives</span></span>

<span data-ttu-id="accde-215">La liste suivante contient les nouvelles directives Razor :</span><span class="sxs-lookup"><span data-stu-id="accde-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="accde-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; la directive `@attribute` applique l’attribut donné à la classe de la page ou de la vue générée.</span><span class="sxs-lookup"><span data-stu-id="accde-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="accde-217">Par exemple, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="accde-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="accde-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; la directive `@implements` implémente une interface pour la classe générée.</span><span class="sxs-lookup"><span data-stu-id="accde-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="accde-219">Par exemple, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="accde-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="accde-220">IdentityServer4 prend en charge l’authentification et l’autorisation pour les API Web et SPAs</span><span class="sxs-lookup"><span data-stu-id="accde-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="accde-221">ASP.NET Core 3,0 offre une authentification dans les applications à page unique (SPAs) à l’aide de la prise en charge de l’autorisation de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="accde-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="accde-222">ASP.NET Core identité pour l’authentification et le stockage des utilisateurs est associée à [IdentityServer4](https://identityserver.io/) pour l’implémentation d’Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="accde-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="accde-223">IdentityServer4 est un Framework OpenID Connect et OAuth 2,0 pour ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="accde-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="accde-224">Il active les fonctionnalités de sécurité suivantes :</span><span class="sxs-lookup"><span data-stu-id="accde-224">It enables the following security features:</span></span>

* <span data-ttu-id="accde-225">Authentification en tant que service (AaaS)</span><span class="sxs-lookup"><span data-stu-id="accde-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="accde-226">Authentification unique (SSO) sur plusieurs types d’applications</span><span class="sxs-lookup"><span data-stu-id="accde-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="accde-227">Contrôle d’accès pour les API</span><span class="sxs-lookup"><span data-stu-id="accde-227">Access control for APIs</span></span>
* <span data-ttu-id="accde-228">Passerelle de Fédération</span><span class="sxs-lookup"><span data-stu-id="accde-228">Federation Gateway</span></span>

<span data-ttu-id="accde-229">Pour plus d’informations, consultez [la documentation de IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [l’authentification et l’autorisation pour](xref:security/authentication/identity/spa)la demande de demande.</span><span class="sxs-lookup"><span data-stu-id="accde-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="accde-230">Certificat et authentification Kerberos</span><span class="sxs-lookup"><span data-stu-id="accde-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="accde-231">L’authentification par certificat requiert :</span><span class="sxs-lookup"><span data-stu-id="accde-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="accde-232">Configuration du serveur pour accepter les certificats.</span><span class="sxs-lookup"><span data-stu-id="accde-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="accde-233">Ajout de l’intergiciel d’authentification dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="accde-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="accde-234">Ajout du service d’authentification par certificat dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="accde-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="accde-235">Les options d’authentification par certificat incluent la possibilité d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="accde-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="accde-236">Accepter des certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="accde-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="accde-237">Vérifiez la révocation des certificats.</span><span class="sxs-lookup"><span data-stu-id="accde-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="accde-238">Vérifiez que le certificat offerts contient les indicateurs d’utilisation appropriés.</span><span class="sxs-lookup"><span data-stu-id="accde-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="accde-239">Un principal d’utilisateur par défaut est construit à partir des propriétés du certificat.</span><span class="sxs-lookup"><span data-stu-id="accde-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="accde-240">Le principal d’utilisateur contient un événement qui permet d’ajouter ou de remplacer le principal.</span><span class="sxs-lookup"><span data-stu-id="accde-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="accde-241">Pour plus d'informations, consultez <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="accde-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="accde-242">[L’authentification Windows](/windows-server/security/windows-authentication/windows-authentication-overview) a été étendue sur Linux et MacOS.</span><span class="sxs-lookup"><span data-stu-id="accde-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="accde-243">Dans les versions précédentes, l’authentification Windows était limitée à [IIS](xref:host-and-deploy/iis/index) et [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="accde-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="accde-244">Dans ASP.NET Core 3,0, [Kestrel](xref:fundamentals/servers/kestrel) a la possibilité d’utiliser Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)et [NTLM sur Windows](/windows-server/security/kerberos/ntlm-overview), Linux et MacOS pour les hôtes joints à un domaine Windows.</span><span class="sxs-lookup"><span data-stu-id="accde-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="accde-245">La prise en charge Kestrel de ces schémas d’authentification est assurée par le package [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="accde-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="accde-246">Comme pour les autres services d’authentification, configurez l’authentification pour l’ensemble de l’application, puis configurez le service :</span><span class="sxs-lookup"><span data-stu-id="accde-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="accde-247">Configuration requise pour l’ordinateur hôte :</span><span class="sxs-lookup"><span data-stu-id="accde-247">Host requirements:</span></span>

* <span data-ttu-id="accde-248">Les hôtes Windows doivent avoir des [noms de principal du service](/windows/win32/ad/service-principal-names) (SPN) ajoutés au compte d’utilisateur qui héberge l’application.</span><span class="sxs-lookup"><span data-stu-id="accde-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="accde-249">Les ordinateurs Linux et macOS doivent être joints au domaine.</span><span class="sxs-lookup"><span data-stu-id="accde-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="accde-250">Les noms de principal du service doivent être créés pour le processus Web.</span><span class="sxs-lookup"><span data-stu-id="accde-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="accde-251">Les [fichiers keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) doivent être générés et configurés sur l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="accde-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="accde-252">Pour plus d'informations, consultez <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="accde-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="accde-253">Modifications du modèle</span><span class="sxs-lookup"><span data-stu-id="accde-253">Template changes</span></span>

<span data-ttu-id="accde-254">Les modèles d’interface utilisateur Web (Razor Pages, MVC avec contrôleur et vues) ont les éléments suivants supprimés :</span><span class="sxs-lookup"><span data-stu-id="accde-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="accde-255">L’interface utilisateur de consentement du cookie n’est plus incluse.</span><span class="sxs-lookup"><span data-stu-id="accde-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="accde-256">Pour activer la fonctionnalité de consentement de cookie dans une application ASP.NET Core générée par un modèle 3,0, consultez <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="accde-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="accde-257">Les scripts et les ressources statiques associées sont désormais référencés en tant que fichiers locaux au lieu d’utiliser CDN.</span><span class="sxs-lookup"><span data-stu-id="accde-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="accde-258">Pour plus d’informations, consultez [les scripts et les ressources statiques associées sont désormais référencés en tant que fichiers locaux au lieu d’utiliser CDN en fonction de l’environnement actuel (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="accde-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="accde-259">Modèle angulaire mis à jour pour utiliser le 8 angulaire.</span><span class="sxs-lookup"><span data-stu-id="accde-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="accde-260">Le modèle de bibliothèque de classes Razor (RCL) est par défaut le développement de composants Razor par défaut.</span><span class="sxs-lookup"><span data-stu-id="accde-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="accde-261">Une nouvelle option de modèle dans Visual Studio fournit la prise en charge des modèles pour les pages et les vues.</span><span class="sxs-lookup"><span data-stu-id="accde-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="accde-262">Lorsque vous créez un RCL à partir du modèle dans une interface de commande, transmettez l’option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="accde-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="accde-263">Hôte générique</span><span class="sxs-lookup"><span data-stu-id="accde-263">Generic Host</span></span>

<span data-ttu-id="accde-264">Les modèles ASP.NET Core 3,0 utilisent <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="accde-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="accde-265">Les versions précédentes utilisaient <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="accde-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="accde-266">L’utilisation de l’hôte générique .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) offre une meilleure intégration des applications ASP.NET Core avec d’autres scénarios de serveur qui ne sont pas spécifiques au Web.</span><span class="sxs-lookup"><span data-stu-id="accde-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="accde-267">Pour plus d’informations, consultez [HostBuilder remplace WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="accde-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="accde-268">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="accde-268">Host configuration</span></span>

<span data-ttu-id="accde-269">Avant la sortie de ASP.NET Core 3,0, les variables d’environnement précédées de `ASPNETCORE_` ont été chargées pour la configuration d’hôte de l’hôte Web.</span><span class="sxs-lookup"><span data-stu-id="accde-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="accde-270">Dans 3,0, `AddEnvironmentVariables` est utilisé pour charger les variables d’environnement précédées de `DOTNET_` pour la configuration de l’hôte avec `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="accde-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="accde-271">Modifications apportées à l’injection du constructeur de démarrage</span><span class="sxs-lookup"><span data-stu-id="accde-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="accde-272">L’hôte générique prend en charge uniquement les types suivants pour `Startup` l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="accde-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="accde-273">Tous les services peuvent toujours être injectés directement comme arguments de la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="accde-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="accde-274">Pour plus d’informations, consultez l' [hôte générique limite l’injection de constructeur de démarrage (ASPNET/announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="accde-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="accde-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="accde-275">Kestrel</span></span>

* <span data-ttu-id="accde-276">La configuration Kestrel a été mise à jour pour la migration vers l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="accde-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="accde-277">Dans 3,0, Kestrel est configuré sur le générateur d’hôte Web fourni par `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="accde-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="accde-278">Les adaptateurs de connexion ont été supprimés de Kestrel et remplacés par un intergiciel de connexion, qui est similaire à l’intergiciel HTTP dans le pipeline ASP.NET Core, mais pour les connexions de niveau inférieur.</span><span class="sxs-lookup"><span data-stu-id="accde-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="accde-279">La couche de transport Kestrel a été exposée en tant qu’interface publique dans `Connections.Abstractions`.</span><span class="sxs-lookup"><span data-stu-id="accde-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="accde-280">L’ambiguïté entre les en-têtes et les codes de fin a été résolue en déplaçant les en-têtes de fin vers une nouvelle collection.</span><span class="sxs-lookup"><span data-stu-id="accde-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="accde-281">Les API d’e/s synchrones, telles que `HttpRequest.Body.Read`, sont une source commune de privation de thread conduisant à des blocages d’application.</span><span class="sxs-lookup"><span data-stu-id="accde-281">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="accde-282">Dans 3,0, `AllowSynchronousIO` est désactivée par défaut.</span><span class="sxs-lookup"><span data-stu-id="accde-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="accde-283">Pour plus d'informations, consultez <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="accde-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="accde-284">HTTP/2 activé par défaut</span><span class="sxs-lookup"><span data-stu-id="accde-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="accde-285">HTTP/2 est activé par défaut dans Kestrel pour les points de terminaison HTTPs.</span><span class="sxs-lookup"><span data-stu-id="accde-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="accde-286">La prise en charge de HTTP/2 pour IIS ou HTTP. sys est activée lorsqu’elle est prise en charge par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="accde-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="accde-287">EventCounters à la demande</span><span class="sxs-lookup"><span data-stu-id="accde-287">EventCounters on request</span></span>

<span data-ttu-id="accde-288">L’EventSource d’hébergement, `Microsoft.AspNetCore.Hosting`, émet les nouveaux types de <xref:System.Diagnostics.Tracing.EventCounter> suivants en rapport avec les demandes entrantes :</span><span class="sxs-lookup"><span data-stu-id="accde-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="accde-289">Routage du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="accde-289">Endpoint routing</span></span>

<span data-ttu-id="accde-290">Le routage des points de terminaison, qui permet aux frameworks (par exemple, MVC) de fonctionner correctement avec l’intergiciel, est amélioré :</span><span class="sxs-lookup"><span data-stu-id="accde-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="accde-291">L’ordre des intergiciels et des points de terminaison est configurable dans le pipeline de traitement des requêtes de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="accde-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="accde-292">Les points de terminaison et l’intergiciel (middleware) sont bien adaptés à d’autres technologies basées sur les ASP.NET Core, telles que les contrôles d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="accde-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="accde-293">Les points de terminaison peuvent implémenter une stratégie, telle que CORS ou Authorization, dans l’intergiciel et le MVC.</span><span class="sxs-lookup"><span data-stu-id="accde-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="accde-294">Les filtres et les attributs peuvent être placés sur les méthodes dans les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="accde-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="accde-295">Pour plus d'informations, consultez <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="accde-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="accde-296">Contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="accde-296">Health Checks</span></span>

<span data-ttu-id="accde-297">Les contrôles d’intégrité utilisent le routage de point de terminaison avec l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="accde-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="accde-298">Dans `Startup.Configure`, appelez `MapHealthChecks` sur le générateur de points de terminaison à l’aide de l’URL de point de terminaison ou du chemin d’accès relatif :</span><span class="sxs-lookup"><span data-stu-id="accde-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="accde-299">Les points de terminaison de contrôle d’intégrité peuvent :</span><span class="sxs-lookup"><span data-stu-id="accde-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="accde-300">Spécifiez un ou plusieurs hôtes/ports autorisés.</span><span class="sxs-lookup"><span data-stu-id="accde-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="accde-301">Exiger une autorisation.</span><span class="sxs-lookup"><span data-stu-id="accde-301">Require authorization.</span></span>
* <span data-ttu-id="accde-302">Exiger CORS.</span><span class="sxs-lookup"><span data-stu-id="accde-302">Require CORS.</span></span>

<span data-ttu-id="accde-303">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="accde-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="accde-304">Canaux sur HttpContext</span><span class="sxs-lookup"><span data-stu-id="accde-304">Pipes on HttpContext</span></span>

<span data-ttu-id="accde-305">Il est maintenant possible de lire le corps de la demande et d’écrire le corps de la réponse à l’aide de l’API <xref:System.IO.Pipelines>.</span><span class="sxs-lookup"><span data-stu-id="accde-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="accde-306">Le paramètre</span><span class="sxs-lookup"><span data-stu-id="accde-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="accde-307">`HttpRequest.BodyReader` propriété fournit une <xref:System.IO.Pipelines.PipeReader> qui peut être utilisée pour lire le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="accde-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="accde-308">Le paramètre</span><span class="sxs-lookup"><span data-stu-id="accde-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="accde-309">`HttpResponse.BodyWriter` propriété fournit une <xref:System.IO.Pipelines.PipeWriter> qui peut être utilisée pour écrire le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="accde-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="accde-310">`HttpRequest.BodyReader` est un analogue du flux de `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="accde-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="accde-311">`HttpResponse.BodyWriter` est un analogue du flux de `HttpResponse.Body`.</span><span class="sxs-lookup"><span data-stu-id="accde-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="accde-312">Amélioration des rapports d’erreurs dans IIS</span><span class="sxs-lookup"><span data-stu-id="accde-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="accde-313">Les erreurs de démarrage lors de l’hébergement d’applications ASP.NET Core dans IIS génèrent désormais des données de diagnostic plus riches.</span><span class="sxs-lookup"><span data-stu-id="accde-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="accde-314">Ces erreurs sont signalées dans le journal des événements Windows avec des traces de pile, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="accde-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="accde-315">En outre, tous les avertissements, erreurs et exceptions non gérées sont consignés dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="accde-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="accde-316">Service Worker et SDK Worker</span><span class="sxs-lookup"><span data-stu-id="accde-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="accde-317">.NET Core 3,0 introduit le nouveau modèle d’application de service Worker.</span><span class="sxs-lookup"><span data-stu-id="accde-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="accde-318">Ce modèle fournit un point de départ pour l’écriture de services à long terme dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="accde-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="accde-319">Pour plus d'informations, consultez .</span><span class="sxs-lookup"><span data-stu-id="accde-319">For more information, see:</span></span>

* [<span data-ttu-id="accde-320">Les Workers .NET Core en tant que services Windows</span><span class="sxs-lookup"><span data-stu-id="accde-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="accde-321">Améliorations des intergiciels-en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="accde-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="accde-322">Dans les versions précédentes de ASP.NET Core, l’appel de <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> et de <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> étaient problématiques lorsqu’ils étaient déployés sur un serveur Linux Azure ou derrière un proxy inverse autre qu’IIS.</span><span class="sxs-lookup"><span data-stu-id="accde-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="accde-323">Le correctif pour les versions précédentes est documenté dans [transférer le schéma pour les proxys inversés Linux et non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="accde-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="accde-324">Ce scénario est résolu dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="accde-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="accde-325">L’hôte active l' [intergiciel d’en-tête transféré](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) lorsque la variable d’environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="accde-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="accde-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` est défini sur `true` dans les images de conteneur.</span><span class="sxs-lookup"><span data-stu-id="accde-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="accde-327">Amélioration des performances</span><span class="sxs-lookup"><span data-stu-id="accde-327">Performance improvements</span></span>

<span data-ttu-id="accde-328">ASP.NET Core 3,0 comprend de nombreuses améliorations qui réduisent l’utilisation de la mémoire et améliorent le débit :</span><span class="sxs-lookup"><span data-stu-id="accde-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="accde-329">Réduction de l’utilisation de la mémoire lors de l’utilisation du conteneur d’injection de dépendances intégré pour les services délimités.</span><span class="sxs-lookup"><span data-stu-id="accde-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="accde-330">Réduction des allocations dans l’infrastructure, y compris les scénarios de middleware et le routage.</span><span class="sxs-lookup"><span data-stu-id="accde-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="accde-331">Réduction de l’utilisation de la mémoire pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="accde-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="accde-332">Amélioration de la réduction de la mémoire et du débit pour les connexions HTTPs.</span><span class="sxs-lookup"><span data-stu-id="accde-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="accde-333">Nouveau sérialiseur JSON optimisé et entièrement asynchrone.</span><span class="sxs-lookup"><span data-stu-id="accde-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="accde-334">Réduction de l’utilisation de la mémoire et des améliorations du débit dans l’analyse de formulaire.</span><span class="sxs-lookup"><span data-stu-id="accde-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="accde-335">ASP.NET Core 3,0 s’exécute uniquement sur .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="accde-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="accde-336">À partir de ASP.NET Core 3,0, .NET Framework n’est plus une version cible de .NET Framework prise en charge.</span><span class="sxs-lookup"><span data-stu-id="accde-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="accde-337">Les projets ciblant .NET Framework peuvent continuer de façon entièrement prise en charge à l’aide de la [version .net Core 2,1 LTS](https://www.microsoft.com/net/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="accde-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="accde-338">La plupart des packages associés à ASP.NET Core 2.1. x seront pris en charge indéfiniment au-delà de la période LTS de trois ans pour .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="accde-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="accde-339">Pour plus d’informations sur la migration, consultez [porter votre code d' .NET Framework vers .net Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="accde-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="accde-340">Utiliser le ASP.NET Core Framework partagé</span><span class="sxs-lookup"><span data-stu-id="accde-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="accde-341">L’infrastructure partagée ASP.NET Core 3,0, contenue dans le [AspNetCore de Microsoft. app](xref:fundamentals/metapackage-app), ne nécessite plus un élément explicite `<PackageReference />` dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="accde-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="accde-342">Le Framework partagé est automatiquement référencé lors de l’utilisation du kit de développement logiciel (SDK) `Microsoft.NET.Sdk.Web` dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="accde-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="accde-343">Assemblys supprimés de la ASP.NET Core Framework partagé</span><span class="sxs-lookup"><span data-stu-id="accde-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="accde-344">Les assemblys les plus notables supprimés de l’infrastructure partagée ASP.NET Core 3,0 sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="accde-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="accde-345">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="accde-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="accde-346">Pour ajouter Json.NET à ASP.NET Core 3,0, consultez [Ajouter la prise en charge du format JSON basé sur Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="accde-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="accde-347">ASP.NET Core 3,0 introduit `System.Text.Json` pour la lecture et l’écriture de JSON.</span><span class="sxs-lookup"><span data-stu-id="accde-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="accde-348">Pour plus d’informations, consultez [nouvelle SÉRIALISATION JSON](#new-json-serialization) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="accde-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="accde-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="accde-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="accde-350">Pour obtenir la liste complète des assemblys supprimés de l’infrastructure partagée, consultez [assemblys en cours de suppression de Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="accde-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="accde-351">Pour plus d’informations sur la motivation de cette modification, consultez [modifications critiques apportées à Microsoft. AspNetCore. app dans 3,0](https://github.com/aspnet/Announcements/issues/325) et [un premier aperçu des modifications apportées à ASP.net Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="accde-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
