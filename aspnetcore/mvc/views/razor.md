---
title: Informations de référence sur la syntaxe Razor pour ASP.NET Core
author: rick-anderson
description: Apprenez à utiliser la syntaxe de balisage Razor pour incorporer du code serveur dans des pages web.
ms.author: riande
ms.date: 11/09/2019
uid: mvc/views/razor
ms.openlocfilehash: dea1cd8986757b0bafab9ba9e8aa358a57a6b5eb
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317403"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Informations de référence sur la syntaxe Razor pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) et [Dan Vicarel](https://github.com/Rabadash8820)

Razor est une syntaxe de balisage qui permet d’incorporer du code serveur dans des pages web. La syntaxe Razor est constituée de balises Razor, ainsi que de code C# et HTML. Les fichiers contenant de la syntaxe Razor ont généralement l’extension de fichier *.cshtml*. Razor est également disponible dans les fichiers de [Composants Razor](xref:blazor/components) ( *.razor*).

## <a name="rendering-html"></a>Rendu HTML

Razor utilise par défaut le langage HTML. Le rendu HTML d’un balisage Razor n’est pas différent du rendu HTML d’un fichier en HTML. Le balisage HTML dans les fichiers Razor *.cshtml* est affiché tel quel par le serveur.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor prend en charge le langage C# et utilise le symbole `@` pour convertir du code HTML en C#. Razor évalue les expressions C# et les affiche dans la sortie HTML.

