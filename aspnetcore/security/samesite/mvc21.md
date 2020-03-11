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
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>Exemple de cookie SameSite ASP.NET Core MVC 2,1

ASP.NET Core 2,1 dispose d’une prise en charge intégrée de l’attribut [SameSite](https://www.owasp.org/index.php/SameSite) , mais il a été écrit dans la norme d’origine. Le [comportement corrigé](https://github.com/dotnet/aspnetcore/issues/8212) a modifié la signification de `SameSite.None` pour émettre l’attribut sameSite avec une valeur de `None`, au lieu de ne pas émettre la valeur du tout. Si vous ne souhaitez pas émettre la valeur, vous pouvez définir la propriété `SameSite` sur un cookie sur-1.

## <a name="sampleCode"></a>Écriture de l’attribut SameSite

Voici un exemple d’écriture d’un attribut SameSite sur un cookie :

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

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Définition des cookies d’authentification de cookie et d’état de session

L’authentification par cookie, l’état de session et [divers autres composants](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) définissent leurs options sameSite via des options de cookie, par exemple

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

Dans le code précédent, l’authentification de cookie et l’état de session définissent leur attribut sameSite sur `None`, en émettant l’attribut avec une valeur de `None` et en définissant également l’attribut Secure sur true.

### <a name="run-the-sample"></a>Exécution de l'exemple

Si vous exécutez l' [exemple de projet](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), chargez votre débogueur de navigateur sur la page initiale et utilisez-le pour afficher la collection de cookies pour le site. Pour ce faire, dans Edge et chrome, appuyez sur `F12` sélectionnez l’onglet `Application` et cliquez sur l’URL du site sous l’option `Cookies` dans la section `Storage`.

![Liste des cookies du débogueur de navigateur](BrowserDebugger.png)

Vous pouvez voir à partir de l’image ci-dessus que le cookie créé par l’exemple lorsque vous cliquez sur le bouton « créer un cookie SameSite » a une valeur d’attribut SameSite de `Lax`, ce qui correspond à la valeur définie dans l' [exemple de code](#sampleCode).

## <a name="interception"></a>Interception des cookies

Afin d’intercepter les cookies, pour ajuster la valeur None en fonction de sa prise en charge dans l’agent Browser de l’utilisateur, vous devez utiliser l’intergiciel `CookiePolicy`. Celui-ci doit être placé dans le pipeline de requête HTTP **avant** tous les composants qui écrivent des cookies et configurés dans `ConfigureServices()`.

Pour l’insérer dans le pipeline, utilisez `app.UseCookiePolicy()` dans la méthode `Configure(IApplicationBuilder, IHostingEnvironment)` dans [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs). Par exemple :

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

Ensuite, dans la `ConfigureServices(IServiceCollection services)` configurez la stratégie de cookie pour appeler une classe d’assistance lorsque des cookies sont ajoutés ou supprimés. Par exemple :

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

La fonction d’assistance `CheckSameSite(HttpContext, CookieOptions)`:

* Est appelé lorsque les cookies sont ajoutés à la demande ou supprimés de la requête.
* Vérifie si la propriété `SameSite` est définie sur `None`.
* Si `SameSite` est défini sur `None` et que l’agent utilisateur actuel ne prend pas en charge la valeur de l’attribut None. La vérification s’effectue à l’aide de la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :
  * Définit `SameSite` pour ne pas émettre la valeur en affectant à la propriété la valeur `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Ciblage .NET Framework

ASP.NET Core et System. Web (ASP.NET Classic) ont des implémentations indépendantes de SameSite. Les correctifs de SameSite KB pour les .NET Framework ne sont pas requis si vous utilisez ASP.NET Core et que la version minimale requise de l’infrastructure System. Web SameSite (.NET 4.7.2) s’appliquent à ASP.NET Core.

ASP.NET Core sur .NET requiert la mise à jour des dépendances de package NuGet pour récupérer les correctifs appropriés.

Pour obtenir les modifications de ASP.NET Core pour .NET Framework Vérifiez que vous disposez d’une référence directe aux packages et versions corrigés (2.1.14 ou versions ultérieures 2,1).

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Informations complémentaires
 
[Mises à jour Chrome](https://www.chromium.org/updates/same-site)
[ASP.NET Core Documentation SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core annonce de modification SameSite 2,1](https://github.com/dotnet/aspnetcore/issues/8212)