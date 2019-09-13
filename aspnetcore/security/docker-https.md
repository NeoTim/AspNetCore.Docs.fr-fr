---
title: Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs
author: rick-anderson
description: Découvrez comment héberger des images ASP.NET Core avec l’arrimeur sur HTTPs
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: security/docker-https
ms.openlocfilehash: f17a3abe1b00b39b7b6787be5b20ce65771190b8
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619694"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="5c955-103">Hébergement d’images ASP.NET Core avec l’arrimeur sur HTTPs</span><span class="sxs-lookup"><span data-stu-id="5c955-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="5c955-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c955-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5c955-105">ASP.NET Core utilise le [protocole HTTPS par défaut](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5c955-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="5c955-106">[Https](https://en.wikipedia.org/wiki/HTTPS) s’appuie sur des [certificats](https://en.wikipedia.org/wiki/Public_key_certificate) pour l’approbation, l’identité et le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="5c955-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="5c955-107">Ce document explique comment exécuter des images conteneur prégénérées avec HTTPs.</span><span class="sxs-lookup"><span data-stu-id="5c955-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="5c955-108">Consultez [développement d’Applications ASP.net core avec l’arrimeur sur https](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="5c955-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="5c955-109">Cet exemple requiert l' [ancrage 17,06](https://docs.docker.com/release-notes/docker-ce) ou une version ultérieure du [client dockr](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="5c955-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c955-110">Prérequis</span><span class="sxs-lookup"><span data-stu-id="5c955-110">Prerequisites</span></span>

<span data-ttu-id="5c955-111">Le [Kit de développement logiciel (SDK) .net Core 2,2](https://www.microsoft.com/net/download) ou version ultérieure est requis pour certaines des instructions contenues dans ce document.</span><span class="sxs-lookup"><span data-stu-id="5c955-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="5c955-112">Certificats</span><span class="sxs-lookup"><span data-stu-id="5c955-112">Certificates</span></span>

<span data-ttu-id="5c955-113">Un certificat d’une [autorité de certification](https://en.wikipedia.org/wiki/Certificate_authority) est requis pour l' [Hébergement de production](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) pour un domaine.</span><span class="sxs-lookup"><span data-stu-id="5c955-113">A certificate from a [certificate authority](https://en.wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span>  <span data-ttu-id="5c955-114">Le [chiffrement](https://letsencrypt.org/) est une autorité de certification qui offre des certificats gratuits.</span><span class="sxs-lookup"><span data-stu-id="5c955-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="5c955-115">Ce document utilise des [certificats de développement auto-signés](https://en.wikipedia.org/wiki/Self-signed_certificate) pour héberger des `localhost`images prégénérées sur.</span><span class="sxs-lookup"><span data-stu-id="5c955-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="5c955-116">Les instructions sont similaires à l’utilisation de certificats de production.</span><span class="sxs-lookup"><span data-stu-id="5c955-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="5c955-117">Pour les certificats de production :</span><span class="sxs-lookup"><span data-stu-id="5c955-117">For production certs:</span></span>

* <span data-ttu-id="5c955-118">L' `dotnet dev-certs` outil n’est pas requis.</span><span class="sxs-lookup"><span data-stu-id="5c955-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="5c955-119">Les certificats n’ont pas besoin d’être stockés à l’emplacement utilisé dans les instructions.</span><span class="sxs-lookup"><span data-stu-id="5c955-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="5c955-120">Tout emplacement doit fonctionner, bien que le stockage des certificats dans votre répertoire de site ne soit pas recommandé.</span><span class="sxs-lookup"><span data-stu-id="5c955-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="5c955-121">Instructions de montage de certificats de volume dans des conteneurs.</span><span class="sxs-lookup"><span data-stu-id="5c955-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="5c955-122">Vous pouvez ajouter des certificats dans des images de `COPY` conteneur à l’aide d’une commande dans un fichier dockerfile.</span><span class="sxs-lookup"><span data-stu-id="5c955-122">You can add certificates into container images with a `COPY` command in a Dockerfile.</span></span> <span data-ttu-id="5c955-123">La copie de certificats dans une image n’est pas recommandée :</span><span class="sxs-lookup"><span data-stu-id="5c955-123">Copying certificates into an image is not recommended:</span></span>

* <span data-ttu-id="5c955-124">Il est difficile d’utiliser la même image pour le test avec des certificats de développeur.</span><span class="sxs-lookup"><span data-stu-id="5c955-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="5c955-125">Il est difficile d’utiliser la même image pour l’hébergement avec des certificats de production.</span><span class="sxs-lookup"><span data-stu-id="5c955-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="5c955-126">Il y a un risque significatif de divulgation de certificats.</span><span class="sxs-lookup"><span data-stu-id="5c955-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="5c955-127">Exécution d’images conteneur prégénérées avec HTTPs</span><span class="sxs-lookup"><span data-stu-id="5c955-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="5c955-128">Utilisez les instructions suivantes pour la configuration de votre système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="5c955-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="5c955-129">Windows utilisant des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="5c955-129">Windows using Linux containers</span></span>

<span data-ttu-id="5c955-130">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="5c955-130">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5c955-131">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="5c955-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5c955-132">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="5c955-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5c955-133">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="5c955-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="5c955-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="5c955-134">macOS or Linux</span></span>

<span data-ttu-id="5c955-135">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="5c955-135">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5c955-136">`dotnet dev-certs https --trust`est pris en charge uniquement sur macOS et Windows.</span><span class="sxs-lookup"><span data-stu-id="5c955-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="5c955-137">Vous devez faire confiance aux certificats sur Linux de la manière prise en charge par votre distribution.</span><span class="sxs-lookup"><span data-stu-id="5c955-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="5c955-138">Il est probable que vous ayez besoin d’approuver le certificat dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="5c955-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="5c955-139">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="5c955-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5c955-140">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="5c955-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5c955-141">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="5c955-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="5c955-142">Windows utilisant des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="5c955-142">Windows using Windows containers</span></span>

<span data-ttu-id="5c955-143">Générer un certificat et configurer l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="5c955-143">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="5c955-144">Dans les commandes précédentes, remplacez `{ password here }` par un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="5c955-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="5c955-145">Exécutez l’image de conteneur avec ASP.NET Core configuré pour le protocole HTTPs :</span><span class="sxs-lookup"><span data-stu-id="5c955-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="5c955-146">Le mot de passe doit correspondre au mot de passe utilisé pour le certificat.</span><span class="sxs-lookup"><span data-stu-id="5c955-146">The password must match the password used for the certificate.</span></span>