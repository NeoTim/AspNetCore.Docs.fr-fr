---
title: Services gRPC avec C#
author: juntaoluo
description: Découvrez les concepts de base liés à l’écriture C#de services gRPC avec.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925188"
---
# <a name="grpc-services-with-c"></a>gRPC services avec C\#

Ce document décrit les concepts nécessaires pour écrire des applications [gRPC](https://grpc.io/docs/guides/) dans C#. Les rubriques traitées ici s’appliquent à la fois aux applications gRPC basées sur le [noyau C](https://grpc.io/blog/grpc-stacks)et à l’ASP.net core.

## <a name="proto-file"></a>fichier proto

gRPC utilise une approche contrat d’abord pour le développement d’API. Les mémoires tampons de protocole (protobuf) sont utilisées par défaut en tant que langage de conception d’interface (IDL). Le fichier *\*. proto* contient les éléments suivants :

* Définition du service gRPC.
* Messages envoyés entre les clients et les serveurs.

Pour plus d’informations sur la syntaxe des fichiers protobuf, consultez la [documentation officielle (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Par exemple, considérez le fichier *Greeter. proto* utilisé dans [prise en main du service gRPC](xref:tutorials/grpc/grpc-start):

* Définit un service de `Greeter`.
* Le service `Greeter` définit un appel de `SayHello`.
* `SayHello` envoie un message de `HelloRequest` et reçoit un message `HelloReply` :

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Ajouter un fichier. proto à une application C\#

Le fichier *\*. proto* est inclus dans un projet en l’ajoutant au groupe d’éléments `<Protobuf>` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#Prise en charge des outils pour les fichiers. proto

Le package d’outils [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) est requis pour générer les C# éléments multimédias à partir des fichiers *\*. proto* . Ressources générées (fichiers) :

* Sont générés en fonction des besoins à chaque fois que le projet est généré.
* Ne sont pas ajoutés au projet ou archivés dans le contrôle de code source.
* Est un artefact de build contenu dans le répertoire *obj* .

Ce package est requis par les projets serveur et client. Le sous-package `Grpc.AspNetCore` contient une référence à `Grpc.Tools`. Les projets serveur peuvent ajouter des `Grpc.AspNetCore` à l’aide du gestionnaire de package dans Visual Studio ou en ajoutant un `<PackageReference>` au fichier projet :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Les projets clients doivent référencer directement `Grpc.Tools` en plus des autres packages requis pour utiliser le client gRPC. Le package d’outils n’est pas requis lors de l’exécution, donc la dépendance est marquée avec `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Ressources C# générées

Le package d’outils génère les C# types représentant les messages définis dans les fichiers *\*. proto* inclus.

Pour les ressources côté serveur, un type de base de service abstrait est généré. Le type de base contient les définitions de tous les appels gRPC contenus dans le fichier *. proto* . Créez une implémentation de service concrète qui dérive de ce type de base et implémente la logique pour les appels gRPC. Pour le `greet.proto`, l’exemple décrit précédemment, un type de `GreeterBase` abstrait qui contient une méthode de `SayHello` virtuelle est généré. Une implémentation concrète `GreeterService` substitue la méthode et implémente la logique qui gère l’appel gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Pour les ressources côté client, un type de client concret est généré. Les appels gRPC dans le fichier *. proto* sont traduits en méthodes sur le type concret, qui peuvent être appelées. Pour le `greet.proto`, l’exemple décrit précédemment, un type de `GreeterClient` concret est généré. Appelez `GreeterClient.SayHelloAsync` pour lancer un appel gRPC sur le serveur.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Par défaut, les ressources serveur et client sont générées pour chaque fichier *\*. proto* inclus dans le groupe d’éléments `<Protobuf>`. Pour garantir que seules les ressources du serveur sont générées dans un projet serveur, l’attribut `GrpcServices` est défini sur `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

De même, l’attribut a la valeur `Client` dans les projets clients.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
