---
title: Version de compatibilité pour ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment la classe Startup dans ASP.NET Core configure des services et le pipeline de requête de l’application.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: 35e3b6acba2bc9a0b863bd6d1e96365328b5f169
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256159"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="53ace-103">Version de compatibilité pour ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="53ace-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="53ace-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53ace-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="53ace-105">La <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> méthode est une absence d’opération pour les applications ASP.net Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="53ace-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="53ace-106">Autrement dit, l' `SetCompatibilityVersion` appel avec n’importe <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> quelle valeur n’a aucun impact sur l’application.</span><span class="sxs-lookup"><span data-stu-id="53ace-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="53ace-107">La version mineure suivante de ASP.net Core peut fournir une `CompatibilityVersion` nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="53ace-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="53ace-108">`CompatibilityVersion`les valeurs `Version_2_0` sont marquées `[Obsolete(...)]`. `Version_2_2`</span><span class="sxs-lookup"><span data-stu-id="53ace-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="53ace-109">Consultez [rupture des modifications d’API dans anti-contrefaçon, cors, diagnostics, MVC et routage](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="53ace-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="53ace-110">Cette liste comprend les modifications avec rupture pour les commutateurs de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="53ace-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="53ace-111">Pour voir comment `SetCompatibilityVersion` fonctionne avec les applications ASP.net Core 2. x, sélectionnez la [version ASP.net Core 2,2 de cet article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="53ace-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53ace-112">La <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> méthode permet à un ASP.net Core application 2. x d’accepter ou de refuser les modifications de comportement potentiellement en rupture introduites dans ASP.net Core MVC 2,1 ou 2,2.</span><span class="sxs-lookup"><span data-stu-id="53ace-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="53ace-113">Ces changements de comportement potentiellement cassants concernent en général la façon dont le sous-système MVC se comporte et la façon dont **votre code** est appelé par le runtime.</span><span class="sxs-lookup"><span data-stu-id="53ace-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="53ace-114">En acceptant, vous obtenez le comportement le plus récent et le comportement à long terme d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53ace-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="53ace-115">Le code suivant définit le mode de compatibilité sur ASP.NET Core 2.2 :</span><span class="sxs-lookup"><span data-stu-id="53ace-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="53ace-116">Nous vous recommandons de tester votre application avec la version la plus récente (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="53ace-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="53ace-117">Nous pensons que la plupart des applications ne connaîtront pas de changements de comportement cassants avec la version la plus récente.</span><span class="sxs-lookup"><span data-stu-id="53ace-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="53ace-118">Les applications qui `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` appellent sont protégées contre les changements de comportement potentiellement inaltérables introduits dans les versions de ASP.net Core 2.1/2.2 Mvc.</span><span class="sxs-lookup"><span data-stu-id="53ace-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="53ace-119">Cette protection :</span><span class="sxs-lookup"><span data-stu-id="53ace-119">This protection:</span></span>

* <span data-ttu-id="53ace-120">Ne s’applique pas à tous les changements de 2.1 et ultérieur. Elle est destinée aux changements de comportement potentiellement cassants du runtime ASP.NET Core dans le sous-système MVC.</span><span class="sxs-lookup"><span data-stu-id="53ace-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="53ace-121">Ne s’étend pas à ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="53ace-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="53ace-122">La compatibilité par défaut pour les applications ASP.net Core 2,1 et 2,2 qui n' `SetCompatibilityVersion` appellent pas est 2,0.</span><span class="sxs-lookup"><span data-stu-id="53ace-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="53ace-123">Autrement dit, ne pas appeler `SetCompatibilityVersion` revient à appeler `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="53ace-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="53ace-124">Le code suivant définit le mode de compatibilité sur ASP.NET Core 2.2, sauf pour les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="53ace-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="53ace-125">Pour les applications qui rencontrent des changements de comportement cassants, l’utilisation des commutateurs de compatibilité appropriés :</span><span class="sxs-lookup"><span data-stu-id="53ace-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="53ace-126">Vous permet d’utiliser la dernière version et de refuser des changements de comportement cassants spécifiques.</span><span class="sxs-lookup"><span data-stu-id="53ace-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="53ace-127">Vous laisse le temps de mettre à jour votre application pour qu’elle fonctionne avec les derniers changements.</span><span class="sxs-lookup"><span data-stu-id="53ace-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="53ace-128">La documentation <xref:Microsoft.AspNetCore.Mvc.MvcOptions> explique clairement ce qui a changé et pourquoi ces changements représentent une amélioration pour la plupart des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="53ace-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="53ace-129">Avec ASP.NET Core 3,0, les anciens comportements pris en charge par les commutateurs de compatibilité ont été supprimés.</span><span class="sxs-lookup"><span data-stu-id="53ace-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="53ace-130">Nous pensons que ce sont des changements positifs, qui vont bénéficier à presque tous les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="53ace-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="53ace-131">En introduisant ces modifications dans 2,1 et 2,2, la plupart des applications peuvent bénéficier de la mise à jour, tandis que d’autres ont du temps.</span><span class="sxs-lookup"><span data-stu-id="53ace-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end