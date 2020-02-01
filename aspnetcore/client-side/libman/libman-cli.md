---
title: Utiliser l’interface CLI LibMan avec ASP.NET Core
author: scottaddie
description: Découvrez comment utiliser l’interface CLI LibMan dans un projet ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928360"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="105c8-103">Utiliser l’interface CLI LibMan avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="105c8-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="105c8-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="105c8-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="105c8-105">L’interface CLI [LibMan](xref:client-side/libman/index) est un outil multiplateforme qui est pris en charge partout où .net Core est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="105c8-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="105c8-106">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="105c8-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="105c8-107">Installation de</span><span class="sxs-lookup"><span data-stu-id="105c8-107">Installation</span></span>

<span data-ttu-id="105c8-108">Pour installer l’interface CLI LibMan :</span><span class="sxs-lookup"><span data-stu-id="105c8-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="105c8-109">Un [outil Global .net Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="105c8-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="105c8-110">Pour installer l’interface CLI LibMan à partir d’une source de package NuGet spécifique :</span><span class="sxs-lookup"><span data-stu-id="105c8-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="105c8-111">Dans l’exemple précédent, un outil Global .NET Core est installé à partir du fichier *C:\Temp\Microsoft.Web.librarymanager.cli.1.0.94-g606058a278.nupkg* de l’ordinateur Windows local.</span><span class="sxs-lookup"><span data-stu-id="105c8-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="105c8-112">Contrôle</span><span class="sxs-lookup"><span data-stu-id="105c8-112">Usage</span></span>

<span data-ttu-id="105c8-113">Une fois l’installation de l’interface CLI réussie, vous pouvez utiliser la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="105c8-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="105c8-114">Pour afficher la version installée de l’interface de commande :</span><span class="sxs-lookup"><span data-stu-id="105c8-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="105c8-115">Pour afficher les commandes CLI disponibles :</span><span class="sxs-lookup"><span data-stu-id="105c8-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="105c8-116">La commande précédente affiche une sortie similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="105c8-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="105c8-117">Les sections suivantes décrivent les commandes CLI disponibles.</span><span class="sxs-lookup"><span data-stu-id="105c8-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="105c8-118">Initialiser LibMan dans le projet</span><span class="sxs-lookup"><span data-stu-id="105c8-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="105c8-119">La commande `libman init` crée un fichier *Libman. JSON* s’il n’en existe pas.</span><span class="sxs-lookup"><span data-stu-id="105c8-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="105c8-120">Le fichier est créé avec le contenu du modèle d’élément par défaut.</span><span class="sxs-lookup"><span data-stu-id="105c8-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-121">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="105c8-122">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-122">Options</span></span>

<span data-ttu-id="105c8-123">Les options suivantes sont disponibles pour la commande `libman init` :</span><span class="sxs-lookup"><span data-stu-id="105c8-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="105c8-124">Chemin d’accès relatif au dossier actif.</span><span class="sxs-lookup"><span data-stu-id="105c8-124">A path relative to the current folder.</span></span> <span data-ttu-id="105c8-125">Les fichiers de bibliothèque sont installés à cet emplacement si aucune propriété `destination` n’est définie pour une bibliothèque dans *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="105c8-126">La valeur `<PATH>` est écrite dans la propriété `defaultDestination` de *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="105c8-127">Fournisseur à utiliser si aucun fournisseur n’est défini pour une bibliothèque donnée.</span><span class="sxs-lookup"><span data-stu-id="105c8-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="105c8-128">La valeur `<PROVIDER>` est écrite dans la propriété `defaultProvider` de *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="105c8-129">Remplacez `<PROVIDER>` par l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="105c8-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-130">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-130">Examples</span></span>

<span data-ttu-id="105c8-131">Pour créer un fichier *Libman. JSON* dans un projet ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="105c8-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="105c8-132">Accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="105c8-132">Navigate to the project root.</span></span>
* <span data-ttu-id="105c8-133">Exécutez la commande suivante : .</span><span class="sxs-lookup"><span data-stu-id="105c8-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="105c8-134">Tapez le nom du fournisseur par défaut ou appuyez sur `Enter` pour utiliser le fournisseur CDNJS par défaut.</span><span class="sxs-lookup"><span data-stu-id="105c8-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="105c8-135">Les valeurs valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="105c8-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![commande Libman init-fournisseur par défaut](_static/libman-init-provider.png)

<span data-ttu-id="105c8-137">Un fichier *Libman. JSON* est ajouté à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="105c8-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="105c8-138">Ajouter des fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="105c8-138">Add library files</span></span>

