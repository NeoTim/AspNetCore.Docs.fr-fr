---
title: Extensibilité de la gestion de clés dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’extensibilité de la gestion de clés de Protection des données ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665878"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="5f7af-103">Extensibilité de la gestion de clés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f7af-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="5f7af-104">Lisez la section [gestion des clés](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) avant de lire cette section, car elle explique certains concepts fondamentaux sous-jacents à ces API.</span><span class="sxs-lookup"><span data-stu-id="5f7af-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="5f7af-105">Types qui implémentent les interfaces suivantes doivent être thread-safe pour les appelants plusieurs.</span><span class="sxs-lookup"><span data-stu-id="5f7af-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="5f7af-106">Clé</span><span class="sxs-lookup"><span data-stu-id="5f7af-106">Key</span></span>

<span data-ttu-id="5f7af-107">L’interface `IKey` est la représentation de base d’une clé dans chiffrement.</span><span class="sxs-lookup"><span data-stu-id="5f7af-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="5f7af-108">L’expression clé est utilisé ici dans le point de vue abstrait, pas dans le sens littéral de « clé de chiffrement ».</span><span class="sxs-lookup"><span data-stu-id="5f7af-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="5f7af-109">Une clé a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="5f7af-109">A key has the following properties:</span></span>

* <span data-ttu-id="5f7af-110">Dates d’activation, la création et l’expiration</span><span class="sxs-lookup"><span data-stu-id="5f7af-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="5f7af-111">État de révocation.</span><span class="sxs-lookup"><span data-stu-id="5f7af-111">Revocation status</span></span>

* <span data-ttu-id="5f7af-112">Identificateur de clé (un GUID)</span><span class="sxs-lookup"><span data-stu-id="5f7af-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="5f7af-113">En outre, `IKey` expose une méthode `CreateEncryptor` qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="5f7af-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="5f7af-114">En outre, `IKey` expose une méthode `CreateEncryptorInstance` qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="5f7af-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5f7af-115">Il n’existe pas d’API permettant de récupérer le matériel de chiffrement brut à partir d’une instance de `IKey`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="5f7af-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="5f7af-116">IKeyManager</span></span>

<span data-ttu-id="5f7af-117">L’interface `IKeyManager` représente un objet responsable du stockage, de la récupération et de la manipulation des clés générales.</span><span class="sxs-lookup"><span data-stu-id="5f7af-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="5f7af-118">Elle expose trois opérations de haut niveau :</span><span class="sxs-lookup"><span data-stu-id="5f7af-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="5f7af-119">Créer une nouvelle clé et conserver dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="5f7af-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="5f7af-120">Obtenir toutes les clés de stockage.</span><span class="sxs-lookup"><span data-stu-id="5f7af-120">Get all keys from storage.</span></span>

* <span data-ttu-id="5f7af-121">Révoquer une ou plusieurs clés et conserver les informations de révocation dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="5f7af-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="5f7af-122">L’écriture d’une `IKeyManager` est une tâche très avancée et la majorité des développeurs ne doivent pas la tenter.</span><span class="sxs-lookup"><span data-stu-id="5f7af-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="5f7af-123">Au lieu de cela, la plupart des développeurs doivent tirer parti des fonctionnalités offertes par la classe [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="5f7af-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="5f7af-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="5f7af-124">XmlKeyManager</span></span>

<span data-ttu-id="5f7af-125">Le type de `XmlKeyManager` est l’implémentation concrète de `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="5f7af-126">Il fournit plusieurs installations utiles, y compris le dépôt de clé et le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="5f7af-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="5f7af-127">Les clés de ce système sont représentées en tant qu’éléments XML (en particulier, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="5f7af-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="5f7af-128">`XmlKeyManager` dépend de plusieurs autres composants dans le cadre de la réalisation de ses tâches :</span><span class="sxs-lookup"><span data-stu-id="5f7af-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="5f7af-129">`AlgorithmConfiguration`, qui dicte les algorithmes utilisés par les nouvelles clés.</span><span class="sxs-lookup"><span data-stu-id="5f7af-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="5f7af-130">`IXmlRepository`, qui contrôle où les clés sont conservées dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="5f7af-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="5f7af-131">`IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="5f7af-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="5f7af-132">`IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.</span><span class="sxs-lookup"><span data-stu-id="5f7af-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="5f7af-133">`IXmlRepository`, qui contrôle où les clés sont conservées dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="5f7af-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="5f7af-134">`IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="5f7af-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="5f7af-135">`IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.</span><span class="sxs-lookup"><span data-stu-id="5f7af-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="5f7af-136">Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent comment ces composants sont reliés entre eux dans `XmlKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation2.png)

