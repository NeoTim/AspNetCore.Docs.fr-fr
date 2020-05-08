---
title: Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment appeler des méthodes .NET à partir de fonctions Blazor JavaScript dans des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 9eab35f3fd856debf9f0305aedf012d63b9b800a
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967322"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="54c68-103">Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="54c68-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="54c68-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="54c68-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="54c68-105">Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54c68-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="54c68-106">Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="54c68-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="54c68-107">Cet article traite de l’appel de méthodes .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54c68-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="54c68-108">Pour plus d’informations sur l’appel de fonctions JavaScript à partir <xref:blazor/call-javascript-from-dotnet>de .net, consultez.</span><span class="sxs-lookup"><span data-stu-id="54c68-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="54c68-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54c68-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="54c68-110">Appel de méthode .NET statique</span><span class="sxs-lookup"><span data-stu-id="54c68-110">Static .NET method call</span></span>

<span data-ttu-id="54c68-111">Pour appeler une méthode .NET statique à partir de JavaScript, `DotNet.invokeMethod` utilisez `DotNet.invokeMethodAsync` les fonctions ou.</span><span class="sxs-lookup"><span data-stu-id="54c68-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="54c68-112">Transmettez l’identificateur de la méthode statique que vous souhaitez appeler, le nom de l’assembly contenant la fonction et les arguments éventuels.</span><span class="sxs-lookup"><span data-stu-id="54c68-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="54c68-113">La version asynchrone est recommandée pour prendre Blazor en charge les scénarios de serveur.</span><span class="sxs-lookup"><span data-stu-id="54c68-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="54c68-114">La méthode .NET doit être publique, statique et avoir l' `[JSInvokable]` attribut.</span><span class="sxs-lookup"><span data-stu-id="54c68-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="54c68-115">L’appel de méthodes génériques ouvertes n’est pas pris en charge actuellement.</span><span class="sxs-lookup"><span data-stu-id="54c68-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="54c68-116">L’exemple d’application comprend une méthode C# pour retourner `int` un tableau.</span><span class="sxs-lookup"><span data-stu-id="54c68-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="54c68-117">L' `JSInvokable` attribut est appliqué à la méthode.</span><span class="sxs-lookup"><span data-stu-id="54c68-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="54c68-118">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="54c68-118">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="54c68-119">JavaScript traité au client appelle la méthode .NET C#.</span><span class="sxs-lookup"><span data-stu-id="54c68-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="54c68-120">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="54c68-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="54c68-121">Quand le bouton déclencher l’ReturnArrayAsync de la **méthode statique .net** est sélectionné, examinez la sortie de la console dans les outils de développement Web du navigateur.</span><span class="sxs-lookup"><span data-stu-id="54c68-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="54c68-122">La sortie de la console est la suivante :</span><span class="sxs-lookup"><span data-stu-id="54c68-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="54c68-123">La quatrième valeur de tableau fait l’objet d’un`data.push(4);`push dans le `ReturnArrayAsync`tableau () retourné par.</span><span class="sxs-lookup"><span data-stu-id="54c68-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="54c68-124">Par défaut, l’identificateur de méthode est le nom de la méthode, mais vous pouvez spécifier un identificateur différent à `JSInvokableAttribute` l’aide du constructeur :</span><span class="sxs-lookup"><span data-stu-id="54c68-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="54c68-125">Dans le fichier JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="54c68-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="54c68-126">Appel de méthode d’instance</span><span class="sxs-lookup"><span data-stu-id="54c68-126">Instance method call</span></span>

