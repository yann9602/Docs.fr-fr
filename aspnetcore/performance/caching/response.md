---
title: "Réponse mise en cache dans ASP.NET Core"
author: rick-anderson
description: "Découvrez comment utiliser la réponse mise en cache à faible bande passante et améliorer les performances des applications ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 09/20/2017
ms.prod: asp.net-core
ms.topic: article
uid: performance/caching/response
ms.openlocfilehash: c38f9b9a1bf1c523951e2cf1f3070858fe5daf04
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="response-caching-in-aspnet-core"></a>Réponse mise en cache dans ASP.NET Core

Par [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), et [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Réponse mise en cache [n’est pas pris en charge dans les Pages Razor avec ASP.NET Core 2.0](https://github.com/aspnet/Mvc/issues/6437). Cette fonctionnalité sera être pris en charge dans les [ASP.NET Core 2.1 version](https://github.com/aspnet/Home/wiki/Roadmap).
  
[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

Réponse mise en cache réduit le nombre de demandes que de client ou de proxy permet à un serveur web. Réponse mise en cache réduit également la quantité de travail, le serveur web exécute pour générer une réponse. Mise en cache de la réponse est contrôlé par des en-têtes qui spécifient comment vous souhaitez que client, le proxy et intergiciel (middleware) en cache les réponses.

Le serveur web peut mettre en cache les réponses lorsque vous ajoutez [intergiciel (middleware) de réponse mise en cache](xref:performance/caching/middleware).

## <a name="http-based-response-caching"></a>Mise en cache de la réponse HTTP

Le [spécification de la mise en cache à HTTP 1.1](https://tools.ietf.org/html/rfc7234) décrit le comportement de caches d’Internet. L’en-tête HTTP principal utilisé pour la mise en cache est [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), qui est utilisé pour spécifier le cache *directives*. Les directives de contrôlent le comportement de mise en comme les demandes de parvenir à partir de clients aux serveurs et les réponses de parvenir à partir de serveurs aux clients. Déplacent des demandes et réponses via des serveurs proxy et les serveurs proxy doivent être conforme à la spécification de la mise en cache à HTTP 1.1.

Common `Cache-Control` directives sont affichés dans le tableau suivant.

| Directive                                                       | Action |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Un cache peut stocker la réponse. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | La réponse ne doit pas être stockée par un cache partagé. Un cache privé peut stocker et réutiliser la réponse. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Le client n’accepte pas de réponse dont âge est supérieur au nombre de secondes spécifié. Exemples : `max-age=60` (60 secondes), `max-age=2592000` (1 mois) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Sur les demandes**: un cache ne doit pas utiliser de réponse stockée pour satisfaire la demande. Remarque : Le serveur d’origine génère à nouveau la réponse pour le client et l’intergiciel (middleware) met à jour la réponse stockée dans son cache.<br><br>**Sur les réponses**: la réponse ne doit pas être utilisée pour une demande ultérieure sans validation sur le serveur d’origine. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Sur les demandes**: un cache ne doit pas stocker la demande.<br><br>**Sur les réponses**: un cache ne doit pas stocker n’importe quelle partie de la réponse. |

Autres en-têtes de cache qui jouent un rôle dans la mise en cache sont affichés dans le tableau suivant.

| Header                                                     | Fonction |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | Une estimation de la durée en secondes écoulées depuis la réponse a été générée ou validée sur le serveur d’origine. |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | Date/heure après laquelle la réponse est considérée comme obsolète. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe pour descendante compatibilité avec HTTP/1.0 met en cache pour le paramètre `no-cache` comportement. Si le `Cache-Control` en-tête est présent, le `Pragma` en-tête est ignoré. |
| [Varier](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Spécifie qu’une réponse mise en cache ne doit pas être envoyée tant que tous les de la `Vary` correspondent à des champs d’en-tête dans la demande d’origine de la réponse mise en cache et la nouvelle demande. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Directives de contrôle de Cache de demande de points de mise en cache basée sur HTTP

Le [spécification de la mise en cache à HTTP 1.1 pour l’en-tête Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requiert un cache d’honorer valide `Cache-Control` en-tête envoyé par le client. Un client peut envoyer des demandes avec un `no-cache` valeur d’en-tête et de forcer le serveur génère une nouvelle réponse pour chaque demande.

Toujours en respectant client `Cache-Control` les en-têtes de demande de sens si vous envisagez de l’objectif de la mise en cache HTTP. Sous la spécification officielle, la mise en cache vise à réduire la latence et la charge réseau de satisfaire les demandes sur un réseau de clients, les proxies et les serveurs. Il n’est pas nécessairement un moyen de contrôler la charge sur un serveur d’origine.

Aucun contrôle n’est en cours développeur sur ce comportement de mise en cache lorsque vous utilisez la [intergiciel (middleware) de réponse mise en cache](xref:performance/caching/middleware) , car l’intergiciel (middleware) est conforme à la mise en cache de la spécification officielle. [Améliorations futures de l’intergiciel (middleware)](https://github.com/aspnet/ResponseCaching/issues/96) autorise la configuration de l’intergiciel (middleware) pour ignorer une demande de `Cache-Control` en-tête lorsque vous décidez de traiter une réponse mise en cache. Cela vous propose une opportunité afin de mieux contrôler la charge sur votre serveur lorsque vous utilisez l’intergiciel (middleware).

## <a name="other-caching-technology-in-aspnet-core"></a>Autres technologies de mise en cache dans ASP.NET Core

### <a name="in-memory-caching"></a>La mise en cache en mémoire

Mise en cache utilise la mémoire serveur pour stocker les données mises en cache. Ce type de mise en cache est adapté à un ou plusieurs serveurs à l’aide de *sessions rémanentes*. Sessions rémanentes signifie que les demandes des clients sont toujours acheminés vers le même serveur pour traitement.

Pour plus d’informations, consultez [Introduction à la mise en cache dans ASP.NET Core](xref:performance/caching/memory).

### <a name="distributed-cache"></a>Cache distribué

Utiliser un cache distribué pour stocker des données en mémoire lorsque l’application est hébergée dans une batterie de serveurs cloud ou le serveur. Le cache est partagé entre les serveurs qui traitent les demandes. Un client peut soumettre une demande traitée par n’importe quel serveur dans le groupe et mis en cache des données pour le client est disponible. ASP.NET Core offre SQL Server et les caches Redis distribué.

Pour plus d’informations, consultez [fonctionne avec un cache distribué](xref:performance/caching/distributed).

### <a name="cache-tag-helper"></a>Application d’assistance de balise de cache

Vous pouvez mettre en cache le contenu à partir d’une vue MVC ou de la Page Razor avec l’application d’assistance de balise de Cache. L’application d’assistance de balise de Cache utilise la mise en cache en mémoire pour stocker les données.

Pour plus d’informations, consultez [application d’assistance de balise de Cache dans ASP.NET MVC de base](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="distributed-cache-tag-helper"></a>Application d’assistance de balise de Cache distribué

Vous pouvez mettre en cache le contenu à partir d’une vue MVC ou de la Page Razor dans les scénarios de batterie de serveurs web ou distribuée avec l’application d’assistance de balise de Cache distribué. L’assistance de balise de Cache distribué utilise SQL Server ou Redis pour stocker les données.

Pour plus d’informations, consultez [assistance de balise de Cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).

## <a name="responsecache-attribute"></a>Attribut de ResponseCache

Le `ResponseCacheAttribute` spécifie les paramètres nécessaires à la configuration des en-têtes appropriés dans la réponse mise en cache. Consultez [ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) pour obtenir une description des paramètres.

> [!WARNING]
> Désactiver la mise en cache du contenu qui contient des informations pour les clients authentifiés. La mise en cache doit être activée pour le contenu qui ne change pas en fonction de l’identité d’un utilisateur ou qu’un utilisateur est connecté.

`VaryByQueryKeys string[]`(nécessite ASP.NET Core 1.1 et versions ultérieures) : si la valeur, l’intergiciel (middleware) réponse mise en cache varie selon la réponse stockée par les valeurs de la liste donnée des clés de requête. L’intergiciel (middleware) réponse mise en cache doit être activé pour définir le `VaryByQueryKeys` propriété ; sinon, une exception runtime est levée. Il n’existe aucun en-tête HTTP correspondant pour le `VaryByQueryKeys` propriété. Cette propriété est une fonctionnalité HTTP gérée par l’intergiciel (middleware) réponse mise en cache. Pour l’intergiciel (middleware) servir une réponse mise en cache, la chaîne de requête et la valeur de chaîne de requête doivent correspondre à une demande précédente. Par exemple, considérez la séquence des demandes et les résultats présentés dans le tableau suivant.

| Demande                          | Résultat                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | retourné à partir du serveur     |
| `http://example.com?key1=value1` | retourné à partir de l’intergiciel (middleware) |
| `http://example.com?key1=value2` | retourné à partir du serveur     |

La première requête est retournée par le serveur et mis en cache dans l’intergiciel (middleware). La deuxième demande est retournée par l’intergiciel (middleware), car la chaîne de requête correspond à la demande précédente. La troisième requête n’est pas dans le cache de l’intergiciel (middleware), car la valeur de chaîne de requête ne correspond pas à une demande précédente. 

Le `ResponseCacheAttribute` est utilisé pour configurer et créer (via `IFilterFactory`) un `ResponseCacheFilter`. Le `ResponseCacheFilter` effectue le travail de mise à jour les en-têtes HTTP appropriés et les fonctionnalités de la réponse. Le filtre :

* Supprime tous les en-têtes existants pour `Vary`, `Cache-Control`, et `Pragma`. 
* Écrit les en-têtes appropriés en fonction des propriétés définies le `ResponseCacheAttribute`. 
* Met à jour de la réponse mise en cache de la fonctionnalité HTTP si `VaryByQueryKeys` est défini.

### <a name="vary"></a>Varier

Cet en-tête est écrit uniquement lorsque le `VaryByHeader` est définie. Il est défini sur la `Vary` valeur de la propriété. L’exemple suivant utilise le `VaryByHeader` propriété :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

Vous pouvez afficher les en-têtes de réponse avec les outils de réseau de votre navigateur. L’illustration suivante montre le F12 Edge de sortie sur le **réseau** onglet lorsque la `About2` méthode d’action est actualisée :

![Bord F12 sortie dans l’onglet réseau lorsque la méthode d’action About2 est appelée.](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore et Location.None

`NoStore`remplace la plupart des autres propriétés. Lorsque cette propriété a la valeur `true`, le `Cache-Control` en-tête est défini sur `no-store`. Si `Location` a la valeur `None`:

* `Cache-Control` a la valeur `no-store,no-cache`.
* `Pragma` a la valeur `no-cache`.

Si `NoStore` est `false` et `Location` est `None`, `Cache-Control` et `Pragma` ont la valeur `no-cache`.

Vous définissez généralement `NoStore` à `true` sur les pages d’erreur. Exemple :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

Ainsi, les en-têtes suivants :

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Emplacement et la durée

Pour activer la mise en cache, `Duration` doit être définie sur une valeur positive et `Location` doit avoir la valeur `Any` (la valeur par défaut) ou `Client`. Dans ce cas, le `Cache-Control` en-tête est défini sur la valeur de l’emplacement suivie du `max-age` de la réponse.

> [!NOTE]
> `Location`d’options de `Any` et `Client` traduire en `Cache-Control` les valeurs d’en-tête de `public` et `private`, respectivement. Comme mentionné précédemment, le paramètre `Location` à `None` définit les deux `Cache-Control` et `Pragma` en-têtes à `no-cache`.

Ci-dessous un exemple montrant les en-têtes produit en définissant `Duration` et en conservant la valeur par défaut `Location` valeur :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

Cela génère l’en-tête suivant :

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>Profils de cache

Au lieu de répéter `ResponseCache` paramètres sur les attributs d’action de contrôleur, les profils de cache peuvent être configurés en tant qu’options lorsque vous configurez MVC dans le `ConfigureServices` méthode dans `Startup`. Les valeurs d’un profil de cache référencé sont utilisées en tant que les valeurs par défaut par le `ResponseCache` d’attribut et sont remplacées par les propriétés spécifiées sur l’attribut.

Configuration d’un profil de cache :

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

Faisant référence à un profil de cache :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

Le `ResponseCache` attribut peut être appliqué à la fois aux actions (méthodes) et contrôleurs (classes). Attributs de niveau de la méthode remplacent les paramètres spécifiés dans les attributs de niveau classe.

Dans l’exemple ci-dessus, un attribut de niveau classe spécifie une durée de 30 secondes, pendant un attribut de niveau de la méthode fait référence à un profil de cache avec une durée de 60 secondes.

L’en-tête résultant :

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Mise en cache dans HTTP à partir de la spécification](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Utilisation d’un cache distribué](xref:performance/caching/distributed)
* [Détecter les modifications à l’aide de jetons de modification](xref:fundamentals/primitives/change-tokens)
* [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
* [Tag Helper de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
