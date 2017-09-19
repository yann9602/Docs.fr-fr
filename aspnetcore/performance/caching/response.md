---
title: "Réponse mise en cache"
author: rick-anderson
description: "Explique comment utiliser la réponse mise en cache pour réduire la bande passante et améliorer les performances."
keywords: "ASP.NET Core, la réponse mise en cache, les en-têtes HTTP"
ms.author: riande
manager: wpickett
ms.date: 07/10/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 97bc56a3cfe7383f207a4f621ab3fe8b8dc258ef
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2017
---
# <a name="response-caching"></a><span data-ttu-id="75c55-104">Réponse mise en cache</span><span class="sxs-lookup"><span data-stu-id="75c55-104">Response Caching</span></span>

<span data-ttu-id="75c55-105">Par [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="75c55-105">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="75c55-106">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="75c55-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a><span data-ttu-id="75c55-107">Quelle est la réponse mise en cache</span><span class="sxs-lookup"><span data-stu-id="75c55-107">What is Response Caching</span></span>

<span data-ttu-id="75c55-108">*Réponse mise en cache* renforce les en-têtes liés au cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="75c55-108">*Response caching* adds cache-related headers to responses.</span></span> <span data-ttu-id="75c55-109">Ces en-têtes choisir comment intergiciel (middleware) en cache les réponses, client et proxy.</span><span class="sxs-lookup"><span data-stu-id="75c55-109">These headers specify how you want client, proxy and middleware to cache responses.</span></span> <span data-ttu-id="75c55-110">Réponse mise en cache peut réduire le nombre de demandes que de client ou de proxy permet au serveur web.</span><span class="sxs-lookup"><span data-stu-id="75c55-110">Response caching can reduce the number of requests a client or proxy makes to the web server.</span></span> <span data-ttu-id="75c55-111">Mise en cache de la réponse peut également de réduire la quantité de travail, le serveur web exécute pour générer la réponse.</span><span class="sxs-lookup"><span data-stu-id="75c55-111">Response caching can also reduce the amount of work the web server performs to generate the response.</span></span> 

<span data-ttu-id="75c55-112">L’en-tête HTTP principal utilisé pour la mise en cache est `Cache-Control`.</span><span class="sxs-lookup"><span data-stu-id="75c55-112">The primary HTTP header used for caching is `Cache-Control`.</span></span> <span data-ttu-id="75c55-113">Consultez le [la mise en cache à HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="75c55-113">See the [HTTP 1.1 Caching](https://tools.ietf.org/html/rfc7234#section-5.2) for more information.</span></span> <span data-ttu-id="75c55-114">Directives de cache courantes :</span><span class="sxs-lookup"><span data-stu-id="75c55-114">Common cache directives:</span></span>

* [<span data-ttu-id="75c55-115">public</span><span class="sxs-lookup"><span data-stu-id="75c55-115">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [<span data-ttu-id="75c55-116">private</span><span class="sxs-lookup"><span data-stu-id="75c55-116">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [<span data-ttu-id="75c55-117">Aucun cache</span><span class="sxs-lookup"><span data-stu-id="75c55-117">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [<span data-ttu-id="75c55-118">Pragma</span><span class="sxs-lookup"><span data-stu-id="75c55-118">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)
* [<span data-ttu-id="75c55-119">Varier</span><span class="sxs-lookup"><span data-stu-id="75c55-119">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)

<span data-ttu-id="75c55-120">Le serveur web peut mettre en cache les réponses en ajoutant la réponse mise en cache d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="75c55-120">The web server can cache responses by adding the response caching middleware.</span></span> <span data-ttu-id="75c55-121">Consultez [réponse mise en cache d’intergiciel (middleware)](middleware.md) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="75c55-121">See [Response caching middleware](middleware.md) for more information.</span></span>

## <a name="distributed-cache-tag-helper"></a><span data-ttu-id="75c55-122">Application d’assistance de balise de Cache distribué</span><span class="sxs-lookup"><span data-stu-id="75c55-122">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="75c55-123">Le [application d’assistance de balise de Cache distribué](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) active de la mise en cache distribuée.</span><span class="sxs-lookup"><span data-stu-id="75c55-123">The [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) enables distributed caching.</span></span>


## <a name="responsecache-attribute"></a><span data-ttu-id="75c55-124">Attribut de ResponseCache</span><span class="sxs-lookup"><span data-stu-id="75c55-124">ResponseCache Attribute</span></span>

<span data-ttu-id="75c55-125">Le `ResponseCacheAttribute` spécifie les paramètres nécessaires à la configuration des en-têtes appropriés dans la réponse mise en cache.</span><span class="sxs-lookup"><span data-stu-id="75c55-125">The `ResponseCacheAttribute` specifies the parameters necessary for setting appropriate headers in response caching.</span></span> <span data-ttu-id="75c55-126">Consultez [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) pour obtenir une description des paramètres.</span><span class="sxs-lookup"><span data-stu-id="75c55-126">See [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)  for a description of the parameters.</span></span>

>[!WARNING]
> <span data-ttu-id="75c55-127">Désactiver la mise en cache du contenu qui contient des informations pour les clients authentifiés.</span><span class="sxs-lookup"><span data-stu-id="75c55-127">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="75c55-128">La mise en cache doit être activé uniquement pour le contenu qui ne change pas en fonction de l’identité d’un utilisateur, ou si un utilisateur est connecté.</span><span class="sxs-lookup"><span data-stu-id="75c55-128">Caching should only be enabled for content that does not change based on a user's identity, or whether a user is logged in.</span></span>

<span data-ttu-id="75c55-129">`VaryByQueryKeys string[]`(nécessite ASP.NET Core 1.1.0 et versions ultérieures) : si la valeur, la réponse mise en cache d’intergiciel (middleware) varie la réponse stockée en fonction des valeurs de la liste donnée des clés de requête.</span><span class="sxs-lookup"><span data-stu-id="75c55-129">`VaryByQueryKeys string[]` (requires ASP.NET Core 1.1.0 and higher): When set, the response caching middleware will vary the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="75c55-130">La réponse mise en cache d’intergiciel (middleware) doit être activée pour définir le `VaryByQueryKeys` propriété, sinon une exception runtime est levée.</span><span class="sxs-lookup"><span data-stu-id="75c55-130">The response caching middleware must be enabled to set the `VaryByQueryKeys` property, otherwise a runtime exception will be thrown.</span></span> <span data-ttu-id="75c55-131">Il n’existe aucun en-tête HTTP correspondant pour le `VaryByQueryKeys` propriété.</span><span class="sxs-lookup"><span data-stu-id="75c55-131">There is no corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="75c55-132">Cette propriété est une fonctionnalité HTTP gérée par la réponse mise en cache d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="75c55-132">This property is an HTTP feature handled by the response caching middleware.</span></span> <span data-ttu-id="75c55-133">Pour l’intergiciel (middleware) servir une réponse mise en cache, la chaîne de requête et la valeur de chaîne de requête doivent correspondre à une demande précédente.</span><span class="sxs-lookup"><span data-stu-id="75c55-133">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="75c55-134">Par exemple, considérez la séquence suivante :</span><span class="sxs-lookup"><span data-stu-id="75c55-134">For example, consider the following sequence:</span></span>

| <span data-ttu-id="75c55-135">Requête</span><span class="sxs-lookup"><span data-stu-id="75c55-135">Request</span></span>          | <span data-ttu-id="75c55-136">Résultat</span><span class="sxs-lookup"><span data-stu-id="75c55-136">Result</span></span> |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | <span data-ttu-id="75c55-137">retourné à partir du serveur</span><span class="sxs-lookup"><span data-stu-id="75c55-137">returned from server</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="75c55-138">retourné à partir de l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="75c55-138">returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="75c55-139">retourné à partir du serveur</span><span class="sxs-lookup"><span data-stu-id="75c55-139">returned from server</span></span> |

<span data-ttu-id="75c55-140">La première requête est retournée par le serveur et mis en cache dans l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="75c55-140">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="75c55-141">La deuxième demande est retournée par l’intergiciel (middleware), car la chaîne de requête correspond à la demande précédente.</span><span class="sxs-lookup"><span data-stu-id="75c55-141">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="75c55-142">La troisième requête n’est pas dans le cache de l’intergiciel (middleware), car la valeur de chaîne de requête ne correspond pas à une demande précédente.</span><span class="sxs-lookup"><span data-stu-id="75c55-142">The third request is not in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="75c55-143">Le `ResponseCacheAttribute` est utilisé pour configurer et créer (via `IFilterFactory`) un `ResponseCacheFilter`.</span><span class="sxs-lookup"><span data-stu-id="75c55-143">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a `ResponseCacheFilter`.</span></span> <span data-ttu-id="75c55-144">Le `ResponseCacheFilter` effectue le travail de mise à jour les en-têtes HTTP appropriés et les fonctionnalités de la réponse.</span><span class="sxs-lookup"><span data-stu-id="75c55-144">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="75c55-145">Le filtre :</span><span class="sxs-lookup"><span data-stu-id="75c55-145">The filter:</span></span>

* <span data-ttu-id="75c55-146">Supprime tous les en-têtes existants pour `Vary`, `Cache-Control`, et `Pragma`.</span><span class="sxs-lookup"><span data-stu-id="75c55-146">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="75c55-147">Écrit les en-têtes appropriés en fonction des propriétés définies le `ResponseCacheAttribute`.</span><span class="sxs-lookup"><span data-stu-id="75c55-147">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="75c55-148">Met à jour de la réponse mise en cache de la fonctionnalité HTTP si `VaryByQueryKeys` est défini.</span><span class="sxs-lookup"><span data-stu-id="75c55-148">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="75c55-149">Varier</span><span class="sxs-lookup"><span data-stu-id="75c55-149">Vary</span></span>

<span data-ttu-id="75c55-150">Cet en-tête est écrit uniquement lorsque le `VaryByHeader` est définie.</span><span class="sxs-lookup"><span data-stu-id="75c55-150">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="75c55-151">Il est défini sur la `Vary` valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="75c55-151">It is set to the `Vary` property's value.</span></span> <span data-ttu-id="75c55-152">L’exemple suivant utilise le `VaryByHeader` propriété.</span><span class="sxs-lookup"><span data-stu-id="75c55-152">The following sample uses the `VaryByHeader` property.</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

<span data-ttu-id="75c55-153">Vous pouvez afficher les en-têtes de réponse avec vos outils de réseau des navigateurs.</span><span class="sxs-lookup"><span data-stu-id="75c55-153">You can view the response headers with your browsers network tools.</span></span> <span data-ttu-id="75c55-154">L’illustration suivante montre le F12 Edge de sortie sur le **réseau** onglet lorsque la `About2` méthode d’action est actualisée.</span><span class="sxs-lookup"><span data-stu-id="75c55-154">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed.</span></span> 

![Bord F12 sortie sur la ** réseau ** onglet lorsque la méthode d’action 'About2' est appelée.](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="75c55-156">NoStore et Location.None</span><span class="sxs-lookup"><span data-stu-id="75c55-156">NoStore and Location.None</span></span>

<span data-ttu-id="75c55-157">`NoStore`remplace la plupart des autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="75c55-157">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="75c55-158">Lorsque cette propriété a la valeur `true`, le `Cache-Control` en-tête a la valeur « no-store ».</span><span class="sxs-lookup"><span data-stu-id="75c55-158">When this property is set to `true`, the `Cache-Control` header will be set to "no-store".</span></span> <span data-ttu-id="75c55-159">Si `Location` a la valeur `None`:</span><span class="sxs-lookup"><span data-stu-id="75c55-159">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="75c55-160">`Cache-Control` a la valeur `"no-store, no-cache"`.</span><span class="sxs-lookup"><span data-stu-id="75c55-160">`Cache-Control` is set to `"no-store, no-cache"`.</span></span> 
* <span data-ttu-id="75c55-161">`Pragma` a la valeur `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="75c55-161">`Pragma` is set to `no-cache`.</span></span> 

<span data-ttu-id="75c55-162">Si `NoStore` est `false` et `Location` est `None`, `Cache-Control` et `Pragma` a la valeur `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="75c55-162">If `NoStore` is `false` and `Location` is `None`,  `Cache-Control` and `Pragma` will be set to `no-cache`.</span></span>

<span data-ttu-id="75c55-163">Vous définissez généralement `NoStore` à `true` sur les pages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="75c55-163">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="75c55-164">Exemple :</span><span class="sxs-lookup"><span data-stu-id="75c55-164">For example:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

<span data-ttu-id="75c55-165">Il en résulte dans les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="75c55-165">This will result in the following headers:</span></span>

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="75c55-166">Emplacement et la durée</span><span class="sxs-lookup"><span data-stu-id="75c55-166">Location and Duration</span></span>

<span data-ttu-id="75c55-167">Pour activer la mise en cache, `Duration` doit être définie sur une valeur positive et `Location` doit avoir la valeur `Any` (la valeur par défaut) ou `Client`.</span><span class="sxs-lookup"><span data-stu-id="75c55-167">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="75c55-168">Dans ce cas, le `Cache-Control` en-tête est fixé à la valeur d’emplacement suivie de « max-age » de la réponse.</span><span class="sxs-lookup"><span data-stu-id="75c55-168">In this case, the `Cache-Control` header will be set to the location value followed by the "max-age" of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="75c55-169">`Location`d’options de `Any` et `Client` traduire en `Cache-Control` les valeurs d’en-tête de `public` et `private`, respectivement.</span><span class="sxs-lookup"><span data-stu-id="75c55-169">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="75c55-170">Comme mentionné précédemment, le paramètre `Location` à `None` définira les deux `Cache-Control` et `Pragma` en-têtes à `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="75c55-170">As noted previously, setting `Location` to `None` will set both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="75c55-171">Ci-dessous un exemple montrant les en-têtes produit en définissant `Duration` et en conservant la valeur par défaut `Location` valeur.</span><span class="sxs-lookup"><span data-stu-id="75c55-171">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value.</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

<span data-ttu-id="75c55-172">Génère les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="75c55-172">Produces the following headers:</span></span>

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a><span data-ttu-id="75c55-173">Profils de cache</span><span class="sxs-lookup"><span data-stu-id="75c55-173">Cache Profiles</span></span>

<span data-ttu-id="75c55-174">Au lieu de répéter `ResponseCache` paramètres sur les attributs d’action de contrôleur, les profils de cache peuvent être configurés en tant qu’options lorsque vous configurez MVC dans le `ConfigureServices` méthode dans `Startup`.</span><span class="sxs-lookup"><span data-stu-id="75c55-174">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="75c55-175">Les valeurs d’un profil de cache référencé seront utilisées en tant que les valeurs par défaut par le `ResponseCache` d’attribut et sont remplacées par les propriétés spécifiées sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="75c55-175">Values found in a referenced cache profile will be used as the defaults by the `ResponseCache` attribute, and will be overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="75c55-176">Configuration d’un profil de cache :</span><span class="sxs-lookup"><span data-stu-id="75c55-176">Setting up a cache profile:</span></span>

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

<span data-ttu-id="75c55-177">Faisant référence à un profil de cache :</span><span class="sxs-lookup"><span data-stu-id="75c55-177">Referencing a cache profile:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

<span data-ttu-id="75c55-178">Le `ResponseCache` attribut peut être appliqué à la fois aux actions (méthodes) ainsi que des contrôleurs (classes).</span><span class="sxs-lookup"><span data-stu-id="75c55-178">The `ResponseCache` attribute can be applied both to actions (methods) as well as controllers (classes).</span></span> <span data-ttu-id="75c55-179">Attributs de niveau de la méthode remplacent les paramètres spécifiés dans les attributs de niveau classe.</span><span class="sxs-lookup"><span data-stu-id="75c55-179">Method-level attributes will override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="75c55-180">Dans l’exemple ci-dessus, un attribut de niveau classe spécifie une durée de 30 secondes lors d’un attribut de niveau de la méthode fait référence à un profil de cache avec une durée de 60 secondes.</span><span class="sxs-lookup"><span data-stu-id="75c55-180">In the above example, a class-level attribute specifies a duration of 30 seconds while a method-level attributes references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="75c55-181">L’en-tête résultant :</span><span class="sxs-lookup"><span data-stu-id="75c55-181">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a><span data-ttu-id="75c55-182">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="75c55-182">Additional Resources</span></span>

* [<span data-ttu-id="75c55-183">Mise en cache dans HTTP à partir de la spécification</span><span class="sxs-lookup"><span data-stu-id="75c55-183">Caching in HTTP from the specification</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="75c55-184">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="75c55-184">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
