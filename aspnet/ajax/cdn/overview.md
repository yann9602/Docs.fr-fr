---
uid: ajax/cdn/overview
title: "Réseau de diffusion de contenu Microsoft Ajax | Documents Microsoft"
author: rick-anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/14/2017
ms.topic: article
ms.assetid: 8935bf14-ca6d-4a4e-9dbe-b96ce74cef49
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /ajax/cdn
msc.type: content
ms.openlocfilehash: f69f707ba64d13fc372b7bc44718c9dcf8cec6e2
ms.sourcegitcommit: 3f491f887074310fc0f145cd01a670aa63b969e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
<a name="microsoft-ajax-content-delivery-network"></a>Réseau de diffusion de contenu Microsoft Ajax
====================
Remarque : Le CDN Microsoft Ajax n’a aucun contrat SLA au-dessus et au-delà de l’aide d’un CDN Azure.

## <a name="table-of-contents"></a>Sommaire

**[AJAX.Microsoft.com renommé ajax.aspnetcdn.com](#ajaxmicrosoftcom_renamed_to_ajaxaspnetcdncom_18)**  
**[Prise en charge de Visual Studio .vsdoc](#Visual_Studio_vsdoc_Support_19)**  
**[À l’aide d’ASP.NET Ajax du CDN](#Using_ASPNET_Ajax_from_the_CDN_20)**  
**[À l’aide de jQuery du CDN](#Using_jQuery_from_the_CDN_21)**  
**[À l’aide de jQuery UI du CDN](#Using_jQuery_UI_from_the_CDN_22)**  
**[Fichiers de tiers dans le CDN](#Third-Party_Files_on_the_CDN_23)**  
  
 [Versions de jQuery sur le CDN](#jQuery_Releases_on_the_CDN_0)  
 [jQuery migrer des versions sur le CDN](#jQuery_Migrate_Releases_on_the_CDN_1)  
 [jQuery UI mises en production sur le CDN](#jQuery_UI_Releases_on_the_CDN_2)  
 [jQuery Validation mises en production sur le CDN](#jQuery_Validation_Releases_on_the_CDN_3)  
 [jQuery Mobile mises en production sur le CDN](#jQuery_Mobile_Releases_on_the_CDN_4)  
 [jQuery versions de modèles sur le CDN](#jQuery_Templates_Releases_on_the_CDN_5)  
 [jQuery Cycle des versions sur le CDN](#jQuery_Cycle_Releases_on_the_CDN_6)  
 [jQuery versions de tables de données sur le CDN](#jQuery_DataTables_Releases_on_the_CDN_7)  
 [Versions de Modernizr sur le CDN](#Modernizr_Releases_on_the_CDN_8)  
 [Versions de JSHint sur le CDN](#JSHint_Releases_on_the_CDN_10)  
 [Versions de Knockout sur le CDN](#Knockout_Releases_on_the_CDN_11)  
 [Globaliser des versions sur le CDN](#Globalize_Releases_on_the_CDN_12)  
 [Répondre versions sur le CDN](#Respond_Releases_on_the_CDN_13)  
 [Versions d’amorçage sur le CDN](#Bootstrap_Releases_on_the_CDN_14)  
 [Versions TouchCarousel d’amorçage sur le CDN](#BootstrapTouchCarousel_Releases_on_the_CDN_18)  
 [Versions de Hammer.js sur le CDN](#Hammerjs_Releases_on_the_CDN_19)  
 [Web Forms ASP.NET et Ajax mises en production sur le CDN](#ASPNET_Web_Forms_and_Ajax_Releases_on_the_CDN_15)  
 [ASP.NET MVC publie le CDN](#ASPNET_MVC_Releases_on_the_CDN_16)  
 [ASP.NET SignalR publie le CDN](#ASPNET_SignalR_Releases_on_the_CDN_17)

Le contenu remise réseau CDN Microsoft Ajax () héberge des bibliothèques JavaScript de tiers populaires tels que jQuery et vous permet de facilement les ajouter à vos applications Web. Par exemple, commencer à l’aide de jQuery, qui est hébergé sur ce CDN en ajoutant simplement un &lt;script&gt; balise à votre page qui pointe vers ajax.aspnetcdn.com.

En tirant parti du CDN, vous pouvez considérablement améliorer les performances de vos applications Ajax. Le contenu du CDN est mis en cache sur les serveurs situés dans le monde entier. En outre, le CDN permet aux navigateurs de réutiliser des fichiers JavaScript mis en cache tiers pour les sites web qui se trouvent dans des domaines différents.

Le CDN prend en charge SSL (HTTPS) au cas où vous deviez traiter une page web à l’aide de Secure Sockets Layer.

Le CDN héberge les bibliothèques de scripts tiers suivants qui ont été téléchargés et sont concédés sous licence, par les propriétaires de ces bibliothèques :

- jQuery (www.jquery.com)
- jQuery UI (www.jqueryui.com)
- jQuery Mobile (www.jquerymobile.com)
- jQuery Validation (www.jquery.com)
- jQuery Cycle (www.malsup.com/jquery/cycle/)
- jQuery DataTables (http://datatables.net/)

Le CDN Microsoft Ajax inclut également les bibliothèques suivantes qui ont été téléchargés par Microsoft :

- ASP.NET Ajax
- Fichiers JavaScript ASP.NET MVC
- Les fichiers ASP.NET SignalR JavaScript

Microsoft ne revendique pas la propriété de toutes les bibliothèques tierces hébergées sur ce CDN. Les propriétaires de copyright des bibliothèques de licence ces bibliothèques pour vous. Les droits que vous devrez peut-être télécharger et utiliser ces bibliothèques sont accordées exclusivement par les propriétaires de copyright respectifs. Étant donné que celles-ci ne sont pas des bibliothèques de Microsoft, Microsoft ne fournit aucune garantie ou les licences de droits de propriété intellectuelle (y compris sans droits de brevet implicites) pour les bibliothèques tierces hébergées sur ce CDN.

Si vous souhaitez soumettre votre bibliothèque JavaScript et votre bibliothèque est un des extensions/plug-ins à ces bibliothèques ou des bibliothèques JavaScript (comme indiqué sur http://trends.builtwith.com) supérieur (a) répandus. ou (b) utile pour les utiliser sur ASP.NET, puis contactez AjaxCDNSubmission@Microsoft.com.

<a id="ajaxmicrosoftcom_renamed_to_ajaxaspnetcdncom_18"></a>

## <a name="ajaxmicrosoftcom-renamed-to-ajaxaspnetcdncom"></a>AJAX.Microsoft.com renommé ajax.aspnetcdn.com

Le CDN permet d’utiliser le nom de domaine microsoft.com et a été modifié pour utiliser le nom de domaine aspnetcdn.com. Cette modification a été apportée pour augmenter les performances, car lorsqu’un navigateur référencé le domaine microsoft.com il envoie tous les cookies à partir de ce domaine sur le réseau avec chaque demande. En renommant à un nom de domaine différent de microsoft.com performances peuvent être augmenté par autant de 25 %. Remarque ajax.microsoft.com continuera de fonctionner mais ajax.aspnetcdn.com est recommandé.

- Ancien Format : http://ajax.microsoft.com/ajax/jQuery/jquery-1.8.0.js
- Nouveau Format : http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.js

<a id="Visual_Studio_vsdoc_Support_19"></a>

## <a name="visual-studio-vsdoc-support"></a>Prise en charge de Visual Studio .vsdoc

Pour utiliser les fichiers .vsdoc correctement avec Visual Studio 2008, vous devez vous assurer que vous disposez de Visual Studio 2008 SP1 installé et le correctif logiciel pour les fichiers vsdoc installé. Vous pouvez obtenir à partir d’ici :

- [Télécharger Visual Studio 2008 SP1](https://www.microsoft.com/downloads/en/details.aspx?FamilyId=FBEE1648-7106-44A7-9649-6D9F6D58056E&amp;displaylang=en "télécharger Visual Studio 2008 SP1")
- [Télécharger le correctif logiciel de .vsdoc pour Visual Studio 2008 SP1](https://code.msdn.microsoft.com/KB958502/Release/ProjectReleases.aspx?ReleaseId=1736 "télécharger .vsdoc correctif pour Visual Studio 2008 SP1")

Visual Studio 2010 prend en charge les fichiers .vsdoc sans les correctifs supplémentaires.

<a id="Using_ASPNET_Ajax_from_the_CDN_20"></a>

## <a name="using-aspnet-ajax-from-the-cdn"></a>À l’aide d’ASP.NET Ajax du CDN

Lorsque vous utilisez ASP.NET 4, vous pouvez rediriger toutes les demandes pour les scripts du framework ASP.NET vers le CDN. La récupération des scripts à partir du CDN au lieu de votre serveur web local peuvent considérablement améliorer les performances des sites Web ASP.NET publics.

Utilisez la propriété ScriptManager EnableCDN pour rediriger toutes les demandes de script framework ASP.NET pour le CDN Microsoft Ajax :

[!code-aspx[Main](overview/samples/sample1.aspx)]

<a id="Using_jQuery_from_the_CDN_21"></a>

## <a name="using-jquery-from-the-cdn"></a>À l’aide de jQuery du CDN

Vous pouvez utiliser des scripts jQuery hébergés sur CDN dans votre application Web en ajoutant l’élément de script suivant à une page :

[!code-html[Main](overview/samples/sample2.html)]

Le CDN inclut également la version réduite du script jQuery, que vous pouvez obtenir à l’aide de l’élément suivant :

[!code-html[Main](overview/samples/sample3.html)]

Pour permettre à votre page à revenir au chargement jQuery à partir d’un chemin d’accès local sur votre propre site Web si le CDN est indisponible, ajoutez l’élément suivant immédiatement après l’élément référençant le CDN :

[!code-html[Main](overview/samples/sample4.html)]

L’exemple de page suivant utilise la version CDN de la bibliothèque jQuery (avec basculement vers une copie locale) pour afficher le contenu d’un élément div lorsqu’un clic est effectué.

[!code-html[Main](overview/samples/sample5.html)]

Vous pouvez en savoir plus sur jQuery et télécharger une copie locale de jQuery en vous rendant sur le [jQuery](http://jquery.com/) site Web.

<a id="Using_jQuery_UI_from_the_CDN_22"></a>

## <a name="using-jquery-ui-from-the-cdn"></a>À l’aide de jQuery UI du CDN

Le CDN héberge également la bibliothèque jQuery UI. La bibliothèque jQuery UI comprend un ensemble complet de widgets et les effets que vous pouvez utiliser dans vos applications ASP.NET. Par exemple, la page suivante illustre comment vous pouvez utiliser la sélecteur de dates de l’interface utilisateur jQuery dans le contexte d’une application Web Forms ASP.NET pour afficher un calendrier contextuel :

[!code-aspx[Main](overview/samples/sample6.aspx)]

Lorsque vous déplacez le focus vers la zone de texte à l’aide de votre clavier, un calendrier s’affiche :

![Calendrier contextuel créé avec le sélecteur de dates](overview/_static/image1.png)

Notez que vous devez inclure les trois fichiers du CDN dans le code ci-dessus :

- La bibliothèque jQuery &mdash; la bibliothèque jQuery UI dépend de la bibliothèque jQuery. Vous devez ajouter la bibliothèque jQuery à votre page avant d’ajouter la bibliothèque jQuery UI.
- La bibliothèque jQuery UI &mdash; la bibliothèque jQuery UI contient tous les effets d’interface utilisateur jQuery et widgets tels que le widget de sélecteur de dates utilisées dans la page ci-dessus.
- Un thème de l’interface utilisateur jQuery &mdash; jQuery UI prend en charge différents thèmes. La page ci-dessus inclut un lien vers un fichier CSS pour importer le thème de Redmond.

Tous les thèmes de l’interface utilisateur jQuery standard sont hébergés sur le CDN. [Visitez cette page](jquery-ui/cdnjqueryui1910.md "jQuery UI 1.8.10 dans le CDN Microsoft Ajax") pour afficher les miniatures pour chaque thème.

Pour plus d’informations sur la bibliothèque jQuery UI, visitez officielle [site Web de l’interface utilisateur jQuery](http://jQueryUI.com "site Web de l’interface utilisateur jQuery").

<a id="Third-Party_Files_on_the_CDN_23"></a>

## <a name="third-party-files-on-the-cdn"></a>Fichiers de tiers dans le CDN

Le CDN héberge certains des bibliothèques JavaScript tiers les plus populaires. Microsoft ne revendique pas la propriété de toutes les bibliothèques tierces hébergées sur ce CDN. Les propriétaires de copyright des bibliothèques de licence ces bibliothèques pour vous. Les droits que vous devrez peut-être télécharger et utiliser ces bibliothèques sont accordées exclusivement par les propriétaires de copyright respectifs. Étant donné que celles-ci ne sont pas des bibliothèques de Microsoft, Microsoft ne fournit aucune garantie ou les licences de droits de propriété intellectuelle (y compris sans droits de brevet implicites) pour les bibliothèques tierces hébergées sur ce CDN.

<a id="jQuery_Releases_on_the_CDN_0"></a>

### <a name="jquery-releases-on-the-cdn"></a>Versions de jQuery sur le CDN

Les versions suivantes de jQuery sont hébergées sur le CDN :

#### <a name="jquery-version-331"></a>version de jQuery 3.3.1
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.3.1.SLIM.min.Map

#### <a name="jquery-version-321"></a>jQuery version 3.2.1
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.1.SLIM.min.Map

#### <a name="jquery-version-320"></a>version de jQuery 3.2.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.2.0.SLIM.min.Map

#### <a name="jquery-version-311"></a>version de jQuery 3.1.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.1.SLIM.min.Map

#### <a name="jquery-version-310"></a>jQuery version 3.1.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.1.0.SLIM.min.Map

#### <a name="jquery-version-300"></a>version de jQuery 3.0.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.min.Map
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.SLIM.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.SLIM.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-3.0.0.SLIM.min.Map

#### <a name="jquery-version-224"></a>version de jQuery 2.2.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.4.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.4.min.Map

#### <a name="jquery-version-223"></a>version de jQuery 2.2.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.3.min.Map

#### <a name="jquery-version-222"></a>version de jQuery 2.2.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.2.min.Map

#### <a name="jquery-version-221"></a>jQuery version 2.2.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.1.min.Map

#### <a name="jquery-version-220"></a>jQuery version2.2.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.2.0.min.Map

#### <a name="jquery-version-214"></a>version de jQuery 2.1.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.4.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.4.min.Map

#### <a name="jquery-version-213"></a>version de jQuery 2.1.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.3.min.Map

#### <a name="jquery-version-212"></a>version de jQuery 2.1.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.2.min.js

#### <a name="jquery-version-211"></a>version de jQuery 2.1.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.1.min.Map

#### <a name="jquery-version-210"></a>version de jQuery 2.1.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.0-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.1.0.min.Map

#### <a name="jquery-version-203"></a>version de jQuery 2.0.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.3-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.3.min.Map

#### <a name="jquery-version-202"></a>version de jQuery 2.0.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.2-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.2.min.Map

#### <a name="jquery-version-201"></a>version de jQuery 2.0.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.1-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.1.min.Map

#### <a name="jquery-version-200"></a>jQuery version 2.0.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.0-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-2.0.0.min.Map

#### <a name="jquery-version-1124"></a>version de jQuery 1.12.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.4.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.4.min.Map

#### <a name="jquery-version-1123"></a>version de jQuery 1.12.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.3.min.Map

#### <a name="jquery-version-1122"></a>version de jQuery 1.12.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.2.min.Map

#### <a name="jquery-version-1121"></a>version de jQuery 1.12.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.1.min.Map

#### <a name="jquery-version-1120"></a>version de jQuery 1.12.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.12.0.min.Map

#### <a name="jquery-version-1113"></a>version de jQuery 1.11.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.3.min.Map

#### <a name="jquery-version-1112"></a>version de jQuery 1.11.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.2.min.Map

#### <a name="jquery-version-1111"></a>version de jQuery 1.11.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.1.min.Map

#### <a name="jquery-version-1110"></a>version de jQuery 1.11.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.0-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.11.0.min.Map

#### <a name="jquery-version-1102"></a>version de jQuery 1.10.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.2-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.2.min.Map

#### <a name="jquery-version-1101"></a>version de jQuery 1.10.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.1-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.1.min.Map

#### <a name="jquery-version-1100"></a>version de jQuery 1.10.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.0-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.10.0.min.Map

#### <a name="jquery-version-191"></a>version de jQuery 1.9.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.1-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.1.min.Map

#### <a name="jquery-version-190"></a>version de jQuery à 1.9.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.0-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.9.0.min.Map

#### <a name="jquery-version-183"></a>version de jQuery 1.8.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.3-VSDoc.js

#### <a name="jquery-version-182"></a>version de jQuery 1.8.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.2-VSDoc.js

#### <a name="jquery-version-181"></a>version de jQuery 1.8.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.1-VSDoc.js

#### <a name="jquery-version-180"></a>version de jQuery 1.8.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.8.0-VSDoc.js

#### <a name="jquery-version-172"></a>version de jQuery 1.7.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.2.min.js

#### <a name="jquery-version-171"></a>jQuery version 1.7.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.1-VSDoc.js

#### <a name="jquery-version-17"></a>jQuery version 1.7

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.7-VSDoc.js

#### <a name="jquery-version-164"></a>version de jQuery 1.6.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.4.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.4-VSDoc.js

#### <a name="jquery-version-163"></a>version de jQuery 1.6.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.3-VSDoc.js

#### <a name="jquery-version-162"></a>version de jQuery 1.6.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.2-VSDoc.js

#### <a name="jquery-version-161"></a>version de jQuery 1.6.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.1-VSDoc.js

#### <a name="jquery-version-16"></a>jQuery version 1.6

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.6-VSDoc.js

#### <a name="jquery-version-152"></a>version de jQuery 1.5.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.2-VSDoc.js

#### <a name="jquery-version-151"></a>version de jQuery 1.5.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.1-VSDoc.js

#### <a name="jquery-version-15"></a>version de jQuery 1.5

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.5-VSDoc.js

#### <a name="jquery-version-144"></a>version de jQuery 1.4.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.4.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.4-VSDoc.js

#### <a name="jquery-version-143"></a>version de jQuery 1.4.3

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.3.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.3-VSDoc.js

#### <a name="jquery-version-142"></a>version de jQuery 1.4.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.2-VSDoc.js

#### <a name="jquery-version-141"></a>version de jQuery 1.4.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.1-VSDoc.js

#### <a name="jquery-version-14"></a>jQuery version 1.4

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.4.min.js

#### <a name="jquery-version-132"></a>version de jQuery 1.3.2

- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.3.2.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.3.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.3.2-VSDoc.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery/jQuery-1.3.2.min-VSDoc.js

<a id="jQuery_Migrate_Releases_on_the_CDN_1"></a>

### <a name="jquery-migrate-releases-on-the-cdn"></a>jQuery migrer des versions sur le CDN

Les versions suivantes de jQuery migrer sont hébergées sur le CDN :

#### <a name="jquery-migrate-version-300"></a>jQuery migration version 3.0.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-3.0.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-3.0.0.min.js

#### <a name="jquery-migrate-version-121"></a>jQuery migration version 1.2.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.2.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.2.1.min.js

jQuery migration version 1.2.0

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.2.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.2.0.min.js

#### <a name="jquery-migrate-version-111"></a>jQuery migration version 1.1.1

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.1.1.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.1.1.min.js

#### <a name="jquery-migrate-version-110"></a>jQuery migration version 1.1.0.

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.1.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.1.0.min.js

#### <a name="jquery-migrate-version-100"></a>Migrer la version 1.0.0 de jQuery

- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.0.0.js
- http://AJAX.aspnetcdn.com/AJAX/jQuery.Migrate/jQuery-Migrate-1.0.0.min.js

<a id="jQuery_UI_Releases_on_the_CDN_2"></a>

### <a name="jquery-ui-releases-on-the-cdn"></a>jQuery UI mises en production sur le CDN

Les versions suivantes de la bibliothèque jQuery UI sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery UI 1.12.1](jquery-ui/cdnjqueryui1121.md "jQuery UI 1.12.1 dans le CDN Microsoft Ajax")
- [jQuery UI 1.12.0](jquery-ui/cdnjqueryui1120.md "jQuery UI 1.12.0 dans le CDN Microsoft Ajax")
- [jQuery UI 1.11.4](jquery-ui/cdnjqueryui1114.md "jQuery UI 1.11.4 dans le CDN Microsoft Ajax")
- [jQuery UI 1.11.3](jquery-ui/cdnjqueryui1113.md "jQuery UI 1.11.3 dans le CDN Microsoft Ajax")
- [jQuery UI 1.11.2](jquery-ui/cdnjqueryui1112.md "jQuery UI 1.11.2 dans le CDN Microsoft Ajax")
- [jQuery UI 1.11.1](jquery-ui/cdnjqueryui1111.md "jQuery UI 1.11.1 dans le CDN Microsoft Ajax")
- [jQuery UI 1.11.0](jquery-ui/cdnjqueryui1110.md "jQuery UI 1.11.0 dans le CDN Microsoft Ajax")
- [jQuery UI 1.10.4](jquery-ui/cdnjqueryui1104.md "jQuery UI 1.10.4 dans le CDN Microsoft Ajax")
- [jQuery UI 1.10.3](jquery-ui/cdnjqueryui1103.md "jQuery UI 1.10.3 dans le CDN Microsoft Ajax")
- [jQuery UI 1.10.2](jquery-ui/cdnjqueryui1102.md "jQuery UI 1.10.2 dans le CDN Microsoft Ajax")
- [jQuery UI 1.10.1](jquery-ui/cdnjqueryui1101.md "jQuery UI 1.10.1 dans le CDN Microsoft Ajax")
- [jQuery UI 1.10.0](jquery-ui/cdnjqueryui1100.md "jQuery UI 1.10.0 dans le CDN Microsoft Ajax")
- [jQuery UI 1.9.2](jquery-ui/cdnjqueryui192.md "jQuery UI 1.9.2 dans le CDN Microsoft Ajax")
- [jQuery UI 1.9.1](jquery-ui/cdnjqueryui191.md "jQuery UI 1.9.1 dans le CDN Microsoft Ajax")
- [jQuery UI à 1.9.0](jquery-ui/cdnjqueryui190.md "jQuery UI à 1.9.0 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.24](jquery-ui/cdnjqueryui1824.md "jQuery UI 1.8.24 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.23](jquery-ui/cdnjqueryui1823.md "jQuery UI 1.8.23 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.22](jquery-ui/cdnjqueryui1822.md "jQuery UI 1.8.22 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.21](jquery-ui/cdnjqueryui1821.md "jQuery UI 1.8.21 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.20](jquery-ui/cdnjqueryui1820.md "jQuery UI 1.8.20 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.19](jquery-ui/cdnjqueryui1819.md "jQuery UI 1.8.19 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.18](jquery-ui/cdnjqueryui1818.md "jQuery UI 1.8.18 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.17](jquery-ui/cdnjqueryui1817.md "jQuery UI 1.8.17 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.16](jquery-ui/cdnjqueryui1816.md "jQuery UI 1.8.16 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.15](jquery-ui/cdnjqueryui1815.md "jQuery UI 1.8.15 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.14](jquery-ui/cdnjqueryui1814.md "jQuery UI 1.8.14 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.13](jquery-ui/cdnjqueryui1813.md "jQuery UI 1.8.13 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.12](jquery-ui/cdnjqueryui1812.md "jQuery UI 1.8.12 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.11](jquery-ui/cdnjqueryui1811.md "jQuery UI 1.8.11 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.10](jquery-ui/cdnjqueryui1910.md "jQuery UI 1.8.10 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.9](jquery-ui/cdnjqueryui189.md "jQuery UI 1.8.9 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.8](jquery-ui/cdnjqueryui188.md "jQuery UI 1.8.8 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.7](jquery-ui/cdnjqueryui187.md "jQuery UI 1.8.7 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.6](jquery-ui/cdnjqueryui186.md "jQuery UI 1.8.6 dans le CDN Microsoft Ajax")
- [jQuery UI 1.8.5](jquery-ui/cdnjqueryui185.md "jQuery UI 1.8.5")

<a id="jQuery_Validation_Releases_on_the_CDN_3"></a>

### <a name="jquery-validation-releases-on-the-cdn"></a>jQuery Validation mises en production sur le CDN

Les versions suivantes de la bibliothèque de Validation jQuery sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery validation 1.17.0](jquery-validate/cdnjqueryvalidate1170.md "jQuery Validation 1.17.0")
- [jQuery validation 1.16.0](jquery-validate/cdnjqueryvalidate1160.md "jQuery Validation 1.16.0")
- [jQuery validation 1.15.1](jquery-validate/cdnjqueryvalidate1151.md "jQuery Validation 1.15.1")
- [jQuery validation 1.15.0](jquery-validate/cdnjqueryvalidate1150.md "jQuery Validation 1.15.0")
- [jQuery validation 1.14.0](jquery-validate/cdnjqueryvalidate1140.md "jQuery Validation 1.14.0")
- [jQuery validation 1.13.1](jquery-validate/cdnjqueryvalidate1131.md "jQuery Validation 1.13.1")
- [jQuery validation 1.13.0](jquery-validate/cdnjqueryvalidate1130.md "jQuery Validation 1.13.0")
- [jQuery validation 1.12.0](jquery-validate/cdnjqueryvalidate1120.md "jQuery Validation 1.12.0")
- [jQuery validation 1.11.1](jquery-validate/cdnjqueryvalidate1111.md "jQuery Validation 1.11.1")
- [jQuery validation 1.11.0](jquery-validate/cdnjqueryvalidate111.md "jQuery Validation 1.11.0")
- [jQuery validation 1.10.0](jquery-validate/cdnjqueryvalidate110.md "jQuery Validation 1.10.0")
- [jQuery validation 1.9](jquery-validate/cdnjqueryvalidate19.md "jquery.validate version 1.9")
- [jQuery validation 1.8.1](jquery-validate/cdnjqueryvalidate181.md "jquery.validate version 1.8.1")
- [jQuery validation 1.8](jquery-validate/cdnjqueryvalidate18.md "jquery.validate version 1.8")
- [jQuery validation 1.7](jquery-validate/cdnjqueryvalidate17.md "jquery.validate version 1.7")
- [jQuery validation 1.6](jquery-validate/cdnjqueryvalidate16.md "jQuery validation 1.6")
- [jQuery validation 1.5.5](jquery-validate/cdnjqueryvalidate155.md "jQuery validation 1.5.5")

<a id="jQuery_Mobile_Releases_on_the_CDN_4"></a>

### <a name="jquery-mobile-releases-on-the-cdn"></a>jQuery Mobile mises en production sur le CDN

Les versions suivantes de la bibliothèque de jQuery Mobile sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery Mobile 1.4.5](jquery-mobile/cdnjquerymobile145.md "jQuery Mobile 1.4.5 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.4.2](jquery-mobile/cdnjquerymobile142.md "jQuery Mobile 1.4.2 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.4.1](jquery-mobile/cdnjquerymobile141.md "jQuery Mobile 1.4.1 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.4.0](jquery-mobile/cdnjquerymobile140.md "jQuery Mobile 1.4.0 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.3.2](jquery-mobile/cdnjquerymobile132.md "jQuery Mobile 1.3.2 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.3.1](jquery-mobile/cdnjquerymobile131.md "jQuery Mobile 1.3.1 sur le CDN Microsoft Ajax")
- [jQuery Mobile version 1.3.0](jquery-mobile/cdnjquerymobile130.md "jQuery Mobile version 1.3.0 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.2.0](jquery-mobile/cdnjquerymobile120.md "jQuery Mobile 1.2.0 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.1.2](jquery-mobile/cdnjquerymobile112.md "jQuery Mobile 1.1.2 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.1.1](jquery-mobile/cdnjquerymobile111.md "jQuery Mobile 1.1.1 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.1.0](jquery-mobile/cdnjquerymobile110.md "jQuery Mobile 1.1.0 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.1.0 RC 2](jquery-mobile/cdnjquerymobile110rc2.md "jQuery Mobile 1.1.0 RC2 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.0.1](jquery-mobile/cdnjquerymobile101.md "jQuery Mobile 1.0.1 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.0](jquery-mobile/cdnjquerymobile10.md "jQuery Mobile 1.0 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.0 RC 2](jquery-mobile/cdnjquerymobile10rc2.md "jQuery Mobile 1.0 RC2 sur le CDN Microsoft Ajax")
- [jQuery Mobile 1.0 RC 1](jquery-mobile/cdnjquerymobile10rc1.md "jQuery Mobile 1.0 RC1 sur le CDN Microsoft Ajax")
- [version bêta 3 de jQuery Mobile 1.0](jquery-mobile/cdnjquerymobile10b3.md "jQuery Mobile 1.0 bêta 3 sur le CDN Microsoft Ajax")

<a id="jQuery_Templates_Releases_on_the_CDN_5"></a>

### <a name="jquery-templates-releases-on-the-cdn"></a>jQuery versions de modèles sur le CDN

Les versions suivantes du plug-in de modèles jQuery sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery modèles bêta 1](jquery-templates/cdnjquerytemplatesbeta1.md "jQuery modèles bêta 1")

<a id="jQuery_Cycle_Releases_on_the_CDN_6"></a>

### <a name="jquery-cycle-releases-on-the-cdn"></a>jQuery Cycle des versions sur le CDN

Les versions suivantes du plug-in du Cycle de jQuery sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery Cycle 2,99](jquery-cycle/cdnjquerycycle299.md "jQuery 2,99 de Cycle")
- [jQuery Cycle 2.94](jquery-cycle/cdnjquerycycle294.md "jQuery Cycle 2.94")
- [jQuery Cycle 2,88](jquery-cycle/cdnjquerycycle288.md "jQuery 2,88 de Cycle")

<a id="jQuery_DataTables_Releases_on_the_CDN_7"></a>

### <a name="jquery-datatables-releases-on-the-cdn"></a>jQuery versions de tables de données sur le CDN

Les versions suivantes du plug-in DataTables de jQuery sont hébergées sur ce CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [jQuery DataTables 1.10.5](jquery-datatables/cdnjquerydatatables105.md "jQuery DataTables 1.10.5")
- [jQuery DataTables 1.10.4](jquery-datatables/cdnjquerydatatables104.md "jQuery DataTables 1.10.4")
- [jQuery DataTables 1.9.4](jquery-datatables/cdnjquerydatatables194.md "jQuery DataTables 1.9.4")
- [jQuery DataTables 1.9.3](jquery-datatables/cdnjquerydatatables193.md "jQuery DataTables 1.9.3")
- [jQuery DataTables 1.9.2](jquery-datatables/cdnjquerydatatables192.md "jQuery DataTables 1.9.2")
- [jQuery DataTables 1.9.1](jquery-datatables/cdnjquerydatatables191.md "jQuery DataTables 1.9.1")
- [jQuery DataTables à 1.9.0](jquery-datatables/cdnjquerydatatables190.md "jQuery DataTables à 1.9.0")
- [jQuery DataTables 1.8.2](jquery-datatables/cdnjquerydatatables182.md "jQuery DataTables 1.8.2")

<a id="Modernizr_Releases_on_the_CDN_8"></a>

### <a name="modernizr-releases-on-the-cdn"></a>Versions de Modernizr sur le CDN

Les versions suivantes de [Modernizr](http://www.modernizr.com "Modernizr") sont hébergés sur le CDN :

- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-2.8.3.js
- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-2.7.2.js
- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-2.7.1.js
- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-2.6.2.js
- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-1.7-Development-only.js
- http://AJAX.aspnetcdn.com/AJAX/Modernizr/Modernizr-2.0.6-Development-only.js

<a id="JSHint_Releases_on_the_CDN_10"></a>

### <a name="jshint-releases-on-the-cdn"></a>Versions de JSHint sur le CDN

Les versions suivantes de [JSHint](http://www.jshint.com "JSHint") sont hébergés sur le CDN :

- http://AJAX.aspnetcdn.com/AJAX/jshint/r07/jshint.js

<a id="Knockout_Releases_on_the_CDN_11"></a>

### <a name="knockout-releases-on-the-cdn"></a>Versions de Knockout sur le CDN

Les versions suivantes de [Knockout](http://www.knockoutjs.com "Knockout") sont hébergés sur le CDN :

- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.2.1.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.2.1.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.2.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.2.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.1.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-2.1.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.0.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.0.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.1.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.1.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.2.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.2.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.3.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.3.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.0.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.0.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.1.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.1.Debug.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.2.js
- http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.4.2.Debug.js

<a id="Globalize_Releases_on_the_CDN_12"></a>

### <a name="globalize-releases-on-the-cdn"></a>Globaliser des versions sur le CDN

Les versions suivantes de [Globalize](https://github.com/jquery/globalize "Globalize") sont hébergés sur le CDN :

#### <a name="globalize-version-100"></a>La version 1.0.0 de globalisation

- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/Node-main.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/Currency.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/date.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/message.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/Number.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/plural.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/1.0.0/globalize/relative-Time.js

#### <a name="globalize-version-011"></a>Globaliser version 0.1.1

- http://AJAX.aspnetcdn.com/AJAX/globalize/0.1.1/globalize.min.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/0.1.1/globalize.js
- http://AJAX.aspnetcdn.com/AJAX/globalize/0.1.1/cultures/globalize.cultures.js

    - toutes les cultures
- http://AJAX.aspnetcdn.com/AJAX/globalize/0.1.1/cultures/globalize.culture. {code de culture} .js

    - Remplacez « {-code de culture} » avec le code de culture de votre choix, par exemple, les fichiers sur le CDN globalize.culture.en-GB.js== Microsoft == ces bibliothèques ont été téléchargés par Microsoft.

<a id="Respond_Releases_on_the_CDN_13"></a>

### <a name="respond-releases-on-the-cdn"></a>Répondre versions sur le CDN

Les versions suivantes de [https://github.com/scottjehl/Respond](https://github.com/scottjehl/Respond "https://github.com/scottjehl/Respond") répondre sont hébergés sur le CDN :

#### <a name="respond-version-142"></a>Répondre version 1.4.2

- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.2/Respond.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.2/Respond.min.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.2/Respond.matchmedia.addListener.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.2/Respond.matchmedia.addListener.min.js

#### <a name="respond-version-141"></a>Version 1.4.1 de répondre

- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.1/Respond.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.1/Respond.min.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.1/Respond.matchmedia.addListener.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.1/Respond.matchmedia.addListener.min.js

#### <a name="respond-version-140"></a>Répondre version1.4.0

- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.0/Respond.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.0/Respond.min.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.0/Respond.matchmedia.addListener.js
- http://AJAX.aspnetcdn.com/AJAX/Respond/1.4.0/Respond.matchmedia.addListener.min.js

#### <a name="respond-version-130"></a>Répondre version version 1.3.0

- http://AJAX.aspnetcdn.com/AJAX/Respond/1.3.0/Respond.js

#### <a name="respond-version-120"></a>Répondre version 1.2.0

- http://AJAX.aspnetcdn.com/AJAX/Respond/1.2.0/Respond.js

<a id="Bootstrap_Releases_on_the_CDN_14"></a>

### <a name="bootstrap-releases-on-the-cdn"></a>Versions d’amorçage sur le CDN

Les versions suivantes de [getbootstrap.com](http://getbootstrap.com "getbootstrap.com") bootstrap sont hébergés sur le CDN :

#### <a name="bootstrap-version-337"></a>Version d’amorçage 3.3.7

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Fonts/glyphicons-halflings-Regular.woff
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.7/Fonts/glyphicons-halflings-Regular.woff2

#### <a name="bootstrap-version-336"></a>Version d’amorçage 3.3.6

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Fonts/glyphicons-halflings-Regular.woff
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.6/Fonts/glyphicons-halflings-Regular.woff2

#### <a name="bootstrap-version-335"></a>Amorçage version 3.3.5

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Fonts/glyphicons-halflings-Regular.woff
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.5/Fonts/glyphicons-halflings-Regular.woff2

#### <a name="bootstrap-version-334"></a>Version d’amorçage 3.3.4

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Fonts/glyphicons-halflings-Regular.woff
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.4/Fonts/glyphicons-halflings-Regular.woff2

#### <a name="bootstrap-version-332"></a>Version d’amorçage 3.3.2

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Fonts/glyphicons-halflings-Regular.woff
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.2/Fonts/glyphicons-halflings-Regular.woff2

#### <a name="bootstrap-version-331"></a>Version d’amorçage 3.3.1

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.1/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-330"></a>Version d’amorçage 3.3.0

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.3.0/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-320"></a>Version d’amorçage 3.2.0

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.2.0/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-311"></a>Version d’amorçage 3.1.1

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.1/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-310"></a>Amorçage version 3.1.0

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap-Theme.CSS.Map
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.1.0/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-303"></a>Version d’amorçage 3.0.3

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.3/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-302"></a>Version d’amorçage 3.0.2

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.2/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-301"></a>Version d’amorçage 3.0.1

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.1/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-300"></a>Version d’amorçage 3.0.0

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/CSS/Bootstrap-Theme.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/CSS/Bootstrap-Theme.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Fonts/glyphicons-halflings-Regular.EOT
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Fonts/glyphicons-halflings-Regular.SVG
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Fonts/glyphicons-halflings-Regular.ttf
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/3.0.0/Fonts/glyphicons-halflings-Regular.woff

#### <a name="bootstrap-version-232"></a>Amorçage version 2.3.2

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/CSS/Bootstrap-Responsive.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/CSS/Bootstrap-Responsive.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/img/glyphicons-halflings.png
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.2/img/glyphicons-halflings-White.png

#### <a name="bootstrap-version-231"></a>Amorçage version 2.3.1

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/Bootstrap.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/Bootstrap.min.js
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/CSS/Bootstrap.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/CSS/Bootstrap.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/CSS/Bootstrap-Responsive.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/CSS/Bootstrap-Responsive.min.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/img/glyphicons-halflings.png
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap/2.3.1/img/glyphicons-halflings-White.png

<a id="BootstrapTouchCarousel_Releases_on_the_CDN_18"></a>

### <a name="bootstrap-touchcarousel-releases-on-the-cdn"></a>Versions TouchCarousel d’amorçage sur le CDN

Les versions suivantes de [https://github.com/ixisio/bootstrap-touch-carousel](https://github.com/ixisio/bootstrap-touch-carousel "https://github.com/ixisio/bootstrap-touch-carousel") TouchCarousel du programme d’amorçage versions sont hébergées sur le CDN :

#### <a name="bootstrap-touchcarousel-version-080"></a>Amorçage TouchCarousel version 0.8.0

- http://AJAX.aspnetcdn.com/AJAX/Bootstrap-Touch-Carousel/0.8.0/CSS/Bootstrap-Touch-Carousel.CSS
- http://AJAX.aspnetcdn.com/AJAX/Bootstrap-Touch-Carousel/0.8.0/js/Bootstrap-Touch-Carousel.js

<a id="Hammerjs_Releases_on_the_CDN_19"></a>

### <a name="hammerjs-releases-on-the-cdn"></a>Versions de Hammer.js sur le CDN

Les versions suivantes de [http://hammerjs.github.io/](http://hammerjs.github.io/ "http://hammerjs.github.io/") Hammer.js versions sont hébergées sur le CDN :

#### <a name="hammerjs-version-204"></a>Hammer.js version 2.0.4

- http://AJAX.aspnetcdn.com/AJAX/hammer.js/2.0.4/hammer.js
- http://AJAX.aspnetcdn.com/AJAX/hammer.js/2.0.4/hammer.min.js
- http://AJAX.aspnetcdn.com/AJAX/hammer.js/2.0.4/hammer.min.Map

<a id="ASPNET_Web_Forms_and_Ajax_Releases_on_the_CDN_15"></a>

### <a name="aspnet-web-forms-and-ajax-releases-on-the-cdn"></a>Web Forms ASP.NET et Ajax mises en production sur le CDN

Les versions suivantes de la bibliothèque ASP.NET Ajax sont hébergées sur le CDN. Cliquez sur chaque lien pour afficher la liste réelle des fichiers.

- [Version de Web Forms ASP.NET et Ajax 4.5.2](cdnajax452.md "Web Forms ASP.NET et Ajax 4.5.2")
- [Version de Web Forms ASP.NET et Ajax 4](cdnajax4.md "Web Forms ASP.NET et Ajax 4")
- [ASP.NET Ajax version 3.5](cdnajax35.md "ASP.NET Ajax 3.5")

<a id="ASPNET_MVC_Releases_on_the_CDN_16"></a>

### <a name="aspnet-mvc-releases-on-the-cdn"></a>ASP.NET MVC publie le CDN

Les fichiers ASP.NET MVC JavaScript suivants sont hébergés sur ce CDN :

#### <a name="aspnet-mvc-523"></a>ASP.NET MVC 5.2.3

- http://AJAX.aspnetcdn.com/AJAX/MVC/5.2.3/jQuery.Validate.unobtrusive.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/5.2.3/jQuery.Validate.unobtrusive.min.js

#### <a name="aspnet-mvc-51"></a>ASP.NET MVC 5.1

- http://AJAX.aspnetcdn.com/AJAX/MVC/5.1/jQuery.Validate.unobtrusive.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/5.1/jQuery.Validate.unobtrusive.min.js

#### <a name="aspnet-mvc-50"></a>ASP.NET MVC 5.0

- http://AJAX.aspnetcdn.com/AJAX/MVC/5.0/jQuery.Validate.unobtrusive.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/5.0/jQuery.Validate.unobtrusive.min.js

#### <a name="aspnet-mvc-40"></a>ASP.NET MVC 4.0

- http://AJAX.aspnetcdn.com/AJAX/MVC/4.0/jQuery.Validate.unobtrusive.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/4.0/jQuery.Validate.unobtrusive.min.js

#### <a name="aspnet-mvc-30"></a>ASP.NET MVC 3.0

- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/jQuery.unobtrusive-AJAX.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/jQuery.unobtrusive-AJAX.min.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/jQuery.Validate.unobtrusive.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/jQuery.Validate.unobtrusive.min.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/MicrosoftMvcAjax.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/3.0/MicrosoftMvcAjax.Debug.js

#### <a name="aspnet-mvc-20"></a>ASP.NET MVC 2.0

- http://AJAX.aspnetcdn.com/AJAX/MVC/2.0/MicrosoftMvcAjax.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/2.0/MicrosoftMvcAjax.Debug.js

#### <a name="aspnet-mvc-10"></a>ASP.NET MVC 1.0

- http://AJAX.aspnetcdn.com/AJAX/MVC/1.0/MicrosoftMvcAjax.js
- http://AJAX.aspnetcdn.com/AJAX/MVC/1.0/MicrosoftMvcAjax.Debug.js

<a id="ASPNET_SignalR_Releases_on_the_CDN_17"></a>

### <a name="aspnet-signalr-releases-on-the-cdn"></a>ASP.NET SignalR publie le CDN

Les fichiers ASP.NET SignalR JavaScript suivants sont hébergés sur ce CDN :

#### <a name="aspnet-signalr-222"></a>ASP.NET SignalR 2.2.2

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.2.js

#### <a name="aspnet-signalr-221"></a>ASP.NET SignalR 2.2.1

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.1.js

#### <a name="aspnet-signalr-220"></a>ASP.NET SignalR 2.2.0

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.2.0.js

#### <a name="aspnet-signalr-210"></a>ASP.NET SignalR 2.1.0

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.1.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.1.0.js

#### <a name="aspnet-signalr-203"></a>ASP.NET SignalR 2.0.3

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.3.js

#### <a name="aspnet-signalr-202"></a>ASP.NET SignalR 2.0.2

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.2.js

#### <a name="aspnet-signalr-201"></a>ASP.NET SignalR 2.0.1

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.1.js

#### <a name="aspnet-signalr-200"></a>ASP.NET SignalR 2.0.0

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-2.0.0.js

#### <a name="aspnet-signalr-113"></a>ASP.NET SignalR 1.1.3

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.3.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.3.js

#### <a name="aspnet-signalr-112"></a>ASP.NET SignalR 1.1.2

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.2.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.2.js

#### <a name="aspnet-signalr-111"></a>ASP.NET SignalR 1.1.1

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.1.js

#### <a name="aspnet-signalr-110"></a>ASP.NET SignalR 1.1.0

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.0.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.1.0.js

#### <a name="aspnet-signalr-101"></a>ASP.NET SignalR 1.0.1

- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.0.1.min.js
- http://AJAX.aspnetcdn.com/AJAX/SignalR/jQuery.SignalR-1.0.1.js

Pour plus d’informations sur les conditions d’utilisation pour le CDN, consultez [Microsoft Ajax CDN conditions d’utilisation](https://www.asp.net/terms-of-use "Microsoft Ajax CDN conditions d’utilisation").
