---
title: Configurer l’authentification par certificat dans ASP.NET Core
author: blowdart
description: Découvrez comment configurer l’authentification par certificat dans ASP.NET Core pour IIS et HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 280daa86510d4445c791b6952653122961f13aeb
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447280"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="d971f-103">Configurer l’authentification par certificat dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d971f-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="d971f-104">`Microsoft.AspNetCore.Authentication.Certificate` contient une implémentation similaire à [l’authentification par certificat](https://tools.ietf.org/html/rfc5246#section-7.4.4) pour les ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d971f-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="d971f-105">L’authentification par certificat se produit au niveau du TLS, à long terme avant qu’il ne soit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d971f-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="d971f-106">Plus précisément, il s’agit d’un gestionnaire d’authentification qui valide le certificat et vous donne un événement dans lequel vous pouvez résoudre ce certificat en `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="d971f-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="d971f-107">[Configurez votre hôte](#configure-your-host-to-require-certificates) pour l’authentification par certificat, qu’il soit IIS, Kestrel, Azure Web Apps ou tout autre que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="d971f-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="d971f-108">Scénarios de proxy et d’équilibrage de charge</span><span class="sxs-lookup"><span data-stu-id="d971f-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="d971f-109">L’authentification par certificat est un scénario avec état principalement utilisé dans lequel un proxy ou un équilibreur de charge ne gère pas le trafic entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="d971f-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="d971f-110">Si un proxy ou un équilibreur de charge est utilisé, l’authentification par certificat fonctionne uniquement si le proxy ou l’équilibreur de charge :</span><span class="sxs-lookup"><span data-stu-id="d971f-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="d971f-111">Gère l’authentification.</span><span class="sxs-lookup"><span data-stu-id="d971f-111">Handles the authentication.</span></span>
* <span data-ttu-id="d971f-112">Transmet les informations d’authentification de l’utilisateur à l’application (par exemple, dans un en-tête de demande), qui agit sur les informations d’authentification.</span><span class="sxs-lookup"><span data-stu-id="d971f-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="d971f-113">Une alternative à l’authentification par certificat dans les environnements où les proxies et les équilibreurs de charge sont utilisés est Active Directory Services fédérés (ADFS) avec OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="d971f-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="d971f-114">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="d971f-114">Get started</span></span>

<span data-ttu-id="d971f-115">Obtenez un certificat HTTPs, appliquez-le et [configurez votre hôte](#configure-your-host-to-require-certificates) pour exiger des certificats.</span><span class="sxs-lookup"><span data-stu-id="d971f-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="d971f-116">Dans votre application Web, ajoutez une référence au package de `Microsoft.AspNetCore.Authentication.Certificate`.</span><span class="sxs-lookup"><span data-stu-id="d971f-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="d971f-117">Ensuite, dans la méthode `Startup.ConfigureServices`, appelez `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` avec vos options, en fournissant un délégué pour `OnCertificateValidated` pour effectuer une validation supplémentaire sur le certificat client envoyé avec les demandes.</span><span class="sxs-lookup"><span data-stu-id="d971f-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="d971f-118">Transformez ces informations en `ClaimsPrincipal` et définissez-les sur la propriété `context.Principal`.</span><span class="sxs-lookup"><span data-stu-id="d971f-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="d971f-119">Si l’authentification échoue, ce gestionnaire retourne une réponse `403 (Forbidden)` plutôt qu’un `401 (Unauthorized)`, comme vous pouvez l’attendre.</span><span class="sxs-lookup"><span data-stu-id="d971f-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="d971f-120">Le raisonnement est que l’authentification doit se produire pendant la connexion TLS initiale.</span><span class="sxs-lookup"><span data-stu-id="d971f-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="d971f-121">Au moment où il atteint le gestionnaire, il est trop tard.</span><span class="sxs-lookup"><span data-stu-id="d971f-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="d971f-122">Il n’existe aucun moyen de mettre à niveau la connexion d’une connexion anonyme à une avec un certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="d971f-123">Ajoutez également `app.UseAuthentication();` dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d971f-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="d971f-124">Dans le cas contraire, le `HttpContext.User` ne sera pas défini sur `ClaimsPrincipal` créé à partir du certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="d971f-125">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d971f-125">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="d971f-126">L’exemple précédent illustre la méthode par défaut pour ajouter l’authentification par certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="d971f-127">Le gestionnaire construit un principal d’utilisateur à l’aide des propriétés de certificat courantes.</span><span class="sxs-lookup"><span data-stu-id="d971f-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="d971f-128">Configurer la validation des certificats</span><span class="sxs-lookup"><span data-stu-id="d971f-128">Configure certificate validation</span></span>

<span data-ttu-id="d971f-129">Le gestionnaire de `CertificateAuthenticationOptions` a des validations intégrées qui sont les validations minimales que vous devez effectuer sur un certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="d971f-130">Chacun de ces paramètres est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="d971f-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="d971f-131">AllowedCertificateTypes = chained, SelfSigned ou All (chaîné | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="d971f-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="d971f-132">Valeur par défaut : `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="d971f-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="d971f-133">Cette vérification valide que seul le type de certificat approprié est autorisé.</span><span class="sxs-lookup"><span data-stu-id="d971f-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="d971f-134">Si l’application utilise des certificats auto-signés, cette option doit être définie sur `CertificateTypes.All` ou `CertificateTypes.SelfSigned`.</span><span class="sxs-lookup"><span data-stu-id="d971f-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="d971f-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="d971f-135">ValidateCertificateUse</span></span>

<span data-ttu-id="d971f-136">Valeur par défaut : `true`</span><span class="sxs-lookup"><span data-stu-id="d971f-136">Default value: `true`</span></span>

<span data-ttu-id="d971f-137">Cette vérification permet de vérifier que le certificat présenté par le client a l’utilisation améliorée de la clé d’authentification du client, ou aucune EKU.</span><span class="sxs-lookup"><span data-stu-id="d971f-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="d971f-138">Comme les spécifications indiquent, si aucune EKU n’est spécifiée, toutes les utilisations améliorées de la EKU sont considérées comme valides.</span><span class="sxs-lookup"><span data-stu-id="d971f-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="d971f-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="d971f-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="d971f-140">Valeur par défaut : `true`</span><span class="sxs-lookup"><span data-stu-id="d971f-140">Default value: `true`</span></span>

<span data-ttu-id="d971f-141">Ce contrôle vérifie que le certificat se trouve dans sa période de validité.</span><span class="sxs-lookup"><span data-stu-id="d971f-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="d971f-142">À chaque demande, le gestionnaire s’assure qu’un certificat valide lorsqu’il a été présenté n’a pas expiré pendant sa session active.</span><span class="sxs-lookup"><span data-stu-id="d971f-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="d971f-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="d971f-143">RevocationFlag</span></span>

<span data-ttu-id="d971f-144">Valeur par défaut : `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="d971f-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="d971f-145">Indicateur qui spécifie les certificats de la chaîne qui sont vérifiés pour la révocation.</span><span class="sxs-lookup"><span data-stu-id="d971f-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="d971f-146">Les vérifications de révocation sont effectuées uniquement lorsque le certificat est chaîné à un certificat racine.</span><span class="sxs-lookup"><span data-stu-id="d971f-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="d971f-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="d971f-147">RevocationMode</span></span>

<span data-ttu-id="d971f-148">Valeur par défaut : `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="d971f-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="d971f-149">Indicateur qui spécifie comment les vérifications de révocation sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="d971f-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="d971f-150">La spécification d’un contrôle en ligne peut entraîner un délai de longue durée pendant que l’autorité de certification est contactée.</span><span class="sxs-lookup"><span data-stu-id="d971f-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="d971f-151">Les vérifications de révocation sont effectuées uniquement lorsque le certificat est chaîné à un certificat racine.</span><span class="sxs-lookup"><span data-stu-id="d971f-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="d971f-152">Puis-je configurer mon application pour exiger un certificat uniquement sur certains chemins d’accès ?</span><span class="sxs-lookup"><span data-stu-id="d971f-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="d971f-153">Cela n’est pas possible.</span><span class="sxs-lookup"><span data-stu-id="d971f-153">This isn't possible.</span></span> <span data-ttu-id="d971f-154">Rappelez-vous que l’échange de certificats est effectué au début de la conversation HTTPs, car il est effectué par le serveur avant la réception de la première requête sur cette connexion, ce qui signifie qu’il n’est pas possible d’effectuer une étendue basée sur des champs de demande.</span><span class="sxs-lookup"><span data-stu-id="d971f-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="d971f-155">Événements du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="d971f-155">Handler events</span></span>

<span data-ttu-id="d971f-156">Le gestionnaire a deux événements :</span><span class="sxs-lookup"><span data-stu-id="d971f-156">The handler has two events:</span></span>

* <span data-ttu-id="d971f-157">`OnAuthenticationFailed` &ndash; appelée si une exception se produit pendant l’authentification et vous permet de réagir.</span><span class="sxs-lookup"><span data-stu-id="d971f-157">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="d971f-158">`OnCertificateValidated` &ndash; appelée une fois le certificat validé, la validation a réussi et un principal par défaut a été créé.</span><span class="sxs-lookup"><span data-stu-id="d971f-158">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="d971f-159">Cet événement vous permet d’effectuer votre propre validation et d’augmenter ou de remplacer le principal.</span><span class="sxs-lookup"><span data-stu-id="d971f-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="d971f-160">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="d971f-160">For examples include:</span></span>
  * <span data-ttu-id="d971f-161">Déterminer si le certificat est connu de vos services.</span><span class="sxs-lookup"><span data-stu-id="d971f-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="d971f-162">Construction de votre propre principal.</span><span class="sxs-lookup"><span data-stu-id="d971f-162">Constructing your own principal.</span></span> <span data-ttu-id="d971f-163">Prenons l’exemple suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d971f-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="d971f-164">Si vous trouvez que le certificat entrant ne répond pas à votre validation supplémentaire, appelez `context.Fail("failure reason")` avec une raison d’échec.</span><span class="sxs-lookup"><span data-stu-id="d971f-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="d971f-165">Pour les fonctionnalités réelles, vous souhaiterez probablement appeler un service enregistré dans une injection de dépendances qui se connecte à une base de données ou à un autre type de magasin d’utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="d971f-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="d971f-166">Accédez à votre service à l’aide du contexte passé dans votre délégué.</span><span class="sxs-lookup"><span data-stu-id="d971f-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="d971f-167">Prenons l’exemple suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d971f-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="d971f-168">D’un plan conceptuel, la validation du certificat est un problème d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="d971f-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="d971f-169">L’ajout d’une vérification, par exemple, d’un émetteur ou d’une empreinte numérique dans une stratégie d’autorisation, plutôt que dans `OnCertificateValidated`, est parfaitement acceptable.</span><span class="sxs-lookup"><span data-stu-id="d971f-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="d971f-170">Configuration de votre hôte pour exiger des certificats</span><span class="sxs-lookup"><span data-stu-id="d971f-170">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="d971f-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="d971f-171">Kestrel</span></span>

<span data-ttu-id="d971f-172">Dans *Program.cs*, configurez Kestrel comme suit :</span><span class="sxs-lookup"><span data-stu-id="d971f-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="d971f-173">Les points de terminaison créés en appelant <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **avant** d’appeler <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> n’ont pas les valeurs par défaut appliquées.</span><span class="sxs-lookup"><span data-stu-id="d971f-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="d971f-174">IIS</span><span class="sxs-lookup"><span data-stu-id="d971f-174">IIS</span></span>

<span data-ttu-id="d971f-175">Effectuez les étapes suivantes dans le gestionnaire des services Internet :</span><span class="sxs-lookup"><span data-stu-id="d971f-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="d971f-176">Sélectionnez votre site à partir de l’onglet **connexions** .</span><span class="sxs-lookup"><span data-stu-id="d971f-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="d971f-177">Double-cliquez sur l’option **Paramètres SSL** dans la fenêtre **affichage des fonctionnalités** .</span><span class="sxs-lookup"><span data-stu-id="d971f-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="d971f-178">Cochez la case **exiger SSL** , puis sélectionnez la case d’option **exiger** dans la section **certificats clients** .</span><span class="sxs-lookup"><span data-stu-id="d971f-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Paramètres de certificat client dans IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="d971f-180">Proxys Web Azure et personnalisés</span><span class="sxs-lookup"><span data-stu-id="d971f-180">Azure and custom web proxies</span></span>

<span data-ttu-id="d971f-181">Consultez la [documentation sur l’hôte et le déploiement](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) pour savoir comment configurer l’intergiciel (middleware) de transfert de certificats.</span><span class="sxs-lookup"><span data-stu-id="d971f-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="d971f-182">Utiliser l’authentification par certificat dans Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="d971f-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="d971f-183">Aucune configuration de transfert n’est requise pour Azure.</span><span class="sxs-lookup"><span data-stu-id="d971f-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="d971f-184">Ce programme est déjà configuré dans l’intergiciel (middleware) de transfert de certificats.</span><span class="sxs-lookup"><span data-stu-id="d971f-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="d971f-185">Cela nécessite que le CertificateForwardingMiddleware soit présent.</span><span class="sxs-lookup"><span data-stu-id="d971f-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="d971f-186">Utiliser l’authentification par certificat dans des proxys Web personnalisés</span><span class="sxs-lookup"><span data-stu-id="d971f-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="d971f-187">La méthode `AddCertificateForwarding` est utilisée pour spécifier :</span><span class="sxs-lookup"><span data-stu-id="d971f-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="d971f-188">Nom de l’en-tête du client.</span><span class="sxs-lookup"><span data-stu-id="d971f-188">The client header name.</span></span>
* <span data-ttu-id="d971f-189">Comment le certificat doit être chargé (à l’aide de la propriété `HeaderConverter`).</span><span class="sxs-lookup"><span data-stu-id="d971f-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="d971f-190">Dans les proxies Web personnalisés, le certificat est transmis en tant qu’en-tête de demande personnalisée, par exemple `X-SSL-CERT`.</span><span class="sxs-lookup"><span data-stu-id="d971f-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="d971f-191">Pour l’utiliser, configurez le transfert de certificats dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d971f-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="d971f-192">La méthode `Startup.Configure` ajoute ensuite l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="d971f-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="d971f-193">`UseCertificateForwarding` est appelé avant les appels à `UseAuthentication` et `UseAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="d971f-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="d971f-194">Une classe distincte peut être utilisée pour implémenter une logique de validation.</span><span class="sxs-lookup"><span data-stu-id="d971f-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="d971f-195">Étant donné que le même certificat auto-signé est utilisé dans cet exemple, assurez-vous que seul votre certificat peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="d971f-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="d971f-196">Vérifiez que les empreintes numériques du certificat client et du certificat de serveur correspondent. dans le cas contraire, n’importe quel certificat peut être utilisé et sera suffisant pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="d971f-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="d971f-197">Elle est utilisée dans la méthode `AddCertificate`.</span><span class="sxs-lookup"><span data-stu-id="d971f-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="d971f-198">Vous pouvez également valider l’objet ou l’émetteur ici si vous utilisez des certificats intermédiaires ou enfants.</span><span class="sxs-lookup"><span data-stu-id="d971f-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="d971f-199">Implémenter un HttpClient à l’aide d’un certificat et de HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="d971f-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="d971f-200">Le HttpClientHandler peut être ajouté directement dans le constructeur de la classe HttpClient.</span><span class="sxs-lookup"><span data-stu-id="d971f-200">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="d971f-201">Vous devez veiller à créer des instances de HttpClient.</span><span class="sxs-lookup"><span data-stu-id="d971f-201">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="d971f-202">Le certificat HttpClient envoie ensuite le certificat avec chaque demande.</span><span class="sxs-lookup"><span data-stu-id="d971f-202">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="d971f-203">Implémenter un HttpClient à l’aide d’un certificat et d’un nommé HttpClient à partir de IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d971f-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="d971f-204">Dans l’exemple suivant, un certificat client est ajouté à un HttpClientHandler à l’aide de la propriété ClientCertificates du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="d971f-204">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="d971f-205">Ce gestionnaire peut ensuite être utilisé dans une instance nommée d’un HttpClient à l’aide de la méthode ConfigurePrimaryHttpMessageHandler.</span><span class="sxs-lookup"><span data-stu-id="d971f-205">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="d971f-206">Il s’agit de l’installation dans la classe Startup de la méthode ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="d971f-206">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="d971f-207">Le IHttpClientFactory peut ensuite être utilisé pour récupérer l’instance nommée avec le gestionnaire et le certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-207">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="d971f-208">La méthode CreateClient avec le nom du client défini dans la classe Startup est utilisée pour récupérer l’instance.</span><span class="sxs-lookup"><span data-stu-id="d971f-208">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="d971f-209">La requête HTTP peut être envoyée à l’aide du client en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="d971f-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="d971f-210">Si le bon certificat est envoyé au serveur, les données sont retournées.</span><span class="sxs-lookup"><span data-stu-id="d971f-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="d971f-211">Si aucun certificat ou le mauvais certificat n’est envoyé, un code d’état HTTP 403 est renvoyé.</span><span class="sxs-lookup"><span data-stu-id="d971f-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="d971f-212">Créer des certificats dans PowerShell</span><span class="sxs-lookup"><span data-stu-id="d971f-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="d971f-213">La création de certificats est la partie la plus difficile dans la configuration de ce fluide.</span><span class="sxs-lookup"><span data-stu-id="d971f-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="d971f-214">Un certificat racine peut être créé à l’aide de l’applet de commande `New-SelfSignedCertificate` PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d971f-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="d971f-215">Lors de la création du certificat, utilisez un mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="d971f-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="d971f-216">Il est important d’ajouter le paramètre `KeyUsageProperty` et le paramètre `KeyUsage` comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="d971f-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="d971f-217">Créer une autorité de certification racine</span><span class="sxs-lookup"><span data-stu-id="d971f-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="d971f-218">La valeur du paramètre `-DnsName` doit correspondre à la cible de déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d971f-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="d971f-219">Par exemple, « localhost » pour le développement.</span><span class="sxs-lookup"><span data-stu-id="d971f-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="d971f-220">Installer dans la racine approuvée</span><span class="sxs-lookup"><span data-stu-id="d971f-220">Install in the trusted root</span></span>

<span data-ttu-id="d971f-221">Le certificat racine doit être approuvé sur votre système hôte.</span><span class="sxs-lookup"><span data-stu-id="d971f-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="d971f-222">Un certificat racine qui n’a pas été créé par une autorité de certification n’est pas approuvé par défaut.</span><span class="sxs-lookup"><span data-stu-id="d971f-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="d971f-223">Le lien suivant explique comment cela peut être accompli sur Windows :</span><span class="sxs-lookup"><span data-stu-id="d971f-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="d971f-224">Certificat intermédiaire</span><span class="sxs-lookup"><span data-stu-id="d971f-224">Intermediate certificate</span></span>

<span data-ttu-id="d971f-225">Un certificat intermédiaire peut maintenant être créé à partir du certificat racine.</span><span class="sxs-lookup"><span data-stu-id="d971f-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="d971f-226">Cela n’est pas obligatoire pour tous les cas d’usage, mais vous devrez peut-être créer de nombreux certificats ou avoir besoin d’activer ou de désactiver des groupes de certificats.</span><span class="sxs-lookup"><span data-stu-id="d971f-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="d971f-227">Le paramètre `TextExtension` est requis pour définir la longueur du chemin d’accès dans les contraintes de base du certificat.</span><span class="sxs-lookup"><span data-stu-id="d971f-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="d971f-228">Le certificat intermédiaire peut ensuite être ajouté au certificat intermédiaire approuvé dans le système hôte Windows.</span><span class="sxs-lookup"><span data-stu-id="d971f-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="d971f-229">Créer un certificat enfant à partir d’un certificat intermédiaire</span><span class="sxs-lookup"><span data-stu-id="d971f-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="d971f-230">Un certificat enfant peut être créé à partir du certificat intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="d971f-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="d971f-231">Il s’agit de l’entité finale et n’a pas besoin de créer des certificats enfants supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="d971f-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="d971f-232">Créer un certificat enfant à partir du certificat racine</span><span class="sxs-lookup"><span data-stu-id="d971f-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="d971f-233">Un certificat enfant peut également être créé directement à partir du certificat racine.</span><span class="sxs-lookup"><span data-stu-id="d971f-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="d971f-234">Exemple de certificat de certificat intermédiaire racine</span><span class="sxs-lookup"><span data-stu-id="d971f-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="d971f-235">Lors de l’utilisation de certificats racine, intermédiaires ou enfants, les certificats peuvent être validés à l’aide de l’empreinte numérique ou PublicKey, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="d971f-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
