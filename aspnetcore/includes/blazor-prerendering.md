---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159892"
---
Bien qu’une application Blazor Server soit prérestitué, certaines actions, telles que l’appel en JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie. Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.

Pour différer les appels Interop JavaScript jusqu’à ce que la connexion avec le navigateur soit établie, vous pouvez utiliser l' [événement du cycle de vie du composant OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Cet événement est appelé uniquement une fois que l’application est entièrement rendue et que la connexion cliente est établie.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

Pour l’exemple de code précédent, fournissez une fonction JavaScript `setElementText` à l’intérieur de l’élément `<head>` de *wwwroot/index.html* (Blazor webassembly) ou *pages/_Host. cshtml* (serveurBlazor). La fonction est appelée avec `IJSRuntime.InvokeVoidAsync` et ne retourne pas de valeur :

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> L’exemple précédent modifie directement le Document Object Model (DOM) à des fins de démonstration uniquement. La modification directe du DOM avec JavaScript n’est pas recommandée dans la plupart des scénarios, car JavaScript peut interférer avec le suivi des modifications de Blazor.

Le composant suivant montre comment utiliser l’interopérabilité JavaScript dans le cadre de la logique d’initialisation d’un composant d’une manière compatible avec le prérendu. Le composant montre qu’il est possible de déclencher une mise à jour de rendu depuis l’intérieur de `OnAfterRenderAsync`. Le développeur doit éviter de créer une boucle infinie dans ce scénario.

Lorsque `JSRuntime.InvokeAsync` est appelée, `ElementRef` est utilisé uniquement dans `OnAfterRenderAsync` et non dans une méthode de cycle de vie antérieure, car il n’y a pas d’élément JavaScript tant que le composant n’est pas rendu.

[StateHasChanged](xref:blazor/lifecycle#state-changes) est appelé pour rerestituer le composant avec le nouvel état obtenu à partir de l’appel JavaScript Interop. Le code ne crée pas de boucle infinie car `StateHasChanged` est appelé uniquement lorsque `infoFromJs` est `null`.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

Pour l’exemple de code précédent, fournissez une fonction JavaScript `setElementText` à l’intérieur de l’élément `<head>` de *wwwroot/index.html* (Blazor webassembly) ou *pages/_Host. cshtml* (serveurBlazor). La fonction est appelée avec `IJSRuntime.InvokeAsync` et retourne une valeur :

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> L’exemple précédent modifie directement le Document Object Model (DOM) à des fins de démonstration uniquement. La modification directe du DOM avec JavaScript n’est pas recommandée dans la plupart des scénarios, car JavaScript peut interférer avec le suivi des modifications de Blazor.
