> [!WARNING]
> <span data-ttu-id="2326e-101">Un paramètre **catch-all** peut faire correspondre les itinéraires de manière incorrecte en raison d’un [bogue](https://github.com/dotnet/aspnetcore/issues/18677) dans le routage.</span><span class="sxs-lookup"><span data-stu-id="2326e-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="2326e-102">Les applications affectées par ce bogue présentent les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="2326e-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="2326e-103">Un itinéraire de rattrapage, par exemple,`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="2326e-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="2326e-104">L’itinéraire catch-all ne parvient pas à faire correspondre les demandes auxquelles il doit correspondre.</span><span class="sxs-lookup"><span data-stu-id="2326e-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="2326e-105">La suppression d’autres itinéraires rend le fonctionnement de l’itinéraire de rattrapage.</span><span class="sxs-lookup"><span data-stu-id="2326e-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="2326e-106">Consultez les bogues GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) et [16579](https://github.com/dotnet/aspnetcore/issues/16579) pour obtenir des exemples de cas qui rencontrent ce bogue.</span><span class="sxs-lookup"><span data-stu-id="2326e-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="2326e-107">Un correctif d’abonnement pour ce bogue est planifié.</span><span class="sxs-lookup"><span data-stu-id="2326e-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="2326e-108">Ce document sera mis à jour lors de la publication du correctif.</span><span class="sxs-lookup"><span data-stu-id="2326e-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="2326e-109">Lorsque le correctif est publié, le code suivant définit un commutateur interne qui corrige ce bogue :</span><span class="sxs-lookup"><span data-stu-id="2326e-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```