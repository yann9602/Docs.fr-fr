---
uid: web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
title: "Ajout de réseaux sociaux pour ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Ce chapitre explique comment intégrer votre site avec les services de mise en réseau sociales. Dans ce chapitre, vous apprendrez comment permettre aux utilisateurs de votre site Web de signet/lien..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/21/2014
ms.topic: article
ms.assetid: 03c342f9-b35c-4d7c-b9ed-cd9aaaffedb6
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
msc.type: authoredcontent
ms.openlocfilehash: 2c43fa7d286e43f3a4581662ce421c7435e1871f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-social-networking-to-aspnet-web-pages-razor-sites"></a>Ajout de réseaux sociaux Sites de réseau pour les Pages Web ASP.NET (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment ajouter des liens réseau sociales pour Facebook, Twitter, Reddit et Digg vers les pages dans un site Web ASP.NET Web Pages (Razor) et comment inclure des fils Twitter, des cartes de jeux Xbox et Gravatar images.
> 
> Ce que vous allez apprendre :
> 
> - Comment permettre aux utilisateurs de lien de signet/votre site.
> - Comment ajouter un flux sur Twitter.
> - Comment ajouter un Facebook **comme** bouton aux pages.
> - Comment effectuer le rendu des images de Gravatar.com.
> - Comment afficher une carte de joueur Xbox sur votre site.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - Bibliothèque d’assistance de Web ASP.NET (package NuGet)
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 3, à l’exception des parties qui utilisent la bibliothèque d’assistance de Web ASP.NET.


<a id="Linking_Your_Website"></a>
## <a name="linking-your-website-on-social-networking-sites"></a>Liaison de votre site Web sur les Sites de réseau Social

Si les personnes aiment pas quelque chose sur votre site, ils souhaitent souvent partager avec vos amis. Vous pouvez faciliter en affichant les glyphes (icônes) que les utilisateurs peuvent cliquer pour partager une page de Digg, Reddit, Facebook, Twitter ou des sites similaires.

Pour afficher ces glyphes, ajoutez le `LinkSharecode` application d’assistance à une page. Les personnes qui visitent votre page peuvent cliquer sur un glyphe individuel. S’ils disposent d’un compte disposant de ce site de réseau social, ils peuvent ensuite valider un lien vers votre page sur ce site.

![Image 1](13-adding-social-networking-to-your-web-site/_static/image1.jpg)

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas déjà ajouté y - créer une page nommée *ListLinkShare.cshtml* et ajouter le balisage suivant :

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample1.cshtml)]

    Dans cet exemple, lorsque le `LinkShare` s’exécute, le titre de la page est passé en tant que paramètre, qui à son tour transmet le titre de page pour le site de réseau social. Toutefois, vous pouvez transmettre une chaîne. Cet exemple spécifie également les sites de réseau social à inclure dans la liste. Vous pouvez spécifier les sites de réseau social qui s’appliquent à votre site.
- Exécutez le *ListLinkShare.cshtml* page dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.)
- Cliquez sur un des sites que vous êtes connecté à un glyphe. Ce lien vous permet à la page sur le site de réseau social sélectionnée où vous pouvez partager un lien. Par exemple, si vous cliquez sur le lien Reddit, vous êtes dirigé vers le `submit to reddit` sur le site Web Reddit.

    ![Image 2](13-adding-social-networking-to-your-web-site/_static/image2.jpg)

<a id="Adding_a_Twitter_Feed"></a>
## <a name="adding-a-twitter-feed"></a>Ajout d’un Twitter de flux

Pour plus d’informations sur l’utilisation d’une application auxiliaire Twitter qui est compatible avec la version actuelle de l’API Twitter, consultez [application auxiliaire Twitter](../ui-layouts-and-themes/twitter-helper.md). Cet exemple montre comment écrire votre propre programme d’assistance, vous pouvez réutiliser facilement le code à partir de nombreuses pages.

<a id="Displaying_a_Facebook_Button"></a>
## <a name="displaying-a-facebook-quotlikequot-button"></a>Afficher un Facebook &quot;comme&quot; bouton

Dans certains cas, la meilleure solution est pour obtenir le code directement depuis le fournisseur de réseau social, plutôt que de compter sur une application auxiliaire. Cela est particulièrement vrai si le fournisseur de réseau social met à jour ses options plus rapidement que la mise à jour de l’application d’assistance.

