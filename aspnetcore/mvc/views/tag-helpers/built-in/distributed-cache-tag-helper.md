---
title: "Distributed d’assistance de balise de Cache | Documents Microsoft"
author: pkellner
description: "Montre comment travailler avec l’application d’assistance de balise de Cache"
keywords: ASP.NET Core,tag helper
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a022
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 462c3775677924fc7b9b715cd6de75fe53ada89e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="distributed-cache-tag-helper"></a>Application d’assistance de balise de Cache distribué

Par [Peter Kellner](http://peterkellner.net) 


L’application d’assistance de balise de Cache distribué permet d’améliorer considérablement les performances de votre application ASP.NET Core en mettant en cache de son contenu à une source de cache distribué.

L’assistance de balise de Cache distribué hérite de la même classe de base que l’application d’assistance de balise de Cache.  Tous les attributs associés à l’application d’assistance de balise de Cache fonctionne également sur l’assistance de balise distribué.


L’assistance de balise de Cache distribué suit le **principe de dépendances explicites** appelé **Injection de constructeur**.  Plus précisément, le `IDistributedCache` conteneur d’interface est passée dans le constructeur de la de Distributed balise Cache l’assistance.  Si aucune implémentation concrète de `IDistributedCache` a été créé dans `ConfigureServices`, se trouve généralement dans startup.cs, puis l’assistance de balise de Cache distribué utilisera le même fournisseur en mémoire pour le stockage des données mises en cache en tant que l’application d’assistance de balise de Cache de base.

## <a name="distributed-cache-tag-helper-attributes"></a>Distributed des attributs de programme d’assistance de balise de Cache

- - -

### <a name="enabled-expires-on-expires-after-expires-sliding-vary-by-header-vary-by-query-vary-by-route-vary-by-cookie-vary-by-user-vary-by-priority"></a>activé expire sur expire après expiration coulissant varient en fonction de l’en-tête varient par requête varient par itinéraire varient par cookie varient par utilisateur varient-par priorité

Consultez d’assistance de balise de Cache pour les définitions. Application d’assistance de balise de Cache distribué hérite de la même classe que l’application d’assistance de balise de Cache tous ces attributs sont communs à partir de l’application d’assistance de balise de Cache.

- - -

### <a name="name-required"></a>nom (obligatoire)

| Type d’attribut    | Exemple de valeur     |
|----------------   |----------------   |
| chaîne    | « my-distributed-cache-unique-key-101 »     |

Requis `name` attribut est utilisé comme une clé pour que le cache stocké pour chaque instance d’une assistance de balise de Cache distribué.  Contrairement à la base du Cache balise d’assistance qui affecte une clé à chaque instance d’assistance de balise de Cache basée sur le nom de la page Razor et l’emplacement de l’application d’assistance de balise dans la page razor, l’assistance de balise de Cache distribué base uniquement sa clé sur l’attribut`name`

Exemple d’utilisation :

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Distributed Cache balise d’assistance IDistributedCache implémentations

Il existe deux implémentations de `IDistributedCache` intégrée à ASP.NET Core.  Une est basée sur **Sql Server** et l’autre est basée sur **Redis**. Vous trouverez des détails de ces implémentations à la ressource référencée ci-dessous nommée « utilisation d’un cache distribué ». Les deux implémentations impliquent la définition d’une instance de `IDistributedCache` dans du ASP.NET Core **startup.cs**.

Il n’existe aucun attribut de balise associés spécifiquement à l’aide d’une implémentation spécifique de `IDistributedCache`.



- - -



## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