<span data-ttu-id="105c8-139">La commande `libman install` télécharge et installe les fichiers de bibliothèque dans le projet.</span><span class="sxs-lookup"><span data-stu-id="105c8-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="105c8-140">Un fichier *Libman. JSON* est ajouté s’il n’en existe pas.</span><span class="sxs-lookup"><span data-stu-id="105c8-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="105c8-141">Le fichier *Libman. JSON* est modifié pour stocker les détails de configuration des fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-142">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="105c8-143">Arguments</span><span class="sxs-lookup"><span data-stu-id="105c8-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="105c8-144">Nom de la bibliothèque à installer.</span><span class="sxs-lookup"><span data-stu-id="105c8-144">The name of the library to install.</span></span> <span data-ttu-id="105c8-145">Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="105c8-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="105c8-146">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-146">Options</span></span>

<span data-ttu-id="105c8-147">Les options suivantes sont disponibles pour la commande `libman install` :</span><span class="sxs-lookup"><span data-stu-id="105c8-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="105c8-148">Emplacement d’installation de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-148">The location to install the library.</span></span> <span data-ttu-id="105c8-149">S’il n’est pas spécifié, l’emplacement par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="105c8-149">If not specified, the default location is used.</span></span> <span data-ttu-id="105c8-150">Si aucune propriété `defaultDestination` n’est spécifiée dans *Libman. JSON*, cette option est requise.</span><span class="sxs-lookup"><span data-stu-id="105c8-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="105c8-151">Spécifiez le nom du fichier à installer à partir de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="105c8-152">S’il n’est pas spécifié, tous les fichiers de la bibliothèque sont installés.</span><span class="sxs-lookup"><span data-stu-id="105c8-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="105c8-153">Fournissez une option de `--files` par fichier à installer.</span><span class="sxs-lookup"><span data-stu-id="105c8-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="105c8-154">Les chemins d’accès relatifs sont également pris en charge.</span><span class="sxs-lookup"><span data-stu-id="105c8-154">Relative paths are supported too.</span></span> <span data-ttu-id="105c8-155">Par exemple : `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="105c8-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="105c8-156">Nom du fournisseur à utiliser pour l’acquisition de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="105c8-157">Remplacez `<PROVIDER>` par l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="105c8-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="105c8-158">S’il n’est pas spécifié, la propriété `defaultProvider` dans *Libman. JSON* est utilisée.</span><span class="sxs-lookup"><span data-stu-id="105c8-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="105c8-159">Si aucune propriété `defaultProvider` n’est spécifiée dans *Libman. JSON*, cette option est requise.</span><span class="sxs-lookup"><span data-stu-id="105c8-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-160">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-160">Examples</span></span>

<span data-ttu-id="105c8-161">Prenons le fichier *Libman. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="105c8-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="105c8-162">Pour installer le fichier jQuery version 3.2.1 *jQuery. min. js* dans le dossier *wwwroot/scripts/jQuery* à l’aide du fournisseur CDNJS :</span><span class="sxs-lookup"><span data-stu-id="105c8-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="105c8-163">Le fichier *Libman. JSON* ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="105c8-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="105c8-164">Pour installer les fichiers *Calendar. js* et *Calendar. CSS* à partir de *C :\\Temp\\contosoCalendar\\* à l’aide du fournisseur de système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="105c8-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="105c8-165">L’invite suivante s’affiche pour deux raisons :</span><span class="sxs-lookup"><span data-stu-id="105c8-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="105c8-166">Le fichier *Libman. JSON* ne contient pas de propriété `defaultDestination`.</span><span class="sxs-lookup"><span data-stu-id="105c8-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="105c8-167">La commande `libman install` ne contient pas l’option `-d|--destination`.</span><span class="sxs-lookup"><span data-stu-id="105c8-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![commande d’installation de Libman-destination](_static/libman-install-destination.png)

<span data-ttu-id="105c8-169">Après avoir accepté la destination par défaut, le fichier *Libman. JSON* ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="105c8-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="105c8-170">Restaurer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="105c8-170">Restore library files</span></span>

<span data-ttu-id="105c8-171">La commande `libman restore` installe les fichiers de bibliothèque définis dans *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="105c8-172">Les règles suivantes s'appliquent :</span><span class="sxs-lookup"><span data-stu-id="105c8-172">The following rules apply:</span></span>

