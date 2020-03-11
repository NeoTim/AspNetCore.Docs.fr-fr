---
title: Liaison de données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment fonctionne la liaison de modèle avec ASP.NET Core, et comment personnaliser son comportement.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 19580768679f30131683717792252c03aade68f9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666277"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="bed7e-103">Liaison de données dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bed7e-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bed7e-104">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="bed7e-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="bed7e-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bed7e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="bed7e-106">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-106">What is Model binding</span></span>

<span data-ttu-id="bed7e-107">Les contrôleurs et Razor Pages utilisent des données provenant de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="bed7e-108">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="bed7e-109">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="bed7e-110">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="bed7e-110">Model binding automates this process.</span></span> <span data-ttu-id="bed7e-111">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="bed7e-111">The model binding system:</span></span>

* <span data-ttu-id="bed7e-112">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="bed7e-113">Fournit les données aux contrôleurs et à Razor Pages dans les paramètres de méthode et les propriétés publiques</span><span class="sxs-lookup"><span data-stu-id="bed7e-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="bed7e-114">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="bed7e-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="bed7e-115">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="bed7e-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="bed7e-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="bed7e-116">Example</span></span>

<span data-ttu-id="bed7e-117">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="bed7e-118">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="bed7e-119">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="bed7e-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="bed7e-120">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="bed7e-121">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="bed7e-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="bed7e-122">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="bed7e-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="bed7e-123">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="bed7e-124">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="bed7e-125">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="bed7e-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="bed7e-126">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="bed7e-127">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="bed7e-128">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="bed7e-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="bed7e-129">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="bed7e-130">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="bed7e-131">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="bed7e-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="bed7e-132">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="bed7e-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="bed7e-133">Cibles</span><span class="sxs-lookup"><span data-stu-id="bed7e-133">Targets</span></span>

<span data-ttu-id="bed7e-134">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="bed7e-135">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="bed7e-136">Paramètres de la méthode de gestionnaire Razor Pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="bed7e-137">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="bed7e-138">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="bed7e-138">[BindProperty] attribute</span></span>

<span data-ttu-id="bed7e-139">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="bed7e-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="bed7e-140">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="bed7e-140">[BindProperties] attribute</span></span>

<span data-ttu-id="bed7e-141">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="bed7e-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="bed7e-142">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="bed7e-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="bed7e-143">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="bed7e-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="bed7e-144">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="bed7e-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="bed7e-145">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="bed7e-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="bed7e-146">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bed7e-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="bed7e-147">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="bed7e-148">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="bed7e-149">Sources</span><span class="sxs-lookup"><span data-stu-id="bed7e-149">Sources</span></span>

<span data-ttu-id="bed7e-150">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="bed7e-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="bed7e-151">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="bed7e-151">Form fields</span></span>
1. <span data-ttu-id="bed7e-152">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="bed7e-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="bed7e-153">Données de routage</span><span class="sxs-lookup"><span data-stu-id="bed7e-153">Route data</span></span>
1. <span data-ttu-id="bed7e-154">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-154">Query string parameters</span></span>
1. <span data-ttu-id="bed7e-155">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="bed7e-155">Uploaded files</span></span>

<span data-ttu-id="bed7e-156">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="bed7e-157">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="bed7e-157">There are a few exceptions:</span></span>

* <span data-ttu-id="bed7e-158">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="bed7e-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="bed7e-159">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="bed7e-160">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="bed7e-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="bed7e-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="bed7e-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="bed7e-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="bed7e-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) : obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="bed7e-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="bed7e-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="bed7e-166">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="bed7e-166">These attributes:</span></span>

* <span data-ttu-id="bed7e-167">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="bed7e-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="bed7e-168">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="bed7e-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="bed7e-169">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="bed7e-170">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="bed7e-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="bed7e-171">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="bed7e-171">[FromBody] attribute</span></span>

