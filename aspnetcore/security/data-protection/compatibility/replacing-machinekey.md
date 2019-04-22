---
title: Remplacez la méthode machineKey ASP.NET dans ASP.NET Core
author: rick-anderson
description: Découvrez comment remplacer machineKey dans ASP.NET pour autoriser l’utilisation d’un système de protection de données nouvelle et plus sûre.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705547"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="80f3e-103">Remplacez la méthode machineKey ASP.NET dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80f3e-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="80f3e-104">L’implémentation de la `<machineKey>` élément dans ASP.NET [est remplaçable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="80f3e-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="80f3e-105">Ainsi, la plupart des appels aux routines de chiffrement ASP.NET puissent être acheminés via un mécanisme de protection des données de remplacement, y compris le nouveau système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="80f3e-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="80f3e-106">Installation de package</span><span class="sxs-lookup"><span data-stu-id="80f3e-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="80f3e-107">Le nouveau système de protection des données peut uniquement être installé dans une application ASP.NET existante ciblant .NET 4.5.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="80f3e-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="80f3e-108">Installation échouera si l’application cible .NET 4.5 ou diminuer.</span><span class="sxs-lookup"><span data-stu-id="80f3e-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="80f3e-109">Pour installer le nouveau système de protection des données dans un projet de 4.5.1+ ASP.NET existant, installez le package Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="80f3e-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="80f3e-110">Il instancie le système de protection de données à l’aide du [configuration par défaut](xref:security/data-protection/configuration/default-settings) paramètres.</span><span class="sxs-lookup"><span data-stu-id="80f3e-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="80f3e-111">Lorsque vous installez le package, il insère une ligne dans *Web.config* qui indique à ASP.NET pour l’utiliser pour [plus les opérations de chiffrement](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), y compris l’authentification par formulaire, l’état d’affichage et les appels à MachineKey.Protect.</span><span class="sxs-lookup"><span data-stu-id="80f3e-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="80f3e-112">La ligne est insérée se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="80f3e-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="80f3e-113">Vous pouvez indiquer si le nouveau système de protection de données est actif en examinant les champs comme `__VIEWSTATE`, qui doit commencer par « CfDJ8 » comme dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="80f3e-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="80f3e-114">« CfDJ8 » est la représentation en base64 de l’en-tête de magie « 09 F0 C9 F0 » qui identifie une charge utile protégée par le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="80f3e-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="80f3e-115">configuration de package</span><span class="sxs-lookup"><span data-stu-id="80f3e-115">Package configuration</span></span>

<span data-ttu-id="80f3e-116">Le système de protection des données est instancié avec une configuration par défaut de zéro.</span><span class="sxs-lookup"><span data-stu-id="80f3e-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="80f3e-117">Toutefois, étant donné que par défaut, les clés sont conservés dans le système de fichiers local, cela ne fonctionne pas pour les applications qui sont déployées dans une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="80f3e-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="80f3e-118">Pour résoudre ce problème, vous pouvez fournir de configuration en créant un type qui sous-classe DataProtectionStartup et remplace sa méthode ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="80f3e-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="80f3e-119">Voici un exemple d’un type de démarrage de protection des données personnalisées qui configuré à la fois où les clés sont conservées et comment elles sont chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="80f3e-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="80f3e-120">Elle remplace également la stratégie d’isolation d’application par défaut en fournissant son propre nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="80f3e-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="80f3e-121">Vous pouvez également utiliser `<machineKey applicationName="my-app" ... />` à la place d’un appel explicite à SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="80f3e-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="80f3e-122">Il s’agit d’un mécanisme pratique de ne pas forcer au développeur de créer un type dérivé de DataProtectionStartup si toutes les il souhaite configurer a été définissant le nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="80f3e-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="80f3e-123">Pour activer cette configuration personnalisée, revenez au fichier Web.config et recherchez le `<appSettings>` élément qui installer le package ajouté au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="80f3e-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="80f3e-124">Il doit ressembler le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="80f3e-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="80f3e-125">Renseignez la valeur vide avec le nom qualifié d’assembly du type dérivé DataProtectionStartup que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="80f3e-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="80f3e-126">Si le nom de l’application est DataProtectionDemo, cela ressemblerait le ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="80f3e-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="80f3e-127">Le système de protection des données qui vient d’être configuré est maintenant prêt à être utilisé à l’intérieur de l’application.</span><span class="sxs-lookup"><span data-stu-id="80f3e-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
