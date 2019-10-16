---
title: ASP.NET Core la gestion de l’État éblouissant
author: guardrex
description: Découvrez comment rendre l’état persistant dans les applications serveur éblouissantes.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/state-management
ms.openlocfilehash: 67042fa9b86125fe95d877dbce246abeb6f35dd0
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391277"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core la gestion de l’État éblouissant

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le serveur éblouissant est un Framework d’applications avec état. La plupart du temps, l’application maintient une connexion continue au serveur. L’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*. 

Voici des exemples d’État détenu pour le circuit d’un utilisateur :

* La hiérarchie de l’interface utilisateur du rendu @ no__t-0the des instances de composant et leur sortie de rendu la plus récente.
* Valeurs de tous les champs et propriétés des instances de composant.
* Données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) dont l’étendue correspond au circuit.

> [!NOTE]
> Cet article traite de la persistance de l’État dans les applications serveur éblouissantes. Les applications webassembly éblouissantes peuvent tirer parti de [la persistance de l’État côté client dans le navigateur,](#client-side-in-the-browser) mais elles nécessitent des solutions personnalisées ou des packages tiers au-delà du cadre de cet article.

## <a name="blazor-circuits"></a>Circuits éblouissants

Si un utilisateur subit une perte de connexion réseau temporaire, éblouissant tente de reconnecter l’utilisateur à son circuit d’origine afin qu’il puisse continuer à utiliser l’application. Toutefois, la reconnexion d’un utilisateur à son circuit d’origine dans la mémoire du serveur n’est pas toujours possible :

* Le serveur ne peut pas conserver un circuit déconnecté de façon infinie. Le serveur doit libérer un circuit déconnecté après un délai d’attente ou lorsque le serveur subit une sollicitation de la mémoire.
* Dans les environnements de déploiement multiserveur avec équilibrage de charge, toutes les demandes de traitement de serveur peuvent devenir indisponibles à un moment donné. Les serveurs individuels peuvent échouer ou être automatiquement supprimés lorsqu’il n’est plus nécessaire de gérer le volume global de demandes. Le serveur d’origine n’est peut-être pas disponible lorsque l’utilisateur tente de se reconnecter.
* L’utilisateur peut fermer et rouvrir son navigateur ou recharger la page, ce qui supprime tout État contenu dans la mémoire du navigateur. Par exemple, les valeurs définies par le biais d’appels Interop JavaScript sont perdues.

Lorsqu’un utilisateur ne peut pas être reconnecté à son circuit d’origine, l’utilisateur reçoit un nouveau circuit avec un état vide. Cela équivaut à fermer et à rouvrir une application de bureau.

## <a name="preserve-state-across-circuits"></a>Conserver l’état entre les circuits

Dans certains scénarios, il est souhaitable de conserver l’état entre les circuits. Une application peut conserver des données importantes pour un utilisateur dans les cas suivants :

* Le serveur Web n’est plus disponible.
* Le navigateur de l’utilisateur est obligé de démarrer un nouveau circuit avec un nouveau serveur Web.

En règle générale, la conservation de l’état entre les circuits s’applique aux scénarios où les utilisateurs créent activement des données, et non simplement à la lecture des données qui existent déjà.

Pour conserver l’État au-delà d’un seul circuit, *ne stockez pas simplement les données dans la mémoire du serveur*. L’application doit conserver les données dans un autre emplacement de stockage. La persistance de l’État n’est pas automatique @ no__t-0you doit prendre des mesures lors du développement de l’application pour implémenter la persistance des données avec état.

La persistance des données est généralement requise uniquement pour l’état de valeur élevée que les utilisateurs ont consacrés à la création. Dans les exemples suivants, l’état persistant fait gagner du temps ou contribue à des activités commerciales :

* WebForm à plusieurs étapes &ndash; il est fastidieux pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un processus à plusieurs étapes si leur état est perdu. Un utilisateur perd l’État dans ce scénario s’il quitte le formulaire à étapes et retourne au formulaire par la suite.
* Panier d’achat &ndash; tout composant commercial important d’une application qui représente un chiffre d’affaires potentiel peut être maintenu. Un utilisateur qui perd son état et, par conséquent, son panier, peut acheter moins de produits ou de services lorsqu’ils reviennent sur le site ultérieurement.

En règle générale, il n’est pas nécessaire de conserver un État facile à recréer, tel que le nom d’utilisateur entré dans une boîte de dialogue de connexion qui n’a pas été envoyée.

> [!IMPORTANT]
> Une application peut conserver uniquement l’état de l' *application*. Les interfaces utilisateur ne peuvent pas être rendues persistantes, telles que les instances de composant et leurs arborescences de rendu. Les composants et les arborescences de rendu ne sont généralement pas sérialisables. Pour conserver un aspect similaire à l’état de l’interface utilisateur, tel que les nœuds développés d’un contrôle TreeView, l’application doit disposer d’un code personnalisé pour modéliser le comportement comme étant un état d’application sérialisable.

## <a name="where-to-persist-state"></a>Emplacement de conservation de l’État

Trois emplacements communs existent pour la conservation de l’État dans une application serveur éblouissante. Chaque approche est la mieux adaptée à différents scénarios et présente des inconvénients différents :

* [Côté serveur dans une base de données](#server-side-in-a-database)
* [URL](#url)
* [Côté client dans le navigateur](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Côté serveur dans une base de données

Pour la persistance des données permanente ou pour toutes les données qui doivent s’étendre sur plusieurs utilisateurs ou périphériques, une base de données indépendante côté serveur est certainement le meilleur choix. Les options sont les suivantes :

* Base de données SQL relationnelle
* Magasin clé-valeur
* Magasin d’objets BLOB
* Magasin de tables

Une fois les données enregistrées dans la base de données, un nouveau circuit peut être démarré par un utilisateur à tout moment. Les données de l’utilisateur sont conservées et disponibles dans n’importe quel nouveau circuit.

Pour plus d’informations sur les options de stockage de données Azure, consultez la documentation sur le [stockage Azure](/azure/storage/) et les [bases de données Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Pour les données temporaires représentant l’état de navigation, modélisez les données en tant que partie de l’URL. Voici des exemples d’État modélisé dans l’URL :

* ID d’une entité affichée.
* Numéro de page actuel dans une grille paginée.

Le contenu de la barre d’adresse du navigateur est conservé :

* Si l’utilisateur recharge manuellement la page.
* Si le serveur Web devient indisponible, @ no__t-0the utilisateur est obligé de recharger la page pour se connecter à un autre serveur.

Pour plus d’informations sur la définition de modèles d’URL avec la directive `@page`, consultez <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Côté client dans le navigateur

Pour les données temporaires que l’utilisateur crée activement, un magasin de stockage commun est les collections `localStorage` et `sessionStorage` du navigateur. L’application n’est pas requise pour gérer ou effacer l’État stocké si le circuit est abandonné, ce qui constitue un avantage par rapport au stockage côté serveur.

> [!NOTE]
> « Côté client » dans cette section fait référence aux scénarios côté client dans le navigateur, et non au [modèle d’hébergement de Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly). `localStorage` et `sessionStorage` peuvent être utilisés dans les applications webassembly éblouissantes, mais uniquement en écrivant du code personnalisé ou à l’aide d’un package tiers.

`localStorage` et `sessionStorage` diffèrent comme suit :

* `localStorage` est limité au navigateur de l’utilisateur. Si l’utilisateur recharge la page ou ferme et ouvre à nouveau le navigateur, l’état persiste. Si l’utilisateur ouvre plusieurs onglets de navigateur, l’État est partagé à travers les onglets. Les données sont conservées dans `localStorage` jusqu’à ce qu’elles soient explicitement effacées.
* `sessionStorage` est limité à l’onglet navigateur de l’utilisateur. Si l’utilisateur recharge l’onglet, l’état persiste. Si l’utilisateur ferme l’onglet ou le navigateur, l’État est perdu. Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet possède sa propre version indépendante des données.

En règle générale, `sessionStorage` est plus sûr à utiliser. `sessionStorage` permet d’éviter le risque qu’un utilisateur ouvre plusieurs onglets et rencontre les éléments suivants :

* Bogues dans le stockage d’État sur les onglets.
* Comportement confus quand une tabulation remplace l’état d’autres onglets.

`localStorage` est le meilleur choix si l’application doit conserver l’État dans la fermeture et la réouverture du navigateur.

Avertissements relatifs à l’utilisation du stockage du navigateur :

* À l’instar de l’utilisation d’une base de données côté serveur, le chargement et l’enregistrement des données sont asynchrones.
* Contrairement à une base de données côté serveur, le stockage n’est pas disponible pendant le prérendu, car la page demandée n’existe pas dans le navigateur pendant l’étape de prérendu.
* Le stockage de quelques kilo-octets de données est raisonnable à conserver pour les applications serveur éblouissantes. Au-delà de quelques kilo-octets, vous devez prendre en compte les implications en termes de performances, car les données sont chargées et enregistrées sur le réseau.
* Les utilisateurs peuvent afficher ou altérer les données. La [protection des données](xref:security/data-protection/introduction) ASP.net Core peut atténuer le risque.

## <a name="third-party-browser-storage-solutions"></a>Solutions de stockage de navigateur tiers

Les packages NuGet tiers fournissent des API pour l’utilisation de `localStorage` et `sessionStorage`.

Il est judicieux de choisir un package qui utilise de manière transparente la [protection des données](xref:security/data-protection/introduction)de ASP.net core. ASP.NET Core protection des données chiffre les données stockées et réduit le risque potentiel de falsification des données stockées. Si les données sérialisées JSON sont stockées en texte clair, les utilisateurs peuvent voir les données à l’aide des outils de développement du navigateur et également modifier les données stockées. La sécurisation des données n’est pas toujours un problème, car les données peuvent être de nature insignifiante. Par exemple, la lecture ou la modification de la couleur stockée d’un élément d’interface utilisateur n’est pas un risque de sécurité significatif pour l’utilisateur ou l’organisation. Évitez d’autoriser les utilisateurs à inspecter ou altérer des *données sensibles*.

## <a name="protected-browser-storage-experimental-package"></a>Package d’expérimentation de stockage protégé du navigateur

Par exemple, un package NuGet qui fournit la [protection des données](xref:security/data-protection/introduction) pour `localStorage` et `sessionStorage` est [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` est un package expérimental non pris en charge, inapproprié pour une utilisation en production à l’heure actuelle.

### <a name="installation"></a>Installation

Pour installer le package `Microsoft.AspNetCore.ProtectedBrowserStorage` :

1. Dans le projet d’application de serveur éblouissant, ajoutez une référence de package à [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. Dans le code HTML de niveau supérieur (par exemple, dans le fichier *pages/_Host. cshtml* dans le modèle de projet par défaut), ajoutez la balise `<script>` suivante :

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Dans la méthode `Startup.ConfigureServices`, appelez `AddProtectedBrowserStorage` pour ajouter les services `localStorage` et `sessionStorage` à la collection de services :

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Enregistrer et charger des données dans un composant

Dans tout composant nécessitant le chargement ou l’enregistrement de données dans le stockage du navigateur, utilisez [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) pour injecter une instance de l’un des éléments suivants :

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Le choix dépend du magasin de stockage que vous souhaitez utiliser. Dans l’exemple suivant, `sessionStorage` est utilisé :

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

L’instruction `@using` peut être placée dans un fichier *_Imports. Razor* plutôt que dans le composant. L’utilisation du fichier *_Imports. Razor* rend l’espace de noms disponible pour les plus grands segments de l’application ou de l’application entière.

Pour conserver la valeur `currentCount` dans le composant `Counter` du modèle de projet, modifiez la méthode `IncrementCount` pour utiliser `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Dans les applications plus volumineuses et plus réalistes, le stockage de champs individuels est un scénario peu probable. Les applications sont plus susceptibles de stocker des objets de modèle entiers qui incluent un État complexe. `ProtectedSessionStore` sérialise et désérialise automatiquement les données JSON.

Dans l’exemple de code précédent, les données `currentCount` sont stockées en tant que `sessionStorage['count']` dans le navigateur de l’utilisateur. Les données ne sont pas stockées en texte clair mais sont protégées à l’aide de la [protection des données](xref:security/data-protection/introduction)de ASP.net core. Les données chiffrées peuvent être consultées si `sessionStorage['count']` est évaluée dans la console de développement du navigateur.

Pour récupérer les données `currentCount` si l’utilisateur revient ultérieurement au composant `Counter` (y compris s’il s’agit d’un circuit entièrement nouveau), utilisez `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Si les paramètres du composant incluent l’état de navigation, appelez `ProtectedSessionStore.GetAsync` et assignez le résultat dans `OnParametersSetAsync`, et non `OnInitializedAsync`. `OnInitializedAsync` n’est appelé qu’une seule fois lors de la première instanciation du composant. `OnInitializedAsync` n’est pas rappelé ultérieurement si l’utilisateur accède à une autre URL tout en restant sur la même page.

> [!WARNING]
> Les exemples de cette section ne fonctionnent que si le prérendu n’est pas activé sur le serveur. Quand le prérendu est activé, une erreur est générée de la façon suivante :
>
> > Impossible d’émettre des appels Interop JavaScript pour l’instant. Cela est dû au fait que le composant est en cours de prérendu.
>
> Désactivez le prérendu ou ajoutez du code supplémentaire pour utiliser le prérendu. Pour en savoir plus sur l’écriture de code qui fonctionne avec le prérendu, consultez la section [handle PreRender](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Gérer l’état de chargement

Étant donné que le stockage du navigateur est asynchrone (accessible via une connexion réseau), il y a toujours un certain temps avant que les données soient chargées et disponibles pour une utilisation par un composant. Pour obtenir les meilleurs résultats, affichez un message d’état de chargement pendant le chargement en cours au lieu d’afficher les données vides ou par défaut.

Une approche consiste à déterminer si les données sont `null` (toujours en cours de chargement). Dans le composant `Counter` par défaut, le nombre est conservé dans une `int`. Rendez `currentCount` Nullable en ajoutant un point d’interrogation (`?`) au type (`int`) :

```csharp
private int? currentCount;
```

Au lieu d’afficher sans condition le bouton nombre et **incrément** , choisissez d’afficher ces éléments uniquement si les données sont chargées :

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gérer le prérendu

Lors du prérendu :

* Une connexion interactive au navigateur de l’utilisateur n’existe pas.
* Le navigateur ne dispose pas encore d’une page dans laquelle il peut exécuter du code JavaScript.

`localStorage` ou `sessionStorage` ne sont pas disponibles pendant le prérendu. Si le composant tente d’interagir avec le stockage, une erreur est générée de la façon suivante :

> Impossible d’émettre des appels Interop JavaScript pour l’instant. Cela est dû au fait que le composant est en cours de prérendu.

L’une des méthodes permettant de résoudre l’erreur consiste à désactiver le prérendu. C’est généralement le meilleur choix si l’application utilise beaucoup le stockage basé sur le navigateur. Le prérendu ajoute de la complexité et ne tire pas parti de l’application, car l’application ne peut pas prérestituer de contenu utile tant que `localStorage` ou `sessionStorage` n’est pas disponible.

Pour désactiver le prérendu, ouvrez le fichier *pages/_Host. cshtml* et modifiez l’appel en `Html.RenderComponentAsync<App>(RenderMode.Server)`.

Le prérendu peut être utile pour d’autres pages qui n’utilisent pas `localStorage` ou `sessionStorage`. Pour conserver le prérendu activé, différez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit. Voici un exemple de stockage d’une valeur de compteur :

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factoriser la conservation de l’État à un emplacement commun

Si de nombreux composants reposent sur un stockage basé sur un navigateur, la réimplémentation du code du fournisseur d’état de nombreuses fois crée une duplication du code. Une option pour éviter la duplication de code consiste à créer un *composant parent du fournisseur d’État* qui encapsule la logique du fournisseur d’État. Les composants enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.

Dans l’exemple suivant d’un composant `CounterStateProvider`, les données du compteur sont conservées :

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Le composant `CounterStateProvider` gère la phase de chargement en ne restituant pas son contenu enfant tant que le chargement n’est pas terminé.

Pour utiliser le composant `CounterStateProvider`, encapsulez une instance du composant autour de tout autre composant qui requiert l’accès à l’état du compteur. Pour rendre l’état accessible à tous les composants d’une application, encapsulez le composant `CounterStateProvider` autour du `Router` dans le composant `App` (*app. Razor*) :

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Les composants encapsulés reçoivent et peuvent modifier l’état du compteur persistant. Le composant `Counter` suivant implémente le modèle :

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Le composant précédent n’est pas requis pour interagir avec `ProtectedBrowserStorage`, pas plus qu’il ne gère pas une phase de « chargement ».

Pour traiter le prérendu comme décrit précédemment, `CounterStateProvider` peut être modifié de sorte que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prérendu. Pour plus d’informations, consultez la section relative au [prérendu des handles](#handle-prerendering) .

En général, le modèle de *composant parent du fournisseur d’État* est recommandé :

* Pour utiliser l’État dans de nombreux autres composants.
* S’il n’existe qu’un seul objet d’état de niveau supérieur à conserver.

Pour conserver un grand nombre d’objets d’état différents et utiliser différents sous-ensembles d’objets à des emplacements différents, il est préférable d’éviter de gérer le chargement et l’enregistrement de l’État globalement.
