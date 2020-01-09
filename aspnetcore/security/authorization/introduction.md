---
title: Présentation de l’autorisation dans ASP.NET Core
author: rick-anderson
description: Découvrez les principes de base de l’autorisation et le fonctionnement de l’autorisation dans les applications ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: b5e60b3c256941fff5e54e1a02e077c34c535902
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693867"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="476bb-103">Présentation de l’autorisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="476bb-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="476bb-104">Le terme Autorisation désigne le processus qui détermine ce qu’un utilisateur est en mesure d’effectuer.</span><span class="sxs-lookup"><span data-stu-id="476bb-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="476bb-105">Par exemple, un utilisateur administratif est autorisé à créer une bibliothèque de documents, ajoutez les documents, modifier des documents et les supprimer.</span><span class="sxs-lookup"><span data-stu-id="476bb-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="476bb-106">Un utilisateur non-administrateur, utilisant la bibliothèque est uniquement autorisé à lire les documents.</span><span class="sxs-lookup"><span data-stu-id="476bb-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="476bb-107">L’autorisation est orthogonale et indépendante de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="476bb-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="476bb-108">Toutefois, l’autorisation nécessite un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="476bb-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="476bb-109">L’authentification est le processus qui consiste à déterminer l’identité d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="476bb-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="476bb-110">L’authentification peut créer une ou plusieurs identités pour l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="476bb-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="476bb-111">Pour plus d’informations sur l’authentification dans ASP.NET Core, consultez <xref:security/authentication/index>.</span><span class="sxs-lookup"><span data-stu-id="476bb-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="476bb-112">Types d’autorisation</span><span class="sxs-lookup"><span data-stu-id="476bb-112">Authorization types</span></span>

<span data-ttu-id="476bb-113">L'autorisation de ASP.NET Core fournit un simple [rôle](xref:security/authorization/roles) déclaratif et un [modèle enrichie basée sur la stratégie](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="476bb-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="476bb-114">L’autorisation est exprimée dans les exigences et les gestionnaires évaluent les revendications d’un utilisateur par rapport aux exigences.</span><span class="sxs-lookup"><span data-stu-id="476bb-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="476bb-115">Les vérifications impératives peuvent reposer sur des stratégies simples ou des stratégies qui évaluent l’identité de l’utilisateur et les propriétés de la ressource à laquelle l’utilisateur tente d’accéder.</span><span class="sxs-lookup"><span data-stu-id="476bb-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="476bb-116">Espaces de noms des</span><span class="sxs-lookup"><span data-stu-id="476bb-116">Namespaces</span></span>

<span data-ttu-id="476bb-117">Les composants d’autorisation, y compris les attributs `AuthorizeAttribute` et `AllowAnonymousAttribute` se trouvent dans l'espace de noms `Microsoft.AspNetCore.Authorization`.</span><span class="sxs-lookup"><span data-stu-id="476bb-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="476bb-118">Consultez la documentation sur l' [autorisation simple](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="476bb-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
