---
title: Sécuriser les applications ASP.NET Core Blazor Server
author: guardrex
description: Découvrez comment limiter les menaces de sécurité pour les applications Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: 61030f9b5beb849a7cf03571da425e49b144994c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663351"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécuriser les applications de serveur ASP.NET Core éblouissantes

Par [Javier Calvarro Nelson](https://github.com/javiercn)

Les applications serveur éblouissantes adoptent un modèle de traitement des données *avec état* , où le serveur et le client maintiennent une relation à long terme. L’état persistant est géré par un [circuit](xref:blazor/state-management)qui peut s’étendre sur des connexions qui sont également potentiellement durables.

Lorsqu’un utilisateur visite un site de serveur éblouissant, le serveur crée un circuit dans la mémoire du serveur. Le circuit indique au navigateur le contenu à afficher et répond aux événements, par exemple lorsque l’utilisateur sélectionne un bouton dans l’interface utilisateur. Pour effectuer ces actions, un circuit appelle des fonctions JavaScript dans le navigateur de l’utilisateur et les méthodes .NET sur le serveur. Cette interaction JavaScript bidirectionnelle est connue sous le terme d’interopérabilité [JavaScript (JS Interop)](xref:blazor/call-javascript-from-dotnet).

Étant donné que l’interopérabilité de JS a lieu sur Internet et que le client utilise un navigateur distant, les applications de serveur éblouissantes partagent la plupart des problèmes de sécurité des applications Web. Cette rubrique décrit les menaces courantes pour les applications serveur éblouissantes et fournit des conseils sur l’atténuation des menaces axées sur les applications accessibles sur Internet.

Dans les environnements restreints, tels que les réseaux d’entreprise ou les intranets, quelques conseils d’atténuation :

* Ne s’applique pas à l’environnement restreint.
* Ne justifie pas le coût de l’implémentation, car le risque de sécurité est faible dans un environnement limité.

## <a name="resource-exhaustion"></a>Épuisement des ressources

L’épuisement des ressources peut se produire lorsqu’un client interagit avec le serveur et oblige le serveur à consommer trop de ressources. Une consommation excessive des ressources affecte principalement :

* [UC](#cpu)
* [Mémoire](#memory)
* [Connexions client](#client-connections)

Les attaques par déni de service (DoS) cherchent généralement à épuiser les ressources d’une application ou d’un serveur. Toutefois, l’épuisement des ressources n’est pas nécessairement le résultat d’une attaque sur le système. Par exemple, les ressources limitées peuvent être épuisées en raison d’une demande élevée de l’utilisateur. DoS est abordé plus en détail dans la section [attaques par déni de service (dos)](#denial-of-service-dos-attacks) .

Les ressources externes à l’infrastructure éblouissant, telles que les bases de données et les descripteurs de fichiers (utilisées pour lire et écrire des fichiers), peuvent également rencontrer une insuffisance des ressources. Pour plus d’informations, consultez <xref:performance/performance-best-practices>.

### <a name="cpu"></a>UC

L’épuisement du processeur peut se produire lorsqu’un ou plusieurs clients forcent le serveur à effectuer des tâches intensives du processeur.

Par exemple, Imaginez une application de serveur éblouissant qui calcule un *numéro Fibonnacci*. Un numéro Fibonnacci est généré à partir d’une séquence Fibonnacci, où chaque nombre de la séquence est la somme des deux nombres précédents. La quantité de travail nécessaire pour atteindre la réponse dépend de la longueur de la séquence et de la taille de la valeur initiale. Si l’application ne place pas de limites sur la demande d’un client, les calculs gourmands en ressources processeur peuvent dominer le temps de l’UC et réduire les performances des autres tâches. Une consommation excessive des ressources est un problème de sécurité qui a un impact sur la disponibilité.

L’épuisement de l’UC est un problème pour toutes les applications accessibles au public. Dans les applications Web standard, les demandes et les connexions expirent comme protection, mais les applications serveur éblouissantes ne fournissent pas les mêmes protections. Les applications serveur éblouissantes doivent inclure des vérifications et des limites appropriées avant d’effectuer des tâches potentiellement gourmandes en ressources processeur.

### <a name="memory"></a>Mémoire

L’épuisement de la mémoire peut se produire lorsqu’un ou plusieurs clients forcent le serveur à consommer une grande quantité de mémoire.

Par exemple, considérez une application éblouissante côté serveur avec un composant qui accepte et affiche une liste d’éléments. Si l’application éblouissant ne place pas de limites sur le nombre d’éléments autorisés ou le nombre d’éléments rendus au client, le traitement et le rendu gourmands en mémoire peuvent dominer la mémoire du serveur jusqu’au point où les performances du serveur sont affectées. Le serveur peut se bloquer ou ralentir le point d’arrêt.

Tenez compte du scénario suivant pour gérer et afficher une liste d’éléments qui se rapportent à un scénario potentiel d’épuisement de mémoire sur le serveur :

* Les éléments d’une propriété ou d’un champ `List<MyItem>` utilisent la mémoire du serveur. Si l’application permet à la liste des éléments de croître sans limite, il y a un risque de manquer de mémoire sur le serveur. Si la mémoire est insuffisante, la session en cours se termine (incident) et toutes les sessions simultanées de cette instance de serveur reçoivent une exception de mémoire insuffisante. Pour éviter que ce scénario ne se produise, l’application doit utiliser une structure de données qui impose une limite d’éléments pour les utilisateurs simultanés.
* Si un schéma de pagination n’est pas utilisé pour le rendu, le serveur utilise de la mémoire supplémentaire pour les objets qui ne sont pas visibles dans l’interface utilisateur. Sans limite du nombre d’éléments, les demandes de mémoire peuvent épuiser la mémoire disponible du serveur. Pour éviter ce scénario, utilisez l’une des approches suivantes :
  * Utilisez des listes paginées lors du rendu.
  * Affiche uniquement les premiers 100 à 1 000 éléments et oblige l’utilisateur à entrer des critères de recherche pour rechercher des éléments au-delà des éléments affichés.
  * Pour un scénario de rendu plus avancé, implémentez des listes ou des grilles qui prennent en charge *la virtualisation*. À l’aide de la virtualisation, les listes affichent uniquement un sous-ensemble d’éléments actuellement visibles par l’utilisateur. Lorsque l’utilisateur interagit avec la barre de défilement dans l’interface utilisateur, le composant affiche uniquement les éléments requis pour l’affichage. Les éléments qui ne sont pas actuellement requis pour l’affichage peuvent être stockés dans le stockage secondaire, qui est l’approche idéale. Les éléments non affichés peuvent également être conservés en mémoire, ce qui est moins idéal.

Les applications de serveur éblouissant offrent un modèle de programmation similaire à d’autres infrastructures d’interface utilisateur pour les applications avec état, telles que WPF, Windows Forms ou éblouissant webassembly. La principale différence réside dans le fait que dans plusieurs infrastructures d’interface utilisateur, la mémoire consommée par l’application appartient au client et affecte uniquement ce client individuel. Par exemple, une application de webassembly éblouissant s’exécute entièrement sur le client et utilise uniquement les ressources de mémoire du client. Dans le scénario de serveur éblouissant, la mémoire consommée par l’application appartient au serveur et est partagée entre les clients sur l’instance de serveur.

Les demandes de mémoire côté serveur sont prises en compte pour toutes les applications de serveur éblouissantes. Toutefois, la plupart des applications Web sont sans État et la mémoire utilisée lors du traitement d’une demande est libérée lorsque la réponse est retournée. Comme recommandation générale, n’autorisez pas les clients à allouer une quantité de mémoire non liée comme dans toute autre application côté serveur qui conserve les connexions clientes. La mémoire consommée par une application de serveur éblouissante persiste pendant une durée plus longue qu’une seule requête.

> [!NOTE]
> Pendant le développement, un profileur peut être utilisé ou une trace capturée pour évaluer les demandes de mémoire des clients. Un profileur ou une trace ne capture pas la mémoire allouée à un client spécifique. Pour capturer l’utilisation de la mémoire d’un client spécifique lors du développement, capturez un vidage et examinez la demande de mémoire de tous les objets enracinés sur le circuit d’un utilisateur.

### <a name="client-connections"></a>Connexions clientes

L’épuisement de la connexion peut se produire lorsqu’un ou plusieurs clients ouvrent un nombre trop important de connexions simultanées au serveur, empêchant ainsi d’autres clients d’établir de nouvelles connexions.

Les clients éblouissant établissent une seule connexion par session et maintiennent la connexion ouverte tant que la fenêtre du navigateur est ouverte. Les demandes sur le serveur de gestion de toutes les connexions ne sont pas spécifiques aux applications éblouissantes. Étant donné la nature persistante des connexions et la nature avec état des applications serveur éblouissantes, l’épuisement des connexions est un risque plus élevé pour la disponibilité de l’application.

Par défaut, le nombre de connexions par utilisateur n’est pas limité pour une application de serveur éblouissante. Si l’application requiert une limite de connexion, effectuez une ou plusieurs des approches suivantes :

* Exiger une authentification, ce qui limite naturellement la capacité des utilisateurs non autorisés à se connecter à l’application. Pour que ce scénario soit efficace, les utilisateurs doivent être empêchés d’approvisionner de nouveaux utilisateurs.
* Limitez le nombre de connexions par utilisateur. La limitation des connexions peut être accomplie à l’aide des approches suivantes. Veillez à autoriser les utilisateurs légitimes à accéder à l’application (par exemple, lorsqu’une limite de connexion est établie en fonction de l’adresse IP du client).
  * Au niveau de l’application :
    * Extensibilité du routage du point de terminaison.
    * Exiger une authentification pour se connecter à l’application et suivre les sessions actives par utilisateur.
    * Rejeter les nouvelles sessions après avoir atteint une limite.
    * Connexions proxy WebSocket à une application via l’utilisation d’un proxy, tel que le [service Azure signalr](/azure/azure-signalr/signalr-overview) , qui multiplexe les connexions entre les clients et une application. Cela permet à une application avec une capacité de connexion supérieure à celle qu’un client unique peut établir, ce qui empêche un client d’épuiser les connexions au serveur.
  * Au niveau du serveur : utilisez un proxy/une passerelle devant l’application. Par exemple, la [porte frontale Azure](/azure/frontdoor/front-door-overview) vous permet de définir, gérer et surveiller le routage global du trafic Web vers une application.

## <a name="denial-of-service-dos-attacks"></a>Attaques par déni de service (DoS)

Les attaques par déni de service (DoS) impliquent un client qui oblige le serveur à épuiser une ou plusieurs de ses ressources, ce qui rend l’application indisponible. Les applications de serveur éblouissantes incluent certaines limites par défaut et s’appuient sur d’autres ASP.NET Core et les limites Signalr pour se protéger contre les attaques par déni de session :

| Limite de l’application du serveur éblouissant                            | Description | Default |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Nombre maximal de circuits déconnectés qu’un serveur donné détient en mémoire à la fois. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Durée maximale pendant laquelle un circuit déconnecté est maintenu en mémoire avant d’être détruit. | 3 minutes |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Durée maximale pendant laquelle le serveur attend avant d’expirer un appel de fonction JavaScript asynchrone. | 1 minute |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Nombre maximal de lots de rendu sans accusé de réception le serveur conserve en mémoire par circuit à un moment donné pour prendre en charge une reconnexion fiable. Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots aient été reconnus par le client. | 10 |


| Signalr et limite de ASP.NET Core             | Description | Default |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Taille de message pour un message individuel. | 32 Ko |

## <a name="interactions-with-the-browser-client"></a>Interactions avec le navigateur (client)

Un client interagit avec le serveur par le biais de la distribution d’événements d’interopérabilité JS et l’exécution du rendu. La communication d’interopérabilité de JS s’effectue dans les deux sens entre JavaScript et .NET :

* Les événements du navigateur sont distribués du client au serveur de façon asynchrone.
* Le serveur répond de manière asynchrone à un rendu de l’interface utilisateur en fonction des besoins.

### <a name="javascript-functions-invoked-from-net"></a>Fonctions JavaScript appelées à partir de .NET

Pour les appels de méthodes .NET à JavaScript :

* Tous les appels ont un délai d’expiration configurable après lequel ils échouent, en retournant un <xref:System.OperationCanceledException> à l’appelant.
  * Il y a un délai d’attente par défaut pour les appels (`CircuitOptions.JSInteropDefaultCallTimeout`) d’une minute. Pour configurer cette limite, consultez <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Un jeton d’annulation peut être fourni pour contrôler l’annulation pour chaque appel. S’appuyer sur le délai d’attente de l’appel par défaut lorsque cela est possible et lié au temps à un appel au client si un jeton d’annulation est fourni.
* Le résultat d’un appel JavaScript ne peut pas être approuvé. Le Blazor client d’application s’exécutant dans le navigateur recherche la fonction JavaScript à appeler. La fonction est appelée, et le résultat ou une erreur est généré. Un client malveillant peut tenter d’effectuer les opérations suivantes :
  * Entraîner un problème dans l’application en renvoyant une erreur à partir de la fonction JavaScript.
  * Induire un comportement inattendu sur le serveur en renvoyant un résultat inattendu de la fonction JavaScript.

Prenez les précautions suivantes pour vous protéger contre les scénarios précédents :

* Encapsulez les appels d’interopérabilité JS dans des instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pour tenir compte des erreurs qui peuvent se produire pendant les appels. Pour plus d’informations, consultez <xref:blazor/handle-errors#javascript-interop>.
* Validez les données retournées par les appels d’interopérabilité JS, y compris les messages d’erreur, avant d’entreprendre une action.

### <a name="net-methods-invoked-from-the-browser"></a>Méthodes .NET appelées à partir du navigateur

N’approuvez pas les appels de JavaScript aux méthodes .NET. Quand une méthode .NET est exposée à JavaScript, réfléchissez à la façon dont la méthode .NET est appelée :

* Traitez toute méthode .NET exposée à JavaScript comme vous le feriez pour un point de terminaison public de l’application.
  * Valider l’entrée.
    * Vérifiez que les valeurs sont comprises dans les plages attendues.
    * Assurez-vous que l’utilisateur a l’autorisation d’effectuer l’action demandée.
  * N’allouez pas une quantité excessive de ressources dans le cadre de l’appel de la méthode .NET. Par exemple, effectuez des vérifications et limitez l’utilisation de l’UC et de la mémoire.
  * Prenez en compte la possibilité d’exposer des méthodes statiques et d’instance à des clients JavaScript. Évitez de partager l’état entre les sessions, sauf si la conception appelle l’état de partage avec les contraintes appropriées.
    * Pour les méthodes d’instance exposées via des objets `DotNetReference` créés à l’origine par le biais de l’injection de dépendances, les objets doivent être inscrits en tant qu’objets délimités. Cela s’applique à tout service d’injection de services utilisé par l’application Blazor Server.
    * Pour les méthodes statiques, évitez d’établir un État qui ne peut pas être étendu au client, sauf si l’application partage explicitement l’État par conception sur tous les utilisateurs d’une instance de serveur.
  * Évitez de passer des données fournies par l’utilisateur dans des paramètres à des appels JavaScript. Si le passage de données dans des paramètres est absolument requis, assurez-vous que le code JavaScript gère le passage des données sans introduire de vulnérabilités [de script entre sites (XSS)](#cross-site-scripting-xss) . Par exemple, n’écrivez pas les données fournies par l’utilisateur dans le Document Object Model (DOM) en définissant la propriété `innerHTML` d’un élément. Envisagez d’utiliser la [stratégie de sécurité de contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour désactiver les `eval` et d’autres primitives JavaScript non sûres.
* Évitez d’implémenter la distribution personnalisée des appels .NET en plus de l’implémentation de la distribution du Framework. L’exposition de méthodes .NET au navigateur est un scénario avancé, qui n’est pas recommandé pour le développement Blazor général.

### <a name="events"></a>Événements

Les événements fournissent un point d’entrée à une application Blazor Server. Les mêmes règles de protection des points de terminaison dans les applications Web s’appliquent à la gestion des événements dans les applications Blazor Server. Un client malveillant peut envoyer toutes les données qu’il souhaite envoyer en tant que charge utile d’un événement.

Par exemple :

* Un événement de modification pour un `<select>` peut envoyer une valeur qui ne figure pas dans les options que l’application a présentées au client.
* Une `<input>` peut envoyer des données texte au serveur, en ignorant la validation côté client.

L’application doit valider les données de tout événement géré par l’application. Les composants Blazor Framework [Forms](xref:blazor/forms-validation) effectuent des validations de base. Si l’application utilise des composants de formulaires personnalisés, du code personnalisé doit être écrit pour valider les données d’événement en fonction des besoins.

Blazor les événements serveur sont asynchrones, plusieurs événements peuvent être envoyés au serveur avant que l’application ait le temps de réagir en générant un nouveau rendu. Cela a des implications en termes de sécurité à prendre en compte. La limitation des actions du client dans l’application doit être effectuée à l’intérieur des gestionnaires d’événements et ne dépend pas de l’état d’affichage affiché en cours.

Imaginez un composant de compteur qui doit permettre à un utilisateur d’incrémenter un compteur au maximum trois fois. Le bouton permettant d’incrémenter le compteur dépend de la valeur de `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Un client peut distribuer un ou plusieurs événements d’incréments avant que l’infrastructure génère un nouveau rendu de ce composant. Le résultat est que l' `count` peut être incrémenté *plus de trois fois* par l’utilisateur, car le bouton n’est pas supprimé rapidement par l’interface utilisateur. La méthode correcte pour atteindre la limite de trois `count` incréments est indiquée dans l’exemple suivant :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

En ajoutant la vérification `if (count < 3) { ... }` à l’intérieur du gestionnaire, la décision d’incrémenter `count` est basée sur l’état actuel de l’application. La décision n’est pas basée sur l’état de l’interface utilisateur tel qu’il était dans l’exemple précédent, qui peut être temporairement périmé.

### <a name="guard-against-multiple-dispatches"></a>Protégez-vous contre plusieurs distributions

Si un rappel d’événement appelle une opération de longue durée de manière asynchrone, comme l’extraction de données à partir d’un service externe ou d’une base de données, envisagez d’utiliser une protection. La protection peut empêcher l’utilisateur de faire passer plusieurs opérations en file d’attente pendant que l’opération est en cours avec des commentaires visuels. Le code de composant suivant définit `isLoading` à `true` lorsque `GetForecastAsync` obtient des données du serveur. Si `isLoading` est `true`, le bouton est désactivé dans l’interface utilisateur :

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Le modèle de protection illustré dans l’exemple précédent fonctionne si l’opération d’arrière-plan est exécutée de façon asynchrone avec le modèle de `await` -`async`.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Annuler tôt et éviter d’utiliser-after-dispose

En plus d’utiliser une protection comme décrit dans la section [protection contre plusieurs distributions](#guard-against-multiple-dispatches) , envisagez d’utiliser une <xref:System.Threading.CancellationToken> pour annuler les opérations de longue durée lorsque le composant est supprimé. Cette approche présente l’avantage supplémentaire d’éviter l' *utilisation de-after-dispose dans les* composants :

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        CancellationTokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Évitez les événements qui génèrent de grandes quantités de données

Certains événements DOM, tels que `oninput` ou `onscroll`, peuvent produire une grande quantité de données. Évitez d’utiliser ces événements dans les applications Blazor Server.

## <a name="additional-security-guidance"></a>Aide supplémentaire sur la sécurité

Les conseils pour la sécurisation des applications ASP.NET Core s’appliquent aux applications Blazor Server et sont traités dans les sections suivantes :

* [Journalisation et données sensibles](#logging-and-sensitive-data)
* [Protéger les informations en transit avec HTTPs](#protect-information-in-transit-with-https)
* [Scripts inter-sites (XSS)](#cross-site-scripting-xss))
* [Protection Cross-Origin](#cross-origin-protection)
* [Prise de la clic](#click-jacking)
* [Ouvrir les redirections](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Journalisation et données sensibles

Les interactions d’interopérabilité JS entre le client et le serveur sont enregistrées dans les journaux du serveur avec <xref:Microsoft.Extensions.Logging.ILogger> instances. Blazor évite la journalisation des informations sensibles, telles que les événements réels ou les entrées et sorties d’interopérabilité JS.

Lorsqu’une erreur se produit sur le serveur, le Framework avertit le client et ferme la session. Par défaut, le client reçoit un message d’erreur générique qui peut être affiché dans les outils de développement du navigateur.

L’erreur côté client n’inclut pas la pile des appels et ne fournit pas de détails sur la cause de l’erreur, mais les journaux du serveur contiennent ces informations. À des fins de développement, les informations d’erreur sensibles peuvent être mises à la disposition du client en activant les erreurs détaillées.

Activer les erreurs détaillées avec :

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors` clé de configuration. Par exemple, affectez à la variable d’environnement `ASPNETCORE_DETAILEDERRORS` la valeur `true`.

> [!WARNING]
> L’exposition des informations sur les erreurs aux clients sur Internet est un risque de sécurité qui doit toujours être évité.

### <a name="protect-information-in-transit-with-https"></a>Protéger les informations en transit avec HTTPs

Blazor Server utilise SignalR pour la communication entre le client et le serveur. Blazor serveur utilise normalement le transport que SignalR négocie, qui est généralement WebSocket.

Blazor Server ne garantit pas l’intégrité et la confidentialité des données envoyées entre le serveur et le client. Utilisez toujours le protocole HTTPs.

### <a name="cross-site-scripting-xss"></a>Scripts inter-sites (XSS)

Les scripts entre sites (XSS) permettent à une partie non autorisée d’exécuter une logique arbitraire dans le contexte du navigateur. Une application compromise peut potentiellement exécuter du code arbitraire sur le client. La vulnérabilité peut être utilisée pour exécuter potentiellement des actions malveillantes sur le serveur :

* Distribuer des événements factices/non valides au serveur.
* Réussites d’échec de distribution/rendu non valide.
* Évitez de distribuer les saisies semi-automatiques de rendu.
* Dispatch des appels d’interopérabilité de JavaScript à .NET.
* Modifiez la réponse des appels d’interopérabilité de .NET à JavaScript.
* Évitez de distribuer les résultats de l’interopérabilité de .NET vers JS.

Le Blazor Server Framework prend des mesures pour se protéger contre certaines des menaces précédentes :

* Arrête la génération de nouvelles mises à jour de l’interface utilisateur si le client n’accuse pas réception des lots de rendu. Configuré avec `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.
* Expire un appel de .NET à JavaScript après une minute sans recevoir de réponse du client. Configuré avec `CircuitOptions.JSInteropDefaultCallTimeout`.
* Effectue une validation de base sur toutes les entrées provenant du navigateur pendant l’interopérabilité de JS :
  * Les références .NET sont valides et du type attendu par la méthode .NET.
  * Les données ne sont pas incorrectes.
  * Le nombre correct d’arguments pour la méthode est présent dans la charge utile.
  * Les arguments ou le résultat peuvent être désérialisés correctement avant l’appel de la méthode.
* Effectue une validation de base dans toutes les entrées provenant du navigateur à partir d’événements distribués :
  * Le type de l’événement est valide.
  * Les données de l’événement peuvent être désérialisées.
  * Un gestionnaire d’événements est associé à l’événement.

Outre les protections implémentées par le Framework, l’application doit être codée par le développeur pour se protéger contre les menaces et prendre les mesures appropriées :

* Valide toujours les données lors de la gestion des événements.
* Prenez les mesures nécessaires lors de la réception de données non valides :
  * Ignorez les données et retournez. Cela permet à l’application de continuer à traiter les demandes.
  * Si l’application détermine que l’entrée est illégitime et ne peut pas être produite par un client légitime, levez une exception. La levée d’une exception interrompt le circuit et met fin à la session.
* N’approuvez pas le message d’erreur fourni par les saisies semi-automatiques de lot incluses dans les journaux. L’erreur est fournie par le client et ne peut généralement pas être approuvée, car le client risque d’être compromis.
* N’approuvez pas l’entrée dans les appels d’interopérabilité JS dans les deux sens entre les méthodes JavaScript et .NET.
* L’application est chargée de valider le fait que le contenu des arguments et les résultats sont valides, même si les arguments ou les résultats sont correctement désérialisés.

Pour qu’une vulnérabilité XSS existe, l’application doit incorporer une entrée d’utilisateur dans la page rendue. Blazor composants serveur exécutent une étape de compilation dans laquelle le balisage d’un fichier *. Razor* est transformé C# en logique procédurale. Au moment de l' C# exécution, la logique génère une *arborescence de rendu* décrivant les éléments, le texte et les composants enfants. Elle est appliquée au DOM du navigateur via une séquence d’instructions JavaScript (ou est sérialisée en HTML dans le cas du prérendu) :

* L’entrée utilisateur rendue par syntaxe Razor normale (par exemple, `@someStringValue`) n’expose pas de vulnérabilité XSS car le syntaxe Razor est ajouté au DOM via des commandes qui peuvent uniquement écrire du texte. Même si la valeur comprend le balisage HTML, la valeur est affichée sous forme de texte statique. Lors du prérendu, la sortie est codée au format HTML, ce qui affiche également le contenu sous forme de texte statique.
* Les balises de script ne sont pas autorisées et ne doivent pas être incluses dans l’arborescence de rendu des composants de l’application. Si une balise de script est incluse dans le balisage d’un composant, une erreur de compilation est générée.
* Les auteurs de composants peuvent créer C# des composants dans sans utiliser Razor. L’auteur du composant est chargé d’utiliser les API appropriées lors de l’émission de la sortie. Par exemple, utilisez `builder.AddContent(0, someUserSuppliedString)` et *non* `builder.AddMarkupContent(0, someUserSuppliedString)`, car ces derniers peuvent créer une vulnérabilité XSS.

Dans le cadre de la protection contre les attaques XSS, envisagez d’implémenter des atténuations XSS, telles que la [stratégie de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Pour plus d’informations, consultez <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Protection Cross-Origin

Les attaques Cross-Origin impliquent un client à partir d’une origine différente qui effectue une action sur le serveur. L’action malveillante est généralement une requête d’extraction ou une publication de formulaire (falsification de requête intersite, CSRF), mais l’ouverture d’un WebSocket malveillant est également possible. Blazor applications serveur offrent [les mêmes garanties que toute autre application SignalR utilisant l’offre de protocole de concentrateur](xref:signalr/security):

* Il est possible d’accéder à des applications Blazor Server entre origines, sauf si des mesures supplémentaires sont prises pour l’empêcher. Pour désactiver l’accès Cross-Origin, désactivez CORS dans le point de terminaison en ajoutant l’intergiciel (middleware) CORS au pipeline et en ajoutant le `DisableCorsAttribute` aux métadonnées du point de terminaison Blazor ou limitez le jeu d’origines autorisées en [configurant SignalR pour le partage des ressources Cross-Origin](xref:signalr/security#cross-origin-resource-sharing).
* Si CORS est activé, des étapes supplémentaires peuvent être nécessaires pour protéger l’application en fonction de la configuration CORS. Si CORS est activé globalement, CORS peut être désactivé pour le Hub Blazor Server en ajoutant les métadonnées `DisableCorsAttribute` aux métadonnées du point de terminaison après avoir appelé `hub.MapBlazorHub()`.

Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Prise de la clic

La prise de vue implique le rendu d’un site en tant que `<iframe>` à l’intérieur d’un site à partir d’une origine différente afin d’inciter l’utilisateur à effectuer des actions sur le site en cas d’attaque.

Pour protéger une application contre le rendu à l’intérieur d’un `<iframe>`, utilisez la [stratégie de sécurité de contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) et l’en-tête `X-Frame-Options`. Pour plus d’informations, consultez [MDN Web docs : X-Frame-options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Ouvrir les redirections

Lors du démarrage d’une session d’application Blazor Server, le serveur effectue une validation de base des URL envoyées dans le cadre du démarrage de la session. L’infrastructure vérifie que l’URL de base est un parent de l’URL actuelle avant d’établir le circuit. Aucune vérification supplémentaire n’est effectuée par l’infrastructure.

Lorsqu’un utilisateur sélectionne un lien sur le client, l’URL du lien est envoyée au serveur, qui détermine l’action à entreprendre. Par exemple, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’accéder au nouvel emplacement.

Les composants peuvent également déclencher des requêtes de navigation par programmation par le biais de l’utilisation de `NavigationManager`. Dans de tels scénarios, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’accéder au nouvel emplacement.

Les composants doivent :

* Évitez d’utiliser l’entrée utilisateur dans le cadre des arguments de l’appel de navigation.
* Validez les arguments pour vous assurer que l’application peut autoriser la cible.

Dans le cas contraire, un utilisateur malveillant peut forcer le navigateur à accéder à un site Contrôlé par un attaquant. Dans ce scénario, l’attaquant détoure l’application en utilisant une entrée utilisateur dans le cadre de l’appel de la méthode `NavigationManager.Navigate`.

Ce Conseil s’applique également lors du rendu des liens dans le cadre de l’application :

* Si possible, utilisez des liens relatifs.
* Vérifiez que les destinations des liens absolus sont valides avant de les inclure dans une page.

Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Pour obtenir des conseils sur l’authentification et l’autorisation, consultez <xref:security/blazor/index>.

## <a name="security-checklist"></a>Liste de contrôle de sécurité

La liste suivante de considérations sur la sécurité n’est pas exhaustive :

* Validez les arguments des événements.
* Validez les entrées et les résultats des appels d’interopérabilité JS.
* Évitez d’utiliser (ou de valider au préalable) une entrée utilisateur pour les appels d’interopérabilité de .NET vers JS.
* Empêche le client d’allouer une quantité de mémoire non liée.
  * Données dans le composant.
  * `DotNetObject` références retournées au client.
* Protégez-vous contre plusieurs distributions.
* Annule les opérations de longue durée lorsque le composant est supprimé.
* Évitez les événements qui génèrent de grandes quantités de données.
* Évitez d’utiliser les entrées utilisateur dans le cadre des appels à `NavigationManager.Navigate` et de valider les entrées d’utilisateur pour les URL par rapport à un ensemble d’origines autorisées en premier, si ce n’est pas inévitable.
* Ne prenez pas des décisions d’autorisation en fonction de l’état de l’interface utilisateur, mais uniquement de l’état du composant.
* Envisagez d’utiliser la [stratégie de sécurité de contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour vous protéger contre les attaques XSS.
* Envisagez d’utiliser CSP et [X-Frame-options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) pour vous protéger contre la prise de contrôle de clic.
* Vérifiez que les paramètres CORS sont appropriés lors de l’activation de CORS ou de la désactivation explicite de CORS pour les applications Blazor.
* Testez pour vous assurer que les limites côté serveur de l’application Blazor fournissent une expérience utilisateur acceptable sans niveaux de risque inacceptable.
