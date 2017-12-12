---
uid: web-forms/overview/ajax-control-toolkit/getting-started/get-started-with-the-ajax-control-toolkit-cs
title: "Prise en main la boîte à outils de contrôle AJAX (c#) | Documents Microsoft"
author: microsoft
description: "En savoir plus vous devez connaître pour commencer à utiliser les outils de contrôle AJAX."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: 16dc5c11-65be-4eae-a818-9fad7f8259c6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/getting-started/get-started-with-the-ajax-control-toolkit-cs
msc.type: authoredcontent
ms.openlocfilehash: 8d3f4dd26a9f82dce78b1c3665f9da6b54bdacba
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="get-started-with-the-ajax-control-toolkit-c"></a>Prise en main la boîte à outils de contrôle AJAX (c#)
====================
par [Microsoft](https://github.com/microsoft)

> En savoir plus vous devez connaître pour commencer à utiliser les outils de contrôle AJAX.


La boîte à outils de contrôle AJAX contient plus de 30 contrôles gratuits que vous pouvez utiliser dans vos applications ASP.NET. Dans ce didacticiel, vous allez apprendre à télécharger les outils de contrôle AJAX et ajouter les contrôles de boîte à outils à votre boîte à outils Visual Studio/Visual Web Developer Express.

## <a name="downloading-the-ajax-control-toolkit"></a>Téléchargement de la boîte à outils de contrôle AJAX

Le [outils de contrôle AJAX](http://devexpress.com/act) est un projet open source développé par les membres de la Communauté ASP.NET et l’équipe d’ASP.NET. 


[![Téléchargement de la boîte à outils de contrôle AJAX](get-started-with-the-ajax-control-toolkit-cs/_static/image1.jpg)](get-started-with-the-ajax-control-toolkit-cs/_static/image1.png)

**Figure 01**: téléchargement de la boîte à outils de contrôle AJAX ([cliquez pour afficher l’image en taille réelle](get-started-with-the-ajax-control-toolkit-cs/_static/image2.png))


Après avoir téléchargé le fichier, vous devez débloquer le fichier. Cliquez sur le fichier, sélectionnez Propriétés, puis cliquez sur le **Unblock** bouton (voir Figure 2).


[![Débloquer le fichier ZIP de boîte à outils de contrôle AJAX](get-started-with-the-ajax-control-toolkit-cs/_static/image2.jpg)](get-started-with-the-ajax-control-toolkit-cs/_static/image3.png)

**Figure 02**: débloquer le fichier ZIP de boîte à outils de contrôle AJAX ([cliquez pour afficher l’image en taille réelle](get-started-with-the-ajax-control-toolkit-cs/_static/image4.png))


Après avoir débloqué le fichier, vous pouvez décompresser le fichier : cliquez sur le fichier et sélectionnez le **extraire tout** option de menu. Maintenant, nous sommes prêts à ajouter de la boîte à outils à la boîte à outils de Visual Studio/Visual Web Developer.

## <a name="adding-the-ajax-control-toolkit-to-the-toolbox"></a>Ajout de la boîte à outils de contrôle AJAX à la boîte à outils

Le moyen le plus simple d’utiliser les outils de contrôle AJAX consiste à ajouter de la boîte à outils à votre boîte à outils de Visual Studio/Visual Web Developer (voir Figure 3). De cette façon, vous pouvez simplement faire glisser un contrôle de boîte à outils sur une page lorsque vous souhaitez utiliser.


[![Outils de contrôle AJAX s’affiche dans la boîte à outils](get-started-with-the-ajax-control-toolkit-cs/_static/image3.jpg)](get-started-with-the-ajax-control-toolkit-cs/_static/image5.png)

**Figure 03**: outils de contrôle AJAX s’affiche dans la boîte à outils ([cliquez pour afficher l’image en taille réelle](get-started-with-the-ajax-control-toolkit-cs/_static/image6.png))


Tout d’abord, vous devez ajouter un onglet de boîte à outils de contrôle AJAX à la boîte à outils. Procédez comme suit.

1. Créer un site Web ASP.NET en sélectionnant l’option de menu fichier, nouveau site Web. Double-cliquez sur la page Default.aspx dans la fenêtre de l’Explorateur de solutions pour ouvrir le fichier dans l’éditeur.
2. Avec le bouton droit de la boîte à outils sous l’onglet Général et sélectionnez l’option de menu **ajouter un onglet** (voir Figure 4).
3. Entrez un nouvel onglet Outils de contrôle AJAX.


[![Ajout d’un nouvel onglet](get-started-with-the-ajax-control-toolkit-cs/_static/image4.jpg)](get-started-with-the-ajax-control-toolkit-cs/_static/image7.png)

**Figure 04**: ajout d’un nouvel onglet ([cliquez pour afficher l’image en taille réelle](get-started-with-the-ajax-control-toolkit-cs/_static/image8.png))


Ensuite, vous devez ajouter les contrôles de boîte à outils de contrôle AJAX pour le nouvel onglet. Procédez comme suit :

- Avec le bouton droit sous l’onglet Outils de contrôle AJAX et sélectionnez l’option de menu **choisir des éléments (voir Figure 5)**.
- Accédez à l’emplacement où vous avez décompressé les outils de contrôle AJAX et sélectionnez l’assembly AjaxControlToolkit.dll.


[![Choisissez les éléments à ajouter à la boîte à outils](get-started-with-the-ajax-control-toolkit-cs/_static/image5.jpg)](get-started-with-the-ajax-control-toolkit-cs/_static/image9.png)

**Figure 05**: choisissez les éléments à ajouter à la boîte à outils ([cliquez pour afficher l’image en taille réelle](get-started-with-the-ajax-control-toolkit-cs/_static/image10.png))


Après avoir effectué ces étapes, tous les contrôles de boîte à outils seront affiche dans votre boîte à outils.

## <a name="upgrading-to-a-new-version-of-the-toolkit"></a>La mise à niveau vers une nouvelle Version de la boîte à outils

Si vous utilisiez une version antérieure du Kit de ressources et que vous devez maintenant déplacer vers une version ultérieure ici sont les étapes recommandées :

- Fichiers binaires - supprimer l’ancienne version de l’assembly AjaxControlToolkit.dll dans votre dossier Bin du site Web.
- Les éléments de boîte à outils - supprimer de l’onglet Outils de contrôle AJAX et suivez les étapes ci-dessus pour créer de nouveau l’onglet avec la nouvelle version de l’assembly AjaxControlToolkit.dll.

>[!div class="step-by-step"]
[Next](using-ajax-control-toolkit-controls-and-control-extenders-cs.md)
