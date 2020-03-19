---
title: Héberger et déployer ASP.NET Core Blazor webassembly
author: guardrex
description: Découvrez comment héberger et déployer une application Blazor à l’aide de ASP.NET Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: ea2c625f424447209a362cdc58bdb18be061e47f
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511351"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="ed8ed-103">Héberger et déployer ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="ed8ed-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ed8ed-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ed8ed-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ed8ed-105">Avec le [modèle d’hébergement WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ed8ed-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ed8ed-106">L’application Blazor, ses dépendances et le Runtime .NET sont téléchargés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="ed8ed-107">L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ed8ed-108">Les stratégies de déploiement suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ed8ed-109">L’application Blazor est fournie par une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ed8ed-110">Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ed8ed-111">L’application Blazor est placée sur un service ou un serveur Web d’hébergement statique, où .NET n’est pas utilisé pour traiter l’application Blazor.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ed8ed-112">Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une application Blazor webassembly en tant que sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ed8ed-113">Réécriture d’URL pour un routage correct</span><span class="sxs-lookup"><span data-stu-id="ed8ed-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ed8ed-114">Les requêtes de routage pour les composants de page dans une application Blazor webassembly ne sont pas aussi simples que les demandes de routage dans un serveur de Blazor, une application hébergée.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ed8ed-115">Prenons l’exemple d’une application Blazor webassembly avec deux composants :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ed8ed-116">*Main. razor* &ndash; se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ed8ed-117">*À propos de* la &ndash; du composant `About`. Razor.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="ed8ed-118">Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ed8ed-119">Le navigateur effectue une requête.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-119">The browser makes a request.</span></span>
1. <span data-ttu-id="ed8ed-120">La page par défaut est retournée, généralement *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ed8ed-121">*index.HTML* amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="ed8ed-122">le routeur de Blazorest chargé et le composant de `Main` Razor est rendu.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ed8ed-123">Dans la page principale, le fait de sélectionner le lien vers le composant `About` fonctionne sur le client, car le routeur Blazor empêche le navigateur de faire une demande sur Internet pour `www.contoso.com` pour `About` et sert le composant `About` rendu lui-même.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ed8ed-124">Toutes les requêtes pour les points de terminaison internes *au sein de l’application Blazor Webassembly* fonctionnent de la même façon : les demandes ne déclenchent pas de demandes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ed8ed-125">Le routeur gère les requêtes en interne.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-125">The router handles the requests internally.</span></span>

<span data-ttu-id="ed8ed-126">Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ed8ed-127">Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ed8ed-128">Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ed8ed-129">Quand *index. html* est retourné, le routeur de Blazor de l’application prend le relais et répond avec la ressource correcte.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ed8ed-130">Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *Web. config* publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ed8ed-131">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="ed8ed-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ed8ed-132">Déploiement hébergé avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed8ed-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ed8ed-133">Un *déploiement hébergé* sert l’application Blazor webassembly aux navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ed8ed-134">L’application cliente Blazor webassembly est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ed8ed-135">Les deux applications sont déployées ensemble.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-135">The two apps are deployed together.</span></span> <span data-ttu-id="ed8ed-136">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ed8ed-137">Pour un déploiement hébergé, Visual Studio comprend le modèle de projet d' **applicationBlazor Webassembly** (`blazorwasm` modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ) avec l’option **hébergée** sélectionnée (`-ho|--hosted` lors de l’utilisation de la commande `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ed8ed-138">Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ed8ed-139">Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ed8ed-140">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="ed8ed-140">Standalone deployment</span></span>

<span data-ttu-id="ed8ed-141">Un *Déploiement autonome* sert l’application Blazor webassembly sous la forme d’un ensemble de fichiers statiques demandés directement par les clients.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ed8ed-142">Tout serveur de fichiers statique peut traiter l’application Blazor.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ed8ed-143">Les ressources de déploiement autonomes sont publiées dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="ed8ed-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="ed8ed-144">IIS</span><span class="sxs-lookup"><span data-stu-id="ed8ed-144">IIS</span></span>

<span data-ttu-id="ed8ed-145">IIS est un serveur de fichiers statiques permettant de Blazor des applications.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ed8ed-146">Pour configurer IIS pour héberger des Blazor, consultez [créer un site Web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ed8ed-147">Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ed8ed-148">Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ed8ed-149">web.config</span><span class="sxs-lookup"><span data-stu-id="ed8ed-149">web.config</span></span>

