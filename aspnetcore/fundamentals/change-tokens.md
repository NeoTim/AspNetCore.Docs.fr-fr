---
title: Détecter les modifications avec des jetons de modification dans ASP.NET Core
author: guardrex
description: Découvrez comment utiliser des jetons de modification pour effectuer le suivi des modifications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/03/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 8b73b72d093b33edeb91bc78080e05aa312579ec
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561659"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="ffcec-103">Détecter les modifications avec des jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffcec-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="ffcec-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ffcec-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ffcec-105">Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.</span><span class="sxs-lookup"><span data-stu-id="ffcec-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="ffcec-106">[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ffcec-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="ffcec-107">Interface d’IChangeToken</span><span class="sxs-lookup"><span data-stu-id="ffcec-107">IChangeToken interface</span></span>

<span data-ttu-id="ffcec-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="ffcec-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="ffcec-109">`IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ffcec-110">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="ffcec-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="ffcec-111">`IChangeToken` a deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="ffcec-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="ffcec-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive.</span><span class="sxs-lookup"><span data-stu-id="ffcec-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="ffcec-113">Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications.</span><span class="sxs-lookup"><span data-stu-id="ffcec-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="ffcec-114">Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.</span><span class="sxs-lookup"><span data-stu-id="ffcec-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="ffcec-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="ffcec-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="ffcec-116">L’interface `IChangeToken` inclut la méthode [RegisterChangeCallback(Action\<Objet>, Objet)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), qui inscrit un rappel qui est appelé quand le jeton a été modifié.</span><span class="sxs-lookup"><span data-stu-id="ffcec-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="ffcec-117">`HasChanged` doit être défini avant que le rappel soit appelé.</span><span class="sxs-lookup"><span data-stu-id="ffcec-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="ffcec-118">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="ffcec-118">ChangeToken class</span></span>

<span data-ttu-id="ffcec-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="ffcec-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="ffcec-120">`ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ffcec-121">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="ffcec-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="ffcec-122">La méthode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit une `Action` à appeler chaque fois que le jeton change :</span><span class="sxs-lookup"><span data-stu-id="ffcec-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="ffcec-123">`Func<IChangeToken>` produit le jeton.</span><span class="sxs-lookup"><span data-stu-id="ffcec-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="ffcec-124">`Action` est appelée quand le jeton change.</span><span class="sxs-lookup"><span data-stu-id="ffcec-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="ffcec-125">La surcharge [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre `TState` supplémentaire qui est passé dans le consommateur du jeton `Action`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="ffcec-126">`OnChange`Retourne un <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="ffcec-127">L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.</span><span class="sxs-lookup"><span data-stu-id="ffcec-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="ffcec-128">Exemples d’utilisation de jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffcec-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="ffcec-129">Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :</span><span class="sxs-lookup"><span data-stu-id="ffcec-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="ffcec-130">Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.</span><span class="sxs-lookup"><span data-stu-id="ffcec-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="ffcec-131">Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="ffcec-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="ffcec-132">Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="ffcec-133">Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.</span><span class="sxs-lookup"><span data-stu-id="ffcec-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="ffcec-134">Surveiller les modifications de configuration</span><span class="sxs-lookup"><span data-stu-id="ffcec-134">Monitor for configuration changes</span></span>

<span data-ttu-id="ffcec-135">Par défaut, les modèles ASP.NET Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* et *appsettings.Production.json*) pour charger les paramètres de configuration des applications.</span><span class="sxs-lookup"><span data-stu-id="ffcec-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="ffcec-136">Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="ffcec-137">`reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="ffcec-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="ffcec-138">Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.AspNetCore.WebHost> <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="ffcec-138">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="ffcec-139">La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="ffcec-140">`FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.</span><span class="sxs-lookup"><span data-stu-id="ffcec-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="ffcec-141">Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="ffcec-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="ffcec-142">L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration.</span><span class="sxs-lookup"><span data-stu-id="ffcec-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="ffcec-143">Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.</span><span class="sxs-lookup"><span data-stu-id="ffcec-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="ffcec-144">Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="ffcec-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="ffcec-145">Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers.</span><span class="sxs-lookup"><span data-stu-id="ffcec-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="ffcec-146">L’exemple utilise le hachage de fichier SHA1.</span><span class="sxs-lookup"><span data-stu-id="ffcec-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="ffcec-147">Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle.</span><span class="sxs-lookup"><span data-stu-id="ffcec-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="ffcec-148">Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="ffcec-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="ffcec-149">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ffcec-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="ffcec-150">Jeton de modification de démarrage simple</span><span class="sxs-lookup"><span data-stu-id="ffcec-150">Simple startup change token</span></span>

<span data-ttu-id="ffcec-151">Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.</span><span class="sxs-lookup"><span data-stu-id="ffcec-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="ffcec-152">Dans `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ffcec-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ffcec-153">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="ffcec-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="ffcec-154">Le rappel est la méthode `InvokeChanged` :</span><span class="sxs-lookup"><span data-stu-id="ffcec-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ffcec-155">Le `state` du rappel est utilisé pour transmettre le `IHostingEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement).</span><span class="sxs-lookup"><span data-stu-id="ffcec-155">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="ffcec-156">Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="ffcec-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="ffcec-157">Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ffcec-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="ffcec-158">Surveiller les modifications de configuration en tant que service</span><span class="sxs-lookup"><span data-stu-id="ffcec-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="ffcec-159">L’exemple implémente :</span><span class="sxs-lookup"><span data-stu-id="ffcec-159">The sample implements:</span></span>

* <span data-ttu-id="ffcec-160">Surveillance de jeton du démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="ffcec-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="ffcec-161">Surveillance en tant que service.</span><span class="sxs-lookup"><span data-stu-id="ffcec-161">Monitoring as a service.</span></span>
* <span data-ttu-id="ffcec-162">Un mécanisme pour activer et désactiver la surveillance.</span><span class="sxs-lookup"><span data-stu-id="ffcec-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="ffcec-163">L’exemple établit une interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="ffcec-164">*Extensions/Configurationmonitor.cs* :</span><span class="sxs-lookup"><span data-stu-id="ffcec-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="ffcec-165">Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :</span><span class="sxs-lookup"><span data-stu-id="ffcec-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="ffcec-166">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="ffcec-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="ffcec-167">`InvokeChanged` est la méthode de rappel.</span><span class="sxs-lookup"><span data-stu-id="ffcec-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="ffcec-168">Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring.</span><span class="sxs-lookup"><span data-stu-id="ffcec-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="ffcec-169">Deux propriétés sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="ffcec-169">Two properties are used:</span></span>

* <span data-ttu-id="ffcec-170">`MonitoringEnabled` &ndash; Indique si le rappel doit exécuter son code personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ffcec-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="ffcec-171">`CurrentState` &ndash; Décrit l’état actuel de la surveillance pour une utilisation dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ffcec-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="ffcec-172">La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :</span><span class="sxs-lookup"><span data-stu-id="ffcec-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="ffcec-173">Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="ffcec-174">Elle indique le `state` actuel dans sa sortie `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="ffcec-175">Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ffcec-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ffcec-176">La page Index permet à l’utilisateur de contrôler la surveillance de la configuration.</span><span class="sxs-lookup"><span data-stu-id="ffcec-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="ffcec-177">L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="ffcec-178">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ffcec-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="ffcec-179">Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="ffcec-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="ffcec-180">Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé.</span><span class="sxs-lookup"><span data-stu-id="ffcec-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="ffcec-181">Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="ffcec-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="ffcec-182">Des boutons dans l’IU activent et désactivent la surveillance.</span><span class="sxs-lookup"><span data-stu-id="ffcec-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="ffcec-183">*Pages/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ffcec-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="ffcec-184">Surveiller les modifications de fichiers mis en cache</span><span class="sxs-lookup"><span data-stu-id="ffcec-184">Monitor cached file changes</span></span>

