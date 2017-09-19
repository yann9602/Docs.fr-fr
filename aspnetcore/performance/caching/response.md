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
# <a name="response-caching"></a>Réponse mise en cache

Par [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), et [Steve Smith](https://ardalis.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a>Quelle est la réponse mise en cache

*Réponse mise en cache* renforce les en-têtes liés au cache des réponses. Ces en-têtes choisir comment intergiciel (middleware) en cache les réponses, client et proxy. Réponse mise en cache peut réduire le nombre de demandes que de client ou de proxy permet au serveur web. Mise en cache de la réponse peut également de réduire la quantité de travail, le serveur web exécute pour générer la réponse. 

L’en-tête HTTP principal utilisé pour la mise en cache est `Cache-Control`. Consultez le [la mise en cache à HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2) pour plus d’informations. Directives de cache courantes :

* [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [Aucun cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)
* [Varier](https://tools.ietf.org/html/rfc7231#section-7.1.4)

Le serveur web peut mettre en cache les réponses en ajoutant la réponse mise en cache d’intergiciel (middleware). Consultez [réponse mise en cache d’intergiciel (middleware)](middleware.md) pour plus d’informations.

## <a name="distributed-cache-tag-helper"></a>Application d’assistance de balise de Cache distribué

Le [application d’assistance de balise de Cache distribué](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) active de la mise en cache distribuée.


## <a name="responsecache-attribute"></a>Attribut de ResponseCache

Le `ResponseCacheAttribute` spécifie les paramètres nécessaires à la configuration des en-têtes appropriés dans la réponse mise en cache. Consultez [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) pour obtenir une description des paramètres.

>[!WARNING]
> Désactiver la mise en cache du contenu qui contient des informations pour les clients authentifiés. La mise en cache doit être activé uniquement pour le contenu qui ne change pas en fonction de l’identité d’un utilisateur, ou si un utilisateur est connecté.

`VaryByQueryKeys string[]`(nécessite ASP.NET Core 1.1.0 et versions ultérieures) : si la valeur, la réponse mise en cache d’intergiciel (middleware) varie la réponse stockée en fonction des valeurs de la liste donnée des clés de requête. La réponse mise en cache d’intergiciel (middleware) doit être activée pour définir le `VaryByQueryKeys` propriété, sinon une exception runtime est levée. Il n’existe aucun en-tête HTTP correspondant pour le `VaryByQueryKeys` propriété. Cette propriété est une fonctionnalité HTTP gérée par la réponse mise en cache d’intergiciel (middleware). Pour l’intergiciel (middleware) servir une réponse mise en cache, la chaîne de requête et la valeur de chaîne de requête doivent correspondre à une demande précédente. Par exemple, considérez la séquence suivante :

| Requête          | Résultat |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | retourné à partir du serveur |
| `http://example.com?key1=value1` | retourné à partir de l’intergiciel (middleware) |
| `http://example.com?key1=value2` | retourné à partir du serveur |

La première requête est retournée par le serveur et mis en cache dans l’intergiciel (middleware). La deuxième demande est retournée par l’intergiciel (middleware), car la chaîne de requête correspond à la demande précédente. La troisième requête n’est pas dans le cache de l’intergiciel (middleware), car la valeur de chaîne de requête ne correspond pas à une demande précédente. 

Le `ResponseCacheAttribute` est utilisé pour configurer et créer (via `IFilterFactory`) un `ResponseCacheFilter`. Le `ResponseCacheFilter` effectue le travail de mise à jour les en-têtes HTTP appropriés et les fonctionnalités de la réponse. Le filtre :

* Supprime tous les en-têtes existants pour `Vary`, `Cache-Control`, et `Pragma`. 
* Écrit les en-têtes appropriés en fonction des propriétés définies le `ResponseCacheAttribute`. 
* Met à jour de la réponse mise en cache de la fonctionnalité HTTP si `VaryByQueryKeys` est défini.

### <a name="vary"></a>Varier

Cet en-tête est écrit uniquement lorsque le `VaryByHeader` est définie. Il est défini sur la `Vary` valeur de la propriété. L’exemple suivant utilise le `VaryByHeader` propriété.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

Vous pouvez afficher les en-têtes de réponse avec vos outils de réseau des navigateurs. L’illustration suivante montre le F12 Edge de sortie sur le **réseau** onglet lorsque la `About2` méthode d’action est actualisée. 

![Bord F12 sortie sur la ** réseau ** onglet lorsque la méthode d’action 'About2' est appelée.](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore et Location.None

`NoStore`remplace la plupart des autres propriétés. Lorsque cette propriété a la valeur `true`, le `Cache-Control` en-tête a la valeur « no-store ». Si `Location` a la valeur `None`:

* `Cache-Control` a la valeur `"no-store, no-cache"`. 
* `Pragma` a la valeur `no-cache`. 

Si `NoStore` est `false` et `Location` est `None`, `Cache-Control` et `Pragma` a la valeur `no-cache`.

Vous définissez généralement `NoStore` à `true` sur les pages d’erreur. Exemple :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

Il en résulte dans les en-têtes suivants :

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Emplacement et la durée

Pour activer la mise en cache, `Duration` doit être définie sur une valeur positive et `Location` doit avoir la valeur `Any` (la valeur par défaut) ou `Client`. Dans ce cas, le `Cache-Control` en-tête est fixé à la valeur d’emplacement suivie de « max-age » de la réponse.

> [!NOTE]
> `Location`d’options de `Any` et `Client` traduire en `Cache-Control` les valeurs d’en-tête de `public` et `private`, respectivement. Comme mentionné précédemment, le paramètre `Location` à `None` définira les deux `Cache-Control` et `Pragma` en-têtes à `no-cache`.

Ci-dessous un exemple montrant les en-têtes produit en définissant `Duration` et en conservant la valeur par défaut `Location` valeur.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

Génère les en-têtes suivants :

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a>Profils de cache

Au lieu de répéter `ResponseCache` paramètres sur les attributs d’action de contrôleur, les profils de cache peuvent être configurés en tant qu’options lorsque vous configurez MVC dans le `ConfigureServices` méthode dans `Startup`. Les valeurs d’un profil de cache référencé seront utilisées en tant que les valeurs par défaut par le `ResponseCache` d’attribut et sont remplacées par les propriétés spécifiées sur l’attribut.

Configuration d’un profil de cache :

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

Faisant référence à un profil de cache :

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

Le `ResponseCache` attribut peut être appliqué à la fois aux actions (méthodes) ainsi que des contrôleurs (classes). Attributs de niveau de la méthode remplacent les paramètres spécifiés dans les attributs de niveau classe.

Dans l’exemple ci-dessus, un attribut de niveau classe spécifie une durée de 30 secondes lors d’un attribut de niveau de la méthode fait référence à un profil de cache avec une durée de 60 secondes.

L’en-tête résultant :

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a>Ressources supplémentaires

* [Mise en cache dans HTTP à partir de la spécification](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
