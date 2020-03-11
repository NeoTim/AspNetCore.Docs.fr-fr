---
title: Scénarios avancés de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les scénarios avancés dans Blazor, y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659452"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Scénarios avancés ASP.NET Core éblouissants

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Gestionnaire de circuit du serveur éblouissant

Le serveur éblouissant permet au code de définir un *Gestionnaire de circuit*qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur. Un gestionnaire de circuit est implémenté en dérivant de `CircuitHandler` et en inscrivant la classe dans le conteneur de services de l’application. L’exemple suivant d’un gestionnaire de circuit effectue le suivi des connexions signaler ouvertes :

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Les gestionnaires de circuits sont inscrits à l’aide de DI. Les instances délimitées sont créées par instance d’un circuit. À l’aide de la `TrackingCircuitHandler` dans l’exemple précédent, un service Singleton est créé car l’état de tous les circuits doit être suivi :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Si les méthodes d’un gestionnaire de circuit personnalisé lèvent une exception non gérée, l’exception est irrémédiable au circuit du serveur éblouissant. Pour tolérer des exceptions dans le code d’un gestionnaire ou les méthodes appelées, encapsulez le code dans une ou plusieurs instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la gestion des erreurs et la journalisation.

Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que l’infrastructure nettoie l’état du circuit, le Framework supprime l’étendue de l’injection de port. La suppression de l’étendue supprime tous les services d’étendue de circuit qui implémentent <xref:System.IDisposable?displayProperty=fullName>. Si un service d’injection de services lève une exception non gérée pendant la suppression, le Framework journalise l’exception.

## <a name="manual-rendertreebuilder-logic"></a>Logique RenderTreeBuilder manuelle

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fournit des méthodes pour la manipulation des composants et des éléments, notamment la C# génération manuelle de composants dans le code.

> [!NOTE]
> L’utilisation de `RenderTreeBuilder` pour créer des composants est un scénario avancé. Un composant mal formé (par exemple, une balise de balisage non fermée) peut entraîner un comportement indéfini.

