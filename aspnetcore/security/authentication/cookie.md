---
title: Utiliser l’authentification par cookie sans ASP.NET Core identité
author: rick-anderson
description: Découvrez comment utiliser l’authentification par cookie sans ASP.NET Core identité.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: b7c8b2cccb27dd6818330b17439675e41bfef013
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219205"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Utiliser l’authentification par cookie sans ASP.NET Core identité

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core identité est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions. Toutefois, il est possible d’utiliser un fournisseur d’authentification basé sur des cookies sans ASP.NET Core d’identité. Pour plus d’informations, consultez <xref:security/authentication/identity>.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application. Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et n’importe quel mot de passe pour vous connecter à l’utilisateur. L’utilisateur est authentifié dans la méthode `AuthenticateUser` dans le fichier *pages/Account/login. cshtml. cs* . Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.

## <a name="configuration"></a>Configuration

Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Dans la méthode `Startup.ConfigureServices`, créez les services d’intergiciel (middleware) d’authentification avec les méthodes <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> et <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application. `AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). La définition de la `AuthenticationScheme` sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma. Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.

Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application. Lorsqu’un schéma d’authentification de cookie n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« cookies »).

La propriété <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> du cookie d’authentification est définie sur `true` par défaut. Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données. Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.

Dans `Startup.Configure`, appelez `UseAuthentication` et `UseAuthorization` pour définir la propriété `HttpContext.User` et exécuter l’intergiciel (middleware) des autorisations pour les requêtes. Appelez les méthodes `UseAuthentication` et `UseAuthorization` avant d’appeler `UseEndpoints`:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

La classe <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> est utilisée pour configurer les options du fournisseur d’authentification.

Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la méthode `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Intergiciel de stratégie de cookie

L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie. L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre&mdash;il affecte uniquement les composants en aval inscrits dans le pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Utilisez <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de la stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.

La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` pour autoriser l’authentification OAuth2. Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict`, définissez le `MinimumSameSitePolicy`. Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Le paramètre d’intergiciel (middleware) de stratégie de cookie pour `MinimumSameSitePolicy` peut affecter le paramètre de `Cookie.SameSite` dans `CookieAuthenticationOptions` paramètres selon le tableau ci-dessous.

| MinimumSameSitePolicy | Cookie.SameSite | Paramètre de cookie. SameSite résultant |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Créer un cookie d’authentification

Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal>. Les informations utilisateur sont sérialisées et stockées dans le cookie. 

Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim>s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` crée un cookie chiffré et l’ajoute à la réponse actuelle. Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.

Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement. Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification. Dans le cas contraire, le schéma par défaut est utilisé.

## <a name="react-to-back-end-changes"></a>Réagir aux modifications principales

Une fois qu’un cookie est créé, le cookie est la seule source d’identité. Si un compte d’utilisateur est désactivé dans les systèmes principaux :

* Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.
* L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.

L’événement <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie. La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.

Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur. Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide. Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une valeur de `LastChanged`. Lorsqu’un utilisateur est mis à jour dans la base de données, la valeur de `LastChanged` est définie sur l’heure actuelle.

Afin d’invalider un cookie lorsque la base de données change en fonction de la valeur de `LastChanged`, créez le cookie avec une revendication `LastChanged` contenant la valeur de `LastChanged` actuelle de la base de données :

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

Pour implémenter une substitution pour l’événement `ValidatePrincipal`, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Voici un exemple d’implémentation de `CookieAuthenticationEvents`:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Inscrivez l’instance événements lors de l’inscription du service de cookie dans la méthode `Startup.ConfigureServices`. Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre classe `CustomCookieAuthenticationEvents` :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour&mdash;une décision qui n’affecte en rien la sécurité. Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété `context.ShouldRenew` la valeur `true`.

> [!WARNING]
> L’approche décrite ici est déclenchée à chaque demande. La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.

## <a name="persistent-cookies"></a>Cookies persistants

Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur. Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire. 

L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur. Les paramètres d’expiration décalés précédemment configurés sont honorés. Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.

Définissez <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Expiration absolue des cookies

Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Pour créer un cookie persistant, `IsPersistent` doit également être défini. Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient. Lorsque `ExpiresUtc` est définie, elle remplace la valeur de l’option <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, si elle est définie.

L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes. Cela ignore tous les paramètres d’expiration décalés précédemment configurés.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core identité est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions. Toutefois, il est possible d’utiliser un fournisseur d’authentification d’authentification basée sur les cookies sans ASP.NET Core d’identité. Pour plus d’informations, consultez <xref:security/authentication/identity>.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application. Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et n’importe quel mot de passe pour vous connecter à l’utilisateur. L’utilisateur est authentifié dans la méthode `AuthenticateUser` dans le fichier *pages/Account/login. cshtml. cs* . Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.

## <a name="configuration"></a>Configuration

Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Dans la méthode `Startup.ConfigureServices`, créez le service d’intergiciel (middleware) d’authentification avec les méthodes <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> et <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application. `AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). La définition de la `AuthenticationScheme` sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma. Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.

Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application. Lorsqu’un schéma d’authentification de cookie n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« cookies »).