<span data-ttu-id="bed7e-172">Appliquez l’attribut `[FromBody]` à un paramètre pour remplir ses propriétés à partir du corps d’une requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="bed7e-173">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="bed7e-174">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="bed7e-175">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="bed7e-176">Par exemple, l’action `Create` suivante spécifie que son paramètre `pet` est renseigné à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="bed7e-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="bed7e-177">La classe `Pet` spécifie que sa propriété `Breed` est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="bed7e-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="bed7e-178">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="bed7e-178">In the preceding example:</span></span>

* <span data-ttu-id="bed7e-179">L’attribut `[FromQuery]` est ignoré.</span><span class="sxs-lookup"><span data-stu-id="bed7e-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="bed7e-180">La propriété `Breed` n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="bed7e-181">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="bed7e-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="bed7e-182">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la propriété `Breed`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="bed7e-183">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="bed7e-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="bed7e-184">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour lier d’autres paramètres de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="bed7e-185">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-185">Additional sources</span></span>

<span data-ttu-id="bed7e-186">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="bed7e-187">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="bed7e-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="bed7e-188">Par exemple, vous pouvez obtenir des données provenant de cookies ou de l’état de session.</span><span class="sxs-lookup"><span data-stu-id="bed7e-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="bed7e-189">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="bed7e-189">To get data from a new source:</span></span>

* <span data-ttu-id="bed7e-190">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="bed7e-191">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="bed7e-192">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="bed7e-193">L’exemple d’application comprend un exemple de [fournisseur de valeurs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) et de [fabrique](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs), qui permet de récupérer les valeurs provenant des cookies.</span><span class="sxs-lookup"><span data-stu-id="bed7e-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="bed7e-194">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="bed7e-195">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="bed7e-196">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new CookieValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="bed7e-197">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-197">No source for a model property</span></span>

<span data-ttu-id="bed7e-198">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="bed7e-199">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="bed7e-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="bed7e-200">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="bed7e-201">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="bed7e-202">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="bed7e-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="bed7e-203">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="bed7e-204">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="bed7e-205">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l’attribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="bed7e-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="bed7e-206">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="bed7e-207">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="bed7e-208">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="bed7e-208">Type conversion errors</span></span>

<span data-ttu-id="bed7e-209">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="bed7e-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="bed7e-210">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="bed7e-211">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="bed7e-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="bed7e-212">Dans une page Razor Pages, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="bed7e-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="bed7e-213">La validation côté client intercepte la plupart des données incorrectes qui sont envoyées à un formulaire Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bed7e-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="bed7e-214">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="bed7e-215">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="bed7e-216">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="bed7e-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="bed7e-217">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="bed7e-218">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="bed7e-219">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="bed7e-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="bed7e-220">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="bed7e-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="bed7e-221">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="bed7e-222">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="bed7e-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="bed7e-223">Types simples</span><span class="sxs-lookup"><span data-stu-id="bed7e-223">Simple types</span></span>

<span data-ttu-id="bed7e-224">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="bed7e-225">Booléen</span><span class="sxs-lookup"><span data-stu-id="bed7e-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="bed7e-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="bed7e-227">Char</span><span class="sxs-lookup"><span data-stu-id="bed7e-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="bed7e-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="bed7e-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="bed7e-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="bed7e-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="bed7e-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="bed7e-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="bed7e-231">Double</span><span class="sxs-lookup"><span data-stu-id="bed7e-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="bed7e-232">Enum</span><span class="sxs-lookup"><span data-stu-id="bed7e-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="bed7e-233">Guid</span><span class="sxs-lookup"><span data-stu-id="bed7e-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="bed7e-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="bed7e-235">Unique</span><span class="sxs-lookup"><span data-stu-id="bed7e-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="bed7e-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="bed7e-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="bed7e-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="bed7e-238">Uri</span><span class="sxs-lookup"><span data-stu-id="bed7e-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="bed7e-239">Version</span><span class="sxs-lookup"><span data-stu-id="bed7e-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="bed7e-240">Types complexes</span><span class="sxs-lookup"><span data-stu-id="bed7e-240">Complex types</span></span>

<span data-ttu-id="bed7e-241">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="bed7e-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="bed7e-242">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="bed7e-243">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="bed7e-244">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="bed7e-245">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="bed7e-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="bed7e-246">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="bed7e-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="bed7e-247">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="bed7e-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="bed7e-248">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="bed7e-249">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="bed7e-249">Prefix = parameter name</span></span>

