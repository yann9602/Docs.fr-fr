---
uid: web-pages/overview/ui-layouts-and-themes/10-working-with-video
title: "Affichant la vidéo dans ASP.NET Web Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Ce chapitre explique comment afficher la vidéo dans un ASP.NET Web Pages avec la page de syntaxe Razor."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 332fb3da-e2a5-460d-bb90-dd911e1e2c95
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/10-working-with-video
msc.type: authoredcontent
ms.openlocfilehash: 0e1849fb780908b55520d8108e2227d046759987
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="displaying-video-in-an-aspnet-web-pages-razor-site"></a>Affichant la vidéo dans un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment utiliser un lecteur vidéo (multimédia) dans un site Web ASP.NET Web Pages (Razor) pour permettre aux utilisateurs d’afficher les vidéos qui sont stockés sur le site. ASP.NET Web Pages avec syntaxe Razor vous permet de lire le disque mémoire Flash (*.swf*), Media Player (*.wmv*) et Silverlight (*.xap*) vidéos.
> 
> Ce que vous allez apprendre :
> 
> - Comment choisir un lecteur vidéo.
> - Comment ajouter une vidéo à une page web.
> - Comment définir les attributs de lecteur vidéo.
> 
> Il s’agit de Razor ASP.NET pages fonctionnalités introduites dans l’article :
> 
> - Le `Video` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - WebMatrix 2
>   
> 
> Ce didacticiel fonctionne également avec WebMatrix 3.


## <a name="introduction"></a>Introduction

Vous pouvez souhaiter afficher une vidéo sur votre site. Une manière de procéder consiste à lier à un site qui a déjà la vidéo, comme YouTube. Si vous souhaitez incorporer une vidéo à partir de ces sites directement dans vos propres pages, vous pouvez généralement obtenir le balisage HTML à partir du site et copiez-le dans votre page. Par exemple, l’exemple suivant montre comment incorporer un YouTube vidéo :

[!code-html[Main](10-working-with-video/samples/sample1.html?highlight=10-14)]

Si vous souhaitez lire une vidéo qui se trouve sur votre site Web (et non sur un site de partage de vidéo public), vous ne peut pas lier directement à l’aide du balisage incorporé comme suit. Toutefois, vous pouvez lire des vidéos à partir de votre site à l’aide de la `Video` assistance, qui restitue un lecteur multimédia directement dans une page.

<a id="Choosing_a_Video_Player"></a>
## <a name="choosing-a-video-player"></a>Choix d’un lecteur vidéo

Il existe un grand nombre de formats pour les fichiers vidéo, et chaque format nécessite généralement un autre lecteur et une manière différente pour configurer le lecteur. Dans les pages ASP.NET Razor, vous pouvez lire une vidéo dans une page web à l’aide de la `Video` helper. Le `Video` helper simplifie le processus d’incorporation de vidéos dans une page web, car il génère automatiquement le `object` et `embed` des éléments HTML qui sont normalement utilisés pour ajouter une vidéo à la page.

Le `Video` helper prend en charge les lecteurs multimédias suivants :

- Adobe Flash
- Windows MediaPlayer
- Microsoft Silverlight

### <a name="the-flash-player"></a>Le lecteur Flash

Le `Flash` lecteur de la `Video` assistance vous permettent de lire des vidéos Flash (*.swf* fichiers) dans une page web. Au minimum, vous devez fournir un chemin d’accès au fichier vidéo. Si vous spécifiez rien d’autre que le chemin d’accès, le lecteur utilise les valeurs par défaut qui sont définies par la version actuelle de Flash. Les paramètres par défaut sont :

- La vidéo est affichée à l’aide de la largeur de la valeur par défaut et sa hauteur et sans une couleur d’arrière-plan.
- La vidéo est lue automatiquement lors de la charge de la page.
- Les boucles de la vidéo en continu jusqu'à ce qu’il est arrêté de manière explicite.
- La vidéo est mis à l’échelle pour afficher l’intégralité de la vidéo, au lieu de rognage pour s’ajuster à une taille spécifique.
- La vidéo est lue dans une fenêtre.

### <a name="the-mediaplayer-player"></a>Le lecteur MediaPlayer

