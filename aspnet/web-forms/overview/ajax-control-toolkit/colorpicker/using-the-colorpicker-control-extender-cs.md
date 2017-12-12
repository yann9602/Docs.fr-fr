---
uid: web-forms/overview/ajax-control-toolkit/colorpicker/using-the-colorpicker-control-extender-cs
title: "À l’aide de l’extendeur du contrôle ColorPicker (c#) | Documents Microsoft"
author: microsoft
description: "ColorPicker est un extendeur ASP.NET AJAX qui fournit une fonctionnalité de choix de couleurs de côté client avec l’interface utilisateur dans un contrôle de menu contextuel. Elle peut être jointe à n’importe quel ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: 0d86a1e7-a910-4ab2-b85c-7a9ea6906c39
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/colorpicker/using-the-colorpicker-control-extender-cs
msc.type: authoredcontent
ms.openlocfilehash: 5b3cde9552e8aecd5e7e651a825902fb79ae108c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-colorpicker-control-extender-c"></a>À l’aide de l’extendeur du contrôle ColorPicker (c#)
====================
par [Microsoft](https://github.com/microsoft)

> ColorPicker est un extendeur ASP.NET AJAX qui fournit une fonctionnalité de choix de couleurs de côté client avec l’interface utilisateur dans un contrôle de menu contextuel. Elle peut être jointe à n’importe quel contrôle ASP.NET de zone de texte. Il s’agit.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez utiliser l’extendeur de contrôle ColorPicker de boîte à outils de contrôle AJAX. L’extendeur de contrôle ColorPicker affiche une boîte de dialogue contextuelle qui vous permet de sélectionner une couleur. Le composant ColorPicker est utile lorsque vous souhaitez fournir une interface utilisateur intuitive pour un utilisateur de choisir une couleur.

## <a name="extending-a-textbox-control-with-the-colorpicker-control-extender"></a>Étendre un contrôle de zone de texte avec l’extendeur du contrôle ColorPicker

Par exemple, imaginez que vous souhaitez créer un site Web qui permet des visiteurs créer des cartes de visite personnalisées. Les visiteurs peuvent entrer le texte pour une carte de visite et sélectionner la couleur. La page ASP.NET dans la liste 1 contienne deux contrôles de zone de texte nommée txtCardText et txtCardColor. Quand vous envoyez le formulaire, les valeurs sélectionnées sont affichés (voir Figure 1).


[![Formulaire simple pour la création d’une carte de visite](using-the-colorpicker-control-extender-cs/_static/image1.jpg)](using-the-colorpicker-control-extender-cs/_static/image1.png)

**Figure 01**: une forme Simple pour la création d’une carte de visite ([cliquez pour afficher l’image en taille réelle](using-the-colorpicker-control-extender-cs/_static/image2.png))


**La liste 1 - CreateCard.aspx**

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample1.aspx)]

L’écran de liste 1 fonctionne, mais elle ne fournit pas une expérience utilisateur satisfaisante. L’utilisateur a vers le type d’une couleur dans la zone de texte. Si l’utilisateur souhaite obtenir une couleur spécialisée - par exemple, uniquement la nuance de droite de pea vert - ensuite, l’utilisateur doit déterminer le code de couleur HTML sans l’aide.

Vous pouvez utiliser l’extendeur de contrôle ColorPicker pour créer une meilleure expérience utilisateur. Le composant ColorPicker affiche une boîte de dialogue couleur lorsque vous déplacez le focus vers un contrôle de zone de texte (voir Figure 2).


[![L’extendeur du contrôle ColorPicker](using-the-colorpicker-control-extender-cs/_static/image2.jpg)](using-the-colorpicker-control-extender-cs/_static/image3.png)

**Figure 02**: l’extendeur de contrôle ColorPicker ([cliquez pour afficher l’image en taille réelle](using-the-colorpicker-control-extender-cs/_static/image4.png))


Vous devez effectuer deux étapes pour utiliser l’extendeur de contrôle ColorPicker avec le formulaire dans la liste 1 :

1. Ajouter un contrôle ScriptManager à la page
2. Ajoutez l’extendeur de contrôle ColorPicker à la page

Avant de pouvoir utiliser le composant ColorPicker, vous devez ajouter un ScriptManager à votre page. Pour ajouter le ScriptManager est intéressant juste en dessous du ouverture côté serveur &lt;formulaire&gt; balise. Vous pouvez faire glisser ScriptManager sur la page à partir de la boîte à outils (ScriptManager se trouve sous l’onglet Extensions AJAX). Vous pouvez également taper la balise suivante dans la vue de Source sous la balise de formulaire côté serveur d’ouverture :

&lt;ASP : ScriptManager ID = « ScriptManager1 » runat = « server » /&gt;