<span data-ttu-id="ffcec-185">Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="ffcec-186">La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="ffcec-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="ffcec-187">Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.</span><span class="sxs-lookup"><span data-stu-id="ffcec-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="ffcec-188">Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache.</span><span class="sxs-lookup"><span data-stu-id="ffcec-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="ffcec-189">Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache.</span><span class="sxs-lookup"><span data-stu-id="ffcec-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="ffcec-190">Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.</span><span class="sxs-lookup"><span data-stu-id="ffcec-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="ffcec-191">L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache.</span><span class="sxs-lookup"><span data-stu-id="ffcec-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="ffcec-192">L’exemple d’application montre une implémentation de l’approche.</span><span class="sxs-lookup"><span data-stu-id="ffcec-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="ffcec-193">L’exemple utilise `GetFileContent` pour :</span><span class="sxs-lookup"><span data-stu-id="ffcec-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="ffcec-194">Retourner le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="ffcec-194">Return file content.</span></span>
* <span data-ttu-id="ffcec-195">Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="ffcec-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="ffcec-196">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ffcec-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="ffcec-197">Un `FileService` est créé pour gérer les recherches des fichiers mis en cache.</span><span class="sxs-lookup"><span data-stu-id="ffcec-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="ffcec-198">L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="ffcec-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="ffcec-199">Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="ffcec-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="ffcec-200">Le contenu du fichier est obtenu à l’aide de `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="ffcec-201">Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="ffcec-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="ffcec-202">Le rappel du jeton est déclenché quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="ffcec-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="ffcec-203">Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="ffcec-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="ffcec-204">Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="ffcec-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="ffcec-205">Dans l’exemple suivant, des fichiers sont stockés à la racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="ffcec-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="ffcec-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointant vers <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de l’application.</span><span class="sxs-lookup"><span data-stu-id="ffcec-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="ffcec-207">`filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="ffcec-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="ffcec-208">`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ffcec-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="ffcec-209">Dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ffcec-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ffcec-210">Le modèle de page charge le contenu du fichier en utilisant le service.</span><span class="sxs-lookup"><span data-stu-id="ffcec-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="ffcec-211">Dans la méthode `OnGet` de la page Index (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ffcec-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="ffcec-212">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="ffcec-212">CompositeChangeToken class</span></span>

<span data-ttu-id="ffcec-213">Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="ffcec-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="ffcec-214">`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="ffcec-215">`ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="ffcec-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="ffcec-216">Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.</span><span class="sxs-lookup"><span data-stu-id="ffcec-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffcec-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ffcec-217">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