Le `MediaPlayer` lecteur de la `Video` assistance vous permet de lire les vidéos Windows Media (*.wmv* fichiers), Windows Media audio (*.wma* fichiers) et MP3 (*.mp3* les fichiers) dans une page web. Vous devez inclure le chemin d’accès du fichier multimédia à lire ; tous les autres paramètres sont facultatifs. Si vous spécifiez uniquement un chemin d’accès, le lecteur utilise les paramètres par défaut définies par la version actuelle de MediaPlayer, telle que :

- La vidéo est affichée à l’aide de la largeur de la valeur par défaut et sa hauteur.
- La vidéo est lue automatiquement lors de la charge de la page.
- La vidéo est lue une fois (elle ne de boucle).
- Le lecteur affiche l’ensemble des contrôles dans l’interface utilisateur.
- La vidéo est lue une fenêtre.

### <a name="the-silverlight-player"></a>Le lecteur Silverlight

Le `Silverlight` lecteur de la `Video` assistance vous permet de lire la vidéo Windows Media (*.wmv* fichiers), Windows Media Audio (*.wma* fichiers) et MP3 (*.mp3* fichiers). Vous devez définir le paramètre de chemin d’accès pointe vers un package d’application basée sur Silverlight (*.xap* fichier). Vous devez également définir les paramètres de largeur et hauteur. Tous les autres paramètres sont facultatifs. Lorsque vous utilisez le lecteur Silverlight pour la vidéo, si vous affectez uniquement les paramètres requis, le lecteur Silverlight affiche la vidéo sans une couleur d’arrière-plan.

> [!NOTE]
> Dans le cas où vous ne connaissez pas déjà Silverlight : le *.xap* fichier est un fichier compressé qui contient des instructions de mise en page dans un *.xaml* de fichiers, le code managé dans des assemblys et des ressources facultatives. Vous pouvez créer un *.xap* fichier dans Visual Studio en tant qu’un projet d’application Silverlight.


Le `Silverlight` lecteur vidéo utilise à la fois les paramètres que vous fournissez pour le lecteur et les paramètres qui sont fournis dans le *.xap* fichier.

> [!TIP] 
> 
> <a id="SB_MimeTypes"></a>
> ### <a name="mime-types"></a>Types MIME
> 
> Lorsqu’un navigateur télécharge un fichier, le navigateur permet de s’assurer que le type de fichier correspond au type MIME spécifié pour le document qui est rendu. Le type MIME est le type de contenu de type ou le support d’un fichier. Le `Video` assistance utilise les types MIME suivants :
> 
> - `application/x-shockwave-flash`
> - `application/x-mplayer2`
> - `application/x-silverlight-2`


<a id="Playing_Flash"></a>
## <a name="playing-flash-swf-videos"></a>Lecture de vidéos de Flash (.swf)

Cette procédure vous montre comment lire une vidéo Flash nommée *sample.swf*. La procédure suppose que vous avez sélectionné un dossier nommé *Media* sur votre site et que le *.swf* fichier se trouve dans ce dossier.

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas déjà ajouté.
2. Dans le site Web, ajoutez une page et nommez-le *FlashVideo.cshtml*.
3. Ajoutez le balisage suivant à la page : 

    [!code-cshtml[Main](10-working-with-video/samples/sample2.cshtml)]
4. Exécutez la page dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.) La page s’affiche et la vidéo est lue automatiquement. 

    ![[image] ] (10-working-with-video/_static/image1.jpg "ch08_video-1.jpg")

Vous pouvez définir le `quality` paramètre pour une vidéo Flash à `low`, `autolow`, `autohigh`, `medium`, `high`, et `best`:

[!code-cshtml[Main](10-working-with-video/samples/sample3.cshtml)]

Vous pouvez modifier la disque mémoire Flash vidéo est lue à une taille spécifique à l’aide de le `scale` paramètre que vous pouvez définir pour les éléments suivants :

- `showall`. Cela rend la vidéo complète visible tout en conservant les proportions d’origine. Toutefois, vous pouvez obtenir des bordures de chaque côté.
- `noorder`. Cela met à l’échelle la vidéo tout en conservant les proportions d’origine, mais il peut être tronqué.
- `exactfit`. Cela rend la vidéo complète visible sans conserver les proportions d’origine, mais la distorsion peut se produire.