<span data-ttu-id="bed7e-250">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="bed7e-251">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="bed7e-252">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="bed7e-253">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="bed7e-253">Prefix = property name</span></span>

<span data-ttu-id="bed7e-254">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="bed7e-255">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bed7e-256">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="bed7e-257">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="bed7e-257">Custom prefix</span></span>

<span data-ttu-id="bed7e-258">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="bed7e-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="bed7e-259">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bed7e-260">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="bed7e-261">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="bed7e-261">Attributes for complex type targets</span></span>

<span data-ttu-id="bed7e-262">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="bed7e-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="bed7e-263">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="bed7e-264">Ils n’affectent pas les formateurs d’entrée, qui traitent les corps de requête JSON et XML postés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="bed7e-265">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="bed7e-266">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="bed7e-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="bed7e-267">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="bed7e-267">[BindRequired] attribute</span></span>

<span data-ttu-id="bed7e-268">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bed7e-269">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="bed7e-270">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="bed7e-271">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="bed7e-271">[BindNever] attribute</span></span>

<span data-ttu-id="bed7e-272">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bed7e-273">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="bed7e-274">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="bed7e-275">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="bed7e-275">[Bind] attribute</span></span>

<span data-ttu-id="bed7e-276">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="bed7e-277">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="bed7e-278">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="bed7e-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="bed7e-279">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="bed7e-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="bed7e-280">Vous pouvez utiliser l’attribut `[Bind]` pour éviter le surpostage dans les scénarios de *création*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="bed7e-281">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="bed7e-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="bed7e-282">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="bed7e-283">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="bed7e-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="bed7e-284">Collections</span><span class="sxs-lookup"><span data-stu-id="bed7e-284">Collections</span></span>

<span data-ttu-id="bed7e-285">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bed7e-286">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bed7e-287">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-287">For example:</span></span>

* <span data-ttu-id="bed7e-288">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="bed7e-289">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="bed7e-290">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="bed7e-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="bed7e-291">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bed7e-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="bed7e-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="bed7e-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="bed7e-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="bed7e-294">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="bed7e-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="bed7e-295">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="bed7e-296">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="bed7e-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="bed7e-297">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-297">Dictionaries</span></span>

<span data-ttu-id="bed7e-298">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bed7e-299">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bed7e-300">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-300">For example:</span></span>

* <span data-ttu-id="bed7e-301">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="bed7e-302">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="bed7e-303">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bed7e-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="bed7e-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="bed7e-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="bed7e-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="bed7e-306">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="bed7e-307">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="bed7e-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="bed7e-308">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="bed7e-309">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="bed7e-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="bed7e-310">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="bed7e-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="bed7e-311">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="bed7e-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="bed7e-312">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="bed7e-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="bed7e-313">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="bed7e-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="bed7e-314">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="bed7e-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="bed7e-315">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="bed7e-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="bed7e-316">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="bed7e-317">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="bed7e-317">Special data types</span></span>

<span data-ttu-id="bed7e-318">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="bed7e-319">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="bed7e-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="bed7e-320">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="bed7e-321">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="bed7e-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="bed7e-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="bed7e-322">CancellationToken</span></span>

<span data-ttu-id="bed7e-323">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="bed7e-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="bed7e-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="bed7e-324">FormCollection</span></span>

<span data-ttu-id="bed7e-325">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="bed7e-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="bed7e-326">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="bed7e-326">Input formatters</span></span>