* <span data-ttu-id="105c8-173">S’il n’existe aucun fichier *Libman. JSON* dans la racine du projet, une erreur est retournée.</span><span class="sxs-lookup"><span data-stu-id="105c8-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="105c8-174">Si une bibliothèque spécifie un fournisseur, la propriété `defaultProvider` dans *Libman. JSON* est ignorée.</span><span class="sxs-lookup"><span data-stu-id="105c8-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="105c8-175">Si une bibliothèque spécifie une destination, la propriété `defaultDestination` dans *Libman. JSON* est ignorée.</span><span class="sxs-lookup"><span data-stu-id="105c8-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-176">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="105c8-177">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-177">Options</span></span>

<span data-ttu-id="105c8-178">Les options suivantes sont disponibles pour la commande `libman restore` :</span><span class="sxs-lookup"><span data-stu-id="105c8-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-179">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-179">Examples</span></span>

<span data-ttu-id="105c8-180">Pour restaurer les fichiers de bibliothèque définis dans *Libman. JSON*:</span><span class="sxs-lookup"><span data-stu-id="105c8-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="105c8-181">Supprimer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="105c8-181">Delete library files</span></span>

<span data-ttu-id="105c8-182">La commande `libman clean` supprime les fichiers de bibliothèque précédemment restaurés via LibMan.</span><span class="sxs-lookup"><span data-stu-id="105c8-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="105c8-183">Dossiers qui deviennent vides après la suppression de cette opération.</span><span class="sxs-lookup"><span data-stu-id="105c8-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="105c8-184">Les configurations associées aux fichiers de bibliothèque dans la propriété `libraries` de *Libman. JSON* ne sont pas supprimées.</span><span class="sxs-lookup"><span data-stu-id="105c8-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-185">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="105c8-186">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-186">Options</span></span>

<span data-ttu-id="105c8-187">Les options suivantes sont disponibles pour la commande `libman clean` :</span><span class="sxs-lookup"><span data-stu-id="105c8-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-188">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-188">Examples</span></span>

<span data-ttu-id="105c8-189">Pour supprimer les fichiers de bibliothèque installés via LibMan :</span><span class="sxs-lookup"><span data-stu-id="105c8-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="105c8-190">Désinstaller les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="105c8-190">Uninstall library files</span></span>

<span data-ttu-id="105c8-191">Commande `libman uninstall` :</span><span class="sxs-lookup"><span data-stu-id="105c8-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="105c8-192">Supprime tous les fichiers associés à la bibliothèque spécifiée à partir de la destination dans *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="105c8-193">Supprime la configuration de bibliothèque associée de *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="105c8-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="105c8-194">Une erreur se produit dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="105c8-194">An error occurs when:</span></span>

* <span data-ttu-id="105c8-195">Il n’existe aucun fichier *Libman. JSON* dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="105c8-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="105c8-196">La bibliothèque spécifiée n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="105c8-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="105c8-197">Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.</span><span class="sxs-lookup"><span data-stu-id="105c8-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-198">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="105c8-199">Arguments</span><span class="sxs-lookup"><span data-stu-id="105c8-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="105c8-200">Nom de la bibliothèque à désinstaller.</span><span class="sxs-lookup"><span data-stu-id="105c8-200">The name of the library to uninstall.</span></span> <span data-ttu-id="105c8-201">Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="105c8-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="105c8-202">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-202">Options</span></span>

<span data-ttu-id="105c8-203">Les options suivantes sont disponibles pour la commande `libman uninstall` :</span><span class="sxs-lookup"><span data-stu-id="105c8-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-204">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-204">Examples</span></span>

<span data-ttu-id="105c8-205">Prenons le fichier *Libman. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="105c8-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="105c8-206">Pour désinstaller jQuery, l’une des commandes suivantes est réussie :</span><span class="sxs-lookup"><span data-stu-id="105c8-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="105c8-207">Pour désinstaller les fichiers Lodash installés via le fournisseur `filesystem` :</span><span class="sxs-lookup"><span data-stu-id="105c8-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="105c8-208">Version de la bibliothèque de mises à jour</span><span class="sxs-lookup"><span data-stu-id="105c8-208">Update library version</span></span>

<span data-ttu-id="105c8-209">La commande `libman update` met à jour une bibliothèque installée via LibMan vers la version spécifiée.</span><span class="sxs-lookup"><span data-stu-id="105c8-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="105c8-210">Une erreur se produit dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="105c8-210">An error occurs when:</span></span>

