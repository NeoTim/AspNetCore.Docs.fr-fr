---
title: Résoudre les problèmes liés à la localisation ASP.NET Core
author: hishamco
description: Découvrez comment diagnostiquer les problèmes liés à la localisation dans les applications ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660376"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="fc345-103">Résoudre les problèmes liés à la localisation ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc345-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="fc345-104">Par [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="fc345-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="fc345-105">Cet article fournit des instructions sur la façon de diagnostiquer les problèmes de localisation des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc345-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="fc345-106">Problèmes de configuration de la localisation</span><span class="sxs-lookup"><span data-stu-id="fc345-106">Localization configuration issues</span></span>

<span data-ttu-id="fc345-107">**Ordre de l’Intergiciel (middleware) de localisation**</span><span class="sxs-lookup"><span data-stu-id="fc345-107">**Localization middleware order**</span></span>  
<span data-ttu-id="fc345-108">Il est possible que la localisation de l’application ne s’effectue pas car le middleware de localisation ne respecte pas l’ordre prévu.</span><span class="sxs-lookup"><span data-stu-id="fc345-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="fc345-109">Pour résoudre ce problème, vérifiez que le middleware de localisation est inscrit avant le middleware MVC.</span><span class="sxs-lookup"><span data-stu-id="fc345-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="fc345-110">Sinon, le middleware de localisation n’est pas appliqué.</span><span class="sxs-lookup"><span data-stu-id="fc345-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="fc345-111">**Le chemin des ressources de localisation est introuvable**</span><span class="sxs-lookup"><span data-stu-id="fc345-111">**Localization resources path not found**</span></span>

<span data-ttu-id="fc345-112">**Les cultures prises en charge dans RequestCultureProvider ne correspondent pas à celles inscrites**</span><span class="sxs-lookup"><span data-stu-id="fc345-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="fc345-113">Problèmes liés au nommage des fichiers de ressources</span><span class="sxs-lookup"><span data-stu-id="fc345-113">Resource file naming issues</span></span>

<span data-ttu-id="fc345-114">ASP.NET Core a des règles prédéfinies et des instructions concernant le nommage des fichiers de ressources de localisation. Elles sont décrites en détail [ici](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="fc345-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="fc345-115">Ressources manquantes</span><span class="sxs-lookup"><span data-stu-id="fc345-115">Missing resources</span></span>

<span data-ttu-id="fc345-116">Les causes courantes de ressources introuvables sont notamment les suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc345-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="fc345-117">Les noms des ressources sont mal orthographiés dans le fichier `resx` ou la requête à l’outil de localisation.</span><span class="sxs-lookup"><span data-stu-id="fc345-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="fc345-118">La ressource est manquante dans le `resx` pour certaines langues, mais elle existe dans d’autres.</span><span class="sxs-lookup"><span data-stu-id="fc345-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="fc345-119">Si le problème persiste, vérifiez les messages du journal de localisation (qui se trouvent au niveau du journal `Debug`) pour obtenir plus de détails sur les ressources manquantes.</span><span class="sxs-lookup"><span data-stu-id="fc345-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="fc345-120">_**Indicateur :** Lorsque vous utilisez `CookieRequestCultureProvider`, vérifiez que les guillemets simples ne sont pas utilisés avec les cultures à l’intérieur de la valeur du cookie de localisation. Par exemple, `c='en-UK'|uic='en-US'` est une valeur de cookie non valide, tandis que `c=en-UK|uic=en-US` est un valide._</span><span class="sxs-lookup"><span data-stu-id="fc345-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="fc345-121">Problèmes liés aux ressources et aux bibliothèques de classes</span><span class="sxs-lookup"><span data-stu-id="fc345-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="fc345-122">ASP.NET Core fournit par défaut un moyen de permettre aux bibliothèques de classes de rechercher leurs fichiers de ressources par le biais de [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="fc345-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="fc345-123">Les problèmes courants liés aux bibliothèques de classes sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="fc345-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="fc345-124">Le `ResourceLocationAttribute` manquant dans une classe de bibliothèque empêche `ResourceManagerStringLocalizerFactory` de découvrir les ressources.</span><span class="sxs-lookup"><span data-stu-id="fc345-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="fc345-125">Nommage des fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="fc345-125">Resource file naming.</span></span> <span data-ttu-id="fc345-126">Pour plus d’informations, consultez la section [Problèmes liés au nommage des fichiers de ressources](#resource-file-naming-issues).</span><span class="sxs-lookup"><span data-stu-id="fc345-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="fc345-127">Changement de l’espace de noms racine de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="fc345-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="fc345-128">Pour plus d’informations, consultez la section [Problèmes liés à l’espace de noms racine](#root-namespace-issues).</span><span class="sxs-lookup"><span data-stu-id="fc345-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="fc345-129">CustomRequestCultureProvider ne fonctionne pas comme prévu</span><span class="sxs-lookup"><span data-stu-id="fc345-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="fc345-130">La classe `RequestLocalizationOptions` possède trois fournisseurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="fc345-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="fc345-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) vous permet de personnaliser la façon dont la culture de localisation est fournie dans votre application.</span><span class="sxs-lookup"><span data-stu-id="fc345-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="fc345-132">`CustomRequestCultureProvider` est utilisé quand les fournisseurs par défaut ne répondent pas à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="fc345-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="fc345-133">Une raison courante pour laquelle un fournisseur personnalisé ne fonctionne pas correctement est qu’il n’est pas le premier fournisseur dans la liste `RequestCultureProviders`.</span><span class="sxs-lookup"><span data-stu-id="fc345-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="fc345-134">Pour résoudre ce problème :</span><span class="sxs-lookup"><span data-stu-id="fc345-134">To resolve this issue:</span></span>

- <span data-ttu-id="fc345-135">Insérez le fournisseur personnalisé à la position 0 dans la liste `RequestCultureProviders`, comme suit :</span><span class="sxs-lookup"><span data-stu-id="fc345-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="fc345-136">Utilisez la méthode d’extension `AddInitialRequestCultureProvider` pour définir le fournisseur personnalisé comme fournisseur initial.</span><span class="sxs-lookup"><span data-stu-id="fc345-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="fc345-137">Problèmes liés à l’espace de noms racine</span><span class="sxs-lookup"><span data-stu-id="fc345-137">Root Namespace issues</span></span>

<span data-ttu-id="fc345-138">Si l’espace de noms racine d’un assembly est différent du nom de l’assembly, la localisation ne fonctionne pas par défaut.</span><span class="sxs-lookup"><span data-stu-id="fc345-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="fc345-139">Pour éviter ce problème, utilisez [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), qui est décrit en détail [ici](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="fc345-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="fc345-140">Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide.</span><span class="sxs-lookup"><span data-stu-id="fc345-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="fc345-141">Par exemple `my-project-name.csproj` utilise l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` conduisant à cette erreur.</span><span class="sxs-lookup"><span data-stu-id="fc345-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="fc345-142">Ressources et action de génération</span><span class="sxs-lookup"><span data-stu-id="fc345-142">Resources & Build Action</span></span>

<span data-ttu-id="fc345-143">Si vous utilisez des fichiers de ressources pour la localisation, il est important qu’ils disposent d’une action de génération appropriée.</span><span class="sxs-lookup"><span data-stu-id="fc345-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="fc345-144">Ils doivent être une **ressource incorporée**, sans quoi `ResourceStringLocalizer` n’est pas en mesure de trouver ces ressources.</span><span class="sxs-lookup"><span data-stu-id="fc345-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