Si vous ne spécifiez pas un `scale` paramètre, la vidéo complète sera visible et les proportions d’origine est conservée sans découpage. L’exemple suivant montre comment utiliser le `scale` paramètre :

[!code-cshtml[Main](10-working-with-video/samples/sample4.cshtml)]

Le lecteur Flash prend en charge un paramètre nommé de mode vidéo `windowMode`. Vous pouvez définir cette option `window`, `opaque`, et `transparent`. Par défaut, le `windowMode` a la valeur `window`, qui affiche la vidéo dans une fenêtre distincte sur la page web. Le `opaque` paramètre masque tout derrière la vidéo sur la page web. Le `transparent` paramètre permet l’arrière-plan de la page web dans la vidéo, en supposant que n’importe quelle partie de la vidéo est transparente.

<a id="Playing_MediaPlayer"></a>
## <a name="playing-mediaplayer-wmv-videos"></a>Lecture MediaPlayer (*.wmv*) vidéos

La procédure suivante vous montre comment lire une vidéo de la fenêtre support nommée *sample.wmv* qui se trouve dans le *Media* dossier.

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
2. Créer une nouvelle page nommée *MediaPlayerVideo.cshtml*.
3. Ajoutez le balisage suivant à la page : 

    [!code-cshtml[Main](10-working-with-video/samples/sample5.cshtml)]
4. Exécutez la page dans un navigateur. La vidéo charge et lit automatiquement. 

    ![[image] ] (10-working-with-video/_static/image2.jpg "ch08_video-2.jpg")

Vous pouvez définir `playCount` vers un entier qui indique combien de fois pour lire la vidéo automatiquement :

[!code-cshtml[Main](10-working-with-video/samples/sample6.cshtml)]

Le `uiMode` paramètre vous permet de spécifier les contrôles qui apparaissent dans l’interface utilisateur. Vous pouvez définir `uiMode` à `invisible`, `none`, `mini`, ou `full`. Si vous ne spécifiez pas un `uiMode` paramètre, la vidéo sera affiché avec la fenêtre d’état, de recherche de la barre, contrôler les boutons et les contrôles de volume en plus de la fenêtre de la vidéo. Ces contrôles affichera également si vous utilisez le lecteur pour lire un fichier audio. Voici un exemple montrant comment utiliser le `uiMode` paramètre :

[!code-cshtml[Main](10-working-with-video/samples/sample7.cshtml)]

Par défaut, audio est activé lorsque la vidéo est lue. Vous pouvez désactiver l’audio en définissant le `mute` valeur true au paramètre :

[!code-cshtml[Main](10-working-with-video/samples/sample8.cshtml)]

Vous pouvez contrôler le niveau audio de la vidéo MediaPlayer en définissant le `volume` paramètre à une valeur comprise entre 0 et 100. La valeur par défaut est 50. Voici un exemple :

[!code-cshtml[Main](10-working-with-video/samples/sample9.cshtml)]

<a id="Playing_Silverlight"></a>
## <a name="playing-silverlight-videos"></a>Lecture de vidéos de Silverlight

Cette procédure vous montre comment lire une vidéo de contenu dans un Silverlight *.xap* page se trouve dans un dossier nommée *Media*.

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
2. Créer une nouvelle page nommée *SilverlightVideo.cshtml*.
3. Ajoutez le balisage suivant à la page : 

    [!code-cshtml[Main](10-working-with-video/samples/sample10.cshtml)]
4. Exécutez la page dans un navigateur. 

    ![[image] ] (10-working-with-video/_static/image3.jpg "ch08_video-3.jpg")

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[Vue d’ensemble de Silverlight](https://msdn.microsoft.com/en-us/library/bb404700(VS.95).aspx)

[Attributs de balise d’objet et incorporer un périphérique flash](http://kb2.adobe.com/cps/127/tn_12701.html)

[Les balises PARAM de kit de développement logiciel Windows Media Player 11](https://msdn.microsoft.com/en-us/library/aa392321(VS.85).aspx)
