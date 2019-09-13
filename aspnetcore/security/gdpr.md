---
title: Prise en charge des Règlement général sur la protection des données (RGPD) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment accéder aux points d’extension RGPD dans une application Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 1086c22c2f3c27373d8cb779f4b1d8eb6792ec2e
ms.sourcegitcommit: 2fa0ffe82a47c7317efc9ea908365881cbcb8ed7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2019
ms.locfileid: "69572875"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Prise en charge de l’UE Règlement général sur la protection des données (RGPD) dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fournit des API et des modèles pour vous aider à répondre à certaines exigences de l' [ue Règlement général sur la protection des données (RGPD)](https://www.eugdpr.org/) :

::: moniker range=">= aspnetcore-3.0"

* Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d’utilisation des cookies.
* La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site.

Pour activer la fonctionnalité de consentement de cookie par défaut comme celle figurant dans les modèles ASP.NET Core 2,2 dans une application générée par le modèle ASP.NET Core 3,0 :

* Ajoutez `using Microsoft.AspNetCore.Http` à la liste des directives using.
* Ajoutez [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) à `Startup.ConfigureServices` et [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) à `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Ajoutez le consentement de cookie partiel au fichier _ *Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Ajoutez le  *\_fichier CookieConsentPartial. cshtml* au projet :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Sélectionnez la version ASP.NET Core 2,2 de cet article pour en savoir plus sur la fonctionnalité de consentement des cookies.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d’utilisation des cookies.
* Une fonctionnalité de consentement de cookie vous permet de demander (et de suivre) le consentement de vos utilisateurs pour le stockage des informations personnelles. Si un utilisateur n’a pas consenti à la collecte de données et que l’application a [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) défini sur `true`, les cookies non essentiels ne sont pas envoyés au navigateur.
* Les cookies peuvent être marqués comme étant essentiels. Les cookies essentiels sont envoyés au navigateur même lorsque l’utilisateur n’a pas donné son consentement et que le suivi est désactivé.
* [Les cookies TempData et de session](#tempdata) ne sont pas fonctionnels lorsque le suivi est désactivé.
* La page [gestion des identités](#pd) fournit un lien permettant de télécharger et de supprimer des données utilisateur.

L' [exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) vous permet de tester la plupart des points d’extension RGPD et des API ajoutés aux modèles ASP.net Core 2,1. Consultez le fichier [Lisez-moi](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pour obtenir des instructions de test.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core la prise en charge RGPD dans le code généré par modèle

Les projets Razor Pages et MVC créés avec les modèles de projet incluent la prise en charge RGPD suivante :

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) et [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) sont définis dans la `Startup` classe.
* [Vue partielle](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)CookieConsentPartial. cshtml.  *\_* Un bouton **accepter** est inclus dans ce fichier. Quand l’utilisateur clique sur le bouton **accepter** , le consentement de stocker les cookies est fourni.
* La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site. Le fichier CookieConsentPartial. cshtml génère un lien vers la page de confidentialité.  *\_*
* Pour les applications créées avec des comptes d’utilisateur individuels, la page gérer fournit des liens permettant de télécharger et de supprimer des [données utilisateur personnelles](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions et UseCookiePolicy

Les [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) sont initialisés `Startup.ConfigureServices`dans :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) est appelé dans `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_Vue partielle de CookieConsentPartial. cshtml

Vue partielle CookieConsentPartial. cshtml :  *\_*

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Voici ce qui est partiel :

* Obtient l’état de suivi de l’utilisateur. Si l’application est configurée pour exiger un consentement, l’utilisateur doit donner son consentement avant de pouvoir effectuer le suivi des cookies. Si le consentement est requis, le volet de consentement du cookie est corrigé en haut de la barre de navigation créée par le  *\_fichier Layout. cshtml* .
* Fournit un élément `<p>` html pour résumer votre stratégie de confidentialité et d’utilisation des cookies.
* Fournit un lien vers la page de confidentialité ou une vue qui vous permet de détailler la politique de confidentialité de votre site.

## <a name="essential-cookies"></a>Cookies essentiels

Si le consentement de stocker des cookies n’a pas été fourni, seuls les cookies marqués comme essentiels sont envoyés au navigateur. Le code suivant rend un cookie essentiel :

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>Les cookies du fournisseur TempData et de l’état de session ne sont pas essentiels

Le cookie du [fournisseur TempData](xref:fundamentals/app-state#tempdata) n’est pas essentiel. Si le suivi est désactivé, le fournisseur TempData n’est pas fonctionnel. Pour activer le fournisseur TempData lorsque le suivi est désactivé, marquez le cookie TempData comme `Startup.ConfigureServices`essentiel dans :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

Les cookies d' [État de session](xref:fundamentals/app-state) ne sont pas essentiels. L’état de session n’est pas fonctionnel lorsque le suivi est désactivé. Le code suivant rend les cookies de session essentiels :

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Données personnelles

ASP.NET Core applications créées avec des comptes d’utilisateur individuels incluent du code pour télécharger et supprimer des données personnelles.

Sélectionnez le nom d’utilisateur, puis sélectionnez **données personnelles**:

![Page gérer les données personnelles](gdpr/_static/pd.png)

Remarques :

* Pour générer le `Account/Manage` code, consultez [structure Identity](xref:security/authentication/scaffold-identity).
* Les liens **Delete** et **Download** agissent uniquement sur les données d’identité par défaut. Les applications qui créent des données utilisateur personnalisées doivent être étendues pour supprimer/télécharger les données utilisateur personnalisées. Pour plus d’informations, consultez [Ajouter, télécharger et supprimer des données utilisateur personnalisées dans identité](xref:security/authentication/add-user-data).
* Les jetons enregistrés pour l’utilisateur qui sont stockés dans la table `AspNetUserTokens` de base de données d’identité sont supprimés lorsque l’utilisateur est supprimé par le biais du comportement de suppression en cascade en raison de la [clé étrangère](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* L' [authentification du fournisseur externe](xref:security/authentication/social/index), telle que Facebook et Google, n’est pas disponible avant que la stratégie de cookie soit acceptée.

::: moniker-end

## <a name="encryption-at-rest"></a>Chiffrement au repos

Certaines bases de données et mécanismes de stockage permettent le chiffrement au repos. Chiffrement au repos :

* Chiffre automatiquement les données stockées.
* Chiffre sans configuration, programmation ou autre travail pour le logiciel qui accède aux données.
* Est l’option la plus simple et la plus sûre.
* Permet à la base de données de gérer les clés et le chiffrement.

Par exemple :

* Microsoft SQL et Azure SQL fournissent des [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure chiffre par défaut la base de données](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Les objets BLOB, les fichiers, les tables et le stockage de files d’attente Azure sont chiffrés par défaut](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Pour les bases de données qui ne fournissent pas de chiffrement intégré au repos, vous pouvez utiliser le chiffrement de disque pour offrir la même protection. Par exemple :

* [BitLocker pour Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux :
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [RGPD-ajout d’un bouton de consentement REVOKE dans ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
