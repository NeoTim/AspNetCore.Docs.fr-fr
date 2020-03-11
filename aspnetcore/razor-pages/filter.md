---
title: Méthodes de filtre pour les pages Razor dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment créer des méthodes de filtre pour les pages Razor dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660754"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="bd923-103">Méthodes de filtre pour les pages Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd923-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd923-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd923-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bd923-105">Les filtres de page Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent aux pages Razor d’exécuter du code avant et après l’exécution d’un gestionnaire de page Razor.</span><span class="sxs-lookup"><span data-stu-id="bd923-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="bd923-106">Les filtres de page Razor sont similaires aux [filtres d’action MVC ASP.NET Core](xref:mvc/controllers/filters#action-filters), à la différence près qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de pages individuelles.</span><span class="sxs-lookup"><span data-stu-id="bd923-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="bd923-107">Les filtres de page Razor :</span><span class="sxs-lookup"><span data-stu-id="bd923-107">Razor Page filters:</span></span>

* <span data-ttu-id="bd923-108">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="bd923-109">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="bd923-110">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="bd923-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="bd923-111">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="bd923-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="bd923-112">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="bd923-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="bd923-113">Peut avoir des dépendances de constructeur remplies par [injection de dépendance](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="bd923-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="bd923-114">Pour plus d’informations, consultez [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) et [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="bd923-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="bd923-115">Alors que les constructeurs de page et l’intergiciel (middleware) activent l’exécution de code personnalisé avant l’exécution d’une méthode de gestionnaire, seuls les filtres de page Razor permettent d’accéder à <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> et à la page.</span><span class="sxs-lookup"><span data-stu-id="bd923-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="bd923-116">L’intergiciel a accès au `HttpContext`, mais pas au « contexte de page ».</span><span class="sxs-lookup"><span data-stu-id="bd923-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="bd923-117">Les filtres ont un paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> dérivé, qui fournit l’accès à `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="bd923-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="bd923-118">Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.</span><span class="sxs-lookup"><span data-stu-id="bd923-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="bd923-119">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd923-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bd923-120">Les filtres de page Razor fournissent les méthodes suivantes que vous pouvez appliquer globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="bd923-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="bd923-121">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-121">Synchronous methods:</span></span>

  * <span data-ttu-id="bd923-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="bd923-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="bd923-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="bd923-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="bd923-125">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="bd923-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="bd923-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="bd923-128">Implémentez la version synchrone **ou bien** la version asynchrone d’une interface de filtre, mais **pas** les deux.</span><span class="sxs-lookup"><span data-stu-id="bd923-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="bd923-129">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="bd923-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="bd923-130">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="bd923-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="bd923-131">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="bd923-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="bd923-132">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="bd923-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="bd923-133">Implémenter des filtres de page Razor globalement</span><span class="sxs-lookup"><span data-stu-id="bd923-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="bd923-134">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="bd923-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="bd923-135">Dans le code précédent, `ProcessUserAgent.Write` est un code fourni par l’utilisateur qui fonctionne avec la chaîne de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="bd923-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="bd923-136">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="bd923-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="bd923-137">Le code suivant appelle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour appliquer les `SampleAsyncPageFilter` à des pages uniquement dans */movies*:</span><span class="sxs-lookup"><span data-stu-id="bd923-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="bd923-138">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="bd923-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="bd923-139">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="bd923-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="bd923-140">Implémenter des filtres de page Razor en substituant les méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="bd923-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="bd923-141">Le code suivant remplace les filtres de page Razor asynchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="bd923-142">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="bd923-142">Implement a filter attribute</span></span>

<span data-ttu-id="bd923-143">Le filtre intégré <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtre basé sur les attributs peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="bd923-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="bd923-144">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="bd923-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="bd923-145">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="bd923-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="bd923-146">Utilisez un outil tel que les outils de développement du navigateur pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="bd923-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="bd923-147">Sous **en-têtes de réponse**, `author: Rick` s’affiche.</span><span class="sxs-lookup"><span data-stu-id="bd923-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="bd923-148">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="bd923-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="bd923-149">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="bd923-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="bd923-150">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="bd923-150">Authorize filter attribute</span></span>

<span data-ttu-id="bd923-151">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="bd923-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd923-152">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd923-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bd923-153">Les filtres de page Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent aux pages Razor d’exécuter du code avant et après l’exécution d’un gestionnaire de page Razor.</span><span class="sxs-lookup"><span data-stu-id="bd923-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="bd923-154">Les filtres de page Razor sont similaires aux [filtres d’action MVC ASP.NET Core](xref:mvc/controllers/filters#action-filters), à la différence près qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de pages individuelles.</span><span class="sxs-lookup"><span data-stu-id="bd923-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="bd923-155">Les filtres de page Razor :</span><span class="sxs-lookup"><span data-stu-id="bd923-155">Razor Page filters:</span></span>

* <span data-ttu-id="bd923-156">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="bd923-157">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="bd923-158">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="bd923-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="bd923-159">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="bd923-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="bd923-160">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="bd923-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="bd923-161">Le code peut être exécuté avant l’exécution d’une méthode de gestionnaire à l’aide du constructeur de page ou d’un middleware (intergiciel), mais seuls les filtres de page Razor ont accès à [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="bd923-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="bd923-162">Les filtres ont un paramètre dérivé de [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) qui fournit un accès à `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="bd923-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="bd923-163">Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.</span><span class="sxs-lookup"><span data-stu-id="bd923-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="bd923-164">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd923-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bd923-165">Les filtres de page Razor fournissent les méthodes suivantes que vous pouvez appliquer globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="bd923-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="bd923-166">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-166">Synchronous methods:</span></span>

  * <span data-ttu-id="bd923-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="bd923-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="bd923-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="bd923-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="bd923-170">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="bd923-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="bd923-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="bd923-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="bd923-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="bd923-173">Implémentez la version synchrone **ou bien** la version asynchrone d’une interface de filtre, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="bd923-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="bd923-174">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="bd923-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="bd923-175">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="bd923-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="bd923-176">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="bd923-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="bd923-177">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="bd923-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="bd923-178">Implémenter des filtres de page Razor globalement</span><span class="sxs-lookup"><span data-stu-id="bd923-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="bd923-179">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="bd923-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="bd923-180">Dans le code précédent, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="bd923-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="bd923-181">Il est utilisé dans l’exemple pour fournir des informations de trace pour l’application.</span><span class="sxs-lookup"><span data-stu-id="bd923-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="bd923-182">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="bd923-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="bd923-183">Le code suivant montre l’intégralité de la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="bd923-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="bd923-184">Le code suivant appelle `AddFolderApplicationModelConvention` pour appliquer le filtre `SampleAsyncPageFilter` uniquement aux pages dans */subFolder* :</span><span class="sxs-lookup"><span data-stu-id="bd923-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="bd923-185">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="bd923-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="bd923-186">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="bd923-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="bd923-187">Implémenter des filtres de page Razor en substituant les méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="bd923-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="bd923-188">Le code suivant se substitue aux filtres de page Razor synchrones :</span><span class="sxs-lookup"><span data-stu-id="bd923-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="bd923-189">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="bd923-189">Implement a filter attribute</span></span>

<span data-ttu-id="bd923-190">Le filtre intégré [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) basé sur un attribut peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="bd923-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="bd923-191">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="bd923-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="bd923-192">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="bd923-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="bd923-193">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="bd923-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="bd923-194">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="bd923-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="bd923-195">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="bd923-195">Authorize filter attribute</span></span>

<span data-ttu-id="bd923-196">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="bd923-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
