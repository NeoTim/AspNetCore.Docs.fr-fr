---
title: 'Tutoriel : Bien démarrer avec des pages Razor dans ASP.NET Core'
author: rick-anderson
description: Cette série de tutoriels montre comment utiliser Razor Pages dans ASP.NET Core. Découvrez comment créer un modèle, générer du code pour Razor Pages, utiliser Entity Framework Core et SQL Server pour l’accès aux données, ajouter des fonctionnalités de recherche, ajouter la validation d’entrée et mettre à jour le modèle à l’aide de migrations.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 67a5fcee0a37861fd39a018443edbc0b9e513213
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487659"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutoriel : Bien démarrer avec des pages Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
C’est le premier d’une série de tutoriels, qui décrit les principes fondamentaux liés à la génération d’une application web de Razor Pages dans ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Dans ce didacticiel, vous avez effectué les actions suivantes :

> [!div class="checklist"]
> * Créer une application web Razor Pages.
> * Exécuter l’application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposez d’une application web Razor Pages fonctionnelle et générée dans les didacticiels suivants.

![Page d’accueil ou page d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Créer une application web Pages Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 3.0** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La commande `dotnet new` crée un nouveau projet Razor Pages dans le dossier *RazorPagesMovie*.
  * La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.

* Après que l’icône en forme de flamme de la barre d’état d’OmniSharp devient verte, une boîte de dialogue indique **Les ressources requises pour créer et déboguer sont manquantes dans « RazorPagesMovie ». Faut-il les ajouter ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Sélectionnez **.NET Core** > **Application** > **Application web** > **Suivant**.

  ![macOS - Boîte de dialogue Nouveau projet](razor-pages-start/_static/webapp.png)

* Dans la boîte de dialogue **Configurer votre nouvelle API web ASP.NET Core**, définissez **Framework cible** sur **.NET Core 3.0**.

  ![Sélection de .NET Core 3.0 pour macOS](razor-pages-start/_static/targetframework3.png)

* Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a>Ouvrir le projet

Dans Visual Studio, sélectionnez **Fichier > Ouvrir**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l'application

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Appuyez sur **Alt-Cmd-Entrée** pour lancer l’exécution sans le débogueur. Vous pouvez également accéder à la barre de menus et accéder à Exécuter > Démarrer sans débogage.

  Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient les pages Razor et les fichiers de prise en charge. Chaque page Razor est une paire de fichiers :

* Un fichier *.cshtml* qui contient le balisage HTML avec du code C# en utilisant la syntaxe Razor.
* Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout.cshtml* configure les éléments d’interface communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d’informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS. Pour plus d’informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient les données de configuration, comme les chaînes de connexion. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contient le code qui configure le comportement de l’application. Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant dans la série :

> [!div class="step-by-step"]
> [Ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Ceci est le premier didacticiel d’une série. [Cette série](xref:tutorials/razor-pages/index) décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Dans ce didacticiel, vous avez effectué les actions suivantes :

> [!div class="checklist"]
> * Créer une application web Razor Pages.
> * Exécuter l’application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposez d’une application web Razor Pages fonctionnelle et générée dans les didacticiels suivants.

![Page d’accueil ou page d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Créer une application web Pages Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.

* Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nommez le projet **RazorPagesMovie**. Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La commande `dotnet new` crée un nouveau projet Razor Pages dans le dossier *RazorPagesMovie*.
  * La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.

* Après que l’icône en forme de flamme de la barre d’état d’OmniSharp devient verte, une boîte de dialogue indique **Les ressources requises pour créer et déboguer sont manquantes dans « RazorPagesMovie ». Faut-il les ajouter ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

À partir d’un terminal, exécutez la commande suivante :

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

Les commandes précédentes utilisent le [CLI .NET Core](/dotnet/core/tools/dotnet) pour créer un projet Razor Pages.

## <a name="open-the-project"></a>Ouvrir le projet

Dans Visual Studio, sélectionnez **Fichier > Ouvrir**, puis sélectionnez le fichier *RazorPagesMovie.csproj*.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l'application

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou page d’index](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou page d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou page d’index](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou page d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.

  Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Page d’accueil ou page d’index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Page d’accueil ou page d’index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient les pages Razor et les fichiers de prise en charge. Chaque page Razor est une paire de fichiers :

* Un fichier *.cshtml* qui contient le balisage HTML avec du code C# en utilisant la syntaxe Razor.
* Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout.cshtml* configure les éléments d’interface communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d’informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS. Pour plus d’informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient les données de configuration, comme les chaînes de connexion. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contient le code qui configure le comportement de l’application, comme le fait qu’elle exige un consentement pour les cookies. Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant dans la série :

> [!div class="step-by-step"]
> [Ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end
