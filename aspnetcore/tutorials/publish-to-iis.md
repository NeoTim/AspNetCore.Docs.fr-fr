---
title: Publier une application ASP.NET Core sur IIS
author: guardrex
description: Découvrez comment héberger une application ASP.NET Core sur un serveur IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 4ac7b3a2f738e443263dd888f556e0aff7c8099b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082382"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publier une application ASP.NET Core sur IIS

Par [Luke Latham](https://github.com/guardrex)

Ce tutoriel explique comment héberger une application ASP.NET Core sur un serveur IIS.

Ce tutoriel couvre les étapes suivantes :

> [!div class="checklist"]
> * Installer le bundle d’hébergement .NET Core sur Windows Server
> * Créer un site IIS dans le gestionnaire IIS
> * Déployer une application ASP.NET Core

## <a name="prerequisites"></a>Prérequis

* Installation du [SDK .NET Core](/dotnet/core/sdk) sur l’ordinateur de développement
* Configuration de Windows Server avec le rôle serveur **Serveur Web (IIS)** . Si votre serveur n’est pas configuré pour héberger des sites web avec IIS, suivez les instructions qui se trouvent dans la section *Configuration IIS* de l’article <xref:host-and-deploy/iis/index#iis-configuration>, puis revenez à ce tutoriel.

> [!WARNING]
> **La configuration d’IIS et la sécurité des sites web impliquent des concepts qui ne sont pas abordés dans ce tutoriel.** Avant d’héberger des applications de production sur IIS, consultez la [documentation IIS de Microsoft](https://www.iis.net/) et cet [article ASP.NET Core concernant l’hébergement sur IIS](xref:host-and-deploy/iis/index).
>
> Voici certains scénarios importants concernant l’hébergement sur IIS qui ne sont pas abordés dans ce tutoriel :
>
> * [Création d’une ruche de Registre pour la protection des données ASP.NET Core](xref:host-and-deploy/iis/index#data-protection)
> * [Configuration de la liste de contrôle d’accès (ACL) du pool d’applications](xref:host-and-deploy/iis/index#application-pool-identity)
> * Pour aborder les concepts de déploiement IIS, ce tutoriel déploie une application pour laquelle aucune sécurité HTTPS n’a été configurée dans IIS. Pour plus d’informations sur l’hébergement d’une application dans laquelle est activé le protocole HTTPS, consultez les rubriques relatives à la sécurité dans la section [Ressources supplémentaires](#additional-resources) de cet article. Vous trouverez des informations supplémentaires sur l’hébergement d’applications ASP.NET Core dans l’article <xref:host-and-deploy/iis/index>.

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

Installez le *bundle d’hébergement .NET Core* sur le serveur IIS. Le bundle installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS. Si le système n’a pas de connexion Internet, obtenez et installez [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) avant d’installer le bundle d’hébergement .NET Core.

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Exécutez le programme d’installation sur le serveur IIS.

1. Redémarrez le système ou exécutez **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commande.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le serveur IIS, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application.

1. Dans le Gestionnaire IIS, ouvrez le nœud du serveur dans le panneau **Connexions**. Cliquez avec le bouton de droite sur le dossier **Sites**. Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application que vous avez créé. Spécifiez la configuration **Liaison** et créez le site web en sélectionnant **OK**.

## <a name="create-an-aspnet-core-razor-pages-app"></a>Créer une application Razor Pages ASP.NET Core

Suivez le tutoriel <xref:getting-started> pour créer une application Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publier et déployer l’application

*Publier une application* signifie que vous créez une application compilée qui peut être hébergée par un serveur. *Déployer une application* signifie que vous déplacez l’application publiée vers un système d’hébergement. L’étape de publication est gérée par le [SDK .NET Core](/dotnet/core/sdk), alors que l’étape de déploiement peut être gérée à l’aide de différentes méthodes. Ce tutoriel utilise la méthode de déploiement par *dossier*, dans laquelle :

* L’application est publiée dans un dossier.
* Le contenu du dossier est déplacé vers le dossier du site IIS (le **chemin physique** du site dans le gestionnaire IIS).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.
1. Dans la boîte de dialogue **Choisir une cible de publication**, sélectionnez l’option de publication **Dossier**.
1. Configurez un chemin pour **Dossier ou partage de fichiers**.
   * Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage. L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.
   * Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS. Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.

# <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)

1. Dans une invite de commande, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Cliquez avec le bouton droit sur le projet dans **Solution**, puis sélectionnez **Publier** > **Publier sur un dossier**.
1. Configurez le chemin **Choisir un dossier**.
   * Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage. L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.
   * Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS. Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.

---

## <a name="browse-the-website"></a>Parcourir le site web

L’application est accessible dans un navigateur après avoir reçu la première requête. Envoyez une requête à l’application au niveau de la liaison de point de terminaison que vous avez établie dans le gestionnaire IIS pour le site.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Installer le bundle d’hébergement .NET Core sur Windows Server
> * Créer un site IIS dans le gestionnaire IIS
> * Déployer une application ASP.NET Core

Pour plus d’informations sur l’hébergement des applications ASP.NET Core sur IIS, consultez l’article de présentation d’IIS :

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Ressources supplémentaires

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Articles de la documentation ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Articles relatifs au déploiement d’applications ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publier une application web sur un dossier à l’aide de Visual Studio pour Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Articles sur la configuration HTTPS IIS

* [Configuration du protocole SSL dans le gestionnaire IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Configuration du protocole SSL sur IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Articles sur IIS et Windows Server

* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)
