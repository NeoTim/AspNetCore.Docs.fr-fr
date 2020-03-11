---
title: Exemple de cookie SameSite ASP.NET Core MVC 2,1
author: rick-anderson
description: Exemple de cookie SameSite ASP.NET Core MVC 2,1
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667747"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="290b6-103">Exemple de cookie SameSite ASP.NET Core MVC 2,1</span><span class="sxs-lookup"><span data-stu-id="290b6-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="290b6-104">ASP.NET Core 2,1 dispose d’une prise en charge intégrée de l’attribut [SameSite](https://www.owasp.org/index.php/SameSite) , mais il a été écrit dans la norme d’origine.</span><span class="sxs-lookup"><span data-stu-id="290b6-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="290b6-105">Le [comportement corrigé](https://github.com/dotnet/aspnetcore/issues/8212) a modifié la signification de `SameSite.None` pour émettre l’attribut sameSite avec une valeur de `None`, au lieu de ne pas émettre la valeur du tout.</span><span class="sxs-lookup"><span data-stu-id="290b6-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="290b6-106">Si vous ne souhaitez pas émettre la valeur, vous pouvez définir la propriété `SameSite` sur un cookie sur-1.</span><span class="sxs-lookup"><span data-stu-id="290b6-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="sampleCode"></a><span data-ttu-id="290b6-107">Écriture de l’attribut SameSite</span><span class="sxs-lookup"><span data-stu-id="290b6-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="290b6-108">Voici un exemple d’écriture d’un attribut SameSite sur un cookie :</span><span class="sxs-lookup"><span data-stu-id="290b6-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="290b6-109">Définition des cookies d’authentification de cookie et d’état de session</span><span class="sxs-lookup"><span data-stu-id="290b6-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="290b6-110">L’authentification par cookie, l’état de session et [divers autres composants](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) définissent leurs options sameSite via des options de cookie, par exemple</span><span class="sxs-lookup"><span data-stu-id="290b6-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="290b6-111">Dans le code précédent, l’authentification de cookie et l’état de session définissent leur attribut sameSite sur `None`, en émettant l’attribut avec une valeur de `None` et en définissant également l’attribut Secure sur true.</span><span class="sxs-lookup"><span data-stu-id="290b6-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="290b6-112">Exécution de l'exemple</span><span class="sxs-lookup"><span data-stu-id="290b6-112">Run the sample</span></span>

<span data-ttu-id="290b6-113">Si vous exécutez l' [exemple de projet](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), chargez votre débogueur de navigateur sur la page initiale et utilisez-le pour afficher la collection de cookies pour le site.</span><span class="sxs-lookup"><span data-stu-id="290b6-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="290b6-114">Pour ce faire, dans Edge et chrome, appuyez sur `F12` sélectionnez l’onglet `Application` et cliquez sur l’URL du site sous l’option `Cookies` dans la section `Storage`.</span><span class="sxs-lookup"><span data-stu-id="290b6-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Liste des cookies du débogueur de navigateur](BrowserDebugger.png)

<span data-ttu-id="290b6-116">Vous pouvez voir à partir de l’image ci-dessus que le cookie créé par l’exemple lorsque vous cliquez sur le bouton « créer un cookie SameSite » a une valeur d’attribut SameSite de `Lax`, ce qui correspond à la valeur définie dans l' [exemple de code](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="290b6-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="interception"></a><span data-ttu-id="290b6-117">Interception des cookies</span><span class="sxs-lookup"><span data-stu-id="290b6-117">Intercepting cookies</span></span>

<span data-ttu-id="290b6-118">Afin d’intercepter les cookies, pour ajuster la valeur None en fonction de sa prise en charge dans l’agent Browser de l’utilisateur, vous devez utiliser l’intergiciel `CookiePolicy`.</span><span class="sxs-lookup"><span data-stu-id="290b6-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="290b6-119">Celui-ci doit être placé dans le pipeline de requête HTTP **avant** tous les composants qui écrivent des cookies et configurés dans `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="290b6-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="290b6-120">Pour l’insérer dans le pipeline, utilisez `app.UseCookiePolicy()` dans la méthode `Configure(IApplicationBuilder, IHostingEnvironment)` dans [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="290b6-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="290b6-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="290b6-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="290b6-122">Ensuite, dans la `ConfigureServices(IServiceCollection services)` configurez la stratégie de cookie pour appeler une classe d’assistance lorsque des cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="290b6-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="290b6-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="290b6-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="290b6-124">La fonction d’assistance `CheckSameSite(HttpContext, CookieOptions)`:</span><span class="sxs-lookup"><span data-stu-id="290b6-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="290b6-125">Est appelé lorsque les cookies sont ajoutés à la demande ou supprimés de la requête.</span><span class="sxs-lookup"><span data-stu-id="290b6-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="290b6-126">Vérifie si la propriété `SameSite` est définie sur `None`.</span><span class="sxs-lookup"><span data-stu-id="290b6-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="290b6-127">Si `SameSite` est défini sur `None` et que l’agent utilisateur actuel ne prend pas en charge la valeur de l’attribut None.</span><span class="sxs-lookup"><span data-stu-id="290b6-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="290b6-128">La vérification s’effectue à l’aide de la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="290b6-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="290b6-129">Définit `SameSite` pour ne pas émettre la valeur en affectant à la propriété la valeur `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="290b6-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="290b6-130">Ciblage .NET Framework</span><span class="sxs-lookup"><span data-stu-id="290b6-130">Targeting .NET Framework</span></span>

<span data-ttu-id="290b6-131">ASP.NET Core et System. Web (ASP.NET Classic) ont des implémentations indépendantes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="290b6-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="290b6-132">Les correctifs de SameSite KB pour les .NET Framework ne sont pas requis si vous utilisez ASP.NET Core et que la version minimale requise de l’infrastructure System. Web SameSite (.NET 4.7.2) s’appliquent à ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="290b6-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="290b6-133">ASP.NET Core sur .NET requiert la mise à jour des dépendances de package NuGet pour récupérer les correctifs appropriés.</span><span class="sxs-lookup"><span data-stu-id="290b6-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="290b6-134">Pour obtenir les modifications de ASP.NET Core pour .NET Framework Vérifiez que vous disposez d’une référence directe aux packages et versions corrigés (2.1.14 ou versions ultérieures 2,1).</span><span class="sxs-lookup"><span data-stu-id="290b6-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="290b6-135">Informations complémentaires</span><span class="sxs-lookup"><span data-stu-id="290b6-135">More Information</span></span>
 
<span data-ttu-id="290b6-136">[Mises à jour Chrome](https://www.chromium.org/updates/same-site)
[ASP.NET Core Documentation SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core annonce de modification SameSite 2,1](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="290b6-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>