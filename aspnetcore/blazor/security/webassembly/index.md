---
title: Sécuriser ASP.NET Core Blazor Webassembly
author: guardrex
description: Découvrez comment sécuriser des applications Blazor WebAssemlby en tant qu’applications à page unique (spas).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/index
ms.openlocfilehash: 5b20ab96f5419a86ab162fdcf7a57020f6aa7227
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103967"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Sécuriser ASP.NET Core Blazor Webassembly

Par [Javier Calvarro Nelson](https://github.com/javiercn)

BlazorLes applications webassembly sont sécurisées de la même façon que les applications à page unique (SPAs). Il existe plusieurs approches pour authentifier les utilisateurs dans la base de code, mais l’approche la plus courante et la plus complète consiste à utiliser une implémentation basée sur le [protocole OAuth 2,0](https://oauth.net/), par exemple [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Bibliothèque d’authentification

BlazorWebassembly prend en charge l’authentification et l’autorisation d’applications à l’aide de OIDC via la bibliothèque [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . La bibliothèque fournit un ensemble de primitives pour l’authentification en toute transparence par rapport aux principaux de ASP.NET Core. La bibliothèque intègre ASP.NET Core Identity avec la prise en charge des autorisations d’API basée sur le [ Identity serveur](https://identityserver.io/). La bibliothèque peut s’authentifier auprès d’un Identity fournisseur tiers (IP) qui prend en charge OIDC, qui sont appelés fournisseurs OpenID (op).

La prise en charge de l’authentification dans Blazor Webassembly est basée sur la bibliothèque de *oidc-client.js* , qui est utilisée pour gérer les détails du protocole d’authentification sous-jacent.

D’autres options d’authentification de la fonction de l’interauthentification existent, telles que l’utilisation de cookies SameSite. Toutefois, la conception de l’ingénierie de Blazor Webassembly est réglée sur OAuth et OIDC comme meilleure option pour l’authentification dans les Blazor applications webassembly. [L’authentification basée sur les jetons](xref:security/anti-request-forgery#token-based-authentication) basée sur des [jetons Web JSON (jetons JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a été choisie via [l’authentification basée](xref:security/anti-request-forgery#cookie-based-authentication) sur les cookies pour des raisons fonctionnelles et de sécurité :

* L’utilisation d’un protocole basé sur les jetons offre une surface d’attaque plus réduite, car les jetons ne sont pas envoyés dans toutes les demandes.
* Les points de terminaison de serveur ne nécessitent pas de protection contre la [falsification de requête intersites (CSRF)](xref:security/anti-request-forgery) , car les jetons sont envoyés explicitement. Cela vous permet d’héberger des applications webassembly en même temps que des Blazor applications MVC ou Razor pages.
* Les jetons ont des autorisations plus étroites que les cookies. Par exemple, les jetons ne peuvent pas être utilisés pour gérer le compte d’utilisateur ou modifier le mot de passe d’un utilisateur, sauf si une telle fonctionnalité est implémentée de manière explicite.
* Les jetons ont une durée de vie brève, par défaut d’une heure, qui limite la fenêtre d’attaque. Les jetons peuvent également être révoqués à tout moment.
* Les jetons JWT autonomes offrent des garanties au client et au serveur sur le processus d’authentification. Par exemple, un client a la possibilité de détecter et de valider que les jetons qu’il reçoit sont légitimes et ont été émis dans le cadre d’un processus d’authentification donné. Si un tiers tente de basculer un jeton au milieu du processus d’authentification, le client peut détecter le jeton commuté et éviter de l’utiliser.
* Les jetons avec OAuth et OIDC ne reposent pas sur l’agent utilisateur qui se comporte correctement pour garantir la sécurité de l’application.
* Les protocoles basés sur les jetons, tels que OAuth et OIDC, permettent d’authentifier et d’autoriser des applications hébergées et autonomes avec le même ensemble de caractéristiques de sécurité.

## <a name="authentication-process-with-oidc"></a>Processus d’authentification avec OIDC

La bibliothèque [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) offre plusieurs primitives pour implémenter l’authentification et l’autorisation à l’aide de OIDC. En général, l’authentification fonctionne comme suit :

* Lorsqu’un utilisateur anonyme sélectionne le bouton de connexion ou demande une page pour laquelle l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué, l’utilisateur est redirigé vers la page de connexion de l’application ( `/authentication/login` ).
* Dans la page de connexion, la bibliothèque d’authentification prépare une redirection vers le point de terminaison d’autorisation. Le point de terminaison d’autorisation est en dehors de l' Blazor application Webassembly et peut être hébergé à une origine distincte. Le point de terminaison est chargé de déterminer si l’utilisateur est authentifié et d’émettre un ou plusieurs jetons en réponse. La bibliothèque d’authentification fournit un rappel de connexion pour recevoir la réponse d’authentification.
  * Si l’utilisateur n’est pas authentifié, l’utilisateur est redirigé vers le système d’authentification sous-jacent, qui est généralement ASP.NET Core Identity .
  * Si l’utilisateur a déjà été authentifié, le point de terminaison d’autorisation génère les jetons appropriés et redirige le navigateur vers le point de terminaison de rappel de connexion ( `/authentication/login-callback` ).
* Lorsque l' Blazor application Webassembly charge le point de terminaison de rappel de connexion ( `/authentication/login-callback` ), la réponse d’authentification est traitée.
  * Si le processus d’authentification se termine correctement, l’utilisateur est authentifié et éventuellement renvoyé à l’URL protégée d’origine que l’utilisateur a demandée.
  * Si le processus d’authentification échoue pour une raison quelconque, l’utilisateur est envoyé à la page Échec de la connexion ( `/authentication/login-failed` ) et une erreur s’affiche.

## <a name="authorization"></a>Autorisation

Dans Blazor les applications Webassembly, les vérifications d’autorisation peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**

## <a name="refresh-tokens"></a>Jetons d’actualisation

Les jetons d’actualisation ne peuvent pas être sécurisés côté client dans les Blazor applications Webassembly. Par conséquent, les jetons d’actualisation ne doivent pas être envoyés à l’application pour une utilisation directe.

Les jetons d’actualisation peuvent être conservés et utilisés par l’application côté serveur dans une Blazor solution Webassembly hébergée pour accéder aux API tierces. Pour plus d’informations, consultez <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Conseils d’implémentation

Les Articles de cette *vue d’ensemble* fournissent des informations sur l’authentification des utilisateurs dans les Blazor applications webassembly sur des fournisseurs spécifiques.

BlazorApplications Webassembly autonomes :

* [Aide générale pour les fournisseurs OIDC et la bibliothèque d’authentification webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Comptes Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

BlazorApplications Webassembly hébergées :

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [IdentityServeurs](xref:blazor/security/webassembly/hosted-with-identity-server)

Pour plus d’informations sur la configuration, consultez <xref:blazor/security/webassembly/additional-scenarios> .