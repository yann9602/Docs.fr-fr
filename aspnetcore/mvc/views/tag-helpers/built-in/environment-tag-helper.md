---
title: "Assistance de balise d’environnement dans ASP.NET Core"
author: pkellner
description: "Assistance de balise d’environnement ASP.NET Core définies, y compris toutes les propriétés"
keywords: ASP.NET Core,tag helper
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 2639e4d7494e752462a1a2cb0648042a2d2d06ec
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Assistance de balise d’environnement dans ASP.NET Core

Par [Peter Kellner](http://peterkellner.net) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

L’application d’assistance de balise environnement restitue conditionnelle son contenu délimité en fonction de l’environnement d’hébergement actuel. Son seul attribut `names` est une liste séparée par des virgules de l’environnement de noms, que si les mettre en correspondance avec l’environnement actuel, déclenche le contenu entre parenthèses à restituer.

## <a name="environment-tag-helper-attributes"></a>Attributs d’assistance des balises d’environnement

### <a name="names"></a>noms

Accepte un seul nom environnement d’hébergement ou d’une liste séparée par des virgules de noms d’environnement qui déclenchent le rendu du contenu entre parenthèses d’hébergement.

Ces valeurs sont comparées à la valeur actuelle retournée à partir de la propriété statique ASP.NET Core `HostingEnvironment.EnvironmentName`.  Cette valeur est une des valeurs suivantes : **intermédiaire**; **Développement** ou **Production**. La comparaison ignore la casse.

Un exemple de valide `environment` d’assistance de balise est :

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a>inclure et exclure des attributs

ASP.NET Core 2.x ajoute le `include`  &  `exclude` attributs. Ces attributs contrôlent rendu du contenu entre parenthèses basé sur les noms d’environnement hébergement inclus ou exclus.

### <a name="include-aspnet-core-20-and-later"></a>inclure le cœur d’ASP.NET 2.0 et versions ultérieur

Le `include` propriété a un comportement semblable de la `names` attribut dans la version 1.0 de ASP.NET Core.

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a>exclure le cœur d’ASP.NET 2.0 et versions ultérieur

En revanche, le `exclude` propriété permet la `EnvironmentTagHelper` restituer le contenu entre parenthèses pour tous les noms d’environnement hébergement à l’exception de l’enregistrement que vous avez spécifié.

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
