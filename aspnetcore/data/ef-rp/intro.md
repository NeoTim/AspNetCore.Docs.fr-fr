---
title: Pages Razor avec Entity Framework Core dans ASP.NET Core - Tutoriel 1 sur 8
author: rick-anderson
description: Montre comment créer une application Pages Razor à l’aide d’Entity Framework Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 01e507326ddd57057aa178ad3909fd4027a013fd
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259375"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Pages Razor avec Entity Framework Core dans ASP.NET Core - Tutoriel 1 sur 8

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.NET Core Razor pages](xref:razor-pages/index) . Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso. Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.

[Télécharger ou afficher l’application complète.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Télécharger les instructions](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

* Si vous débutez avec Razor Pages, parcourez le tutoriel [Bien démarrer avec Razor Pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celui-ci.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Moteurs de base de données

Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.

Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.

Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Exemple d’application

L’application générée dans ces didacticiels est le site web de base d’une université. Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés. Le tutoriel traite essentiellement de l’utilisation d’EF Core, et non de la façon de personnaliser l’interface utilisateur.

Suivez le lien en haut de la page pour obtenir le code source du projet terminé. Le dossier *cu30* contient le code de la version 3.0 d’ASP.NET Core. Les fichiers qui reflètent l’état du code pour les tutoriels 1-7 se trouvent dans le dossier *cu30snapshots*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour exécuter l’application après avoir téléchargé le projet terminé :

* Supprimez trois fichiers et un dossier dont le nom contient *SQLite*.
* Générez le projet.
* Dans la console du Gestionnaire de package (PMC), exécutez la commande suivante :

  ```powershell
  Update-Database
  ```

* Exécutez le projet pour amorcer la base de données.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pour exécuter l’application après avoir téléchargé le projet terminé :

* Supprimez *ContosoUniversity.csproj*, puis renommez *ContosoUniversitySQLite.csproj* en *ContosoUniversity.csproj*.
* Supprimez *Startup.cs* et renommez *StartupSQLite.cs* en *Startup.cs*.
* Supprimez *appSettings.json* et renommez *appSettingsSQLite.json* en *appSettings.json*.
* Supprimez le dossier *Migrations* et renommez *MigrationsSQL* en *Migrations*.
* Générez le projet.
* Dans une invite de commandes, exécutez la commande suivante dans le dossier du projet :

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* Dans votre outil SQLite, exécutez l’instruction SQL suivante :

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Exécutez le projet pour amorcer la base de données.

---

## <a name="create-the-web-app-project"></a>Créer le projet d’application web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Nouvelle application web ASP.NET Core**.
* Nommez le projet *ContosoUniversity*. Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.
* Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les listes déroulantes, puis **Application web**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.

* Exécutez les commandes suivantes pour créer un projet Razor Pages et `cd` dans le nouveau dossier de projet :

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Configurer le style du site

Configurez l’en-tête, le pied de page et le menu du site en mettant à jour *Pages/Shared/_Layout.cshtml* :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il existe trois occurrences.

* Supprimez les entrées de menu **Home** et **Privacy** et ajoutez les entrées **About**, **Students**, **Courses**, **Instructors** et **Departments**.

Les modifications sont mises en surbrillance.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant de façon à remplacer le texte relatif à ASP.NET Core par le texte se rapportant à cette application :

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.

## <a name="the-data-model"></a>Modèle de données

Les sections suivantes créent un modèle de données :

![Diagramme de modèle de données Cours-Inscription-Étudiant](intro/_static/data-model-diagram.png)

Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.

## <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* Créez un dossier *Models* dans le dossier de projet. 

* Créez *Models/Student.cs* avec le code suivant :

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.

La propriété `Enrollments` est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant. Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment. 

Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant. Par exemple, supposez qu’une ligne Student présente un ID égal à 1. Les lignes Enrollment associées auront un StudentID égal à 1. StudentID est une *clé étrangère* dans la table Enrollment. 

La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister. Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`. Lorsque `ICollection<Enrollment>` est utilisé, le EF Core crée une collection `HashSet<Enrollment>` par défaut.

## <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Créez *Models/Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même. Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement. Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux. L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.

La propriété `Grade` est un `enum`. La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` [accepte les valeurs Null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`, par conséquent, la propriété contient une seule entité `Student`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété comme une clé étrangère si elle est nommée `<navigation property name><primary key property name>`. Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`. Les propriétés de clé étrangère peuvent également être nommées `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

## <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Créez *Models/Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments` est une propriété de navigation. A une entité `Course` peut être associée à un nombre quelconque d'entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="scaffold-student-pages"></a>Générer automatiquement des modèles de pages Student

Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :

* Une classe de *contexte* EF Core. Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé. Il dérive de la classe `Microsoft.EntityFrameworkCore.DbContext`.
* Les pages Razor qui gèrent les opérations Create, Read, Update et Delete (CRUD) pour l’entité `Student`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Créez un dossier *Students* dans le dossier *Pages*.
* Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students*, puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Pages Razor avec Entity Framework (CRUD)** > **AJOUTER**.
* Dans la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)**  :
  * Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)** .
  * Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).
  * Remplacez le nom du contexte de données *ContosoUniversity.Models.ContosoUniversityContext* par *ContosoUniversity.Data.SchoolContext*.
  * Sélectionnez **Ajouter**.

