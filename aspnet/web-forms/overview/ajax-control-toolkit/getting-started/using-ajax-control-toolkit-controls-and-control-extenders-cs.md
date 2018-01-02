---
uid: web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-cs
title: "À l’aide de contrôles de boîte à outils de contrôle AJAX et extensions de contrôle (c#) | Documents Microsoft"
author: microsoft
description: "Découvrez comment ajouter des contrôles de boîte à outils de contrôle AJAX et aux extendeurs à vos pages ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: c1e6b51c-3bc3-4bf7-9916-9991197af3dd
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-cs
msc.type: authoredcontent
ms.openlocfilehash: 42a210ac41e83e2379aa64979f42ce66c843f878
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-ajax-control-toolkit-controls-and-control-extenders-c"></a>À l’aide de contrôles de boîte à outils de contrôle AJAX et extensions de contrôle (c#)
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment ajouter des contrôles de boîte à outils de contrôle AJAX et aux extendeurs à vos pages ASP.NET.


La boîte à outils de contrôle AJAX contient un ensemble de contrôles et les extendeurs de contrôle. Dans ce bref didacticiel, vous allez apprendre à ajouter des contrôles et les extensions de contrôle à une page ASP.NET.

> [!NOTE] 
> 
> Pour obtenir des instructions sur l’installation de la boîte à outils de contrôle AJAX et l’ajout de la boîte à outils de contrôle AJAX à la boîte à outils Visual Studio/Visual Web Developer, consultez le didacticiel [prise en main la boîte à outils de contrôle AJAX](get-started-with-the-ajax-control-toolkit-cs.md).


## <a name="using-ajax-control-toolkit-controls"></a>À l’aide de contrôles de boîte à outils de contrôle AJAX

Un contrôle de boîte à outils de contrôle AJAX fonctionne comme un contrôle ASP.NET normal. Vous pouvez faire glisser le contrôle à partir de la boîte à outils vers une page ASP.NET. Vous pouvez ajouter le contrôle à la page en mode Création ou de vue de Source.

Il existe un besoin particulier lorsque vous utilisez les contrôles à partir de la boîte à outils de contrôle AJAX. La page doit contenir un contrôle ScriptManager. Le contrôle ScriptManager est responsable de l’inclure tous les JavaScript nécessaire requis par les contrôles de boîte à outils de contrôle AJAX.

Par exemple, l’onglet Outils de contrôle AJAX inclut un contrôle nommé le contrôle de l’éditeur. Ce contrôle affiche un éditeur HTML complet. Suivez ces étapes pour ajouter le contrôle d’édition à une page :

1. Créer une page ASP.NET nommée ShowEditor.aspx
2. Sélectionnez le contrôle ScriptManager sous l’onglet Extensions AJAX dans la boîte à outils et faites glisser le contrôle sur la page.
3. Sélectionnez le contrôle d’édition sous l’onglet Outils de contrôle AJAX dans la boîte à outils et faites glisser le contrôle sur la page (voir Figure 1). Le concepteur doit ressembler à la Figure 2.
4. Exécuter le site web en sélectionnant l’option de menu **déboguer, démarrer le débogage** ou en appuyant sur la touche F5.
5. Vous devez voir la page dans la Figure 3.


[![Sélection du contrôle de l’éditeur HTML](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.png)

**Figure 01**: sélection du contrôle de l’éditeur HTML ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.png))


[![Concepteur de Visual Studio avec le contrôle ScriptManager et modifier](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.png)

**Figure 02**: concepteur avec le contrôle ScriptManager et l’édition de Visual Studio ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.png))


[![La page DisplayEditor.aspx](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.png)

**Figure 03**: DisplayEditor.aspx la page ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.png))


## <a name="using-ajax-control-toolkit-control-extenders"></a>À l’aide des extensions de contrôle de boîte à outils de contrôle AJAX

La boîte à outils de contrôle AJAX contient également des extensions de contrôle. Comme son nom l’indique, un extendeur de contrôle étend les fonctionnalités d’un contrôle existant. Par exemple, l’extendeur de contrôle ConfirmButton étend le contrôle ASP.NET standard. L’extendeur modifie le comportement de s de contrôle de bouton afin que le bouton affiche une boîte de dialogue de confirmation lorsque vous cliquez dessus.

Un extendeur de contrôle, comme un contrôle de boîte à outils de contrôle AJAX, nécessite un contrôle ScriptManager. Avant de commencer à l’aide des extensions de contrôle dans la page, vous devez ajouter un contrôle ScriptManager à une page.

Suivez ces étapes pour utiliser l’extendeur de contrôle ConfirmButton :

1. Créer une page ASP.NET nommée ShowConfirmButton.aspx
2. Ajouter un contrôle ScriptManager à la page en faisant glisser le contrôle sur la page sous l’onglet Extensions AJAX.
3. Ajouter un contrôle bouton standard à la page en faisant glisser le bouton sous l’onglet Standard dans la boîte à outils jusqu'à l’aire du concepteur.
4. Cliquez sur le **ajouter un extendeur** option de tâches (voir Figure 4).
5. Dans la boîte de dialogue Choisir un extendeur, sélectionnez ConfirmButtonExtender (voir Figure 5) et cliquez sur le bouton OK.
6. Sélectionnez le contrôle de bouton dans le concepteur et développez les extendeurs, Button1\_nœud ConfirmButtonExtender dans la fenêtre Propriétés (voir Figure 6). Affectez la valeur *vraiment ?* à la propriété ConfirmText la valeur.
7. Exécuter la page en sélectionnant l’option de menu **déboguer, démarrer le débogage** ou appuyez sur la touche F5.


[![L’option de la tâche Ajouter un extendeur](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.png)

**Figure 04**: l’option Ajouter un extendeur la tâche ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image8.png))


[![En sélectionnant l’extendeur de contrôle ConfirmButton](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image9.png)

**Figure 05**: en sélectionnant l’extendeur de contrôle ConfirmButton ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image10.png))


[![Définition d’une propriété ConfirmButton](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image11.png)

**Figure 06**: définition d’une propriété ConfirmButton ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image12.png))


Lorsque la page s’ouvre, vous devriez voir un bouton. Lorsque vous cliquez sur le bouton, vous obtenez la boîte de dialogue de confirmation dans la Figure 7.


[![Affichage de la boîte de dialogue de confirmation](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image13.png)

**Figure 07**: affichage de la boîte de dialogue de confirmation ([cliquez pour afficher l’image en taille réelle](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image14.png))


Notez que vous normalement ne faites pas glisser un extendeur de contrôle sur une page. Au lieu de cela, vous utilisez la **ajouter un extendeur** option pour ajouter un extendeur à un contrôle que vous avez déjà ajouté à une page de tâches. Notez, en outre, que vous définissez contrôle propriétés extendeur en ouvrant la feuille de propriétés pour le contrôle en cours d’extension.

Un seul contrôle ASP.NET peut être étendu par plusieurs systèmes de contrôle. La feuille de propriétés pour le contrôle en cours d’extension répertorie tous les extendeurs de contrôle associés au contrôle.

>[!div class="step-by-step"]
[Précédent](get-started-with-the-ajax-control-toolkit-cs.md)
[Suivant](creating-a-custom-ajax-control-toolkit-control-extender-cs.md)
