---
title: Outils pour ASP.NET CoreBlazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 33245e669b317ed577a8a1652b2eed8f9ea5b915
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407643"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="658ed-103">Outils pour ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="658ed-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="658ed-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="658ed-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="658ed-105">Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="658ed-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="658ed-106">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="658ed-106">Create a new project.</span></span>

1. <span data-ttu-id="658ed-107">Sélectionnez \*\* Blazor application\*\*.</span><span class="sxs-lookup"><span data-stu-id="658ed-107">Select **Blazor App**.</span></span> <span data-ttu-id="658ed-108">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="658ed-108">Select **Next**.</span></span>

1. <span data-ttu-id="658ed-109">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="658ed-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="658ed-110">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="658ed-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="658ed-111">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="658ed-111">Select **Create**.</span></span>

1. <span data-ttu-id="658ed-112">Pour une Blazor WebAssembly expérience, choisissez le modèle d' \*\* Blazor WebAssembly application\*\* .</span><span class="sxs-lookup"><span data-stu-id="658ed-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="658ed-113">Pour une Blazor Server expérience, choisissez le modèle d' \*\* Blazor Server application\*\* .</span><span class="sxs-lookup"><span data-stu-id="658ed-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="658ed-114">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="658ed-114">Select **Create**.</span></span>

   <span data-ttu-id="658ed-115">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="658ed-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="658ed-116">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="658ed-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="658ed-117">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="658ed-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="658ed-118">Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="658ed-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="658ed-119">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="658ed-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="658ed-120">Installez la dernière version de [Visual Studio code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="658ed-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="658ed-121">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="658ed-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="658ed-122">Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="658ed-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="658ed-123">Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="658ed-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="658ed-124">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="658ed-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="658ed-125">Ouvrez le dossier `WebApplication1` dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="658ed-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="658ed-126">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="658ed-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="658ed-127">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="658ed-127">Select **Yes**.</span></span>

1. <span data-ttu-id="658ed-128">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="658ed-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="658ed-129">Approuver un certificat de développement</span><span class="sxs-lookup"><span data-stu-id="658ed-129">Trust a development certificate</span></span>

<span data-ttu-id="658ed-130">Il n’existe pas de méthode centralisée pour approuver un certificat sur Linux.</span><span class="sxs-lookup"><span data-stu-id="658ed-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="658ed-131">En règle générale, l’une des approches suivantes est adoptée :</span><span class="sxs-lookup"><span data-stu-id="658ed-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="658ed-132">Excluez l’URL de l’application dans la liste d’exclusion du navigateur.</span><span class="sxs-lookup"><span data-stu-id="658ed-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="658ed-133">Faites confiance à tous les certificats auto-signés pour `localhost` .</span><span class="sxs-lookup"><span data-stu-id="658ed-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="658ed-134">Ajoutez le certificat à la liste des certificats approuvés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="658ed-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="658ed-135">Pour plus d’informations, reportez-vous aux conseils fournis par votre navigateur et votre distribution Linux.</span><span class="sxs-lookup"><span data-stu-id="658ed-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="658ed-136">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="658ed-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="658ed-137">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="658ed-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="658ed-138">Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="658ed-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="658ed-139">Pour une Blazor WebAssembly expérience, choisissez le modèle d' \*\* Blazor WebAssembly application\*\* .</span><span class="sxs-lookup"><span data-stu-id="658ed-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="658ed-140">Pour une Blazor Server expérience, choisissez le modèle d' \*\* Blazor Server application\*\* .</span><span class="sxs-lookup"><span data-stu-id="658ed-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="658ed-141">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="658ed-141">Select **Next**.</span></span>

   <span data-ttu-id="658ed-142">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="658ed-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="658ed-143">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="658ed-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="658ed-144">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="658ed-144">Select **Next**.</span></span>

1. <span data-ttu-id="658ed-145">Dans le champ **nom du projet** , nommez l’application `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="658ed-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="658ed-146">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="658ed-146">Select **Create**.</span></span>

1. <span data-ttu-id="658ed-147">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="658ed-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="658ed-148">Exécutez l’application avec **Run**  >  le bouton exécuter**Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="658ed-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="658ed-149">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="658ed-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="658ed-150">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="658ed-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="658ed-151">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="658ed-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end