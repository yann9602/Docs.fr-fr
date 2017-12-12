---
uid: web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
title: Rendu de ASP.NET Web Pages (Razor) Sites pour les appareils mobiles | Documents Microsoft
author: tfitzmac
description: "Cet article décrit comment créer des pages dans un site ASP.NET Web Pages (Razor) qui apparaîtront correctement sur les appareils mobiles. Vous apprendrez : comment vous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: f15ab392-c05e-4269-83bf-7c6d2b8c8ec8
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
msc.type: authoredcontent
ms.openlocfilehash: 08b714eb2ffaefc7c7e2e5c9a7428106b231e5b7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="rendering-aspnet-web-pages-razor-sites-for-mobile-devices"></a>Rendu des Sites Web ASP.NET (Razor) de Pages pour les appareils mobiles
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment créer des pages dans un site ASP.NET Web Pages (Razor) qui apparaîtront correctement sur les appareils mobiles.
> 
> Ce que vous allez apprendre :
> 
> - Comment utiliser une convention d’affectation de noms pour spécifier qu’une page est conçue spécifiquement pour les appareils mobiles.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


Les Pages Web ASP.NET vous permet de créer des affichages personnalisés pour restituer le contenu sur mobile ou d’autres périphériques.

Le plus simple pour créer une page de périphérique spécifiques dans un site ASP.NET Web Pages est à l’aide d’un modèle d’affectation de noms de fichier comme celui-ci : *nom de fichier.* *Mobile**.cshtml*. Vous pouvez créer deux versions d’une page (par exemple, un nommé *MyFile.cshtml* et l’autre nommé *MyFile.Mobile.cshtml*). À l’exécution, quand un appareil mobile demande *MyFile.cshtml*, ASP.NET restitue le contenu à partir de *MyFile.Mobile.cshtml*. Dans le cas contraire, *MyFile.cshtml* est rendu.

L’exemple suivant montre comment activer le rendu mobile en ajoutant une page de contenu pour les appareils mobiles. *Page1.cshtml* contient le contenu ainsi qu’une barre latérale de navigation. *Page1.Mobile.cshtml* contient le même contenu, mais omet la barre latérale.

1. Dans un site ASP.NET Web Pages, créez un fichier nommé *Page1.cshtml* et remplacer le contenu actuel avec le balisage suivant.

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample1.html)]
2. Créez un fichier nommé *Page1.Mobile.cshtml* et remplacer le contenu existant par le balisage suivant. Notez que la version mobile de la page omet la section de navigation pour optimiser le rendu sur un écran de petite taille.

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample2.html)]
3. Exécuter un navigateur et accédez à *Page1.cshtml*. ![mobilesites-1](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image1.png)
4. Exécuter un navigateur mobile (ou un émulateur d’appareil mobile) et accédez à *Page1.cshtml*. (Notez que vous n’incluez pas *.mobile.* dans le cadre de l’URL). Même si la demande est de *Page1.cshtml*, ASP.NET restitue *Page1.Mobile.cshtml*.

    ![mobilesites-2](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image2.png)

> [!NOTE]
> Pour tester les pages mobiles, vous pouvez utiliser un simulateur d’appareil mobile qui s’exécute sur un ordinateur de bureau. Cet outil vous permet de tester les pages web comme elles ressemblent sur les appareils mobiles (autrement dit, généralement avec un beaucoup plus petite afficher la zone). Par exemple, un simulateur est la [un module complémentaire du sélecteur de l’Agent utilisateur](http://addons.mozilla.org/en-us/firefox/addon/user-agent-switcher/) pour Mozilla Firefox, qui vous permet d’émuler différents navigateurs des appareils mobiles à partir d’une version de bureau de Firefox.


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[Émulateur de Windows Phone](https://msdn.microsoft.com/en-us/library/ff402563(v=VS.92).aspx)
