---
title: Créer une Blazor application de liste de tâches
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176226"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="34946-103">Créer une Blazor application de liste de tâches</span><span class="sxs-lookup"><span data-stu-id="34946-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="34946-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="34946-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="34946-105">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="34946-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="34946-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="34946-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34946-107">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="34946-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="34946-108">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="34946-108">Modify Razor components</span></span>
> * <span data-ttu-id="34946-109">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="34946-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="34946-110">Utiliser l’injection de dépendances et le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="34946-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="34946-111">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="34946-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="34946-112">Créez une Blazor application nommée `TodoList` dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="34946-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="34946-113">La commande précédente crée un dossier nommé `TodoList` pour contenir l’application.</span><span class="sxs-lookup"><span data-stu-id="34946-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="34946-114">Accédez au dossier à l' `TodoList` aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="34946-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="34946-115">Ajoutez un nouveau `Todo` Razor composant à l’application dans le `Pages` dossier à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="34946-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="34946-116">les noms de fichiers de composant requièrent une première lettre majuscule, donc confirmez que le `Todo` nom de fichier du composant commence par une lettre majuscule `T` .</span><span class="sxs-lookup"><span data-stu-id="34946-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="34946-117">Dans `Pages/Todo.razor` fournir le balisage initial pour le composant :</span><span class="sxs-lookup"><span data-stu-id="34946-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="34946-118">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="34946-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="34946-119">Le `NavMenu` composant ( `Shared/NavMenu.razor` ) est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="34946-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="34946-120">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans l’application.</span><span class="sxs-lookup"><span data-stu-id="34946-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="34946-121">Ajoutez un `<NavLink>` élément pour le `Todo` composant en ajoutant la balise d’élément de liste suivante sous les éléments de liste existants dans le `Shared/NavMenu.razor` fichier :</span><span class="sxs-lookup"><span data-stu-id="34946-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="34946-122">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="34946-122">Rebuild and run the app.</span></span> <span data-ttu-id="34946-123">Consultez la nouvelle page Todo pour vérifier que le lien vers le composant `Todo` fonctionne.</span><span class="sxs-lookup"><span data-stu-id="34946-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="34946-124">Ajoutez un `TodoItem.cs` fichier à la racine du projet pour contenir une classe qui représente un élément TODO.</span><span class="sxs-lookup"><span data-stu-id="34946-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="34946-125">Utilisez le code C# suivant pour la classe `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="34946-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="34946-126">Revenez au `Todo` composant ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="34946-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="34946-127">Ajoutez un champ pour les éléments Todo dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="34946-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="34946-128">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="34946-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="34946-129">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="34946-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="34946-130">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="34946-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="34946-131">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="34946-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="34946-132">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="34946-132">Rebuild and run the app.</span></span> <span data-ttu-id="34946-133">Lorsque le **`Add todo`** bouton est sélectionné, rien ne se produit, car un gestionnaire d’événements n’est pas relié au bouton.</span><span class="sxs-lookup"><span data-stu-id="34946-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="34946-134">Ajoutez une méthode `AddTodo` au composant `Todo` et enregistrez-la pour les sélections du bouton à l’aide de l’attribut `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="34946-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="34946-135">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="34946-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="34946-136">Pour obtenir le titre du nouvel élément todo, ajoutez un champ de chaîne `newTodo` en haut du bloc `@code` et liez-le à la valeur du texte d’entrée à l’aide de l’attribut `bind` dans l’élément `<input>` :</span><span class="sxs-lookup"><span data-stu-id="34946-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="34946-137">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="34946-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="34946-138">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="34946-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="34946-139">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="34946-139">Rebuild and run the app.</span></span> <span data-ttu-id="34946-140">Ajoutez quelques éléments todo à la liste Todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="34946-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="34946-141">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="34946-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="34946-142">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="34946-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="34946-143">Remplacez `@todo.Title` par un élément `<input>` lié à `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="34946-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="34946-144">Pour vérifier que ces valeurs sont liées, mettez à jour l’en-tête `<h3>` pour afficher le nombre d’éléments todo qui ne sont pas terminés (`IsDone` est `false`).</span><span class="sxs-lookup"><span data-stu-id="34946-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="34946-145">Le `Todo` composant terminé ( `Pages/Todo.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="34946-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="34946-146">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="34946-146">Rebuild and run the app.</span></span> <span data-ttu-id="34946-147">Ajoutez des éléments todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="34946-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="34946-148">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="34946-148">Next steps</span></span>

<span data-ttu-id="34946-149">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="34946-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34946-150">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="34946-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="34946-151">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="34946-151">Modify Razor components</span></span>
> * <span data-ttu-id="34946-152">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="34946-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="34946-153">Utiliser l’injection de dépendances et le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="34946-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="34946-154">En savoir plus sur les outils pour ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="34946-154">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>