Quand un symbole `@` est suivi d’un [mot clé réservé Razor](#razor-reserved-keywords), il est converti en balise Razor. Sinon, il est converti en code C# brut.

Pour mettre un symbole `@` en échappement dans le balisage Razor, ajoutez un deuxième symbole `@` :

```cshtml
<p>@@Username</p>
```

Le code est affiché en HTML avec un seul symbole `@` :

```html
<p>@Username</p>
```

Les attributs et le code HTML contenant des adresses e-mail ne traitent pas le symbole `@` comme un caractère de conversion. Dans l’exemple suivant, les adresses e-mail ne sont pas modifiées par l’analyse Razor :

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Expressions Razor implicites

Les expressions Razor implicites commencent par `@` suivi de code C# :

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

À l’exception du mot clé `await` C#, les expressions implicites ne doivent pas contenir d’espaces. Si l’instruction C# se termine de façon non ambigüe, il est possible d’insérer des espaces n’importe où dans la chaîne :

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Les expressions implicites **ne doivent pas** contenir de caractères génériques C#, car les caractères entre crochets (`<>`) sont interprétés comme une balise HTML. Le code suivant n’est **pas** valide :

```cshtml
<p>@GenericMethod<int>()</p>
```

Le code précédent génère l’un des types d’erreur de compilateur suivants :

* L’élément « int » n’a pas été fermé. Tous les éléments doivent se fermer automatiquement ou contenir une balise de fin correspondante.
* Impossible de convertir le groupe de méthodes 'GenericMethod' en type non-délégué 'object'. Souhaitiez-vous appeler la méthode ?

Les appels de méthode générique doivent être inclus dans un wrapper dans une [expression Razor explicite](#explicit-razor-expressions) ou dans un [bloc de code Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Expressions Razor explicites

Les expressions Razor explicites se composent d’un symbole `@` suivi d’un contenu entre parenthèses. Pour afficher l’heure de la semaine précédente, utilisez le balisage Razor suivant :

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Le contenu situé entre les parenthèses `@()` est évalué et affiché dans la sortie.

Les expressions implicites, décrites dans la section précédente, ne doivent généralement pas contenir d’espaces. Dans le code suivant, une semaine n’est pas déduite de l’heure actuelle :

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Le code s’affiche en HTML de la façon suivante :

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Les expressions explicites peuvent servir à concaténer du texte avec un résultat d’expression :

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse e-mail, et `<p>Age@joe.Age</p>` est affiché. Avec une expression explicite, `<p>Age33</p>` est affiché.

Les expressions explicites peuvent être utilisées pour afficher la sortie de méthodes génériques dans les fichiers *.cshtml*. Le balisage suivant montre comment corriger l’erreur affichée précédemment provoquée par les crochets d’un générique C#. Le code est écrit sous forme d’expression explicite :

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Encodage des expressions

Les expressions C# évaluées qui correspondent à une chaîne sont encodées en HTML. Les expressions C# évaluées qui correspondent à `IHtmlContent` sont affichées directement par `IHtmlContent.WriteTo`. Les expressions C# évaluées qui ne correspondent pas à `IHtmlContent` sont converties en chaîne par `ToString` et sont encodées avant d’être affichées.

```cshtml
@("<span>Hello World</span>")
```

Le code s’affiche en HTML de la façon suivante :

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Le code HTML s’affiche dans le navigateur de cette façon :

```
<span>Hello World</span>
```

La sortie `HtmlHelper.Raw` n’est pas encodée, mais elle est affichée sous forme de balisage HTML.

> [!WARNING]
> Utiliser `HtmlHelper.Raw` sur des entrées utilisateur non nettoyées présente un risque pour la sécurité. Les entrées utilisateur peuvent contenir du code malveillant JavaScript ou d’un autre type. Le nettoyage des entrées utilisateur est difficile. C’est pourquoi il est préférable de ne pas utiliser `HtmlHelper.Raw` sur des entrées utilisateur.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Le code s’affiche en HTML de la façon suivante :

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Blocs de code Razor

Les blocs de code Razor commencent par `@` et sont délimités par deux `{}`. Contrairement aux expressions, le code C# figurant dans des blocs de code n’est pas affiché. Les blocs de code et les expressions dans une vue ont la même étendue et sont définis dans l’ordre :

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

Le code s’affiche en HTML de la façon suivante :

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Dans des blocs de code, déclarez des [fonctions locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) avec des balises servant de méthodes de création de modèles :

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

Le code s’affiche en HTML de la façon suivante :

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Transitions implicites

Un bloc de code utilise le langage C# par défaut, mais la page Razor peut le reconvertir en HTML :

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Conversion délimitée explicite

Pour définir une sous-section d’un bloc de code qui doit s’afficher en HTML, placez les caractères à afficher dans la balise Razor `<text>` suivante :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Utilisez cette approche pour afficher du code HTML qui n’est pas entouré d’une balise HTML. Sans la balise HTML ou Razor, une erreur d’exécution Razor se produit.

La balise `<text>` est utile pour contrôler les espaces blancs dans le contenu affiché :

* Seul le contenu situé dans la balise `<text>` est affiché.
* Aucun espace blanc avant ou après la balise `<text>` ne s’affiche dans la sortie HTML.

### <a name="explicit-line-transition"></a>Transition de ligne explicite

Pour afficher le reste d’une ligne entière au format HTML à l’intérieur d’un bloc de code, utilisez la syntaxe `@:` :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sans la balise `@:` dans le code, une erreur d’exécution Razor se produit.

La présence de caractères `@` en trop dans un fichier Razor risque de provoquer des erreurs du compilateur au niveau des instructions suivantes dans le bloc. Ces erreurs du compilateur peuvent être difficiles à comprendre, car elles se produisent en réalité avant les erreurs signalées. Ce type d’erreur est courant après la combinaison de plusieurs expressions implicites ou explicites dans un même bloc de code.

## <a name="control-structures"></a>Structures de contrôle

Les structures de contrôle sont une extension des blocs de code. Toutes les caractéristiques des blocs de code (conversion de balisage, Inline C#) valent aussi pour les structures suivantes :

### <a name="conditionals-if-else-if-else-and-switch"></a>Conditions \@if, else if, else et \@switch

`@if` contrôle l’exécution du code :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` et `else if` ne nécessitent pas le symbole `@` :

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

Le balisage suivant montre comment utiliser une instruction switch :

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

### <a name="looping-for-foreach-while-and-do-while"></a>Boucle \@for, \@foreach, \@while et \@do while

Le rendu HTML peut être réalisé à partir d'instructions de contrôle de boucle. Pour afficher une liste de personnes :

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

Les instructions de boucle suivantes sont prises en charge :

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

### <a name="compound-using"></a>Composé \@using

En C#, une instruction `using` est utilisée pour garantir la dispose d’un objet. Dans Razor, le même mécanisme permet de créer des HTML Helpers avec du contenu supplémentaire. Dans le code suivant, les HTML Helpers affichent une balise `<form>` à l’aide de l’instruction `@using` :

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@try, catch, finally

La gestion des exceptions est similaire à C# :

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@lock

Razor permet de verrouiller les sections critiques par des instructions lock :

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Comments

Razor prend en charge les commentaires HTML et C# :

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Le code s’affiche en HTML de la façon suivante :

```html
<!-- HTML comment -->
```

Le serveur supprime les commentaires Razor avant d’afficher la page web. Razor délimite les commentaires avec `@*  *@`. Le code suivant est commenté pour indiquer au serveur de ne pas afficher le balisage :

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Directives

Les directives Razor sont représentées par des expressions implicites constituées du symbole `@` suivi de mots clés réservés. En règle générale, une directive change la manière dont une vue est analysée ou active une fonctionnalité différente.

Pour mieux comprendre le fonctionnement des directives, vous devez bien comprendre comment Razor génère le code pour une vue.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Le code génère une classe semblable à celle-ci :

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

Plus loin dans cet article, la section [Inspecter la classe C# Razor générée pour une vue](#inspect-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.

### <a name="attribute"></a>\@attribute

La directive `@attribute` permet d’ajouter l’attribut donné à la classe de la page ou de la vue générée. L’exemple suivant ajoute l’attribut `[Authorize]` :

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>code \@

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

Le blob `@code` permet à un [composant Razor](xref:blazor/components) d’ajouter des membres C# (champs, propriétés et méthodes) à un composant :

```cshtml
@code {
    // C# members (fields, properties, and methods)
}
```

Pour les composants Razor, `@code` est un alias [@functions](#functions) et est recommandé sur `@functions`. Plus d’un bloc `@code` est autorisé.

::: moniker-end

### <a name="functions"></a>\@functions

La directive `@functions` permet d’ajouter des membres C# (champs, propriétés et méthodes) à la classe générée :

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

Dans les [composants Razor](xref:blazor/components), utilisez `@code` sur `@functions` pour ajouter des membres C#.

::: moniker-end

Par exemple :

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Le code génère le balisage HTML suivant :

```html
<div>From method: Hello</div>
```

Le code suivant correspond à la classe C# Razor générée :

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Les méthodes `@functions` servent de méthodes de création de modèles lorsqu’elles comprennent des balises :

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

Le code s’affiche en HTML de la façon suivante :

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@implements

La directive `@implements` implémente une interface pour la classe générée.

L’exemple suivant implémente <xref:System.IDisposable?displayProperty=fullName> afin que la méthode <xref:System.IDisposable.Dispose*> puisse être appelée :

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

### <a name="inherits"></a>\@inherits

La directive `@inherits` fournit un contrôle complet de la classe héritée par la vue :

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Le code suivant est un type de page Razor personnalisé :

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

Le `CustomText` s’affiche dans une vue :

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Le code s’affiche en HTML de la façon suivante :

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` et `@inherits` peuvent s’utiliser dans la même vue. `@inherits` peut être dans un fichier *_ViewImports.cshtml* importé par la vue :

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Le code suivant est un exemple de vue fortement typée :

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Si « rick@contoso.com » est passé au modèle, la vue génère le balisage HTML suivant :

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@inject

La directive `@inject` permet à la page Razor d’injecter un service dans une vue à partir du [conteneur de services](xref:fundamentals/dependency-injection). Pour plus d’informations, consultez [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@layout

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

La directive `@layout` spécifie une disposition pour un composant Razor. Les composants de disposition sont utilisés pour éviter la duplication et l’incohérence de code. Pour plus d'informations, consultez <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@model

*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*

La directive `@model` spécifie le type du modèle passé à une vue ou une page :

```cshtml
@model TypeNameOfModel
```

Dans une application ASP.NET Core MVC ou Razor Pages créée avec des comptes d’utilisateur individuels, *Views/Account/Login.cshtml* contient la déclaration de modèle suivante :

```cshtml
@model LoginViewModel
```

La classe générée hérite de `RazorPage<dynamic>` :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor expose une propriété `Model` pour accéder au modèle passé à la vue :

```cshtml
<div>The Login Email: @Model.Email</div>
```

La directive `@model` spécifie le type de la propriété `Model`. La directive spécifie le type `T` dans `RazorPage<T>` pour la classe générée dont dérive la vue. Si la directive `@model` n’est pas spécifiée, la propriété `Model` est de type `dynamic`. Pour plus d’informations, consultez les [modèles fortement typés et le mot clé @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

La directive `@namespace` :

* Définit l’espace de noms de la classe de la page Razor, de la vue MVC ou du composant Razor généré.
* Définit les espaces de noms dérivés racine d’une classe de pages, de vues ou de composants à partir du fichier d’importations le plus proche dans l’arborescence de répertoires, *_ViewImports.cshtml* (vues ou pages) ou *_Imports.razor* (composants Razor).

```cshtml
@namespace Your.Namespace.Here
```

Pour l’exemple de Razor Pages illustré dans le tableau suivant :

* Chaque page importe *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* contient `@namespace Hello.World`.
* Chaque page a `Hello.World` comme racine de son espace de noms.

| Page                                        | Espace de noms                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Les relations précédentes s’appliquent aux fichiers d’importation utilisés avec les vues MVC et les composants Razor.

Lorsque plusieurs fichiers d’importation ont une directive `@namespace`, le fichier le plus proche de la page, de la vue ou du composant dans l’arborescence de répertoires est utilisé pour définir l’espace de noms racine.

Si le dossier *EvenMorePages* dans l’exemple précédent comprend un fichier d’importations avec `@namespace Another.Planet` (ou si le fichier *Pages/MorePages/EvenMorePages/Page.cshtml* contient `@namespace Another.Planet`), le résultat est indiqué dans le tableau suivant.

| Page                                        | Espace de noms               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@page

::: moniker range=">= aspnetcore-3.0"

La directive `@page` a des effets différents selon le type du fichier dans lequel elle apparaît. La directive :

* Dans un fichier *.cshtml*, indique que le fichier est une page Razor. Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes) et <xref:razor-pages/index>.
* Spécifie qu’un composant Razor doit gérer les requêtes directement. Pour plus d'informations, consultez <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La directive `@page` sur la première ligne d’un fichier *.cshtml* indique que le fichier est une page Razor. Pour plus d'informations, consultez <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@section

*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*

La directive `@section` est utilisée conjointement avec des [dispositions MVC et Razor Pages](xref:mvc/views/layout) pour permettre aux vues et aux pages d’afficher le contenu dans différentes parties de la page HTML. Pour plus d'informations, consultez <xref:mvc/views/layout>.

### <a name="using"></a>\@using

La directive `@using` ajoute la directive `using` C# à la vue générée :

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

Dans des [composants Razor](xref:blazor/components), `@using` contrôle également les composants dans la portée.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Attributs de directive

### <a name="attributes"></a>\@attributes

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

`@attributes` permet à un composant de restituer des attributs non déclarés. Pour plus d'informations, consultez <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bind

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

La liaison de données dans des composants s’effectue avec l’attribut `@bind`. Pour plus d'informations, consultez <xref:blazor/components#data-binding>.

### <a name="onevent"></a>\@sur {EVENT}

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

Razor fournit des fonctionnalités de gestion des événements pour les composants. Pour plus d'informations, consultez <xref:blazor/components#event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@sur {EVENT} :p reventDefault

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

Empêche l’action par défaut pour l’événement.

### <a name="oneventstoppropagation"></a>\@sur {EVENT} : stopPropagation

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

Arrête la propagation d’événements pour l’événement.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

L’attribut de directive `@key` amène les composants à comparer l’algorithme afin de garantir la préservation des éléments ou des composants en fonction de la valeur de la clé. Pour plus d'informations, consultez <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

Les références de composants (`@ref`) permettent de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance. Pour plus d'informations, consultez <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Ce scénario s’applique uniquement aux composants Razor (.razor).*

La directive `@typeparam` déclare un paramètre de type générique pour la classe de composant générée. Pour plus d'informations, consultez <xref:blazor/components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Délégués Razor basés sur des modèles

Les modèles Razor vous permettent de définir un extrait de code d’interface utilisateur avec le format suivant :

```cshtml
@<tag>...</tag>
```

L’exemple suivant montre comment spécifier un délégué Razor basé sur un modèle comme <xref:System.Func%602>. Le [type dynamique](/dotnet/csharp/programming-guide/types/using-type-dynamic) est spécifié pour le paramètre de la méthode encapsulée par le délégué. Un [type objet](/dotnet/csharp/language-reference/keywords/object) est spécifié comme valeur de retour du délégué. Le modèle est utilisé avec une <xref:System.Collections.Generic.List%601> de `Pet` qui a une propriété `Name`.

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

Le modèle est rendu avec des éléments `pets` fournis par une instruction `foreach` :

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Sortie rendue :

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Vous pouvez également fournir un modèle Razor inline en tant qu’argument à une méthode. Dans l’exemple suivant, la méthode `Repeat` reçoit un modèle Razor. La méthode utilise le modèle pour produire du contenu HTML avec des répétitions d’éléments fournis à partir d’une liste :

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

En utilisant la liste d’éléments « pets » de l’exemple précédent, la méthode `Repeat` est appelée avec :

* <xref:System.Collections.Generic.List%601> de `Pet`.
* Nombre de fois que chaque élément « pet » doit être répété.
* Modèle inline à utiliser pour les éléments de liste d’une liste non triée.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Sortie rendue :

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

## <a name="tag-helpers"></a>Tag Helpers

*Ce scénario s’applique uniquement aux vues MVC et à Razor Pages (.cshtml).*

Il existe trois directives spécifiques aux [Tag Helpers](xref:mvc/views/tag-helpers/intro).

| Directive | Fonction |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Rend les Tag Helpers disponibles dans une vue. |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Supprime les Tag Helpers précédemment ajoutés à une vue. |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Spécifie un préfixe de balise pour activer la prise en charge des Tag Helpers et rendre leur usage explicite. |

## <a name="razor-reserved-keywords"></a>Mots clés réservés Razor

### <a name="razor-keywords"></a>Mots clés Razor

* page (nécessite ASP.NET Core 2.1 ou ultérieur)
* namespace
* fonctions
* hérite
* modèle
* section
* helper (non pris en charge par ASP.NET Core)

Les mots clés Razor sont précédés d’une séquence d’échappement `@(Razor Keyword)` (par exemple, `@(functions)`).

### <a name="c-razor-keywords"></a>Mots clés Razor C#

* casse
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* try
* catch
* finally
* using
* while

Les mots clés Razor C# doivent être précédés d’une double séquence d’échappement `@(@C# Razor Keyword)` (par exemple, `@(@case)`). La première séquence d’échappement `@` est pour l’analyseur Razor. La seconde séquence d’échappement `@` est pour l’analyseur C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Mots clés réservés non utilisés par Razor

* classe

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Inspecter la classe C# Razor générée pour une vue

::: moniker range=">= aspnetcore-2.1"

Avec le SDK .NET Core 2.1 ou ultérieur, le [SDK Razor](xref:razor-pages/sdk) gère la compilation des fichiers Razor. Quand vous créez un projet, le SDK Razor génère un répertoire *obj/<configuration_build>/<moniker_framework_cible>/Razor* dans la racine du projet. La structure de répertoires dans le répertoire *Razor* reflète la structure de répertoires du projet.

Considérez la structure de répertoires suivante dans un projet Razor Pages ASP.NET Core 2.1 ciblant .NET Core 2.1 :

* **Areas/**
  * **Admin/**
    * **Pages/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Pages/**
  * **Shared/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

La création du projet dans la configuration *Debug* génère le répertoire *obj* suivant :

* **obj/**
  * **Debug/**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Areas/**
          * **Admin/**
            * **Pages/**
              * *Index.g.cshtml.cs*
        * **Pages/**
          * **Shared/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Pour afficher la classe générée pour *Pages/Index.cshtml*, ouvrez *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Ajoutez la classe suivante au projet ASP.NET Core MVC :

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Dans `Startup.ConfigureServices`, remplacez la classe `RazorTemplateEngine` ajoutée par MVC par la classe `CustomTemplateEngine` :

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Définissez un point d’arrêt sur l’instruction `return csharpDocument;` de `CustomTemplateEngine`. Quand l’exécution du programme s’arrête au point d’arrêt, affichez la valeur de `generatedCode`.

![Vue Visualiseur de texte du code généré](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Recherches de vues et respect de la casse

Le moteur de vue Razor effectue des recherches de vues en respectant la casse. Toutefois, la recherche réellement effectuée est déterminée par le système de fichiers sous-jacent :

* Source basé sur un fichier :
  * Sur les systèmes d’exploitation avec des systèmes de fichiers qui ne respectent pas la casse (par exemple, Windows), les recherches de fournisseurs de fichiers physiques ne respectent pas la casse. Par exemple, `return View("Test")` trouve les correspondances */Views/Home/Test.cshtml*, */Views/home/test.cshtml* et toutes les autres variantes de casse.
  * Sur des systèmes de fichiers respectant la casse (par exemple, Linux, OSX, et avec `EmbeddedFileProvider`), les recherches respectent la casse. Par exemple, `return View("Test")` trouve uniquement la correspondance */Views/Home/Test.cshtml*.
* Vues précompilées : Avec ASP.NET Core 2.0 et les versions ultérieures, les recherches de vues précompilées ne respectent pas la casse, quels que soient les systèmes d’exploitation. Le comportement est le même que celui du fournisseur de fichiers physiques sur Windows. Si deux vues précompilées diffèrent seulement par leur casse, le résultat de la recherche est non déterministe.

Les développeurs doivent s’efforcer d’utiliser la même casse pour les noms de fichiers et de répertoires que pour les noms des éléments suivants :

* Zone, contrôleur et action
* Pages Razor

L’utilisation d’une casse identique garantit que les déploiements trouvent toujours les vues associées, indépendamment du système de fichiers sous-jacent.
