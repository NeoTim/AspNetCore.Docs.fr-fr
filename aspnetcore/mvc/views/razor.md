---
title: Informations de référence sur la syntaxe Razor pour ASP.NET Core
author: rick-anderson
description: Apprenez à utiliser la syntaxe de balisage Razor pour incorporer du code serveur dans des pages web.
ms.author: riande
ms.date: 09/19/2019
uid: mvc/views/razor
ms.openlocfilehash: 9a319f7efb6d879559afd9faca6955aba719fa2f
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168295"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="79dfe-103">Informations de référence sur la syntaxe Razor pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79dfe-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="79dfe-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) et [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="79dfe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="79dfe-105">Razor est une syntaxe de balisage qui permet d’incorporer du code serveur dans des pages web.</span><span class="sxs-lookup"><span data-stu-id="79dfe-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="79dfe-106">La syntaxe Razor est constituée de balises Razor, ainsi que de code C# et HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="79dfe-107">Les fichiers contenant de la syntaxe Razor ont généralement l’extension de fichier *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79dfe-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="79dfe-108">Razor est également disponible dans les fichiers de [Composants Razor](xref:blazor/components) ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="79dfe-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="79dfe-109">Rendu HTML</span><span class="sxs-lookup"><span data-stu-id="79dfe-109">Rendering HTML</span></span>

<span data-ttu-id="79dfe-110">Razor utilise par défaut le langage HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-110">The default Razor language is HTML.</span></span> <span data-ttu-id="79dfe-111">Le rendu HTML d’un balisage Razor n’est pas différent du rendu HTML d’un fichier en HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="79dfe-112">Le balisage HTML dans les fichiers Razor *.cshtml* est affiché tel quel par le serveur.</span><span class="sxs-lookup"><span data-stu-id="79dfe-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="79dfe-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-113">Razor syntax</span></span>

<span data-ttu-id="79dfe-114">Razor prend en charge le langage C# et utilise le symbole `@` pour convertir du code HTML en C#.</span><span class="sxs-lookup"><span data-stu-id="79dfe-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="79dfe-115">Razor évalue les expressions C# et les affiche dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="79dfe-116">Quand un symbole `@` est suivi d’un [mot clé réservé Razor](#razor-reserved-keywords), il est converti en balise Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="79dfe-117">Sinon, il est converti en code C# brut.</span><span class="sxs-lookup"><span data-stu-id="79dfe-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="79dfe-118">Pour mettre un symbole `@` en échappement dans le balisage Razor, ajoutez un deuxième symbole `@` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="79dfe-119">Le code est affiché en HTML avec un seul symbole `@` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="79dfe-120">Les attributs et le code HTML contenant des adresses e-mail ne traitent pas le symbole `@` comme un caractère de conversion.</span><span class="sxs-lookup"><span data-stu-id="79dfe-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="79dfe-121">Dans l’exemple suivant, les adresses e-mail ne sont pas modifiées par l’analyse Razor :</span><span class="sxs-lookup"><span data-stu-id="79dfe-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="79dfe-122">Expressions Razor implicites</span><span class="sxs-lookup"><span data-stu-id="79dfe-122">Implicit Razor expressions</span></span>

<span data-ttu-id="79dfe-123">Les expressions Razor implicites commencent par `@` suivi de code C# :</span><span class="sxs-lookup"><span data-stu-id="79dfe-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="79dfe-124">À l’exception du mot clé `await` C#, les expressions implicites ne doivent pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="79dfe-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="79dfe-125">Si l’instruction C# se termine de façon non ambigüe, il est possible d’insérer des espaces n’importe où dans la chaîne :</span><span class="sxs-lookup"><span data-stu-id="79dfe-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="79dfe-126">Les expressions implicites **ne doivent pas** contenir de caractères génériques C#, car les caractères entre crochets (`<>`) sont interprétés comme une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="79dfe-127">Le code suivant n’est **pas** valide :</span><span class="sxs-lookup"><span data-stu-id="79dfe-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="79dfe-128">Le code précédent génère l’un des types d’erreur de compilateur suivants :</span><span class="sxs-lookup"><span data-stu-id="79dfe-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="79dfe-129">L’élément « int » n’a pas été fermé.</span><span class="sxs-lookup"><span data-stu-id="79dfe-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="79dfe-130">Tous les éléments doivent se fermer automatiquement ou contenir une balise de fin correspondante.</span><span class="sxs-lookup"><span data-stu-id="79dfe-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="79dfe-131">Impossible de convertir le groupe de méthodes 'GenericMethod' en type non-délégué 'object'.</span><span class="sxs-lookup"><span data-stu-id="79dfe-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="79dfe-132">Souhaitiez-vous appeler la méthode ?</span><span class="sxs-lookup"><span data-stu-id="79dfe-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="79dfe-133">Les appels de méthode générique doivent être inclus dans un wrapper dans une [expression Razor explicite](#explicit-razor-expressions) ou dans un [bloc de code Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="79dfe-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="79dfe-134">Expressions Razor explicites</span><span class="sxs-lookup"><span data-stu-id="79dfe-134">Explicit Razor expressions</span></span>

<span data-ttu-id="79dfe-135">Les expressions Razor explicites se composent d’un symbole `@` suivi d’un contenu entre parenthèses.</span><span class="sxs-lookup"><span data-stu-id="79dfe-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="79dfe-136">Pour afficher l’heure de la semaine précédente, utilisez le balisage Razor suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="79dfe-137">Le contenu situé entre les parenthèses `@()` est évalué et affiché dans la sortie.</span><span class="sxs-lookup"><span data-stu-id="79dfe-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="79dfe-138">Les expressions implicites, décrites dans la section précédente, ne doivent généralement pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="79dfe-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="79dfe-139">Dans le code suivant, une semaine n’est pas déduite de l’heure actuelle :</span><span class="sxs-lookup"><span data-stu-id="79dfe-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="79dfe-140">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="79dfe-141">Les expressions explicites peuvent servir à concaténer du texte avec un résultat d’expression :</span><span class="sxs-lookup"><span data-stu-id="79dfe-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="79dfe-142">Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse e-mail, et `<p>Age@joe.Age</p>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="79dfe-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="79dfe-143">Avec une expression explicite, `<p>Age33</p>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="79dfe-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="79dfe-144">Les expressions explicites peuvent être utilisées pour afficher la sortie de méthodes génériques dans les fichiers *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79dfe-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="79dfe-145">Le balisage suivant montre comment corriger l’erreur affichée précédemment provoquée par les crochets d’un générique C#.</span><span class="sxs-lookup"><span data-stu-id="79dfe-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="79dfe-146">Le code est écrit sous forme d’expression explicite :</span><span class="sxs-lookup"><span data-stu-id="79dfe-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="79dfe-147">Encodage des expressions</span><span class="sxs-lookup"><span data-stu-id="79dfe-147">Expression encoding</span></span>

<span data-ttu-id="79dfe-148">Les expressions C# évaluées qui correspondent à une chaîne sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="79dfe-149">Les expressions C# évaluées qui correspondent à `IHtmlContent` sont affichées directement par `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="79dfe-150">Les expressions C# évaluées qui ne correspondent pas à `IHtmlContent` sont converties en chaîne par `ToString` et sont encodées avant d’être affichées.</span><span class="sxs-lookup"><span data-stu-id="79dfe-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="79dfe-151">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="79dfe-152">Le code HTML s’affiche dans le navigateur de cette façon :</span><span class="sxs-lookup"><span data-stu-id="79dfe-152">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="79dfe-153">La sortie `HtmlHelper.Raw` n’est pas encodée, mais elle est affichée sous forme de balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="79dfe-154">Utiliser `HtmlHelper.Raw` sur des entrées utilisateur non nettoyées présente un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="79dfe-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="79dfe-155">Les entrées utilisateur peuvent contenir du code malveillant JavaScript ou d’un autre type.</span><span class="sxs-lookup"><span data-stu-id="79dfe-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="79dfe-156">Le nettoyage des entrées utilisateur est difficile.</span><span class="sxs-lookup"><span data-stu-id="79dfe-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="79dfe-157">C’est pourquoi il est préférable de ne pas utiliser `HtmlHelper.Raw` sur des entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="79dfe-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="79dfe-158">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="79dfe-159">Blocs de code Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-159">Razor code blocks</span></span>

<span data-ttu-id="79dfe-160">Les blocs de code Razor commencent par `@` et sont délimités par deux `{}`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="79dfe-161">Contrairement aux expressions, le code C# figurant dans des blocs de code n’est pas affiché.</span><span class="sxs-lookup"><span data-stu-id="79dfe-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="79dfe-162">Les blocs de code et les expressions dans une vue ont la même étendue et sont définis dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="79dfe-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="79dfe-163">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79dfe-164">Dans des blocs de code, déclarez des [fonctions locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) avec des balises servant de méthodes de création de modèles :</span><span class="sxs-lookup"><span data-stu-id="79dfe-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="79dfe-165">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="79dfe-166">Transitions implicites</span><span class="sxs-lookup"><span data-stu-id="79dfe-166">Implicit transitions</span></span>

<span data-ttu-id="79dfe-167">Un bloc de code utilise le langage C# par défaut, mais la page Razor peut le reconvertir en HTML :</span><span class="sxs-lookup"><span data-stu-id="79dfe-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="79dfe-168">Conversion délimitée explicite</span><span class="sxs-lookup"><span data-stu-id="79dfe-168">Explicit delimited transition</span></span>

<span data-ttu-id="79dfe-169">Pour définir une sous-section d’un bloc de code qui doit s’afficher en HTML, placez les caractères à afficher dans la balise Razor `<text>` suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="79dfe-170">Utilisez cette approche pour afficher du code HTML qui n’est pas entouré d’une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="79dfe-171">Sans la balise HTML ou Razor, une erreur d’exécution Razor se produit.</span><span class="sxs-lookup"><span data-stu-id="79dfe-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="79dfe-172">La balise `<text>` est utile pour contrôler les espaces blancs dans le contenu affiché :</span><span class="sxs-lookup"><span data-stu-id="79dfe-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="79dfe-173">Seul le contenu situé dans la balise `<text>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="79dfe-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="79dfe-174">Aucun espace blanc avant ou après la balise `<text>` ne s’affiche dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-colon"></a><span data-ttu-id="79dfe-175">Conversion de ligne explicite avec \@&colon;</span><span class="sxs-lookup"><span data-stu-id="79dfe-175">Explicit line transition with \@&colon;</span></span>

<span data-ttu-id="79dfe-176">Pour afficher le reste d’une ligne entière en HTML à l’intérieur d’un bloc de code, utilisez la syntaxe `@:` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-176">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="79dfe-177">Sans la balise `@:` dans le code, une erreur d’exécution Razor se produit.</span><span class="sxs-lookup"><span data-stu-id="79dfe-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="79dfe-178">La présence de caractères `@` en trop dans un fichier Razor risque de provoquer des erreurs du compilateur au niveau des instructions suivantes dans le bloc.</span><span class="sxs-lookup"><span data-stu-id="79dfe-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="79dfe-179">Ces erreurs du compilateur peuvent être difficiles à comprendre, car elles se produisent en réalité avant les erreurs signalées.</span><span class="sxs-lookup"><span data-stu-id="79dfe-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="79dfe-180">Ce type d’erreur est courant après la combinaison de plusieurs expressions implicites ou explicites dans un même bloc de code.</span><span class="sxs-lookup"><span data-stu-id="79dfe-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="79dfe-181">Structures de contrôle</span><span class="sxs-lookup"><span data-stu-id="79dfe-181">Control structures</span></span>

<span data-ttu-id="79dfe-182">Les structures de contrôle sont une extension des blocs de code.</span><span class="sxs-lookup"><span data-stu-id="79dfe-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="79dfe-183">Toutes les caractéristiques des blocs de code (conversion de balisage, Inline C#) valent aussi pour les structures suivantes :</span><span class="sxs-lookup"><span data-stu-id="79dfe-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="79dfe-184">Conditions \@if, else if, else et \@switch</span><span class="sxs-lookup"><span data-stu-id="79dfe-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="79dfe-185">`@if` contrôle l’exécution du code :</span><span class="sxs-lookup"><span data-stu-id="79dfe-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="79dfe-186">`else` et `else if` ne nécessitent pas le symbole `@` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="79dfe-187">Le balisage suivant montre comment utiliser une instruction switch :</span><span class="sxs-lookup"><span data-stu-id="79dfe-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="79dfe-188">Boucle \@for, \@foreach, \@while et \@do while</span><span class="sxs-lookup"><span data-stu-id="79dfe-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="79dfe-189">Le rendu HTML peut être réalisé à partir d'instructions de contrôle de boucle.</span><span class="sxs-lookup"><span data-stu-id="79dfe-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="79dfe-190">Pour afficher une liste de personnes :</span><span class="sxs-lookup"><span data-stu-id="79dfe-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="79dfe-191">Les instructions de boucle suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="79dfe-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="79dfe-192">Composé \@using</span><span class="sxs-lookup"><span data-stu-id="79dfe-192">Compound \@using</span></span>

<span data-ttu-id="79dfe-193">En C#, une instruction `using` est utilisée pour garantir la dispose d’un objet.</span><span class="sxs-lookup"><span data-stu-id="79dfe-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="79dfe-194">Dans Razor, le même mécanisme permet de créer des HTML Helpers avec du contenu supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="79dfe-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="79dfe-195">Dans le code suivant, les HTML Helpers affichent une balise `<form>` à l’aide de l’instruction `@using` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="79dfe-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="79dfe-196">\@try, catch, finally</span></span>

<span data-ttu-id="79dfe-197">La gestion des exceptions est similaire à C# :</span><span class="sxs-lookup"><span data-stu-id="79dfe-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="79dfe-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="79dfe-198">\@lock</span></span>

<span data-ttu-id="79dfe-199">Razor permet de verrouiller les sections critiques par des instructions lock :</span><span class="sxs-lookup"><span data-stu-id="79dfe-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="79dfe-200">Commentaires</span><span class="sxs-lookup"><span data-stu-id="79dfe-200">Comments</span></span>

<span data-ttu-id="79dfe-201">Razor prend en charge les commentaires HTML et C# :</span><span class="sxs-lookup"><span data-stu-id="79dfe-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="79dfe-202">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="79dfe-203">Le serveur supprime les commentaires Razor avant d’afficher la page web.</span><span class="sxs-lookup"><span data-stu-id="79dfe-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="79dfe-204">Razor délimite les commentaires avec `@*  *@`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="79dfe-205">Le code suivant est commenté pour indiquer au serveur de ne pas afficher le balisage :</span><span class="sxs-lookup"><span data-stu-id="79dfe-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="79dfe-206">Directives</span><span class="sxs-lookup"><span data-stu-id="79dfe-206">Directives</span></span>

<span data-ttu-id="79dfe-207">Les directives Razor sont représentées par des expressions implicites constituées du symbole `@` suivi de mots clés réservés.</span><span class="sxs-lookup"><span data-stu-id="79dfe-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="79dfe-208">En règle générale, une directive change la manière dont une vue est analysée ou active une fonctionnalité différente.</span><span class="sxs-lookup"><span data-stu-id="79dfe-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="79dfe-209">Pour mieux comprendre le fonctionnement des directives, vous devez bien comprendre comment Razor génère le code pour une vue.</span><span class="sxs-lookup"><span data-stu-id="79dfe-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="79dfe-210">Le code génère une classe semblable à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="79dfe-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="79dfe-211">Plus loin dans cet article, la section [Inspecter la classe C# Razor générée pour une vue](#inspect-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="79dfe-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="79dfe-212">\@attribute</span></span>

<span data-ttu-id="79dfe-213">La directive `@attribute` permet d’ajouter l’attribut donné à la classe de la page ou de la vue générée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="79dfe-214">L’exemple suivant ajoute l’attribut `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="79dfe-215">\@code</span><span class="sxs-lookup"><span data-stu-id="79dfe-215">\@code</span></span>

<span data-ttu-id="79dfe-216">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-217">Le blob `@code` permet à un [composant Razor](xref:blazor/components) d’ajouter des membres C# (champs, propriétés et méthodes) à un composant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```cshtml
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="79dfe-218">Pour les composants Razor, `@code` est un alias [@functions](#functions) et est recommandé sur `@functions`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-218">For Razor components, `@code` is an alias of [@functions](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="79dfe-219">Plus d’un bloc `@code` est autorisé.</span><span class="sxs-lookup"><span data-stu-id="79dfe-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="79dfe-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="79dfe-220">\@functions</span></span>

<span data-ttu-id="79dfe-221">La directive `@functions` permet d’ajouter des membres C# (champs, propriétés et méthodes) à la classe générée :</span><span class="sxs-lookup"><span data-stu-id="79dfe-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79dfe-222">Dans les [composants Razor](xref:blazor/components), utilisez `@code` sur `@functions` pour ajouter des membres C#.</span><span class="sxs-lookup"><span data-stu-id="79dfe-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="79dfe-223">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="79dfe-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="79dfe-224">Le code génère le balisage HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="79dfe-225">Le code suivant correspond à la classe C# Razor générée :</span><span class="sxs-lookup"><span data-stu-id="79dfe-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79dfe-226">Les méthodes `@functions` servent de méthodes de création de modèles lorsqu’elles comprennent des balises :</span><span class="sxs-lookup"><span data-stu-id="79dfe-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="79dfe-227">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="79dfe-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="79dfe-228">\@implements</span></span>

<span data-ttu-id="79dfe-229">La directive `@implements` implémente une interface pour la classe générée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="79dfe-230">L’exemple suivant implémente <xref:System.IDisposable?displayProperty=fullName> afin que la méthode <xref:System.IDisposable.Dispose*> puisse être appelée :</span><span class="sxs-lookup"><span data-stu-id="79dfe-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="79dfe-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="79dfe-231">\@inherits</span></span>

<span data-ttu-id="79dfe-232">La directive `@inherits` fournit un contrôle complet de la classe héritée par la vue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="79dfe-233">Le code suivant est un type de page Razor personnalisé :</span><span class="sxs-lookup"><span data-stu-id="79dfe-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="79dfe-234">Le `CustomText` s’affiche dans une vue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="79dfe-235">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="79dfe-236">`@model` et `@inherits` peuvent s’utiliser dans la même vue.</span><span class="sxs-lookup"><span data-stu-id="79dfe-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="79dfe-237">`@inherits` peut être dans un fichier *_ViewImports.cshtml* importé par la vue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="79dfe-238">Le code suivant est un exemple de vue fortement typée :</span><span class="sxs-lookup"><span data-stu-id="79dfe-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="79dfe-239">Si « rick@contoso.com » est passé au modèle, la vue génère le balisage HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="79dfe-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="79dfe-240">\@inject</span></span>

<span data-ttu-id="79dfe-241">La directive `@inject` permet à la page Razor d’injecter un service dans une vue à partir du [conteneur de services](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="79dfe-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="79dfe-242">Pour plus d’informations, consultez [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="79dfe-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="79dfe-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="79dfe-243">\@layout</span></span>

<span data-ttu-id="79dfe-244">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-245">La directive `@layout` spécifie une disposition pour un composant Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="79dfe-246">Les composants de disposition sont utilisés pour éviter la duplication et l’incohérence de code.</span><span class="sxs-lookup"><span data-stu-id="79dfe-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="79dfe-247">Pour plus d'informations, consultez <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="79dfe-248">\@model</span><span class="sxs-lookup"><span data-stu-id="79dfe-248">\@model</span></span>

<span data-ttu-id="79dfe-249">*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="79dfe-250">La directive `@model` spécifie le type du modèle passé à une vue ou une page :</span><span class="sxs-lookup"><span data-stu-id="79dfe-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="79dfe-251">Dans une application ASP.NET Core MVC ou Razor Pages créée avec des comptes d’utilisateur individuels, *Views/Account/Login.cshtml* contient la déclaration de modèle suivante :</span><span class="sxs-lookup"><span data-stu-id="79dfe-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="79dfe-252">La classe générée hérite de `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="79dfe-253">Razor expose une propriété `Model` pour accéder au modèle passé à la vue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="79dfe-254">La directive `@model` spécifie le type de la propriété `Model`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="79dfe-255">La directive spécifie le type `T` dans `RazorPage<T>` pour la classe générée dont dérive la vue.</span><span class="sxs-lookup"><span data-stu-id="79dfe-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="79dfe-256">Si la directive `@model` n’est pas spécifiée, la propriété `Model` est de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="79dfe-257">Pour plus d’informations, consultez les [modèles fortement typés et le mot clé @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="79dfe-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="79dfe-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="79dfe-258">\@namespace</span></span>

<span data-ttu-id="79dfe-259">La directive `@namespace` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="79dfe-260">Définit l’espace de noms de la classe de la page Razor, de la vue MVC ou du composant Razor généré.</span><span class="sxs-lookup"><span data-stu-id="79dfe-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="79dfe-261">Définit les espaces de noms dérivés racine d’une classe de pages, de vues ou de composants à partir du fichier d’importations le plus proche dans l’arborescence de répertoires, *_ViewImports.cshtml* (vues ou pages) ou *_Imports.razor* (composants Razor).</span><span class="sxs-lookup"><span data-stu-id="79dfe-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="79dfe-262">Pour l’exemple de Razor Pages illustré dans le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="79dfe-263">Chaque page importe *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79dfe-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="79dfe-264">*Pages/_ViewImports.cshtml* contient `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="79dfe-265">Chaque page a `Hello.World` comme racine de son espace de noms.</span><span class="sxs-lookup"><span data-stu-id="79dfe-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="79dfe-266">Page</span><span class="sxs-lookup"><span data-stu-id="79dfe-266">Page</span></span>                                        | <span data-ttu-id="79dfe-267">Espace de noms</span><span class="sxs-lookup"><span data-stu-id="79dfe-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="79dfe-268">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="79dfe-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="79dfe-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="79dfe-271">Les relations précédentes s’appliquent aux fichiers d’importation utilisés avec les vues MVC et les composants Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="79dfe-272">Lorsque plusieurs fichiers d’importation ont une directive `@namespace`, le fichier le plus proche de la page, de la vue ou du composant dans l’arborescence de répertoires est utilisé pour définir l’espace de noms racine.</span><span class="sxs-lookup"><span data-stu-id="79dfe-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="79dfe-273">Si le dossier *EvenMorePages* dans l’exemple précédent comprend un fichier d’importations avec `@namespace Another.Planet` (ou si le fichier *Pages/MorePages/EvenMorePages/Page.cshtml* contient `@namespace Another.Planet`), le résultat est indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="79dfe-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="79dfe-274">Page</span><span class="sxs-lookup"><span data-stu-id="79dfe-274">Page</span></span>                                        | <span data-ttu-id="79dfe-275">Espace de noms</span><span class="sxs-lookup"><span data-stu-id="79dfe-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="79dfe-276">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="79dfe-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="79dfe-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="79dfe-279">\@page</span><span class="sxs-lookup"><span data-stu-id="79dfe-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79dfe-280">La directive `@page` a des effets différents selon le type du fichier dans lequel elle apparaît.</span><span class="sxs-lookup"><span data-stu-id="79dfe-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="79dfe-281">La directive :</span><span class="sxs-lookup"><span data-stu-id="79dfe-281">The directive:</span></span>

* <span data-ttu-id="79dfe-282">Dans un fichier *.cshtml*, indique que le fichier est une page Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="79dfe-283">Pour plus d'informations, consultez <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-283">For more information, see <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="79dfe-284">Spécifie qu’un composant Razor doit gérer les requêtes directement.</span><span class="sxs-lookup"><span data-stu-id="79dfe-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="79dfe-285">Pour plus d'informations, consultez <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79dfe-286">La directive `@page` sur la première ligne d’un fichier *.cshtml* indique que le fichier est une page Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="79dfe-287">Pour plus d'informations, consultez <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="79dfe-288">\@section</span><span class="sxs-lookup"><span data-stu-id="79dfe-288">\@section</span></span>

<span data-ttu-id="79dfe-289">*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="79dfe-290">La directive `@section` est utilisée conjointement avec des [dispositions MVC et Razor Pages](xref:mvc/views/layout) pour permettre aux vues et aux pages d’afficher le contenu dans différentes parties de la page HTML.</span><span class="sxs-lookup"><span data-stu-id="79dfe-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="79dfe-291">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="79dfe-292">\@using</span><span class="sxs-lookup"><span data-stu-id="79dfe-292">\@using</span></span>

<span data-ttu-id="79dfe-293">La directive `@using` ajoute la directive `using` C# à la vue générée :</span><span class="sxs-lookup"><span data-stu-id="79dfe-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79dfe-294">Dans des [composants Razor](xref:blazor/components), `@using` contrôle également les composants dans la portée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="79dfe-295">Attributs de directive</span><span class="sxs-lookup"><span data-stu-id="79dfe-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="79dfe-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="79dfe-296">\@attributes</span></span>

<span data-ttu-id="79dfe-297">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-298">`@attributes` permet à un composant de restituer des attributs non déclarés.</span><span class="sxs-lookup"><span data-stu-id="79dfe-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="79dfe-299">Pour plus d'informations, consultez <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="79dfe-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="79dfe-300">\@bind</span></span>

<span data-ttu-id="79dfe-301">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-302">La liaison de données dans des composants s’effectue avec l’attribut `@bind`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="79dfe-303">Pour plus d'informations, consultez <xref:blazor/components#data-binding>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-303">For more information, see <xref:blazor/components#data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="79dfe-304">\@on{event}</span><span class="sxs-lookup"><span data-stu-id="79dfe-304">\@on{event}</span></span>

<span data-ttu-id="79dfe-305">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-306">Razor fournit des fonctionnalités de gestion des événements pour les composants.</span><span class="sxs-lookup"><span data-stu-id="79dfe-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="79dfe-307">Pour plus d'informations, consultez <xref:blazor/components#event-handling>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-307">For more information, see <xref:blazor/components#event-handling>.</span></span>

### <a name="key"></a><span data-ttu-id="79dfe-308">\@key</span><span class="sxs-lookup"><span data-stu-id="79dfe-308">\@key</span></span>

<span data-ttu-id="79dfe-309">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-310">L’attribut de directive `@key` amène les composants à comparer l’algorithme afin de garantir la préservation des éléments ou des composants en fonction de la valeur de la clé.</span><span class="sxs-lookup"><span data-stu-id="79dfe-310">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="79dfe-311">Pour plus d'informations, consultez <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-311">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="79dfe-312">\@ref</span><span class="sxs-lookup"><span data-stu-id="79dfe-312">\@ref</span></span>

<span data-ttu-id="79dfe-313">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-313">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-314">Les références de composants (`@ref`) permettent de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance.</span><span class="sxs-lookup"><span data-stu-id="79dfe-314">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="79dfe-315">Pour plus d'informations, consultez <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-315">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="79dfe-316">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="79dfe-316">\@typeparam</span></span>

<span data-ttu-id="79dfe-317">*Ce scénario s’applique uniquement aux composants Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="79dfe-318">La `@typeparam` directive déclare un paramètre de type générique pour la classe de composant générée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-318">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="79dfe-319">Pour plus d'informations, consultez <xref:blazor/components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-319">For more information, see <xref:blazor/components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="79dfe-320">Délégués Razor basés sur des modèles</span><span class="sxs-lookup"><span data-stu-id="79dfe-320">Templated Razor delegates</span></span>

<span data-ttu-id="79dfe-321">Les modèles Razor vous permettent de définir un extrait de code d’interface utilisateur avec le format suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-321">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="79dfe-322">L’exemple suivant montre comment spécifier un délégué Razor basé sur un modèle comme <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="79dfe-322">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="79dfe-323">Le [type dynamique](/dotnet/csharp/programming-guide/types/using-type-dynamic) est spécifié pour le paramètre de la méthode encapsulée par le délégué.</span><span class="sxs-lookup"><span data-stu-id="79dfe-323">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="79dfe-324">Un [type objet](/dotnet/csharp/language-reference/keywords/object) est spécifié comme valeur de retour du délégué.</span><span class="sxs-lookup"><span data-stu-id="79dfe-324">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="79dfe-325">Le modèle est utilisé avec une <xref:System.Collections.Generic.List%601> de `Pet` qui a une propriété `Name`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-325">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="79dfe-326">Le modèle est rendu avec des éléments `pets` fournis par une instruction `foreach` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-326">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="79dfe-327">Sortie rendue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-327">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="79dfe-328">Vous pouvez également fournir un modèle Razor inline en tant qu’argument à une méthode.</span><span class="sxs-lookup"><span data-stu-id="79dfe-328">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="79dfe-329">Dans l’exemple suivant, la méthode `Repeat` reçoit un modèle Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-329">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="79dfe-330">La méthode utilise le modèle pour produire du contenu HTML avec des répétitions d’éléments fournis à partir d’une liste :</span><span class="sxs-lookup"><span data-stu-id="79dfe-330">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="79dfe-331">En utilisant la liste d’éléments « pets » de l’exemple précédent, la méthode `Repeat` est appelée avec :</span><span class="sxs-lookup"><span data-stu-id="79dfe-331">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="79dfe-332"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-332"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="79dfe-333">Nombre de fois que chaque élément « pet » doit être répété.</span><span class="sxs-lookup"><span data-stu-id="79dfe-333">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="79dfe-334">Modèle inline à utiliser pour les éléments de liste d’une liste non triée.</span><span class="sxs-lookup"><span data-stu-id="79dfe-334">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="79dfe-335">Sortie rendue :</span><span class="sxs-lookup"><span data-stu-id="79dfe-335">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="79dfe-336">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="79dfe-336">Tag Helpers</span></span>

<span data-ttu-id="79dfe-337">*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="79dfe-337">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="79dfe-338">Il existe trois directives spécifiques aux [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="79dfe-338">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="79dfe-339">Directive</span><span class="sxs-lookup"><span data-stu-id="79dfe-339">Directive</span></span> | <span data-ttu-id="79dfe-340">Fonction</span><span class="sxs-lookup"><span data-stu-id="79dfe-340">Function</span></span> |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="79dfe-341">Rend les Tag Helpers disponibles dans une vue.</span><span class="sxs-lookup"><span data-stu-id="79dfe-341">Makes Tag Helpers available to a view.</span></span> |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="79dfe-342">Supprime les Tag Helpers précédemment ajoutés à une vue.</span><span class="sxs-lookup"><span data-stu-id="79dfe-342">Removes Tag Helpers previously added from a view.</span></span> |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="79dfe-343">Spécifie un préfixe de balise pour activer la prise en charge des Tag Helpers et rendre leur usage explicite.</span><span class="sxs-lookup"><span data-stu-id="79dfe-343">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="79dfe-344">Mots clés réservés Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-344">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="79dfe-345">Mots clés Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-345">Razor keywords</span></span>

* <span data-ttu-id="79dfe-346">page (nécessite ASP.NET Core 2.1 ou ultérieur)</span><span class="sxs-lookup"><span data-stu-id="79dfe-346">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="79dfe-347">namespace</span><span class="sxs-lookup"><span data-stu-id="79dfe-347">namespace</span></span>
* <span data-ttu-id="79dfe-348">fonctions</span><span class="sxs-lookup"><span data-stu-id="79dfe-348">functions</span></span>
* <span data-ttu-id="79dfe-349">hérite</span><span class="sxs-lookup"><span data-stu-id="79dfe-349">inherits</span></span>
* <span data-ttu-id="79dfe-350">modèle</span><span class="sxs-lookup"><span data-stu-id="79dfe-350">model</span></span>
* <span data-ttu-id="79dfe-351">section</span><span class="sxs-lookup"><span data-stu-id="79dfe-351">section</span></span>
* <span data-ttu-id="79dfe-352">helper (non pris en charge par ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="79dfe-352">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="79dfe-353">Les mots clés Razor sont précédés d’une séquence d’échappement `@(Razor Keyword)` (par exemple, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="79dfe-353">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="79dfe-354">Mots clés Razor C#</span><span class="sxs-lookup"><span data-stu-id="79dfe-354">C# Razor keywords</span></span>

* <span data-ttu-id="79dfe-355">casse</span><span class="sxs-lookup"><span data-stu-id="79dfe-355">case</span></span>
* <span data-ttu-id="79dfe-356">do</span><span class="sxs-lookup"><span data-stu-id="79dfe-356">do</span></span>
* <span data-ttu-id="79dfe-357">default</span><span class="sxs-lookup"><span data-stu-id="79dfe-357">default</span></span>
* <span data-ttu-id="79dfe-358">for</span><span class="sxs-lookup"><span data-stu-id="79dfe-358">for</span></span>
* <span data-ttu-id="79dfe-359">foreach</span><span class="sxs-lookup"><span data-stu-id="79dfe-359">foreach</span></span>
* <span data-ttu-id="79dfe-360">if</span><span class="sxs-lookup"><span data-stu-id="79dfe-360">if</span></span>
* <span data-ttu-id="79dfe-361">else</span><span class="sxs-lookup"><span data-stu-id="79dfe-361">else</span></span>
* <span data-ttu-id="79dfe-362">lock</span><span class="sxs-lookup"><span data-stu-id="79dfe-362">lock</span></span>
* <span data-ttu-id="79dfe-363">switch</span><span class="sxs-lookup"><span data-stu-id="79dfe-363">switch</span></span>
* <span data-ttu-id="79dfe-364">try</span><span class="sxs-lookup"><span data-stu-id="79dfe-364">try</span></span>
* <span data-ttu-id="79dfe-365">catch</span><span class="sxs-lookup"><span data-stu-id="79dfe-365">catch</span></span>
* <span data-ttu-id="79dfe-366">finally</span><span class="sxs-lookup"><span data-stu-id="79dfe-366">finally</span></span>
* <span data-ttu-id="79dfe-367">using</span><span class="sxs-lookup"><span data-stu-id="79dfe-367">using</span></span>
* <span data-ttu-id="79dfe-368">while</span><span class="sxs-lookup"><span data-stu-id="79dfe-368">while</span></span>

<span data-ttu-id="79dfe-369">Les mots clés Razor C# doivent être précédés d’une double séquence d’échappement `@(@C# Razor Keyword)` (par exemple, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="79dfe-369">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="79dfe-370">La première séquence d’échappement `@` est pour l’analyseur Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-370">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="79dfe-371">La seconde séquence d’échappement `@` est pour l’analyseur C#.</span><span class="sxs-lookup"><span data-stu-id="79dfe-371">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="79dfe-372">Mots clés réservés non utilisés par Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-372">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="79dfe-373">class</span><span class="sxs-lookup"><span data-stu-id="79dfe-373">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="79dfe-374">Inspecter la classe C# Razor générée pour une vue</span><span class="sxs-lookup"><span data-stu-id="79dfe-374">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="79dfe-375">Avec le SDK .NET Core 2.1 ou ultérieur, le [SDK Razor](xref:razor-pages/sdk) gère la compilation des fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="79dfe-375">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="79dfe-376">Quand vous créez un projet, le SDK Razor génère un répertoire *obj/<configuration_build>/<moniker_framework_cible>/Razor* dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="79dfe-376">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="79dfe-377">La structure de répertoires dans le répertoire *Razor* reflète la structure de répertoires du projet.</span><span class="sxs-lookup"><span data-stu-id="79dfe-377">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="79dfe-378">Considérez la structure de répertoires suivante dans un projet Razor Pages ASP.NET Core 2.1 ciblant .NET Core 2.1 :</span><span class="sxs-lookup"><span data-stu-id="79dfe-378">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="79dfe-379">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-379">**Areas/**</span></span>
  * <span data-ttu-id="79dfe-380">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-380">**Admin/**</span></span>
    * <span data-ttu-id="79dfe-381">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-381">**Pages/**</span></span>
      * <span data-ttu-id="79dfe-382">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-382">*Index.cshtml*</span></span>
      * <span data-ttu-id="79dfe-383">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-383">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="79dfe-384">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-384">**Pages/**</span></span>
  * <span data-ttu-id="79dfe-385">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-385">**Shared/**</span></span>
    * <span data-ttu-id="79dfe-386">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-386">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="79dfe-387">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-387">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="79dfe-388">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-388">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="79dfe-389">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79dfe-389">*Index.cshtml*</span></span>
  * <span data-ttu-id="79dfe-390">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-390">*Index.cshtml.cs*</span></span>

<span data-ttu-id="79dfe-391">La création du projet dans la configuration *Debug* génère le répertoire *obj* suivant :</span><span class="sxs-lookup"><span data-stu-id="79dfe-391">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="79dfe-392">**obj/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-392">**obj/**</span></span>
  * <span data-ttu-id="79dfe-393">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-393">**Debug/**</span></span>
    * <span data-ttu-id="79dfe-394">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-394">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="79dfe-395">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-395">**Razor/**</span></span>
        * <span data-ttu-id="79dfe-396">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-396">**Areas/**</span></span>
          * <span data-ttu-id="79dfe-397">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-397">**Admin/**</span></span>
            * <span data-ttu-id="79dfe-398">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-398">**Pages/**</span></span>
              * <span data-ttu-id="79dfe-399">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-399">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="79dfe-400">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-400">**Pages/**</span></span>
          * <span data-ttu-id="79dfe-401">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="79dfe-401">**Shared/**</span></span>
            * <span data-ttu-id="79dfe-402">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-402">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79dfe-403">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-403">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79dfe-404">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-404">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79dfe-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79dfe-405">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="79dfe-406">Pour afficher la classe générée pour *Pages/Index.cshtml*, ouvrez *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="79dfe-406">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="79dfe-407">Ajoutez la classe suivante au projet ASP.NET Core MVC :</span><span class="sxs-lookup"><span data-stu-id="79dfe-407">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="79dfe-408">Dans `Startup.ConfigureServices`, remplacez la classe `RazorTemplateEngine` ajoutée par MVC par la classe `CustomTemplateEngine` :</span><span class="sxs-lookup"><span data-stu-id="79dfe-408">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="79dfe-409">Définissez un point d’arrêt sur l’instruction `return csharpDocument;` de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-409">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="79dfe-410">Quand l’exécution du programme s’arrête au point d’arrêt, affichez la valeur de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="79dfe-410">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Vue Visualiseur de texte du code généré](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="79dfe-412">Recherches de vues et respect de la casse</span><span class="sxs-lookup"><span data-stu-id="79dfe-412">View lookups and case sensitivity</span></span>

<span data-ttu-id="79dfe-413">Le moteur de vue Razor effectue des recherches de vues en respectant la casse.</span><span class="sxs-lookup"><span data-stu-id="79dfe-413">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="79dfe-414">Toutefois, la recherche réellement effectuée est déterminée par le système de fichiers sous-jacent :</span><span class="sxs-lookup"><span data-stu-id="79dfe-414">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="79dfe-415">Source basé sur un fichier :</span><span class="sxs-lookup"><span data-stu-id="79dfe-415">File based source:</span></span>
  * <span data-ttu-id="79dfe-416">Sur les systèmes d’exploitation avec des systèmes de fichiers qui ne respectent pas la casse (par exemple, Windows), les recherches de fournisseurs de fichiers physiques ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="79dfe-416">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="79dfe-417">Par exemple, `return View("Test")` trouve les correspondances */Views/Home/Test.cshtml*, */Views/home/test.cshtml* et toutes les autres variantes de casse.</span><span class="sxs-lookup"><span data-stu-id="79dfe-417">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="79dfe-418">Sur des systèmes de fichiers respectant la casse (par exemple, Linux, OSX, et avec `EmbeddedFileProvider`), les recherches respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="79dfe-418">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="79dfe-419">Par exemple, `return View("Test")` trouve uniquement la correspondance */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79dfe-419">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="79dfe-420">Vues précompilées : Avec ASP.NET Core 2.0 et les versions ultérieures, les recherches de vues précompilées ne respectent pas la casse, quels que soient les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="79dfe-420">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="79dfe-421">Le comportement est le même que celui du fournisseur de fichiers physiques sur Windows.</span><span class="sxs-lookup"><span data-stu-id="79dfe-421">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="79dfe-422">Si deux vues précompilées diffèrent seulement par leur casse, le résultat de la recherche est non déterministe.</span><span class="sxs-lookup"><span data-stu-id="79dfe-422">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="79dfe-423">Les développeurs doivent s’efforcer d’utiliser la même casse pour les noms de fichiers et de répertoires que pour les noms des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="79dfe-423">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="79dfe-424">Zone, contrôleur et action</span><span class="sxs-lookup"><span data-stu-id="79dfe-424">Area, controller, and action names.</span></span>
* <span data-ttu-id="79dfe-425">Pages Razor</span><span class="sxs-lookup"><span data-stu-id="79dfe-425">Razor Pages.</span></span>

<span data-ttu-id="79dfe-426">L’utilisation d’une casse identique garantit que les déploiements trouvent toujours les vues associées, indépendamment du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="79dfe-426">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