<span data-ttu-id="54c68-127">Vous pouvez également appeler des méthodes d’instance .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54c68-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="54c68-128">Pour appeler une méthode d’instance .NET à partir de JavaScript :</span><span class="sxs-lookup"><span data-stu-id="54c68-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="54c68-129">Passer l’instance .NET par référence à JavaScript :</span><span class="sxs-lookup"><span data-stu-id="54c68-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="54c68-130">Effectuez un appel statique à `DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="54c68-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="54c68-131">Encapsulez l’instance `DotNetObjectReference` dans une instance `Create` et appelez `DotNetObjectReference` sur l’instance.</span><span class="sxs-lookup"><span data-stu-id="54c68-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="54c68-132">Supprimez `DotNetObjectReference` des objets (un exemple apparaît plus loin dans cette section).</span><span class="sxs-lookup"><span data-stu-id="54c68-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="54c68-133">Appeler des méthodes d’instance .NET sur l’instance `invokeMethod` à `invokeMethodAsync` l’aide des fonctions ou.</span><span class="sxs-lookup"><span data-stu-id="54c68-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="54c68-134">L’instance .NET peut également être passée comme argument lors de l’appel d’autres méthodes .NET à partir de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54c68-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="54c68-135">L’exemple d’application enregistre les messages dans la console côté client.</span><span class="sxs-lookup"><span data-stu-id="54c68-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="54c68-136">Pour les exemples suivants présentés dans l’exemple d’application, examinez la sortie de console du navigateur dans les outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="54c68-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="54c68-137">Lorsque le bouton de **méthode d’instance .net de déclenchement HelloHelper. SayHello** est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe `Blazor`un nom,, à la méthode.</span><span class="sxs-lookup"><span data-stu-id="54c68-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="54c68-138">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="54c68-138">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="54c68-139">`CallHelloHelperSayHello`appelle la fonction `sayHello` JavaScript avec une nouvelle instance de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="54c68-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="54c68-140">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="54c68-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="54c68-141">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="54c68-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="54c68-142">Le nom est passé au `HelloHelper`constructeur de, qui définit la `HelloHelper.Name` propriété.</span><span class="sxs-lookup"><span data-stu-id="54c68-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="54c68-143">Lorsque la fonction `sayHello` JavaScript est exécutée `HelloHelper.SayHello` , retourne `Hello, {Name}!` le message, qui est écrit dans la console par la fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="54c68-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="54c68-144">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="54c68-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="54c68-145">Sortie de la console dans les outils de développement Web du navigateur :</span><span class="sxs-lookup"><span data-stu-id="54c68-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="54c68-146">Pour éviter une fuite de mémoire et autoriser garbage collection sur un composant qui crée `DotNetObjectReference`un, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="54c68-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="54c68-147">Supprimez l’objet dans la classe qui a créé `DotNetObjectReference` l’instance :</span><span class="sxs-lookup"><span data-stu-id="54c68-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="54c68-148">Le modèle précédent présenté dans la `ExampleJsInterop` classe peut également être implémenté dans un composant :</span><span class="sxs-lookup"><span data-stu-id="54c68-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="54c68-149">Lorsque le composant ou la classe ne supprime pas `DotNetObjectReference`le, supprimez l’objet sur le client en `.dispose()`appelant :</span><span class="sxs-lookup"><span data-stu-id="54c68-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="54c68-150">Appel de méthode d’instance de composant</span><span class="sxs-lookup"><span data-stu-id="54c68-150">Component instance method call</span></span>

<span data-ttu-id="54c68-151">Pour appeler les méthodes .NET d’un composant :</span><span class="sxs-lookup"><span data-stu-id="54c68-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="54c68-152">Utilisez la `invokeMethod` fonction `invokeMethodAsync` ou pour effectuer un appel de méthode statique au composant.</span><span class="sxs-lookup"><span data-stu-id="54c68-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="54c68-153">La méthode statique du composant encapsule l’appel à sa méthode d’instance en tant que `Action`appelé.</span><span class="sxs-lookup"><span data-stu-id="54c68-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="54c68-154">Dans le code JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="54c68-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="54c68-155">*Pages/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="54c68-155">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="54c68-156">Lorsqu’il existe plusieurs composants, chacun avec des méthodes d’instance à appeler, utilisez une classe d’assistance pour appeler les méthodes d' `Action`instance (en tant que s) de chaque composant.</span><span class="sxs-lookup"><span data-stu-id="54c68-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="54c68-157">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="54c68-157">In the following example:</span></span>

* <span data-ttu-id="54c68-158">Le `JSInterop` composant contient plusieurs `ListItem` composants.</span><span class="sxs-lookup"><span data-stu-id="54c68-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="54c68-159">Chaque `ListItem` composant se compose d’un message et d’un bouton.</span><span class="sxs-lookup"><span data-stu-id="54c68-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="54c68-160">Quand un `ListItem` bouton de composant est sélectionné, `ListItem`la `UpdateMessage` méthode modifie le texte de l’élément de liste et masque le bouton.</span><span class="sxs-lookup"><span data-stu-id="54c68-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="54c68-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="54c68-161">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="54c68-162">Dans le code JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="54c68-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="54c68-163">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="54c68-163">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="54c68-164">*Pages/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="54c68-164">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="54c68-165">Éviter les références d’objets circulaires</span><span class="sxs-lookup"><span data-stu-id="54c68-165">Avoid circular object references</span></span>

<span data-ttu-id="54c68-166">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :</span><span class="sxs-lookup"><span data-stu-id="54c68-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="54c68-167">Appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="54c68-167">.NET method calls.</span></span>
* <span data-ttu-id="54c68-168">Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="54c68-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="54c68-169">Pour plus d’informations, consultez les problèmes suivants :</span><span class="sxs-lookup"><span data-stu-id="54c68-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="54c68-170">Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="54c68-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="54c68-171">Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="54c68-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="54c68-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="54c68-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="54c68-173">Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)</span><span class="sxs-lookup"><span data-stu-id="54c68-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="54c68-174">[Effectuer des transferts de données Blazor volumineux dans des applications serveur](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="54c68-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
