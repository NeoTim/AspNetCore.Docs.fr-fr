---
title: Héberger et déployer des ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment héberger et déployer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103678"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Héberger et déployer des ASP.NET CoreBlazor

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publier l’application

Les applications sont publiées pour le déploiement dans la configuration Release.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **générer**  >  **publier {application}** dans la barre de navigation.
1. Sélectionnez l’onglet *Cible de publication*. Pour publier localement, sélectionnez **Dossier**.
1. Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement. Cliquez sur le bouton **Publier**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Sélectionnez **générer**  >  **publier dans le dossier**.
1. Confirmez le dossier pour recevoir les ressources publiées et sélectionnez **publier**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) pour publier l’application avec une configuration Release :

```dotnetcli
dotnet publish -c Release
```

---

La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement. Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.

Emplacements de publication :

* BlazorWebassembly
  * Autonome : l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* . Pour déployer l’application en tant que site statique, copiez le contenu du dossier *wwwroot* sur l’hôte de site statique.
  * Hébergé : l' Blazor application Webassembly cliente est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que toutes les autres ressources Web statiques de l’application serveur. Déployez le contenu du dossier de *publication* sur l’hôte.
* BlazorServeur : l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish* . Déployez le contenu du dossier de *publication* sur l’hôte.

Les ressources du dossier sont déployées sur le serveur web. Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.

## <a name="app-base-path"></a>Chemin de base de l’application

Le *chemin d’accès de base* de l’application est le chemin de l’URL racine de l’application. Considérez les éléments suivants ASP.NET Core application et Blazor sous-application :

* L’application ASP.NET Core est nommée `MyApp` :
  * L’application réside physiquement dans *d:/MyApp*.
  * Les demandes sont reçues à l’adresse `https://www.contoso.com/{MYAPP RESOURCE}` .
* Une Blazor application nommée `CoolApp` est une sous-application de `MyApp` :
  * La sous-application réside physiquement dans *d:/MyApp/coolapp*.
  * Les demandes sont reçues à l’adresse `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Si vous ne spécifiez pas de configuration supplémentaire pour `CoolApp` , la sous-application dans ce scénario n’a aucune connaissance de son emplacement sur le serveur. Par exemple, l’application ne peut pas construire des URL relatives correctes pour ses ressources sans savoir qu’elle réside sur le chemin d’URL relatif `/CoolApp/` .

Pour fournir la configuration du Blazor chemin d’accès de base de l’application `https://www.contoso.com/CoolApp/` , l’attribut de la `<base>` balise `href` est défini sur le chemin d’accès racine relatif dans le fichier *pages/_Host. cshtml* ( Blazor Server) ou *wwwroot/index.html* ( Blazor webassembly) :

```html
<base href="/CoolApp/">
```

BlazorLes applications serveur définissent également le chemin d’accès de base côté serveur en appelant <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> dans le pipeline de demande de l’application `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

En fournissant le chemin d’URL relatif, un composant qui ne se trouve pas dans le répertoire racine peut construire des URL relatives au chemin d’accès racine de l’application. Les composants à différents niveaux de la structure de répertoires peuvent créer des liens vers d’autres ressources à des emplacements de l’application. Le chemin d’accès de base de l’application est également utilisé pour intercepter les liens hypertexte sélectionnés où la `href` cible du lien se trouve dans l’espace URI du chemin d’accès de base de l’application. Le Blazor routeur gère la navigation interne.

Dans de nombreux scénarios d’hébergement, le chemin d’URL relatif à l’application est la racine de l’application. Dans ce cas, le chemin d’accès de base de l’URL relative de l’application est une barre oblique ( `<base href="/" />` ), qui est la configuration par défaut pour une Blazor application. Dans d’autres scénarios d’hébergement, tels que les pages GitHub et les sous-applications IIS, le chemin d’accès de base de l’application doit être défini sur le chemin d’URL relatif du serveur de l’application.

Pour définir le chemin d’accès de base de l’application, mettez à jour la `<base>` balise dans les `<head>` éléments tag du fichier *pages/_Host. cshtml* ( Blazor Server) ou *wwwroot/index.html* ( Blazor webassembly). Affectez `href` à l’attribut la valeur `/{RELATIVE URL PATH}/` (la barre oblique de fin est requise), où `{RELATIVE URL PATH}` est le chemin d’URL relatif complet de l’application.

Pour une Blazor application Webassembly avec un chemin d’URL relatif non racine (par exemple, `<base href="/CoolApp/">` ), l’application ne parvient pas à trouver ses ressources lorsqu’elle est *exécutée localement*. Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution. N’incluez pas de barre oblique finale. Pour passer l’argument de base Path lors de l’exécution locale de l’application, exécutez la `dotnet run` commande à partir du répertoire de l’application avec l' `--pathbase` option :

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pour une Blazor application Webassembly avec un chemin d’URL relatif `/CoolApp/` ( `<base href="/CoolApp/">` ), la commande est la suivante :

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L' Blazor application Webassembly répond localement à l’adresse `http://localhost:port/CoolApp` .

## <a name="deployment"></a>Déploiement

Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>