Pour ajouter des fonctionnalités de Facebook (par exemple, le bouton Like) à votre site, vous pouvez récupérer des extraits de code à partir de la [developers.facebook.com](https://developers.facebook.com/) site. Sur le site Facebook, vous utilisez leurs outils pour générer un extrait de code qui s’applique à votre site.

Le code en surbrillance suivant est le code qui a été récupéré à partir de l’outil comme bouton sur le site developers.facebook.com. Vous devez fournir votre propre code d’application.

[!code-html[Main](13-adding-social-networking-to-your-web-site/samples/sample2.html?highlight=7-14,16-17)]

<a id="Rendering_a_Gravatar_Image"></a>
## <a name="rendering-a-gravatar-image"></a>Rendu d’une Image Gravatar

A *Gravatar* (un &quot;avatar globalement reconnu&quot;) est une image qui peut être utilisé sur plusieurs sites Web en tant que votre avatar &#8212; autrement dit, une image qui vous représente. Par exemple, un Gravatar peut identifier une personne dans un billet de forum, un commentaire de blog et ainsi de suite. (Vous pouvez inscrire votre propre Gravatar sur le site Web de Gravatar à [http://www.gravatar.com/](http://www.gravatar.com/).) Si vous souhaitez afficher des images en regard des noms de personnes ou les adresses de messagerie sur votre site Web, vous pouvez utiliser l’application d’assistance Gravatar.

Dans cet exemple, vous utilisez un Gravatar unique qui représente vous-même. Une autre façon d’utiliser un Gravatar doit permettre aux utilisateurs de spécifier leur adresse Gravatar lorsqu’ils s’inscrivent sur votre site. (Vous pouvez apprendre à permettre aux utilisateurs d’inscrire dans [Ajout de la sécurité et l’appartenance à un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).) Ensuite, chaque fois que vous affichez les informations de cet utilisateur, il vous suffit d’ajouter le Gravatar à vous permet d’afficher le nom d’utilisateur.

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
2. Créer une nouvelle page web nommée *Gravatar.cshtml*.
3. Ajoutez le balisage suivant au fichier : 

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample3.cshtml)]

    Le `Gravatar.GetHtml` méthode affiche l’image Gravatar sur la page. Pour modifier la taille de l’image, vous pouvez inclure un nombre en tant que second paramètre. La taille par défaut est 80. Les numéros d’inférieur à 80 rendre l’image plus petits. Nombres supérieurs à 80 agrandir l’image.
4. Dans le `Gravatar.GetHtml` méthodes, remplacez `<Your Gravatar account here>` avec l’adresse de messagerie que vous utilisez pour votre compte Gravatar. (Si vous n’avez pas un compte Gravatar, vous pouvez utiliser l’adresse de messagerie d’un utilisateur.)
5. Exécutez la page dans votre navigateur. La page affiche deux images Gravatar pour l’adresse de messagerie que vous avez spécifié. La deuxième image est inférieure à la première. 

    ![Image 4](13-adding-social-networking-to-your-web-site/_static/image3.jpg)

<a id="Displaying_an_Xbox_Gamer_Card"></a>
## <a name="displaying-an-xbox-gamer-card"></a>Affichage d’une carte de joueur Xbox

Lorsque les personnes jouent Microsoft Xbox en ligne, chaque utilisateur possède un ID unique. Les statistiques sont conservées pour chaque lecteur sous la forme d’une carte de joueur, qui indique leur réputation, les jeux et récemment lu jeux. Si vous êtes un passionné de Xbox, vous pouvez afficher votre carte de joueur sur les pages de votre site à l’aide de la `GamerCard` helper.

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
2. Créer une nouvelle page nommée *XboxGamer.cshtml* et ajoutez le balisage suivant.

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample4.cshtml)]

    Vous utilisez le `GamerCard.GetHtml` propriété pour spécifier l’alias de la carte de joueur à afficher.
3. Exécutez la page dans votre navigateur. La page affiche la carte de joueur Xbox que vous avez spécifié.

    ![Image 5](13-adding-social-networking-to-your-web-site/_static/image4.jpg)