Prenons le composant `PetDetails` suivant, qui peut être intégré manuellement à un autre composant :

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Dans l’exemple suivant, la boucle de la méthode `CreateComponent` génère trois composants `PetDetails`. Lors de l’appel de méthodes `RenderTreeBuilder` pour créer les composants (`OpenComponent` et `AddAttribute`), les numéros de séquence sont des numéros de ligne de code source. L’algorithme de différence éblouissant s’appuie sur les numéros de séquence correspondant à des lignes de code distinctes, et non sur des appels d’appel distincts. Lors de la création d’un composant avec des méthodes `RenderTreeBuilder`, coder en dur les arguments pour les numéros de séquence. **L’utilisation d’un calcul ou d’un compteur pour générer le numéro de séquence peut entraîner des performances médiocres.** Pour plus d’informations, consultez la section [numéros de séquence liés à des numéros de ligne de code et non à un ordre d’exécution](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

composant `BuiltContent` :

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Les types dans `Microsoft.AspNetCore.Components.RenderTree` permettent le traitement des *résultats* des opérations de rendu. Il s’agit des détails internes de l’implémentation du Framework éblouissant. Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions ultérieures.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Les numéros de séquence sont liés aux numéros de ligne de code et non à l’ordre d’exécution

Les fichiers de composants Razor ( *. Razor*) sont toujours compilés. La compilation est un avantage potentiel de l’interprétation du code, car l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.

Un exemple clé de ces améliorations concerne les *numéros de séquence*. Les numéros de séquence indiquent au runtime que les sorties proviennent de lignes de code distinctes et ordonnées. Le runtime utilise ces informations pour générer des différences d’arborescence efficaces en temps linéaire, ce qui est beaucoup plus rapide qu’un algorithme de comparaison d’arborescence générale.

Considérez le fichier de composant Razor ( *. Razor*) suivant :

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Le code précédent se compile comme suit :

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Lorsque le code s’exécute pour la première fois, si `someFlag` est `true`, le générateur reçoit :

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Imaginez que `someFlag` devient `false`et que le balisage est de nouveau restitué. Cette fois-ci, le générateur reçoit :

| Séquence | Type       | Données   |
| :------: | ---------- | :----: |
| 1        | Nœud de texte  | Seconde |

Quand le runtime effectue une comparaison, il constate que l’élément au `0` de la séquence a été supprimé, donc il génère le *script d’édition*trivial suivant :

* Supprimez le premier nœud de texte.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Le problème de la génération par programmation des numéros de séquence

Imaginez plutôt que vous avez écrit la logique de générateur d’arborescence de rendu suivante :

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

La première sortie est désormais :

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Ce résultat est identique au cas précédent, donc aucun problème négatif n’existe. `someFlag` est `false` sur le deuxième rendu et la sortie est la suivante :

| Séquence | Type      | Données   |
| :------: | --------- | ------ |
| 0        | Nœud de texte | Seconde |

Cette fois-ci, l’algorithme diff constate que *deux* modifications ont été apportées, et l’algorithme génère le script Edit suivant :

* Remplacez la valeur du premier nœud de texte par `Second`.
* Supprimez le deuxième nœud de texte.

La génération des numéros de séquence a perdu toutes les informations utiles sur l’emplacement où les `if/else` branches et les boucles étaient présentes dans le code d’origine. Il en résulte une comparaison de **deux fois plus longtemps** qu’auparavant.

Il s’agit d’un exemple trivial. Dans des cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec des boucles, le coût des performances est généralement plus élevé. Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou supprimés, l’algorithme diff doit être recurse profondément dans les arborescences de rendu. Cela entraîne généralement la création de scripts de modification plus longs, car l’algorithme diff est mal informé de la relation entre les anciennes et les nouvelles structures.

### <a name="guidance-and-conclusions"></a>Conseils et conclusions

* Les performances de l’application sont affectées si les numéros séquentiels sont générés dynamiquement.
* L’infrastructure ne peut pas créer ses propres numéros de séquence automatiquement au moment de l’exécution, car les informations nécessaires n’existent pas, sauf si elles sont capturées au moment de la compilation.
* N’écrivez pas de longs blocs de `RenderTreeBuilder` logique implémentée manuellement. Préférer les fichiers *. Razor* et permettre au compilateur de gérer les numéros de séquence. Si vous ne parvenez pas à éviter une logique de `RenderTreeBuilder` manuelle, fractionnez les blocs de code longs en éléments plus petits encapsulés dans `OpenRegion`/`CloseRegion` appels. Chaque région a son propre espace distinct pour les numéros de séquence, ce qui vous permet de redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.
* Si les numéros de séquence sont codés en dur, l’algorithme diff exige uniquement que les numéros de séquence augmentent dans la valeur. La valeur initiale et les écarts ne sont pas pertinents. Une option légitime consiste à utiliser le numéro de ligne de code comme numéro de séquence, ou à commencer à partir de zéro et à augmenter par des ou des centaines (ou un intervalle de préférence). 
* Blazor utilise des numéros de séquence, tandis que d’autres infrastructures d’interface utilisateur de comparaison d’arborescence ne les utilisent pas. La comparaison est beaucoup plus rapide lorsque les numéros de séquence sont utilisés et Blazor présente l’avantage d’une étape de compilation qui traite automatiquement les numéros séquentiels pour les développeurs qui créent des fichiers *. Razor* .

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Effectuer des transferts de données volumineux dans des applications Blazor Server

Dans certains scénarios, de grandes quantités de données doivent être transférées entre JavaScript et Blazor. En général, les transferts de données volumineux se produisent dans les cas suivants :

* Les API du système de fichiers du navigateur sont utilisées pour charger ou télécharger un fichier.
* L’interopérabilité avec une bibliothèque tierce est nécessaire.

Dans Blazor Server, une limitation est en place pour empêcher le passage de messages volumineux uniques susceptibles d’entraîner des problèmes de performances.

Tenez compte des conseils suivants lors du développement de code qui transfère des données entre JavaScript et Blazor:

* Découpez les données en éléments plus petits et envoyez les segments de données de façon séquentielle jusqu’à ce que toutes les données soient reçues par le serveur.
* N’allouez pas d’objets volumineux C# en JavaScript et dans du code.
* Ne bloquez pas le thread d’interface utilisateur principal pendant de longues périodes lors de l’envoi ou de la réception de données.
* Libérez la mémoire consommée lorsque le processus est terminé ou annulé.
* Appliquer les exigences supplémentaires suivantes pour des raisons de sécurité :
  * Déclarez la taille maximale du fichier ou des données qui peut être passée.
  * Déclarez le taux de téléchargement minimal du client au serveur.
* Une fois les données reçues par le serveur, les données peuvent être :
  * Stocké temporairement dans une mémoire tampon jusqu’à ce que tous les segments soient collectés.
  * Consommé immédiatement. Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur le disque à mesure que chaque segment est reçu.

La classe de chargeur de fichiers suivante gère l’interopérabilité de JS avec le client. La classe de chargeur utilise l’interopérabilité JS pour :

* Interroger le client pour envoyer un segment de données.
* Abandonner la transaction si le délai d’interrogation est dépassé.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

Dans l'exemple précédent :

* Le `_maxBase64SegmentSize` est défini sur `8192`, calculé à partir de `_maxBase64SegmentSize = _segmentSize * 4 / 3`.
* Les API de gestion de mémoire .NET Core de bas niveau sont utilisées pour stocker les segments de mémoire sur le serveur dans `_uploadedSegments`.
* Une méthode `ReceiveFile` est utilisée pour gérer le téléchargement via l’interopérabilité JS :
  * La taille du fichier est déterminée en octets par le biais de l’interopérabilité JS avec `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * Le nombre de segments à recevoir est calculé et stocké dans `numberOfSegments`.
  * Les segments sont demandés dans une boucle `for` via l’interopérabilité JS avec `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`. Tous les segments, mais le dernier doit être de 8 192 octets avant le décodage. Le client est contraint d’envoyer les données de manière efficace.
  * Pour chaque segment reçu, les vérifications sont effectuées avant le décodage avec <xref:System.Convert.TryFromBase64String*>.
  * Un flux avec les données est retourné sous la forme d’un nouveau <xref:System.IO.Stream> (`SegmentedStream`) une fois le téléchargement terminé.

La classe de flux segmentée expose la liste de segments sous la forme d’un <xref:System.IO.Stream>ReadOnly non identifiable :

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Le code suivant implémente les fonctions JavaScript pour recevoir les données :

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