<span data-ttu-id="ed8ed-150">Quand un projet de Blazor est publié, un fichier *Web. config* est créé avec la configuration IIS suivante :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ed8ed-151">Les types MIME sont définis pour les extensions de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ed8ed-152">*. dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="ed8ed-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="ed8ed-153">&ndash; *. json* `application/json`</span><span class="sxs-lookup"><span data-stu-id="ed8ed-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="ed8ed-154">*wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="ed8ed-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="ed8ed-155">*woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ed8ed-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="ed8ed-156">*woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ed8ed-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="ed8ed-157">La compression HTTP est activée pour les types MIME suivants :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ed8ed-158">Des règles du module de réécriture d’URL sont établies :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ed8ed-159">Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application (*wwwroot/{chemin demandé}* ).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ed8ed-160">Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier des ressources statiques (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ed8ed-161">Installer le module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="ed8ed-161">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ed8ed-162">Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-162">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ed8ed-163">Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-163">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ed8ed-164">Vous devez le télécharger à partir du site web IIS.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-164">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ed8ed-165">Utilisez Web Platform Installer pour installer le module :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-165">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ed8ed-166">Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-166">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ed8ed-167">Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-167">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ed8ed-168">Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-168">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ed8ed-169">Copiez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-169">Copy the installer to the server.</span></span> <span data-ttu-id="ed8ed-170">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-170">Run the installer.</span></span> <span data-ttu-id="ed8ed-171">Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-171">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ed8ed-172">Il n’est pas nécessaire de redémarrer le serveur après l’installation.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-172">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ed8ed-173">Configurer le site web</span><span class="sxs-lookup"><span data-stu-id="ed8ed-173">Configure the website</span></span>

<span data-ttu-id="ed8ed-174">Affectez le dossier de l’application comme **chemin physique** du site web.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-174">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ed8ed-175">Le dossier contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-175">The folder contains:</span></span>

* <span data-ttu-id="ed8ed-176">Le fichier *web.config* utilisé par IIS pour configurer le site web, notamment les règles de redirection nécessaires et les types de contenu de fichiers</span><span class="sxs-lookup"><span data-stu-id="ed8ed-176">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ed8ed-177">Le dossier de ressources statiques de l’application</span><span class="sxs-lookup"><span data-stu-id="ed8ed-177">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ed8ed-178">Héberger en tant que sous-application IIS</span><span class="sxs-lookup"><span data-stu-id="ed8ed-178">Host as an IIS sub-app</span></span>

<span data-ttu-id="ed8ed-179">Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-179">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ed8ed-180">Désactivez le gestionnaire de module ASP.NET Core hérité.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-180">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ed8ed-181">Supprimez le gestionnaire dans le fichier *Web. config* publié de Blazor application en ajoutant une section `<handlers>` au fichier :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-181">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ed8ed-182">Désactivez l’héritage de la section `<system.webServer>` de l’application racine (parent) à l’aide d’un élément `<location>` avec `inheritInChildApplications` défini sur `false`:</span><span class="sxs-lookup"><span data-stu-id="ed8ed-182">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="ed8ed-183">La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-183">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ed8ed-184">Dans le fichier *index.html* de l’application, définissez le chemin de base de l’application sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-184">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ed8ed-185">Dépannage</span><span class="sxs-lookup"><span data-stu-id="ed8ed-185">Troubleshooting</span></span>

<span data-ttu-id="ed8ed-186">Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-186">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ed8ed-187">Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-187">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ed8ed-188">Cela empêche le gestionnaire des services Internet de charger la configuration du site Web et le site Web de servir les fichiers statiques de Blazor.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-188">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ed8ed-189">Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-189">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ed8ed-190">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="ed8ed-190">Azure Storage</span></span>

<span data-ttu-id="ed8ed-191">L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications Blazor sans serveur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-191">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ed8ed-192">Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-192">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ed8ed-193">Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-193">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ed8ed-194">Définissez le **nom du document d’index** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-194">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ed8ed-195">Définissez le **chemin d’accès au document d’erreur** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-195">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="ed8ed-196">Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-196">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ed8ed-197">Lorsqu’une demande pour l’une de ces ressources est reçue que le routeur Blazor doit gérer, l’erreur *404-introuvable* générée par le service BLOB achemine la requête vers le **chemin d’accès du document d’erreur**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-197">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ed8ed-198">L’objet BLOB *index. html* est retourné et le routeur Blazor charge et traite le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-198">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ed8ed-199">Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-199">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ed8ed-200">Nginx</span><span class="sxs-lookup"><span data-stu-id="ed8ed-200">Nginx</span></span>

<span data-ttu-id="ed8ed-201">Le fichier *nginx.conf* suivant est simplifié afin de montrer comment configurer Nginx pour envoyer le fichier *index.html* chaque fois qu’il ne trouve aucun fichier correspondant sur le disque.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-201">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="ed8ed-202">Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-202">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ed8ed-203">Nginx dans Docker</span><span class="sxs-lookup"><span data-stu-id="ed8ed-203">Nginx in Docker</span></span>

<span data-ttu-id="ed8ed-204">Pour héberger des Blazor dans l’Ancreur à l’aide de nginx, configurez le fichier dockerfile pour utiliser l’image Nginx alpine.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-204">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ed8ed-205">Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-205">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ed8ed-206">Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-206">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ed8ed-207">Apache</span><span class="sxs-lookup"><span data-stu-id="ed8ed-207">Apache</span></span>

<span data-ttu-id="ed8ed-208">Pour déployer une application Blazor webassembly sur CentOS 7 ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-208">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ed8ed-209">Créez le fichier de configuration Apache.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-209">Create the Apache configuration file.</span></span> <span data-ttu-id="ed8ed-210">L’exemple suivant est un fichier de configuration simplifié (*blazorapp. config*) :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-210">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="ed8ed-211">Placez le fichier de configuration Apache dans le répertoire `/etc/httpd/conf.d/`, qui est le répertoire de configuration Apache par défaut dans CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-211">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ed8ed-212">Placez les fichiers de l’application dans le répertoire `/var/www/blazorapp` (l’emplacement spécifié pour `DocumentRoot` dans le fichier de configuration).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-212">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ed8ed-213">Redémarrez le service Apache.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-213">Restart the Apache service.</span></span>

<span data-ttu-id="ed8ed-214">Pour plus d’informations, consultez [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-214">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ed8ed-215">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="ed8ed-215">GitHub Pages</span></span>

<span data-ttu-id="ed8ed-216">Pour gérer les réécritures d’URL, ajoutez un fichier *404.html* avec un script qui gère la redirection de la requête vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-216">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ed8ed-217">Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-217">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ed8ed-218">Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-218">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ed8ed-219">Quand vous utilisez un site de projet plutôt qu’un site d’entreprise, ajoutez ou mettez à jour la balise `<base>` dans *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-219">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ed8ed-220">Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-220">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ed8ed-221">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="ed8ed-221">Host configuration values</span></span>

<span data-ttu-id="ed8ed-222">[Blazor applications Webassembly](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-222">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ed8ed-223">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="ed8ed-223">Content root</span></span>

<span data-ttu-id="ed8ed-224">L’argument `--contentroot` définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-224">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ed8ed-225">Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-225">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ed8ed-226">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-226">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ed8ed-227">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-227">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ed8ed-228">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-228">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ed8ed-229">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-229">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ed8ed-230">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > **Arguments d’application**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-230">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ed8ed-231">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-231">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ed8ed-232">Base du chemin</span><span class="sxs-lookup"><span data-stu-id="ed8ed-232">Path base</span></span>

<span data-ttu-id="ed8ed-233">L’argument `--pathbase` définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL relatif non racine (la balise `<base>` `href` est définie sur un chemin d’accès autre que `/` pour la mise en lots et la production).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-233">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ed8ed-234">Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-234">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ed8ed-235">Pour plus d’informations, consultez chemin de la base de l' [application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ed8ed-235">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ed8ed-236">Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-236">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ed8ed-237">Si vous spécifiez `<base>` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base href="/CoolApp/">`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-237">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ed8ed-238">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-238">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ed8ed-239">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-239">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ed8ed-240">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-240">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ed8ed-241">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-241">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ed8ed-242">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > **Arguments d’application**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-242">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ed8ed-243">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-243">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ed8ed-244">URLs</span><span class="sxs-lookup"><span data-stu-id="ed8ed-244">URLs</span></span>

<span data-ttu-id="ed8ed-245">L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-245">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ed8ed-246">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ed8ed-247">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ed8ed-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ed8ed-248">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ed8ed-249">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ed8ed-250">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > **Arguments d’application**.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ed8ed-251">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ed8ed-252">Configurer l'éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="ed8ed-252">Configure the Linker</span></span>

Blazor<span data-ttu-id="ed8ed-253"> effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-253"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ed8ed-254">Pour plus d’informations, consultez <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="ed8ed-254">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
