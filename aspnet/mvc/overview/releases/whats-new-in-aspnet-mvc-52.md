---
uid: mvc/overview/releases/whats-new-in-aspnet-mvc-52
title: "Quelles sont les nouveautés dans ASP.NET MVC 5.2 | Documents Microsoft"
author: microsoft
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/25/2014
ms.topic: article
ms.assetid: 97972587-2720-48b4-b158-f35f2e855fbf
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/releases/whats-new-in-aspnet-mvc-52
msc.type: authoredcontent
ms.openlocfilehash: 94f6131fdb86d1694c1f563c5f6806f119c71266
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="whats-new-in-aspnet-mvc-52"></a>Quelles sont les nouveautés dans ASP.NET MVC 5.2
====================
par [Microsoft](https://github.com/microsoft)

Cette rubrique décrit les nouveautés de ASP.NET MVC 5.2, [Microsoft.AspNet.MVC 5.2.2](#52) et [version bêta d’ASP.NET MVC 5.2.3](#mvc523Beta)

- [Configuration logicielle requise](#softRequire)
- [Télécharger](#download)
- [Documentation](#documentation)
- [Nouvelles fonctionnalités dans ASP.NET MVC 5.2](#new-features)

    - [Améliorations du routage d’attribut](#attributerouting)
- [Problèmes connus et les modifications avec rupture](#knownbreakingchanges)
- [Correctifs de bogues](#bug-fixes)
- [Microsoft.AspNet.MVC 5.2.2](#52)

<a id="softRequire"></a>
## <a name="software-requirements"></a>Configuration logicielle

- Visual Studio 2012 : Téléchargement [Web ASP.NET et outils 2013.1 pour Visual Studio 2012](https://go.microsoft.com/fwlink/?LinkId=390062).
- Visual Studio 2013 : Téléchargement [mise à jour de Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=390064) ou une version ultérieure. Cette mise à jour est nécessaire pour la modification des vues de Razor ASP.NET MVC 5.2.

<a id="download"></a>
## <a name="download"></a>Téléchargement

Les fonctions d’exécution sont publiées en tant que packages NuGet lors de la galerie NuGet. Tous les packages de runtime suivent le [contrôle de version sémantique](http://semver.org/) spécification. Le dernier package d’ASP.NET MVC 5.2 a la version suivante : « 5.2.0 ». Vous pouvez installer ou mettre à jour ces packages via [NuGet](http://www.nuget.org/packages/Microsoft.AspNet.Mvc/). Cette version inclut également des packages localisés correspondants sur NuGet.

Vous pouvez installer ou mettre à jour pour les packages NuGet publiés à l’aide de la Console du Gestionnaire de Package NuGet :

Install-Package Microsoft.AspNet.Mvc-Version 5.2.0

<a id="documentation"></a>
## <a name="documentation"></a>Documentation

Didacticiels et autres informations sur ASP.NET MVC 5.2 sont disponibles à partir du site web ASP.NET ([https://www.asp.net/mvc](../../index.md)).

<a id="new-features"></a>
## <a name="new-features-in-aspnet-mvc-52"></a>Nouvelles fonctionnalités dans ASP.NET MVC 5.2

<a id="attributerouting"></a>
### <a name="attribute-routing-improvements"></a>Améliorations du routage d’attribut

Attribut routage maintenant fournit un point d’extensibilité appelé IDirectRouteProvider, ce qui permet un contrôle total sur la façon dont les itinéraires d’attribut sont détectées et configurés. Un IDirectRouteProvider est chargé de fournir une liste des actions et les contrôleurs, ainsi que les informations d’itinéraire associés pour spécifier exactement quelle configuration de routage est souhaitée pour ces actions. Une implémentation d’un IDirectRouteProvider peut être spécifiée lors de l’appel MapAttributes/MapHttpAttributeRoutes.

Personnalisation de IDirectRouteProvider sera plus simple, en étendant notre implémentation par défaut, DefaultDirectRouteProvider. Cette classe fournit des méthodes virtuelles substituables distincts pour modifier la logique de détection des attributs, création d’entrées d’itinéraire et découvrir le préfixe d’itinéraire et au préfixe de zone.

La nouvel attribut routage d’extensibilité de IDirectRouteProvider, un utilisateur peut effectuer les tâches suivantes :

1. Prend en charge l’héritage des itinéraires d’attribut. Par exemple, dans le scénario suivant contrôleurs Blog et magasin utilisent une convention d’itinéraire d’attribut qui est définie par le BaseController. 

    [!code-csharp[Main](whats-new-in-aspnet-mvc-52/samples/sample1.cs)]
2. Générer automatiquement des noms d’itinéraires pour les itinéraires d’attribut. 

    [!code-csharp[Main](whats-new-in-aspnet-mvc-52/samples/sample2.cs)]
3. Modifier les préfixes d’itinéraire dans un emplacement centralisé avant que les itinéraires sont ajoutés à la table d’itinéraires.
4. Filtrer les contrôleurs sur lequel vous souhaitez le routage d’attribut pour rechercher. Nous espérons bientôt blog sur 3 et 4.

### <a name="facebook-fixes-for-changed-api-surface"></a>Correctifs de Facebook pour surface modifiée de l’API

Le package MVC Facebook [a été interrompue](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=All&amp;type=All&amp;priority=All&amp;release=v5.2%20RC&amp;assignedTo=All&amp;component=Facebook&amp;sortField=AssignedTo&amp;sortDirection=Ascending&amp;page=0&amp;reasonClosed=All) en raison de quelques modifications d’API apportées par Facebook. Nous publierons également un nouveau package de Facebook (Microsoft.AspNet.Facebook 1.0.0) pour résoudre ces problèmes.

<a id="knownbreakingchanges"></a>
## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

### <a name="scaffolding-mvcweb-api-into-a-project-with-520-packages-results-in-512-packages-for-ones-that-dont-already-exist-in-the-project"></a>API Web/MVC de génération de modèles automatique dans un projet avec 5.2.0 résultats packages dans 5.1.2 packages pour ceux qui n’existent pas déjà dans le projet

Mise à jour les packages NuGet pour ASP.NET MVC 5.2.0 ne met pas à jour les outils de Visual Studio, tels que de la génération de modèles automatique ASP.NET ou le modèle de projet d’Application Web ASP.NET. Ils utilisent la version précédente des packages de runtime ASP.NET (par exemple, 5.1.2 dans Update 2). Par conséquent, la génération de modèles automatique ASP.NET installera la version précédente (par exemple, 5.1.2 dans Update 2) des packages requis, si elles ne sont pas déjà disponibles dans vos projets. Toutefois, la génération de modèles automatique ASP.NET dans Visual Studio 2013 RTM ou une mise à jour 1 ne remplace pas les packages plus récentes dans vos projets. Si vous utilisez la génération de modèles automatique ASP.NET après la mise à jour les packages de vos projets Web API 2.2 ou ASP.NET MVC 5.2, assurez-vous que les versions des API Web ASP.NET MVC sont cohérentes.

### <a name="microsoftjqueryunobtrusivevalidation-nuget-package-installation-fails-because-it-is-unable-to-find-a-version-of-microsoftjqueryunobtrusivevalidation-compatible-to-jquery-141"></a>Installation du package NuGet de Microsoft.jQuery.Unobtrusive.Validation échoue, car il est impossible de trouver une version de Microsoft.jQuery.Unobtrusive.Validation compatible avec jQuery 1.4.1

Microsoft.jQuery.Unobtrusive.Validation requiert jQuery &gt;= 1.8 et jQuery.Validation &gt;= 1.8. But,jQuery.validation (1,8) doit jQuery (&#8805; 1.3.2 &amp; &amp; &#8804; 1.6). Pour cette raison, lorsque NuGet installe les JQuery 1.8 et jQuery.Validation 1.8 en même temps, il échoue. Lorsque vous rencontrez ce problème, vous pouvez simplement mettre à jour la version de jQuery.Validation à &gt; =  [1.8.0.1](https://www.nuget.org/packages/jQuery.Validation/1.8.0.1) qui a l’extrémité jQuery fixe tout d’abord, vous devez être en mesure d’installer Microsoft.jQuery.Unobtrusive.Validation.

### <a name="the-jqueryvalidation-nuget-package-version-1130-does-not-recognize-some-international-email-addresses"></a>Jquery. Version du package nuget validation 1.13.0 ne reconnaît pas certaines adresses de messagerie internationales

version du package nuget jQuery.Validation 1.11.1 est la dernière version connue qui reconnaît suivant des adresses de messagerie valide. Les versions ultérieures n’est peut-être pas en mesure de les identifier. Exemple :

Standard adresse internationalisation (IAE) de messagerie (par exemple, [&#29992; &#25143;@domain.com ](mailto:&#29992;&#25143;@domain.com))   
 EAI + international IRI (Resource Identifiers) (par exemple,., [&#29992; &#25143; @&#1076; &#1086; &#1084; &#1077; &#1085;. &#1088; &#1092; ](mailto:&#29992;&#25143;@&#1076;&#1086;&#1084;&#1077;&#1085;.&#1088;&#1092;))

Le problème est signalé au [https://github.com/jzaefferer/jquery-validation/issues/1222](https://github.com/jzaefferer/jquery-validation/issues/1222)

### <a name="syntax-highlighting-for-razor-views-in-visual-studio-2013"></a>Mise en surbrillance de syntaxe pour les vues Razor dans Visual Studio 2013

Si vous mettez à jour ASP.NET MVC 5.2 sans mettre à jour de Visual Studio 2013, vous n’obtiendrez pas prise en charge de l’éditeur Visual Studio pour mettre en surbrillance de syntaxe lors de la modification des vues Razor. Vous devez mettre à jour Visual Studio 2013 pour obtenir cette prise en charge.

<a id="bug-fixes"></a>
## <a name="bug-fixes-and-minor-feature-updates"></a>Correctifs de bogues et mises à jour mineures de fonctionnalité

Cette version inclut également plusieurs correctifs de bogues et les fonctionnalités mineures mises à jour. Vous trouverez la liste complète ici :

- [package 5.2](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=Closed&amp;type=All&amp;priority=All&amp;release=v5.2%20RC&amp;assignedTo=All&amp;component=MVC&amp;sortField=AssignedTo&amp;sortDirection=Ascending&amp;page=0&amp;reasonClosed=Fixed)

<a id="52"></a>
## <a name="microsoftaspnetmvc-522"></a>Microsoft.AspNet.MVC 5.2.2

Cette version ne dispose de toutes les nouvelles fonctionnalités ou des correctifs de bogues dans MVC. Nous avons apporté un [modifier dans les Pages Web](https://blogs.msdn.com/b/webdev/archive/2014/07/28/announcing-the-beta-release-of-web-pages-3-2-1.aspx) pour une amélioration significative des performances et ont ensuite effectuer la mise à jour de tous les autres packages dépendants que nous avons pour dépendent de cette nouvelle version de Web Pages.

<a id="mvc523Beta"></a>
## <a name="aspnet-mvc-523-beta"></a>Version bêta d’ASP.NET MVC 5.2.3

Vous pouvez lire sur la version [ici](https://blogs.msdn.com/b/webdev/archive/2014/12/17/asp-net-mvc-5-2-3-web-pages-5-2-3-and-web-api-5-2-3-beta-releases.aspx). Cette version contient uniquement les correctifs de bogues. Vous pouvez utiliser [cette requête](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&amp;status=Closed&amp;type=All&amp;priority=All&amp;release=v5.2.3%20Beta&amp;assignedTo=All&amp;component=MVC&amp;sortField=LastUpdatedDate&amp;sortDirection=Descending&amp;page=0&amp;reasonClosed=Fixed) pour afficher la liste des problèmes résolus dans cette version.
