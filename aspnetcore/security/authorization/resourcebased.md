---
title: Autorisation basée sur les ressources dans ASP.NET Core
author: scottaddie
description: Apprenez à implémenter l’autorisation basée sur les ressources dans une application ASP.NET Core lorsqu’un attribut Authorize ne suffit pas.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: acc931da1be0940fac72b0aabe07ab17ca7e63bd
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660001"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="6b3ce-103">Autorisation basée sur les ressources dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b3ce-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="6b3ce-104">La stratégie d’autorisation dépend de la ressource faisant l’objet d’un accès.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="6b3ce-105">Prenons l’exemple d’un document qui a une propriété auteur.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-105">Consider a document that has an author property.</span></span> <span data-ttu-id="6b3ce-106">Seul l’auteur est autorisé à mettre à jour le document.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="6b3ce-107">Par conséquent, le document doit être récupéré à partir du magasin de données avant que l’évaluation de l’autorisation puisse se produire.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="6b3ce-108">L’évaluation d’attribut se produit avant la liaison de données et avant l’exécution du gestionnaire de page ou de l’action qui charge le document.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="6b3ce-109">Pour ces raisons, l’autorisation déclarative avec un attribut `[Authorize]` n’est pas suffisante.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="6b3ce-110">Au lieu de cela, vous pouvez appeler une méthode d’autorisation personnalisée &mdash;a style appelé *autorisation impérative*.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="6b3ce-111">[Affichez ou téléchargez un exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6b3ce-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="6b3ce-112">[Affichez ou téléchargez un exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6b3ce-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="6b3ce-113">[Affichez ou téléchargez un exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6b3ce-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="6b3ce-114">[Créer une application ASP.net core avec des données utilisateur protégées par autorisation](xref:security/authorization/secure-data) contient un exemple d’application qui utilise l’autorisation basée sur les ressources.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="6b3ce-115">Utiliser l’autorisation impérative</span><span class="sxs-lookup"><span data-stu-id="6b3ce-115">Use imperative authorization</span></span>

<span data-ttu-id="6b3ce-116">L’autorisation est implémentée en tant que service [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) et est inscrite dans la collection de services au sein de la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="6b3ce-117">Le service est rendu disponible via l' [injection de dépendances](xref:fundamentals/dependency-injection) aux gestionnaires de pages ou aux actions.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="6b3ce-118">`IAuthorizationService` a deux surcharges de méthode `AuthorizeAsync` : l’une acceptant la ressource et le nom de la stratégie, l’autre acceptant la ressource et une liste de spécifications à évaluer.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="6b3ce-119">Dans l’exemple suivant, la ressource à sécuriser est chargée dans un objet `Document` personnalisé.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="6b3ce-120">Une surcharge de `AuthorizeAsync` est appelée pour déterminer si l’utilisateur actuel est autorisé à modifier le document fourni.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="6b3ce-121">Une stratégie d’autorisation « EditPolicy » personnalisée est prise en compte dans la décision.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="6b3ce-122">Consultez [autorisation basée sur une stratégie personnalisée](xref:security/authorization/policies) pour plus d’informations sur la création de stratégies d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="6b3ce-123">Les exemples de code suivants supposent que l’authentification s’exécute et définit la propriété `User`.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="6b3ce-124">Écrire un gestionnaire basé sur des ressources</span><span class="sxs-lookup"><span data-stu-id="6b3ce-124">Write a resource-based handler</span></span>

<span data-ttu-id="6b3ce-125">L’écriture d’un gestionnaire pour l’autorisation basée sur les ressources n’est pas très différente de celle de l' [écriture d’un gestionnaire de spécifications brutes](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="6b3ce-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="6b3ce-126">Créez une classe d’exigence personnalisée et implémentez une classe de gestionnaire des spécifications.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="6b3ce-127">Pour plus d’informations sur la création d’une classe d’exigence, consultez [spécifications](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="6b3ce-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="6b3ce-128">La classe de gestionnaire spécifie la spécification et le type de ressource.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="6b3ce-129">Par exemple, un gestionnaire utilisant une `SameAuthorRequirement` et une ressource de `Document` suit :</span><span class="sxs-lookup"><span data-stu-id="6b3ce-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="6b3ce-130">Dans l’exemple précédent, imaginez que `SameAuthorRequirement` est un cas spécial d’une classe `SpecificAuthorRequirement` plus générique.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="6b3ce-131">La classe `SpecificAuthorRequirement` (non affichée) contient une propriété `Name` représentant le nom de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="6b3ce-132">La propriété `Name` peut avoir la valeur de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="6b3ce-133">Inscrire la spécification et le gestionnaire dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b3ce-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="6b3ce-134">Exigences opérationnelles</span><span class="sxs-lookup"><span data-stu-id="6b3ce-134">Operational requirements</span></span>

<span data-ttu-id="6b3ce-135">Si vous prenez des décisions en fonction des résultats des opérations CRUD (création, lecture, mise à jour, suppression), utilisez la classe d’assistance [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="6b3ce-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="6b3ce-136">Cette classe vous permet d’écrire un gestionnaire unique au lieu d’une classe individuelle pour chaque type d’opération.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="6b3ce-137">Pour l’utiliser, fournissez des noms d’opérations :</span><span class="sxs-lookup"><span data-stu-id="6b3ce-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="6b3ce-138">Le gestionnaire est implémenté comme suit, à l’aide d’une `OperationAuthorizationRequirement` exigence et d’une ressource de `Document` :</span><span class="sxs-lookup"><span data-stu-id="6b3ce-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="6b3ce-139">Le gestionnaire précédent valide l’opération à l’aide de la ressource, de l’identité de l’utilisateur et de la propriété `Name` de l’exigence.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="6b3ce-140">Défi et interdisent avec un gestionnaire de ressources opérationnelles</span><span class="sxs-lookup"><span data-stu-id="6b3ce-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="6b3ce-141">Cette section montre comment le problème et les résultats de l’action interdire sont traités et comment les problèmes et les interdisent diffèrent.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="6b3ce-142">Pour appeler un gestionnaire de ressources opérationnelles, spécifiez l’opération lors de l’appel de `AuthorizeAsync` dans votre gestionnaire de page ou action.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="6b3ce-143">L’exemple suivant détermine si l’utilisateur authentifié est autorisé à afficher le document fourni.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="6b3ce-144">Les exemples de code suivants supposent que l’authentification s’exécute et définit la propriété `User`.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="6b3ce-145">Si l’autorisation est établie, la page d’affichage du document est retournée.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="6b3ce-146">Si l’autorisation échoue mais que l’utilisateur est authentifié, retourner `ForbidResult` informe tout intergiciel d’authentification qui a échoué.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="6b3ce-147">Une `ChallengeResult` est retournée lorsque l’authentification doit être effectuée.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="6b3ce-148">Pour les clients de navigateur interactifs, il peut être approprié de rediriger l’utilisateur vers une page de connexion.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="6b3ce-149">Si l’autorisation est réussie, la vue du document est retournée.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="6b3ce-150">Si l’autorisation échoue, le fait de retourner `ChallengeResult` informe l’intergiciel (middleware) d’authentification que l’autorisation a échoué et l’intergiciel (middleware) peut prendre la réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="6b3ce-151">Une réponse appropriée peut retourner un code d’état 401 ou 403.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="6b3ce-152">Pour les clients de navigateur interactifs, cela peut signifier que vous redirigez l’utilisateur vers une page de connexion.</span><span class="sxs-lookup"><span data-stu-id="6b3ce-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
