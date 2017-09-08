---
title: "Assistance de balise d’environnement dans ASP.NET Core"
author: pkellner
description: "Assistance de balise d’environnement ASP.Net Core définies, y compris toutes les propriétés"
keywords: "ASP.NET Core, d’assistance de balise"
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper
ms.openlocfilehash: 5870d9ebd02becf29f892c91310022d3b9a6b7af
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
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