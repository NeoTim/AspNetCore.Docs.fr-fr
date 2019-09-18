---
title: Créer une API web avec ASP.NET Core et MongoDB
author: prkhandelwal
description: Ce tutoriel montre comment créer une API web ASP.NET Core à l’aide d’une base de données NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: acf2ded8b92a8f77678af7b772ac2a69264a642c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082378"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Créer une API web avec ASP.NET Core et MongoDB

Par [Pratik Khandelwal](https://twitter.com/K2Prk) et [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Configurer MongoDB
> * Créer une base de données MongoDB
> * Définir une collection et un schéma MongoDB
> * Effectuer des opérations CRUD MongoDB à partir d’une API web
> * Personnaliser la sérialisation JSON

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK .NET Core 3.0 ou version ultérieure](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail **ASP.NET et développement web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK .NET Core 3.0 ou version ultérieure](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [SDK .NET Core 3.0 ou version ultérieure](https://www.microsoft.com/net/download/all)
* [Visual Studio pour Mac version 7.7 ou ultérieure](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Configurer MongoDB

Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*. Ajoutez *C:\\Program Files\\MongoDB\\Server\\\<numéro_version>\\bin* à la variable d’environnement `Path`. Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.

Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents. Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données. Par exemple, *C:\\BooksData* sous Windows. Le cas échéant, créez le répertoire. L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.
1. Ouvrez un interpréteur de commandes. Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017. N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Ouvrez une autre instance de l’interpréteur de commandes. Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :

   ```console
   mongo
   ```

1. Utilisez la ligne suivante dans l’interpréteur de commandes :

   ```console
   use BookstoreDb
   ```

   Le cas échéant, une base de données nommée *BookstoreDb* est créée. Si la base de données existe déjà, sa connexion est ouverte pour les transactions.

1. Créez une collection `Books` à l’aide de la commande suivante :

   ```console
   db.createCollection('Books')
   ```

   Le résultat suivant s’affiche :

   ```console
   { "ok" : 1 }
   ```

1. Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Le résultat suivant s’affiche :

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
   > Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.

1. Affichez les documents de la base de données à l’aide de la commande suivante :

   ```console
   db.Books.find({}).pretty()
   ```

   Le résultat suivant s’affiche :

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

   Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.

La base de données est en lecture seule. Vous pouvez commencer à créer l’API web ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Créer le projet d’API web ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Accédez à **Fichier** > **Nouveau** > **Projet**.
1. Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.
1. Nommez le projet *BooksApi*, puis sélectionnez **Créer**.
1. Sélectionnez le framework cible **.NET Core** et **ASP.NET Core 3.0**. Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.
1. Consultez la galerie [NuGet : MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .NET pour MongoDB. Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet. Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez les commandes suivantes dans l’interpréteur de commandes :

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.

1. Une fois que l’icône en forme de flamme de la barre d’état d’OmniSharp devient verte, une boîte de dialogue indique **Les composants nécessaires à la build et au débogage sont manquants dans « BooksApi ». Faut-il les ajouter ?** . Sélectionnez **Oui**.
1. Consultez la galerie [NuGet : MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .NET pour MongoDB. Ouvrez **Terminal intégré** et accédez à la racine du projet. Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Accédez à **Fichier** > **Nouvelle solution** > **.NET Core** > **Application**.
1. Sélectionnez le modèle de projet C# **API web ASP.NET Core**, puis sélectionnez **Suivant**.
1. Sélectionnez **.NET Core 3.0** dans la liste déroulante **Framework cible**, puis sélectionnez **Suivant**.
1. Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.
1. Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.
1. Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.
1. Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.

---

## <a name="add-an-entity-model"></a>Ajouter un modèle d’entité

1. Ajoutez un répertoire *Models* à la racine du projet.
1. Ajoutez une classe `Book` au répertoire *Models* avec le code suivant :

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

   Dans la classe précédente, la propriété `Id` :

   * Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.
   * Est annotée avec [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété en tant que clé primaire du document.
   * Est annotée avec [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour autoriser le passage du paramètre en tant que type `string` à la place d’une structure [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo gère la conversion de `string` en `ObjectId`.

   La propriété `BookName` est annotée avec l’attribut [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.

## <a name="add-a-configuration-model"></a>Ajouter un modèle de configuration

1. Ajoutez les valeurs de configuration de base de données suivantes à *appsettings.json* :

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   La classe `BookstoreDatabaseSettings` précédente est utilisée pour stocker les valeurs de propriété `BookstoreDatabaseSettings` du fichier  *appsettings.json*. Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.

1. Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Dans le code précédent :

   * L’instance de configuration à laquelle la section `BookstoreDatabaseSettings` du fichier *appsettings.json* est liée est inscrite dans le conteneur d’injection de dépendances. Par exemple, la propriété `ConnectionString` d’un objet `BookstoreDatabaseSettings` est peuplée avec la propriété `BookstoreDatabaseSettings:ConnectionString` dans *appsettings.json*.
   * L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton. Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.

1. Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Ajouter un service d’opérations CRUD

1. Ajoutez un répertoire *Services* à la racine du projet.
1. Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur. Cette technique permet d’accéder aux valeurs de configuration d’*appsettings.json*, qui ont été ajoutées à la section [Ajouter un modèle de configuration](#add-a-configuration-model).

1. Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices. La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`. Selon les [recommandations officielles de réutilisation de Mongo Client](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` doit être inscrit auprès de l’injection de dépendances avec une durée de vie de service de singleton.

1. Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Lit l’instance de serveur qui permet d’effectuer des opérations de base de données. Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Représente la base de données Mongo qui permet d’effectuer des opérations. Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique. Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode. Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :

  * `collection` représente le nom de la collection.
  * `TDocument` représente le type d’objet CLR stocké dans la collection.

`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection. Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Supprime un seul document correspondant aux critères de recherche fournis.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Retourne tous les documents de la collection correspondant aux critères de recherche fournis.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Insère l’objet fourni en tant que nouveau document dans la collection.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Remplace le seul document correspondant aux critères de recherche fournis par l’objet indiqué.

## <a name="add-a-controller"></a>Ajouter un contrôleur

Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Le contrôleur d’API web précédent :

* Utilise la classe `BookService` pour effectuer des opérations CRUD.
* Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.
* Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur. `CreatedAtRoute` ajoute également un en-tête `Location` à la réponse. L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.

## <a name="test-the-web-api"></a>Tester l’API web

1. Générez et exécutez l’application.

1. Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur. La réponse JSON suivante apparaît :

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

1. Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur. La réponse JSON suivante apparaît :

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Configurer les options de sérialisation JSON

Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :

* Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.
* La propriété `bookName` doit être retournée sous la forme `Name`.

Pour satisfaire les exigences précédentes, apportez les changements suivants :

1. JSON.NET a été supprimé du framework partagé ASP.NET. Ajoutez une référence de package à [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddMvc` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR. Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.

1. Dans *Models/Book.cs*, annotez la propriété `BookName` avec l’attribut [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) suivant :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.

1. Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api). Notez la différence des noms de propriétés JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Configurer MongoDB
> * Créer une base de données MongoDB
> * Définir une collection et un schéma MongoDB
> * Effectuer des opérations CRUD MongoDB à partir d’une API web
> * Personnaliser la sérialisation JSON

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Kit SDK .NET Core 2.2](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail **ASP.NET et développement web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Kit SDK .NET Core 2.2](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [Kit SDK .NET Core 2.2](https://www.microsoft.com/net/download/all)
* [Visual Studio pour Mac version 7.7 ou ultérieure](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Configurer MongoDB

Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*. Ajoutez *C:\\Program Files\\MongoDB\\Server\\\<numéro_version>\\bin* à la variable d’environnement `Path`. Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.

Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents. Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données. Par exemple, *C:\\BooksData* sous Windows. Le cas échéant, créez le répertoire. L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.
1. Ouvrez un interpréteur de commandes. Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017. N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Ouvrez une autre instance de l’interpréteur de commandes. Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :

   ```console
   mongo
   ```

1. Utilisez la ligne suivante dans l’interpréteur de commandes :

   ```console
   use BookstoreDb
   ```

   Le cas échéant, une base de données nommée *BookstoreDb* est créée. Si la base de données existe déjà, sa connexion est ouverte pour les transactions.

1. Créez une collection `Books` à l’aide de la commande suivante :

   ```console
   db.createCollection('Books')
   ```

   Le résultat suivant s’affiche :

   ```console
   { "ok" : 1 }
   ```

1. Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Le résultat suivant s’affiche :

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
   > Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.

1. Affichez les documents de la base de données à l’aide de la commande suivante :

   ```console
   db.Books.find({}).pretty()
   ```

   Le résultat suivant s’affiche :

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

   Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.

La base de données est en lecture seule. Vous pouvez commencer à créer l’API web ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Créer le projet d’API web ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Accédez à **Fichier** > **Nouveau** > **Projet**.
1. Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.
1. Nommez le projet *BooksApi*, puis sélectionnez **Créer**.
1. Sélectionnez la version cible de .NET Framework **.NET Core** et **ASP.NET Core 2.2**. Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.
1. Consultez la galerie [NuGet : MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .NET pour MongoDB. Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet. Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez les commandes suivantes dans l’interpréteur de commandes :

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.

1. Une fois que l’icône en forme de flamme de la barre d’état d’OmniSharp devient verte, une boîte de dialogue indique **Les composants nécessaires à la build et au débogage sont manquants dans « BooksApi ». Faut-il les ajouter ?** . Sélectionnez **Oui**.
1. Consultez la galerie [NuGet : MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .NET pour MongoDB. Ouvrez **Terminal intégré** et accédez à la racine du projet. Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Accédez à **Fichier** > **Nouvelle solution** > **.NET Core** > **Application**.
1. Sélectionnez le modèle de projet C# **API web ASP.NET Core**, puis sélectionnez **Suivant**.
1. Sélectionnez **.NET Core 2.2** dans la liste déroulante **Framework cible**, puis sélectionnez **Suivant**.
1. Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.
1. Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.
1. Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.
1. Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.

---

## <a name="add-an-entity-model"></a>Ajouter un modèle d’entité

1. Ajoutez un répertoire *Models* à la racine du projet.
1. Ajoutez une classe `Book` au répertoire *Models* avec le code suivant :

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

   Dans la classe précédente, la propriété `Id` :

   * Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.
   * Est annotée avec [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété en tant que clé primaire du document.
   * Est annotée avec [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour autoriser le passage du paramètre en tant que type `string` à la place d’une structure [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo gère la conversion de `string` en `ObjectId`.

   La propriété `BookName` est annotée avec l’attribut [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.

## <a name="add-a-configuration-model"></a>Ajouter un modèle de configuration

1. Ajoutez les valeurs de configuration de base de données suivantes à *appsettings.json* :

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   La classe `BookstoreDatabaseSettings` précédente est utilisée pour stocker les valeurs de propriété `BookstoreDatabaseSettings` du fichier  *appsettings.json*. Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.

1. Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Dans le code précédent :

   * L’instance de configuration à laquelle la section `BookstoreDatabaseSettings` du fichier *appsettings.json* est liée est inscrite dans le conteneur d’injection de dépendances. Par exemple, la propriété `ConnectionString` d’un objet `BookstoreDatabaseSettings` est peuplée avec la propriété `BookstoreDatabaseSettings:ConnectionString` dans *appsettings.json*.
   * L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton. Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.

1. Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Ajouter un service d’opérations CRUD

1. Ajoutez un répertoire *Services* à la racine du projet.
1. Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur. Cette technique permet d’accéder aux valeurs de configuration d’*appsettings.json*, qui ont été ajoutées à la section [Ajouter un modèle de configuration](#add-a-configuration-model).

1. Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices. La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`. Selon les [recommandations officielles de réutilisation de Mongo Client](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` doit être inscrit auprès de l’injection de dépendances avec une durée de vie de service de singleton.

1. Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Lit l’instance de serveur qui permet d’effectuer des opérations de base de données. Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Représente la base de données Mongo qui permet d’effectuer des opérations. Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique. Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode. Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :

  * `collection` représente le nom de la collection.
  * `TDocument` représente le type d’objet CLR stocké dans la collection.

`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection. Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Supprime un seul document correspondant aux critères de recherche fournis.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Retourne tous les documents de la collection correspondant aux critères de recherche fournis.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Insère l’objet fourni en tant que nouveau document dans la collection.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Remplace le seul document correspondant aux critères de recherche fournis par l’objet indiqué.

## <a name="add-a-controller"></a>Ajouter un contrôleur

Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Le contrôleur d’API web précédent :

* Utilise la classe `BookService` pour effectuer des opérations CRUD.
* Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.
* Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur. `CreatedAtRoute` ajoute également un en-tête `Location` à la réponse. L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.

## <a name="test-the-web-api"></a>Tester l’API web

1. Générez et exécutez l’application.

1. Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur. La réponse JSON suivante apparaît :

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

1. Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur. La réponse JSON suivante apparaît :

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Configurer les options de sérialisation JSON

Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :

* Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.
* La propriété `bookName` doit être retournée sous la forme `Name`.

Pour satisfaire les exigences précédentes, apportez les changements suivants :

1. Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddMvc` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR. Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.

1. Dans *Models/Book.cs*, annotez la propriété `BookName` avec l’attribut [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) suivant :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.

1. Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api). Notez la différence des noms de propriétés JSON.

::: moniker-end

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’API web ASP.NET Core, consultez les ressources suivantes :

* [Version YouTube de cet article](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
