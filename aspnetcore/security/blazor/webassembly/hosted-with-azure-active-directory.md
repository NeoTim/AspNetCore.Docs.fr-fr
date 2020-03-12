---
title: Sécuriser une application hébergée ASP.NET Core Blazor webassembly avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0803e436d66ef7df3c68739e674a8652fde11166
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083845"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Sécuriser une application hébergée ASP.NET Core Blazor webassembly avec Azure Active Directory

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



Cet article explique comment créer une [application hébergée parBlazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Inscrire des applications dans AAD B2C et créer une solution

### <a name="create-a-tenant"></a>Créer un client

Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.

### <a name="register-a-server-api-app"></a>Inscrire une application API serveur

Suivez les instructions du Guide de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity et les](/azure/active-directory/develop/quickstart-register-app) rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* dans le **Azure Active Directory** > zone **inscriptions d’applications** de la portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, **Blazor Server AAD**).
1. Choisissez un **type de compte pris en charge**. Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.
1. Désactivez la case à cocher accorder les **autorisations > ** **accorder à l’administrateur de openid et de offline_access** .
1. Sélectionnez **Inscription**.

Dans **autorisations d’API**, supprimez le **Microsoft Graph** > autorisation **User. Read** , car l’application ne nécessite pas d’accès de profil UER ou de connexion.

Dans **exposer une API**:

1. sélectionner **Ajouter une étendue**.
1. Sélectionnez **Enregistrer et continuer**.
1. Spécifiez un **nom d’étendue** (par exemple, `API.Access`).
1. Indiquez un **nom d’affichage du consentement** de l’administrateur (par exemple, `Access API`).
1. Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.`).
1. Confirmez que l' **État** est défini sur **activé**.
1. Sélectionnez **Ajouter une étendue**.

Notez les informations suivantes :

* *Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)
* ID de répertoire (ID de locataire) (par exemple, `222222222-2222-2222-2222-222222222222`)
* Domaine du locataire AAD (par exemple, `contoso.onmicrosoft.com`)
* Étendue par défaut (par exemple, `API.Access`)

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les instructions du Guide de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity et les](/azure/active-directory/develop/quickstart-register-app) rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application cliente* dans la zone de **inscriptions d’applications** > **Azure Active Directory** de la portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD**).
1. Choisissez un **type de compte pris en charge**. Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. Laissez la liste déroulante **URI de redirection** définie sur **Web**et fournissez un URI de redirection de `https://localhost:5001/authentication/login-callback`.
1. Désactivez la case à cocher accorder les **autorisations > ** **accorder à l’administrateur de openid et de offline_access** .
1. Sélectionnez **Inscription**.

Dans > **d’authentification** **configurations de plateforme** > **Web**:

1. Confirmez que l' **URI de redirection** de `https://localhost:5001/authentication/login-callback` est présent.
1. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Dans **autorisations d’API**:

1. Vérifiez que l’application a **Microsoft Graph** > autorisation **User. Read** .
1. Sélectionnez **Ajouter une autorisation** suivi de **mes API**.
1. Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, **Blazor Server AAD**).
1. Ouvrez la liste des **API** .
1. Activez l’accès à l’API (par exemple, `API.Access`).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez le bouton **Grant admin content for {locataire Name}** . Sélectionnez **Oui** pour confirmer.

Enregistrez l’ID de l’application *cliente* (ID client) (par exemple, `33333333-3333-3333-3333-333333333333`).

### <a name="create-the-app"></a>Créer l’application

Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample`). Le nom du dossier devient également une partie du nom du projet.

> [!NOTE]
> Consultez la section [prise en charge du service d’authentification](#Authentication service support) pour obtenir une modification importante de la configuration de l’étendue du jeton d’accès par défaut. La valeur fournie par le modèle Blazor webassembly doit être modifiée manuellement une fois que l' *application cliente* a été créée à partir du modèle.

## <a name="server-app-configuration"></a>Configuration de l’application serveur

*Cette section se rapporte à l’application **serveur** de la solution.*

### <a name="authentication-package"></a>Package d’authentification

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La méthode `AddAuthentication` définit les services d’authentification au sein de l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La méthode `AddAzureADBearer` définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par le Azure Active Directory :

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` et `UseAuthorization` Assurez-vous que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>Paramètres de l’application

Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Contrôleur WeatherForecast

Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l’attribut `[Authorize]` appliqué au contrôleur. Il est **important** de comprendre que :

* L’attribut `[Authorize]` dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.
* L’attribut `[Authorize]` utilisé dans l’application Blazor webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuration de l’application cliente

*Cette section se rapporte à l’application **cliente** de la solution.*

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes professionnels ou scolaires (`SingleOrg`), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du package `Microsoft.AspNetCore.Blazor.Templates` présentée dans l’article <xref:blazor/get-started>.

Le package `Microsoft.Authentication.WebAssembly.Msal` ajoute transitivement le package `Microsoft.AspNetCore.Components.WebAssembly.Authentication` à l’application.

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service à l’aide de la méthode d’extension `AddMsalAuthentication` fournie par le package `Microsoft.Authentication.WebAssembly.Msal`. Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).

*Program.cs* :

Lorsque l' *application cliente* est générée, l’étendue du jeton d’accès par défaut est au format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`. **Supprimez la partie `api://` de la valeur de portée.** Ce problème sera résolu dans une prochaine version préliminaire.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> L’étendue du jeton d’accès par défaut doit être au format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (par exemple, `11111111-1111-1111-1111-111111111111/API.Access`). Si un modèle ou un schéma et un hôte sont fournis au paramètre d’étendue (comme indiqué dans le portail Azure), l' *application cliente* lève une exception non gérée lorsqu’elle reçoit une réponse *non autorisée 401* de l' *application API serveur*.

La méthode `AddMsalAuthentication` accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.

Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :

* Inclus par défaut dans la demande de connexion.
* Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.

Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles. Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authentication/azure-active-directory/index>
