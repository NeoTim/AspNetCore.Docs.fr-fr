---
title: Migrer de l’authentification d’appartenance ASP.NET vers l’identité ASP.NET Core 2,0
author: isaac2004
description: Découvrez comment migrer des applications ASP.NET existantes à l’aide de l’authentification d’appartenance vers ASP.NET Core identité 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659242"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="7b2bc-103">Migrer de l’authentification d’appartenance ASP.NET vers l’identité ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="7b2bc-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="7b2bc-104">De [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="7b2bc-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="7b2bc-105">Cet article explique comment migrer le schéma de base de données pour les applications ASP.NET à l’aide de l’authentification d’appartenance vers ASP.NET Core identité 2,0.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="7b2bc-106">Ce document décrit les étapes nécessaires à la migration du schéma de base de données pour les applications basées sur l’appartenance ASP.NET vers le schéma de base de données utilisé pour ASP.NET Core identité.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="7b2bc-107">Pour plus d’informations sur la migration de l’authentification basée sur l’appartenance ASP.NET vers ASP.NET Identity, consultez [migrer une application existante de l’appartenance SQL vers ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="7b2bc-108">Pour plus d’informations sur ASP.NET Core identité, consultez [Présentation de l’identité sur ASP.net Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="7b2bc-109">Examen du schéma d’appartenance</span><span class="sxs-lookup"><span data-stu-id="7b2bc-109">Review of Membership schema</span></span>

<span data-ttu-id="7b2bc-110">Avant ASP.NET 2,0, les développeurs devaient créer l’intégralité du processus d’authentification et d’autorisation pour leurs applications.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="7b2bc-111">Avec ASP.NET 2,0, l’appartenance a été introduite et fournit une solution réutilisable pour gérer la sécurité dans les applications ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="7b2bc-112">Les développeurs pouvaient désormais démarrer un schéma dans une base de données SQL Server à l’aide de la commande [aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="7b2bc-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="7b2bc-113">Après l’exécution de cette commande, les tables suivantes ont été créées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-113">After running this command, the following tables were created in the database.</span></span>

  ![Tables d’appartenance](identity/_static/membership-tables.png)

<span data-ttu-id="7b2bc-115">Pour migrer des applications existantes vers ASP.NET Core identité 2,0, les données de ces tables doivent être migrées vers les tables utilisées par le nouveau schéma d’identité.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="7b2bc-116">Schéma d’identité ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="7b2bc-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="7b2bc-117">ASP.NET Core 2,0 suit le principe d' [identité](/aspnet/identity/index) introduit dans ASP.net 4,5.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="7b2bc-118">Bien que le principe soit partagé, l’implémentation entre les frameworks est différente, même entre les versions de ASP.NET Core (consultez [migrer l’authentification et l’identité vers ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="7b2bc-119">La façon la plus rapide d’afficher le schéma pour ASP.NET Core l’identité 2,0 est de créer une nouvelle application ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="7b2bc-120">Procédez comme suit dans Visual Studio 2017 :</span><span class="sxs-lookup"><span data-stu-id="7b2bc-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="7b2bc-121">Sélectionnez **Fichier** > **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7b2bc-122">Créez un projet d' **application Web ASP.net Core** nommé *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="7b2bc-123">Sélectionnez **ASP.NET Core 2,0** dans la liste déroulante, puis sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="7b2bc-124">Ce modèle produit une application [Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="7b2bc-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7b2bc-125">Avant de cliquer sur **OK**, cliquez sur **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="7b2bc-126">Choisissez des **comptes d’utilisateur individuels** pour les modèles d’identité.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="7b2bc-127">Enfin, cliquez sur **OK**, puis sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="7b2bc-128">Visual Studio crée un projet à l’aide du modèle d’identité ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="7b2bc-129">Sélectionnez **outils** > **Gestionnaire de package NuGet** > **console du gestionnaire** de package pour ouvrir la fenêtre **console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="7b2bc-130">Accédez à la racine du projet dans PMC, puis exécutez la commande [Entity Framework (EF) Core](/ef/core) `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="7b2bc-131">ASP.NET Core identité 2,0 utilise EF Core pour interagir avec la base de données qui stocke les données d’authentification.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="7b2bc-132">Pour que l’application nouvellement créée fonctionne, vous devez disposer d’une base de données pour stocker ces données.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="7b2bc-133">Après la création d’une nouvelle application, la méthode la plus rapide pour inspecter le schéma dans un environnement de base de données consiste à créer la base de données à l’aide de [EF Core migrations](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="7b2bc-134">Ce processus crée une base de données, localement ou ailleurs, qui imite ce schéma.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="7b2bc-135">Pour plus d’informations, consultez la documentation précédente.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="7b2bc-136">Les commandes EF Core utilisent la chaîne de connexion pour la base de données spécifiée dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="7b2bc-137">La chaîne de connexion suivante cible une base de données sur *localhost* nommée *asp-net-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="7b2bc-138">Dans ce paramètre, EF Core est configurée pour utiliser la chaîne de connexion `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="7b2bc-139">Sélectionnez **afficher** > **Explorateur d’objets SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="7b2bc-140">Développez le nœud correspondant au nom de la base de données spécifié dans la propriété `ConnectionStrings:DefaultConnection` de *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="7b2bc-141">La commande `Update-Database` a créé la base de données spécifiée avec le schéma et toutes les données nécessaires à l’initialisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="7b2bc-142">L’image suivante représente la structure de table créée à l’aide des étapes précédentes.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Tables d’identité](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="7b2bc-144">Migrer le schéma</span><span class="sxs-lookup"><span data-stu-id="7b2bc-144">Migrate the schema</span></span>

<span data-ttu-id="7b2bc-145">Il existe des différences subtiles entre les structures et les champs de table pour l’appartenance et l’identité ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="7b2bc-146">Le modèle a changé de manière substantielle pour l’authentification/l’autorisation avec les applications ASP.NET et ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="7b2bc-147">Les objets clés qui sont toujours utilisés avec l’identité sont des *utilisateurs* et des *rôles*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="7b2bc-148">Voici les tables de mappage pour *les utilisateurs, les* *rôles*et les *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="7b2bc-149">Utilisateurs</span><span class="sxs-lookup"><span data-stu-id="7b2bc-149">Users</span></span>

|<span data-ttu-id="7b2bc-150">*<br>d’identité (dbo. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="7b2bc-151">*<br>d’appartenance (dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="7b2bc-152">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-152">**Field Name**</span></span>                 |<span data-ttu-id="7b2bc-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-153">**Type**</span></span>|<span data-ttu-id="7b2bc-154">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-154">**Field Name**</span></span>                                    |<span data-ttu-id="7b2bc-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="7b2bc-156">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="7b2bc-157">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="7b2bc-158">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="7b2bc-159">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="7b2bc-160">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="7b2bc-161">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="7b2bc-162">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="7b2bc-163">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="7b2bc-164">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="7b2bc-165">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="7b2bc-166">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="7b2bc-167">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="7b2bc-168">bit</span><span class="sxs-lookup"><span data-stu-id="7b2bc-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="7b2bc-169">bit</span><span class="sxs-lookup"><span data-stu-id="7b2bc-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="7b2bc-170">Tous les mappages de champs ne ressemblent pas aux relations un-à-un de l’appartenance à ASP.NET Core identité.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="7b2bc-171">Le tableau précédent prend le schéma de l’utilisateur d’appartenance par défaut et le mappe au schéma d’identité ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="7b2bc-172">Tous les autres champs personnalisés qui ont été utilisés pour l’appartenance doivent être mappés manuellement.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="7b2bc-173">Dans ce mappage, il n’existe pas de mappage pour les mots de passe, car les critères de mot de passe et les sels de mot de passe ne migrent pas entre les deux.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="7b2bc-174">**Il est recommandé de conserver la valeur null pour le mot de passe et de demander aux utilisateurs de réinitialiser leur mot de passe.**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="7b2bc-175">Dans ASP.NET Core identité, `LockoutEnd` doit être défini à une date ultérieure si l’utilisateur est verrouillé. Cela est illustré dans le script de migration.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="7b2bc-176">Rôles</span><span class="sxs-lookup"><span data-stu-id="7b2bc-176">Roles</span></span>

|<span data-ttu-id="7b2bc-177">*<br>d’identité (dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="7b2bc-178">*<br>d’appartenance (dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="7b2bc-179">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-179">**Field Name**</span></span>                 |<span data-ttu-id="7b2bc-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-180">**Type**</span></span>|<span data-ttu-id="7b2bc-181">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-181">**Field Name**</span></span>   |<span data-ttu-id="7b2bc-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="7b2bc-183">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-183">string</span></span>  |`RoleId`         | <span data-ttu-id="7b2bc-184">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="7b2bc-185">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-185">string</span></span>  |`RoleName`       | <span data-ttu-id="7b2bc-186">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="7b2bc-187">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="7b2bc-188">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="7b2bc-189">Rôles d'utilisateur</span><span class="sxs-lookup"><span data-stu-id="7b2bc-189">User Roles</span></span>

|<span data-ttu-id="7b2bc-190">*<br>d’identité (dbo. AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="7b2bc-191">*<br>d’appartenance (dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="7b2bc-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="7b2bc-192">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-192">**Field Name**</span></span>           |<span data-ttu-id="7b2bc-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-193">**Type**</span></span>  |<span data-ttu-id="7b2bc-194">**Nom du champ**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-194">**Field Name**</span></span>|<span data-ttu-id="7b2bc-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="7b2bc-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="7b2bc-196">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-196">string</span></span>    |`RoleId`      |<span data-ttu-id="7b2bc-197">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="7b2bc-198">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-198">string</span></span>    |`UserId`      |<span data-ttu-id="7b2bc-199">string</span><span class="sxs-lookup"><span data-stu-id="7b2bc-199">string</span></span>                     |

<span data-ttu-id="7b2bc-200">Référencez les tables de mappage précédentes lors de la création d’un script de migration pour *les utilisateurs et les* *rôles*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="7b2bc-201">L’exemple suivant suppose que vous disposez de deux bases de données sur un serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="7b2bc-202">Une base de données contient le schéma et les données d’appartenance ASP.NET existants.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="7b2bc-203">L’autre base de données *CoreIdentitySample* a été créée à l’aide des étapes décrites précédemment.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="7b2bc-204">Les commentaires sont inclus dans Inline pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="7b2bc-205">Une fois le script précédent terminé, l’application d’identité ASP.NET Core créée précédemment est remplie avec les utilisateurs d’appartenance.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="7b2bc-206">Les utilisateurs doivent modifier leur mot de passe avant de se connecter.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="7b2bc-207">Si le système d’appartenance avait des utilisateurs dont les noms d’utilisateur ne correspondaient pas à leur adresse de messagerie, les modifications sont requises pour l’application créée précédemment pour s’adapter à cette opération.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="7b2bc-208">Le modèle par défaut s’attend à ce que `UserName` et `Email` soient identiques.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="7b2bc-209">Dans les situations où elles sont différentes, le processus de connexion doit être modifié pour utiliser `UserName` au lieu de `Email`.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="7b2bc-210">Dans la `PageModel` de la page de connexion, située dans *Pages\Account\Login.cshtml.cs*, supprimez l’attribut `[EmailAddress]` de la propriété *email* .</span><span class="sxs-lookup"><span data-stu-id="7b2bc-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="7b2bc-211">Renommez-le nom d' *utilisateur*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-211">Rename it to *UserName*.</span></span> <span data-ttu-id="7b2bc-212">Cela nécessite une modification chaque fois que `EmailAddress` est mentionné, dans la *vue* et *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="7b2bc-213">Le résultat se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="7b2bc-213">The result looks like the following:</span></span>

 ![Connexion fixe](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="7b2bc-215">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7b2bc-215">Next steps</span></span>

<span data-ttu-id="7b2bc-216">Dans ce didacticiel, vous avez appris comment porter les utilisateurs de l’appartenance SQL vers ASP.NET Core identité 2,0.</span><span class="sxs-lookup"><span data-stu-id="7b2bc-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="7b2bc-217">Pour plus d’informations sur ASP.NET Core identité, consultez [Présentation de l’identité](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7b2bc-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