* <span data-ttu-id="105c8-211">Il n’existe aucun fichier *Libman. JSON* dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="105c8-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="105c8-212">La bibliothèque spécifiée n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="105c8-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="105c8-213">Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.</span><span class="sxs-lookup"><span data-stu-id="105c8-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-214">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="105c8-215">Arguments</span><span class="sxs-lookup"><span data-stu-id="105c8-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="105c8-216">Nom de la bibliothèque à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="105c8-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="105c8-217">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-217">Options</span></span>

<span data-ttu-id="105c8-218">Les options suivantes sont disponibles pour la commande `libman update` :</span><span class="sxs-lookup"><span data-stu-id="105c8-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="105c8-219">Obtenez la dernière version préliminaire de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="105c8-220">Obtenez une version spécifique de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-221">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-221">Examples</span></span>

* <span data-ttu-id="105c8-222">Pour mettre à jour jQuery vers la dernière version :</span><span class="sxs-lookup"><span data-stu-id="105c8-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="105c8-223">Pour mettre à jour jQuery vers la version 3.3.1 :</span><span class="sxs-lookup"><span data-stu-id="105c8-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="105c8-224">Pour mettre à jour jQuery vers la dernière version préliminaire :</span><span class="sxs-lookup"><span data-stu-id="105c8-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="105c8-225">Gérer le cache de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="105c8-225">Manage library cache</span></span>

<span data-ttu-id="105c8-226">La commande `libman cache` gère le cache de la bibliothèque LibMan.</span><span class="sxs-lookup"><span data-stu-id="105c8-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="105c8-227">Le fournisseur `filesystem` n’utilise pas le cache de bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="105c8-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="105c8-228">Résumé</span><span class="sxs-lookup"><span data-stu-id="105c8-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="105c8-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="105c8-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="105c8-230">Utilisé uniquement avec la commande `clean`.</span><span class="sxs-lookup"><span data-stu-id="105c8-230">Only used with the `clean` command.</span></span> <span data-ttu-id="105c8-231">Spécifie le cache du fournisseur à nettoyer.</span><span class="sxs-lookup"><span data-stu-id="105c8-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="105c8-232">Les valeurs valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="105c8-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="105c8-233">Options</span><span class="sxs-lookup"><span data-stu-id="105c8-233">Options</span></span>

<span data-ttu-id="105c8-234">Les options suivantes sont disponibles pour la commande `libman cache` :</span><span class="sxs-lookup"><span data-stu-id="105c8-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="105c8-235">Répertoriez les noms des fichiers qui sont mis en cache.</span><span class="sxs-lookup"><span data-stu-id="105c8-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="105c8-236">Répertoriez les noms des bibliothèques mises en cache.</span><span class="sxs-lookup"><span data-stu-id="105c8-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="105c8-237">Exemples</span><span class="sxs-lookup"><span data-stu-id="105c8-237">Examples</span></span>

* <span data-ttu-id="105c8-238">Pour afficher les noms des bibliothèques mises en cache par fournisseur, utilisez l’une des commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="105c8-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="105c8-239">Une sortie similaire à la suivante s’affiche à l’écran :</span><span class="sxs-lookup"><span data-stu-id="105c8-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="105c8-240">Pour afficher les noms des fichiers de bibliothèque mis en cache par fournisseur :</span><span class="sxs-lookup"><span data-stu-id="105c8-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="105c8-241">Une sortie similaire à la suivante s’affiche à l’écran :</span><span class="sxs-lookup"><span data-stu-id="105c8-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="105c8-242">Notez que la sortie précédente montre que les versions 3.2.1 et 3.3.1 de jQuery sont mises en cache sous le fournisseur CDNJS.</span><span class="sxs-lookup"><span data-stu-id="105c8-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="105c8-243">Pour vider le cache de bibliothèque pour le fournisseur CDNJS :</span><span class="sxs-lookup"><span data-stu-id="105c8-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="105c8-244">Une fois le cache du fournisseur CDNJS vidé, la commande `libman cache list` affiche ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="105c8-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="105c8-245">Pour vider le cache pour tous les fournisseurs pris en charge :</span><span class="sxs-lookup"><span data-stu-id="105c8-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="105c8-246">Une fois tous les caches de fournisseur vidés, la commande `libman cache list` affiche ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="105c8-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="105c8-247">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="105c8-247">Additional resources</span></span>

* [<span data-ttu-id="105c8-248">Installer un outil Global</span><span class="sxs-lookup"><span data-stu-id="105c8-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="105c8-249">Dépôt GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="105c8-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
