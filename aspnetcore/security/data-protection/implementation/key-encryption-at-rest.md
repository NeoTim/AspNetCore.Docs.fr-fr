---
title: Chiffrement à clé au repos dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation du chiffrement à clé de protection des données ASP.NET Core au repos.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658388"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Chiffrement à clé au repos dans ASP.NET Core

Le système de protection [des données utilise par défaut un mécanisme de découverte](xref:security/data-protection/configuration/default-settings) pour déterminer comment les clés de chiffrement doivent être chiffrées au repos. Le développeur peut remplacer le mécanisme de découverte et spécifier manuellement comment les clés doivent être chiffrées au repos.

> [!WARNING]
> Si vous spécifiez un [emplacement de persistance de clé](xref:security/data-protection/implementation/key-storage-providers)explicite, le système de protection des données annule l’inscription du mécanisme de chiffrement à clé par défaut au repos. Par conséquent, les clés ne sont plus chiffrées au repos. Nous vous recommandons de [spécifier un mécanisme de chiffrement à clé explicite](xref:security/data-protection/implementation/key-encryption-at-rest) pour les déploiements de production. Les options du mécanisme de chiffrement au repos sont décrites dans cette rubrique.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Pour stocker des clés dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurez le système avec [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) dans la classe `Startup` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Pour plus d’informations, consultez [configurer la protection des données ASP.net Core : ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>DPAPI Windows

**S’applique uniquement aux déploiements Windows.**

Lorsque Windows DPAPI est utilisé, le matériel de clé est chiffré avec [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) avant d’être conservé dans le stockage. DPAPI est un mécanisme de chiffrement approprié pour les données qui ne sont jamais lues en dehors de l’ordinateur actuel (bien qu’il soit possible de sauvegarder ces clés jusqu’à Active Directory. consultez [DPAPI et profils itinérants](https://support.microsoft.com/kb/309408/#6)). Pour configurer le chiffrement de la clé au repos DPAPI, appelez l’une des méthodes d’extension [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Si `ProtectKeysWithDpapi` est appelée sans paramètre, seul le compte d’utilisateur Windows actuel peut déchiffrer l’anneau de clé persistant. Vous pouvez éventuellement spécifier que n’importe quel compte d’utilisateur sur l’ordinateur (pas seulement le compte d’utilisateur actuel) soit en mesure de déchiffrer l’anneau de clé :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certificat X.509

Si l’application est répartie sur plusieurs ordinateurs, il peut être pratique de distribuer un certificat X. 509 partagé sur les ordinateurs et de configurer les applications hébergées pour utiliser le certificat pour le chiffrement des clés au repos :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

En raison des limitations de .NET Framework, seuls les certificats avec des clés privées CAPI sont pris en charge. Consultez le contenu ci-dessous pour obtenir des solutions possibles à ces limitations.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Ce mécanisme est disponible uniquement sur Windows 8/Windows Server 2012 ou version ultérieure.**

À compter de Windows 8, le système d’exploitation Windows prend en charge DPAPI-GN (également appelé CNG DPAPI). Pour plus d’informations, consultez [à propos de CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Le principal est encodé sous la forme d’une règle de descripteur de protection. Dans l’exemple suivant qui appelle [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), seul l’utilisateur joint au domaine avec le SID spécifié peut déchiffrer l’anneau de clé :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Il y a également une surcharge sans paramètre de `ProtectKeysWithDpapiNG`. Utilisez cette méthode pratique pour spécifier la règle « SID = {CURRENT_ACCOUNT_SID} », où *CURRENT_ACCOUNT_SID* est le SID du compte d’utilisateur Windows actuel :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

Dans ce scénario, le contrôleur de domaine Active Directory est responsable de la distribution des clés de chiffrement utilisées par les opérations DPAPI-GN. L’utilisateur cible peut déchiffrer la charge utile chiffrée à partir de n’importe quel ordinateur appartenant à un domaine (à condition que le processus s’exécute sous son identité).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Chiffrement basé sur les certificats avec Windows DPAPI-GN

Si l’application s’exécute sur Windows 8.1/Windows Server 2012 R2 ou version ultérieure, vous pouvez utiliser Windows DPAPI-GN pour effectuer un chiffrement basé sur les certificats. Utilisez la chaîne de descripteur de règle « CERTIFICATe = HashId : empreinte numérique », où *empreinte* est l’empreinte numérique SHA1 codée au format hexadécimal du certificat :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Toute application pointée sur ce référentiel doit s’exécuter sur Windows 8.1/Windows Server 2012 R2 ou version ultérieure pour déchiffrer les clés.

## <a name="custom-key-encryption"></a>Chiffrement à clé personnalisée

Si les mécanismes intégrés ne sont pas appropriés, le développeur peut spécifier son propre mécanisme de chiffrement à clé en fournissant un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personnalisé.