<span data-ttu-id="bed7e-327">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="bed7e-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="bed7e-328">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="bed7e-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="bed7e-329">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="bed7e-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="bed7e-330">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="bed7e-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="bed7e-331">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bed7e-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="bed7e-332">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="bed7e-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="bed7e-333">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="bed7e-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="bed7e-334">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="bed7e-335">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="bed7e-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="bed7e-336">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="bed7e-337">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="bed7e-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="bed7e-338">Personnaliser la liaison de modèle avec des formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="bed7e-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="bed7e-339">Un formateur d’entrée est entièrement chargé de lire les données dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="bed7e-340">Pour personnaliser ce processus, configurez les API utilisées par le formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="bed7e-341">Cette section décrit comment personnaliser le formateur d’entrée basé sur `System.Text.Json`pour comprendre un type personnalisé nommé `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="bed7e-342">Prenons le modèle suivant, qui contient une propriété de `ObjectId` personnalisée nommée `Id`:</span><span class="sxs-lookup"><span data-stu-id="bed7e-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="bed7e-343">Pour personnaliser le processus de liaison de modèle lors de l’utilisation de `System.Text.Json`, créez une classe dérivée de <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="bed7e-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="bed7e-344">Pour utiliser un convertisseur personnalisé, appliquez l’attribut <xref:System.Text.Json.Serialization.JsonConverterAttribute> au type.</span><span class="sxs-lookup"><span data-stu-id="bed7e-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="bed7e-345">Dans l’exemple suivant, le type de `ObjectId` est configuré avec `ObjectIdConverter` comme convertisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="bed7e-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="bed7e-346">Pour plus d’informations, consultez [Comment écrire des convertisseurs personnalisés](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="bed7e-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="bed7e-347">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="bed7e-348">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="bed7e-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="bed7e-349">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="bed7e-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="bed7e-350">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="bed7e-351">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bed7e-352">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="bed7e-353">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bed7e-354">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="bed7e-355">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="bed7e-355">Custom model binders</span></span>

<span data-ttu-id="bed7e-356">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="bed7e-357">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="bed7e-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="bed7e-358">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="bed7e-358">Manual model binding</span></span> 

<span data-ttu-id="bed7e-359">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bed7e-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="bed7e-360">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="bed7e-361">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="bed7e-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="bed7e-362">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="bed7e-363">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="bed7e-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> utilise des fournisseurs de valeurs pour obtenir des données à partir du corps du formulaire, de la chaîne de requête et des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="bed7e-365">`TryUpdateModelAsync` est généralement :</span><span class="sxs-lookup"><span data-stu-id="bed7e-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="bed7e-366">Utilisé avec les applications Razor Pages et MVC à l’aide de contrôleurs et de vues pour empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="bed7e-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="bed7e-367">Non utilisé avec une API Web, sauf s’il est consommé à partir des données de formulaire, des chaînes de requête et des données de routage.</span><span class="sxs-lookup"><span data-stu-id="bed7e-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="bed7e-368">Les points de terminaison de l’API Web qui utilisent JSON utilisent des [formateurs d’entrée](#input-formatters) pour désérialiser le corps de la requête dans un objet.</span><span class="sxs-lookup"><span data-stu-id="bed7e-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="bed7e-369">Pour plus d’informations, consultez [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="bed7e-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="bed7e-370">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="bed7e-370">[FromServices] attribute</span></span>

<span data-ttu-id="bed7e-371">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="bed7e-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="bed7e-372">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="bed7e-373">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bed7e-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bed7e-374">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bed7e-375">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bed7e-376">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="bed7e-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="bed7e-377">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bed7e-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="bed7e-378">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-378">What is Model binding</span></span>

<span data-ttu-id="bed7e-379">Les contrôleurs et Razor Pages utilisent des données provenant de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="bed7e-380">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="bed7e-381">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="bed7e-382">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="bed7e-382">Model binding automates this process.</span></span> <span data-ttu-id="bed7e-383">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="bed7e-383">The model binding system:</span></span>

* <span data-ttu-id="bed7e-384">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="bed7e-385">Fournit les données aux contrôleurs et à Razor Pages dans les paramètres de méthode et les propriétés publiques</span><span class="sxs-lookup"><span data-stu-id="bed7e-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="bed7e-386">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="bed7e-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="bed7e-387">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="bed7e-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="bed7e-388">Exemple</span><span class="sxs-lookup"><span data-stu-id="bed7e-388">Example</span></span>

<span data-ttu-id="bed7e-389">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="bed7e-390">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="bed7e-391">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="bed7e-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="bed7e-392">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="bed7e-393">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="bed7e-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="bed7e-394">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="bed7e-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="bed7e-395">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="bed7e-396">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="bed7e-397">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="bed7e-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="bed7e-398">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="bed7e-399">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="bed7e-400">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="bed7e-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="bed7e-401">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="bed7e-402">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="bed7e-403">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="bed7e-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="bed7e-404">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="bed7e-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="bed7e-405">Cibles</span><span class="sxs-lookup"><span data-stu-id="bed7e-405">Targets</span></span>

<span data-ttu-id="bed7e-406">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="bed7e-407">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="bed7e-408">Paramètres de la méthode de gestionnaire Razor Pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="bed7e-409">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="bed7e-410">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="bed7e-410">[BindProperty] attribute</span></span>

<span data-ttu-id="bed7e-411">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="bed7e-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="bed7e-412">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="bed7e-412">[BindProperties] attribute</span></span>

<span data-ttu-id="bed7e-413">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="bed7e-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="bed7e-414">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="bed7e-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="bed7e-415">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="bed7e-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="bed7e-416">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="bed7e-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="bed7e-417">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="bed7e-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="bed7e-418">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bed7e-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="bed7e-419">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="bed7e-420">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="bed7e-421">Sources</span><span class="sxs-lookup"><span data-stu-id="bed7e-421">Sources</span></span>

<span data-ttu-id="bed7e-422">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="bed7e-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="bed7e-423">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="bed7e-423">Form fields</span></span>
1. <span data-ttu-id="bed7e-424">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="bed7e-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="bed7e-425">Données de routage</span><span class="sxs-lookup"><span data-stu-id="bed7e-425">Route data</span></span>
1. <span data-ttu-id="bed7e-426">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-426">Query string parameters</span></span>
1. <span data-ttu-id="bed7e-427">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="bed7e-427">Uploaded files</span></span>

<span data-ttu-id="bed7e-428">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="bed7e-429">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="bed7e-429">There are a few exceptions:</span></span>

* <span data-ttu-id="bed7e-430">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="bed7e-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="bed7e-431">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="bed7e-432">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="bed7e-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="bed7e-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="bed7e-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="bed7e-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="bed7e-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) : obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="bed7e-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="bed7e-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="bed7e-438">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="bed7e-438">These attributes:</span></span>

