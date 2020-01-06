---
title: Outils de conteneur Visual Studio avec ASP.NET Core
author: spboyer
description: Découvrez comment utiliser les outils Visual Studio et Docker pour Windows pour mettre une application ASP.NET Core dans un conteneur.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: cecec09d3d58a84a7c15630e41d23facdb9b236e
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75356153"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="3c982-103">Outils de conteneur Visual Studio avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c982-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="3c982-104">Visual Studio 2017 et versions ultérieures prennent en charge la génération, le débogage et l’exécution d’applications ASP.NET Core conteneurisées ciblant .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c982-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="3c982-105">Les conteneurs Windows et Linux sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="3c982-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="3c982-106">[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3c982-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c982-107">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="3c982-107">Prerequisites</span></span>

* [<span data-ttu-id="3c982-108">Docker pour Windows</span><span class="sxs-lookup"><span data-stu-id="3c982-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="3c982-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail **Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="3c982-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="3c982-110">Installation et configuration</span><span class="sxs-lookup"><span data-stu-id="3c982-110">Installation and setup</span></span>

<span data-ttu-id="3c982-111">Pour l’installation Docker, commencez par passer en revue les informations contenues dans [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span><span class="sxs-lookup"><span data-stu-id="3c982-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="3c982-112">Ensuite, installez [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="3c982-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="3c982-113">Il est nécessaire de configurer les **[lecteurs partagés](https://docs.docker.com/docker-for-windows/#shared-drives)** dans Docker pour Windows pour prendre en charge le mappage de volume et le débogage.</span><span class="sxs-lookup"><span data-stu-id="3c982-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="3c982-114">Cliquez avec le bouton droit sur l’icône Docker de la zone de notification, sélectionnez **Paramètres**, puis **Lecteurs partagés**.</span><span class="sxs-lookup"><span data-stu-id="3c982-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="3c982-115">Sélectionnez le lecteur où Docker stocke les fichiers.</span><span class="sxs-lookup"><span data-stu-id="3c982-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="3c982-116">Cliquez sur **Appliquer**.</span><span class="sxs-lookup"><span data-stu-id="3c982-116">Click **Apply**.</span></span>

![Boîte de dialogue où est sélectionné le partage de lecteur C local pour les conteneurs](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="3c982-118">Visual Studio 2017 versions 15.6 et ultérieures sollicitent l’utilisateur quand les **lecteurs partagés** ne sont pas configurés.</span><span class="sxs-lookup"><span data-stu-id="3c982-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="3c982-119">Ajouter un projet à un conteneur Docker</span><span class="sxs-lookup"><span data-stu-id="3c982-119">Add a project to a Docker container</span></span>

<span data-ttu-id="3c982-120">Pour mettre en conteneur un projet ASP.NET Core, le projet doit cibler .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c982-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="3c982-121">Les conteneurs Linux et Windows sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="3c982-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="3c982-122">Quand vous ajoutez la prise en charge de Docker à un projet, choisissez un conteneur Windows ou Linux.</span><span class="sxs-lookup"><span data-stu-id="3c982-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="3c982-123">L’hôte Docker doit exécuter le même type de conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="3c982-124">Pour changer le type de conteneur dans l’instance de Docker en cours d’exécution, cliquez avec le bouton droit sur l’icône Docker de la zone de notification, puis choisissez **Basculer vers les conteneurs Windows...** ou **Basculer vers les conteneurs Linux...** .</span><span class="sxs-lookup"><span data-stu-id="3c982-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="3c982-125">Nouvelle application</span><span class="sxs-lookup"><span data-stu-id="3c982-125">New app</span></span>

<span data-ttu-id="3c982-126">Quand vous créez une application avec les modèles de projet **Application web ASP.NET Core**, cochez la case **Activer la prise en charge de Docker** :</span><span class="sxs-lookup"><span data-stu-id="3c982-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Case Activer la prise en charge de Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="3c982-128">Si la version cible du .NET Framework est .NET Core, la liste déroulante **OS** (SE) permet la sélection d’un type de conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="3c982-129">Application existante</span><span class="sxs-lookup"><span data-stu-id="3c982-129">Existing app</span></span>

<span data-ttu-id="3c982-130">Pour les projets ASP.NET Core ciblant .NET Core, il existe deux options pour ajouter la prise en charge de Docker via les outils.</span><span class="sxs-lookup"><span data-stu-id="3c982-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="3c982-131">Ouvrez le projet dans Visual Studio et choisissez l’une des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="3c982-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="3c982-132">Sélectionnez **Prise en charge de Docker** dans le menu **Projet**.</span><span class="sxs-lookup"><span data-stu-id="3c982-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="3c982-133">Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez **Ajouter** > **Prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="3c982-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="3c982-134">Les outils de conteneur Visual Studio ne prennent pas en charge l’ajout de Docker à un projet ASP.NET Core existant ciblant le .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3c982-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="3c982-135">Vue d’ensemble du fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="3c982-135">Dockerfile overview</span></span>

<span data-ttu-id="3c982-136">Un fichier *Dockerfile*, la recette de la création d’une image Docker finale, est ajouté à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="3c982-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="3c982-137">Reportez-vous à [Informations de référence sur Dockerfile](https://docs.docker.com/engine/reference/builder/) pour comprendre les commandes qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="3c982-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="3c982-138">Ce fichier *Dockerfile* spécifique utilise une [build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) avec quatre différentes étapes de build nommées :</span><span class="sxs-lookup"><span data-stu-id="3c982-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="3c982-139">Le fichier *Dockerfile* précédent est basé sur l’image [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="3c982-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="3c982-140">Cette image de base comprend le runtime ASP.NET Core et des packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="3c982-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="3c982-141">Les packages sont compilés juste-à-temps (JIT) pour améliorer les performances de démarrage.</span><span class="sxs-lookup"><span data-stu-id="3c982-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="3c982-142">Quand la case **Configurer pour HTTPS** de la boîte de dialogue du nouveau projet est cochée, le fichier *Dockerfile* expose deux ports.</span><span class="sxs-lookup"><span data-stu-id="3c982-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="3c982-143">Un port est utilisé pour le trafic HTTP tandis que l’autre est utilisé pour HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3c982-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="3c982-144">Si la case n’est pas cochée, un seul port (80) est exposé pour le trafic HTTP.</span><span class="sxs-lookup"><span data-stu-id="3c982-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="3c982-145">Le fichier *Dockerfile* précédent est basé sur l’image [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/).</span><span class="sxs-lookup"><span data-stu-id="3c982-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="3c982-146">Cette image de base comprend les packages NuGet ASP.NET Core, qui ont été compilés juste-à-temps (JIT) pour améliorer les performances de démarrage.</span><span class="sxs-lookup"><span data-stu-id="3c982-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="3c982-147">Ajouter la prise en charge des orchestrateurs de conteneurs à une application</span><span class="sxs-lookup"><span data-stu-id="3c982-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="3c982-148">Visual Studio 2017 version 15.7 ou antérieure prend en charge [Docker Compose](https://docs.docker.com/compose/overview/) en tant que solution d’orchestration de conteneurs unique.</span><span class="sxs-lookup"><span data-stu-id="3c982-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="3c982-149">Les artefacts Docker Compose sont ajoutés via **Ajouter** > **Prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="3c982-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="3c982-150">Visual Studio 2017 version 15.8 ou ultérieure ajoute une solution d’orchestration seulement si cela lui est demandé.</span><span class="sxs-lookup"><span data-stu-id="3c982-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="3c982-151">Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez **Ajouter** > **Prise en charge de l’orchestrateur de conteneurs**.</span><span class="sxs-lookup"><span data-stu-id="3c982-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="3c982-152">Deux choix différents sont proposés : [Docker Compose](#docker-compose) et [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="3c982-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="3c982-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="3c982-153">Docker Compose</span></span>

<span data-ttu-id="3c982-154">Les outils de conteneur Visual Studio ajoutent un projet *docker-compose* à la solution avec les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="3c982-154">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="3c982-155">*dockr-compose. dcproj* &ndash; le fichier représentant le projet.</span><span class="sxs-lookup"><span data-stu-id="3c982-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="3c982-156">Comprend un élément `<DockerTargetOS>` spécifiant le système d’exploitation à utiliser.</span><span class="sxs-lookup"><span data-stu-id="3c982-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="3c982-157">*. dockerignore* &ndash; répertorie les modèles de fichiers et de répertoires à exclure lors de la génération d’un contexte de génération.</span><span class="sxs-lookup"><span data-stu-id="3c982-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="3c982-158">*docker-compose. yml* &ndash; fichier de [docker compose](https://docs.docker.com/compose/overview/) de base utilisé pour définir la collection d’images générées et exécutées avec `docker-compose build` et `docker-compose run`, respectivement.</span><span class="sxs-lookup"><span data-stu-id="3c982-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="3c982-159">*docker-compose. override. yml* &ndash; un fichier facultatif, lu par docker compose, avec des remplacements de configuration pour les services.</span><span class="sxs-lookup"><span data-stu-id="3c982-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="3c982-160">Visual Studio exécute `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` pour fusionner ces fichiers.</span><span class="sxs-lookup"><span data-stu-id="3c982-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="3c982-161">Le fichier *docker-compose.yml* référence le nom de l’image créée pendant l’exécution du projet :</span><span class="sxs-lookup"><span data-stu-id="3c982-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="3c982-162">Dans l’exemple précédent, `image: hellodockertools` génère l’image `hellodockertools:dev` quand l’application est exécutée en mode **débogage**.</span><span class="sxs-lookup"><span data-stu-id="3c982-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="3c982-163">L’image `hellodockertools:latest` est générée quand l’application est exécutée en mode **mise en production**.</span><span class="sxs-lookup"><span data-stu-id="3c982-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="3c982-164">En guise de préfixe, ajoutez au nom de l’image le nom d’utilisateur [Docker Hub](https://hub.docker.com/) (par exemple, `dockerhubusername/hellodockertools`) si l’image est destinée à être envoyée (push) au Registre.</span><span class="sxs-lookup"><span data-stu-id="3c982-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="3c982-165">Vous pouvez aussi changer le nom de l’image pour inclure l’URL de Registre privé (par exemple, `privateregistry.domain.com/hellodockertools`) en fonction de la configuration.</span><span class="sxs-lookup"><span data-stu-id="3c982-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="3c982-166">Si vous souhaitez un autre comportement basé sur la configuration de build (par exemple, Debug ou Release), ajoutez des fichiers *docker-compose* propres à la configuration.</span><span class="sxs-lookup"><span data-stu-id="3c982-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="3c982-167">Les fichiers doivent être nommés en fonction de la configuration de build (par exemple, *docker-compose.vs.debug.yml* et *docker-compose.vs.release.yml*), et placés dans le même emplacement que le fichier *docker-compose-override.yml*.</span><span class="sxs-lookup"><span data-stu-id="3c982-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="3c982-168">À l’aide des fichiers de substitution spécifiques à la configuration, vous pouvez spécifier différents paramètres de configuration (par exemple, des variables d’environnement ou des points d’entrée) pour les configurations de build Debug et Release.</span><span class="sxs-lookup"><span data-stu-id="3c982-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="3c982-169">Par Docker Compose pour afficher une option à exécuter dans Visual Studio, le projet d’ancrage doit être le projet de démarrage.</span><span class="sxs-lookup"><span data-stu-id="3c982-169">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="3c982-170">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="3c982-170">Service Fabric</span></span>

<span data-ttu-id="3c982-171">En plus des [prérequis](#prerequisites) de base, la solution d’orchestration [Service Fabric](/azure/service-fabric/) impose les prérequis suivants :</span><span class="sxs-lookup"><span data-stu-id="3c982-171">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="3c982-172">[Kit SDK Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="3c982-172">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="3c982-173">Charge de travail **Développement Azure** de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c982-173">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="3c982-174">Service Fabric ne prend pas en charge les conteneurs Linux s’exécutant dans le cluster de développement local sur Windows.</span><span class="sxs-lookup"><span data-stu-id="3c982-174">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="3c982-175">Si le projet utilise déjà un conteneur Linux, Visual Studio vous invite à basculer vers des conteneurs Windows.</span><span class="sxs-lookup"><span data-stu-id="3c982-175">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="3c982-176">Les outils de conteneur Visual Studio effectuent les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="3c982-176">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="3c982-177">Ajoute une *&lt;project_name&gt;* application service Fabric projet d' **application** à la solution.</span><span class="sxs-lookup"><span data-stu-id="3c982-177">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="3c982-178">Ajoute un fichier *Dockerfile* et un fichier *.dockerignore* au projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c982-178">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="3c982-179">S’il existe déjà un fichier *Dockerfile* dans le projet ASP.NET Core, il est renommé *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="3c982-179">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="3c982-180">Un nouveau fichier *Dockerfile*, semblable au suivant, est créé :</span><span class="sxs-lookup"><span data-stu-id="3c982-180">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="3c982-181">Ajoute un élément `<IsServiceFabricServiceProject>` au fichier *.csproj* du projet ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="3c982-181">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="3c982-182">Ajoute un dossier *PackageRoot* au projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c982-182">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="3c982-183">Le dossier comprend le manifeste de service et les paramètres du nouveau service.</span><span class="sxs-lookup"><span data-stu-id="3c982-183">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="3c982-184">Pour plus d’informations, consultez [Déployer une application .NET dans un conteneur Windows vers Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="3c982-184">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="3c982-185">Déboguer</span><span class="sxs-lookup"><span data-stu-id="3c982-185">Debug</span></span>

<span data-ttu-id="3c982-186">Sélectionnez **Docker** dans la liste déroulante de débogage dans la barre d’outils et démarrez le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="3c982-186">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="3c982-187">La vue **Docker** de la fenêtre **Sortie** affiche les actions suivantes qui se déroulent :</span><span class="sxs-lookup"><span data-stu-id="3c982-187">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="3c982-188">La balise *2.1-aspnetcore-runtime* de l’image de runtime *microsoft/dotnet* est acquise (si elle ne se trouve pas déjà dans le cache).</span><span class="sxs-lookup"><span data-stu-id="3c982-188">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="3c982-189">L’image installe les runtimes ASP.NET Core et .NET Core et les bibliothèques associées.</span><span class="sxs-lookup"><span data-stu-id="3c982-189">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="3c982-190">Elle est optimisée pour l’exécution des applications ASP.NET Core en production.</span><span class="sxs-lookup"><span data-stu-id="3c982-190">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="3c982-191">La variable d’environnement `ASPNETCORE_ENVIRONMENT` a la valeur `Development` dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-191">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="3c982-192">Deux ports attribués dynamiquement sont exposés : un pour HTTP et l’autre pour HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3c982-192">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="3c982-193">Le port attribué à localhost peut être interrogé avec la commande `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="3c982-193">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="3c982-194">L’application est copiée dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-194">The app is copied to the container.</span></span>
* <span data-ttu-id="3c982-195">Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port attribué dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="3c982-195">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="3c982-196">L’image Docker obtenue de l’application est marquée avec la balise *dev*.</span><span class="sxs-lookup"><span data-stu-id="3c982-196">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="3c982-197">L’image est basée sur la balise *2.1-aspnetcore-runtime* de l’image de base *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="3c982-197">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="3c982-198">Exécutez la commande `docker images` dans la fenêtre **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="3c982-198">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="3c982-199">Les images sur la machine s’affichent :</span><span class="sxs-lookup"><span data-stu-id="3c982-199">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="3c982-200">L’image d’exécution *microsoft/aspnetcore* est acquise (si elle n’est pas déjà dans le cache).</span><span class="sxs-lookup"><span data-stu-id="3c982-200">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="3c982-201">La variable d’environnement `ASPNETCORE_ENVIRONMENT` a la valeur `Development` dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-201">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="3c982-202">Le port 80 est exposé et mappé à un port attribué dynamiquement pour localhost.</span><span class="sxs-lookup"><span data-stu-id="3c982-202">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="3c982-203">Le port est déterminé par l’hôte Docker et peut être interrogé avec la commande `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="3c982-203">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="3c982-204">L’application est copiée dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-204">The app is copied to the container.</span></span>
* <span data-ttu-id="3c982-205">Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port attribué dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="3c982-205">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="3c982-206">L’image Docker obtenue de l’application est marquée avec la balise *dev*.</span><span class="sxs-lookup"><span data-stu-id="3c982-206">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="3c982-207">L’image est basée sur l’image de base *microsoft/aspnetcore*.</span><span class="sxs-lookup"><span data-stu-id="3c982-207">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="3c982-208">Exécutez la commande `docker images` dans la fenêtre **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="3c982-208">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="3c982-209">Les images sur la machine s’affichent :</span><span class="sxs-lookup"><span data-stu-id="3c982-209">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3c982-210">L’image *dev* n’inclut pas le contenu de l’application, car les configurations **Debug** utilisent le montage de volume pour fournir l’expérience itérative.</span><span class="sxs-lookup"><span data-stu-id="3c982-210">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="3c982-211">Pour placer une image, utilisez la configuration **release**.</span><span class="sxs-lookup"><span data-stu-id="3c982-211">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="3c982-212">Exécutez la commande `docker ps` dans la console du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="3c982-212">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="3c982-213">Notez que l’application s’exécute à l’aide du conteneur :</span><span class="sxs-lookup"><span data-stu-id="3c982-213">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="3c982-214">Modifier & Continuer</span><span class="sxs-lookup"><span data-stu-id="3c982-214">Edit and continue</span></span>

<span data-ttu-id="3c982-215">Les modifications apportées à des fichiers statiques et à des vues Razor sont automatiquement mises à jour sans qu’une étape de compilation ne soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3c982-215">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="3c982-216">Apportez la modification, enregistrez et actualisez le navigateur pour afficher la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="3c982-216">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="3c982-217">Les modifications de fichiers de code nécessitent une compilation et un redémarrage de Kestrel au sein du conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-217">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="3c982-218">Après avoir effectué la modification, utilisez `CTRL+F5` pour exécuter le processus et démarrer l’application au sein du conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-218">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="3c982-219">Le conteneur Docker n’est pas regénéré ni arrêté.</span><span class="sxs-lookup"><span data-stu-id="3c982-219">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="3c982-220">Exécutez la commande `docker ps` dans la console du Gestionnaire de package.</span><span class="sxs-lookup"><span data-stu-id="3c982-220">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="3c982-221">Notez que le conteneur d’origine est toujours en cours d’exécution comme 10 minutes auparavant :</span><span class="sxs-lookup"><span data-stu-id="3c982-221">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="3c982-222">Publier des images Docker</span><span class="sxs-lookup"><span data-stu-id="3c982-222">Publish Docker images</span></span>

<span data-ttu-id="3c982-223">Une fois terminé le cycle de développement et de débogage de l’application, les outils de conteneur Visual Studio aident à créer l’image de production de l’application.</span><span class="sxs-lookup"><span data-stu-id="3c982-223">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="3c982-224">Changez la liste déroulante de configuration en **Release** et générez l’application.</span><span class="sxs-lookup"><span data-stu-id="3c982-224">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="3c982-225">Les outils obtiennent l’image de compilation/publication auprès de Docker Hub (si elle ne se trouve pas déjà dans le cache).</span><span class="sxs-lookup"><span data-stu-id="3c982-225">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="3c982-226">Une image est produite avec la balise *latest*, qui peut être envoyée (push) au Registre privé ou à Docker Hub.</span><span class="sxs-lookup"><span data-stu-id="3c982-226">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="3c982-227">Exécutez la commande `docker images` dans la console du Gestionnaire de package pour afficher la liste des images.</span><span class="sxs-lookup"><span data-stu-id="3c982-227">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="3c982-228">Une sortie similaire à la suivante s’affiche à l’écran :</span><span class="sxs-lookup"><span data-stu-id="3c982-228">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="3c982-229">Les images `microsoft/aspnetcore-build` et `microsoft/aspnetcore` répertoriées dans la sortie précédente sont remplacées par des images `microsoft/dotnet` à compter de .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="3c982-229">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="3c982-230">Pour plus d’informations, consultez [l’annonce de migration des dépôts Docker](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="3c982-230">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3c982-231">La commande `docker images` retourne des images intermédiaires avec des noms de dépôt et des balises identifiées comme *\<none>* (non répertoriées ci-dessus).</span><span class="sxs-lookup"><span data-stu-id="3c982-231">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="3c982-232">Ces images sans nom sont produites par le *fichier dockerfile*de [Build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) .</span><span class="sxs-lookup"><span data-stu-id="3c982-232">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="3c982-233">Elles améliorent l’efficacité de la création de l’image finale ; seules les couches nécessaires sont regénérées en cas de modifications.</span><span class="sxs-lookup"><span data-stu-id="3c982-233">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="3c982-234">Quand les images intermédiaires ne sont plus nécessaires, supprimez-les à l’aide de la commande [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).</span><span class="sxs-lookup"><span data-stu-id="3c982-234">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="3c982-235">Vous pourriez vous attendre à ce que l’image de production ou de publication ait une taille inférieure à l’image de *développement*.</span><span class="sxs-lookup"><span data-stu-id="3c982-235">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="3c982-236">En raison de l’utilisation du mappage de volume, le débogueur et l’application étaient exécutés à partir de l’ordinateur local et non dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3c982-236">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="3c982-237">L’image *latest* a empaqueté le code de l’application nécessaire pour l’exécuter sur un ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="3c982-237">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="3c982-238">Le delta correspond donc à la taille du code de l’application.</span><span class="sxs-lookup"><span data-stu-id="3c982-238">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c982-239">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3c982-239">Additional resources</span></span>

* [<span data-ttu-id="3c982-240">Développement de conteneurs avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c982-240">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="3c982-241">Azure Service Fabric : Préparer votre environnement de développement</span><span class="sxs-lookup"><span data-stu-id="3c982-241">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="3c982-242">Déployer une application .NET dans un conteneur Windows vers Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="3c982-242">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="3c982-243">Détecter un problème de développement Visual Studio avec Docker</span><span class="sxs-lookup"><span data-stu-id="3c982-243">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="3c982-244">Référentiel GitHub des outils de conteneur Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c982-244">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
