---
title: Présentation de l’authentification pour les applications à page unique sur ASP.NET Core
author: javiercn
description: Utilisez l’identité avec une seule application de page hébergée dans une application ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 623f739b17c0bed3ce929f562c9581ab26ecf5bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661412"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="1e8f8-103">Authentification et autorisation pour SPAs</span><span class="sxs-lookup"><span data-stu-id="1e8f8-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="1e8f8-104">ASP.NET Core 3,0 ou version ultérieure offre une authentification dans les applications à page unique (SPAs) à l’aide de la prise en charge de l’autorisation de l’API.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="1e8f8-105">ASP.NET Core identité pour l’authentification et le stockage des utilisateurs est associée à [IdentityServer](https://identityserver.io/) pour l’implémentation d’Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="1e8f8-106">Un paramètre d’authentification a été ajouté aux modèles de projet **angulaire** et **REACT** qui est similaire au paramètre d’authentification dans les modèles de projet **application Web (Model-View-Controller)** et **application Web** (Razor pages).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="1e8f8-107">Les valeurs de paramètre autorisées sont **None** et **Individual**.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="1e8f8-108">Le modèle de projet **REACT. js et Redux** ne prend pas en charge le paramètre d’authentification pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="1e8f8-109">Créer une application avec prise en charge des autorisations d’API</span><span class="sxs-lookup"><span data-stu-id="1e8f8-109">Create an app with API authorization support</span></span>

<span data-ttu-id="1e8f8-110">L’authentification et l’autorisation de l’utilisateur peuvent être utilisées avec les deux types d’angle et de réaction.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="1e8f8-111">Ouvrez une invite de commandes et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="1e8f8-112">**Angulaire**:</span><span class="sxs-lookup"><span data-stu-id="1e8f8-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="1e8f8-113">**Réaction**:</span><span class="sxs-lookup"><span data-stu-id="1e8f8-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="1e8f8-114">La commande précédente crée une application ASP.NET Core avec un répertoire *ClientApp* contenant le Spa.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="1e8f8-115">Description générale des composants ASP.NET Core de l’application</span><span class="sxs-lookup"><span data-stu-id="1e8f8-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="1e8f8-116">Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="1e8f8-117">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="1e8f8-117">Startup class</span></span>

<span data-ttu-id="1e8f8-118">La classe `Startup` présente les ajouts suivants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="1e8f8-119">À l’intérieur de la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="1e8f8-120">Identité avec l’interface utilisateur par défaut :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="1e8f8-121">IdentityServer avec une méthode d’assistance `AddApiAuthorization` supplémentaire qui définit des conventions de ASP.NET Core par défaut par-dessus IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-121">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="1e8f8-122">Authentification avec une méthode d’assistance `AddIdentityServerJwt` supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="1e8f8-123">À l’intérieur de la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="1e8f8-124">Intergiciel d’authentification responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="1e8f8-125">L’intergiciel IdentityServer qui expose les points de terminaison Open ID Connect :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="1e8f8-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="1e8f8-126">AddApiAuthorization</span></span>

<span data-ttu-id="1e8f8-127">Cette méthode d’assistance configure IdentityServer pour utiliser notre configuration prise en charge.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="1e8f8-128">IdentityServer est une infrastructure puissante et extensible pour gérer les problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="1e8f8-129">En même temps, cela expose une complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="1e8f8-130">Par conséquent, un ensemble de conventions et d’options de configuration qui vous sont fournies est considéré comme un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="1e8f8-131">Une fois vos besoins d’authentification modifiés, toute la puissance de IdentityServer est toujours disponible pour personnaliser l’authentification en fonction de vos besoins.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="1e8f8-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="1e8f8-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="1e8f8-133">Cette méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="1e8f8-134">La stratégie est configurée pour permettre à l’identité de traiter toutes les demandes routées vers n’importe quel sous-chemin dans l’espace d’URL d’identité « /Identity ».</span><span class="sxs-lookup"><span data-stu-id="1e8f8-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="1e8f8-135">Le `JwtBearerHandler` gère toutes les autres requêtes.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="1e8f8-136">En outre, cette méthode enregistre une ressource API `<<ApplicationName>>API` avec IdentityServer avec une étendue par défaut de `<<ApplicationName>>API` et configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par IdentityServer pour l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="1e8f8-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="1e8f8-137">WeatherForecastController</span></span>

<span data-ttu-id="1e8f8-138">Dans le fichier *Controllers\WeatherForecastController.cs* , notez l’attribut `[Authorize]` appliqué à la classe qui indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="1e8f8-139">La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, qui est configurée par `AddIdentityServerJwt` vers le schéma de stratégie mentionné plus haut, ce qui fait du `JwtBearerHandler` configuré par une telle méthode d’assistance le gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="1e8f8-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="1e8f8-140">ApplicationDbContext</span></span>

<span data-ttu-id="1e8f8-141">Dans le fichier *Data\ApplicationDbContext.cs* , Notez que la même `DbContext` est utilisée dans l’identité, à l’exception près qu’elle étend `ApiAuthorizationDbContext` (une classe plus dérivée de `IdentityDbContext`) pour inclure le schéma pour IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="1e8f8-142">Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des classes d’identité `DbContext` disponibles et configurez le contexte pour inclure le schéma d’identité en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sur la méthode de `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="1e8f8-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="1e8f8-143">OidcConfigurationController</span></span>

<span data-ttu-id="1e8f8-144">Dans le fichier *Controllers\OidcConfigurationController.cs* , notez le point de terminaison configuré pour servir les paramètres OIDC que le client doit utiliser.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1e8f8-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="1e8f8-145">appsettings.json</span></span>

<span data-ttu-id="1e8f8-146">Dans le fichier *appSettings. JSON* de la racine du projet, une nouvelle section `IdentityServer` décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="1e8f8-147">Dans l’exemple suivant, il existe un seul client.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-147">In the following example, there's a single client.</span></span> <span data-ttu-id="1e8f8-148">Le nom du client correspond au nom de l’application et est mappé par Convention au paramètre `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="1e8f8-149">Le profil indique le type d’application en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="1e8f8-150">Il est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="1e8f8-151">Plusieurs profils sont disponibles, comme expliqué dans la section [profils d’application](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="1e8f8-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="1e8f8-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="1e8f8-152">appsettings.Development.json</span></span>

<span data-ttu-id="1e8f8-153">Dans le *appSettings. Fichier Development. JSON* de la racine du projet, il existe une section `IdentityServer` qui décrit la clé utilisée pour signer les jetons.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="1e8f8-154">Lors du déploiement en production, une clé doit être approvisionnée et déployée parallèlement à l’application, comme expliqué dans la section [déploiement en production](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="1e8f8-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="1e8f8-155">Description générale de l’application angulaire</span><span class="sxs-lookup"><span data-stu-id="1e8f8-155">General description of the Angular app</span></span>

<span data-ttu-id="1e8f8-156">L’authentification et la prise en charge de l’autorisation d’API dans le modèle angulaire résident dans son propre module angulaire dans le répertoire *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="1e8f8-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="1e8f8-157">Le module est composé des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="1e8f8-158">3 composants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-158">3 components:</span></span>
  * <span data-ttu-id="1e8f8-159">*login. Component. TS*: gère le déroulement de la connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="1e8f8-160">*logout. Component. TS*: gère le déroulement de la déconnexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1e8f8-161">*login-menu. Component. TS*: un widget qui affiche l’un des ensembles de liens suivants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1e8f8-162">Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1e8f8-163">Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="1e8f8-164">`AuthorizeGuard` de la protection d’itinéraire qui peut être ajouté aux itinéraires et requiert qu’un utilisateur soit authentifié avant de visiter l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="1e8f8-165">`AuthorizeInterceptor` d’intercepteur HTTP qui associe le jeton d’accès aux requêtes HTTP sortantes ciblant l’API lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="1e8f8-166">`AuthorizeService` de service qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="1e8f8-167">Module angulaire qui définit les itinéraires associés aux parties d’authentification de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="1e8f8-168">Il expose le composant de menu de connexion, l’intercepteur, la protection et le service à consommer à partir du reste de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="1e8f8-169">Description générale de l’application REACT</span><span class="sxs-lookup"><span data-stu-id="1e8f8-169">General description of the React app</span></span>

<span data-ttu-id="1e8f8-170">La prise en charge de l’authentification et de l’autorisation d’API dans le modèle REACT réside dans le répertoire *ClientApp\src\components\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="1e8f8-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="1e8f8-171">Il se compose des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="1e8f8-172">4 composants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-172">4 components:</span></span>
  * <span data-ttu-id="1e8f8-173">*Login. js*: gère le déroulement de la connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="1e8f8-174">*Logout. js*: gère le déroulement de la déconnexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1e8f8-175">*LoginMenu. js*: widget qui affiche l’un des ensembles de liens suivants :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1e8f8-176">Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1e8f8-177">Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="1e8f8-178">*AuthorizeRoute. js*: composant de routage qui requiert l’authentification d’un utilisateur avant le rendu du composant indiqué dans le paramètre `Component`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="1e8f8-179">Une `authService` instance exportée de la classe `AuthorizeService` qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="1e8f8-180">Maintenant que vous avez vu les principaux composants de la solution, vous pouvez examiner de manière plus détaillée les scénarios individuels de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="1e8f8-181">Exiger une autorisation sur une nouvelle API</span><span class="sxs-lookup"><span data-stu-id="1e8f8-181">Require authorization on a new API</span></span>

<span data-ttu-id="1e8f8-182">Par défaut, le système est configuré pour demander facilement une autorisation pour les nouvelles API.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="1e8f8-183">Pour ce faire, créez un nouveau contrôleur et ajoutez l’attribut `[Authorize]` à la classe de contrôleur ou à n’importe quelle action dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="1e8f8-184">Personnaliser le gestionnaire d’authentification d’API</span><span class="sxs-lookup"><span data-stu-id="1e8f8-184">Customize the API authentication handler</span></span>

<span data-ttu-id="1e8f8-185">Pour personnaliser la configuration du gestionnaire JWT de l’API, configurez son <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="1e8f8-186">Le gestionnaire JWT de l’API déclenche des événements qui permettent de contrôler le processus d’authentification à l’aide de `JwtBearerEvents`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="1e8f8-187">Pour assurer la prise en charge de l’autorisation d’API, `AddIdentityServerJwt` inscrit ses propres gestionnaires d’événements.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="1e8f8-188">Pour personnaliser la gestion d’un événement, encapsulez le gestionnaire d’événements existant avec une logique supplémentaire, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="1e8f8-189">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-189">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="1e8f8-190">Dans le code précédent, le gestionnaire d’événements `OnTokenValidated` est remplacé par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="1e8f8-191">Cette implémentation :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-191">This implementation:</span></span>

1. <span data-ttu-id="1e8f8-192">Appelle l’implémentation d’origine fournie par la prise en charge de l’autorisation d’API.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="1e8f8-193">Exécutez sa propre logique personnalisée.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="1e8f8-194">Protéger un itinéraire côté client (angulaire)</span><span class="sxs-lookup"><span data-stu-id="1e8f8-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="1e8f8-195">La protection d’un itinéraire côté client s’effectue en ajoutant la protection d’autorisation à la liste des gardes à exécuter lors de la configuration d’un itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="1e8f8-196">Par exemple, vous pouvez voir comment la `fetch-data` itinéraire est configuré dans le module angulaire de l’application principale :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="1e8f8-197">Il est important de mentionner que la protection d’un itinéraire ne protège pas le point de terminaison réel (ce qui nécessite toujours un attribut `[Authorize]` qui lui est appliqué), mais qu’il empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="1e8f8-198">Authentifier les demandes d’API (angulaires)</span><span class="sxs-lookup"><span data-stu-id="1e8f8-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="1e8f8-199">L’authentification des demandes auprès des API hébergées parallèlement à l’application s’effectue automatiquement par le biais de l’intercepteur client HTTP défini par l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="1e8f8-200">Protéger un itinéraire côté client (REACT)</span><span class="sxs-lookup"><span data-stu-id="1e8f8-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="1e8f8-201">Protégez un itinéraire côté client à l’aide du composant `AuthorizeRoute` au lieu du composant `Route` brut.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="1e8f8-202">Par exemple, notez la configuration de l’itinéraire `fetch-data` dans le composant `App` :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="1e8f8-203">Protection d’un itinéraire :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-203">Protecting a route:</span></span>

* <span data-ttu-id="1e8f8-204">Ne protège pas le point de terminaison réel (qui requiert toujours un attribut `[Authorize]` qui lui est appliqué).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="1e8f8-205">Empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="1e8f8-206">Authentifier les demandes d’API (REACT)</span><span class="sxs-lookup"><span data-stu-id="1e8f8-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="1e8f8-207">Pour authentifier les demandes avec REACT, commencez par importer l’instance de `authService` à partir du `AuthorizeService`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="1e8f8-208">Le jeton d’accès est récupéré à partir de la `authService` et est joint à la demande, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="1e8f8-209">Dans les composants de réaction, ce travail est généralement effectué dans la méthode de cycle de vie `componentDidMount` ou en tant que résultat de certaines interactions de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="1e8f8-210">Importer le authService dans votre composant</span><span class="sxs-lookup"><span data-stu-id="1e8f8-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="1e8f8-211">Récupérer et attacher le jeton d’accès à la réponse</span><span class="sxs-lookup"><span data-stu-id="1e8f8-211">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="1e8f8-212">Déployer en production</span><span class="sxs-lookup"><span data-stu-id="1e8f8-212">Deploy to production</span></span>

<span data-ttu-id="1e8f8-213">Pour déployer l’application en production, vous devez configurer les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="1e8f8-214">Une base de données pour stocker les comptes d’utilisateur d’identité et les autorisations IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="1e8f8-215">Certificat de production à utiliser pour la signature des jetons.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="1e8f8-216">Il n’existe aucune exigence spécifique pour ce certificat. Il peut s’agir d’un certificat auto-signé ou d’un certificat approvisionné par le biais d’une autorité de certification.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="1e8f8-217">Il peut être généré à l’aide d’outils standard tels que PowerShell ou OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="1e8f8-218">Il peut être installé dans le magasin de certificats sur les ordinateurs cibles ou déployé en tant que fichier *. pfx* avec un mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="1e8f8-219">Exemple : déploiement sur sites Web Azure</span><span class="sxs-lookup"><span data-stu-id="1e8f8-219">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="1e8f8-220">Cette section décrit le déploiement de l’application sur des sites Web Azure à l’aide d’un certificat stocké dans le magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-220">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="1e8f8-221">Pour modifier l’application afin de charger un certificat à partir du magasin de certificats, le plan de App Service doit être au moins au niveau standard lorsque vous configurez dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-221">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="1e8f8-222">Dans le fichier *appSettings. JSON* de l’application, modifiez la section `IdentityServer` pour inclure les détails de la clé :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="1e8f8-223">Le nom du magasin représente le nom du magasin de certificats dans lequel le certificat est stocké.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-223">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="1e8f8-224">Dans ce cas, il pointe vers le magasin de l’utilisateur personnel.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-224">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="1e8f8-225">L’emplacement du magasin représente l’emplacement à partir duquel charger le certificat (`CurrentUser` ou `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-225">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="1e8f8-226">La propriété Name du certificat correspond au sujet distinctif du certificat.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-226">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="1e8f8-227">Pour effectuer un déploiement sur des sites Web Azure, déployez l’application en suivant les étapes décrites dans [déployer l’application sur Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) pour créer les ressources Azure nécessaires et déployer l’application en production.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-227">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="1e8f8-228">Après avoir effectué les instructions précédentes, l’application est déployée dans Azure, mais n’est pas encore fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="1e8f8-229">Le certificat utilisé par l’application doit toujours être configuré.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-229">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="1e8f8-230">Localisez l’empreinte numérique du certificat à utiliser et suivez les étapes décrites dans [charger vos certificats](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-230">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="1e8f8-231">Bien que ces étapes mentionnent SSL, il existe une section **certificats privés** sur le portail, dans laquelle vous pouvez télécharger le certificat approvisionné à utiliser avec l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-231">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="1e8f8-232">Après cette étape, redémarrez l’application et celle-ci doit être fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-232">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="1e8f8-233">Autres options de configuration</span><span class="sxs-lookup"><span data-stu-id="1e8f8-233">Other configuration options</span></span>

<span data-ttu-id="1e8f8-234">La prise en charge de l’autorisation de l’API s’appuie sur IdentityServer avec un ensemble de conventions, de valeurs par défaut et d’améliorations pour simplifier l’expérience de la création de la demande.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="1e8f8-235">Inutile de préciser que la pleine puissance de IdentityServer est disponible en arrière-plan si les intégrations de ASP.NET Core ne couvrent pas votre scénario.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="1e8f8-236">La prise en charge ASP.NET Core est axée sur les applications « internes », où toutes les applications sont créées et déployées par notre organisation.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="1e8f8-237">Par conséquent, le support n’est pas proposé pour les éléments tels que le consentement ou la Fédération.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="1e8f8-238">Pour ces scénarios, utilisez IdentityServer et suivez leur documentation.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="1e8f8-239">Profils d’application</span><span class="sxs-lookup"><span data-stu-id="1e8f8-239">Application profiles</span></span>

<span data-ttu-id="1e8f8-240">Les profils d’application sont des configurations prédéfinies pour les applications qui définissent davantage leurs paramètres.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="1e8f8-241">À ce stade, les profils suivants sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="1e8f8-242">`IdentityServerSPA`: représente un SPA hébergé en même temps que IdentityServer comme une unité unique.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="1e8f8-243">La valeur par défaut de la `redirect_uri` `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="1e8f8-244">La valeur par défaut de la `post_logout_redirect_uri` `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="1e8f8-245">L’ensemble d’étendues comprend les `openid`, les `profile`et toutes les étendues définies pour les API dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1e8f8-246">L’ensemble de types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1e8f8-247">Le mode de réponse autorisé est `fragment`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1e8f8-248">`SPA`: représente un SPA qui n’est pas hébergé avec IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="1e8f8-249">L’ensemble d’étendues comprend les `openid`, les `profile`et toutes les étendues définies pour les API dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1e8f8-250">L’ensemble de types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="1e8f8-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1e8f8-251">Le mode de réponse autorisé est `fragment`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1e8f8-252">`IdentityServerJwt`: représente une API hébergée conjointement avec IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="1e8f8-253">L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="1e8f8-254">`API`: représente une API qui n’est pas hébergée avec IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="1e8f8-255">L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="1e8f8-256">Configuration via AppSettings</span><span class="sxs-lookup"><span data-stu-id="1e8f8-256">Configuration through AppSettings</span></span>

<span data-ttu-id="1e8f8-257">Configurez les applications par le biais du système de configuration en les ajoutant à la liste des `Clients` ou des `Resources`.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="1e8f8-258">Configurez la propriété `redirect_uri` et `post_logout_redirect_uri` de chaque client, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="1e8f8-259">Lors de la configuration des ressources, vous pouvez configurer les étendues de la ressource comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1e8f8-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="1e8f8-260">Configuration par le biais du code</span><span class="sxs-lookup"><span data-stu-id="1e8f8-260">Configuration through code</span></span>

<span data-ttu-id="1e8f8-261">Vous pouvez également configurer les clients et les ressources par le biais du code à l’aide d’une surcharge de `AddApiAuthorization` qui prend une mesure pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="1e8f8-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="1e8f8-262">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1e8f8-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