* <span data-ttu-id="bed7e-439">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="bed7e-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="bed7e-440">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="bed7e-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="bed7e-441">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="bed7e-442">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="bed7e-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="bed7e-443">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="bed7e-443">[FromBody] attribute</span></span>

<span data-ttu-id="bed7e-444">Appliquez l’attribut `[FromBody]` à un paramètre pour remplir ses propriétés à partir du corps d’une requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="bed7e-445">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="bed7e-446">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="bed7e-447">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="bed7e-448">Par exemple, l’action `Create` suivante spécifie que son paramètre `pet` est renseigné à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="bed7e-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="bed7e-449">La classe `Pet` spécifie que sa propriété `Breed` est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="bed7e-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="bed7e-450">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="bed7e-450">In the preceding example:</span></span>

* <span data-ttu-id="bed7e-451">L’attribut `[FromQuery]` est ignoré.</span><span class="sxs-lookup"><span data-stu-id="bed7e-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="bed7e-452">La propriété `Breed` n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="bed7e-453">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="bed7e-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="bed7e-454">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la propriété `Breed`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="bed7e-455">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="bed7e-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="bed7e-456">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour lier d’autres paramètres de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="bed7e-457">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-457">Additional sources</span></span>

<span data-ttu-id="bed7e-458">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="bed7e-459">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="bed7e-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="bed7e-460">Par exemple, vous pouvez obtenir des données provenant de cookies ou de l’état de session.</span><span class="sxs-lookup"><span data-stu-id="bed7e-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="bed7e-461">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="bed7e-461">To get data from a new source:</span></span>

