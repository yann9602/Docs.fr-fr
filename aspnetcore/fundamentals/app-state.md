---
title: "État de session et d’application dans ASP.NET Core"
author: rick-anderson
description: "Approches de conservation d’application et l’état utilisateur (session) entre les demandes."
keywords: "ASP.NET Core, état de l’Application, l’état de session, querystring, valider"
ms.author: riande
manager: wpickett
ms.date: 06/08/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c639d3b0d896b927bb2b70658032fc1bd8e87191
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a><span data-ttu-id="a7176-104">Introduction à l’état de session et d’application dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7176-104">Introduction to session and application state in ASP.NET Core</span></span>

<span data-ttu-id="a7176-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), et [Diana LaRose](https://github.com/DianaLaRose)</span><span class="sxs-lookup"><span data-stu-id="a7176-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Diana LaRose](https://github.com/DianaLaRose)</span></span>

<span data-ttu-id="a7176-106">HTTP est un protocole sans état.</span><span class="sxs-lookup"><span data-stu-id="a7176-106">HTTP is a stateless protocol.</span></span> <span data-ttu-id="a7176-107">Un serveur web traite chaque demande HTTP comme une demande indépendante et ne conserve pas les valeurs de l’utilisateur à partir de requêtes précédentes.</span><span class="sxs-lookup"><span data-stu-id="a7176-107">A  web server treats each HTTP request as an independent request and does not retain user values from previous requests.</span></span> <span data-ttu-id="a7176-108">Cet article décrit les différentes façons de conserver l’application et l’état de session entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="a7176-108">This article discusses different ways to preserve application and session state between requests.</span></span> 

## <a name="session-state"></a><span data-ttu-id="a7176-109">État de session</span><span class="sxs-lookup"><span data-stu-id="a7176-109">Session state</span></span>

<span data-ttu-id="a7176-110">État de session est une fonctionnalité dans ASP.NET Core que vous pouvez utiliser pour enregistrer et stocker des données utilisateur pendant que l’utilisateur accède à votre application web.</span><span class="sxs-lookup"><span data-stu-id="a7176-110">Session state is a feature in ASP.NET Core that you can use to save and store user data while the user browses your web app.</span></span> <span data-ttu-id="a7176-111">Consistant en une table de hachage ou de dictionnaire sur le serveur, l’état de session conserve les données entre les demandes à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a7176-111">Consisting of a dictionary or hash table on the server, session state persists data across requests from a browser.</span></span> <span data-ttu-id="a7176-112">Les données de session sont sauvegardées par un cache.</span><span class="sxs-lookup"><span data-stu-id="a7176-112">The session data is backed by a cache.</span></span>

<span data-ttu-id="a7176-113">ASP.NET Core maintient l’état de session en donnant le client un cookie qui contient l’ID de session, qui est envoyée au serveur avec chaque demande.</span><span class="sxs-lookup"><span data-stu-id="a7176-113">ASP.NET Core maintains session state by giving the client a cookie that contains the session ID, which is sent to the server with each request.</span></span> <span data-ttu-id="a7176-114">Le serveur utilise l’ID de session pour extraire les données de session.</span><span class="sxs-lookup"><span data-stu-id="a7176-114">The server uses the session ID to fetch the session data.</span></span> <span data-ttu-id="a7176-115">Étant donné que le cookie de session est spécifique au navigateur, vous ne peuvent pas partager des sessions dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="a7176-115">Because the session cookie is specific to the browser, you cannot share sessions across browsers.</span></span> <span data-ttu-id="a7176-116">Les cookies de session sont supprimées uniquement lorsque la session se termine.</span><span class="sxs-lookup"><span data-stu-id="a7176-116">Session cookies are deleted only when the browser session ends.</span></span> <span data-ttu-id="a7176-117">Si un cookie est reçu pour une session a expiré, une nouvelle session qui utilise le même cookie de session est créée.</span><span class="sxs-lookup"><span data-stu-id="a7176-117">If a cookie is received for an expired session, a new session that uses the same session cookie  is created.</span></span> 

<span data-ttu-id="a7176-118">Le serveur conserve une session pendant une période limitée après la dernière demande.</span><span class="sxs-lookup"><span data-stu-id="a7176-118">The server retains a session for a limited time after the last request.</span></span> <span data-ttu-id="a7176-119">Vous pouvez définir le délai d’expiration de session ou utiliser la valeur par défaut de 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="a7176-119">You can either set the session timeout or use the default value of 20 minutes.</span></span> <span data-ttu-id="a7176-120">État de session est idéal pour le stockage des données utilisateur qui sont spécifique à une session particulière, mais ne doivent pas être persistante de façon permanente.</span><span class="sxs-lookup"><span data-stu-id="a7176-120">Session state is ideal for storing user data that is specific to a particular session but doesn’t need to be persisted permanently.</span></span> <span data-ttu-id="a7176-121">Données sont supprimées du magasin de stockage soit lorsque vous appelez `Session.Clear` ou l’expiration de la session dans le magasin de données.</span><span class="sxs-lookup"><span data-stu-id="a7176-121">Data is deleted from the backing store either when you call `Session.Clear` or when the session expires in the data store.</span></span> <span data-ttu-id="a7176-122">Le serveur ne connaît pas lorsque le navigateur est fermé ou lorsque le cookie de session est supprimé.</span><span class="sxs-lookup"><span data-stu-id="a7176-122">The server does not know when the browser is closed or when the session cookie is deleted.</span></span>

> [!WARNING]
> <span data-ttu-id="a7176-123">Ne stockez pas de données sensibles dans la session.</span><span class="sxs-lookup"><span data-stu-id="a7176-123">Do not store sensitive data in session.</span></span> <span data-ttu-id="a7176-124">Le client ne peut pas fermer le navigateur et effacer le cookie de session (et certains navigateurs maintenir les cookies de session sur windows).</span><span class="sxs-lookup"><span data-stu-id="a7176-124">The client might not close the browser and clear the session cookie (and some browsers keep session cookies alive across windows).</span></span> <span data-ttu-id="a7176-125">En outre, une session ne peut pas être restreinte à un seul utilisateur ; l’utilisateur suivant risque de continuer avec la même session.</span><span class="sxs-lookup"><span data-stu-id="a7176-125">Also, a session might not be restricted to a single user; the next user might continue with the same session.</span></span>

<span data-ttu-id="a7176-126">Le fournisseur de session en mémoire stocke les données de session sur le serveur local.</span><span class="sxs-lookup"><span data-stu-id="a7176-126">The in-memory session provider stores session data on the local server.</span></span> <span data-ttu-id="a7176-127">Si vous envisagez d’exécuter votre application web sur une batterie de serveurs, vous devez utiliser des sessions rémanentes pour lier chaque session à un serveur spécifique.</span><span class="sxs-lookup"><span data-stu-id="a7176-127">If you plan to run your web app on a server farm, you must use sticky sessions to tie each session to a specific server.</span></span> <span data-ttu-id="a7176-128">La plateforme Windows Azure Web Sites par défaut, les sessions rémanentes (Application Request Routing ou ARR).</span><span class="sxs-lookup"><span data-stu-id="a7176-128">The Windows Azure Web Sites platform defaults to sticky sessions (Application Request Routing or ARR).</span></span> <span data-ttu-id="a7176-129">Toutefois, les sessions rémanentes peuvent affecter l’évolutivité et compliquer la mise à jour des applications web.</span><span class="sxs-lookup"><span data-stu-id="a7176-129">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="a7176-130">Une meilleure option consiste à utiliser le Redis ou distribuées SQL Server met en cache, qui ne nécessitent pas sessions rémanentes.</span><span class="sxs-lookup"><span data-stu-id="a7176-130">A better option is to use the Redis or SQL Server distributed caches, which don't require sticky sessions.</span></span> <span data-ttu-id="a7176-131">Pour plus d’informations, consultez [fonctionne avec un Cache distribué de](xref:performance/caching/distributed).</span><span class="sxs-lookup"><span data-stu-id="a7176-131">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="a7176-132">Pour plus d’informations sur la configuration des fournisseurs de services, consultez [configuration de Session](#configuring-session) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="a7176-132">For details on setting up service providers, see [Configuring Session](#configuring-session) later in this article.</span></span>

<span data-ttu-id="a7176-133">Le reste de cette section décrit les options pour le stockage des données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a7176-133">The remainder of this section describes the options for storing user data.</span></span>

<a name="temp"></a>
### <a name="tempdata"></a><span data-ttu-id="a7176-134">TempData</span><span class="sxs-lookup"><span data-stu-id="a7176-134">TempData</span></span>

<span data-ttu-id="a7176-135">ASP.NET MVC de base expose le [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) propriété sur un [contrôleur](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="a7176-135">ASP.NET Core MVC exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="a7176-136">Cette propriété stocke les données jusqu’à ce qu’elles soient lues.</span><span class="sxs-lookup"><span data-stu-id="a7176-136">This property stores data until it is read.</span></span> <span data-ttu-id="a7176-137">Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression.</span><span class="sxs-lookup"><span data-stu-id="a7176-137">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="a7176-138">`TempData`est particulièrement utile pour la redirection, lorsqu’il manque des données plus longtemps qu’une demande unique.</span><span class="sxs-lookup"><span data-stu-id="a7176-138">`TempData` is particularly useful for redirection, when data is needed for more than a single request.</span></span> <span data-ttu-id="a7176-139">`TempData`s’appuie sur l’état de session.</span><span class="sxs-lookup"><span data-stu-id="a7176-139">`TempData` is built on top of session state.</span></span> 

## <a name="cookie-based-tempdata-provider"></a><span data-ttu-id="a7176-140">Fournisseur de TempData basé sur cookie</span><span class="sxs-lookup"><span data-stu-id="a7176-140">Cookie-based TempData provider</span></span> 

<span data-ttu-id="a7176-141">Dans ASP.NET Core 1.1 et ultérieures, vous pouvez utiliser le fournisseur TempData basé sur cookie pour stocker les TempData d’un utilisateur dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="a7176-141">In ASP.NET Core 1.1 and higher, you can use the cookie-based TempData provider to store a user's TempData in a cookie.</span></span> <span data-ttu-id="a7176-142">Pour activer le fournisseur TempData basé sur cookie, inscrire le `CookieTempDataProvider` service `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7176-142">To enable the  cookie-based TempData provider, register the `CookieTempDataProvider` service in `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add CookieTempDataProvider after AddMvc and include ViewFeatures.
    // using Microsoft.AspNetCore.Mvc.ViewFeatures;
    services.AddSingleton<ITempDataProvider, CookieTempDataProvider>();
}
```

<span data-ttu-id="a7176-143">Les données de cookie sont codées avec le [Base64UrlTextEncoder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder).</span><span class="sxs-lookup"><span data-stu-id="a7176-143">The cookie data is encoded with the [Base64UrlTextEncoder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder).</span></span> <span data-ttu-id="a7176-144">Étant donné que le cookie est chiffré et mémorisé en bloc, la limite de taille de cookie unique ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="a7176-144">Because the cookie is encrypted and chunked, the single cookie size limit does not apply.</span></span> <span data-ttu-id="a7176-145">Les données de cookie ne sont pas compressées, étant donné que la compression des données d’encryped peut entraîner des problèmes de sécurité telles que la [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) et [violation](https://wikipedia.org/wiki/BREACH_(security_exploit)) attaques.</span><span class="sxs-lookup"><span data-stu-id="a7176-145">The cookie data is not compressed, because compressing encryped data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="a7176-146">Pour plus d’informations sur le fournisseur TempData basé sur cookie, consultez [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).</span><span class="sxs-lookup"><span data-stu-id="a7176-146">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).</span></span>

### <a name="query-strings"></a><span data-ttu-id="a7176-147">Chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="a7176-147">Query strings</span></span>

<span data-ttu-id="a7176-148">Vous pouvez passer une quantité limitée de données à partir d’une demande à un autre en l’ajoutant à la chaîne de requête de la nouvelle demande.</span><span class="sxs-lookup"><span data-stu-id="a7176-148">You can pass a limited amount of data from one request to another by adding it to the new request’s query string.</span></span> <span data-ttu-id="a7176-149">Cela est utile pour capturer l’état de manière persistante qui autorise les liens avec état incorporée à partager par courrier électronique ou de réseaux sociaux.</span><span class="sxs-lookup"><span data-stu-id="a7176-149">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="a7176-150">Toutefois, pour cette raison, vous ne devez jamais utiliser des chaînes de requête pour les données sensibles.</span><span class="sxs-lookup"><span data-stu-id="a7176-150">However, for this reason,  you should never use query strings for sensitive data.</span></span> <span data-ttu-id="a7176-151">En plus facilement partagé, y compris les données dans les chaînes de requête peut créer des opportunités pour [Cross-Site demande Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attaques, ce qui peuvent amener les utilisateurs à visiter les sites malveillants lors de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="a7176-151">In addition to being easily shared, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="a7176-152">Des personnes malveillantes peuvent ensuite dérober des données utilisateur à partir de votre application ou des actions malveillantes part de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a7176-152">Attackers can then steal user data from your app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="a7176-153">N’importe quel état de session ou application préservé doit protéger contre les attaques CSRF.</span><span class="sxs-lookup"><span data-stu-id="a7176-153">Any preserved application or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="a7176-154">Pour plus d’informations sur les attaques CSRF, consultez [attaques empêchant Cross-Site Request Forgery (XSRF/CSRF) dans ASP.NET Core](../security/anti-request-forgery.md).</span><span class="sxs-lookup"><span data-stu-id="a7176-154">For more information on CSRF attacks, see [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core](../security/anti-request-forgery.md).</span></span>

### <a name="post-data-and-hidden-fields"></a><span data-ttu-id="a7176-155">Données de publication et les champs masqués</span><span class="sxs-lookup"><span data-stu-id="a7176-155">Post data and hidden fields</span></span>

<span data-ttu-id="a7176-156">Données peuvent être enregistrées dans les champs masqués et validées sur la demande suivante.</span><span class="sxs-lookup"><span data-stu-id="a7176-156">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="a7176-157">Cela est courant dans les formulaires à plusieurs pages.</span><span class="sxs-lookup"><span data-stu-id="a7176-157">This is common in multipage forms.</span></span> <span data-ttu-id="a7176-158">Toutefois, étant donné que le client peut potentiellement falsifier des données, le serveur doit toujours revalider.</span><span class="sxs-lookup"><span data-stu-id="a7176-158">However, because the  client can potentially tamper with the data, the server must always revalidate it.</span></span> 

### <a name="cookies"></a><span data-ttu-id="a7176-159">Cookies</span><span class="sxs-lookup"><span data-stu-id="a7176-159">Cookies</span></span>

<span data-ttu-id="a7176-160">Les cookies permettent de stocker des données spécifiques à l’utilisateur dans les applications web.</span><span class="sxs-lookup"><span data-stu-id="a7176-160">Cookies provide a way to store user-specific data in web applications.</span></span> <span data-ttu-id="a7176-161">Étant donné que les cookies sont envoyés avec chaque demande, leur taille doit être conservée au minimum.</span><span class="sxs-lookup"><span data-stu-id="a7176-161">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="a7176-162">Dans l’idéal, doit être stocké dans un cookie uniquement un identificateur avec les données réelles stockées sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a7176-162">Ideally, only an identifier should be stored in a cookie with the actual data stored on the server.</span></span> <span data-ttu-id="a7176-163">La plupart des navigateurs limiter les cookies à 4 096 octets.</span><span class="sxs-lookup"><span data-stu-id="a7176-163">Most browsers restrict cookies to 4096 bytes.</span></span> <span data-ttu-id="a7176-164">En outre, qu’un nombre limité de cookies est disponible pour chaque domaine.</span><span class="sxs-lookup"><span data-stu-id="a7176-164">In addition, only a limited number of cookies are available for each domain.</span></span>  

<span data-ttu-id="a7176-165">Étant donné que les cookies sont au risque de falsification, ils doivent être validées sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a7176-165">Because cookies are subject to tampering, they must be validated on the server.</span></span> <span data-ttu-id="a7176-166">Bien que la durabilité du cookie sur un client est soumis à l’expiration et l’intervention de l’utilisateur, ils sont généralement la forme la plus durable de persistance des données sur le client.</span><span class="sxs-lookup"><span data-stu-id="a7176-166">Although the durability of the cookie on a client is subject to user intervention and expiration, they are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="a7176-167">Les cookies sont souvent utilisés pour la personnalisation, où le contenu est personnalisé pour un utilisateur connu.</span><span class="sxs-lookup"><span data-stu-id="a7176-167">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="a7176-168">Étant donné que l’utilisateur est identifié uniquement et pas authentifié dans la plupart des cas, vous pouvez généralement sécuriser un cookie en stockant le nom d’utilisateur, nom de compte ou un ID d’utilisateur unique (par exemple, un GUID) dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="a7176-168">Because the user is only identified and not authenticated in most cases, you can typically secure a cookie by storing the user name, account name, or a unique user ID (such as a GUID) in the cookie.</span></span> <span data-ttu-id="a7176-169">Vous pouvez ensuite utiliser le cookie pour accéder à l’infrastructure de personnalisation utilisateur d’un site.</span><span class="sxs-lookup"><span data-stu-id="a7176-169">You can then use the cookie to access the user personalization infrastructure of a site.</span></span>

### <a name="httpcontextitems"></a><span data-ttu-id="a7176-170">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="a7176-170">HttpContext.Items</span></span>

<span data-ttu-id="a7176-171">Le `Items` collection est un bon emplacement pour stocker des données qui sont nécessaire lors uniquement un traitement de la demande particulier.</span><span class="sxs-lookup"><span data-stu-id="a7176-171">The `Items` collection is a good location to store data that is needed only while processing one particular request.</span></span> <span data-ttu-id="a7176-172">Contenu de la collection est supprimés après chaque demande.</span><span class="sxs-lookup"><span data-stu-id="a7176-172">The collection's contents are discarded after each request.</span></span> <span data-ttu-id="a7176-173">Le `Items` collection est mieux utilisée comme un moyen pour les composants ou d’intergiciel (middleware) pour communiquer lorsqu’ils fonctionnent à différents moments dans le temps au cours d’une demande et n’ont aucun moyen direct pour passer des paramètres.</span><span class="sxs-lookup"><span data-stu-id="a7176-173">The `Items` collection is best used as a way for components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span> <span data-ttu-id="a7176-174">Pour plus d’informations, consultez [utilisation de HttpContext.Items](#working-with-httpcontextitems), plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="a7176-174">For more information, see [Working with HttpContext.Items](#working-with-httpcontextitems), later in this article.</span></span>

### <a name="cache"></a><span data-ttu-id="a7176-175">d'instance/de clé</span><span class="sxs-lookup"><span data-stu-id="a7176-175">Cache</span></span>

<span data-ttu-id="a7176-176">La mise en cache est un moyen efficace de stocker et récupérer des données.</span><span class="sxs-lookup"><span data-stu-id="a7176-176">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="a7176-177">Vous pouvez contrôler la durée de vie des éléments du cache en fonction de l’heure et d’autres considérations.</span><span class="sxs-lookup"><span data-stu-id="a7176-177">You can control the lifetime of cached items based on time and other considerations.</span></span> <span data-ttu-id="a7176-178">En savoir plus sur [mise en cache](../performance/caching/index.md).</span><span class="sxs-lookup"><span data-stu-id="a7176-178">Learn more about [Caching](../performance/caching/index.md).</span></span>

<a name=session></a>

## <a name="configuring-session"></a><span data-ttu-id="a7176-179">Configuration de Session</span><span class="sxs-lookup"><span data-stu-id="a7176-179">Configuring Session</span></span>

<span data-ttu-id="a7176-180">Le `Microsoft.AspNetCore.Session` package fournit un intergiciel (middleware) pour gérer l’état de session.</span><span class="sxs-lookup"><span data-stu-id="a7176-180">The `Microsoft.AspNetCore.Session` package provides middleware for managing session state.</span></span> <span data-ttu-id="a7176-181">Pour activer l’intergiciel de session, `Startup`doit contenir :</span><span class="sxs-lookup"><span data-stu-id="a7176-181">To enable the session middleware, `Startup`must contain:</span></span>

- <span data-ttu-id="a7176-182">Un de le [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) caches mémoire.</span><span class="sxs-lookup"><span data-stu-id="a7176-182">Any of the [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="a7176-183">Le `IDistributedCache` implémentation est utilisée comme un magasin de stockage pour la session.</span><span class="sxs-lookup"><span data-stu-id="a7176-183">The `IDistributedCache` implementation is used as a backing store for session.</span></span>
- <span data-ttu-id="a7176-184">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) appeler, ce qui nécessite le package NuGet « Microsoft.AspNetCore.Session ».</span><span class="sxs-lookup"><span data-stu-id="a7176-184">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) call, which requires NuGet package "Microsoft.AspNetCore.Session".</span></span>
- <span data-ttu-id="a7176-185">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) appeler.</span><span class="sxs-lookup"><span data-stu-id="a7176-185">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) call.</span></span>

<span data-ttu-id="a7176-186">Le code suivant montre comment définir le fournisseur de session en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a7176-186">The following code shows how to set up the in-memory session provider.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a7176-187">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a7176-187">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/Startup.cs?highlight=11-19,24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a7176-188">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a7176-188">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

---

<span data-ttu-id="a7176-189">Vous pouvez faire référence à la Session à partir de `HttpContext` une fois qu’il est installé et configuré.</span><span class="sxs-lookup"><span data-stu-id="a7176-189">You can reference Session from `HttpContext` once it is installed and configured.</span></span>

<span data-ttu-id="a7176-190">Si vous essayez d’accéder à `Session` avant `UseSession` a été appelée, l’exception `InvalidOperationException: Session has not been configured for this application or request` est levée.</span><span class="sxs-lookup"><span data-stu-id="a7176-190">If you try to access `Session` before `UseSession` has been called, the exception `InvalidOperationException: Session has not been configured for this application or request` is thrown.</span></span>

<span data-ttu-id="a7176-191">Si vous essayez de créer un nouveau `Session` (autrement dit, aucun cookie de session n’a été créé) une fois que vous avez déjà commencé l’écriture dans le `Response` diffuser en continu, l’exception `InvalidOperationException: The session cannot be established after the response has started` est levée.</span><span class="sxs-lookup"><span data-stu-id="a7176-191">If you try to create a new `Session` (that is, no session cookie has been created) after you have already begun writing to the `Response` stream, the exception `InvalidOperationException: The session cannot be established after the response has started` is thrown.</span></span> <span data-ttu-id="a7176-192">L’exception peut être trouvée dans le journal de serveur web ; Il ne sera pas affiché dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a7176-192">The exception can be found in the web server log; it will not be displayed in the browser.</span></span>

### <a name="loading-session-asynchronously"></a><span data-ttu-id="a7176-193">Chargement asynchrone de Session</span><span class="sxs-lookup"><span data-stu-id="a7176-193">Loading Session asynchronously</span></span> 

<span data-ttu-id="a7176-194">Le fournisseur de session par défaut dans ASP.NET Core charge l’enregistrement de session sous-jacent [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) magasin de façon asynchrone uniquement si le [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) méthode est appelée explicitement avant  le `TryGetValue`, `Set`, ou `Remove` méthodes.</span><span class="sxs-lookup"><span data-stu-id="a7176-194">The default session provider in ASP.NET Core loads the session record from the underlying [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) store asynchronously only if the [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) method is explicitly called before  the `TryGetValue`, `Set`, or `Remove` methods.</span></span> <span data-ttu-id="a7176-195">Si `LoadAsync` n’est pas appelée en premier lieu, sous-jacent enregistrement de session est chargée de façon synchrone, ce qui pourrait avoir un impact sur la capacité de l’application à l’échelle.</span><span class="sxs-lookup"><span data-stu-id="a7176-195">If `LoadAsync` is not called first, the underlying session record is loaded synchronously, which could potentially impact the ability of the app to scale.</span></span>

<span data-ttu-id="a7176-196">Pour que les applications d’appliquer ce modèle, vous devez encapsuler le [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) et [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) implémentations avec des versions qui lèvent une exception si le `LoadAsync` méthode n’est pas appelé avant `TryGetValue`, `Set`, ou `Remove`.</span><span class="sxs-lookup"><span data-stu-id="a7176-196">To have applications enforce this pattern, wrap the [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method is not called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="a7176-197">Enregistrer les versions encapsulées dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="a7176-197">Register the wrapped versions in the services container.</span></span>

### <a name="implementation-details"></a><span data-ttu-id="a7176-198">Détails d’implémentation</span><span class="sxs-lookup"><span data-stu-id="a7176-198">Implementation Details</span></span>

<span data-ttu-id="a7176-199">Session utilise un cookie pour suivre et identifier les demandes à partir d’un seul navigateur.</span><span class="sxs-lookup"><span data-stu-id="a7176-199">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="a7176-200">Par défaut, ce cookie est nommé ». AspNet.Session » et qu’il utilise un chemin d’accès « / ».</span><span class="sxs-lookup"><span data-stu-id="a7176-200">By default, this cookie is named ".AspNet.Session", and it uses a path of "/".</span></span> <span data-ttu-id="a7176-201">Étant donné que la valeur par défaut du cookie ne spécifie pas un domaine, il n'est pas mis à disposition le script côté client dans la page (car `CookieHttpOnly` par défaut est `true`).</span><span class="sxs-lookup"><span data-stu-id="a7176-201">Because the cookie default does not specify a domain, it is not made available to the client-side script on the page (because `CookieHttpOnly` defaults to `true`).</span></span>

<span data-ttu-id="a7176-202">Pour remplacer les valeurs par défaut de la session, utilisez `SessionOptions`:</span><span class="sxs-lookup"><span data-stu-id="a7176-202">To override session defaults, use `SessionOptions`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a7176-203">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a7176-203">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupCopy.cs?name=snippet1&highlight=8-12)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a7176-204">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a7176-204">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

---

<span data-ttu-id="a7176-205">Le serveur utilise le `IdleTimeout` propriété pour déterminer la durée pendant laquelle une session peut être inactive avant que son contenu est abandonné.</span><span class="sxs-lookup"><span data-stu-id="a7176-205">The server uses the `IdleTimeout` property to determine how long a session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="a7176-206">Cette propriété est indépendante de l’expiration du cookie.</span><span class="sxs-lookup"><span data-stu-id="a7176-206">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="a7176-207">Chaque demande qui passe par l’intergiciel de Session (lues ou écrites pour) réinitialise le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="a7176-207">Each request that passes through the Session middleware (read from or written to) resets the timeout.</span></span>

<span data-ttu-id="a7176-208">Étant donné que `Session` est *sans verrouillage*, si deux demandes tentent de modifier le contenu de la session, il remplace le premier.</span><span class="sxs-lookup"><span data-stu-id="a7176-208">Because `Session` is *non-locking*, if two requests both attempt to modify the contents of session, the last one overrides the first.</span></span> <span data-ttu-id="a7176-209">`Session`est implémenté comme un *session cohérente*, ce qui signifie que tout le contenu est stocké ensemble.</span><span class="sxs-lookup"><span data-stu-id="a7176-209">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="a7176-210">Deux requêtes qui sont modifier différentes parties de la session (clés différents) peuvent toujours avoir un impact sur eux.</span><span class="sxs-lookup"><span data-stu-id="a7176-210">Two requests that are modifying different parts of the session (different keys) might still impact each other.</span></span>

## <a name="setting-and-getting-session-values"></a><span data-ttu-id="a7176-211">Définition et l’obtention des valeurs de Session</span><span class="sxs-lookup"><span data-stu-id="a7176-211">Setting and getting Session values</span></span>

<span data-ttu-id="a7176-212">Session est accessible via la `Session` propriété sur `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="a7176-212">Session is accessed through the `Session` property on `HttpContext`.</span></span> <span data-ttu-id="a7176-213">Cette propriété est un [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) implémentation.</span><span class="sxs-lookup"><span data-stu-id="a7176-213">This property is an [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

<span data-ttu-id="a7176-214">L’exemple suivant illustre la définition et l’obtention d’une valeur int et une chaîne :</span><span class="sxs-lookup"><span data-stu-id="a7176-214">The following example shows setting and getting an int and a string:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="a7176-215">Si vous ajoutez les méthodes d’extension, vous pouvez définir et obtenir des objets sérialisables à la Session :</span><span class="sxs-lookup"><span data-stu-id="a7176-215">If you add the following extension methods, you can set and get serializable objects to Session:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

<span data-ttu-id="a7176-216">L’exemple suivant montre comment définir et obtenir un objet sérialisable :</span><span class="sxs-lookup"><span data-stu-id="a7176-216">The following sample shows how to set and get a serializable object:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a><span data-ttu-id="a7176-217">Utilisation de HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="a7176-217">Working with HttpContext.Items</span></span>

<span data-ttu-id="a7176-218">Le `HttpContext` abstraction fournit la prise en charge pour une collection de dictionnaires de type `IDictionary<object, object>`, appelé `Items`.</span><span class="sxs-lookup"><span data-stu-id="a7176-218">The `HttpContext` abstraction provides support for a dictionary collection of type `IDictionary<object, object>`, called `Items`.</span></span> <span data-ttu-id="a7176-219">Cette collection est disponible depuis le début d’une *HttpRequest* et supprimées à la fin de chaque demande.</span><span class="sxs-lookup"><span data-stu-id="a7176-219">This collection is available from the start of an *HttpRequest* and is discarded at the end of each request.</span></span> <span data-ttu-id="a7176-220">Il est accessible en affectant une valeur à une entrée de clé, ou en demandant la valeur d’une clé particulière.</span><span class="sxs-lookup"><span data-stu-id="a7176-220">You can access it by  assigning a value to a keyed entry, or by requesting the value for a particular key.</span></span>

<span data-ttu-id="a7176-221">Dans l’exemple ci-dessous, [intergiciel (middleware)](middleware.md) ajoute `isVerified` à la `Items` collection.</span><span class="sxs-lookup"><span data-stu-id="a7176-221">In the sample below, [Middleware](middleware.md) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="a7176-222">Plus tard dans le pipeline, un autre intergiciel (middleware) peut y accéder :</span><span class="sxs-lookup"><span data-stu-id="a7176-222">Later in the pipeline, another middleware could access it:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

<span data-ttu-id="a7176-223">Pour un intergiciel (middleware) qui servira uniquement par une application unique, `string` clés sont acceptables.</span><span class="sxs-lookup"><span data-stu-id="a7176-223">For middleware that will only be used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="a7176-224">Toutefois, intergiciel (middleware) qui est partagée entre les applications doit utiliser des clés de l’objet unique afin d’éviter tout risque de collision de clés.</span><span class="sxs-lookup"><span data-stu-id="a7176-224">However, middleware that will be shared between applications should use unique object keys to avoid any chance of key collisions.</span></span> <span data-ttu-id="a7176-225">Si vous développez un intergiciel (middleware) qui doit fonctionner sur plusieurs applications, utilisez une clé d’objet unique définie dans votre classe d’intergiciel (middleware) comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="a7176-225">If you are developing middleware that must work across multiple applications, use a unique object key defined in your middleware class as shown below:</span></span>

```csharp
public class SampleMiddleware
{
    public static readonly object SampleKey = new Object();

    public async Task Invoke(HttpContext httpContext)
    {
        httpContext.Items[SampleKey] = "some value";
        // additional code omitted
    }
}
```

<span data-ttu-id="a7176-226">Tout autre code peut accéder à la valeur stockée dans `HttpContext.Items` à l’aide de la clé exposée par la classe de l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="a7176-226">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

<span data-ttu-id="a7176-227">Cette approche a également l’avantage d’éliminer la répétition de « chaînes magiques » à plusieurs endroits dans le code.</span><span class="sxs-lookup"><span data-stu-id="a7176-227">This approach also has the advantage of eliminating repetition of "magic strings" in multiple places in the code.</span></span>

<a name=appstate-errors></a>

## <a name="application-state-data"></a><span data-ttu-id="a7176-228">Données de l’état</span><span class="sxs-lookup"><span data-stu-id="a7176-228">Application state data</span></span>

<span data-ttu-id="a7176-229">Utilisez [Injection de dépendance](xref:fundamentals/dependency-injection) pour rendre les données accessibles à tous les utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="a7176-229">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="a7176-230">Définir un service qui contient les données (par exemple, une classe nommée `MyAppData`).</span><span class="sxs-lookup"><span data-stu-id="a7176-230">Define a service containing the data (for example, a class named `MyAppData`).</span></span>

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. <span data-ttu-id="a7176-231">Ajouter la classe de service `ConfigureServices` (par exemple `services.AddSingleton<MyAppData>();`).</span><span class="sxs-lookup"><span data-stu-id="a7176-231">Add the service class to `ConfigureServices` (for example `services.AddSingleton<MyAppData>();`).</span></span>
3. <span data-ttu-id="a7176-232">Consommer la classe de service de données dans chaque contrôleur :</span><span class="sxs-lookup"><span data-stu-id="a7176-232">Consume the data service class in each controller:</span></span>

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.)
    }
} 
```

### <a name="common-errors-when-working-with-session"></a><span data-ttu-id="a7176-233">Erreurs courantes lors de l’utilisation avec une session</span><span class="sxs-lookup"><span data-stu-id="a7176-233">Common errors when working with session</span></span>

* <span data-ttu-id="a7176-234">« Impossible de résoudre le service pour le type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' lors de la tentative d’activation 'Microsoft.AspNetCore.Session.DistributedSessionStore'. »</span><span class="sxs-lookup"><span data-stu-id="a7176-234">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="a7176-235">Cela est généralement dû au moins une configuration `IDistributedCache` implémentation.</span><span class="sxs-lookup"><span data-stu-id="a7176-235">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="a7176-236">Pour plus d’informations, consultez [fonctionne avec un Cache distribué de](xref:performance/caching/distributed) et [mise en mémoire cache](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="a7176-236">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed) and [In memory caching](xref:performance/caching/memory).</span></span>

### <a name="additional-resources"></a><span data-ttu-id="a7176-237">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7176-237">Additional Resources</span></span>


* [<span data-ttu-id="a7176-238">ASP.NET Core 1.x : exemple de code utilisé dans ce document</span><span class="sxs-lookup"><span data-stu-id="a7176-238">ASP.NET Core 1.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
* [<span data-ttu-id="a7176-239">ASP.NET Core 2.x : exemple de code utilisé dans ce document</span><span class="sxs-lookup"><span data-stu-id="a7176-239">ASP.NET Core 2.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSessionDotNetCore2.0App)