La propriété <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> du cookie d’authentification est définie sur `true` par défaut. Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données. Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.

Dans la méthode `Startup.Configure`, appelez la méthode `UseAuthentication` pour appeler l’intergiciel (middleware) d’authentification qui définit la propriété `HttpContext.User`. Appelez la méthode `UseAuthentication` avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

La classe <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> est utilisée pour configurer les options du fournisseur d’authentification.

Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la méthode `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Intergiciel de stratégie de cookie

L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie. L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre&mdash;il affecte uniquement les composants en aval inscrits dans le pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Utilisez <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de la stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.

La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` pour autoriser l’authentification OAuth2. Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict`, définissez le `MinimumSameSitePolicy`. Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Le paramètre d’intergiciel (middleware) de stratégie de cookie pour `MinimumSameSitePolicy` peut affecter le paramètre de `Cookie.SameSite` dans `CookieAuthenticationOptions` paramètres selon le tableau ci-dessous.

| MinimumSameSitePolicy | Cookie.SameSite | Paramètre de cookie. SameSite résultant |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Créer un cookie d’authentification

Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal>. Les informations utilisateur sont sérialisées et stockées dans le cookie. 

Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim>s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` crée un cookie chiffré et l’ajoute à la réponse actuelle. Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.

Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement. Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification. Dans le cas contraire, le schéma par défaut est utilisé.

## <a name="react-to-back-end-changes"></a>Réagir aux modifications principales

Une fois qu’un cookie est créé, le cookie est la seule source d’identité. Si un compte d’utilisateur est désactivé dans les systèmes principaux :

* Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.
* L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.

L’événement <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie. La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.

Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur. Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide. Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une valeur de `LastChanged`. Lorsqu’un utilisateur est mis à jour dans la base de données, la valeur de `LastChanged` est définie sur l’heure actuelle.

Afin d’invalider un cookie lorsque la base de données change en fonction de la valeur de `LastChanged`, créez le cookie avec une revendication `LastChanged` contenant la valeur de `LastChanged` actuelle de la base de données :

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

Pour implémenter une substitution pour l’événement `ValidatePrincipal`, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Voici un exemple d’implémentation de `CookieAuthenticationEvents`:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Inscrivez l’instance événements lors de l’inscription du service de cookie dans la méthode `Startup.ConfigureServices`. Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre classe `CustomCookieAuthenticationEvents` :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour&mdash;une décision qui n’affecte en rien la sécurité. Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété `context.ShouldRenew` la valeur `true`.

> [!WARNING]
> L’approche décrite ici est déclenchée à chaque demande. La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.

## <a name="persistent-cookies"></a>Cookies persistants

Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur. Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire. 

L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur. Les paramètres d’expiration décalés précédemment configurés sont honorés. Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.

Définissez <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Expiration absolue des cookies

Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Pour créer un cookie persistant, `IsPersistent` doit également être défini. Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient. Lorsque `ExpiresUtc` est définie, elle remplace la valeur de l’option <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, si elle est définie.

L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes. Cela ignore tous les paramètres d’expiration décalés précédemment configurés.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Vérifications des rôles basés sur des stratégies](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