* <span data-ttu-id="bed7e-462">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="bed7e-463">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="bed7e-464">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="bed7e-465">L’exemple d’application comprend un exemple de [fournisseur de valeurs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) et de [fabrique](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs), qui permet de récupérer les valeurs provenant des cookies.</span><span class="sxs-lookup"><span data-stu-id="bed7e-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="bed7e-466">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="bed7e-467">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="bed7e-468">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new CookieValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="bed7e-469">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-469">No source for a model property</span></span>

<span data-ttu-id="bed7e-470">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="bed7e-471">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="bed7e-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="bed7e-472">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="bed7e-473">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="bed7e-474">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="bed7e-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="bed7e-475">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="bed7e-476">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="bed7e-477">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l’attribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="bed7e-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="bed7e-478">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="bed7e-479">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="bed7e-480">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="bed7e-480">Type conversion errors</span></span>

<span data-ttu-id="bed7e-481">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="bed7e-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="bed7e-482">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="bed7e-483">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="bed7e-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="bed7e-484">Dans une page Razor Pages, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="bed7e-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="bed7e-485">La validation côté client intercepte la plupart des données incorrectes qui sont envoyées à un formulaire Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bed7e-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="bed7e-486">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="bed7e-487">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="bed7e-488">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="bed7e-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="bed7e-489">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="bed7e-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="bed7e-490">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="bed7e-491">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="bed7e-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="bed7e-492">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="bed7e-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="bed7e-493">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="bed7e-494">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="bed7e-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="bed7e-495">Types simples</span><span class="sxs-lookup"><span data-stu-id="bed7e-495">Simple types</span></span>

<span data-ttu-id="bed7e-496">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="bed7e-497">Booléen</span><span class="sxs-lookup"><span data-stu-id="bed7e-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="bed7e-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="bed7e-499">Char</span><span class="sxs-lookup"><span data-stu-id="bed7e-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="bed7e-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="bed7e-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="bed7e-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="bed7e-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="bed7e-502">Decimal</span><span class="sxs-lookup"><span data-stu-id="bed7e-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="bed7e-503">Double</span><span class="sxs-lookup"><span data-stu-id="bed7e-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="bed7e-504">Enum</span><span class="sxs-lookup"><span data-stu-id="bed7e-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="bed7e-505">Guid</span><span class="sxs-lookup"><span data-stu-id="bed7e-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="bed7e-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="bed7e-507">Unique</span><span class="sxs-lookup"><span data-stu-id="bed7e-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="bed7e-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="bed7e-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="bed7e-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="bed7e-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="bed7e-510">Uri</span><span class="sxs-lookup"><span data-stu-id="bed7e-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="bed7e-511">Version</span><span class="sxs-lookup"><span data-stu-id="bed7e-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="bed7e-512">Types complexes</span><span class="sxs-lookup"><span data-stu-id="bed7e-512">Complex types</span></span>

<span data-ttu-id="bed7e-513">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="bed7e-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="bed7e-514">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="bed7e-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="bed7e-515">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="bed7e-516">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="bed7e-517">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="bed7e-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="bed7e-518">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="bed7e-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="bed7e-519">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="bed7e-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="bed7e-520">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="bed7e-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="bed7e-521">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="bed7e-521">Prefix = parameter name</span></span>

<span data-ttu-id="bed7e-522">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="bed7e-523">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="bed7e-524">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="bed7e-525">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="bed7e-525">Prefix = property name</span></span>

<span data-ttu-id="bed7e-526">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="bed7e-527">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bed7e-528">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="bed7e-529">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="bed7e-529">Custom prefix</span></span>

<span data-ttu-id="bed7e-530">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="bed7e-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="bed7e-531">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="bed7e-532">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="bed7e-533">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="bed7e-533">Attributes for complex type targets</span></span>

<span data-ttu-id="bed7e-534">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="bed7e-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="bed7e-535">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="bed7e-536">Ils n’affectent pas les formateurs d’entrée, qui traitent les corps de requête JSON et XML postés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="bed7e-537">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="bed7e-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="bed7e-538">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="bed7e-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="bed7e-539">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="bed7e-539">[BindRequired] attribute</span></span>