Le moyen le plus simple pour ajouter l’extendeur de contrôle ColorPicker à la page est en mode Design. Si vous pointez votre souris sur la zone de texte txtCardColor, une option de la tâche s’affiche le permet de vous permet d’ajouter un extendeur (voir Figure 3). Si vous sélectionnez cette option, l’Assistant extendeur s’affiche (voir Figure 4).


[![Ajout d’un extendeur](using-the-colorpicker-control-extender-cs/_static/image3.jpg)](using-the-colorpicker-control-extender-cs/_static/image5.png)

**Figure 03**: ajout d’un extendeur ([cliquez pour afficher l’image en taille réelle](using-the-colorpicker-control-extender-cs/_static/image6.png))


[![Sélection d’un extendeur de contrôle avec l’Assistant extendeur](using-the-colorpicker-control-extender-cs/_static/image4.jpg)](using-the-colorpicker-control-extender-cs/_static/image7.png)

**Figure 04**: sélection d’un extendeur de contrôle avec l’Assistant extendeur ([cliquez pour afficher l’image en taille réelle](using-the-colorpicker-control-extender-cs/_static/image8.png))


Vous pouvez choisir l’extendeur ColorPicker pour étendre la zone de texte txtCardColor avec l’extendeur ColorPicker. Cliquez sur OK pour fermer la boîte de dialogue.

Après avoir apporté ces modifications, la source de la page ressemble à la liste 2.

Listing 2 - CreateCard.aspx (avec ColorPicker)

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample2.aspx)]

Notez que la page contienne désormais un contrôle ColorPickerExtender qui apparaît directement sous le contrôle de zone de texte txtCardColor. Le contrôle ColorPickerExtender étend le contrôle txtCardColor afin qu’il affiche une boîte de dialogue de sélecteur de couleurs.

## <a name="using-a-button-to-launch-the-color-picker-dialog"></a>Utilisation d’un bouton pour lancer la boîte de dialogue de sélecteur de couleurs

L’extendeur ColorPicker prend en charge les propriétés suivantes :

- PopupButtonId - l’ID d’un bouton dans la page qui provoque la boîte de dialogue du sélecteur de couleur apparaissent.
- PopupPosition - la position, par rapport au contrôle cible, de la boîte de dialogue de sélecteur de couleurs. Les valeurs possibles sont absolue, centre, en bas à gauche, en bas à droite, en haut à gauche, en haut à droite, droite et gauche (la valeur par défaut est en bas à gauche).
- SampleControlId - l’ID d’un contrôle qui affiche la couleur sélectionnée.
- SelectedColor - couleur initiale sélectionnée par le composant ColorPicker.

Vous pouvez utiliser ces propriétés pour personnaliser l’affichage de la boîte de dialogue de sélecteur de couleurs et l’affichage de la couleur sélectionnée. La page de liste 3 illustre comment vous pouvez utiliser plusieurs de ces propriétés.

**La liste 3 - CreateCardButton.aspx**

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample3.aspx)]

La page de liste 3 inclut un choisir la couleur du bouton (voir Figure 5). Lorsque vous cliquez sur ce bouton, la boîte de dialogue de sélecteur de couleur s’affiche au-dessus de la zone de texte. Si vous sélectionnez une couleur dans la boîte de dialogue la couleur sélectionnée s’affiche en tant que la couleur d’arrière-plan de la lblSample contrôle Label.

La propriété ColorPicker PopupButtonID est utilisée pour associer le bouton de choisir la couleur à l’extendeur ColorPicker. Lorsque vous fournissez une valeur pour la propriété PopupButtonID, la boîte de dialogue de sélecteur de couleurs ne s’affiche plus lorsque le contrôle cible a le focus. Vous devez cliquer sur le bouton pour afficher la boîte de dialogue.

La propriété SampleControlID est utilisée pour associer un contrôle qui affiche la couleur sélectionnée avec le composant ColorPicker. Le composant ColorPicker modifie la couleur d’arrière-plan de ce contrôle la couleur actuellement sélectionnée.


[![Affichage de la boîte de dialogue du sélecteur de couleurs avec un bouton](using-the-colorpicker-control-extender-cs/_static/image5.jpg)](using-the-colorpicker-control-extender-cs/_static/image9.png)

**Figure 05**: affichage de la boîte de dialogue du sélecteur de couleurs avec un bouton ([cliquez pour afficher l’image en taille réelle](using-the-colorpicker-control-extender-cs/_static/image10.png))


## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à utiliser l’extendeur de contrôle ColorPicker pour afficher une boîte de dialogue Sélecteur de couleurs contextuelle. Tout d’abord, nous avons examiné comment vous pouvez afficher la boîte de dialogue lorsque le focus est déplacé vers un contrôle de zone de texte. Ensuite, vous avez appris à créer un bouton qui affiche la boîte de dialogue de sélecteur de couleur quand le bouton est activé.

>[!div class="step-by-step"]
[Next](using-the-colorpicker-control-extender-vb.md)
