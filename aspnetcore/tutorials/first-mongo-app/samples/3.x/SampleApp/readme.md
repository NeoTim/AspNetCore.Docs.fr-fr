---
page_type: sample
description: Ce tutoriel montre comment créer une API web ASP.NET Core à l’aide d’une base de données NoSQL MongoDB.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 01f9cf237dcf2a9b95c181c2cb87ef9f59102244
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663498"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="e5c7b-102">Créer une API web avec ASP.NET Core et MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="e5c7b-103">Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="e5c7b-104">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="e5c7b-105">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-105">Configure MongoDB</span></span>
* <span data-ttu-id="e5c7b-106">Créer une base de données MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-106">Create a MongoDB database</span></span>
* <span data-ttu-id="e5c7b-107">Définir une collection et un schéma MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="e5c7b-108">Effectuer des opérations CRUD MongoDB à partir d’une API web</span><span class="sxs-lookup"><span data-stu-id="e5c7b-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="e5c7b-109">Personnaliser la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="e5c7b-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e5c7b-110">Conditions préalables requises</span><span class="sxs-lookup"><span data-stu-id="e5c7b-110">Prerequisites</span></span>

* [<span data-ttu-id="e5c7b-111">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-111">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="e5c7b-112">[Préversion de Visual Studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) avec la charge de travail **ASP.NET et développement web**</span><span class="sxs-lookup"><span data-stu-id="e5c7b-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="e5c7b-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="e5c7b-114">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="e5c7b-114">Configure MongoDB</span></span>

<span data-ttu-id="e5c7b-115">Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="e5c7b-116">Ajoutez *C:\\Program Files\\MongoDB\\Server\\\<numéro_version>\\bin* à la variable d’environnement `Path`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="e5c7b-117">Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="e5c7b-118">Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="e5c7b-119">Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="e5c7b-120">Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="e5c7b-121">Par exemple, *C:\\BooksData* sous Windows.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="e5c7b-122">Le cas échéant, créez le répertoire.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="e5c7b-123">L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="e5c7b-124">Ouvrez un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-124">Open a command shell.</span></span> <span data-ttu-id="e5c7b-125">Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="e5c7b-126">N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="e5c7b-127">Ouvrez une autre instance de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-127">Open another command shell instance.</span></span> <span data-ttu-id="e5c7b-128">Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="e5c7b-129">Utilisez la ligne suivante dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="e5c7b-130">Le cas échéant, une base de données nommée *BookstoreDb* est créée.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="e5c7b-131">Si la base de données existe déjà, sa connexion est ouverte pour les transactions.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="e5c7b-132">Créez une collection `Books` à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="e5c7b-133">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="e5c7b-134">Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="e5c7b-135">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-135">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > <span data-ttu-id="e5c7b-136">Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="e5c7b-137">Affichez les documents de la base de données à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="e5c7b-138">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-138">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="e5c7b-139">Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="e5c7b-140">La base de données est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-140">The database is ready.</span></span> <span data-ttu-id="e5c7b-141">Vous pouvez commencer à créer l’API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="e5c7b-142">Créer le projet d’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5c7b-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="e5c7b-143">Accédez à **Fichier** > **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="e5c7b-144">Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="e5c7b-145">Nommez le projet *BooksApi*, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="e5c7b-146">Sélectionnez le framework cible **.NET Core** et **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="e5c7b-147">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="e5c7b-148">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="e5c7b-149">Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="e5c7b-150">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="e5c7b-151">Ajouter un modèle d’entité</span><span class="sxs-lookup"><span data-stu-id="e5c7b-151">Add an entity model</span></span>

1. <span data-ttu-id="e5c7b-152">Ajoutez un répertoire *Models* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="e5c7b-153">Ajoutez une classe `Book` au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    <span data-ttu-id="e5c7b-154">Dans la classe précédente, la propriété `Id` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="e5c7b-155">Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="e5c7b-156">Est annoté avec [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété comme clé primaire du document.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="e5c7b-157">Est annoté avec [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour permettre le passage du paramètre en tant que type `string` au lieu d’une structure [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="e5c7b-158">Mongo gère la conversion de `string` en `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="e5c7b-159">La propriété `BookName` est annotée avec l’attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="e5c7b-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="e5c7b-160">La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="e5c7b-161">Ajouter un modèle de configuration</span><span class="sxs-lookup"><span data-stu-id="e5c7b-161">Add a configuration model</span></span>

1. <span data-ttu-id="e5c7b-162">Ajoutez les valeurs de configuration de base de données suivantes à *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="e5c7b-163">Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="e5c7b-164">La classe `BookstoreDatabaseSettings` précédente est utilisée pour stocker les valeurs de propriété *du fichier* appsettings.json`BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="e5c7b-165">Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="e5c7b-166">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="e5c7b-167">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-167">In the preceding code:</span></span>

    * <span data-ttu-id="e5c7b-168">L’instance de configuration à laquelle la section *du fichier*appsettings.json`BookstoreDatabaseSettings` est liée est inscrite dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="e5c7b-169">Par exemple, la propriété `BookstoreDatabaseSettings` d’un objet `ConnectionString` est peuplée avec la propriété `BookstoreDatabaseSettings:ConnectionString` dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="e5c7b-170">L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e5c7b-171">Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="e5c7b-172">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="e5c7b-173">Ajouter un service d’opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="e5c7b-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="e5c7b-174">Ajoutez un répertoire *Services* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="e5c7b-175">Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="e5c7b-176">Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="e5c7b-177">Cette technique permet d’accéder aux valeurs de configuration d’*appsettings.json*, qui ont été ajoutées à la section [Ajouter un modèle de configuration](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="e5c7b-178">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="e5c7b-179">Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="e5c7b-180">La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="e5c7b-181">Selon les [recommandations officielles de réutilisation de Mongo Client](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` doit être inscrit auprès de l’injection de dépendances avec une durée de vie de service de singleton.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="e5c7b-182">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="e5c7b-183">La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="e5c7b-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lit l’instance de serveur pour effectuer des opérations de base de données.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="e5c7b-185">Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="e5c7b-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; représente la base de données Mongo pour effectuer des opérations.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="e5c7b-187">Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="e5c7b-188">Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="e5c7b-189">Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="e5c7b-190">`collection` représente le nom de la collection.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="e5c7b-191">`TDocument` représente le type d’objet CLR stocké dans la collection.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="e5c7b-192">`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="e5c7b-193">Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="e5c7b-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; supprime un document unique correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="e5c7b-195">[Find\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retourne tous les documents de la collection correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="e5c7b-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insère l’objet fourni sous la forme d’un nouveau document dans la collection.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="e5c7b-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; remplace le document unique qui correspond aux critères de recherche fournis par l’objet fourni.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e5c7b-198">Ajout d'un contrôleur</span><span class="sxs-lookup"><span data-stu-id="e5c7b-198">Add a controller</span></span>

<span data-ttu-id="e5c7b-199">Ajoutez une classe `BooksController` au répertoire *Contrôleurs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="e5c7b-200">Le contrôleur d’API web précédent :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-200">The preceding web API controller:</span></span>

* <span data-ttu-id="e5c7b-201">Utilise la classe `BookService` pour effectuer des opérations CRUD.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="e5c7b-202">Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="e5c7b-203">Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="e5c7b-204">Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="e5c7b-205">`CreatedAtRoute` ajoute également un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="e5c7b-206">L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="e5c7b-207">Tester l’API web</span><span class="sxs-lookup"><span data-stu-id="e5c7b-207">Test the web API</span></span>

1. <span data-ttu-id="e5c7b-208">Générez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-208">Build and run the app.</span></span>

1. <span data-ttu-id="e5c7b-209">Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="e5c7b-210">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-210">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. <span data-ttu-id="e5c7b-211">Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="e5c7b-212">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="e5c7b-213">Configurer les options de sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="e5c7b-213">Configure JSON serialization options</span></span>

<span data-ttu-id="e5c7b-214">Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="e5c7b-215">Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="e5c7b-216">La propriété `bookName` doit être retournée sous la forme `Name`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="e5c7b-217">Pour satisfaire les exigences précédentes, apportez les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="e5c7b-218">JSON.NET a été supprimé du framework partagé ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="e5c7b-219">Ajoutez une référence de package à [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="e5c7b-220">Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddMvc` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="e5c7b-221">À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="e5c7b-222">Par exemple, la propriété `Book` de la classe `Author` est sérialisée en tant que `Author`.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="e5c7b-223">Dans *Models/Book. cs*, annotez la propriété `BookName` avec l’attribut [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) suivant :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="e5c7b-224">La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="e5c7b-225">Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="e5c7b-226">Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="e5c7b-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="e5c7b-227">Notez la différence des noms de propriétés JSON.</span><span class="sxs-lookup"><span data-stu-id="e5c7b-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e5c7b-228">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e5c7b-228">Next steps</span></span>

<span data-ttu-id="e5c7b-229">Pour plus d’informations sur la création d’API web ASP.NET Core, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="e5c7b-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="e5c7b-230">Version YouTube de cet article</span><span class="sxs-lookup"><span data-stu-id="e5c7b-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="e5c7b-231">Créer des API web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5c7b-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
