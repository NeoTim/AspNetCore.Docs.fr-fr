---
title: Journalisation et diagnostics dans ASP.NET Core SignalR
author: anurse
description: Découvrez Comment collecter des diagnostics à partir de votre application ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660971"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Journalisation et diagnostics dans ASP.NET Core Signalr

Par [Andrew Stanton-infirmière](https://twitter.com/anurse)

Cet article fournit des conseils sur la collecte de diagnostics à partir de votre application ASP.NET Core Signalr pour vous aider à résoudre les problèmes.

## <a name="server-side-logging"></a>Journalisation côté serveur

> [!WARNING]
> Les journaux côté serveur peuvent contenir des informations sensibles de votre application. **Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.

Étant donné que Signalr fait partie de ASP.NET Core, il utilise le système de journalisation ASP.NET Core. Dans la configuration par défaut, Signalr enregistre très peu d’informations, mais cela peut être configuré. Pour plus d’informations sur la configuration de la journalisation des ASP.NET Core, consultez la documentation sur [ASP.net Core Logging](xref:fundamentals/logging/index#configuration) .

Signalr utilise deux catégories d’enregistreur d’événements :

* `Microsoft.AspNetCore.SignalR` &ndash; pour les journaux liés aux protocoles de concentrateur, l’activation de hubs, l’appel de méthodes et d’autres activités liées au Hub.
* `Microsoft.AspNetCore.Http.Connections` &ndash; pour les journaux liés aux transports, tels que WebSockets, l’interrogation longue et les événements envoyés par le serveur et l’infrastructure Signalr de bas niveau.

Pour activer les journaux détaillés à partir de Signalr, configurez les deux préfixes précédents au niveau `Debug` dans votre fichier *appSettings. JSON* en ajoutant les éléments suivants à la sous-section `LogLevel` dans `Logging`:

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Vous pouvez également le configurer dans le code de votre méthode `CreateWebHostBuilder` :

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Si vous n’utilisez pas la configuration basée sur JSON, définissez les valeurs de configuration suivantes dans votre système de configuration :

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Consultez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées. Par exemple, lors de l’utilisation de variables d’environnement, deux caractères de `_` sont utilisés à la place de la `:` (par exemple, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

Nous vous recommandons d’utiliser le niveau de `Debug` lors de la collecte de diagnostics plus détaillés pour votre application. Le niveau de `Trace` produit des diagnostics de bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.

## <a name="access-server-side-logs"></a>Accéder aux journaux côté serveur

La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.

### <a name="as-a-console-app-outside-iis"></a>En tant qu’application console en dehors d’IIS

Si vous exécutez dans une application console, l’enregistreur d’événements de [console](xref:fundamentals/logging/index#console-provider) doit être activé par défaut. Les journaux signalr s’affichent dans la console.

### <a name="within-iis-express-from-visual-studio"></a>Dans IIS Express à partir de Visual Studio

Visual Studio affiche la sortie du journal dans la fenêtre **sortie** . Sélectionnez l’option déroulante **ASP.net Core serveur Web** .

### <a name="azure-app-service"></a>Azure App Service

Activez l’option **journalisation des applications (système de fichiers)** dans la section **journaux de diagnostic** du portail Azure App service et configurez le **niveau** sur `Verbose`. Les journaux doivent être disponibles à partir du service de **streaming des journaux** et dans les journaux du système de fichiers du App service. Pour plus d’informations, consultez la page [streaming des journaux Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Autres environnements

Si l’application est déployée dans un autre environnement (par exemple, docker, Kubernetes ou service Windows), consultez <xref:fundamentals/logging/index> pour plus d’informations sur la configuration des fournisseurs de journalisation appropriés pour l’environnement.

## <a name="javascript-client-logging"></a>Journalisation du client JavaScript

> [!WARNING]
> Les journaux côté client peuvent contenir des informations sensibles de votre application. **Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.

Lorsque vous utilisez le client JavaScript, vous pouvez configurer les options de journalisation à l’aide de la méthode `configureLogging` sur `HubConnectionBuilder`:

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la méthode `configureLogging`.

Le tableau suivant montre les niveaux de journal disponibles pour le client JavaScript. La définition du niveau de journal sur l’une de ces valeurs active la journalisation à ce niveau et tous les niveaux au-dessus de celui-ci dans la table.

| Level | Description |
| ----- | ----------- |
| `None` | Aucun message n’est enregistré. |
| `Critical` | Messages indiquant un échec dans l’ensemble de l’application. |
| `Error` | Messages indiquant un échec dans l’opération en cours. |
| `Warning` | Messages indiquant un problème non fatal. |
| `Information` | Messages d’information. |
| `Debug` | Messages de diagnostic utiles pour le débogage. |
| `Trace` | Messages de diagnostic très détaillés conçus pour diagnostiquer des problèmes spécifiques. |

Une fois que vous avez configuré le niveau de détail, les journaux sont écrits dans la console du navigateur (ou la sortie standard dans une application NodeJS).

Si vous souhaitez envoyer des journaux à un système de journalisation personnalisé, vous pouvez fournir un objet JavaScript qui implémente l’interface `ILogger`. La seule méthode qui doit être implémentée est `log`, qui prend le niveau de l’événement et le message associé à l’événement. Par exemple :

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Journalisation du client .NET

> [!WARNING]
> Les journaux côté client peuvent contenir des informations sensibles de votre application. **Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.

Pour récupérer des journaux à partir du client .NET, vous pouvez utiliser la méthode `ConfigureLogging` sur `HubConnectionBuilder`. Cela fonctionne de la même façon que la méthode `ConfigureLogging` sur `WebHostBuilder` et `HostBuilder`. Vous pouvez configurer les mêmes fournisseurs de journalisation que ceux que vous utilisez dans ASP.NET Core. Toutefois, vous devez installer et activer manuellement les packages NuGet pour les différents fournisseurs de journalisation.

### <a name="console-logging"></a>Écriture dans le journal de la console

Pour activer la journalisation de la console, ajoutez le package [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Ensuite, utilisez la méthode `AddConsole` pour configurer le journal de la console :

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Journalisation de la fenêtre sortie du débogage

Vous pouvez également configurer les journaux pour accéder à la fenêtre **sortie** dans Visual Studio. Installez le package [Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) et utilisez la méthode `AddDebug` :

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Autres fournisseurs de journalisation

SignalR prend en charge d’autres fournisseurs de journalisation tels que Serilog, Seq, NLog ou tout autre système de journalisation qui s’intègre à `Microsoft.Extensions.Logging`. Si votre système de journalisation fournit une `ILoggerProvider`, vous pouvez l’inscrire auprès de `AddProvider`:

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Commentaires du contrôle

Si vous vous connectez à partir d’autres emplacements dans votre application, le fait de modifier le niveau par défaut en `Debug` peut être trop détaillé. Vous pouvez utiliser un filtre pour configurer le niveau de journalisation pour les journaux de SignalR. Cela peut être fait dans le code, à peu près de la même façon que sur le serveur :

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Suivis réseau

> [!WARNING]
> Une trace réseau contient le contenu complet de chaque message envoyé par votre application. **Ne** publiez jamais de traces de réseau brutes à partir d’applications de production vers des forums publics tels que github.

Si vous rencontrez un problème, un suivi réseau peut parfois fournir de nombreuses informations utiles. Cela s’avère particulièrement utile si vous envisagez de faire un problème dans notre suivi des problèmes.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Collecter un suivi réseau avec Fiddler (option recommandée)

Cette méthode fonctionne pour toutes les applications.

Fiddler est un outil très puissant pour la collecte de traces HTTP. Installez-le à partir de [Telerik.com/Fiddler](https://www.telerik.com/fiddler), lancez-le, puis exécutez votre application et reproduisez le problème. Fiddler est disponible pour Windows et il existe des versions bêta pour macOS et Linux.

Si vous vous connectez à l’aide de HTTPs, vous devez vous assurer que Fiddler peut déchiffrer le trafic HTTPs. Pour plus d’informations, consultez la [documentation de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Une fois que vous avez collecté la trace, vous pouvez exporter la trace en choisissant **fichier** > **Enregistrer** > **toutes les sessions** dans la barre de menus.

![Exportation de toutes les sessions de Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Collecter un suivi réseau avec tcpdump (macOS et Linux uniquement)

Cette méthode fonctionne pour toutes les applications.

Vous pouvez collecter des traces TCP brutes à l’aide de tcpdump en exécutant la commande suivante à partir d’une interface de commande. Vous devrez peut-être `root` ou préfixer la commande avec `sudo` si vous recevez une erreur d’autorisation :

```console
tcpdump -i [interface] -w trace.pcap
```

Remplacez `[interface]` par l’interface réseau sur laquelle vous souhaitez effectuer la capture. En règle générale, cela ressemble à `/dev/eth0` (pour votre interface Ethernet standard) ou `/dev/lo0` (pour le trafic localhost). Pour plus d’informations, consultez la page man `tcpdump` sur votre système hôte.

## <a name="collect-a-network-trace-in-the-browser"></a>Collecter un suivi réseau dans le navigateur

Cette méthode fonctionne uniquement pour les applications basées sur un navigateur.

La plupart des Outils de développement de navigateur possèdent un onglet « réseau » qui vous permet de capturer l’activité réseau entre le navigateur et le serveur. Toutefois, ces traces n’incluent pas les messages d’événement WebSocket et envoyés par le serveur. Si vous utilisez ces transports, l’utilisation d’un outil tel que Fiddler ou TcpDump (décrit ci-dessous) est une meilleure approche.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge et Internet Explorer

(Les instructions sont les mêmes pour Edge et Internet Explorer)

1. Appuyez sur F12 pour ouvrir les outils de développement
2. Cliquez sur l’onglet réseau.
3. Actualisez la page (si nécessaire) et reproduisez le problème
4. Cliquez sur l’icône Enregistrer dans la barre d’outils pour exporter la trace en tant que fichier « QAR » :

![Icône Enregistrer dans l’onglet Réseau des outils de développement Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Appuyez sur F12 pour ouvrir les outils de développement
2. Cliquez sur l’onglet réseau.
3. Actualisez la page (si nécessaire) et reproduisez le problème
4. Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer en tant que fichier \ avec le contenu » :

![Option « Enregistrer en tant que fichier... avec du contenu » dans l’onglet Réseau des outils de développement Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Appuyez sur F12 pour ouvrir les outils de développement
2. Cliquez sur l’onglet réseau.
3. Actualisez la page (si nécessaire) et reproduisez le problème
4. Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer tout comme QAR ».

![Option « Enregistrer tout comme QAR » dans l’onglet Réseau des outils de développement Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Joindre les fichiers de diagnostic aux problèmes GitHub

Vous pouvez joindre des fichiers de diagnostic aux problèmes GitHub en les renommant afin qu’ils aient une extension de `.txt`, puis en les faisant glisser et en les déposant sur le problème.

> [!NOTE]
> Ne collez pas le contenu des fichiers journaux ou des traces réseau dans un problème GitHub. Ces journaux et suivis peuvent être assez volumineux, et GitHub les tronque généralement.

![Glissement des fichiers journaux sur un problème GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
