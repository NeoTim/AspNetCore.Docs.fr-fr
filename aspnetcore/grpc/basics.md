---
title: Services gRPC avec C#
author: juntaoluo
description: Découvrez les concepts de base liés à l’écriture C#de services gRPC avec.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 700fe9463317f9ee30dfe4ebf5201c7b9c0c5ad6
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412466"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="fadb4-103">services gRPC avec C\#</span><span class="sxs-lookup"><span data-stu-id="fadb4-103">gRPC services with C\#</span></span>

<span data-ttu-id="fadb4-104">Ce document décrit les concepts nécessaires pour écrire des applications [gRPC](https://grpc.io/docs/guides/) dans C#.</span><span class="sxs-lookup"><span data-stu-id="fadb4-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="fadb4-105">Les rubriques traitées ici s’appliquent à la fois aux applications gRPC basées sur le [noyau C](https://grpc.io/blog/grpc-stacks)et à l’ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="fadb4-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="fadb4-106">fichier proto</span><span class="sxs-lookup"><span data-stu-id="fadb4-106">proto file</span></span>

<span data-ttu-id="fadb4-107">gRPC utilise une approche contrat d’abord pour le développement d’API.</span><span class="sxs-lookup"><span data-stu-id="fadb4-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="fadb4-108">Les mémoires tampons de protocole (protobuf) sont utilisées par défaut en tant que langage de conception d’interface (IDL).</span><span class="sxs-lookup"><span data-stu-id="fadb4-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="fadb4-109">Le fichier *. proto* contient les éléments suivants:</span><span class="sxs-lookup"><span data-stu-id="fadb4-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="fadb4-110">Définition du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadb4-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="fadb4-111">Messages envoyés entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="fadb4-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="fadb4-112">Pour plus d’informations sur la syntaxe des fichiers protobuf, consultez la [documentation officielle (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="fadb4-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="fadb4-113">Par exemple, considérez le fichier *Greeter. proto* utilisé dans [prise en main du service gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="fadb4-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="fadb4-114">Définit un `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="fadb4-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="fadb4-115">Le `Greeter` service définit un `SayHello` appel.</span><span class="sxs-lookup"><span data-stu-id="fadb4-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="fadb4-116">`SayHello`envoie un `HelloRequest` message et reçoit un `HelloReply` message:</span><span class="sxs-lookup"><span data-stu-id="fadb4-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="fadb4-117">Ajouter un fichier. proto à une application\# C</span><span class="sxs-lookup"><span data-stu-id="fadb4-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="fadb4-118">Le fichier *. proto* est inclus dans un projet en l' `<Protobuf>` ajoutant au groupe d’éléments:</span><span class="sxs-lookup"><span data-stu-id="fadb4-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="fadb4-119">C#Prise en charge des outils pour les fichiers. proto</span><span class="sxs-lookup"><span data-stu-id="fadb4-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="fadb4-120">Le package d’outils [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) est requis pour générer les C# ressources à partir de fichiers *. proto* .</span><span class="sxs-lookup"><span data-stu-id="fadb4-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="fadb4-121">Ressources générées (fichiers):</span><span class="sxs-lookup"><span data-stu-id="fadb4-121">The generated assets (files):</span></span>

* <span data-ttu-id="fadb4-122">Sont générés en fonction des besoins à chaque fois que le projet est généré.</span><span class="sxs-lookup"><span data-stu-id="fadb4-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="fadb4-123">Ne sont pas ajoutés au projet ou archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="fadb4-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="fadb4-124">Est un artefact de build contenu dans le répertoire *obj* .</span><span class="sxs-lookup"><span data-stu-id="fadb4-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="fadb4-125">Ce package est requis par les projets serveur et client.</span><span class="sxs-lookup"><span data-stu-id="fadb4-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="fadb4-126">Le `Grpc.AspNetCore` repackage contient une référence à `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="fadb4-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="fadb4-127">Les projets serveur peuvent `Grpc.AspNetCore` être ajoutés à l’aide du gestionnaire de package dans Visual `<PackageReference>` Studio ou en ajoutant un au fichier projet:</span><span class="sxs-lookup"><span data-stu-id="fadb4-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="fadb4-128">Les projets clients doivent `Grpc.Tools` référencer directement.</span><span class="sxs-lookup"><span data-stu-id="fadb4-128">Client projects should reference `Grpc.Tools` directly.</span></span> <span data-ttu-id="fadb4-129">Le package d’outils n’est pas requis lors de l’exécution, donc la `PrivateAssets="All"`dépendance est marquée avec:</span><span class="sxs-lookup"><span data-stu-id="fadb4-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=1&range=11)]

## <a name="generated-c-assets"></a><span data-ttu-id="fadb4-130">Ressources C# générées</span><span class="sxs-lookup"><span data-stu-id="fadb4-130">Generated C# assets</span></span>

<span data-ttu-id="fadb4-131">Le package d’outils génère les C# types représentant les messages définis dans les fichiers *. proto* inclus.</span><span class="sxs-lookup"><span data-stu-id="fadb4-131">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="fadb4-132">Pour les ressources côté serveur, un type de base de service abstrait est généré.</span><span class="sxs-lookup"><span data-stu-id="fadb4-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="fadb4-133">Le type de base contient les définitions de tous les appels gRPC contenus dans le fichier *. proto* .</span><span class="sxs-lookup"><span data-stu-id="fadb4-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="fadb4-134">Créez une implémentation de service concrète qui dérive de ce type de base et implémente la logique pour les appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadb4-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="fadb4-135">Pour le `greet.proto`, l’exemple décrit précédemment, un type `GreeterBase` abstrait qui contient une méthode `SayHello` virtuelle est généré.</span><span class="sxs-lookup"><span data-stu-id="fadb4-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="fadb4-136">Une implémentation `GreeterService` concrète remplace la méthode et implémente la logique qui gère l’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="fadb4-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="fadb4-137">Pour les ressources côté client, un type de client concret est généré.</span><span class="sxs-lookup"><span data-stu-id="fadb4-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="fadb4-138">Les appels gRPC dans le fichier *. proto* sont traduits en méthodes sur le type concret, qui peuvent être appelées.</span><span class="sxs-lookup"><span data-stu-id="fadb4-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="fadb4-139">Pour le `greet.proto`, l’exemple décrit précédemment, un type `GreeterClient` concret est généré.</span><span class="sxs-lookup"><span data-stu-id="fadb4-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="fadb4-140">Appelez `GreeterClient.SayHello` pour lancer un appel gRPC sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fadb4-140">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=3-6&name=snippet)]

<span data-ttu-id="fadb4-141">Par défaut, les ressources serveur et client sont générées pour chaque fichier *. proto* inclus `<Protobuf>` dans le groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="fadb4-141">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="fadb4-142">Pour garantir que seules les ressources du serveur sont générées dans un projet `GrpcServices` serveur, l’attribut `Server`a la valeur.</span><span class="sxs-lookup"><span data-stu-id="fadb4-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="fadb4-143">De même, l’attribut a la valeur `Client` dans les projets clients.</span><span class="sxs-lookup"><span data-stu-id="fadb4-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fadb4-144">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fadb4-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
