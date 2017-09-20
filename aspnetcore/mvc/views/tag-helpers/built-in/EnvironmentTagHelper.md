---
title: "Assistance de balise d’environnement dans ASP.NET Core"
author: pkellner
description: "Assistance de balise d’environnement ASP.Net Core définies, y compris toutes les propriétés"
keywords: ASP.NET Core,tag helper
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper
ms.openlocfilehash: e16f942b4a42ef495c7a7daff6724ff071bb35a1
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="04d60-104">Assistance de balise d’environnement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04d60-104">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="04d60-105">Par [Peter Kellner](http://peterkellner.net) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="04d60-105">By [Peter Kellner](http://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="04d60-106">L’application d’assistance de balise environnement restitue conditionnelle son contenu délimité en fonction de l’environnement d’hébergement actuel.</span><span class="sxs-lookup"><span data-stu-id="04d60-106">The Environment Tag Helper conditionally renders its enclosed content based on the current hosting environment.</span></span> <span data-ttu-id="04d60-107">Son seul attribut `names` est une liste séparée par des virgules de l’environnement de noms, que si les mettre en correspondance avec l’environnement actuel, déclenche le contenu entre parenthèses à restituer.</span><span class="sxs-lookup"><span data-stu-id="04d60-107">Its single attribute `names` is a comma separated list of environment names, that if any match to the current environment, will trigger the enclosed content to be rendered.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="04d60-108">Attributs d’assistance des balises d’environnement</span><span class="sxs-lookup"><span data-stu-id="04d60-108">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="04d60-109">noms</span><span class="sxs-lookup"><span data-stu-id="04d60-109">names</span></span>

<span data-ttu-id="04d60-110">Accepte un seul nom environnement d’hébergement ou d’une liste séparée par des virgules de noms d’environnement qui déclenchent le rendu du contenu entre parenthèses d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="04d60-110">Accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="04d60-111">Ces valeurs sont comparées à la valeur actuelle retournée à partir de la propriété statique ASP.NET Core `HostingEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="04d60-111">These value(s) are compared to the current value returned from the ASP.NET Core static property `HostingEnvironment.EnvironmentName`.</span></span>  <span data-ttu-id="04d60-112">Cette valeur est une des valeurs suivantes : **intermédiaire**; **Développement** ou **Production**.</span><span class="sxs-lookup"><span data-stu-id="04d60-112">This value is one of the following: **Staging**; **Development** or **Production**.</span></span> <span data-ttu-id="04d60-113">La comparaison ignore la casse.</span><span class="sxs-lookup"><span data-stu-id="04d60-113">The comparison ignores case.</span></span>

<span data-ttu-id="04d60-114">Un exemple de valide `environment` d’assistance de balise est :</span><span class="sxs-lookup"><span data-stu-id="04d60-114">An example of a valid `environment` tag helper is:</span></span>

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="04d60-115">inclure et exclure des attributs</span><span class="sxs-lookup"><span data-stu-id="04d60-115">include and exclude attributes</span></span>

<span data-ttu-id="04d60-116">ASP.NET Core 2.x ajoute le `include`  &  `exclude` attributs.</span><span class="sxs-lookup"><span data-stu-id="04d60-116">ASP.NET Core 2.x adds the `include` & `exclude` attributes.</span></span> <span data-ttu-id="04d60-117">Ces attributs contrôlent rendu du contenu entre parenthèses basé sur les noms d’environnement hébergement inclus ou exclus.</span><span class="sxs-lookup"><span data-stu-id="04d60-117">These attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include-aspnet-core-20-and-later"></a><span data-ttu-id="04d60-118">inclure le cœur d’ASP.NET 2.0 et versions ultérieur</span><span class="sxs-lookup"><span data-stu-id="04d60-118">include ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="04d60-119">Le `include` propriété a un comportement semblable de la `names` attribut dans la version 1.0 de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04d60-119">The `include` property has a similar behavior of the `names` attribute in ASP.NET Core 1.0.</span></span>

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a><span data-ttu-id="04d60-120">exclure le cœur d’ASP.NET 2.0 et versions ultérieur</span><span class="sxs-lookup"><span data-stu-id="04d60-120">exclude ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="04d60-121">En revanche, le `exclude` propriété permet la `EnvironmentTagHelper` restituer le contenu entre parenthèses pour tous les noms d’environnement hébergement à l’exception de l’enregistrement que vous avez spécifié.</span><span class="sxs-lookup"><span data-stu-id="04d60-121">In contrast, the `exclude` property lets the `EnvironmentTagHelper` render the enclosed content for all hosting environment names except the one(s) that you specified.</span></span>

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a><span data-ttu-id="04d60-122">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="04d60-122">Additional resources</span></span>

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