<span data-ttu-id="bed7e-540">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bed7e-541">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="bed7e-542">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="bed7e-543">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="bed7e-543">[BindNever] attribute</span></span>

<span data-ttu-id="bed7e-544">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="bed7e-545">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="bed7e-546">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="bed7e-547">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="bed7e-547">[Bind] attribute</span></span>

<span data-ttu-id="bed7e-548">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="bed7e-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="bed7e-549">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="bed7e-550">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="bed7e-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="bed7e-551">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="bed7e-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="bed7e-552">Vous pouvez utiliser l’attribut `[Bind]` pour éviter le surpostage dans les scénarios de *création*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="bed7e-553">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="bed7e-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="bed7e-554">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="bed7e-555">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="bed7e-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="bed7e-556">Collections</span><span class="sxs-lookup"><span data-stu-id="bed7e-556">Collections</span></span>

<span data-ttu-id="bed7e-557">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bed7e-558">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bed7e-559">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-559">For example:</span></span>

* <span data-ttu-id="bed7e-560">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="bed7e-561">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-561">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="bed7e-562">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="bed7e-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="bed7e-563">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bed7e-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="bed7e-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="bed7e-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="bed7e-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="bed7e-566">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="bed7e-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="bed7e-567">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="bed7e-568">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="bed7e-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="bed7e-569">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-569">Dictionaries</span></span>

<span data-ttu-id="bed7e-570">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="bed7e-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="bed7e-571">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="bed7e-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="bed7e-572">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-572">For example:</span></span>

* <span data-ttu-id="bed7e-573">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="bed7e-574">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="bed7e-574">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="bed7e-575">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="bed7e-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="bed7e-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="bed7e-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="bed7e-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="bed7e-578">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="bed7e-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="bed7e-579">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="bed7e-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="bed7e-580">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="bed7e-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="bed7e-581">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="bed7e-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="bed7e-582">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="bed7e-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="bed7e-583">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="bed7e-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="bed7e-584">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="bed7e-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="bed7e-585">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="bed7e-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="bed7e-586">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="bed7e-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="bed7e-587">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="bed7e-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="bed7e-588">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="bed7e-589">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="bed7e-589">Special data types</span></span>

<span data-ttu-id="bed7e-590">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="bed7e-591">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="bed7e-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="bed7e-592">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="bed7e-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="bed7e-593">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="bed7e-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="bed7e-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="bed7e-594">CancellationToken</span></span>

<span data-ttu-id="bed7e-595">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="bed7e-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="bed7e-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="bed7e-596">FormCollection</span></span>

<span data-ttu-id="bed7e-597">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="bed7e-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="bed7e-598">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="bed7e-598">Input formatters</span></span>

<span data-ttu-id="bed7e-599">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="bed7e-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="bed7e-600">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="bed7e-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="bed7e-601">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="bed7e-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="bed7e-602">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="bed7e-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="bed7e-603">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="bed7e-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="bed7e-604">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="bed7e-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="bed7e-605">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="bed7e-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="bed7e-606">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="bed7e-607">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="bed7e-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="bed7e-608">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="bed7e-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="bed7e-609">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="bed7e-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="bed7e-610">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="bed7e-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="bed7e-611">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="bed7e-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="bed7e-612">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="bed7e-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="bed7e-613">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="bed7e-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="bed7e-614">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bed7e-615">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="bed7e-616">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bed7e-617">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="bed7e-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="bed7e-618">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="bed7e-618">Custom model binders</span></span>

<span data-ttu-id="bed7e-619">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="bed7e-620">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="bed7e-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="bed7e-621">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="bed7e-621">Manual model binding</span></span>

<span data-ttu-id="bed7e-622">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bed7e-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="bed7e-623">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="bed7e-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="bed7e-624">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="bed7e-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="bed7e-625">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="bed7e-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="bed7e-626">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="bed7e-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="bed7e-627">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="bed7e-627">[FromServices] attribute</span></span>

<span data-ttu-id="bed7e-628">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="bed7e-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="bed7e-629">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="bed7e-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="bed7e-630">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bed7e-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bed7e-631">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="bed7e-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bed7e-632">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bed7e-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
