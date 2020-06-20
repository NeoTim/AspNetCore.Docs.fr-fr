---
title: ASP.NET Core l' Blazor injection de dépendances
author: guardrex
description: Découvrez comment les Blazor applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: b4ac0dbc6dabdeff4689544f2e11278b8302c553
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103702"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core l' Blazor injection de dépendances

Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)

Blazorprend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection). Les applications peuvent utiliser des services intégrés en les injectant dans des composants. Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.

La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central. Cela peut être utile dans les Blazor applications pour :

* Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .
* Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence. Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application. L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application. Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret. L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.

## <a name="default-services"></a>Services par défaut

Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.

| Service | Durée de vie | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Temporaire | Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.<br><br>L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor application webassembly utilise le navigateur pour gérer le trafic HTTP en arrière-plan.<br><br>BlazorLes applications serveur n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service par défaut. Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor application serveur.<br><br>Pour plus d’informations, consultez <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor Webassembly)<br>Étendue ( Blazor serveur) | Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués. Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor Webassembly)<br>Étendue ( Blazor serveur) | Contient des assistances pour l’utilisation des URI et de l’état de navigation. Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau. Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.

## <a name="add-services-to-an-app"></a>Ajouter des services à une application

### <a name="blazor-webassembly"></a>BlazorWebassembly

Configurez les services de la collection de services de l’application dans la `Main` méthode de *Program.cs*. Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants. Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

L’hôte fournit également une instance de configuration centrale pour l’application. En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, *appsettings.jssur*) à `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>BlazorServeurs

Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> . Les services sont ajoutés en fournissant des descripteurs de service à la collection de services. L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durée de vie du service

Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.

| Durée de vie | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | BlazorLes applications webassembly n’ont pas actuellement de concept d’étendues DI. `Scoped`-les services inscrits se comportent comme des `Singleton` services. Toutefois, le Blazor modèle d’hébergement de serveur prend en charge la `Scoped` durée de vie. Dans Blazor les applications serveur, l’inscription d’un service étendu est limitée à la *connexion*. Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI crée une *seule instance* du service. Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service. |

Le système DI est basé sur le système DI dans ASP.NET Core. Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Demander un service dans un composant

Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject)a deux paramètres :

* Type : type du service à injecter.
* Propriété : nom de la propriété qui reçoit le service d’application injecté. La propriété ne nécessite pas de création manuelle. Le compilateur crée la propriété.

Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.

Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.

L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) . Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` . Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

En interne, la propriété générée ( `DataRepository` ) utilise l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut. En règle générale, cet attribut n’est pas utilisé directement. Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Dans les composants dérivés de la classe de base, la [`@inject`](xref:mvc/views/razor#inject) directive n’est pas obligatoire. Le <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la classe de base est suffisant :

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Utiliser DI dans les services

Les services complexes peuvent nécessiter des services supplémentaires. Dans l’exemple précédent, `DataAccess` peut nécessiter le <xref:System.Net.Http.HttpClient> service par défaut. [`@inject`](xref:mvc/views/razor#inject)(ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut) n’est pas disponible pour une utilisation dans les services. L' *injection de constructeur* doit être utilisée à la place. Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service. Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Conditions préalables pour l’injection de constructeur :

* Un constructeur doit exister dont les arguments peuvent tous être remplis par DI. Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.
* Le constructeur applicable doit être *public*.
* Un constructeur applicable doit exister. En cas d’ambiguïté, DI lève une exception.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de composants de base de l’utilitaire pour gérer une étendue DI

Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle. Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI. Dans Blazor les applications serveur, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu. Dans les Blazor applications Webassembly, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.

Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un type abstrait dérivé de <xref:Microsoft.AspNetCore.Components.ComponentBase> qui crée une étendue di correspondant à la durée de vie du composant. À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant. Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés. Cela peut être utile pour les services qui :

* Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.
* Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.

Deux versions du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type sont disponibles :

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un enfant abstrait et jetable du <xref:Microsoft.AspNetCore.Components.ComponentBase> type avec une propriété protégée <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> de type <xref:System.IServiceProvider> . Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.

  Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut ne sont pas créés dans l’étendue du composant. Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> . Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>dérive de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et ajoute une <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriété qui retourne une instance de `T` à partir du fournisseur di étendu. Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de <xref:System.IServiceProvider> lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant. La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Utilisation de l’Entity Framework DbContext à partir de DI

Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework les objets (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> . L’inscription des services EF à l’aide de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ajoute en <xref:Microsoft.EntityFrameworkCore.DbContext> tant que service étendu par défaut. L’inscription en tant que service étendu peut entraîner des problèmes dans Blazor les applications, car elle entraîne une <xref:Microsoft.EntityFrameworkCore.DbContext> longue durée de vie des instances et leur partage sur l’ensemble de l’application. <xref:Microsoft.EntityFrameworkCore.DbContext>n’est pas thread-safe et ne doit pas être utilisé simultanément.

Selon l’application, l’utilisation <xref:Microsoft.AspNetCore.Components.OwningComponentBase> de pour limiter l’étendue d’un <xref:Microsoft.EntityFrameworkCore.DbContext> à un seul composant *peut* résoudre le problème. Si un composant n’utilise pas <xref:Microsoft.EntityFrameworkCore.DbContext> en parallèle, le fait de dériver le composant de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et de récupérer le <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> est suffisant, car il garantit que :

* Les composants distincts ne partagent pas un <xref:Microsoft.EntityFrameworkCore.DbContext> .
* La <xref:Microsoft.EntityFrameworkCore.DbContext> fonction vit uniquement tant que le composant en dépend.

Si un seul composant peut utiliser un <xref:Microsoft.EntityFrameworkCore.DbContext> en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même l’utilisation de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> n’évite pas les problèmes liés à des opérations EF simultanées. Dans ce cas, utilisez un différent <xref:Microsoft.EntityFrameworkCore.DbContext> pour chaque opération EF logique. Utilisez l’une des approches suivantes :

* Créez <xref:Microsoft.EntityFrameworkCore.DbContext> directement à l’aide de <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> comme argument, qui peut être récupéré à partir de di et est thread-safe.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* Inscrivez le <xref:Microsoft.EntityFrameworkCore.DbContext> dans le conteneur de service avec une durée de vie transitoire :
  * Lors de l’inscription du contexte, utilisez <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . La <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension accepte deux paramètres facultatifs de type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`peut conserver sa valeur par défaut <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Le temporaire <xref:Microsoft.EntityFrameworkCore.DbContext> peut être injecté comme normal (à l’aide de [`@inject`](xref:mvc/views/razor#inject) ) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle. Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander <xref:Microsoft.EntityFrameworkCore.DbContext> des objets distincts pour chaque opération parallèle à l’aide de <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/dependency-injection>
* [Recommandations IDisposable pour les instances temporaires et partagées](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>