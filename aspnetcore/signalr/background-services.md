---
title: Hôte ASP.NET Core Signalr dans les services d’arrière-plan
author: bradygaster
description: Découvrez comment envoyer des messages aux clients Signalr à partir de classes BackgroundService .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773925"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="ca3fd-103">Hôte ASP.NET Core Signalr dans les services d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="ca3fd-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="ca3fd-104">Par [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="ca3fd-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="ca3fd-105">Cet article fournit des conseils pour :</span><span class="sxs-lookup"><span data-stu-id="ca3fd-105">This article provides guidance for:</span></span>

* <span data-ttu-id="ca3fd-106">Hébergement de hubs Signalr à l’aide d’un processus de travail en arrière-plan hébergé avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="ca3fd-107">Envoi de messages à des clients connectés à partir d’un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).net core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="ca3fd-108">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ca3fd-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="ca3fd-109">Signaler le Signalr au démarrage</span><span class="sxs-lookup"><span data-stu-id="ca3fd-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca3fd-110">L’hébergement ASP.NET Core hubs Signalr dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un Hub dans une application Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="ca3fd-111">Dans la `Startup.ConfigureServices` méthode, l' `services.AddSignalR` appel de ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge signalr.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="ca3fd-112">Dans `Startup.Configure`, la `MapHub` méthode est appelée dans le `UseEndpoints` rappel pour relier le ou les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ca3fd-113">L’hébergement ASP.NET Core hubs Signalr dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un Hub dans une application Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="ca3fd-114">Dans la `Startup.ConfigureServices` méthode, l' `services.AddSignalR` appel de ajoute les services requis à la couche d’injection de dépendances ASP.net Core (di) pour prendre en charge signalr.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="ca3fd-115">Dans `Startup.Configure`, la `UseSignalR` méthode est appelée pour relier le ou les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="ca3fd-116">Dans l’exemple précédent, la `ClockHub` classe implémente la `Hub<T>` classe pour créer un concentrateur fortement typé.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="ca3fd-117">A été configuré dans la `Startup` classe pour répondre aux demandes au niveau du point `/hubs/clock`de terminaison. `ClockHub`</span><span class="sxs-lookup"><span data-stu-id="ca3fd-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="ca3fd-118">Pour plus d’informations sur les hubs fortement typés, consultez [utiliser des hubs dans signalr pour ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="ca3fd-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="ca3fd-119">Cette fonctionnalité n’est pas limitée à la classe [\<Hub t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="ca3fd-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="ca3fd-120">Toute classe qui hérite de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), telle que [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), fonctionne également.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="ca3fd-121">L’interface utilisée par le fortement typé `ClockHub` est l' `IClock` interface.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="ca3fd-122">Appeler un concentrateur Signalr à partir d’un service d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="ca3fd-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="ca3fd-123">Au démarrage, la `Worker` classe, a `BackgroundService`, est connectée à `AddHostedService`l’aide de.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="ca3fd-124">Étant donné que signalr est également relié au `Startup` cours de la phase, dans lequel chaque concentrateur est attaché à un point de terminaison individuel dans le pipeline de requête HTTP `IHubContext<T>` de ASP.net Core, chaque concentrateur est représenté par un sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="ca3fd-125">À l’aide des fonctionnalités di de ASP.net Core, les autres classes instanciées par la `BackgroundService` couche d’hébergement, comme les classes, les classes de contrôleur MVC ou les modèles de page Razor, peuvent recevoir des `IHubContext<ClockHub, IClock>` références aux hubs côté serveur en acceptant des instances de lors de la construction.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="ca3fd-126">Étant donné `ExecuteAsync` que la méthode est appelée de manière itérative dans le service d’arrière-plan, la date et l’heure actuelles du serveur sont `ClockHub`envoyées aux clients connectés à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="ca3fd-127">Réagir aux événements Signalr avec les services d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="ca3fd-127">React to SignalR events with background services</span></span>

<span data-ttu-id="ca3fd-128">À l’instar d’une application à page unique utilisant le client JavaScript pour signalr ou une application de bureau .NET <xref:signalr/dotnet-client> `BackgroundService` , vous pouvez `IHostedService` utiliser le à l’aide de l’implémentation, ou pour vous connecter aux hubs signalr et répondre aux événements.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="ca3fd-129">La `ClockHubClient` classe implémente à la `IClock` fois l’interface `IHostedService` et l’interface.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="ca3fd-130">De cette façon, il peut être connecté `Startup` pendant l’exécution continue et répondre aux événements de concentrateur à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="ca3fd-131">`ClockHubClient` Pendant l’initialisation, crée une instance `HubConnection` de et relie la `IClock.ShowTime` méthode en tant que gestionnaire de l’événement du `ShowTime` concentrateur.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="ca3fd-132">Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="ca3fd-133">Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="ca3fd-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="ca3fd-134">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ca3fd-134">Additional resources</span></span>

* [<span data-ttu-id="ca3fd-135">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="ca3fd-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ca3fd-136">Hubs</span><span class="sxs-lookup"><span data-stu-id="ca3fd-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ca3fd-137">Publier sur Azure</span><span class="sxs-lookup"><span data-stu-id="ca3fd-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ca3fd-138">Hubs fortement typés</span><span class="sxs-lookup"><span data-stu-id="ca3fd-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