Les packages suivants sont automatiquement installés :

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique. Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.

* Créez un dossier *Pages/Students*.

* Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.

  **Sur Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **Sur macOS ou Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Si vous rencontrez un problème à l’étape précédente, générez le projet et recommencez l’étape de génération de modèles automatique.

Le processus de génération de modèles automatique :

* Crée les pages Razor suivantes dans le dossier *Pages/Students* :
  * *Create.cshtml* et *Create.cshtml.cs*
  * *Delete.cshtml* et *Delete.cshtml.cs*
  * *Details.cshtml* et *Details.cshtml.cs*
  * *Edit.cshtml* et *Edit.cshtml.cs*
  * *Index.cshtml* et *Index.cshtml.cs*
* Crée *Data/SchoolContext. cs*.
* Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.
* Ajoute une chaîne de connexion de base de données à *appsettings.json*.

## <a name="database-connection-string"></a>Chaîne de connexion de base de données

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB est une version légère de SQL Server Express Database Engine et est prévue pour le développement d’applications, et pas à des fins de production. Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Modifiez la chaîne de connexion de sorte qu’elle pointe vers un fichier de base de données SQLite nommé *CU.db* :

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Mettre à jour la classe du contexte de base de données

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données. Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Mettez à jour *SchoolContext.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Le code en surbrillance crée une propriété [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

Comme un jeu d’entités contient plusieurs entités, les propriétés DBSet doivent être des noms au pluriel. Comme l’outil de génération de modèles automatique a créé un DBSet `Student`, cette étape le remplace par le pluriel `Students`. 

Pour que le code Razor Pages corresponde au nouveau nom DBSet, apportez une modification globale à l’ensemble du projet en remplaçant `_context.Student` par `_context.Students`.  Il y a 8 occurrences.

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Certains services (comme le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Les composants qui requièrent ces services (tels que les Pages Razor) sont fournis à ces services via les paramètres du constructeur. Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans `ConfigureServices`, les lignes en surbrillance ont été ajoutées par l’outil de génération de modèles automatique :

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans `ConfigureServices`, vérifiez que le code ajouté par l’outil de génération de modèles automatique appelle `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir de la *appsettings.json* fichier.

## <a name="create-the-database"></a>Créer la base de données

Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte. S’il n’existe pas de base de données, elle crée la base de données et le schéma. `EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :

* Supprimez la base de données. Toutes les données existantes sont perdues.
* Modifiez le modèle de données. Par exemple, ajoutez un champ `EmailAddress`.
* Exécuter l’application.
* `EnsureCreated` crée une base de données avec le nouveau schéma.

Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données. La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées. Dans ce cas, procédez à des migrations.

Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations. Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.

### <a name="test-the-app"></a>Tester l’application

* Exécuter l’application.
* Sélectionnez le lien **Students**, puis **Créer nouveau**.
* Testez les liens Edit, Details et Delete.

## <a name="seed-the-database"></a>Amorcer la base de données

La méthode `EnsureCreated` crée une base de données vide. Cette section ajoute du code qui remplit la base de données avec des données de test.

Créez *Data/DbInitializer.cs* avec le code suivant :

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Le code vérifie si des étudiants figurent dans la base de données. S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données. Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.

* Dans *Program.cs*, remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.

---

* Redémarrez l’application.

* Sélectionnez la page Students pour examiner les données amorcées.

## <a name="view-the-database"></a>Afficher la base de données

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
* Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** . Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.
* Développez le noeud **Tables**.
* Avec le bouton droit sur la table **Student**, cliquez sur **des données d’affichage** pour afficher les colonnes créées et les lignes insérées dans la table.
* Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées. Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.

---

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit une petite quantité de charge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Le mot clé `async` fait en sorte que le compilateur :
  * Génèrer des rappels pour les parties du corps de méthode.
  * Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.
* Le type de retour `Task<T>` représente le travail en cours.
* Le (mot clé) `await`, le compilateur fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Les éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Seules les instructions qui génèrent des requêtes ou des commandes envoyées à la base de données sont exécutées de façon asynchrone. Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`, Il n’inclut pas les instructions qui modifient simplement un `IQueryable`, tel que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.
* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Tutoriel suivant](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L’exemple d’application web Contoso University montre comment créer une application web Razor Pages ASP.NET Core à l’aide d’Entity Framework (EF) Core.

L’exemple d’application est un site web pour une université Contoso fictive. Il inclut des fonctionnalités telles que l'admission d’étudiant, la création de cours et les affectations de formateur. Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.

[Télécharger ou afficher l’application complète.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Télécharger les instructions](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Vous êtes familiarisé avec [Pages Razor](xref:razor-pages/index). Les programmeurs débutants doivent lire [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) avant de démarrer cette série.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un bon moyen d’obtenir de l’aide est de publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>L’application web Contoso University

L’application générée dans ces didacticiels est le site web de base d’une université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis. Le didacticiel est axé sur Core EF avec des pages Razor, et non sur l’interface utilisateur.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Créer l’application web Razor Pages ContosoUniversity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **ContosoUniversity**. Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.
* Sélectionnez **ASP.NET Core 2.1** dans la liste déroulante, puis sélectionnez **Application web**.

Pour les images des étapes précédentes, consultez [Créer une application web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Exécuter l’application.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Configurer le style du site

Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site. Mettez à jour *Pages/Shared/_Layout.cshtml* avec les changements suivants :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Ajoutez des entrées de menu pour **Students**, **Courses**, **Instructors** et **Departments**, et supprimez l’entrée de menu **Contact**.

Les modifications sont mises en surbrillance. (Tout le balisage n’est *pas* affiché.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Créer le modèle de données

Créez des classes d’entités pour l’application Contoso University. Commencez par les trois entités suivantes :

![Diagramme de modèle de données Cours-Inscription-Étudiant](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`. Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`. Un étudiant peut s'inscrire dans n’importe quel nombre de cours. Un cours peut avoir une quantité illimitée d’élèves inscrits.

Dans les sections suivantes, une classe pour chacune de ces entités est créée.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

Créez un dossier *Models*. Dans le dossier *Models*, créez un fichier de classe nommé *Student.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. Dans `classnameID`, `classname` est le nom de la classe. L’autre clé primaire reconnue automatiquement est `StudentID` dans l’exemple précédent.

La propriété `Enrollments` est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité. Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`. Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`. Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`. Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`. La table `Enrollment` a deux lignes avec `StudentID` = 1. `StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.

Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`. `ICollection<T>`peut être spécifié, ou un type tel que `List<T>` ou `HashSet<T>`. Lorsque `ICollection<T>` est utilisé, le EF Core crée une collection `HashSet<T>` par défaut. Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.

### <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Dans le dossier *Models*, créez un fichier *Enrollment.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

La propriété `EnrollmentID` est la clé primaire. Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l'entité `Student`. En général, les développeurs choisissent un modèle et l’utilisent pour tous les modèles de données. Dans un prochain didacticiel, du code sans classname sera indiqué pour rendre plus facile à implémenter l’héritage dans le modèle de données.

La propriété `Grade` est un `enum`. Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` peut être nulle. Un niveau qui a la valeur "null" est différent de zéro. Une note "null" signifie qu'une note n’est pas connud ou n’a pas encore été affectée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`, par conséquent, la propriété contient une seule entité `Student`. L'entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété comme une clé étrangère si elle est nommée `<navigation property name><primary key property name>`. Par exemple,`StudentID` pour la propriété de navigation `Student`, dans la mesure où `Student`, la clé primaire de l’entité est `ID`. Les propriétés de clé étrangère peuvent également être nommées `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

### <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Dans le dossier *Models*, créez un fichier *Course.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

La propriété `Enrollments` est une propriété de navigation. A une entité `Course` peut être associée à un nombre quelconque d'entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

## <a name="scaffold-the-student-model"></a>Générer automatiquement le modèle d’étudiant

Dans cette section, le modèle d’étudiant est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création (Create), de lecture (Read), de mise à jour (Update) et de suppression (Delete) (CRUD) pour le modèle d’étudiant.

* Générez le projet.
* Créez le dossier *Pages/Students*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students* > **Ajouter** > **Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Pages Razor avec Entity Framework (CRUD)** > **AJOUTER**.

Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :

* Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)** .
* Dans la ligne **Classe de contexte de données**, sélectionnez le signe **+** (plus) et remplacez le nom généré par **ContosoUniversity.Models.SchoolContext**.
* Dans la liste déroulante **Classe de contexte de données**, sélectionnez **ContosoUniversity.Models.SchoolContext**
* Sélectionnez **Ajouter**.

![Boîte de dialogue CRUD](intro/_static/s1.png)

Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) si vous rencontrez un problème à l’étape précédente.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez les commandes suivantes pour générer automatiquement le modèle d’étudiant.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Le processus de génération de modèles automatique a créé et changé les fichiers suivants :

### <a name="files-created"></a>Fichiers créés

* *Pages/Students* Create, Delete, Details, Edit, Index.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Mises à jour du fichier

* *Startup.cs* : Les changements apportés à ce fichier sont détaillés dans la section suivante.
* *appsettings.json* : La chaîne de connexion utilisée pour se connecter à une base de données locale est ajoutée.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Les composants qui requièrent ces services (tels que les Pages Razor) sont fournis à ces services via les paramètres du constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `ConfigureServices` dans *Startup.cs*. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.

## <a name="update-main"></a>Mettre à jour la méthode Main

Dans *Program.cs*, modifiez la méthode `Main` pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.
* Appelez [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Supprimez le contexte une fois la méthode `EnsureCreated` exécutée.

Le code suivant montre le fichier *Program.cs* mis à jour.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` garantit que la base de données existe pour le contexte. Si elle existe, aucune action n’est effectuée. Si elle n’existe pas, la base de données et tous ses schéma sont créés. `EnsureCreated` n’utilise pas de migrations pour créer la base de données. Une base de données créée avec `EnsureCreated` ne peut pas être mise à jour à l’aide de migrations par la suite.

`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :

* Supprimez la base de données.
* Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).
* Exécuter l’application.
* `EnsureCreated` crée une base de données avec la colonne `EmailAddress`.

`EnsureCreated` est pratique au début du développement quand le schéma évolue rapidement. Plus loin dans le tutoriel, la base de données est supprimée et les migrations sont utilisées.

### <a name="test-the-app"></a>Tester l’application

Exécutez l’application et acceptez la politique de cookies. Cette application ne conserve pas les informations personnelles. Vous pouvez en savoir plus sur la politique de cookies à la section [Prise en charge du règlement général sur la protection des données (RGPD)](xref:security/gdpr).

* Sélectionnez le lien **Students**, puis **Créer nouveau**.
* Testez les liens Edit, Details et Delete.

## <a name="examine-the-schoolcontext-db-context"></a>Examiner le contexte de base de données SchoolContext

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données. Le contexte de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Mettez à jour *SchoolContext.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Le code en surbrillance crée une propriété [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

`DbSet<Enrollment>` et `DbSet<Course>` peuvent être omis. EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`. Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Ajouter du code pour initialiser la base de données avec des données de test

EF Core crée une base de données vide. Dans cette section, une méthode `Initialize` est écrite pour la remplir avec des données de test.

Dans le dossier *données*, créez un nouveau fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Remarque : Le code précédent utilise `Models` pour l’espace de noms (`namespace ContosoUniversity.Models`) au lieu de `Data`. `Models` est cohérent avec le code généré par l’outil de génération de modèles automatique. Pour plus d’informations, consultez [ce problème de génération de modèles automatique GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Le code vérifie s'il existe des étudiants dans la base de données. S’il n’y en a pas, la base de données est initialisée avec des données de test. Il charge les données de test dans les tableaux plutôt que dans les collections `List<T>` afin d’optimiser les performances.

La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données. Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.

Dans *Program.cs*, modifiez la méthode `Main` pour appeler `Initialize` :

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.

---

## <a name="view-the-db"></a>Afficher la base de données

Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID. Il sera donc « SchoolContext-{GUID} ». Le GUID est différent pour chaque utilisateur.
Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .

Développez le noeud **Tables**.

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Le mot clé `async` fait en sorte que le compilateur :
  * Génèrer des rappels pour les parties du corps de méthode.
  * Crée automatiquement l’objet [Task](/dotnet/api/system.threading.tasks.task) qui est retourné. Pour plus d’informations, consultez [Type de retour Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Le type de retour implicite `Task` représente le travail en cours.
* Le mot clé `await` fait en sorte que le compilateur fractionne la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Uniquement les instructions qui génèrent des requêtes ou des commandes à envoyer à la base de données sont exécutées de façon asynchrone. Ceci inclut, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, et `SaveChangesAsync`. mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.
* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).

Dans le didacticiel suivant, les opérations CRUD de base (créer, lire, mettre à jour, supprimer).



## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Next](xref:data/ef-rp/crud)

::: moniker-end
