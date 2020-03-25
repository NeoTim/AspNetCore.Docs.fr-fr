---
title: Routage vers les actions du contrôleur dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core MVC utilise le middleware (intergiciel) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: be7da9eeaf64c2f52c095b5179ccc22db43d57c3
ms.sourcegitcommit: 99e71ae03319ab386baf2ebde956fc2d511df8b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80242571"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="7547a-103">Routage vers les actions du contrôleur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7547a-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="7547a-104">Par [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7547a-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7547a-105">Les contrôleurs de ASP.NET Core utilisent l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) de routage pour faire correspondre les URL des demandes entrantes et les mapper aux [actions](#action).</span><span class="sxs-lookup"><span data-stu-id="7547a-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="7547a-106">Itinéraires des modèles :</span><span class="sxs-lookup"><span data-stu-id="7547a-106">Routes templates:</span></span>

* <span data-ttu-id="7547a-107">Sont définis dans le code de démarrage ou les attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="7547a-108">Décrivez comment les chemins d’URL sont mis en correspondance avec les [actions](#action).</span><span class="sxs-lookup"><span data-stu-id="7547a-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="7547a-109">Sont utilisées pour générer des URL pour les liens.</span><span class="sxs-lookup"><span data-stu-id="7547a-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="7547a-110">Les liens générés sont généralement retournés dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="7547a-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="7547a-111">Les actions sont soit [de façon conventionnelle,](#cr) soit [routées par attribut](#ar).</span><span class="sxs-lookup"><span data-stu-id="7547a-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="7547a-112">Le fait de placer un itinéraire sur le contrôleur ou l' [action](#action) le rend positionné par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="7547a-113">Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="7547a-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7547a-114">Ce document :</span><span class="sxs-lookup"><span data-stu-id="7547a-114">This document:</span></span>

* <span data-ttu-id="7547a-115">Explique les interactions entre MVC et le routage :</span><span class="sxs-lookup"><span data-stu-id="7547a-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="7547a-116">Comment les applications MVC classiques utilisent les fonctionnalités de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="7547a-117">Couvre les deux :</span><span class="sxs-lookup"><span data-stu-id="7547a-117">Covers both:</span></span>
    * <span data-ttu-id="7547a-118">Le [routage conventionnel](#cr) est généralement utilisé avec les contrôleurs et les vues.</span><span class="sxs-lookup"><span data-stu-id="7547a-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="7547a-119">*Routage d’attribut* utilisé avec les API REST.</span><span class="sxs-lookup"><span data-stu-id="7547a-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="7547a-120">Si vous êtes principalement intéressé par le routage des API REST, passez à la section relative à l' [acheminement des attributs pour les API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7547a-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="7547a-121">Consultez [routage](xref:fundamentals/routing) pour plus d’informations sur le routage avancé.</span><span class="sxs-lookup"><span data-stu-id="7547a-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="7547a-122">Fait référence au système de routage par défaut ajouté dans ASP.NET Core 3,0, appelé routage de point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7547a-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="7547a-123">Il est possible d’utiliser des contrôleurs avec la version précédente du routage pour des raisons de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="7547a-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="7547a-124">Pour obtenir des instructions, consultez le [Guide de migration 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="7547a-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="7547a-125">Reportez-vous à la [version 2,2 de ce document](xref:mvc/controllers/routing?view=aspnetcore-2.2) pour obtenir des documents de référence sur le système de routage hérité.</span><span class="sxs-lookup"><span data-stu-id="7547a-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="7547a-126">Configurer l’itinéraire conventionnel</span><span class="sxs-lookup"><span data-stu-id="7547a-126">Set up conventional route</span></span>

<span data-ttu-id="7547a-127">`Startup.Configure` a généralement un code similaire à ce qui suit lors de l’utilisation du [routage conventionnel](#crd):</span><span class="sxs-lookup"><span data-stu-id="7547a-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="7547a-128">Dans l’appel à <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> est utilisé pour créer un itinéraire unique.</span><span class="sxs-lookup"><span data-stu-id="7547a-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="7547a-129">L’itinéraire unique est nommé `default` itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-129">The single route is named `default` route.</span></span> <span data-ttu-id="7547a-130">La plupart des applications avec contrôleurs et vues utilisent un modèle de routage similaire à l’itinéraire `default`.</span><span class="sxs-lookup"><span data-stu-id="7547a-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="7547a-131">Les API REST doivent utiliser le [routage d’attributs](#ar).</span><span class="sxs-lookup"><span data-stu-id="7547a-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="7547a-132">Le modèle de routage `"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="7547a-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="7547a-133">Correspond à un chemin d’URL comme `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="7547a-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="7547a-134">Extrait les valeurs d’itinéraire `{ controller = Products, action = Details, id = 5 }` en tokenant le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="7547a-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7547a-135">L’extraction des valeurs de route aboutit à une correspondance si l’application possède un contrôleur nommé `ProductsController` et une action `Details` :</span><span class="sxs-lookup"><span data-stu-id="7547a-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 <span data-ttu-id="7547a-136">La méthode [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) est incluse dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) et est utilisée pour afficher des informations de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-136">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

  * <span data-ttu-id="7547a-137">`/Products/Details/5` modèle lie la valeur de `id = 5` pour définir le paramètre `id` sur `5`.</span><span class="sxs-lookup"><span data-stu-id="7547a-137">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="7547a-138">Pour plus d’informations, consultez [liaison de modèle](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="7547a-138">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="7547a-139">`{controller=Home}` définit `Home` comme `controller`par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-139">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="7547a-140">`{action=Index}` définit `Index` comme `action`par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-140">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="7547a-141">Le caractère `?` dans `{id?}` définit `id` comme facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-141">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="7547a-142">Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="7547a-142">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7547a-143">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7547a-143">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="7547a-144">Correspond au chemin d’accès de l’URL `/`.</span><span class="sxs-lookup"><span data-stu-id="7547a-144">Matches the URL path `/`.</span></span>
* <span data-ttu-id="7547a-145">Produit les valeurs de route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-145">Produces the route values `{ controller = Home, action = Index }`.</span></span>
* <span data-ttu-id="7547a-146">La méthode [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) est incluse dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) et est utilisée pour afficher des informations de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-146">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

<span data-ttu-id="7547a-147">Les valeurs de `controller` et `action` utilisent les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-147">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="7547a-148">`id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’accès de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-148">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7547a-149">`/` correspond uniquement s’il existe une action `HomeController` et `Index` :</span><span class="sxs-lookup"><span data-stu-id="7547a-149">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7547a-150">À l’aide de la définition de contrôleur et du modèle de routage précédents, l' `HomeController.Index` action est exécutée pour les chemins d’URL suivants :</span><span class="sxs-lookup"><span data-stu-id="7547a-150">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="7547a-151">Le chemin d’accès de l’URL `/` utilise les contrôleurs de `Home` par défaut du modèle de routage et l’action `Index`.</span><span class="sxs-lookup"><span data-stu-id="7547a-151">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="7547a-152">Le chemin d’URL `/Home` utilise l’action de `Index` par défaut du modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-152">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="7547a-153">La méthode pratique <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> :</span><span class="sxs-lookup"><span data-stu-id="7547a-153">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="7547a-154">Remplace</span><span class="sxs-lookup"><span data-stu-id="7547a-154">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7547a-155">Le routage est configuré à l’aide de l’intergiciel (middleware) <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span><span class="sxs-lookup"><span data-stu-id="7547a-155">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="7547a-156">Pour utiliser des contrôleurs :</span><span class="sxs-lookup"><span data-stu-id="7547a-156">To use controllers:</span></span>

* <span data-ttu-id="7547a-157">Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> dans `UseEndpoints` pour mapper les contrôleurs [routés d’attribut](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7547a-157">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="7547a-158">Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>pour mapper des contrôleurs [routés de façon conventionnelle](#cr) .</span><span class="sxs-lookup"><span data-stu-id="7547a-158">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7547a-159">Routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7547a-159">Conventional routing</span></span>

<span data-ttu-id="7547a-160">Le routage conventionnel est utilisé avec les contrôleurs et les vues.</span><span class="sxs-lookup"><span data-stu-id="7547a-160">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="7547a-161">La route `default` :</span><span class="sxs-lookup"><span data-stu-id="7547a-161">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="7547a-162">est un exemple de *routage conventionnel*.</span><span class="sxs-lookup"><span data-stu-id="7547a-162">is an example of a *conventional routing*.</span></span> <span data-ttu-id="7547a-163">Il s’agit du *routage conventionnel* , car il établit une *Convention* pour les chemins d’URL :</span><span class="sxs-lookup"><span data-stu-id="7547a-163">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7547a-164">Le premier segment de chemin d’accès, `{controller=Home}`, correspond au nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-164">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="7547a-165">Le deuxième segment, `{action=Index}`, correspond au nom de l' [action](#action) .</span><span class="sxs-lookup"><span data-stu-id="7547a-165">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="7547a-166">Le troisième segment, `{id?}` est utilisé pour un `id`facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-166">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="7547a-167">Le `?` dans `{id?}` le rend facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-167">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="7547a-168">`id` est utilisé pour mapper à une entité de modèle.</span><span class="sxs-lookup"><span data-stu-id="7547a-168">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="7547a-169">À l’aide de cette `default` itinéraire, le chemin d’URL :</span><span class="sxs-lookup"><span data-stu-id="7547a-169">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="7547a-170">`/Products/List` est mappée à l’action `ProductsController.List`.</span><span class="sxs-lookup"><span data-stu-id="7547a-170">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="7547a-171">`/Blog/Article/17` est mappé à `BlogController.Article` et, en général, le modèle lie le paramètre `id` à 17.</span><span class="sxs-lookup"><span data-stu-id="7547a-171">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="7547a-172">Ce mappage :</span><span class="sxs-lookup"><span data-stu-id="7547a-172">This mapping:</span></span>

* <span data-ttu-id="7547a-173">Est basé **uniquement**sur les noms de contrôleur et d' [action](#action) .</span><span class="sxs-lookup"><span data-stu-id="7547a-173">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="7547a-174">N’est pas basé sur des espaces de noms, des emplacements de fichiers sources ou des paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="7547a-174">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="7547a-175">L’utilisation du routage conventionnel avec l’itinéraire par défaut permet de créer l’application sans avoir à trouver un nouveau modèle d’URL pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7547a-175">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="7547a-176">Pour une application avec des actions de style [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , avec cohérence des URL entre les contrôleurs :</span><span class="sxs-lookup"><span data-stu-id="7547a-176">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="7547a-177">Permet de simplifier le code.</span><span class="sxs-lookup"><span data-stu-id="7547a-177">Helps simplify the code.</span></span>
* <span data-ttu-id="7547a-178">Rend l’interface utilisateur plus prévisible.</span><span class="sxs-lookup"><span data-stu-id="7547a-178">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7547a-179">Le `id` dans le code précédent est défini comme facultatif par le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-179">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="7547a-180">Les actions peuvent s’exécuter sans l’ID facultatif fourni dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-180">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="7547a-181">En règle générale, lorsque`id` est omis de l’URL :</span><span class="sxs-lookup"><span data-stu-id="7547a-181">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="7547a-182">`id` a la valeur `0` par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="7547a-182">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="7547a-183">Aucune entité n’a été trouvée dans le `id == 0`de la base de données correspondant.</span><span class="sxs-lookup"><span data-stu-id="7547a-183">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="7547a-184">Le [routage d’attributs](#ar) fournit un contrôle affiné pour rendre l’ID requis pour certaines actions et non pour d’autres.</span><span class="sxs-lookup"><span data-stu-id="7547a-184">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7547a-185">Par Convention, la documentation comprend des paramètres facultatifs comme `id` lorsqu’ils sont susceptibles d’apparaître dans une utilisation correcte.</span><span class="sxs-lookup"><span data-stu-id="7547a-185">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="7547a-186">La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites.</span><span class="sxs-lookup"><span data-stu-id="7547a-186">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="7547a-187">La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="7547a-187">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="7547a-188">Prend en charge un schéma de routage de base et descriptif.</span><span class="sxs-lookup"><span data-stu-id="7547a-188">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="7547a-189">Est un point de départ pratique pour les applications basées sur une interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7547a-189">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="7547a-190">Est le seul modèle de routage nécessaire pour de nombreuses applications d’interface utilisateur Web.</span><span class="sxs-lookup"><span data-stu-id="7547a-190">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="7547a-191">Pour les applications d’interface utilisateur Web plus volumineuses, un autre itinéraire utilise des [zones](#areas) si tout cela est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7547a-191">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="7547a-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> et <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="7547a-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="7547a-193">Assigner automatiquement une valeur de **commande** à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés.</span><span class="sxs-lookup"><span data-stu-id="7547a-193">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="7547a-194">Routage des points de terminaison dans ASP.NET Core 3,0 et versions ultérieures :</span><span class="sxs-lookup"><span data-stu-id="7547a-194">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="7547a-195">N’a pas de concept d’itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7547a-195">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="7547a-196">Ne fournit pas de garantie de classement pour l’exécution de l’extensibilité, tous les points de terminaison sont traités à la fois.</span><span class="sxs-lookup"><span data-stu-id="7547a-196">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="7547a-197">Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7547a-197">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="7547a-198">Le [routage des attributs](#ar) est expliqué plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="7547a-198">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="7547a-199">Plusieurs itinéraires conventionnels</span><span class="sxs-lookup"><span data-stu-id="7547a-199">Multiple conventional routes</span></span>

<span data-ttu-id="7547a-200">Vous pouvez ajouter plusieurs [itinéraires conventionnels](#cr) dans `UseEndpoints` en ajoutant des appels à <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="7547a-200">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="7547a-201">Cela permet de définir plusieurs conventions ou d’ajouter des itinéraires conventionnels dédiés à une [action](#action)spécifique, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7547a-201">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="7547a-202">Le `blog` itinéraire dans le code précédent est un **itinéraire conventionnel dédié**.</span><span class="sxs-lookup"><span data-stu-id="7547a-202">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="7547a-203">Il s’agit d’un itinéraire conventionnel dédié, car :</span><span class="sxs-lookup"><span data-stu-id="7547a-203">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="7547a-204">Il utilise le [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7547a-204">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="7547a-205">Elle est dédiée à une [action](#action)spécifique.</span><span class="sxs-lookup"><span data-stu-id="7547a-205">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="7547a-206">Étant donné que `controller` et `action` n’apparaissent pas dans le modèle de routage `"blog/{*article}"` en tant que paramètres :</span><span class="sxs-lookup"><span data-stu-id="7547a-206">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="7547a-207">Ils peuvent uniquement avoir les valeurs par défaut `{ controller = "Blog", action = "Article" }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-207">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="7547a-208">Cet itinéraire est toujours mappé au `BlogController.Article`d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-208">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7547a-209">`/Blog`, `/Blog/Article`et `/Blog/{any-string}` sont les seuls chemins d’URL qui correspondent à l’itinéraire du blog.</span><span class="sxs-lookup"><span data-stu-id="7547a-209">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="7547a-210">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-210">The preceding example:</span></span>

* <span data-ttu-id="7547a-211">`blog` itinéraire a une priorité plus élevée pour les correspondances que l’itinéraire de `default`, car il est ajouté en premier.</span><span class="sxs-lookup"><span data-stu-id="7547a-211">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="7547a-212">Est un exemple de routage de style [Slug](https://developer.mozilla.org/docs/Glossary/Slug) dans lequel il est courant d’avoir un nom d’article dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-212">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="7547a-213">Dans ASP.NET Core 3,0 et versions ultérieures, le routage ne suit pas :</span><span class="sxs-lookup"><span data-stu-id="7547a-213">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="7547a-214">Définissez un concept appelé *itinéraire*.</span><span class="sxs-lookup"><span data-stu-id="7547a-214">Define a concept called a *route*.</span></span> <span data-ttu-id="7547a-215">`UseRouting` ajoute la correspondance d’itinéraire au pipeline de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="7547a-215">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="7547a-216">L’intergiciel `UseRouting` examine l’ensemble des points de terminaison définis dans l’application et sélectionne la meilleure correspondance de point de terminaison en fonction de la demande.</span><span class="sxs-lookup"><span data-stu-id="7547a-216">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="7547a-217">Fournir des garanties sur l’ordre d’exécution de l’extensibilité comme <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="7547a-217">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="7547a-218">Consultez [routage](xref:fundamentals/routing) pour obtenir des documents de référence sur le routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-218">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="7547a-219">Ordre de routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7547a-219">Conventional routing order</span></span>

<span data-ttu-id="7547a-220">Le routage conventionnel correspond uniquement à une combinaison d’action et de contrôleur définie par l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-220">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="7547a-221">Cela vise à simplifier les cas où les itinéraires conventionnels se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="7547a-221">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="7547a-222">L’ajout d’itinéraires à l’aide de <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> assigne automatiquement une valeur de commande à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés.</span><span class="sxs-lookup"><span data-stu-id="7547a-222">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="7547a-223">Les correspondances d’un itinéraire qui apparaît précédemment ont une priorité plus élevée.</span><span class="sxs-lookup"><span data-stu-id="7547a-223">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="7547a-224">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7547a-224">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7547a-225">En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone.</span><span class="sxs-lookup"><span data-stu-id="7547a-225">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="7547a-226">Les [itinéraires conventionnels dédiés](#dcr) avec intercepter tous les paramètres d’itinéraire comme `{*article}` peuvent rendre une route trop [gourmande](xref:fundamentals/routing#greedy), ce qui signifie qu’elle correspond aux URL que vous avez prévues pour une mise en correspondance avec d’autres itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7547a-226">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7547a-227">Mettez les itinéraires gourmands plus tard dans la table de routage pour empêcher les correspondances gourmandes.</span><span class="sxs-lookup"><span data-stu-id="7547a-227">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="7547a-228">Résolution des actions ambiguës</span><span class="sxs-lookup"><span data-stu-id="7547a-228">Resolving ambiguous actions</span></span>

<span data-ttu-id="7547a-229">Lorsque deux points de terminaison correspondent au routage, le routage doit effectuer l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7547a-229">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="7547a-230">Choisissez le meilleur candidat.</span><span class="sxs-lookup"><span data-stu-id="7547a-230">Choose the best candidate.</span></span>
* <span data-ttu-id="7547a-231">Levée d'une exception.</span><span class="sxs-lookup"><span data-stu-id="7547a-231">Throw an exception.</span></span>

<span data-ttu-id="7547a-232">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7547a-232">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="7547a-233">Le contrôleur précédent définit deux actions qui correspondent :</span><span class="sxs-lookup"><span data-stu-id="7547a-233">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="7547a-234">Le chemin d’URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="7547a-234">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="7547a-235">`{ controller = Products33, action = Edit, id = 17 }`de données de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-235">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="7547a-236">Il s’agit d’un modèle classique pour les contrôleurs MVC :</span><span class="sxs-lookup"><span data-stu-id="7547a-236">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="7547a-237">`Edit(int)` affiche un formulaire pour modifier un produit.</span><span class="sxs-lookup"><span data-stu-id="7547a-237">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="7547a-238">`Edit(int, Product)` traite le formulaire publié.</span><span class="sxs-lookup"><span data-stu-id="7547a-238">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="7547a-239">Pour résoudre le routage correct :</span><span class="sxs-lookup"><span data-stu-id="7547a-239">To resolve the correct route:</span></span>

* <span data-ttu-id="7547a-240">`Edit(int, Product)` est sélectionné lorsque la requête est une `POST`HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-240">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="7547a-241">`Edit(int)` est sélectionné lorsque le [verbe http](#verb) est autre chose.</span><span class="sxs-lookup"><span data-stu-id="7547a-241">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="7547a-242">`Edit(int)` est généralement appelée via `GET`.</span><span class="sxs-lookup"><span data-stu-id="7547a-242">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="7547a-243">Le <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, est fourni au routage afin qu’il puisse choisir en fonction de la méthode HTTP de la requête.</span><span class="sxs-lookup"><span data-stu-id="7547a-243">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="7547a-244">Le `HttpPostAttribute` rend `Edit(int, Product)` une meilleure correspondance que `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="7547a-244">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="7547a-245">Il est important de comprendre le rôle des attributs comme `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7547a-245">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="7547a-246">Des attributs similaires sont définis pour d’autres [verbes HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="7547a-246">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="7547a-247">Dans le cadre d’un [routage conventionnel](#cr), il est courant que des actions utilisent le même nom d’action lorsqu’elles font partie d’un formulaire d’affichage, envoyer un flux de travail de formulaire.</span><span class="sxs-lookup"><span data-stu-id="7547a-247">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="7547a-248">Par exemple, consultez [examiner les deux méthodes d’action de modification](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="7547a-248">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="7547a-249">Si le routage ne peut pas choisir un meilleur candidat, une <xref:System.Reflection.AmbiguousMatchException> est levée, en répertoriant les différents points de terminaison correspondants.</span><span class="sxs-lookup"><span data-stu-id="7547a-249">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="7547a-250">Noms de routes conventionnels</span><span class="sxs-lookup"><span data-stu-id="7547a-250">Conventional route names</span></span>

<span data-ttu-id="7547a-251">Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de route conventionnels :</span><span class="sxs-lookup"><span data-stu-id="7547a-251">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7547a-252">Les noms de routes donnent un nom logique à l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-252">The route names give the route a logical name.</span></span> <span data-ttu-id="7547a-253">L’itinéraire nommé peut être utilisé pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-253">The named route can be used for URL generation.</span></span> <span data-ttu-id="7547a-254">L’utilisation d’un itinéraire nommé simplifie la création d’URL lorsque l’ordonnancement des itinéraires peut compliquer la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-254">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7547a-255">Les noms de routes doivent être uniques à l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-255">Route names must be unique application wide.</span></span>

<span data-ttu-id="7547a-256">Noms des itinéraires :</span><span class="sxs-lookup"><span data-stu-id="7547a-256">Route names:</span></span>

* <span data-ttu-id="7547a-257">N’ont aucun impact sur la correspondance d’URL ou la gestion des demandes.</span><span class="sxs-lookup"><span data-stu-id="7547a-257">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="7547a-258">Sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-258">Are used only for URL generation.</span></span>

<span data-ttu-id="7547a-259">Le concept de nom d’itinéraire est représenté dans le routage en tant que [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="7547a-259">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="7547a-260">Nom de l' **itinéraire** et **nom du point de terminaison**:</span><span class="sxs-lookup"><span data-stu-id="7547a-260">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="7547a-261">Sont interchangeables.</span><span class="sxs-lookup"><span data-stu-id="7547a-261">Are interchangeable.</span></span>
* <span data-ttu-id="7547a-262">Celui qui est utilisé dans la documentation et le code dépend de l’API qui est décrite.</span><span class="sxs-lookup"><span data-stu-id="7547a-262">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="7547a-263">Routage d’attribut pour les API REST</span><span class="sxs-lookup"><span data-stu-id="7547a-263">Attribute routing for REST APIs</span></span>

<span data-ttu-id="7547a-264">Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des [verbes HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="7547a-264">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="7547a-265">Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes.</span><span class="sxs-lookup"><span data-stu-id="7547a-265">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7547a-266">Le code `StartUp.Configure` suivant est courant pour une API REST et est utilisé dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-266">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="7547a-267">Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> est appelé dans `UseEndpoints` pour mapper les contrôleurs routés d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-267">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="7547a-268">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-268">In the following example:</span></span>

* <span data-ttu-id="7547a-269">La méthode `Configure` précédente est utilisée.</span><span class="sxs-lookup"><span data-stu-id="7547a-269">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="7547a-270">`HomeController` correspond à un ensemble d’URL similaires à ce que l’itinéraire conventionnel par défaut `{controller=Home}/{action=Index}/{id?}` correspond.</span><span class="sxs-lookup"><span data-stu-id="7547a-270">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="7547a-271">L’action `HomeController.Index` est exécutée pour tous les chemins d’URL `/`, `/Home`, `/Home/Index`ou `/Home/Index/3`.</span><span class="sxs-lookup"><span data-stu-id="7547a-271">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="7547a-272">Cet exemple met en évidence une différence de programmation clé entre le routage d’attributs et le [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7547a-272">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="7547a-273">Le routage des attributs requiert plus d’entrée pour spécifier un itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-273">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="7547a-274">L’itinéraire par défaut conventionnel gère les routes de façon plus succincte.</span><span class="sxs-lookup"><span data-stu-id="7547a-274">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7547a-275">Toutefois, le routage d’attributs permet et requiert un contrôle précis des modèles de routage qui s’appliquent à chaque [action](#action).</span><span class="sxs-lookup"><span data-stu-id="7547a-275">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="7547a-276">Avec le routage d’attributs, le nom du contrôleur et les noms d’action **ne jouent aucun** rôle dans lequel l’action est mise en correspondance.</span><span class="sxs-lookup"><span data-stu-id="7547a-276">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="7547a-277">L’exemple suivant correspond aux mêmes URL que l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-277">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7547a-278">Le code suivant utilise le remplacement de jeton pour `action` et `controller`:</span><span class="sxs-lookup"><span data-stu-id="7547a-278">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="7547a-279">Le code suivant s’applique `[Route("[controller]/[action]")]` au contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7547a-279">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7547a-280">Dans le code précédent, les modèles de méthode `Index` doivent ajouter `/` ou `~/` aux modèles de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-280">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="7547a-281">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-281">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="7547a-282">Pour plus d’informations sur la sélection d’un modèle de routage, consultez [priorité des modèles de routage](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="7547a-282">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="7547a-283">Noms de routage réservés</span><span class="sxs-lookup"><span data-stu-id="7547a-283">Reserved routing names</span></span>

<span data-ttu-id="7547a-284">Les mots clés suivants sont des noms de paramètres d’itinéraire réservés lors de l’utilisation de contrôleurs ou Razor Pages :</span><span class="sxs-lookup"><span data-stu-id="7547a-284">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="7547a-285">L’utilisation de `page` comme paramètre d’itinéraire avec routage d’attribut est une erreur courante.</span><span class="sxs-lookup"><span data-stu-id="7547a-285">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="7547a-286">Cela entraîne un comportement incohérent et confus avec la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-286">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="7547a-287">Les noms de paramètres spéciaux sont utilisés par la génération d’URL pour déterminer si une opération de génération d’URL fait référence à une page Razor ou à un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-287">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="7547a-288">Modèles de verbe HTTP</span><span class="sxs-lookup"><span data-stu-id="7547a-288">HTTP verb templates</span></span>

<span data-ttu-id="7547a-289">ASP.NET Core a les modèles de verbe HTTP suivants :</span><span class="sxs-lookup"><span data-stu-id="7547a-289">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="7547a-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="7547a-291">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-291">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="7547a-292">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="7547a-293">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="7547a-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="7547a-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="7547a-296">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="7547a-296">Route templates</span></span>

<span data-ttu-id="7547a-297">ASP.NET Core contient les modèles d’itinéraire suivants :</span><span class="sxs-lookup"><span data-stu-id="7547a-297">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="7547a-298">Tous les [modèles de verbe http](#verb) sont des modèles de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-298">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="7547a-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7547a-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="7547a-300">Routage d’attribut avec attributs de verbe http</span><span class="sxs-lookup"><span data-stu-id="7547a-300">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="7547a-301">Prenons le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-301">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7547a-302">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-302">In the preceding code:</span></span>

* <span data-ttu-id="7547a-303">Chaque action contient l’attribut `[HttpGet]`, qui limite la correspondance aux requêtes HTTP obtient uniquement.</span><span class="sxs-lookup"><span data-stu-id="7547a-303">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="7547a-304">L’action `GetProduct` comprend le modèle `"{id}"`, par conséquent `id` est ajouté au modèle `"api/[controller]"` sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-304">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="7547a-305">Le modèle de méthode est `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="7547a-305">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="7547a-306">Par conséquent, cette action correspond uniquement aux demandes d’extraction de pour le formulaire `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span><span class="sxs-lookup"><span data-stu-id="7547a-306">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="7547a-307">L’action `GetIntProduct` contient le modèle `"int/{id:int}")`.</span><span class="sxs-lookup"><span data-stu-id="7547a-307">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="7547a-308">La partie `:int` du modèle limite les valeurs de l’itinéraire `id` aux chaînes qui peuvent être converties en entier.</span><span class="sxs-lookup"><span data-stu-id="7547a-308">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="7547a-309">Une demande d’accès à `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="7547a-309">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="7547a-310">Ne correspond pas à cette action.</span><span class="sxs-lookup"><span data-stu-id="7547a-310">Doesn't match this action.</span></span>
  * <span data-ttu-id="7547a-311">Retourne une erreur [404 introuvable](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="7547a-311">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="7547a-312">L’action `GetInt2Product` contient des `{id}` dans le modèle, mais ne contraint pas les `id` à des valeurs qui peuvent être converties en entier.</span><span class="sxs-lookup"><span data-stu-id="7547a-312">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="7547a-313">Une demande d’accès à `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="7547a-313">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="7547a-314">Correspond à cet itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-314">Matches this route.</span></span>
  * <span data-ttu-id="7547a-315">La liaison de modèle ne parvient pas à convertir `abc` en entier.</span><span class="sxs-lookup"><span data-stu-id="7547a-315">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="7547a-316">Le paramètre `id` de la méthode est un entier.</span><span class="sxs-lookup"><span data-stu-id="7547a-316">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="7547a-317">Retourne une [demande 400 incorrecte](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , car la liaison de modèle n’a pas pu convertir`abc` en entier.</span><span class="sxs-lookup"><span data-stu-id="7547a-317">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="7547a-318">Le routage d’attribut peut utiliser des attributs de <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> tels que les <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, les <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>et les <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="7547a-318">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="7547a-319">Tous les attributs de [verbe http](#verb) acceptent un modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-319">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="7547a-320">L’exemple suivant montre deux actions qui correspondent au même modèle de routage :</span><span class="sxs-lookup"><span data-stu-id="7547a-320">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="7547a-321">Utilisation du chemin d’URL `/products3`:</span><span class="sxs-lookup"><span data-stu-id="7547a-321">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="7547a-322">L’action `MyProductsController.ListProducts` s’exécute lorsque le [verbe http](#verb) est `GET`.</span><span class="sxs-lookup"><span data-stu-id="7547a-322">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="7547a-323">L’action `MyProductsController.CreateProduct` s’exécute lorsque le [verbe http](#verb) est `POST`.</span><span class="sxs-lookup"><span data-stu-id="7547a-323">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="7547a-324">Lors de la création d’une API REST, il est rare que vous deviez utiliser `[Route(...)]` sur une méthode d’action, car l’action accepte toutes les méthodes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-324">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="7547a-325">Il est préférable d’utiliser l’attribut de [verbe http](#verb) plus spécifique pour préciser ce que votre API prend en charge.</span><span class="sxs-lookup"><span data-stu-id="7547a-325">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="7547a-326">Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7547a-326">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7547a-327">Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-327">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="7547a-328">Cela signifie que de nombreuses opérations, par exemple, obtenir et poster sur la même ressource logique, utilisent la même URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-328">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="7547a-329">Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.</span><span class="sxs-lookup"><span data-stu-id="7547a-329">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="7547a-330">Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-330">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7547a-331">Dans l’exemple suivant, `id` est requis en tant que partie du chemin d’URL :</span><span class="sxs-lookup"><span data-stu-id="7547a-331">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7547a-332">Action `Products2ApiController.GetProduct(int)` :</span><span class="sxs-lookup"><span data-stu-id="7547a-332">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="7547a-333">Est exécuté avec un chemin d’URL comme `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="7547a-333">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="7547a-334">N’est pas exécuté avec le chemin d’accès de l’URL `/products2`.</span><span class="sxs-lookup"><span data-stu-id="7547a-334">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="7547a-335">L’attribut [[Consommed]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permet à une action de limiter les types de contenu de demande pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7547a-335">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7547a-336">Pour plus d’informations, consultez [définir les types de contenu de demande pris en charge avec l’attribut consomme](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="7547a-336">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="7547a-337">Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.</span><span class="sxs-lookup"><span data-stu-id="7547a-337">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="7547a-338">Pour plus d’informations sur les `[ApiController]`, consultez [attribut ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="7547a-338">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="7547a-339">Nom de l’itinéraire</span><span class="sxs-lookup"><span data-stu-id="7547a-339">Route name</span></span>

<span data-ttu-id="7547a-340">Le code suivant définit un nom d’itinéraire de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="7547a-340">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7547a-341">Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique.</span><span class="sxs-lookup"><span data-stu-id="7547a-341">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7547a-342">Noms des itinéraires :</span><span class="sxs-lookup"><span data-stu-id="7547a-342">Route names:</span></span>

* <span data-ttu-id="7547a-343">N’ont aucun impact sur le comportement de correspondance d’URL du routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-343">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="7547a-344">Sont utilisés uniquement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-344">Are only used for URL generation.</span></span>

<span data-ttu-id="7547a-345">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-345">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7547a-346">Comparez le code précédent avec l’itinéraire par défaut conventionnel, qui définit le paramètre `id` comme facultatif (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="7547a-346">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7547a-347">La possibilité de spécifier avec précision les API présente des avantages, par exemple, pour distribuer des `/products` et des `/products/5` à différentes actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-347">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="7547a-348">Combinaison d’itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-348">Combining attribute routes</span></span>

<span data-ttu-id="7547a-349">Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="7547a-349">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7547a-350">Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-350">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7547a-351">Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-351">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="7547a-352">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-352">In the preceding example:</span></span>

* <span data-ttu-id="7547a-353">Le chemin d’accès de l’URL `/products` peut correspondre `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="7547a-353">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="7547a-354">Le chemin d’accès de l’URL `/products/5` peut faire correspondre `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="7547a-354">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="7547a-355">Ces deux actions correspondent uniquement à HTTP `GET`, car elles sont marquées avec l’attribut `[HttpGet]`.</span><span class="sxs-lookup"><span data-stu-id="7547a-355">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="7547a-356">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-356">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7547a-357">L’exemple suivant correspond à un ensemble de chemins d’URL similaires à l’itinéraire par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-357">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7547a-358">Le tableau suivant décrit les attributs de `[Route]` dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-358">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="7547a-359">Attribut</span><span class="sxs-lookup"><span data-stu-id="7547a-359">Attribute</span></span>               | <span data-ttu-id="7547a-360">Combine avec `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="7547a-360">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="7547a-361">Définit le modèle de routage</span><span class="sxs-lookup"><span data-stu-id="7547a-361">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="7547a-362">Oui</span><span class="sxs-lookup"><span data-stu-id="7547a-362">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="7547a-363">Oui</span><span class="sxs-lookup"><span data-stu-id="7547a-363">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="7547a-364">**Non**</span><span class="sxs-lookup"><span data-stu-id="7547a-364">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="7547a-365">Oui</span><span class="sxs-lookup"><span data-stu-id="7547a-365">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="7547a-366">Ordre de routage des attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-366">Attribute route order</span></span>

<span data-ttu-id="7547a-367">Le routage génère une arborescence et met en correspondance tous les points de terminaison simultanément :</span><span class="sxs-lookup"><span data-stu-id="7547a-367">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="7547a-368">Les entrées d’itinéraire se comportent comme si elles étaient placées dans un ordre idéal.</span><span class="sxs-lookup"><span data-stu-id="7547a-368">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="7547a-369">Les itinéraires les plus spécifiques ont une chance de s’exécuter avant les itinéraires plus généraux.</span><span class="sxs-lookup"><span data-stu-id="7547a-369">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7547a-370">Par exemple, un itinéraire d’attribut comme `blog/search/{topic}` est plus spécifique qu’un itinéraire d’attribut comme `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="7547a-370">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="7547a-371">L’itinéraire de `blog/search/{topic}` a une priorité plus élevée, par défaut, car il est plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="7547a-371">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="7547a-372">En utilisant le [routage conventionnel](#cr), le développeur est chargé de placer les routes dans l’ordre souhaité.</span><span class="sxs-lookup"><span data-stu-id="7547a-372">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7547a-373">Les itinéraires d’attributs peuvent configurer une commande à l’aide de la propriété <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>.</span><span class="sxs-lookup"><span data-stu-id="7547a-373">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="7547a-374">Tous les [attributs d’itinéraire](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fournis par le framework incluent `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-374">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="7547a-375">Les routes sont traitées selon un ordre croissant de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-375">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7547a-376">L’ordre par défaut est `0`.</span><span class="sxs-lookup"><span data-stu-id="7547a-376">The default order is `0`.</span></span> <span data-ttu-id="7547a-377">La définition d’un itinéraire à l’aide d' `Order = -1` s’exécute avant les itinéraires qui ne définissent pas de commande.</span><span class="sxs-lookup"><span data-stu-id="7547a-377">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="7547a-378">La définition d’un itinéraire à l’aide d' `Order = 1` s’exécute après l’ordonnancement par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-378">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="7547a-379">**Évitez** de dépendre de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-379">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="7547a-380">Si l’espace URL d’une application exige que les valeurs d’ordre explicites soient routées correctement, il est probable que les clients soient également déroutants.</span><span class="sxs-lookup"><span data-stu-id="7547a-380">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7547a-381">En général, le routage des attributs sélectionne l’itinéraire correct avec la correspondance d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-381">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="7547a-382">Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation d’un nom d’itinéraire comme remplacement est généralement plus simple que l’application de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-382">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7547a-383">Considérez les deux contrôleurs suivants qui définissent tous deux le `/home`de correspondance de routage :</span><span class="sxs-lookup"><span data-stu-id="7547a-383">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7547a-384">La demande d' `/home` avec le code précédent lève une exception semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="7547a-384">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="7547a-385">L’ajout d' `Order` à l’un des attributs de routage résout l’ambiguïté :</span><span class="sxs-lookup"><span data-stu-id="7547a-385">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="7547a-386">Avec le code précédent, `/home` exécute le point de terminaison `HomeController.Index`.</span><span class="sxs-lookup"><span data-stu-id="7547a-386">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="7547a-387">Pour accéder au `MyDemoController.MyIndex`, demandez `/home/MyIndex`.</span><span class="sxs-lookup"><span data-stu-id="7547a-387">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="7547a-388">**Remarque** :</span><span class="sxs-lookup"><span data-stu-id="7547a-388">**Note**:</span></span>

* <span data-ttu-id="7547a-389">Le code précédent est un exemple ou une conception de routage médiocre.</span><span class="sxs-lookup"><span data-stu-id="7547a-389">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="7547a-390">Il a été utilisé pour illustrer la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-390">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="7547a-391">La propriété `Order` résout uniquement l’ambiguïté, ce modèle ne peut pas être mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="7547a-391">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="7547a-392">Il serait préférable de supprimer le modèle de `[Route("Home")]`.</span><span class="sxs-lookup"><span data-stu-id="7547a-392">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="7547a-393">Consultez [Razor pages conventions de routage et d’application : ordre de routage](xref:razor-pages/razor-pages-conventions#route-order) pour plus d’informations sur l’ordre des itinéraires avec Razor pages.</span><span class="sxs-lookup"><span data-stu-id="7547a-393">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="7547a-394">Dans certains cas, une erreur HTTP 500 est retournée avec des itinéraires ambigus.</span><span class="sxs-lookup"><span data-stu-id="7547a-394">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="7547a-395">Utilisez la [journalisation](xref:fundamentals/logging/index) pour voir quels points de terminaison ont provoqué l' `AmbiguousMatchException`.</span><span class="sxs-lookup"><span data-stu-id="7547a-395">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7547a-396">Remplacement de jetons dans les modèles de routage [contrôleur], [action], [zone]</span><span class="sxs-lookup"><span data-stu-id="7547a-396">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="7547a-397">Pour plus de commodité, les itinéraires d’attributs prennent en charge le remplacement de jeton pour les paramètres d’itinéraire réservés en plaçant un jeton dans l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7547a-397">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="7547a-398">Accolades carrées : `[]`</span><span class="sxs-lookup"><span data-stu-id="7547a-398">Square braces: `[]`</span></span>
* <span data-ttu-id="7547a-399">Accolades : `{}`</span><span class="sxs-lookup"><span data-stu-id="7547a-399">Curly braces: `{}`</span></span>

<span data-ttu-id="7547a-400">Les jetons `[action]`, `[area]`et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom du contrôleur à partir de l’action dans laquelle l’itinéraire est défini :</span><span class="sxs-lookup"><span data-stu-id="7547a-400">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7547a-401">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-401">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="7547a-402">Correspond à `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="7547a-402">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="7547a-403">Correspond à `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="7547a-403">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="7547a-404">Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-404">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7547a-405">L’exemple précédent se comporte de la même façon que le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-405">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="7547a-406">Les routes d’attribut peuvent aussi être combinées avec l’héritage.</span><span class="sxs-lookup"><span data-stu-id="7547a-406">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7547a-407">Il s’agit d’une puissante combinaison avec le remplacement des jetons.</span><span class="sxs-lookup"><span data-stu-id="7547a-407">This is powerful combined with token replacement.</span></span> <span data-ttu-id="7547a-408">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-408">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="7547a-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`génère un nom d’itinéraire unique pour chaque action :</span><span class="sxs-lookup"><span data-stu-id="7547a-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="7547a-410">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-410">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="7547a-411">génère un nom d’itinéraire unique pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7547a-411">generates a unique route name for each action.</span></span>

<span data-ttu-id="7547a-412">Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="7547a-412">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7547a-413">Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons</span><span class="sxs-lookup"><span data-stu-id="7547a-413">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7547a-414">Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre.</span><span class="sxs-lookup"><span data-stu-id="7547a-414">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7547a-415">Un transformateur de paramètre implémente <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> et transforme la valeur des paramètres.</span><span class="sxs-lookup"><span data-stu-id="7547a-415">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="7547a-416">Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé remplace la valeur `SubscriptionManagement` route par `subscription-management`:</span><span class="sxs-lookup"><span data-stu-id="7547a-416">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="7547a-417"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> est une convention de modèle d’application qui :</span><span class="sxs-lookup"><span data-stu-id="7547a-417">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="7547a-418">Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.</span><span class="sxs-lookup"><span data-stu-id="7547a-418">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7547a-419">Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.</span><span class="sxs-lookup"><span data-stu-id="7547a-419">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="7547a-420">La méthode `ListAll` précédente correspond à `/subscription-management/list-all`.</span><span class="sxs-lookup"><span data-stu-id="7547a-420">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="7547a-421">`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7547a-421">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="7547a-422">Consultez la [documentation Web MDN sur Slug](https://developer.mozilla.org/docs/Glossary/Slug) pour connaître la définition de Slug.</span><span class="sxs-lookup"><span data-stu-id="7547a-422">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="7547a-423">Plusieurs itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-423">Multiple attribute routes</span></span>

<span data-ttu-id="7547a-424">Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action.</span><span class="sxs-lookup"><span data-stu-id="7547a-424">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7547a-425">L’utilisation la plus courante de cette méthode consiste à imiter le comportement de l’itinéraire conventionnel par défaut, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-425">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="7547a-426">Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux est combiné avec chacun des attributs de route sur les méthodes d’action :</span><span class="sxs-lookup"><span data-stu-id="7547a-426">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="7547a-427">Toutes les contraintes d’itinéraire de [verbe http](#verb) implémentent `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="7547a-427">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="7547a-428">Lorsque plusieurs attributs d’itinéraire qui implémentent <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sont placés sur une action :</span><span class="sxs-lookup"><span data-stu-id="7547a-428">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="7547a-429">Chaque contrainte d’action est associée au modèle de routage appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-429">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="7547a-430">L’utilisation de plusieurs itinéraires sur des actions peut paraître utile et puissante, mais il est préférable de conserver l’espace d’URL de base et bien défini.</span><span class="sxs-lookup"><span data-stu-id="7547a-430">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="7547a-431">Utilisez plusieurs itinéraires sur des actions **uniquement** lorsque cela est nécessaire, par exemple pour prendre en charge les clients existants.</span><span class="sxs-lookup"><span data-stu-id="7547a-431">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7547a-432">Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7547a-432">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7547a-433">Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.</span><span class="sxs-lookup"><span data-stu-id="7547a-433">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="7547a-434">Dans le code précédent, `[HttpPost("product/{id:int}")]` applique une contrainte d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-434">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="7547a-435">L’action `ProductsController.ShowProduct` correspond uniquement aux chemins d’URL tels que `/product/3`.</span><span class="sxs-lookup"><span data-stu-id="7547a-435">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="7547a-436">La partie de modèle de routage `{id:int}` limite ce segment à des entiers uniquement.</span><span class="sxs-lookup"><span data-stu-id="7547a-436">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7547a-437">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7547a-437">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7547a-438">Attributs d’itinéraire personnalisés à l’aide de IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="7547a-438">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="7547a-439">Tous les [attributs d’itinéraire](#rt) implémentent <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="7547a-439">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="7547a-440">Le runtime ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7547a-440">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="7547a-441">Recherche des attributs sur les classes de contrôleur et les méthodes d’action au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-441">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="7547a-442">Utilise les attributs qui implémentent `IRouteTemplateProvider` pour générer le jeu initial d’itinéraires.</span><span class="sxs-lookup"><span data-stu-id="7547a-442">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7547a-443">Implémentez `IRouteTemplateProvider` pour définir des attributs de routage personnalisés.</span><span class="sxs-lookup"><span data-stu-id="7547a-443">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="7547a-444">Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :</span><span class="sxs-lookup"><span data-stu-id="7547a-444">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="7547a-445">La méthode `Get` précédente retourne `Order = 2, Template = api/MyTestApi`.</span><span class="sxs-lookup"><span data-stu-id="7547a-445">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7547a-446">Utiliser le modèle d’application pour personnaliser les itinéraires d’attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-446">Use application model to customize attribute routes</span></span>

<span data-ttu-id="7547a-447">Le modèle d’application :</span><span class="sxs-lookup"><span data-stu-id="7547a-447">The application model:</span></span>

* <span data-ttu-id="7547a-448">Est un modèle objet créé au démarrage.</span><span class="sxs-lookup"><span data-stu-id="7547a-448">Is an object model created at startup.</span></span>
* <span data-ttu-id="7547a-449">Contient toutes les métadonnées utilisées par ASP.NET Core pour router et exécuter les actions dans une application.</span><span class="sxs-lookup"><span data-stu-id="7547a-449">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="7547a-450">Le modèle d’application comprend toutes les données collectées à partir des attributs d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-450">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="7547a-451">Les données des attributs de route sont fournies par l’implémentation de `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="7547a-451">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="7547a-452">Utilisées</span><span class="sxs-lookup"><span data-stu-id="7547a-452">Conventions:</span></span>

* <span data-ttu-id="7547a-453">Peut être écrit pour modifier le modèle d’application afin de personnaliser le comportement du routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-453">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="7547a-454">Sont lus au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-454">Are read at app startup.</span></span>

<span data-ttu-id="7547a-455">Cette section présente un exemple simple de personnalisation du routage à l’aide du modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-455">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="7547a-456">Le code suivant permet aux itinéraires de s’aligner à peu près avec la structure de dossiers du projet.</span><span class="sxs-lookup"><span data-stu-id="7547a-456">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="7547a-457">Le code suivant empêche l’application de la Convention de `namespace` aux contrôleurs qui sont des attributs routés :</span><span class="sxs-lookup"><span data-stu-id="7547a-457">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="7547a-458">Par exemple, le contrôleur suivant n’utilise pas `NamespaceRoutingConvention`:</span><span class="sxs-lookup"><span data-stu-id="7547a-458">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7547a-459">La méthode `NamespaceRoutingConvention.Apply` :</span><span class="sxs-lookup"><span data-stu-id="7547a-459">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="7547a-460">Ne fait rien si le contrôleur est un attribut routé.</span><span class="sxs-lookup"><span data-stu-id="7547a-460">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="7547a-461">Définit le modèle de contrôleurs en fonction de la `namespace`, avec le `namespace` de base supprimé.</span><span class="sxs-lookup"><span data-stu-id="7547a-461">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="7547a-462">La `NamespaceRoutingConvention` peut être appliquée dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7547a-462">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="7547a-463">Considérons, par exemple, le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-463">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="7547a-464">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7547a-464">In the preceding code:</span></span>

* <span data-ttu-id="7547a-465">Le `namespace` de base est `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="7547a-465">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="7547a-466">Le nom complet du contrôleur précédent est `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="7547a-466">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="7547a-467">Le `NamespaceRoutingConvention` définit le modèle de contrôleurs sur `Admin/Controllers/Users/[action]/{id?`.</span><span class="sxs-lookup"><span data-stu-id="7547a-467">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="7547a-468">Le `NamespaceRoutingConvention` peut également être appliqué en tant qu’attribut sur un contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7547a-468">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7547a-469">Routage mixte : routage conventionnel et routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-469">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7547a-470">Les applications ASP.NET Core peuvent combiner l’utilisation du routage conventionnel et du routage des attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-470">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7547a-471">Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.</span><span class="sxs-lookup"><span data-stu-id="7547a-471">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7547a-472">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-472">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7547a-473">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7547a-473">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7547a-474">Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa.</span><span class="sxs-lookup"><span data-stu-id="7547a-474">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7547a-475">**Tout** attribut de route sur le contrôleur effectue **toutes les** actions dans l’attribut de contrôleur routé.</span><span class="sxs-lookup"><span data-stu-id="7547a-475">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="7547a-476">Le routage des attributs et le routage conventionnel utilisent le même moteur de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-476">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="7547a-477">Génération d’URL et valeurs ambiantes</span><span class="sxs-lookup"><span data-stu-id="7547a-477">URL Generation and ambient values</span></span>

<span data-ttu-id="7547a-478">Les applications peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-478">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7547a-479">La génération d’URL élimine le codage en dur des URL, ce qui rend le code plus robuste et plus facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="7547a-479">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="7547a-480">Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre uniquement les notions de base du fonctionnement de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-480">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="7547a-481">Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7547a-481">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7547a-482">L’interface <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> est l’élément sous-jacent de l’infrastructure entre MVC et le routage pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-482">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7547a-483">Une instance de `IUrlHelper` est disponible via la propriété `Url` des contrôleurs, des vues et des composants de vue.</span><span class="sxs-lookup"><span data-stu-id="7547a-483">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7547a-484">Dans l’exemple suivant, l’interface `IUrlHelper` est utilisée via la propriété `Controller.Url` pour générer une URL vers une autre action.</span><span class="sxs-lookup"><span data-stu-id="7547a-484">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7547a-485">Si l’application utilise l’itinéraire conventionnel par défaut, la valeur de la variable `url` est la chaîne de chemin d’URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="7547a-485">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7547a-486">Ce chemin d’URL est créé par le routage en combinant :</span><span class="sxs-lookup"><span data-stu-id="7547a-486">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="7547a-487">Valeurs d’itinéraire de la requête actuelle, qui sont appelées **valeurs ambiantes**.</span><span class="sxs-lookup"><span data-stu-id="7547a-487">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="7547a-488">Les valeurs transmises à `Url.Action` et remplacent ces valeurs dans le modèle de routage :</span><span class="sxs-lookup"><span data-stu-id="7547a-488">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7547a-489">La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes.</span><span class="sxs-lookup"><span data-stu-id="7547a-489">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7547a-490">Un paramètre d’itinéraire qui n’a pas de valeur peut :</span><span class="sxs-lookup"><span data-stu-id="7547a-490">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="7547a-491">Utilisez une valeur par défaut s’il en a une.</span><span class="sxs-lookup"><span data-stu-id="7547a-491">Use a default value if it has one.</span></span>
* <span data-ttu-id="7547a-492">Être ignoré s’il est facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-492">Be skipped if it's optional.</span></span> <span data-ttu-id="7547a-493">Par exemple, le `id` à partir du modèle de routage `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="7547a-493">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="7547a-494">La génération d’URL échoue si un paramètre d’itinéraire requis n’a pas de valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="7547a-494">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7547a-495">Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.</span><span class="sxs-lookup"><span data-stu-id="7547a-495">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7547a-496">L’exemple précédent de `Url.Action` suppose un [routage conventionnel](#cr).</span><span class="sxs-lookup"><span data-stu-id="7547a-496">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="7547a-497">La génération d’URL fonctionne de la même manière avec le [routage des attributs](#ar), bien que les concepts soient différents.</span><span class="sxs-lookup"><span data-stu-id="7547a-497">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="7547a-498">Avec routage conventionnel :</span><span class="sxs-lookup"><span data-stu-id="7547a-498">With conventional routing:</span></span>

* <span data-ttu-id="7547a-499">Les valeurs de route sont utilisées pour développer un modèle.</span><span class="sxs-lookup"><span data-stu-id="7547a-499">The route values are used to expand a template.</span></span>
* <span data-ttu-id="7547a-500">Les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle.</span><span class="sxs-lookup"><span data-stu-id="7547a-500">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="7547a-501">Cela fonctionne parce que les URL mises en correspondance par le routage adhèrent à une convention.</span><span class="sxs-lookup"><span data-stu-id="7547a-501">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="7547a-502">L’exemple suivant utilise le routage d’attributs :</span><span class="sxs-lookup"><span data-stu-id="7547a-502">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="7547a-503">L’action `Source` dans le code précédent génère `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="7547a-503">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="7547a-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> a été ajouté dans ASP.NET Core 3,0 comme alternative à `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="7547a-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="7547a-505">`LinkGenerator` offre des fonctionnalités similaires mais plus flexibles.</span><span class="sxs-lookup"><span data-stu-id="7547a-505">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="7547a-506">Chaque autre méthode sur `IUrlHelper` a également une famille correspondante de méthodes sur `LinkGenerator`.</span><span class="sxs-lookup"><span data-stu-id="7547a-506">Each other the methods on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7547a-507">Génération des URL par nom d’action</span><span class="sxs-lookup"><span data-stu-id="7547a-507">Generating URLs by action name</span></span>

<span data-ttu-id="7547a-508">[URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)et toutes les surcharges associées sont toutes conçues pour générer le point de terminaison cible en spécifiant un nom de contrôleur et un nom d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-508">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="7547a-509">Lorsque vous utilisez `Url.Action`, les valeurs d’itinéraire actuelles pour `controller` et `action` sont fournies par le Runtime :</span><span class="sxs-lookup"><span data-stu-id="7547a-509">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="7547a-510">La valeur de `controller` et `action` font partie des valeurs [ambiantes](#ambient) et des valeurs.</span><span class="sxs-lookup"><span data-stu-id="7547a-510">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="7547a-511">La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et `controller` et génère un chemin d’URL qui achemine vers l’action actuelle.</span><span class="sxs-lookup"><span data-stu-id="7547a-511">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="7547a-512">Le routage tente d’utiliser les valeurs des valeurs ambiantes pour renseigner les informations qui n’ont pas été fournies lors de la génération d’une URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-512">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="7547a-513">Prenons l’exemple d’un itinéraire comme `{a}/{b}/{c}/{d}` avec des valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`:</span><span class="sxs-lookup"><span data-stu-id="7547a-513">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="7547a-514">Le routage dispose de suffisamment d’informations pour générer une URL sans aucune valeur supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="7547a-514">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="7547a-515">Le routage a suffisamment d’informations, car tous les paramètres d’itinéraire ont une valeur.</span><span class="sxs-lookup"><span data-stu-id="7547a-515">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="7547a-516">Si la valeur `{ d = Donovan }` est ajoutée :</span><span class="sxs-lookup"><span data-stu-id="7547a-516">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="7547a-517">La valeur `{ d = David }` est ignorée.</span><span class="sxs-lookup"><span data-stu-id="7547a-517">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="7547a-518">Le chemin d’URL généré est `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="7547a-518">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="7547a-519">**Avertissement**: les chemins d’accès d’URL sont hiérarchiques.</span><span class="sxs-lookup"><span data-stu-id="7547a-519">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="7547a-520">Dans l’exemple précédent, si la valeur `{ c = Cheryl }` est ajoutée :</span><span class="sxs-lookup"><span data-stu-id="7547a-520">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="7547a-521">Les deux valeurs `{ c = Carol, d = David }` sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="7547a-521">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="7547a-522">Il n’y a plus de valeur pour `d` et la génération d’URL échoue.</span><span class="sxs-lookup"><span data-stu-id="7547a-522">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="7547a-523">Les valeurs souhaitées de `c` et `d` doivent être spécifiées pour générer une URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-523">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="7547a-524">Vous pouvez vous attendre à rencontrer ce problème avec l’itinéraire par défaut `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="7547a-524">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="7547a-525">Ce problème est rare dans la pratique, car `Url.Action` spécifie toujours explicitement une valeur `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-525">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="7547a-526">Plusieurs surcharges d' [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) acceptent un objet de valeurs d’itinéraire pour fournir des valeurs pour les paramètres de routage autres que `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-526">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7547a-527">L’objet de valeurs d’itinéraire est fréquemment utilisé avec `id`.</span><span class="sxs-lookup"><span data-stu-id="7547a-527">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="7547a-528">Par exemple : `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7547a-528">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7547a-529">Objet de valeurs d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="7547a-529">The route values object:</span></span>

* <span data-ttu-id="7547a-530">Par Convention, est généralement un objet de type anonyme.</span><span class="sxs-lookup"><span data-stu-id="7547a-530">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="7547a-531">Peut être un `IDictionary<>` ou un [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="7547a-531">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="7547a-532">Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="7547a-532">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7547a-533">Le code précédent génère `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="7547a-533">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="7547a-534">Le code suivant génère une URL absolue :</span><span class="sxs-lookup"><span data-stu-id="7547a-534">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="7547a-535">Pour créer une URL absolue, utilisez l’une des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="7547a-535">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="7547a-536">Surcharge qui accepte un `protocol`.</span><span class="sxs-lookup"><span data-stu-id="7547a-536">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="7547a-537">Par exemple, le code précédent.</span><span class="sxs-lookup"><span data-stu-id="7547a-537">For example, the preceding code.</span></span>
* <span data-ttu-id="7547a-538">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), qui génère des URI absolus par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-538">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="7547a-539">Générer des URL par itinéraire</span><span class="sxs-lookup"><span data-stu-id="7547a-539">Generate URLs by route</span></span>

<span data-ttu-id="7547a-540">Le code précédent a démontré la génération d’une URL en passant le nom du contrôleur et de l’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-540">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7547a-541">`IUrlHelper` fournit également la famille de méthodes [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="7547a-541">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="7547a-542">Ces méthodes sont similaires à [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mais elles ne copient pas les valeurs actuelles de `action` et `controller` aux valeurs de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-542">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7547a-543">L’utilisation la plus courante de `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="7547a-543">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="7547a-544">Spécifie un nom d’itinéraire pour générer l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-544">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="7547a-545">Ne spécifie généralement pas un nom de contrôleur ou d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-545">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="7547a-546">Le fichier Razor suivant génère un lien HTML vers l' `Destination_Route`:</span><span class="sxs-lookup"><span data-stu-id="7547a-546">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="7547a-547">Générer des URL en HTML et Razor</span><span class="sxs-lookup"><span data-stu-id="7547a-547">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="7547a-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> fournit les méthodes <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> [html. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) et [html. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) pour générer des éléments `<form>` et `<a>` respectivement.</span><span class="sxs-lookup"><span data-stu-id="7547a-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7547a-549">Ces méthodes utilisent la méthode [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pour générer une URL et elles acceptent des arguments similaires.</span><span class="sxs-lookup"><span data-stu-id="7547a-549">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7547a-550">Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="7547a-550">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7547a-551">Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7547a-551">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7547a-552">Ils utilisent tous les deux `IUrlHelper` pour leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="7547a-552">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7547a-553">Pour plus d’informations, consultez [tag Helpers in Forms](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="7547a-553">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="7547a-554">Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="7547a-554">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="7547a-555">Génération d’URL dans les résultats d’action</span><span class="sxs-lookup"><span data-stu-id="7547a-555">URL generation in Action Results</span></span>

<span data-ttu-id="7547a-556">Les exemples précédents ont montré l’utilisation de `IUrlHelper` dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-556">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="7547a-557">L’utilisation la plus courante dans un contrôleur consiste à générer une URL dans le cadre d’un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-557">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7547a-558">Les classes de base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> et <xref:Microsoft.AspNetCore.Mvc.Controller> fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action.</span><span class="sxs-lookup"><span data-stu-id="7547a-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7547a-559">Une utilisation classique consiste à effectuer une redirection après avoir accepté l’entrée d’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7547a-559">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="7547a-560">Les méthodes de fabrique des résultats des actions, telles que <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> et <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> suivent un modèle similaire aux méthodes sur `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="7547a-560">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7547a-561">Cas spécial pour les routes conventionnelles dédiées</span><span class="sxs-lookup"><span data-stu-id="7547a-561">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7547a-562">Le [routage conventionnel](#cr) peut utiliser un type spécial de définition d’itinéraire appelé [itinéraire conventionnel dédié](#dcr).</span><span class="sxs-lookup"><span data-stu-id="7547a-562">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="7547a-563">Dans l’exemple suivant, l’itinéraire nommé `blog` est une route conventionnelle dédiée :</span><span class="sxs-lookup"><span data-stu-id="7547a-563">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7547a-564">À l’aide des définitions d’itinéraire précédentes, `Url.Action("Index", "Home")` génère le chemin d’URL `/` à l’aide de la `default` itinéraire, mais pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="7547a-564">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="7547a-565">Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7547a-565">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7547a-566">Les [itinéraires conventionnels dédiés](#dcr) s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de routage correspondant qui empêche l’itinéraire d’être trop [gourmand](xref:fundamentals/routing#greedy) en génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-566">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="7547a-567">Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-567">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7547a-568">Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-568">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7547a-569">La génération d’URL à l’aide de `blog` échoue parce que les valeurs `{ controller = Home, action = Index }` ne correspondent pas `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-569">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7547a-570">Le routage essaye alors d’utiliser `default`, ce qui réussit.</span><span class="sxs-lookup"><span data-stu-id="7547a-570">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7547a-571">Domaines</span><span class="sxs-lookup"><span data-stu-id="7547a-571">Areas</span></span>

<span data-ttu-id="7547a-572">Les [zones](xref:mvc/controllers/areas) sont une fonctionnalité MVC utilisée pour organiser les fonctionnalités associées dans un groupe sous la forme d’un groupe distinct :</span><span class="sxs-lookup"><span data-stu-id="7547a-572">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="7547a-573">Espace de noms de routage pour les actions du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-573">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="7547a-574">Structure de dossiers pour les vues.</span><span class="sxs-lookup"><span data-stu-id="7547a-574">Folder structure for views.</span></span>

<span data-ttu-id="7547a-575">L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, à condition qu’ils aient des zones différentes.</span><span class="sxs-lookup"><span data-stu-id="7547a-575">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="7547a-576">L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-576">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7547a-577">Cette section explique comment le routage interagit avec les zones.</span><span class="sxs-lookup"><span data-stu-id="7547a-577">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="7547a-578">Pour plus d’informations sur la façon dont les zones sont utilisées avec les vues, consultez [zones](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="7547a-578">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="7547a-579">L’exemple suivant configure MVC pour utiliser l’itinéraire conventionnel par défaut et un itinéraire de `area` pour un `area` nommé `Blog`:</span><span class="sxs-lookup"><span data-stu-id="7547a-579">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7547a-580">Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> est appelé pour créer le `"blog_route"`.</span><span class="sxs-lookup"><span data-stu-id="7547a-580">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="7547a-581">Le deuxième paramètre, `"Blog"`, est le nom de la zone.</span><span class="sxs-lookup"><span data-stu-id="7547a-581">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="7547a-582">En cas de correspondance avec un chemin d’URL comme `/Manage/Users/AddUser`, l’itinéraire `"blog_route"` génère les valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-582">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7547a-583">La valeur `area` route est produite par une valeur par défaut pour `area`.</span><span class="sxs-lookup"><span data-stu-id="7547a-583">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="7547a-584">L’itinéraire créé par `MapAreaControllerRoute` équivaut à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="7547a-584">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="7547a-585">`MapAreaControllerRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7547a-585">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7547a-586">La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-586">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="7547a-587">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7547a-587">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7547a-588">En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone.</span><span class="sxs-lookup"><span data-stu-id="7547a-588">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7547a-589">À l’aide de l’exemple précédent, les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }` correspondent à l’action suivante :</span><span class="sxs-lookup"><span data-stu-id="7547a-589">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7547a-590">L’attribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) désigne un contrôleur dans le cadre d’une zone.</span><span class="sxs-lookup"><span data-stu-id="7547a-590">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="7547a-591">Ce contrôleur se trouve dans la zone de `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7547a-591">This controller is in the `Blog` area.</span></span> <span data-ttu-id="7547a-592">Les contrôleurs sans attribut `[Area]` ne sont pas membres d’une zone et ne correspondent **pas** lorsque la valeur de l’itinéraire `area` est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-592">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7547a-593">Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-593">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="7547a-594">L’espace de noms de chaque contrôleur est indiqué ici par exhaustivité.</span><span class="sxs-lookup"><span data-stu-id="7547a-594">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="7547a-595">Si les contrôleurs précédents utilisent le même espace de noms, une erreur de compilateur est générée.</span><span class="sxs-lookup"><span data-stu-id="7547a-595">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="7547a-596">Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.</span><span class="sxs-lookup"><span data-stu-id="7547a-596">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7547a-597">Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`.</span><span class="sxs-lookup"><span data-stu-id="7547a-597">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7547a-598">Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-598">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="7547a-599">En termes de mise en correspondance avec *aucune valeur*, l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.</span><span class="sxs-lookup"><span data-stu-id="7547a-599">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7547a-600">Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que [valeur ambiante](#ambient) pour le routage à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-600">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="7547a-601">Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="7547a-601">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="7547a-602">Le code suivant génère une URL pour `/Zebra/Users/AddUser`:</span><span class="sxs-lookup"><span data-stu-id="7547a-602">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="7547a-603">Définition de l’action</span><span class="sxs-lookup"><span data-stu-id="7547a-603">Action definition</span></span>

<span data-ttu-id="7547a-604">Les méthodes publiques sur un contrôleur, à l’exception de celles avec l’attribut non- [action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , sont des actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-604">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="7547a-605">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="7547a-605">Sample code</span></span>

 * <span data-ttu-id="7547a-606">La méthode [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) est incluse dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) et est utilisée pour afficher des informations de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-606">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="7547a-607">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7547a-607">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7547a-608">ASP.NET Core MVC utilise [l’intergiciel](xref:fundamentals/middleware/index) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-608">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="7547a-609">Les routes sont définies dans le code de démarrage ou dans des attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-609">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="7547a-610">Les routes décrivent comment les chemins des URL doivent être mis en correspondance avec les actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-610">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="7547a-611">Les routes sont également utilisées pour générer des URL (pour les liens) envoyés dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="7547a-611">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="7547a-612">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-612">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7547a-613">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7547a-613">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7547a-614">Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="7547a-614">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7547a-615">Ce document explique les interactions entre MVC et le routage, et comment les applications MVC classiques utilisent les fonctionnalités du routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-615">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="7547a-616">Pour plus d’informations sur le routage avancé, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7547a-616">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="7547a-617">Configuration de l’intergiciel de routage</span><span class="sxs-lookup"><span data-stu-id="7547a-617">Setting up Routing Middleware</span></span>

<span data-ttu-id="7547a-618">Dans votre méthode *Configure*, vous pouvez voir un code similaire à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="7547a-618">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7547a-619">À l’intérieur de l’appel à `UseMvc`, `MapRoute` est utilisé pour créer une route, nous allons appeler route `default`.</span><span class="sxs-lookup"><span data-stu-id="7547a-619">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="7547a-620">La plupart des applications MVC utilisent une route avec un modèle similaire à la route `default`.</span><span class="sxs-lookup"><span data-stu-id="7547a-620">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="7547a-621">Le modèle de route `"{controller=Home}/{action=Index}/{id?}"` peut correspondre à un chemin d’URL comme `/Products/Details/5` et il extrait les valeurs `{ controller = Products, action = Details, id = 5 }` de la route en décomposant le chemin en jetons.</span><span class="sxs-lookup"><span data-stu-id="7547a-621">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7547a-622">MVC tente de trouver un contrôleur nommé `ProductsController` et d’exécuter l’action `Details` :</span><span class="sxs-lookup"><span data-stu-id="7547a-622">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="7547a-623">Notez que dans cet exemple, la liaison de modèle utilise la valeur de `id = 5` pour définir le paramètre `id` sur `5` lors de l’appel de cette action.</span><span class="sxs-lookup"><span data-stu-id="7547a-623">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="7547a-624">Pour plus d’informations, consultez [Liaison de modèle](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="7547a-624">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="7547a-625">Utilisation de la route `default` :</span><span class="sxs-lookup"><span data-stu-id="7547a-625">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7547a-626">Le modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7547a-626">The route template:</span></span>

* <span data-ttu-id="7547a-627">`{controller=Home}` définit `Home` comme `controller` par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-627">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="7547a-628">`{action=Index}` définit `Index` comme `action` par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-628">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="7547a-629">`{id?}` définit `id` comme étant facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-629">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="7547a-630">Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="7547a-630">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7547a-631">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7547a-631">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="7547a-632">`"{controller=Home}/{action=Index}/{id?}"` peut correspondre au chemin d’URL `/` et produit les valeurs de route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-632">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="7547a-633">Les valeurs de `controller` et `action` utilisent les valeurs par défaut, et `id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-633">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7547a-634">MVC utilisez ces valeurs de route pour sélectionner `HomeController` et l’action `Index` :</span><span class="sxs-lookup"><span data-stu-id="7547a-634">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7547a-635">Avec cette définition de contrôleur et ce modèle de route, l’action `HomeController.Index` est exécutée pour tous les chemins d’URL suivants :</span><span class="sxs-lookup"><span data-stu-id="7547a-635">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="7547a-636">La méthode pratique `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="7547a-636">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="7547a-637">Peut être utilisée pour remplacer :</span><span class="sxs-lookup"><span data-stu-id="7547a-637">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7547a-638">`UseMvc` et `UseMvcWithDefaultRoute` ajoutent une instance de `RouterMiddleware` au pipeline de l’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="7547a-638">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="7547a-639">MVC n’interagit pas directement avec l’intergiciel et utilise le routage pour gérer les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7547a-639">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="7547a-640">MVC est connecté aux routes via une instance de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="7547a-640">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="7547a-641">Le code de `UseMvc` est similaire à ceci :</span><span class="sxs-lookup"><span data-stu-id="7547a-641">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="7547a-642">`UseMvc` ne définit directement aucune route, il ajoute un espace réservé à la collection de routes pour la route `attribute`.</span><span class="sxs-lookup"><span data-stu-id="7547a-642">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="7547a-643">La surcharge `UseMvc(Action<IRouteBuilder>)` vous permet d’ajouter vos propres routes et prend également en charge le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-643">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="7547a-644">`UseMvc` et toutes ses variantes ajoutent un espace réservé pour l’attribut itinéraire-l’acheminement des attributs est toujours disponible, quelle que soit la façon dont vous configurez `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="7547a-644">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="7547a-645">`UseMvcWithDefaultRoute` définit une route par défaut et prend en charge le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-645">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="7547a-646">La section [Routage par attribut](#attribute-routing-ref-label) comprend plus de détails sur le routage par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-646">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7547a-647">Routage conventionnel</span><span class="sxs-lookup"><span data-stu-id="7547a-647">Conventional routing</span></span>

<span data-ttu-id="7547a-648">La route `default` :</span><span class="sxs-lookup"><span data-stu-id="7547a-648">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7547a-649">Le code précédent est un exemple de routage conventionnel.</span><span class="sxs-lookup"><span data-stu-id="7547a-649">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="7547a-650">Ce style est appelé routage conventionnel, car il établit une *Convention* pour les chemins d’URL :</span><span class="sxs-lookup"><span data-stu-id="7547a-650">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7547a-651">Le premier segment de chemin d’accès correspond au nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-651">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="7547a-652">Le deuxième correspond au nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-652">The second maps to the action name.</span></span>
* <span data-ttu-id="7547a-653">Le troisième segment est utilisé pour un `id`facultatif.</span><span class="sxs-lookup"><span data-stu-id="7547a-653">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="7547a-654">`id` est mappée à une entité de modèle.</span><span class="sxs-lookup"><span data-stu-id="7547a-654">`id` maps to a model entity.</span></span>

<span data-ttu-id="7547a-655">En utilisant cette route `default`, le chemin d’URL `/Products/List` mappe à l’action `ProductsController.List`, et `/Blog/Article/17` mappe à `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7547a-655">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="7547a-656">Ce mappage est basé **uniquement** sur les noms de contrôleur et d’action ; il n’est pas basé sur les espaces de noms, les emplacements des fichiers sources ou les paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="7547a-656">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="7547a-657">L’utilisation du routage conventionnel avec la route par défaut vous permet de créer rapidement l’application sans avoir à élaborer un nouveau modèle d’URL pour chaque action que vous définissez.</span><span class="sxs-lookup"><span data-stu-id="7547a-657">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="7547a-658">Pour une application avec des actions de style CRUD, le fait de disposer d’une cohérence pour les URL entre vos contrôleurs peut simplifier votre code et rendre votre interface utilisateur plus prévisible.</span><span class="sxs-lookup"><span data-stu-id="7547a-658">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7547a-659">`id` est défini comme étant facultatif par le modèle de route, ce qui signifie que vos actions peuvent s’exécuter sans l’ID fourni dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-659">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="7547a-660">En général, si `id` est omis de l’URL, il est défini sur `0` par la liaison de modèle : aucune entité correspondant à `id == 0` n’est donc trouvée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7547a-660">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="7547a-661">Le routage par attribut vous donne un contrôle précis pour rendre le code obligatoire pour certaines actions et pas pour d’autres.</span><span class="sxs-lookup"><span data-stu-id="7547a-661">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7547a-662">Par convention, la documentation inclut des paramètres facultatifs comme `id` quand ils sont susceptibles d’apparaître dans une utilisation correcte.</span><span class="sxs-lookup"><span data-stu-id="7547a-662">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="7547a-663">Routes multiples</span><span class="sxs-lookup"><span data-stu-id="7547a-663">Multiple routes</span></span>

<span data-ttu-id="7547a-664">Vous pouvez ajouter plusieurs routes dans `UseMvc` en ajoutant plusieurs appels à `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="7547a-664">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="7547a-665">Ceci vous permet de définir plusieurs conventions ou d’ajouter des routes conventionnelles qui sont dédiées à une action spécifique, comme :</span><span class="sxs-lookup"><span data-stu-id="7547a-665">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7547a-666">La route `blog` est ici une *route conventionnelle dédiée*, ce qui signifie qu’elle utilise le système de routage conventionnel, mais qu’elle est dédiée à une action spécifique.</span><span class="sxs-lookup"><span data-stu-id="7547a-666">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="7547a-667">Comme `controller` et `action` n’apparaissent pas dans le modèle de route en tant que paramètres, ils peuvent seulement avoir les valeurs par défaut et par conséquent, cette route sera toujours mappée à l’action `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7547a-667">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7547a-668">Les routes de la collection de routes sont ordonnées et elles sont traitées dans l’ordre où elles sont ajoutées.</span><span class="sxs-lookup"><span data-stu-id="7547a-668">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="7547a-669">Ainsi, dans cet exemple, la route `blog` est essayée avant la route `default`.</span><span class="sxs-lookup"><span data-stu-id="7547a-669">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-670">Les *itinéraires conventionnels dédiés* utilisent souvent des paramètres de routage de type **« catch-all »** comme `{*article}` pour capturer la partie restante du chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-670">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="7547a-671">Ceci peut rendre une route « trop globale », ce qui signifie qu’elle correspond à des URL qui devaient être mises en correspondance par d’autres routes.</span><span class="sxs-lookup"><span data-stu-id="7547a-671">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7547a-672">Pour résoudre ce problème, placez les routes « globales » plus loin dans la table de routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-672">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="7547a-673">Processus de repli</span><span class="sxs-lookup"><span data-stu-id="7547a-673">Fallback</span></span>

<span data-ttu-id="7547a-674">Dans le cadre du traitement des requêtes, MVC vérifie que les valeurs de route peuvent être utilisées pour rechercher un contrôleur et une action dans votre application.</span><span class="sxs-lookup"><span data-stu-id="7547a-674">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="7547a-675">Si les valeurs de route ne correspondent pas à une action, la route n’est pas considérée comme une correspondance, et la route suivante est essayée.</span><span class="sxs-lookup"><span data-stu-id="7547a-675">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="7547a-676">Ceci est appelé *processus de repli* et est conçu pour simplifier les cas où des routes conventionnelles se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="7547a-676">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="7547a-677">Résolution des ambiguïtés pour les actions</span><span class="sxs-lookup"><span data-stu-id="7547a-677">Disambiguating actions</span></span>

<span data-ttu-id="7547a-678">Quand deux actions correspondent via le routage, MVC doit résoudre l’ambiguïté pour choisir le « meilleur » candidat ou sinon lever une exception.</span><span class="sxs-lookup"><span data-stu-id="7547a-678">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="7547a-679">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7547a-679">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="7547a-680">Ce contrôleur définit deux actions qui correspondraient au chemin d’URL `/Products/Edit/17` et les données de routage `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-680">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="7547a-681">Il s’agit d’un modèle classique pour les contrôleurs MVC, où `Edit(int)` montre un formulaire pour modifier un produit, et où `Edit(int, Product)` traite le formulaire envoyé.</span><span class="sxs-lookup"><span data-stu-id="7547a-681">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="7547a-682">Pour rendre cela possible, MVC doit choisir `Edit(int, Product)` quand la requête est `POST` HTTP, et `Edit(int)` quand le verbe HTTP est autre chose.</span><span class="sxs-lookup"><span data-stu-id="7547a-682">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="7547a-683">`HttpPostAttribute` (`[HttpPost]`) est une implémentation de `IActionConstraint` qui permet la sélection de l’action seulement quand le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="7547a-683">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7547a-684">La présence d’une `IActionConstraint` fait de `Edit(int, Product)` une correspondance « meilleure » que `Edit(int)`, de sorte que `Edit(int, Product)` est essayé en premier.</span><span class="sxs-lookup"><span data-stu-id="7547a-684">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="7547a-685">Vous devez écrire des implémentations personnalisées de `IActionConstraint` seulement dans des scénarios spécifiques, mais il est important de comprendre le rôle d’attributs comme `HttpPostAttribute` ; des attributs similaires sont définis pour les autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-685">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="7547a-686">Dans le routage conventionnel, il est courant que des actions utilisent un même nom d’action quand elles font partie d’un flux de travail `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="7547a-686">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="7547a-687">L’avantage de ce modèle devient plus évident après la lecture de la section [Présentation d’IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="7547a-687">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="7547a-688">Si plusieurs routes correspondent et que MVC ne peut pas trouver une « meilleure » route, elle lève une `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="7547a-688">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="7547a-689">Noms des routes</span><span class="sxs-lookup"><span data-stu-id="7547a-689">Route names</span></span>

<span data-ttu-id="7547a-690">Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de routes :</span><span class="sxs-lookup"><span data-stu-id="7547a-690">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7547a-691">Les noms de routes donnent aux routes des noms logiques : le nom de route peut ainsi être utilisé pour la génération des URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-691">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="7547a-692">Ceci simplifie considérablement la création d’URL quand l’ordonnancement des routes peut rendre compliquée la génération des URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-692">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7547a-693">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-693">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7547a-694">Les noms de routes n’ont pas d’impact sur la correspondance des URL ni sur le traitement des requêtes ; ils sont utilisés seulement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-694">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="7547a-695">La section [Routage](xref:fundamentals/routing) contient des informations plus détaillées sur la génération d’URL, notamment la génération d’URL dans les helpers spécifiques à MVC.</span><span class="sxs-lookup"><span data-stu-id="7547a-695">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="7547a-696">Routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-696">Attribute routing</span></span>

<span data-ttu-id="7547a-697">Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes.</span><span class="sxs-lookup"><span data-stu-id="7547a-697">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7547a-698">Dans l’exemple suivant, `app.UseMvc();` est utilisé dans la méthode `Configure` et aucune route n’est passée.</span><span class="sxs-lookup"><span data-stu-id="7547a-698">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="7547a-699">`HomeController` va trouver des correspondances avec un ensemble d’URL similaires aux correspondances qui seraient trouvées par la route par défaut `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="7547a-699">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="7547a-700">L’action `HomeController.Index()` sera exécutée pour tous les chemins d’URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="7547a-700">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-701">Cet exemple met en évidence une différence importante en termes de programmation entre le routage par attributs et le routage conventionnel.</span><span class="sxs-lookup"><span data-stu-id="7547a-701">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="7547a-702">Le routage par attributs nécessite des entrées en plus grand nombre pour spécifier une route ; la route conventionnelle par défaut gère les routes de façon plus succincte.</span><span class="sxs-lookup"><span data-stu-id="7547a-702">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7547a-703">Cependant, le routage par attributs permet (et nécessite) un contrôle précis des modèles de routes qui s’appliquent à chaque action.</span><span class="sxs-lookup"><span data-stu-id="7547a-703">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="7547a-704">Avec le routage par attributs, le nom du contrôleur et les noms des actions ne jouent **aucun** rôle dans la sélection de l’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-704">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="7547a-705">Cet exemple trouve une correspondance avec les mêmes URL que l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="7547a-705">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="7547a-706">Les modèles de routes ci-dessus ne définissent pas de paramètres de route pour `action`, `area` et `controller`.</span><span class="sxs-lookup"><span data-stu-id="7547a-706">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="7547a-707">En fait, ces paramètres de route ne sont pas autorisés dans les routes par attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-707">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="7547a-708">Comme le modèle de route est déjà associé à une action, cela n’aurait pas de sens d’analyser le nom de l’action à partir de l’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-708">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="7547a-709">Routage par attributs avec des attributs Http[Verbe]</span><span class="sxs-lookup"><span data-stu-id="7547a-709">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="7547a-710">Le routage par attributs peut aussi utiliser les attributs `Http[Verb]`, comme `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7547a-710">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="7547a-711">Tous ces attributs peuvent accepter un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-711">All of these attributes can accept a route template.</span></span> <span data-ttu-id="7547a-712">Cet exemple montre deux actions qui correspondent au même modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7547a-712">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="7547a-713">Pour un chemin d’URL comme `/products`, l’action `ProductsApi.ListProducts` est exécutée quand le verbe HTTP est `GET`, et `ProductsApi.CreateProduct` est exécutée quand le verbe HTTP est `POST`.</span><span class="sxs-lookup"><span data-stu-id="7547a-713">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7547a-714">Le routage par attributs recherche d’abord une correspondance de l’URL par rapport à l’ensemble des modèles de routes défini par les attributs de la route.</span><span class="sxs-lookup"><span data-stu-id="7547a-714">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="7547a-715">Une fois qu’un modèle de route correspond, les contraintes de `IActionConstraint` sont appliquées pour déterminer quelles actions peuvent être exécutées.</span><span class="sxs-lookup"><span data-stu-id="7547a-715">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="7547a-716">Lors de la génération d’une API REST, il est rare que vous souhaitiez utiliser `[Route(...)]` sur une méthode d’action, car l’action accepte toutes les méthodes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-716">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="7547a-717">Il est préférable d’utiliser les `Http*Verb*Attributes` plus spécifiques pour plus de précision quant à ce qui est pris en charge par votre API.</span><span class="sxs-lookup"><span data-stu-id="7547a-717">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="7547a-718">Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7547a-718">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7547a-719">Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-719">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7547a-720">Dans cet exemple, `id` est obligatoire dans le chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-720">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7547a-721">L’action `ProductsApi.GetProduct(int)` est exécutée pour un chemin d’URL comme `/products/3`, mais pas pour un chemin d’URL comme `/products`.</span><span class="sxs-lookup"><span data-stu-id="7547a-721">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="7547a-722">Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.</span><span class="sxs-lookup"><span data-stu-id="7547a-722">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="7547a-723">Nom des routes</span><span class="sxs-lookup"><span data-stu-id="7547a-723">Route Name</span></span>

<span data-ttu-id="7547a-724">Le code suivant définit un *nom de route*`Products_List` :</span><span class="sxs-lookup"><span data-stu-id="7547a-724">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7547a-725">Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique.</span><span class="sxs-lookup"><span data-stu-id="7547a-725">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7547a-726">Les noms de routes n’ont pas d’impact sur le comportement de mise en correspondance des URL du routage et ils sont utilisés seulement pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-726">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="7547a-727">Les noms de routes doivent être unique à l’échelle de l’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-727">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-728">Comparez ceci avec la *route par défaut* conventionnelle, qui définit le paramètre `id` comme étant facultatif (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="7547a-728">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7547a-729">Cette possibilité de spécifier les API avec précision présente des avantages, par exemple de permettre de diriger `/products` et `/products/5` vers des actions différentes.</span><span class="sxs-lookup"><span data-stu-id="7547a-729">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="7547a-730">Combinaison de routes</span><span class="sxs-lookup"><span data-stu-id="7547a-730">Combining routes</span></span>

<span data-ttu-id="7547a-731">Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="7547a-731">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7547a-732">Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-732">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7547a-733">Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-733">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7547a-734">Dans cet exemple, le chemin d’URL `/products` peut être mis en correspondance avec `ProductsApi.ListProducts` et le chemin d’URL `/products/5` peut être mis en correspondance avec `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="7547a-734">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="7547a-735">Ces deux actions correspondent uniquement à HTTP `GET`, car elles sont marquées avec le `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7547a-735">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="7547a-736">Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7547a-736">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7547a-737">Cet exemple met en correspondance avec un ensemble de chemins d’URL similaires à la *route par défaut*.</span><span class="sxs-lookup"><span data-stu-id="7547a-737">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="7547a-738">Ordonnancement des routes d’attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-738">Ordering attribute routes</span></span>

<span data-ttu-id="7547a-739">Contrairement aux itinéraires conventionnels, qui s’exécutent dans un ordre défini, le routage d’attributs génère une arborescence et met en correspondance tous les itinéraires simultanément.</span><span class="sxs-lookup"><span data-stu-id="7547a-739">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="7547a-740">Il se comporte comme-si les entrées des routes étaient placées dans un ordre idéal ; les routes les plus spécifiques ont ainsi plus de probabilités de s’exécuter avant les routes plus générales.</span><span class="sxs-lookup"><span data-stu-id="7547a-740">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7547a-741">Par exemple, une route comme `blog/search/{topic}` est plus spécifique qu’une route comme `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="7547a-741">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="7547a-742">D’un point de vue logique, la route `blog/search/{topic}` « s’exécute » par défaut en premier, car il s’agit du seul ordre sensé.</span><span class="sxs-lookup"><span data-stu-id="7547a-742">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="7547a-743">Avec le routage conventionnel, le développeur est responsable du placement des routes dans l’ordre souhaité.</span><span class="sxs-lookup"><span data-stu-id="7547a-743">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7547a-744">Les routes d’attribut peuvent configurer un ordre en utilisant la propriété `Order` de tous les attributs de route fournis par le framework.</span><span class="sxs-lookup"><span data-stu-id="7547a-744">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="7547a-745">Les routes sont traitées selon un ordre croissant de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-745">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7547a-746">L’ordre par défaut est `0`.</span><span class="sxs-lookup"><span data-stu-id="7547a-746">The default order is `0`.</span></span> <span data-ttu-id="7547a-747">La définition d’une route avec `Order = -1` fait que cette route « s’exécute » avant les routes qui ne définissent pas d’ordre.</span><span class="sxs-lookup"><span data-stu-id="7547a-747">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="7547a-748">La définition d’une route avec `Order = 1` fait que cette route « s’exécute » après l’ordre des routes par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-748">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="7547a-749">Évitez de dépendre de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-749">Avoid depending on `Order`.</span></span> <span data-ttu-id="7547a-750">Si votre espace d’URL nécessite des valeurs d’ordre explicites pour router correctement, il est probable qu’il prête également à confusion pour les clients.</span><span class="sxs-lookup"><span data-stu-id="7547a-750">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7547a-751">D’une façon générale, le routage par attributs sélectionne la route correcte avec la mise en correspondance d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-751">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="7547a-752">Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation à titre de remplacement d’un nom de route est généralement plus simple que d’appliquer la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-752">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7547a-753">Le routage de Razor Pages et celui du contrôleur MVC partagent une implémentation.</span><span class="sxs-lookup"><span data-stu-id="7547a-753">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="7547a-754">Les informations relatives à l’ordre d’itinéraire dans les rubriques de Razor Pages sont disponibles à la page [Conventions d’itinéraires et d’applications Razor Pages : ordre d’itinéraire](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="7547a-754">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7547a-755">Remplacement de jetons dans les modèles de routes ([contrôleur], [action], [zone])</span><span class="sxs-lookup"><span data-stu-id="7547a-755">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="7547a-756">Pour plus de commodité, les routes d’attribut prennent en charge *le remplacement de jetons*, qui se fait via la mise entre crochets d’un jeton (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="7547a-756">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="7547a-757">Les jetons `[action]`, `[area]` et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom du contrôleur de l’action où la route est définie.</span><span class="sxs-lookup"><span data-stu-id="7547a-757">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="7547a-758">Dans l’exemple suivant, les actions correspondent aux chemins d’URL comme décrit dans les commentaires :</span><span class="sxs-lookup"><span data-stu-id="7547a-758">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7547a-759">Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-759">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7547a-760">L’exemple ci-dessus se comporte comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-760">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7547a-761">Les routes d’attribut peuvent aussi être combinées avec l’héritage.</span><span class="sxs-lookup"><span data-stu-id="7547a-761">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7547a-762">Combiné avec le remplacement de jetons, c’est particulièrement puissant.</span><span class="sxs-lookup"><span data-stu-id="7547a-762">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="7547a-763">Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-763">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="7547a-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` génère un nom de route unique pour chaque action.</span><span class="sxs-lookup"><span data-stu-id="7547a-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="7547a-765">Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="7547a-765">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7547a-766">Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons</span><span class="sxs-lookup"><span data-stu-id="7547a-766">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7547a-767">Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre.</span><span class="sxs-lookup"><span data-stu-id="7547a-767">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7547a-768">Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres.</span><span class="sxs-lookup"><span data-stu-id="7547a-768">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="7547a-769">Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="7547a-769">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="7547a-770">`RouteTokenTransformerConvention` est une convention de modèle d’application qui :</span><span class="sxs-lookup"><span data-stu-id="7547a-770">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="7547a-771">Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.</span><span class="sxs-lookup"><span data-stu-id="7547a-771">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7547a-772">Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.</span><span class="sxs-lookup"><span data-stu-id="7547a-772">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="7547a-773">`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7547a-773">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="7547a-774">Routes multiples</span><span class="sxs-lookup"><span data-stu-id="7547a-774">Multiple Routes</span></span>

<span data-ttu-id="7547a-775">Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action.</span><span class="sxs-lookup"><span data-stu-id="7547a-775">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7547a-776">L’utilisation la plus courante de ceci est d’imiter le comportement de la *route conventionnelle par défaut*, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7547a-776">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="7547a-777">Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux se combine avec chacun des attributs de route sur les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-777">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="7547a-778">Quand plusieurs attributs de route (qui implémentent `IActionConstraint`) sont placés sur une action, chaque contrainte d’action se combine avec le modèle de route de l’attribut qui l’a défini.</span><span class="sxs-lookup"><span data-stu-id="7547a-778">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="7547a-779">Si utiliser plusieurs routes sur des actions peut paître puissant, il est préférable de conserver l’espace d’URL de votre application simple et bien définie.</span><span class="sxs-lookup"><span data-stu-id="7547a-779">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="7547a-780">Utilisez plusieurs routes sur les actions seulement là où c’est nécessaire, par exemple pour prendre en charge des clients existants.</span><span class="sxs-lookup"><span data-stu-id="7547a-780">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7547a-781">Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7547a-781">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7547a-782">Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.</span><span class="sxs-lookup"><span data-stu-id="7547a-782">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="7547a-783">Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="7547a-783">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7547a-784">Attributs de route personnalisés avec `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="7547a-784">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="7547a-785">Tous les attributs de route fournis dans le framework (`[Route(...)]`, `[HttpGet(...)]`, etc.) implémentent l’interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="7547a-785">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="7547a-786">MVC recherche les attributs sur les classes de contrôleur et les méthodes d’action quand l’application démarre et utilise ceux qui implémentent `IRouteTemplateProvider` pour créer l’ensemble initial des routes.</span><span class="sxs-lookup"><span data-stu-id="7547a-786">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7547a-787">Vous pouvez implémenter `IRouteTemplateProvider` pour définir vos propres attributs de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-787">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="7547a-788">Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :</span><span class="sxs-lookup"><span data-stu-id="7547a-788">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="7547a-789">L’attribut de l’exemple ci-dessus définit automatiquement `Template` sur `"api/[controller]"` quand `[MyApiController]` est appliqué.</span><span class="sxs-lookup"><span data-stu-id="7547a-789">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7547a-790">Utilisation d’un modèle d’application pour personnaliser les routes d’attribut</span><span class="sxs-lookup"><span data-stu-id="7547a-790">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="7547a-791">Le *modèle d’application* est un modèle d’objet créé au démarrage avec toutes les métadonnées utilisée par MVC pour router et exécuter vos actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-791">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="7547a-792">Le *modèle d’application* inclut toutes les données collectées à partir des attributs de route (via `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="7547a-792">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="7547a-793">Vous pouvez écrire des *conventions* pour modifier le modèle d’application au moment du démarrage pour personnaliser le comporte du routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-793">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="7547a-794">Cette section montre un exemple simple de personnalisation du routage avec le modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="7547a-794">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7547a-795">Routage mixte : routage conventionnel et routage par attributs</span><span class="sxs-lookup"><span data-stu-id="7547a-795">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7547a-796">Les applications MVC peuvent combiner l’utilisation du routage conventionnel et du routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-796">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7547a-797">Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.</span><span class="sxs-lookup"><span data-stu-id="7547a-797">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7547a-798">Les actions sont routées de façon conventionnelle ou routées par attribut.</span><span class="sxs-lookup"><span data-stu-id="7547a-798">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7547a-799">Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ».</span><span class="sxs-lookup"><span data-stu-id="7547a-799">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7547a-800">Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa.</span><span class="sxs-lookup"><span data-stu-id="7547a-800">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7547a-801">**Tout** attribut de route sur le contrôleur a pour effet que toutes les actions du contrôleur sont routées par attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-801">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-802">Ce qui distingue les deux types de systèmes de routage est le processus appliqué une fois qu’une URL correspond à un modèle de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-802">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="7547a-803">Dans le routage conventionnel, les valeurs de route de la correspondance sont utilisées pour choisir l’action et le contrôleur dans une table de recherche comprenant toutes les actions routées de façon conventionnelle.</span><span class="sxs-lookup"><span data-stu-id="7547a-803">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="7547a-804">Dans le routage par attributs, chaque modèle est déjà associé à une action, et aucune recherche supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7547a-804">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="7547a-805">Segments complexes</span><span class="sxs-lookup"><span data-stu-id="7547a-805">Complex segments</span></span>

<span data-ttu-id="7547a-806">Les segments complexes (par exemple, `[Route("/dog{token}cat")]`), sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande.</span><span class="sxs-lookup"><span data-stu-id="7547a-806">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="7547a-807">Consultez [le code source](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) pour obtenir une description.</span><span class="sxs-lookup"><span data-stu-id="7547a-807">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="7547a-808">Pour plus d’informations, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="7547a-808">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="7547a-809">Génération des URL</span><span class="sxs-lookup"><span data-stu-id="7547a-809">URL Generation</span></span>

<span data-ttu-id="7547a-810">Les applications MVC peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions.</span><span class="sxs-lookup"><span data-stu-id="7547a-810">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7547a-811">La génération d’URL élimine le codage en dur des URL, ce qui rend votre code plus robuste et plus facile à maintenir.</span><span class="sxs-lookup"><span data-stu-id="7547a-811">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="7547a-812">Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre seulement les principes de base du fonctionnement de la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-812">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="7547a-813">Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7547a-813">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7547a-814">L’interface `IUrlHelper` est l’élément d’infrastructure sous-jacent entre MVC et le routage pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-814">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7547a-815">Vous pouvez trouver une instance de `IUrlHelper` disponible via la propriété `Url` dans les composants controllers, views et view.</span><span class="sxs-lookup"><span data-stu-id="7547a-815">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7547a-816">Dans cet exemple, l’interface `IUrlHelper` est utilisée via la propriété `Controller.Url` pour générer une URL vers une autre action.</span><span class="sxs-lookup"><span data-stu-id="7547a-816">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7547a-817">Si l’application utilise la route conventionnelle par défaut, la valeur de la variable `url` est la chaîne de chemin d’URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="7547a-817">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7547a-818">Ce chemin d’URL est créé par le routage en combinant les valeurs de route de la requête en cours (valeurs ambiantes) avec les valeurs passées à `Url.Action`, et en remplaçant les valeurs dans le modèle de route :</span><span class="sxs-lookup"><span data-stu-id="7547a-818">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7547a-819">La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes.</span><span class="sxs-lookup"><span data-stu-id="7547a-819">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7547a-820">Un paramètre de route qui n’a pas de valeur peut utiliser une valeur par défaut s’il en a une, ou il peut être ignoré s’il est facultatif (comme dans le cas de `id` dans cet exemple).</span><span class="sxs-lookup"><span data-stu-id="7547a-820">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="7547a-821">La génération d’URL échoue si un paramètre de route obligatoire n’a pas de valeur correspondante.</span><span class="sxs-lookup"><span data-stu-id="7547a-821">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7547a-822">Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.</span><span class="sxs-lookup"><span data-stu-id="7547a-822">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7547a-823">L’exemple de `Url.Action` ci-dessus suppose un routage conventionnel, mais la génération d’URL fonctionne de façon similaire avec le routage par attributs, même si les concepts sont différents.</span><span class="sxs-lookup"><span data-stu-id="7547a-823">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="7547a-824">Avec le routage conventionnel, les valeurs de route sont utilisées pour développer un modèle, et les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle : ceci fonctionne car les URL mises en correspondance par le routage adhèrent à une *convention*.</span><span class="sxs-lookup"><span data-stu-id="7547a-824">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="7547a-825">Dans le routage par attributs, les valeurs de route pour `controller` et `action` ne sont pas autorisées à apparaître dans le modèle : au lieu de cela, elles sont utilisées pour rechercher le modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="7547a-825">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="7547a-826">Cet exemple utilise le routage par attributs :</span><span class="sxs-lookup"><span data-stu-id="7547a-826">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="7547a-827">MVC génère une table de recherche de toutes les actions routées par attributs et recherche une correspondance avec les valeurs de `controller` et de `action` pour sélectionner le modèle de route à utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-827">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="7547a-828">Dans l’exemple ci-dessus, `custom/url/to/destination` est généré.</span><span class="sxs-lookup"><span data-stu-id="7547a-828">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7547a-829">Génération des URL par nom d’action</span><span class="sxs-lookup"><span data-stu-id="7547a-829">Generating URLs by action name</span></span>

<span data-ttu-id="7547a-830">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7547a-830">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="7547a-831">`Action`) et toutes les surcharges associées sont tous basés sur cette idée que vous voulez spécifier ce à quoi vous liez en spécifiant un nom de contrôleur et un nom d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-831">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-832">Lorsque vous utilisez `Url.Action`, les valeurs d’itinéraire actuelles pour `controller` et `action` sont spécifiées pour vous-la valeur de `controller` et `action` font partie des *valeurs ambiantes* **et** des *valeurs*.</span><span class="sxs-lookup"><span data-stu-id="7547a-832">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="7547a-833">La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et de `controller`, et génère un chemin d’URL qui route vers l’action actuelle.</span><span class="sxs-lookup"><span data-stu-id="7547a-833">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="7547a-834">Le routage essaye d’utiliser les valeurs dans les valeurs ambiantes pour renseigner les informations que vous n’avez pas fournies lors de la génération d’une URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-834">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="7547a-835">En utilisant une route comme `{a}/{b}/{c}/{d}` et les valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`, le routage a suffisamment d’informations pour générer une URL sans aucune autre valeur supplémentaire, car tous les paramètres de route ont une valeur.</span><span class="sxs-lookup"><span data-stu-id="7547a-835">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="7547a-836">Si vous avez ajouté la valeur `{ d = Donovan }`, la valeur `{ d = David }` est ignorée, et le chemin d’URL généré est `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="7547a-836">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="7547a-837">Les chemins d’URL sont hiérarchiques.</span><span class="sxs-lookup"><span data-stu-id="7547a-837">URL paths are hierarchical.</span></span> <span data-ttu-id="7547a-838">Dans l’exemple ci-dessus, si vous avez ajouté la valeur `{ c = Cheryl }`, les deux valeurs `{ c = Carol, d = David }` sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="7547a-838">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="7547a-839">Dans ce cas nous n’avons plus de valeur pour `d` et la génération d’URL échoue.</span><span class="sxs-lookup"><span data-stu-id="7547a-839">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="7547a-840">Vous devez spécifier la valeur souhaitée de `c` et de `d`.</span><span class="sxs-lookup"><span data-stu-id="7547a-840">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="7547a-841">Vous vous attendez peut-être à rencontrer ce problème avec la route par défaut (`{controller}/{action}/{id?}`), mais vous rencontrerez rarement ce comportement en pratique, car `Url.Action` spécifie toujours explicitement une valeur pour `controller` et pour `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-841">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="7547a-842">Les surcharges plus longues de `Url.Action` prennent également un objet supplémentaire de *valeurs de route*  pour fournir des valeurs pour les paramètres de route autres que `controller` et `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-842">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7547a-843">Vous verrez ceci plus couramment utilisé avec un `id` comme `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7547a-843">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7547a-844">Par convention, l’objet de *valeurs de route* objet est généralement un objet de type anonyme, mais il peut également être un `IDictionary<>` ou un *objet .NET traditionnel*.</span><span class="sxs-lookup"><span data-stu-id="7547a-844">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="7547a-845">Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="7547a-845">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="7547a-846">Pour créer une URL absolue, utilisez une surcharge qui accepte un `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="7547a-846">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="7547a-847">Génération des URL par route</span><span class="sxs-lookup"><span data-stu-id="7547a-847">Generating URLs by route</span></span>

<span data-ttu-id="7547a-848">Le code ci-dessus a montré la génération d’une URL en passant le nom du contrôleur et le nom de l’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-848">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7547a-849">`IUrlHelper` fournit également la famille de méthodes `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="7547a-849">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="7547a-850">Ces méthodes sont similaires à `Url.Action`, mais elle ne copient pas les valeurs actuelles de `action` et de `controller` vers les valeurs de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-850">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7547a-851">L’utilisation la plus courante est de spécifier un nom de route pour utiliser une route spécifique pour générer l’URL, généralement *sans* spécifier un nom de contrôleur ou d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-851">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="7547a-852">Génération des URL en HTML</span><span class="sxs-lookup"><span data-stu-id="7547a-852">Generating URLs in HTML</span></span>

<span data-ttu-id="7547a-853">`IHtmlHelper` fournit les méthodes `HtmlHelper``Html.BeginForm` et `Html.ActionLink` pour générer respectivement les éléments `<form>` et `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7547a-853">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7547a-854">Ces méthodes utilisent la méthode `Url.Action` pour générer une URL et ils acceptent les arguments similaires.</span><span class="sxs-lookup"><span data-stu-id="7547a-854">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7547a-855">Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.</span><span class="sxs-lookup"><span data-stu-id="7547a-855">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7547a-856">Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7547a-856">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7547a-857">Ils utilisent tous les deux `IUrlHelper` pour leur implémentation.</span><span class="sxs-lookup"><span data-stu-id="7547a-857">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7547a-858">Pour plus d’informations, consultez [Utilisation des formulaires](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="7547a-858">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="7547a-859">Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="7547a-859">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="7547a-860">Génération des URL dans les résultats d’action</span><span class="sxs-lookup"><span data-stu-id="7547a-860">Generating URLS in Action Results</span></span>

<span data-ttu-id="7547a-861">Les exemples précédents ont montré l’utilisation de `IUrlHelper` dans un contrôleur, alors que l’utilisation la plus courante dans un contrôleur est de générer une URL dans le cadre d’un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-861">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7547a-862">Les classes de base `ControllerBase` et `Controller` fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action.</span><span class="sxs-lookup"><span data-stu-id="7547a-862">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7547a-863">Une utilisation typique est de rediriger après acceptation de l’entrée utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7547a-863">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="7547a-864">Les méthodes de fabrique de résultats d’action suivent un modèle similaire aux méthodes sur `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="7547a-864">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7547a-865">Cas spécial pour les routes conventionnelles dédiées</span><span class="sxs-lookup"><span data-stu-id="7547a-865">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7547a-866">Le routage conventionnel peut utiliser un type spécial de définition de route appelé *route conventionnelle dédiée*.</span><span class="sxs-lookup"><span data-stu-id="7547a-866">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="7547a-867">Dans l’exemple ci-dessous, la route nommée `blog` est une route conventionnelle dédiée.</span><span class="sxs-lookup"><span data-stu-id="7547a-867">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7547a-868">En utilisant ces définitions de route, `Url.Action("Index", "Home")` génère le chemin d’URL `/` avec la route `default`, mais pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="7547a-868">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="7547a-869">Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7547a-869">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7547a-870">Les routes conventionnelles dédiées s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de route correspondant qui empêche la route d’être « trop globale » avec la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-870">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="7547a-871">Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route.</span><span class="sxs-lookup"><span data-stu-id="7547a-871">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7547a-872">Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7547a-872">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7547a-873">La génération d’URL avec `blog` échoue, car les valeurs `{ controller = Home, action = Index }` ne correspondent pas à `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-873">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7547a-874">Le routage essaye alors d’utiliser `default`, ce qui réussit.</span><span class="sxs-lookup"><span data-stu-id="7547a-874">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7547a-875">Domaines</span><span class="sxs-lookup"><span data-stu-id="7547a-875">Areas</span></span>

<span data-ttu-id="7547a-876">Les [zones](areas.md) sont une fonctionnalité de MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms de routage distinct (pour les actions de contrôleur) et d’une structure de dossiers (pour les vues).</span><span class="sxs-lookup"><span data-stu-id="7547a-876">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="7547a-877">L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, pour autant qu’ils soient dans des *zones* différentes.</span><span class="sxs-lookup"><span data-stu-id="7547a-877">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="7547a-878">L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`.</span><span class="sxs-lookup"><span data-stu-id="7547a-878">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7547a-879">Cette section explique comment le routage interagit avec les zones. Pour plus d’informations sur l’utilisation des zones avec des vues, consultez [Zones](areas.md).</span><span class="sxs-lookup"><span data-stu-id="7547a-879">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="7547a-880">L’exemple suivant configure MVC pour utiliser la route conventionnelle par défaut et une *route de zone* pour une zone nommée `Blog` :</span><span class="sxs-lookup"><span data-stu-id="7547a-880">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7547a-881">Lors de la mise en correspondance d’un chemin d’URL comme `/Manage/Users/AddUser`, la première route produit les valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-881">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7547a-882">La valeur de route `area` est produite par une valeur par défaut pour `area` ; en fait, la route créée par `MapAreaRoute` est équivalente à la suivante :</span><span class="sxs-lookup"><span data-stu-id="7547a-882">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="7547a-883">`MapAreaRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7547a-883">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7547a-884">La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-884">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="7547a-885">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="7547a-885">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7547a-886">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="7547a-886">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7547a-887">Avec l’exemple ci-dessus, les valeurs de route seraient mises en correspondance avec l’action suivante :</span><span class="sxs-lookup"><span data-stu-id="7547a-887">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7547a-888">`AreaAttribute` est ce qui indique qu’un contrôleur fait partie d’une zone : nous disons que ce contrôleur est dans la zone `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7547a-888">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="7547a-889">Les contrôleurs sans attribut `[Area]` ne sont membres d’aucune zone et ne sont **pas** trouvés en correspondance quand la valeur de route `area` est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-889">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7547a-890">Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7547a-890">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="7547a-891">L’espace de noms de chaque contrôleur est montré ici par souci d’exhaustivité, sans quoi les contrôleurs auraient un conflit de noms et généreraient une erreur de compilateur.</span><span class="sxs-lookup"><span data-stu-id="7547a-891">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="7547a-892">Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.</span><span class="sxs-lookup"><span data-stu-id="7547a-892">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7547a-893">Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`.</span><span class="sxs-lookup"><span data-stu-id="7547a-893">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7547a-894">Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.</span><span class="sxs-lookup"><span data-stu-id="7547a-894">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-895">En termes de mise en correspondance avec *aucune valeur*, l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.</span><span class="sxs-lookup"><span data-stu-id="7547a-895">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7547a-896">Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que *valeur ambiante*, que le routage peut utiliser pour la génération d’URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-896">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="7547a-897">Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="7547a-897">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="7547a-898">Présentation d’IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7547a-898">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="7547a-899">Cette section est une présentation détaillée des mécanismes internes du framework et de la façon dont MVC choisit une action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="7547a-899">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="7547a-900">Une application classique n’a pas besoin d’une `IActionConstraint` personnalisée.</span><span class="sxs-lookup"><span data-stu-id="7547a-900">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="7547a-901">Vous avez probablement déjà utilisé `IActionConstraint` même si vous n’êtes pas familiarisé avec l’interface.</span><span class="sxs-lookup"><span data-stu-id="7547a-901">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="7547a-902">L’attribut `[HttpGet]` et les attributs `[Http-VERB]` similaires implémentent `IActionConstraint` de façon à limiter l’exécution d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="7547a-902">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="7547a-903">Dans l’hypothèse d’une route conventionnelle par défaut, le chemin d’URL `/Products/Edit` produirait les valeurs `{ controller = Products, action = Edit }`, qui seraient en correspondance avec **les deux** actions montrées ici.</span><span class="sxs-lookup"><span data-stu-id="7547a-903">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="7547a-904">Dans la terminologie `IActionConstraint`, nous pouvons dire que ces deux actions sont considérées comme candidates, car elles correspondent toutes deux aux données de la route.</span><span class="sxs-lookup"><span data-stu-id="7547a-904">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="7547a-905">Quand `HttpGetAttribute` s’exécute, il indique que *Edit()* est une correspondance pour *GET* et qu’il n’est une correspondance pour aucun des autres verbes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-905">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="7547a-906">L’action `Edit(...)` n’a aucune contrainte définie et elle ne correspond donc à aucun verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-906">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="7547a-907">Par conséquent, dans l’hypothèse d’un `POST`, seul `Edit(...)` est en correspondance.</span><span class="sxs-lookup"><span data-stu-id="7547a-907">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="7547a-908">Cependant, pour un `GET`, les deux actions peuvent néanmoins être en correspondance, mais une action avec `IActionConstraint` est toujours considérée comme étant *meilleure*  qu’une action sans.</span><span class="sxs-lookup"><span data-stu-id="7547a-908">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="7547a-909">Par conséquent, comme `Edit()` a `[HttpGet]`, elle est considérée comme étant plus spécifique et est sélectionnée si les deux actions peuvent correspondre.</span><span class="sxs-lookup"><span data-stu-id="7547a-909">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="7547a-910">D’un point de vue conceptuel, `IActionConstraint` est une forme de *surcharge*, mais au lieu de surcharger des méthodes portant le même nom, elle surcharge entre des actions qui correspondent à la même URL.</span><span class="sxs-lookup"><span data-stu-id="7547a-910">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="7547a-911">Le routage par attributs utilise également `IActionConstraint` et peut aboutir à des actions de différents contrôleurs, toutes considérées comme candidates.</span><span class="sxs-lookup"><span data-stu-id="7547a-911">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="7547a-912">Implémentation d’IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7547a-912">Implementing IActionConstraint</span></span>

<span data-ttu-id="7547a-913">La façon la plus simple d’implémenter une `IActionConstraint` est de créer une classe dérivée de `System.Attribute` et de la placer sur vos actions et vos contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="7547a-913">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="7547a-914">MVC découvre automatiquement les `IActionConstraint` qui sont appliquées en tant qu’attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-914">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="7547a-915">Vous pouvez utiliser le modèle d’application pour appliquer des contraintes, et il s’agit probablement de l’approche la plus souple, car elle vous permet de programmer des méta-informations indiquant comment elles sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="7547a-915">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="7547a-916">Dans l’exemple suivant, une contrainte choisit une action en fonction de l' *indicatif du pays* à partir des données de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7547a-916">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="7547a-917">[Exemple complet sur GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="7547a-917">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="7547a-918">Vous êtes responsable de l’implémentation de la méthode `Accept` et du choix d’un « ordre » pour l’exécution de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="7547a-918">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="7547a-919">Dans ce cas, la méthode `Accept` retourne `true` pour indiquer que l’action est une correspondance quand la valeur de la route `country` correspond à la valeur.</span><span class="sxs-lookup"><span data-stu-id="7547a-919">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="7547a-920">Ceci diffère d’un `RouteValueAttribute`, car elle permet à défaut d’appliquer une action sans attributs.</span><span class="sxs-lookup"><span data-stu-id="7547a-920">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="7547a-921">L’exemple montre que si vous définissez une action `en-US`, un code pays comme `fr-FR` passe à défaut à un contrôleur plus générique auquel `[CountrySpecific(...)]` n’est pas appliqué.</span><span class="sxs-lookup"><span data-stu-id="7547a-921">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="7547a-922">La propriété `Order` décide de *l’étape* dont la contrainte fait partie.</span><span class="sxs-lookup"><span data-stu-id="7547a-922">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="7547a-923">Les contraintes d’action s’exécutent dans des groupes en fonction de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7547a-923">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="7547a-924">Par exemple, tous les attributs de méthode HTTP fournis par le framework utilisent la même valeur pour `Order`, de façon à ce qu’ils s’exécutent dans la même étape.</span><span class="sxs-lookup"><span data-stu-id="7547a-924">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="7547a-925">Vous pouvez avoir autant d’étapes que nécessaire pour implémenter les stratégies souhaitées.</span><span class="sxs-lookup"><span data-stu-id="7547a-925">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="7547a-926">Pour décider d’une valeur pour `Order`, déterminez si votre contrainte doit ou non être appliquée avant les méthodes HTTP.</span><span class="sxs-lookup"><span data-stu-id="7547a-926">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="7547a-927">Les nombres les plus petits s’exécutent en premier.</span><span class="sxs-lookup"><span data-stu-id="7547a-927">Lower numbers run first.</span></span>

::: moniker-end
