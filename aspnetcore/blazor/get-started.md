---
title: Prise en main d’ASP.NET Core éblouissante
author: guardrex
description: Commencez avec éblouissant en créant une application éblouissant avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/get-started
ms.openlocfilehash: 1358a2e92af9d9104e565718692b1ca1940b9d9e
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993403"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="d6784-103">Prise en main d’ASP.NET Core éblouissante</span><span class="sxs-lookup"><span data-stu-id="d6784-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="d6784-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6784-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6784-105">Prise en main de éblouissant:</span><span class="sxs-lookup"><span data-stu-id="d6784-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="d6784-106">Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,0 Preview](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="d6784-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="d6784-107">Installez les modèles éblouissants en exécutant la commande suivante dans une interface de commande:</span><span class="sxs-lookup"><span data-stu-id="d6784-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview8.19405.7
   ```

1. <span data-ttu-id="d6784-108">Suivez les instructions de votre choix d’outils:</span><span class="sxs-lookup"><span data-stu-id="d6784-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d6784-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6784-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="d6784-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-110">1\.</span></span> <span data-ttu-id="d6784-111">Installez la dernière version [préliminaire de Visual Studio](https://visualstudio.com/vs/preview) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="d6784-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="d6784-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-112">2\.</span></span> <span data-ttu-id="d6784-113">Créer un nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="d6784-113">Create a new project.</span></span>

   <span data-ttu-id="d6784-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-114">3\.</span></span> <span data-ttu-id="d6784-115">Sélectionnez l' **application éblouissant**.</span><span class="sxs-lookup"><span data-stu-id="d6784-115">Select **Blazor App**.</span></span> <span data-ttu-id="d6784-116">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="d6784-116">Select **Next**.</span></span>

   <span data-ttu-id="d6784-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-117">4\.</span></span> <span data-ttu-id="d6784-118">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6784-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d6784-119">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="d6784-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d6784-120">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d6784-120">Select **Create**.</span></span>

   <span data-ttu-id="d6784-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-121">5\.</span></span> <span data-ttu-id="d6784-122">Pour une expérience éblouissante côté client, choisissez le modèle **application éblouissant** webassembly.</span><span class="sxs-lookup"><span data-stu-id="d6784-122">For a Blazor client-side experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d6784-123">Pour une expérience côté serveur éblouissant, choisissez le modèle **application de serveur éblouissant** .</span><span class="sxs-lookup"><span data-stu-id="d6784-123">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d6784-124">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d6784-124">Select **Create**.</span></span> <span data-ttu-id="d6784-125">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, côté serveur et côté client, <xref:blazor/hosting-models>consultez.</span><span class="sxs-lookup"><span data-stu-id="d6784-125">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d6784-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-126">6\.</span></span> <span data-ttu-id="d6784-127">Appuyez sur **F5** pour exécuter l'application.</span><span class="sxs-lookup"><span data-stu-id="d6784-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="d6784-128">Si vous avez installé l’extension Visual Studio éblouissant pour une version préliminaire antérieure de ASP.NET Core éblouissant (version préliminaire 6 ou antérieure), vous pouvez désinstaller l’extension.</span><span class="sxs-lookup"><span data-stu-id="d6784-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="d6784-129">L’installation des modèles éblouissants dans un interpréteur de commandes est désormais suffisante pour faire apparaître les modèles dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d6784-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d6784-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6784-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="d6784-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-131">1\.</span></span> <span data-ttu-id="d6784-132">Installez [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="d6784-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="d6784-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-133">2\.</span></span> <span data-ttu-id="d6784-134">Installez le dernier [ C# Visual Studio code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="d6784-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="d6784-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-135">3\.</span></span> <span data-ttu-id="d6784-136">Pour une expérience éblouissante côté client, exécutez la commande suivante dans une interface de commande:</span><span class="sxs-lookup"><span data-stu-id="d6784-136">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="d6784-137">Pour une expérience côté serveur éblouissant, exécutez la commande suivante dans une interface de commande:</span><span class="sxs-lookup"><span data-stu-id="d6784-137">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="d6784-138">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, côté serveur et côté client, <xref:blazor/hosting-models>consultez.</span><span class="sxs-lookup"><span data-stu-id="d6784-138">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d6784-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-139">4\.</span></span> <span data-ttu-id="d6784-140">Ouvrez le dossier *WebApplication1* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="d6784-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="d6784-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-141">5\.</span></span> <span data-ttu-id="d6784-142">Pour un projet côté serveur éblouissant, l’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="d6784-142">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d6784-143">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="d6784-143">Select **Yes**.</span></span>

   <span data-ttu-id="d6784-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-144">6\.</span></span> <span data-ttu-id="d6784-145">Si vous utilisez une application côté serveur éblouissante, exécutez l’application à l’aide du débogueur Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d6784-145">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="d6784-146">Si vous utilisez une application cliente éblouissant, exécutez `dotnet run` à partir du dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6784-146">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="d6784-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="d6784-147">7\.</span></span> <span data-ttu-id="d6784-148">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d6784-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **Blazor Server App** template. For a Blazor client-side experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d6784-149">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6784-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="d6784-150">Pour une expérience éblouissante côté client, exécutez les commandes suivantes dans un interpréteur de commandes:</span><span class="sxs-lookup"><span data-stu-id="d6784-150">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d6784-151">Pour une expérience côté serveur éblouissant, exécutez les commandes suivantes dans une interface de commande:</span><span class="sxs-lookup"><span data-stu-id="d6784-151">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d6784-152">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, côté serveur et côté client, <xref:blazor/hosting-models>consultez.</span><span class="sxs-lookup"><span data-stu-id="d6784-152">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d6784-153">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d6784-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="d6784-154">Plusieurs pages sont disponibles à partir des onglets de la barre latérale:</span><span class="sxs-lookup"><span data-stu-id="d6784-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="d6784-155">Dossier de base</span><span class="sxs-lookup"><span data-stu-id="d6784-155">Home</span></span>
* <span data-ttu-id="d6784-156">Compteur</span><span class="sxs-lookup"><span data-stu-id="d6784-156">Counter</span></span>
* <span data-ttu-id="d6784-157">Extraire les données</span><span class="sxs-lookup"><span data-stu-id="d6784-157">Fetch data</span></span>

<span data-ttu-id="d6784-158">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="d6784-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d6784-159">L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture de JavaScript, mais les composants C#Razor offrent une meilleure approche à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="d6784-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="d6784-160">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="d6784-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="d6784-161">Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte `Counter` que le composant restitue son contenu.</span><span class="sxs-lookup"><span data-stu-id="d6784-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="d6784-162">Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="d6784-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="d6784-163">Chaque fois que le bouton **Click Me** est sélectionné:</span><span class="sxs-lookup"><span data-stu-id="d6784-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="d6784-164">L' `onclick` événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d6784-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="d6784-165">La méthode `IncrementCount` est appelée.</span><span class="sxs-lookup"><span data-stu-id="d6784-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="d6784-166">`currentCount` Est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="d6784-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="d6784-167">Le composant est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="d6784-167">The component is rendered again.</span></span>

<span data-ttu-id="d6784-168">Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="d6784-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="d6784-169">Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="d6784-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="d6784-170">Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application `<Counter />` en ajoutant un `Index` élément au composant.</span><span class="sxs-lookup"><span data-stu-id="d6784-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="d6784-171">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="d6784-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="d6784-172">Exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d6784-172">Run the app.</span></span> <span data-ttu-id="d6784-173">La page d’accueil possède son propre compteur fourni `Counter` par le composant.</span><span class="sxs-lookup"><span data-stu-id="d6784-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="d6784-174">Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components#child-content), ce qui vous permet de définir des propriétés sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="d6784-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="d6784-175">Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc du `@code` composant:</span><span class="sxs-lookup"><span data-stu-id="d6784-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="d6784-176">Ajoutez une propriété publique pour `IncrementAmount` avec un `[Parameter]` attribut.</span><span class="sxs-lookup"><span data-stu-id="d6784-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="d6784-177">Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="d6784-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="d6784-178">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="d6784-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="d6784-179">Spécifiez `Index` `<Counter>` dans l’élément du composant à l’aide d’un attribut. `IncrementAmount`</span><span class="sxs-lookup"><span data-stu-id="d6784-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="d6784-180">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="d6784-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="d6784-181">Exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d6784-181">Run the app.</span></span> <span data-ttu-id="d6784-182">Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton **Click Me** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="d6784-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d6784-183">Le `Counter` composant (*Counter.* `/counter` Razor) de continue à être incrémenté d’une unité.</span><span class="sxs-lookup"><span data-stu-id="d6784-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d6784-184">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d6784-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="d6784-185">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d6784-185">Additional resources</span></span>

* <xref:signalr/introduction>