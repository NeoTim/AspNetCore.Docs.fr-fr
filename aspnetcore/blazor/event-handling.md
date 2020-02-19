---
title: Gestion des événements ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les scénarios de gestion des événements de Blazor, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453239"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="53cd5-103">ASP.NET Core la gestion des événements éblouissants</span><span class="sxs-lookup"><span data-stu-id="53cd5-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="53cd5-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="53cd5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="53cd5-105">Les composants Razor fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="53cd5-105">Razor components provide event handling features.</span></span> <span data-ttu-id="53cd5-106">Pour un attribut d’élément HTML nommé `on{EVENT}` (par exemple, `onclick` et `onsubmit`) avec une valeur de type délégué, les composants Razor traitent la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="53cd5-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="53cd5-107">Le nom de l’attribut est toujours mis en forme [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="53cd5-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="53cd5-108">Le code suivant appelle la méthode `UpdateHeading` lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="53cd5-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="53cd5-109">Le code suivant appelle la méthode `CheckChanged` lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="53cd5-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="53cd5-110">Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="53cd5-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="53cd5-111">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="53cd5-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="53cd5-112">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="53cd5-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="53cd5-113">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="53cd5-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="53cd5-114">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="53cd5-114">Event argument types</span></span>

<span data-ttu-id="53cd5-115">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="53cd5-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="53cd5-116">La spécification d’un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="53cd5-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="53cd5-117">Les `EventArgs` prises en charge sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="53cd5-118">Événement</span><span class="sxs-lookup"><span data-stu-id="53cd5-118">Event</span></span>            | <span data-ttu-id="53cd5-119">Classe</span><span class="sxs-lookup"><span data-stu-id="53cd5-119">Class</span></span>                | <span data-ttu-id="53cd5-120">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="53cd5-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="53cd5-121">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="53cd5-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="53cd5-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="53cd5-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="53cd5-123">Déplacez</span><span class="sxs-lookup"><span data-stu-id="53cd5-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="53cd5-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="53cd5-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="53cd5-125">`DataTransfer` et `DataTransferItem` contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="53cd5-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="53cd5-126">Error</span><span class="sxs-lookup"><span data-stu-id="53cd5-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="53cd5-127">Événement</span><span class="sxs-lookup"><span data-stu-id="53cd5-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="53cd5-128">*Généralités*</span><span class="sxs-lookup"><span data-stu-id="53cd5-128">*General*</span></span><br><span data-ttu-id="53cd5-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="53cd5-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="53cd5-130">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="53cd5-130">*Clipboard*</span></span><br><span data-ttu-id="53cd5-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="53cd5-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="53cd5-132">*Input*</span><span class="sxs-lookup"><span data-stu-id="53cd5-132">*Input*</span></span><br><span data-ttu-id="53cd5-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="53cd5-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="53cd5-134">*Média*</span><span class="sxs-lookup"><span data-stu-id="53cd5-134">*Media*</span></span><br><span data-ttu-id="53cd5-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="53cd5-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="53cd5-136">Focus</span><span class="sxs-lookup"><span data-stu-id="53cd5-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="53cd5-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="53cd5-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="53cd5-138">N’inclut pas la prise en charge de `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="53cd5-139">Entrée</span><span class="sxs-lookup"><span data-stu-id="53cd5-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="53cd5-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="53cd5-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="53cd5-141">Clavier</span><span class="sxs-lookup"><span data-stu-id="53cd5-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="53cd5-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="53cd5-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="53cd5-143">Souris</span><span class="sxs-lookup"><span data-stu-id="53cd5-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="53cd5-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="53cd5-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="53cd5-145">Pointeur de la souris</span><span class="sxs-lookup"><span data-stu-id="53cd5-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="53cd5-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="53cd5-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="53cd5-147">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="53cd5-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="53cd5-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="53cd5-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="53cd5-149">Progress</span><span class="sxs-lookup"><span data-stu-id="53cd5-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="53cd5-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="53cd5-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="53cd5-151">Entrées tactiles</span><span class="sxs-lookup"><span data-stu-id="53cd5-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="53cd5-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="53cd5-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="53cd5-153">`TouchPoint` représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="53cd5-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="53cd5-154">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="53cd5-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="53cd5-155">[Classes EventArgs dans la source de référence ASP.net Core (branche dotnet/aspnetcore ou version 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="53cd5-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="53cd5-156">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="53cd5-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="53cd5-157">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="53cd5-157">Lambda expressions</span></span>

<span data-ttu-id="53cd5-158">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="53cd5-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="53cd5-159">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="53cd5-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="53cd5-160">L’exemple suivant crée trois boutons, chacun d’entre eux appelant `UpdateHeading` passant un argument d’événement (`MouseEventArgs`) et son numéro de bouton (`buttonNumber`) lorsqu’il est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="53cd5-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="53cd5-161">N’utilisez **pas** la variable de boucle (`i`) dans une boucle `for` directement dans une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="53cd5-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="53cd5-162">Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda provoquant la même valeur de `i`dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="53cd5-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="53cd5-163">Capturez toujours sa valeur dans une variable locale (`buttonNumber` dans l’exemple précédent), puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="53cd5-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="53cd5-164">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="53cd5-164">EventCallback</span></span>

<span data-ttu-id="53cd5-165">Un scénario courant avec des composants imbriqués est le souhait d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit&mdash;par exemple, lorsqu’un événement `onclick` se produit dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="53cd5-166">Pour exposer des événements entre les composants, utilisez un `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="53cd5-167">Un composant parent peut affecter une méthode de rappel à l' `EventCallback`d’un composant enfant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="53cd5-168">La `ChildComponent` dans l’exemple d’application (*Components/ChildComponent. Razor*) montre comment le gestionnaire de `onclick` d’un bouton est configuré pour recevoir un délégué `EventCallback` de l' `ParentComponent`de l’exemple.</span><span class="sxs-lookup"><span data-stu-id="53cd5-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="53cd5-169">Le `EventCallback` est tapé avec `MouseEventArgs`, ce qui est approprié pour un événement `onclick` à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="53cd5-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="53cd5-170">L' `ParentComponent` affecte à la `EventCallback<T>` (`OnClickCallback`) de l’enfant sa méthode `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="53cd5-171">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="53cd5-171">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="53cd5-172">Lorsque le bouton est sélectionné dans la `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="53cd5-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="53cd5-173">La méthode `ShowMessage` de `ParentComponent`est appelée.</span><span class="sxs-lookup"><span data-stu-id="53cd5-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="53cd5-174">`_messageText` est mis à jour et affiché dans le `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="53cd5-175">Un appel à [StateHasChanged](xref:blazor/lifecycle#state-changes) n’est pas requis dans la méthode du rappel (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="53cd5-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="53cd5-176">`StateHasChanged` est appelé automatiquement pour rerestituer le `ParentComponent`, tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="53cd5-177">`EventCallback` et `EventCallback<T>` autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="53cd5-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="53cd5-178">`EventCallback<T>` est fortement typé et requiert un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="53cd5-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="53cd5-179">`EventCallback` est faiblement typé et autorise tout type d’argument.</span><span class="sxs-lookup"><span data-stu-id="53cd5-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="53cd5-180">Appelez une `EventCallback` ou `EventCallback<T>` avec `InvokeAsync` et attendez la <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="53cd5-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="53cd5-181">Utilisez `EventCallback` et `EventCallback<T>` pour la gestion des événements et la liaison des paramètres du composant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="53cd5-182">Préférez la `EventCallback<T>` fortement typée sur `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="53cd5-183">`EventCallback<T>` offre un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="53cd5-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="53cd5-184">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="53cd5-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="53cd5-185">Utilisez `EventCallback` quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="53cd5-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="53cd5-186">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="53cd5-186">Prevent default actions</span></span>

<span data-ttu-id="53cd5-187">Utilisez l’attribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive pour empêcher l’action par défaut pour un événement.</span><span class="sxs-lookup"><span data-stu-id="53cd5-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="53cd5-188">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="53cd5-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="53cd5-189">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l’attribut de directive `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="53cd5-190">Le compteur est incrémenté, et la clé de **+** n’est pas capturée dans la valeur de l’élément `<input>` :</span><span class="sxs-lookup"><span data-stu-id="53cd5-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="53cd5-191">La spécification de l’attribut `@on{EVENT}:preventDefault` sans valeur est équivalente à `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="53cd5-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="53cd5-192">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="53cd5-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="53cd5-193">Dans l’exemple suivant, `_shouldPreventDefault` est un champ `bool` défini sur `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="53cd5-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="53cd5-194">Un gestionnaire d’événements n’est pas nécessaire pour empêcher l’action par défaut.</span><span class="sxs-lookup"><span data-stu-id="53cd5-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="53cd5-195">Le gestionnaire d’événements et les scénarios d’action par défaut peuvent être utilisés indépendamment.</span><span class="sxs-lookup"><span data-stu-id="53cd5-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="53cd5-196">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="53cd5-196">Stop event propagation</span></span>

<span data-ttu-id="53cd5-197">Utilisez l’attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="53cd5-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="53cd5-198">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième `<div>` enfant de se propager vers le `<div>`parent :</span><span class="sxs-lookup"><span data-stu-id="53cd5-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
