---
uid: web-pages/overview/ui-layouts-and-themes/displaying-maps-in-an-aspnet-web-pages-site
title: Affichage des mappages dans une application Web Pages (Razor) Site | Documents Microsoft
author: tfitzmac
description: "Cet article explique comment afficher des cartes interactives sur les pages d’un site Web d’ASP.NET Web Pages (Razor) en fonction de mappage des services fournis par Bing, Google, Ma..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: b5c268dd-ca6a-4562-b94c-a220fcf01f58
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/displaying-maps-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 6f3e6a0cfb8c08cd971e88986d0f059dd8237aab
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="displaying-maps-in-an-aspnet-web-pages-razor-site"></a>Affichage des mappages dans un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment afficher des cartes interactives sur les pages d’un site Web d’ASP.NET Web Pages (Razor) en fonction de mappage des services fournis par Bing, Google, MapQuest et Yahoo.
> 
> Ce que vous allez apprendre :
> 
> - Explique comment générer une table basée sur une adresse.
> - Explique comment générer une table basée sur des coordonnées de latitude et longitude.
> - Comment inscrire un compte de développeur de Bing Maps et d’obtenir une clé à utiliser avec Bing Maps.
> 
> Il s’agit de la fonctionnalité d’ASP.NET introduite dans l’article :
> 
> - Le `Maps` helper.
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


Dans les Pages Web, vous pouvez afficher les mappages sur une page à l’aide de `Maps` helper. Vous pouvez générer des mappages en vous basés sur une adresse ou sur un jeu de coordonnées de latitude et de longitude. La `Maps` classe vous permet de vous appeler des moteurs de carte populaires, y compris de Bing, Google, MapQuest et Yahoo.

Les étapes d’ajout du mappage à une page sont identiques quel des moteurs de carte que vous appelez. Il suffit d’ajouter une référence de fichier JavaScript qui rend les méthodes disponibles pour afficher la carte, puis vous appelez les méthodes de la `Maps` helper.

Vous choisissez un service de carte en fonction duquel `Maps` méthode d’assistance que vous utilisez. Vous pouvez utiliser une de ces :

- `Maps.GetBingHtml`
- `Maps.GetGoogleHtml`
- `Maps.GetYahooHtml`
- `Maps.GetMapQuestHtml`

## <a name="installing-the-pieces-you-need"></a>Installer les éléments que vous avez besoin

Pour afficher des cartes, vous avez besoin de ces éléments :

- Le `Maps` helper. Ce programme d’assistance est dans la version 2 de la bibliothèque de programmes d’assistance ASP.NET Web. Si vous n’avez pas déjà ajouté la bibliothèque, vous pouvez l’installer dans votre site en tant que package NuGet. Pour plus d’informations, consultez [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372). (Dans la galerie, recherchez le `microsoft-web-helpers` package.)
- La bibliothèque jQuery. Plusieurs des modèles de site WebMatrix incluent déjà des bibliothèques jQuery dans leurs *Script* dossiers. Si vous n’avez pas ces bibliothèques, vous pouvez télécharger la dernière bibliothèque jQuery directement à partir de la [jQuery.org](http://jQuery.org) site. Ou vous pouvez créer un nouveau site à l’aide d’un modèle (par exemple, le **Starter Site** modèle), puis copiez les fichiers jQuery à partir de ce site vers votre site actuel.

Enfin, si vous souhaitez utiliser Bing maps, vous devez tout d’abord créer un compte (gratuit) et obtenir une clé. Pour obtenir une clé, procédez comme suit :

1. Créer un compte sur le [compte de développeur de Bing Maps](https://www.microsoft.com/maps/developers/web.aspx). Vous devez disposer d’un compte de Microsoft (Windows Live ID) également.

    Vous pouvez spécifier que vous souhaitez utiliser la clé pour **/Test d’évaluation de**. Si vous testez la fonction de mappage sur votre ordinateur à l’aide de WebMatrix et IIS Express, consultez le **Site** espace de travail et notez l’URL de votre site (par exemple, `http://localhost:50408`, bien que votre numéro de port sera probablement différent). Vous pouvez utiliser cette *localhost* adresse que le site lorsque vous inscrivez.
2. Une fois que vous avez enregistré pour un compte, accédez au centre des comptes Bing Maps, puis cliquez sur **clés créez ou affichez**:

    ![mapping-2](displaying-maps-in-an-aspnet-web-pages-site/_static/image1.png)
3. Enregistrement de la clé Bing crée.

## <a name="creating-a-map-based-on-an-address-using-google"></a>Création d’une table basée sur une adresse (à l’aide de Google)

L’exemple suivant montre comment créer une page qui affiche une table basée sur une adresse. Cet exemple montre comment utiliser des cartes de Google.

1. Créez un fichier nommé *MapAddress.cshtml* à la racine du site. Cette page génère une table basée sur une adresse que vous passez à celui-ci.
2. Copiez le code suivant dans le fichier, en remplaçant le contenu existant.

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    Notez les fonctionnalités suivantes de la page :

    - Le `<script>` élément dans le `<head>` élément. Dans l’exemple, le `<script>` références de l’élément le *jquery-1.6.4.min.js* fichier, qui est une version réduite (compressée) de la bibliothèque jQuery, version 1.6.4. Notez que la référence de la part du principe que la *.js* fichier se trouve dans le *Scripts* dossier de votre site. 

        > [!NOTE]
        > Si vous utilisez une version différente de la bibliothèque jQuery, assurez-vous simplement que vous êtes pointant vers la version correctement.
    - L’appel à la `@Maps.GetGoogleHtml` dans le corps de la page. Pour mapper une adresse, vous devez passer une chaîne d’adresse. Les méthodes pour les autres moteurs de carte fonctionnent de manière similaire (`@Maps.GetYahooHtml`, `@Maps.GetMapQuestHtml`).
- Exécuter la page et entrez une adresse. La page affiche une table, basée sur des cartes de Google, qui indique l’emplacement que vous avez spécifié.

    ![mappage de-1.](displaying-maps-in-an-aspnet-web-pages-site/_static/image2.png)

## <a name="creating-a-map-based-on-latitude-and-longitude-coordinates-using-bing"></a>Création d’une table basée sur une Latitude et Longitude coordonne (à l’aide de Bing)

Cet exemple montre comment créer une table basée sur des coordonnées. Cet exemple montre comment utiliser Bing maps et inclure votre clé Bing. (Vous pouvez créer une table basée sur des coordonnées à l’aide d’autres moteurs carte également, sans l’aide d’une clé Bing).

1. Créez un fichier nommé *MapCoordinates.cshtml* à la racine du site et de remplacer le contenu existant avec le code et le balisage suivant :

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample2.cshtml)]
2. Remplacez `your-key-here` avec la clé Bing Maps que vous avez créé précédemment.
3. Exécutez le *MapCoordinates.cshtml* page, entrez les coordonnées de latitude et longitude, puis cliquez sur le **carte !** disproportionnée. (Si vous ne connaissez pas toutes les coordonnées, essayez ce qui suit. C’est un emplacement sur le campus de Redmond de Microsoft).

    - Latitude : 47.6781005859375
    - Longitude :-122.158317565918

    La page s’affiche à l’aide de coordonnées que vous avez spécifié.

    ![mappage-3](displaying-maps-in-an-aspnet-web-pages-site/_static/image3.png)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[Référence de l’API Microsoft.Maps](https://msdn.microsoft.com/library/gg427611.aspx)
