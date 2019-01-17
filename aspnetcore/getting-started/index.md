---
title: Bien démarrer avec ASP.NET Core
author: rick-anderson
description: Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
ms.openlocfilehash: eee927f4306fa7757f3f361e6c6f367658512897
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341808"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="d7bda-103">Tutoriel : Bien démarrer avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7bda-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="d7bda-104">Ce tutoriel montre comment utiliser l’interface de ligne de commande .NET Core pour créer une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7bda-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="d7bda-105">Vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="d7bda-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7bda-106">Créer un projet application web.</span><span class="sxs-lookup"><span data-stu-id="d7bda-106">Create a web app project.</span></span>
> * <span data-ttu-id="d7bda-107">Activer HTTPS localement.</span><span class="sxs-lookup"><span data-stu-id="d7bda-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="d7bda-108">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="d7bda-108">Run the app.</span></span>
> * <span data-ttu-id="d7bda-109">Modifier une page Razor.</span><span class="sxs-lookup"><span data-stu-id="d7bda-109">Edit a Razor page.</span></span>

<span data-ttu-id="d7bda-110">À la fin du tutoriel, vous disposerez d’une application web qui fonctionne et s’exécute sur votre machine locale.</span><span class="sxs-lookup"><span data-stu-id="d7bda-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Page d’accueil d’application web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="d7bda-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d7bda-112">Prerequisites</span></span>

* [<span data-ttu-id="d7bda-113">SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="d7bda-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="d7bda-114">Créer un projet application web</span><span class="sxs-lookup"><span data-stu-id="d7bda-114">Create a web app project</span></span>

<span data-ttu-id="d7bda-115">Ouvrez un interpréteur de commandes, puis entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d7bda-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="d7bda-116">Activer HTTPS localement</span><span class="sxs-lookup"><span data-stu-id="d7bda-116">Enable local HTTPS</span></span>

<span data-ttu-id="d7bda-117">Approuvez le certificat de développement HTTPS :</span><span class="sxs-lookup"><span data-stu-id="d7bda-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="d7bda-118">Fenêtres</span><span class="sxs-lookup"><span data-stu-id="d7bda-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="d7bda-119">La commande précédente affiche la boîte de dialogue suivante :</span><span class="sxs-lookup"><span data-stu-id="d7bda-119">The preceding command displays the following dialog:</span></span>

![Boîte de dialogue Avertissement de sécurité](_static/cert.png)

<span data-ttu-id="d7bda-121">Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d7bda-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="d7bda-122">macOS</span><span class="sxs-lookup"><span data-stu-id="d7bda-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="d7bda-123">La commande précédente affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="d7bda-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="d7bda-124">*L’approbation du certificat de développement HTTPS a été demandée. Si le certificat n’a pas encore été approuvé, nous exécutons la commande suivante :* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="d7bda-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>  
<span data-ttu-id="d7bda-125">\* Cette commande peut vous demander votre mot de passe afin d’installer le certificat sur le trousseau système.</span><span class="sxs-lookup"><span data-stu-id="d7bda-125">\*This command might prompt you for your password to install the certificate on the system keychain.</span></span>

<span data-ttu-id="d7bda-126">Mot de passe :\*</span><span class="sxs-lookup"><span data-stu-id="d7bda-126">Password:\*</span></span>

<span data-ttu-id="d7bda-127">Entrez votre mot de passe si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="d7bda-127">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d7bda-128">Linux</span><span class="sxs-lookup"><span data-stu-id="d7bda-128">Linux</span></span>](#tab/linux)

<span data-ttu-id="d7bda-129">Consultez la documentation de votre distribution Linux pour savoir comment approuver le certificat de développement HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7bda-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

## <a name="run-the-app"></a><span data-ttu-id="d7bda-130">Exécuter l'application</span><span class="sxs-lookup"><span data-stu-id="d7bda-130">Run the app</span></span>

<span data-ttu-id="d7bda-131">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="d7bda-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="d7bda-132">Une fois que l’interface de commande indique que l’application a démarré, accédez à [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="d7bda-132">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="d7bda-133">Cliquez sur **Accepter** pour accepter la politique de confidentialité et de cookies.</span><span class="sxs-lookup"><span data-stu-id="d7bda-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="d7bda-134">Cette application ne conserve pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="d7bda-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="d7bda-135">Modifier une page Razor</span><span class="sxs-lookup"><span data-stu-id="d7bda-135">Edit a Razor page</span></span>

<span data-ttu-id="d7bda-136">Ouvrez *Pages/Index.cshtml* et modifiez la page avec le balisage mis en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="d7bda-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="d7bda-137">Accédez à [https://localhost:5001](https://localhost:5001) et vérifiez que les changements sont affichés.</span><span class="sxs-lookup"><span data-stu-id="d7bda-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7bda-138">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d7bda-138">Next steps</span></span>

<span data-ttu-id="d7bda-139">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="d7bda-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7bda-140">Créer un projet application web.</span><span class="sxs-lookup"><span data-stu-id="d7bda-140">Create a web app project.</span></span>
> * <span data-ttu-id="d7bda-141">Activer HTTPS localement.</span><span class="sxs-lookup"><span data-stu-id="d7bda-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="d7bda-142">Exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="d7bda-142">Run the project.</span></span>
> * <span data-ttu-id="d7bda-143">Apportez la modification souhaitée.</span><span class="sxs-lookup"><span data-stu-id="d7bda-143">Make a change.</span></span>

<span data-ttu-id="d7bda-144">Pour en savoir plus sur ASP.NET Core, consultez la présentation :</span><span class="sxs-lookup"><span data-stu-id="d7bda-144">To learn more about ASP.NET Core, see the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index>