<span data-ttu-id="5f7af-138">*Création de clé/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="5f7af-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="5f7af-139">Dans l’implémentation de `CreateNewKey`, le composant `AlgorithmConfiguration` est utilisé pour créer un `IAuthenticatedEncryptorDescriptor`unique, qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="5f7af-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="5f7af-140">Si un récepteur de dépôt de clé est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="5f7af-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="5f7af-141">Le XML non chiffré est ensuite exécuté via un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="5f7af-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="5f7af-142">Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="5f7af-143">(Si aucune `IXmlEncryptor` n’est configurée, le document non chiffré est conservé dans le `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="5f7af-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Récupération de clé](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation1.png)

<span data-ttu-id="5f7af-146">*Création de clé/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="5f7af-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="5f7af-147">Dans l’implémentation de `CreateNewKey`, le composant `IAuthenticatedEncryptorConfiguration` est utilisé pour créer un `IAuthenticatedEncryptorDescriptor`unique, qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="5f7af-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="5f7af-148">Si un récepteur de dépôt de clé est présent, les données XML brutes (non chiffré) est fourni pour le récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="5f7af-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="5f7af-149">Le XML non chiffré est ensuite exécuté via un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="5f7af-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="5f7af-150">Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="5f7af-151">(Si aucune `IXmlEncryptor` n’est configurée, le document non chiffré est conservé dans le `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="5f7af-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Récupération de clé](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="5f7af-153">*Récupération de clé/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="5f7af-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="5f7af-154">Dans l’implémentation de `GetAllKeys`, les documents XML représentant les clés et les révocations sont lus à partir des `IXmlRepository`sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="5f7af-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="5f7af-155">Si ces documents sont chiffrées, le système sera automatiquement les déchiffrer.</span><span class="sxs-lookup"><span data-stu-id="5f7af-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="5f7af-156">`XmlKeyManager` crée les instances de `IAuthenticatedEncryptorDescriptorDeserializer` appropriées pour désérialiser les documents dans des instances de `IAuthenticatedEncryptorDescriptor`, qui sont ensuite encapsulées dans des instances de `IKey` individuelles.</span><span class="sxs-lookup"><span data-stu-id="5f7af-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="5f7af-157">Cette collection d’instances de `IKey` est retournée à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="5f7af-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="5f7af-158">Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document de format de stockage de clés](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="5f7af-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="5f7af-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-159">IXmlRepository</span></span>

<span data-ttu-id="5f7af-160">L’interface `IXmlRepository` représente un type qui peut conserver XML dans un magasin de stockage et récupérer du code XML.</span><span class="sxs-lookup"><span data-stu-id="5f7af-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="5f7af-161">Elle expose deux API :</span><span class="sxs-lookup"><span data-stu-id="5f7af-161">It exposes two APIs:</span></span>

* <span data-ttu-id="5f7af-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="5f7af-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="5f7af-163">Les implémentations de `IXmlRepository` n’ont pas besoin d’analyser le XML qui les traverse.</span><span class="sxs-lookup"><span data-stu-id="5f7af-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="5f7af-164">Ils doivent traiter les documents XML comme opaque et laissent les couches supérieures à vous inquiétez pas sur la génération et l’analyse de documents.</span><span class="sxs-lookup"><span data-stu-id="5f7af-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="5f7af-165">Il existe quatre types concrets intégrés qui implémentent `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="5f7af-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="5f7af-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="5f7af-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="5f7af-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="5f7af-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="5f7af-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="5f7af-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="5f7af-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="5f7af-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="5f7af-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="5f7af-174">Pour plus d’informations, consultez le document sur les [fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="5f7af-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="5f7af-175">L’inscription d’un `IXmlRepository` personnalisé est appropriée lors de l’utilisation d’un autre magasin de stockage (par exemple, stockage table Azure).</span><span class="sxs-lookup"><span data-stu-id="5f7af-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="5f7af-176">Pour modifier le référentiel par défaut au niveau de l’application, inscrivez une instance de `IXmlRepository` personnalisée :</span><span class="sxs-lookup"><span data-stu-id="5f7af-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="5f7af-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-177">IXmlEncryptor</span></span>

<span data-ttu-id="5f7af-178">L’interface `IXmlEncryptor` représente un type qui peut chiffrer un élément XML en texte brut.</span><span class="sxs-lookup"><span data-stu-id="5f7af-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="5f7af-179">Elle expose une seule API :</span><span class="sxs-lookup"><span data-stu-id="5f7af-179">It exposes a single API:</span></span>

* <span data-ttu-id="5f7af-180">Chiffrer (plaintextElement XElement) : EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="5f7af-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="5f7af-181">Si un `IAuthenticatedEncryptorDescriptor` sérialisé contient des éléments marqués comme « nécessite un chiffrement », `XmlKeyManager` exécutera ces éléments via la méthode de `Encrypt` de `IXmlEncryptor`configurée, et il conserve l’élément chiffré plutôt que l’élément de texte en clair dans le `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="5f7af-182">La sortie de la méthode `Encrypt` est un objet `EncryptedXmlInfo`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="5f7af-183">Cet objet est un wrapper qui contient à la fois le `XElement` chiffré résultant et le type qui représente un `IXmlDecryptor` qui peut être utilisé pour déchiffrer l’élément correspondant.</span><span class="sxs-lookup"><span data-stu-id="5f7af-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="5f7af-184">Il existe quatre types concrets intégrés qui implémentent `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="5f7af-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="5f7af-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="5f7af-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="5f7af-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="5f7af-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="5f7af-189">Pour plus d’informations, consultez le [document chiffrement à clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="5f7af-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="5f7af-190">Pour modifier le mécanisme par défaut de chiffrement par clé au repos à l’ensemble de l’application, inscrivez une instance de `IXmlEncryptor` personnalisée :</span><span class="sxs-lookup"><span data-stu-id="5f7af-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="5f7af-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="5f7af-191">IXmlDecryptor</span></span>

<span data-ttu-id="5f7af-192">L’interface `IXmlDecryptor` représente un type qui sait comment déchiffrer un `XElement` qui a été chiffré par le biais d’un `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="5f7af-193">Elle expose une seule API :</span><span class="sxs-lookup"><span data-stu-id="5f7af-193">It exposes a single API:</span></span>

* <span data-ttu-id="5f7af-194">Déchiffrer (encryptedElement XElement) : XElement</span><span class="sxs-lookup"><span data-stu-id="5f7af-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="5f7af-195">La méthode `Decrypt` annule le chiffrement effectué par `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="5f7af-196">En règle générale, chaque implémentation de `IXmlEncryptor` concrète aura une implémentation de `IXmlDecryptor` concrète correspondante.</span><span class="sxs-lookup"><span data-stu-id="5f7af-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="5f7af-197">Les types qui implémentent `IXmlDecryptor` doivent avoir l’un des deux constructeurs publics suivants :</span><span class="sxs-lookup"><span data-stu-id="5f7af-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="5f7af-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="5f7af-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="5f7af-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="5f7af-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="5f7af-200">La `IServiceProvider` passée au constructeur peut être null.</span><span class="sxs-lookup"><span data-stu-id="5f7af-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="5f7af-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="5f7af-201">IKeyEscrowSink</span></span>

<span data-ttu-id="5f7af-202">L’interface `IKeyEscrowSink` représente un type qui peut effectuer un tiers de confiance d’informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="5f7af-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="5f7af-203">Rappelez-vous que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement) et c’est ce qui a conduit à l’introduction du type [IXmlEncryptor](#ixmlencryptor) en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="5f7af-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="5f7af-204">Toutefois, des accidents se produisent, et des anneaux de clé peut être supprimés ou endommagés.</span><span class="sxs-lookup"><span data-stu-id="5f7af-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="5f7af-205">L’interface du tiers de confiance fournit un hachage d’échappement d’urgence, permettant d’accéder au code XML sérialisé brut avant qu’il ne soit transformé par un [IXmlEncryptor](#ixmlencryptor)configuré.</span><span class="sxs-lookup"><span data-stu-id="5f7af-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="5f7af-206">L’interface expose une API unique :</span><span class="sxs-lookup"><span data-stu-id="5f7af-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="5f7af-207">Store (Guid keyId, élément de XElement)</span><span class="sxs-lookup"><span data-stu-id="5f7af-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="5f7af-208">Il s’agit de l’implémentation `IKeyEscrowSink` pour gérer l’élément fourni de manière sécurisée et cohérente avec la stratégie d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="5f7af-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="5f7af-209">Une implémentation possible peut être que le récepteur de tiers de confiance chiffre l’élément XML à l’aide d’un certificat X. 509 d’entreprise connu dans lequel la clé privée du certificat a été déposée. le type de `CertificateXmlEncryptor` peut vous aider.</span><span class="sxs-lookup"><span data-stu-id="5f7af-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="5f7af-210">L’implémentation de `IKeyEscrowSink` est également chargée de rendre l’élément fourni de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="5f7af-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="5f7af-211">Par défaut, aucun mécanisme tiers de confiance n’est activé, bien que les administrateurs de serveur puissent [le configurer globalement](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="5f7af-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="5f7af-212">Elle peut également être configurée par programme à l’aide de la méthode `IDataProtectionBuilder.AddKeyEscrowSink`, comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f7af-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="5f7af-213">Les surcharges de méthode `AddKeyEscrowSink` reflètent les surcharges de `IServiceCollection.AddSingleton` et de `IServiceCollection.AddInstance`, car les instances `IKeyEscrowSink` sont destinées à être des singletons.</span><span class="sxs-lookup"><span data-stu-id="5f7af-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="5f7af-214">Si plusieurs instances de `IKeyEscrowSink` sont inscrites, chacune sera appelée pendant la génération de la clé, de sorte que les clés peuvent être déposées simultanément sur plusieurs mécanismes.</span><span class="sxs-lookup"><span data-stu-id="5f7af-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="5f7af-215">Il n’existe aucune API pour lire des documents à partir d’une instance de `IKeyEscrowSink`.</span><span class="sxs-lookup"><span data-stu-id="5f7af-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="5f7af-216">Ceci est cohérent avec la théorie de conception du mécanisme de tiers de confiance : il est conçu pour améliorer le matériel de clé pour une autorité approuvée, et étant donné que l’application est lui-même pas une autorité approuvée, il ne doit pas avoir accès à son propre matériel mises en dépôt.</span><span class="sxs-lookup"><span data-stu-id="5f7af-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="5f7af-217">L’exemple de code suivant illustre la création et l’inscription d’un `IKeyEscrowSink` dans lequel les clés sont déposées de sorte que seuls les membres de « CONTOSODomain admins » puissent les récupérer.</span><span class="sxs-lookup"><span data-stu-id="5f7af-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="5f7af-218">Pour exécuter cet exemple, vous devez être sur un appareil joint au domaine Windows 8 / machine Windows Server 2012 et le contrôleur de domaine doivent être Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="5f7af-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
