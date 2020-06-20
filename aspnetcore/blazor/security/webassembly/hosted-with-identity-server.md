---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec le Identity serveur
author: guardrex
description: Pour créer une Blazor application hébergée avec l’authentification à partir de Visual Studio qui utilise un backend [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0fdc88e8e50856fcc4da0beb74f03925ae24401e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103968"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec le Identity serveur

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une Blazor application hébergée qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans Visual Studio :

1. Créez une application ** Blazor webassembly** . Pour plus d’informations, consultez <xref:blazor/get-started>.
1. Dans la boîte de dialogue **créer une nouvelle Blazor application** , sélectionnez **modifier** dans la section **authentification** .
1. Sélectionnez **des comptes d’utilisateur individuels** , puis cliquez sur **OK**.
1. Activez la case à cocher **ASP.net Core hébergé** dans la section **avancé** .
1. Cliquez sur le bouton **Créer**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Pour créer l’application dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ). Le nom du dossier devient également une partie du nom du projet.

---

## <a name="server-app-configuration"></a>Configuration de l’application serveur

Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.

### <a name="startup-class"></a>Classe de démarrage

La `Startup` classe a les ajouts suivants.

* Dans `Startup.ConfigureServices` :

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer avec une <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance supplémentaire qui définit les conventions de ASP.net Core par défaut par-dessus IdentityServer :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentification avec une <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dans `Startup.Configure` :

  * L’intergiciel IdentityServer expose les points de terminaison Open ID Connect (OIDC) :

    ```csharp
    app.UseIdentityServer();
    ```

  * L’intergiciel d’authentification est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :

    ```csharp
    app.UseAuthentication();
    ```

  * L’intergiciel d’autorisation active les fonctionnalités d’autorisation :

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [IdentityServer](https://identityserver.io/) pour les scénarios de ASP.net core. IdentityServer est une infrastructure puissante et extensible pour gérer les problèmes de sécurité des applications. IdentityServer expose une complexité inutile pour les scénarios les plus courants. Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ. Une fois vos besoins d’authentification modifiés, toute la puissance de IdentityServer est disponible pour personnaliser l’authentification en fonction des exigences d’une application.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut. La stratégie est configurée pour autoriser Identity à gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL `/Identity` . Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes. En outre, cette méthode :

* Inscrit une `{APPLICATION NAME}API` ressource API avec IdentityServer avec une étendue par défaut `{APPLICATION NAME}API` .
* Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par IdentityServer pour l’application.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Dans `WeatherForecastController` (*Controllers/WeatherForecastController. cs*), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe. L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource. La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Dans `ApplicationDbContext` (*Data/ApplicationDbContext. cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> s’étend <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> pour inclure le schéma pour IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> méthode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Dans `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), le point de terminaison client est approvisionné pour servir les paramètres OIDC.

### <a name="app-settings-files"></a>Fichiers de paramètres d’application

Dans le fichier de paramètres d’application (*appsettings.jssur*) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés. Dans l’exemple suivant, il existe un seul client. Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth. Le profil indique le type d’application en cours de configuration. Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>Configuration de l’application cliente

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes d’utilisateur individuels ( `Individual` ), l’application reçoit automatiquement une référence de package pour le package [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) dans le fichier projet de l’application. Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Prise en charge des autorisations d’API

La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par la méthode d’extension fournie dans le package [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Cette méthode Configure les services requis par l’application pour interagir avec le système d’autorisation existant.

```csharp
builder.Services.AddApiAuthorization();
```

Par défaut, la configuration de l’application est chargée par Convention à partir de `_configuration/{client-id}` . Par Convention, l’ID client est défini sur le nom de l’assembly de l’application. Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.

### <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans le `MainLayout` composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :

* Pour les utilisateurs authentifiés :
  * Affiche le nom de l’utilisateur actuel.
  * Propose un lien vers la page de profil utilisateur dans ASP.NET Core Identity .
  * Offre un bouton permettant de se déconnecter de l’application.
* Pour les utilisateurs anonymes :
  * Offre la possibilité de s’inscrire.
  * Offre la possibilité de se connecter.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application à partir du projet serveur. Lorsque vous utilisez Visual Studio, vous pouvez :

* Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .
* Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nom et revendication de rôle avec autorisation d’API

### <a name="custom-user-factory"></a>Fabrique d’utilisateur personnalisée

Dans l’application cliente, créez une fabrique d’utilisateurs personnalisée. IdentityLe serveur envoie plusieurs rôles sous forme de tableau JSON dans une seule `role` revendication. Un rôle unique est envoyé sous la forme d’une valeur de chaîne dans la revendication. La fabrique crée une `role` revendication individuelle pour chacun des rôles de l’utilisateur.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

Dans l’application cliente, inscrivez la fabrique en `Program.Main` (*Program.cs*) :

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Dans l’application serveur, appelez <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sur le Identity générateur, qui ajoute des services liés aux rôles :

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Configurer le Identity serveur

Utilisez l' **une** des approches suivantes :

* [Options d’autorisation d’API](#api-authorization-options)
* [Service de profil](#profile-service)

#### <a name="api-authorization-options"></a>Options d’autorisation d’API

Dans l’application serveur :

* Configurez Identity le serveur pour placer les `name` `role` revendications et dans le jeton d’ID et le jeton d’accès.
* Empêche le mappage par défaut des rôles dans le gestionnaire de jetons JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Service de profil

Dans l’application serveur, créez une `ProfileService` implémentation.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

Dans l’application serveur, inscrivez le service de profil dans `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Utiliser des mécanismes d’autorisation

Dans l’application cliente, les approches d’autorisation des composants sont fonctionnelles à ce stade. L’un des mécanismes d’autorisation des composants peut utiliser un rôle pour autoriser l’utilisateur :

* [Composant AuthorizeView](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )
* [Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )

  Plusieurs tests de rôle sont pris en charge :

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`est renseigné dans l’application cliente avec le nom d’utilisateur de l’utilisateur, qui est généralement l’adresse de messagerie de connexion.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Déploiement sur Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importer un certificat à partir d’Key Vault (documentation Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)