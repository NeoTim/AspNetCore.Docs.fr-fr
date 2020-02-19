---
title: ASP.NET Core Blazor la globalisation et la localisation
author: guardrex
description: Découvrez comment rendre des composants Razor accessibles aux utilisateurs dans plusieurs cultures et langages.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453267"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="1a6e4-103">ASP.NET Core Blazor la globalisation et la localisation</span><span class="sxs-lookup"><span data-stu-id="1a6e4-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="1a6e4-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1a6e4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1a6e4-105">Les composants Razor peuvent être rendus accessibles aux utilisateurs dans plusieurs cultures et langages.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="1a6e4-106">Les scénarios de globalisation et de localisation .NET suivants sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="1a6e4-107">. Système de ressources du réseau</span><span class="sxs-lookup"><span data-stu-id="1a6e4-107">.NET's resources system</span></span>
* <span data-ttu-id="1a6e4-108">Mise en forme des nombres et des dates spécifiques à la culture</span><span class="sxs-lookup"><span data-stu-id="1a6e4-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="1a6e4-109">Un ensemble limité de scénarios de localisation de ASP.NET Core est actuellement pris en charge :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="1a6e4-110">`IStringLocalizer<>` *est pris en charge* dans les applications Blazor.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="1a6e4-111">la localisation des annotations de données `IHtmlLocalizer<>`, `IViewLocalizer<>`et est ASP.NET Core les scénarios MVC et **non pris en charge** dans les applications Blazor.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="1a6e4-112">Pour plus d’informations, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="1a6e4-113">Globalisation</span><span class="sxs-lookup"><span data-stu-id="1a6e4-113">Globalization</span></span>

<span data-ttu-id="1a6e4-114">les fonctionnalités de `@bind` de Blazoreffectuent des mises en forme et analysent les valeurs pour l’affichage en fonction de la culture actuelle de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-114">Blazor's `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="1a6e4-115">La culture actuelle est accessible à partir de la propriété <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="1a6e4-116">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) est utilisé pour les types de champ suivants (`<input type="{TYPE}" />`) :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="1a6e4-117">Les types de champ précédents :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-117">The preceding field types:</span></span>

* <span data-ttu-id="1a6e4-118">Sont affichés à l’aide des règles de mise en forme appropriées basées sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="1a6e4-119">Ne peut pas contenir de texte de forme libre.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="1a6e4-120">Fournir des caractéristiques d’interaction de l’utilisateur en fonction de l’implémentation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="1a6e4-121">Les types de champs suivants ont des exigences de mise en forme spécifiques et ne sont pas actuellement pris en charge par Blazor, car ils ne sont pas pris en charge par tous les principaux navigateurs :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="1a6e4-122">`@bind` prend en charge le paramètre `@bind:culture` pour fournir une <xref:System.Globalization.CultureInfo?displayProperty=fullName> pour l’analyse et la mise en forme d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="1a6e4-123">La spécification d’une culture n’est pas recommandée lors de l’utilisation des types de champ `date` et `number`.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="1a6e4-124">`date` et `number` disposent d’une prise en charge intégrée Blazor qui fournit la culture requise.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="1a6e4-125">Localisation</span><span class="sxs-lookup"><span data-stu-id="1a6e4-125">Localization</span></span>

<span data-ttu-id="1a6e4-126">les applications Blazor Server sont localisées à l’aide de l' [intergiciel (middleware](xref:fundamentals/localization#localization-middleware)) de localisation.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-126">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="1a6e4-127">L’intergiciel sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="1a6e4-128">La culture peut être définie à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="1a6e4-129">Cookies</span><span class="sxs-lookup"><span data-stu-id="1a6e4-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="1a6e4-130">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="1a6e4-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="1a6e4-131">Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="1a6e4-132">Configurer l’éditeur de liens pour l’internationalisation (Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="1a6e4-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="1a6e4-133">Par défaut, la configuration de l’éditeur de liens de Blazorpour les applications webassembly Blazor supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="1a6e4-134">Pour plus d’informations et de conseils sur le contrôle du comportement de l’éditeur de liens, consultez <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="1a6e4-135">Cookies</span><span class="sxs-lookup"><span data-stu-id="1a6e4-135">Cookies</span></span>

<span data-ttu-id="1a6e4-136">Un cookie de culture de localisation peut conserver la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="1a6e4-137">Le cookie est créé par la méthode `OnGet` de la page hôte de l’application (*pages/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="1a6e4-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="1a6e4-138">L’intergiciel (middleware) de localisation lit le cookie sur les demandes suivantes pour définir la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="1a6e4-139">L’utilisation d’un cookie garantit que la connexion WebSocket peut propager correctement la culture.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="1a6e4-140">Si les schémas de localisation sont basés sur le chemin d’URL ou la chaîne de requête, il est possible que le schéma ne soit pas en mesure de fonctionner avec les WebSockets, et donc de ne pas conserver la culture.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="1a6e4-141">Par conséquent, l’utilisation d’un cookie de culture de localisation est l’approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="1a6e4-142">Toute technique peut être utilisée pour assigner une culture si la culture est rendue persistante dans un cookie de localisation.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="1a6e4-143">Si l’application a déjà un schéma de localisation établi pour les ASP.NET Core côté serveur, continuez à utiliser l’infrastructure de localisation existante de l’application et à définir le cookie de la culture de localisation dans le schéma de l’application.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="1a6e4-144">L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lu par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="1a6e4-145">Créez un fichier *pages/Host. cshtml. cs* avec le contenu suivant dans l’application Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="1a6e4-146">La localisation est gérée par l’application dans la séquence d’événements suivante :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="1a6e4-147">Le navigateur envoie une requête HTTP initiale à l’application.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="1a6e4-148">La culture est affectée par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="1a6e4-149">La méthode `OnGet` dans *_Host. cshtml. cs* rend persistante la culture dans un cookie dans le cadre de la réponse.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="1a6e4-150">Le navigateur ouvre une connexion WebSocket pour créer une session Blazor Server interactive.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="1a6e4-151">L’intergiciel de localisation lit le cookie et assigne la culture.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="1a6e4-152">La session du serveur de Blazor commence par la culture correcte.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="1a6e4-153">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="1a6e4-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="1a6e4-154">Pour fournir une interface utilisateur permettant à un utilisateur de sélectionner une culture, il est recommandé d’effectuer une *approche basée sur la redirection* .</span><span class="sxs-lookup"><span data-stu-id="1a6e4-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="1a6e4-155">Le processus est similaire à ce qui se produit dans une application Web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée&mdash;l’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="1a6e4-156">L’application conserve la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="1a6e4-157">Le contrôleur définit la culture sélectionnée de l’utilisateur dans un cookie et redirige l’utilisateur vers l’URI d’origine.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="1a6e4-158">Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="1a6e4-159">Utilisez le résultat de l’action `LocalRedirect` pour empêcher les attaques de redirection ouvertes.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="1a6e4-160">Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="1a6e4-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="1a6e4-161">Le composant suivant montre un exemple d’exécution de la redirection initiale lorsque l’utilisateur sélectionne une culture :</span><span class="sxs-lookup"><span data-stu-id="1a6e4-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="1a6e4-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1a6e4-162">Additional resources</span></span>

* <xref:fundamentals/localization>
