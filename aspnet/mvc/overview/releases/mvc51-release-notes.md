---
uid: mvc/overview/releases/mvc51-release-notes
title: "Quelles sont les nouveautés dans ASP.NET MVC 5.1 | Documents Microsoft"
author: microsoft
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 9a83a058-9b01-48aa-acce-ec041e694567
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/releases/mvc51-release-notes
msc.type: authoredcontent
ms.openlocfilehash: be10486c9fd39738f44cdda4fedb409058017601
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="whats-new-in-aspnet-mvc-51"></a>Quelles sont les nouveautés dans ASP.NET MVC 5.1
====================
par [Microsoft](https://github.com/microsoft)

Cette rubrique décrit les nouveautés de ASP.NET Web MVC 5.1.

- [Configuration logicielle requise](#SoftwareRequirements)
- [Télécharger](#download)
- [Documentation](#documentation)
- [Nouvelles fonctionnalités dans ASP.NET MVC 5.1](#new-features)

    - [Améliorations du routage d’attribut](#AttributeRouting)
    - [Prise en charge d’amorçage pour les modèles de l’éditeur](#Bootstrap)
    - [Prise en charge de l’énumération dans les vues](#Enum)
    - [Validation non obstrusive pour les attributs de MinLength/MaxLength](#Unobtrusive)
    - [Prise en charge le contexte de 'THI' dans Ajax discrète](#thisContext)
- [Problèmes connus et des modifications avec rupture](#KnownBreakingChanges)- [correctifs de bogues](#bug-fixes)

<a id="SoftwareRequirements"></a>
## <a name="software-requirements"></a>Configuration logicielle

- Visual Studio 2012 : Téléchargement [Web ASP.NET et outils 2013.1 pour Visual Studio 2012](https://go.microsoft.com/fwlink/?LinkId=390062).
- Visual Studio 2013 : Téléchargement [Visual Studio 2013 Update 1](https://go.microsoft.com/fwlink/?LinkId=390064). Cette mise à jour est nécessaire pour la modification des vues de Razor ASP.NET MVC 5.1.

<a id="download"></a>
## <a name="download"></a>Téléchargement

Les fonctions d’exécution sont publiées en tant que packages NuGet lors de la galerie NuGet. Tous les packages de runtime suivent le [contrôle de version sémantique](http://semver.org/) spécification. Le dernier package RTM d’ASP.NET MVC 5.1 a la version suivante : « 5.1.2 ». Vous pouvez installer ou mettre à jour ces packages via [NuGet](http://www.nuget.org/packages/Microsoft.AspNet.Mvc/). Cette version inclut également des packages localisés correspondants sur NuGet.

Vous pouvez installer ou mettre à jour pour les packages NuGet publiés à l’aide de la Console du Gestionnaire de Package NuGet :

[!code-console[Main](mvc51-release-notes/samples/sample1.cmd)]

<a id="documentation"></a>
## <a name="documentation"></a>Documentation

Didacticiels et autres informations sur ASP.NET MVC 5.1 RTM sont disponibles à partir du site web ASP.NET (https://www.asp.net). 

<a id="new-features"></a>
## <a name="new-features-in-aspnet-mvc-51"></a>Nouvelles fonctionnalités dans ASP.NET MVC 5.1

<a id="AttributeRouting"></a>

### <a name="attribute-routing-improvements"></a>Améliorations du routage d’attribut

 Attribut routage maintenant contraintes prend en charge, l’activation de contrôle de version et d’en-tête en fonction de sélection d’itinéraire. Nombreux aspects d’itinéraires d’attribut sont maintenant personnalisables via la `IDirectRouteFactory` interface et `RouteFactoryAttribute` classe. Le préfixe d’itinéraire est désormais extensible via la `IRoutePrefix` interface et `RoutePrefixAttribute` classe. 

<a id="Enum"></a>

### <a name="enum-support-in-views"></a>Prise en charge de l’énumération dans les vues

1. Nouvelle `@Html.EnumDropDownListFor()` méthodes d’assistance. Ceux-ci doivent être utilisés comme la plupart des programmes d’assistance HTML avec l’inconvénient de l’expression doit correspondre à un [enum](https://msdn.microsoft.com/en-us/library/cc138362.aspx) type ou un [Nullable&lt;T&gt; ](https://msdn.microsoft.com/en-us/library/2cf62fcy.aspx) où `T` est un [enum](https://msdn.microsoft.com/en-us/library/cc138362.aspx) type. Utilisez `EnumHelper.IsValidForEnumHelper()` pour vérifier ces exigences.
2. Nouvelle `EnumHelper.GetSelectList()` les méthodes qui retournent un `IList<SelectListItem>`. Cela est utile lorsque vous souhaitez manipuler une liste de sélection avant d’appeler, par exemple, `@Html.DropDownListFor()`, ou lorsque vous souhaitez afficher les noms qui `@Html.EnumDropDownListFor()` montre.

Le code suivant illustre ces API.

[!code-cshtml[Main](mvc51-release-notes/samples/sample2.cshtml)]

Vous pouvez voir un exemple complet [ici](https://aspnet.codeplex.com/SourceControl/latest#Samples/MVC/EnumSample/).

<a id="Bootstrap"></a>

### <a name="bootstrap-support-for-editor-templates"></a>Prise en charge d’amorçage pour les modèles de l’éditeur

Nous avons maintenant autoriser la transmission des attributs HTML dans [EditorFor](https://msdn.microsoft.com/en-us/library/system.web.mvc.html.editorextensions.editorfor(v=vs.100).aspx) comme un [objet anonyme](https://msdn.microsoft.com/en-us/library/bb397696.aspx).

Exemple :

[!code-cshtml[Main](mvc51-release-notes/samples/sample3.cshtml)]

<a id="Unobtrusive"></a>

### <a name="unobtrusive-validation-for-minlengthattribute-and-maxlengthattribute"></a>Validation non obstrusive pour MinLengthAttribute et MaxLengthAttribute

La validation côté client pour les types string et array sera désormais être pris en charge pour les propriétés décorée avec le [MinLength](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.minlengthattribute(v=vs.110).aspx) et [MaxLength](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.maxlengthattribute(v=vs.110).aspx) attributs.

<a id="thisContext"></a>

### <a name="supporting-the-this-context-in-unobtrusive-ajax"></a>Prise en charge le contexte de 'THI' dans Ajax discrète

Les fonctions de rappel (`OnBegin, OnComplete, OnFailure, OnSuccess`) sera désormais en mesure de trouver l’élément appelant via le `this` contexte. Exemple :

[!code-html[Main](mvc51-release-notes/samples/sample4.html)]

<a id="KnownBreakingChanges"></a>

## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

### <a name="attribute-routing"></a>Routage d’attributs

Ambiguïtés dans les correspondances de routage attribut signalent une erreur plutôt que de choisir la première correspondance.

Itinéraires d’attribut sont interdit d’utiliser le `{controller}` paramètre et d’utiliser le `{action}` paramètre itinéraires placés sur les actions. Utilise ces paramètres pourrait entraîner très probablement les ambiguïtés. 

### <a name="scaffolding-mvcweb-api-into-a-project-with-51-packages-results-in-50-packages-for-ones-that-dont-already-exist-in-the-project"></a>API Web/MVC de génération de modèles automatique dans un projet avec des résultats packages 5.1 dans les 5.0 packages pour ceux qui n’existent pas déjà dans le projet

Mise à jour les packages NuGet pour RTM d’ASP.NET MVC 5.1 ne met pas à jour les outils de Visual Studio, tels que de la génération de modèles automatique ASP.NET ou le modèle de projet d’Application Web ASP.NET. Ils utilisent la version précédente des packages de runtime ASP.NET (5.0.0.0). Par conséquent, la génération de modèles automatique ASP.NET installera la version précédente (5.0.0.0) des packages requis, si elles ne sont pas déjà disponibles dans vos projets. Toutefois, la génération de modèles automatique ASP.NET dans Visual Studio 2013 RTM ou une mise à jour 1 ne remplace pas les packages plus récentes dans vos projets. Si vous utilisez la génération de modèles automatique ASP.NET après la mise à jour les packages de vos projets Web API 2.1 ou ASP.NET MVC 5.1, vérifiez que les versions des API Web ASP.NET MVC sont cohérentes. 

### <a name="syntax-highlighting-for-razor-views-in-visual-studio-2013"></a>Mise en surbrillance de syntaxe pour les vues Razor dans Visual Studio 2013

Si vous mettez à jour vers RTM d’ASP.NET MVC 5.1 sans mettre à jour de Visual Studio 2013, vous n’obtiendrez pas prise en charge de l’éditeur Visual Studio pour mettre en surbrillance de syntaxe lors de la modification des vues Razor. Vous devez mettre à jour Visual Studio 2013 pour obtenir cette prise en charge. 

### <a name="type-renames"></a>Changements de noms de type

Parmi les types utilisés pour l’extensibilité de routage attribut sont renommés dans RTM 5.1.

| **Ancien nom de Type (5.1 RC)** | **Nouveau Type de nom (5.1 RTM)** |
| --- | --- |
| IDirectRouteProvider | IDirectRouteFactory |
| RouteProviderAttribute | RouteFactoryAttribute |
| DirectRouteProviderContext | DirectRouteFactoryContext |

<a id="bug-fixes"></a>
## <a name="bug-fixes"></a>Correctifs de bogues

Cette version inclut également plusieurs résolutions de bogue. Vous trouverez la liste complète ici :

- [5.1.0 package de](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=Closed&amp;type=All&amp;priority=All&amp;release=v5.1%20Preview|v5.1%20RTM&amp;assignedTo=All&amp;component=MVC&amp;sortField=AssignedTo&amp;sortDirection=Ascending&amp;page=0&amp;reasonClosed=Fixed)
- [5.1.1 package de](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=All&amp;type=All&amp;priority=All&amp;release=v5.1.1%20RTM&amp;assignedTo=All&amp;component=MVC&amp;sortField=AssignedTo&amp;sortDirection=Ascending&amp;page=0&amp;reasonClosed=Fixed)

Le 5.1.2 package contient des mises à jour IntelliSense, mais aucune des correctifs de bogues.
