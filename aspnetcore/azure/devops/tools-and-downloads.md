---
title: Outils et téléchargements - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Outils et téléchargements sont nécessaires pour les opérations de développement avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659487"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="a87c1-103">Outils et téléchargements</span><span class="sxs-lookup"><span data-stu-id="a87c1-103">Tools and downloads</span></span>

<span data-ttu-id="a87c1-104">Azure dispose de plusieurs interfaces pour la configuration et la gestion des ressources, telles que les [portail Azure](https://portal.azure.com), les [Azure CLI](/cli/azure/), les [Azure PowerShell](/powershell/azure/overview), les [Azure Cloud Shell](https://shell.azure.com/bash)et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a87c1-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="a87c1-105">Ce guide adopte une approche minimaliste et utilise Azure Cloud Shell autant que possible afin de réduire les étapes requises.</span><span class="sxs-lookup"><span data-stu-id="a87c1-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="a87c1-106">Toutefois, le portail Azure doit être utilisé pour certaines parties.</span><span class="sxs-lookup"><span data-stu-id="a87c1-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a87c1-107">Conditions préalables requises</span><span class="sxs-lookup"><span data-stu-id="a87c1-107">Prerequisites</span></span>

<span data-ttu-id="a87c1-108">Les abonnements suivants sont requis :</span><span class="sxs-lookup"><span data-stu-id="a87c1-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="a87c1-109">&mdash; Azure si vous n’avez pas de compte, [procurez-vous une version d’évaluation gratuite](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="a87c1-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="a87c1-110">Azure DevOps Services &mdash; votre abonnement et votre organisation Azure DevOps sont créés dans le chapitre 4.</span><span class="sxs-lookup"><span data-stu-id="a87c1-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="a87c1-111">GitHub &mdash; si vous n’avez pas de compte, inscrivez-vous [gratuitement](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="a87c1-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="a87c1-112">Les outils suivants sont requis :</span><span class="sxs-lookup"><span data-stu-id="a87c1-112">The following tools are required:</span></span>

* <span data-ttu-id="a87c1-113">[Git](https://git-scm.com/downloads) &mdash; une compréhension fondamentale de git est recommandée pour ce guide.</span><span class="sxs-lookup"><span data-stu-id="a87c1-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="a87c1-114">Consultez la [documentation git](https://git-scm.com/doc), en particulier [git remote](https://git-scm.com/docs/git-remote) et [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="a87c1-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="a87c1-115">[Kit SDK .NET Core](https://www.microsoft.com/net/download/) &mdash; version 2.1.300 ou ultérieure est nécessaire pour générer et exécuter l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a87c1-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="a87c1-116">Si Visual Studio est installé avec la charge de travail de **développement multiplateforme .net Core** , le kit SDK .net Core est déjà installé.</span><span class="sxs-lookup"><span data-stu-id="a87c1-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="a87c1-117">Vérifiez votre installation du SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a87c1-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="a87c1-118">Ouvrez une invite de commandes et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a87c1-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="a87c1-119">Outils recommandés (Windows uniquement)</span><span class="sxs-lookup"><span data-stu-id="a87c1-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="a87c1-120">Les outils Azure robustes de [Visual Studio](https://visualstudio.microsoft.com)fournissent une interface utilisateur graphique pour la plupart des fonctionnalités décrites dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="a87c1-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="a87c1-121">N’importe quelle édition de Visual Studio fonctionnent, y compris l’édition Visual Studio Community gratuite.</span><span class="sxs-lookup"><span data-stu-id="a87c1-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="a87c1-122">Les didacticiels sont écrits pour illustrer le développement, déploiement et DevOps avec et sans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a87c1-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="a87c1-123">Vérifiez que les [charges de travail](/visualstudio/install/modify-visual-studio) suivantes sont installées sur Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="a87c1-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="a87c1-124">Développement web et ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a87c1-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="a87c1-125">Développement Azure</span><span class="sxs-lookup"><span data-stu-id="a87c1-125">Azure development</span></span>
  * <span data-ttu-id="a87c1-126">Développement multiplateforme .NET Core</span><span class="sxs-lookup"><span data-stu-id="a87c1-126">.NET Core cross-platform development</span></span>
