---
uid: web-forms/overview/ajax-control-toolkit/getting-started/creating-a-custom-ajax-control-toolkit-control-extender-cs
title: "Création d’un AJAX personnalisé extendeur de contrôle de boîte à outils (c#) d’un contrôle | Documents Microsoft"
author: microsoft
description: "Extensions personnalisées permettent de personnaliser et étendre les fonctionnalités des contrôles ASP.NET sans avoir à créer de nouvelles classes."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: 96b56eca-a892-45a4-96b4-67e61178650a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/getting-started/creating-a-custom-ajax-control-toolkit-control-extender-cs
msc.type: authoredcontent
ms.openlocfilehash: 2ae03484dd1161c65b77f4718bb8cedb5abfdd82
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-custom-ajax-control-toolkit-control-extender-c"></a>Création d’un extendeur de contrôle de boîte à outils de contrôle AJAX personnalisés (c#)
====================
par [Microsoft](https://github.com/microsoft)

> Extensions personnalisées permettent de personnaliser et étendre les fonctionnalités des contrôles ASP.NET sans avoir à créer de nouvelles classes.


Dans ce didacticiel, vous allez apprendre à créer un extendeur de contrôle de boîte à outils de contrôle AJAX personnalisé. Nous créons une simple, mais utile, nouvel extendeur qui modifie l’état d’un bouton de désactivé à activé lorsque vous tapez le texte dans une zone de texte. Après avoir lu ce didacticiel, vous serez en mesure d’étendre avec vos propres extensions de contrôle ASP.NET AJAX Toolkit.

Vous pouvez créer des extensions de contrôle personnalisé à l’aide de Visual Studio ou Visual Web Developer (Assurez-vous que vous disposez de la dernière version de Visual Web Developer).

## <a name="overview-of-the-disabledbutton-extender"></a>Vue d’ensemble de l’extendeur DisabledButton

Notre nouvel extendeur de contrôle est nommé l’extendeur DisabledButton. Cet extendeur possède trois propriétés :

- TargetControlID - la zone de texte qui s’étend le contrôle.
- TargetButtonIID - le bouton qui est activé ou désactivé.
- DisabledText - le texte qui s’affiche dans le bouton. Lorsque vous commencez à taper, le bouton affiche la valeur de la propriété de texte du bouton.

Vous raccorder l’extendeur DisabledButton à un contrôle zone de texte et bouton. Avant de taper du texte, le bouton est désactivé et la zone de texte et bouton ressembler à ceci :


[![](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image2.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image1.png)

([Cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image3.png))


Une fois que vous commencez à taper le texte, le bouton est activé et la zone de texte et bouton ressembler à ceci :


[![](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image5.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image4.png)

([Cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image6.png))


Pour créer notre extendeur de contrôle, nous devons créer les trois fichiers suivants :

- DisabledButtonExtender.cs - ce fichier est la classe de contrôle côté serveur qui gère la création de votre extendeur et vous permettent de définir les propriétés au moment du design. Il définit également les propriétés qui peuvent être définies sur votre extendeur. Ces propriétés sont accessibles via le code et au moment du design et correspondent aux propriétés définies dans le fichier DisableButtonBehavior.js.
- DisabledButtonBehavior.js : Ce fichier est où vous allez ajouter l’ensemble de votre logique de script client.
- DisabledButtonDesigner.cs - cette classe permet à des fonctionnalités au moment du design. Vous avez besoin de cette classe si vous souhaitez que l’extendeur de contrôle pour fonctionner correctement avec Visual Studio/Visual Web Developer Designer.

Par conséquent, un extendeur de contrôle se compose d’un contrôle côté serveur, un comportement côté client et une classe de concepteur côté serveur. Vous apprenez à créer trois de ces fichiers dans les sections suivantes.

## <a name="creating-the-custom-extender-website-and-project"></a>Création du site Web d’extendeur personnalisé et le projet

La première étape consiste à créer un projet bibliothèque de classes et le site Web dans Visual Studio/Visual Web Developer. Nous ll créer l’extendeur personnalisé dans le projet de bibliothèque de classes et tester l’extendeur personnalisé dans le site Web.

Permettent de démarrer avec le site Web de s. Suivez ces étapes pour créer le site Web :

1. Sélectionnez l’option de menu **fichier, le nouveau Site Web**.
2. Sélectionnez le **Site Web ASP.NET** modèle.
3. Nommez le nouveau site Web *Website1*.
4. Cliquez sur le **OK** bouton.

Ensuite, nous avons besoin créer le projet de bibliothèque de classes qui contient le code de l’extendeur de contrôle :

1. Sélectionnez l’option de menu **fichier, ajouter un nouveau projet**.
2. Sélectionnez le **bibliothèque de classes** modèle.
3. Nom de la bibliothèque de classes avec le nom **CustomExtenders**.
4. Cliquez sur le **OK** bouton.

Après avoir effectué ces étapes, la fenêtre de l’Explorateur de solutions doit ressembler à la Figure 1.


[![Solution avec un projet de bibliothèque de site Web et de la classe](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image8.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image7.png)

**Figure 01**: Solution avec le projet de bibliothèque de site Web et de la classe ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image9.png))


Ensuite, vous devez ajouter toutes les références d’assembly nécessaires au projet de bibliothèque de classes :

1. Cliquez sur le projet CustomExtenders et sélectionnez l’option de menu **ajouter une référence**.
2. Sélectionnez l’onglet .NET.
3. Ajoutez des références aux assemblys suivants :

    1. System.Web.dll
    2. System.Web.Extensions.dll
    3. System.Design.dll
    4. System.Web.Extensions.Design.dll
4. Sélectionnez l’onglet Parcourir.
5. Ajoutez une référence à l’assembly AjaxControlToolkit.dll. Cet assembly se trouve dans le dossier où vous avez téléchargé les outils de contrôle AJAX.

Après avoir effectué ces étapes, votre dossier de références de projet de bibliothèque de classe doit ressembler à la Figure 2.


[![Dossier des références avec les références requises](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image11.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image10.png)

**Figure 02**: dossier références avec les références requises ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image12.png))


## <a name="creating-the-custom-control-extender"></a>Création de l’extendeur de contrôle personnalisé

Maintenant que nous avons notre bibliothèque de classes, nous pouvons commencer la création du contrôle d’extendeur. Permettent de démarrer avec l’élémentaire d’une classe de contrôle d’extendeur personnalisé (voir le Listing 1) s.

**La liste 1 - MyCustomExtender.cs**

[!code-csharp[Main](creating-a-custom-ajax-control-toolkit-control-extender-cs/samples/sample1.cs)]

Il existe plusieurs choses que vous remarquez sur la classe d’extendeur de contrôle dans la liste 1. Tout d’abord, notez que la classe hérite de la classe de base ExtenderControlBase. Tous les contrôles d’extendeur AJAX Control Toolkit dérivent de cette classe de base. Par exemple, la classe de base inclut la propriété TargetID qui est une propriété obligatoire de chaque extension de contrôle.

Ensuite, notez que la classe inclut les deux attributs suivants liés à un script client :

- Vue - génère un fichier à inclure comme une ressource incorporée dans un assembly.
- ClientScriptResource - provoque une ressource de script doit être récupéré à partir d’un assembly.

L’attribut de la ressource Web est utilisé pour incorporer le fichier MyControlBehavior.js JavaScript dans l’assembly lors de la compilation de l’extendeur personnalisé. L’attribut ClientScriptResource est utilisé pour récupérer le script MyControlBehavior.js à partir de l’assembly lorsque l’extendeur personnalisé est utilisé dans une page web.


Dans l’ordre pour les attributs de ressource Web et ClientScriptResource fonctionne, vous devez compiler le fichier JavaScript en tant que ressource incorporée. Sélectionnez le fichier dans la fenêtre de l’Explorateur de solutions, ouvrez la feuille de propriétés et affecter la valeur *ressource incorporée* à la **Action de génération** propriété.


Notez que l’extendeur de contrôle comprend également un attribut TargetControlType ne. Cet attribut est utilisé pour spécifier le type de contrôle qui est étendu par l’extendeur de contrôle. Dans le cas de liste 1, l’extendeur de contrôle est utilisé pour étendre une zone de texte.

Enfin, notez que l’extendeur personnalisé inclut une propriété nommée MyProperty. La propriété est marquée avec l’attribut ExtenderControlProperty. Les méthodes GetPropertyValue() et SetPropertyValue() sont utilisées pour passer la valeur de propriété à partir de l’extendeur de contrôle côté serveur, le comportement côté client.

Permettent de s continuez et implémentez le code pour notre extendeur DisabledButton. Le code pour cet extendeur sont accessibles dans le Listing 2.

**Listing 2 - DisabledButtonExtender.cs**

[!code-csharp[Main](creating-a-custom-ajax-control-toolkit-control-extender-cs/samples/sample2.cs)]

L’extendeur DisabledButton dans la liste 2 a deux propriétés nommées TargetButtonID et DisabledText. Le IDReferenceProperty appliquée à la propriété TargetButtonID vous empêche de celles de l’ID d’un contrôle bouton affecter à cette propriété.

Les attributs de ressource Web et ClientScriptResource associent un comportement côté client situé dans un fichier nommé DisabledButtonBehavior.js avec cette extension. Nous aborderons ce fichier JavaScript dans la section suivante.

## <a name="creating-the-custom-extender-behavior"></a>Création d’un comportement d’extendeur personnalisé

Le composant côté client d’un extendeur de contrôle est appelé un comportement. La logique réelle de désactivation et activation du bouton est contenue dans le comportement DisabledButton. Le code JavaScript pour le comportement est inclus dans la liste 3.

**La liste 3 - DisabledButton.js**

[!code-javascript[Main](creating-a-custom-ajax-control-toolkit-control-extender-cs/samples/sample3.js)]

Le fichier JavaScript dans le Listing 3 contient une classe de côté client nommée DisabledButtonBehavior. Cette classe, comme son double côté serveur, inclut deux propriétés nommées TargetButtonID et DisabledText auquel vous pouvez accéder à l’aide de get\_TargetButtonID/set\_TargetButtonID et\_DisabledText/set\_ DisabledText.

La méthode initialize() associe un gestionnaire d’événements de touche relâchée à l’élément cible pour le comportement. Chaque fois que vous tapez une lettre dans la zone de texte associé à ce problème, le gestionnaire keyup s’exécute. Le Gestionnaire de touche relâchée Active ou désactive le bouton selon que la zone de texte associée au comportement contient un texte.

N’oubliez pas que vous devez compiler le fichier JavaScript dans la liste de 3 comme une ressource incorporée. Sélectionnez le fichier dans la fenêtre de l’Explorateur de solutions, ouvrez la feuille de propriétés et affecter la valeur *ressource incorporée* à la **Action de génération** propriété (voir Figure 3). Cette option est disponible dans Visual Studio et Visual Web Developer.


[![Ajout d’un fichier JavaScript en tant que ressource incorporée](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image14.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image13.png)

**Figure 03**: ajout d’un fichier JavaScript en tant que ressource incorporée ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image15.png))


## <a name="creating-the-custom-extender-designer"></a>Création du Concepteur d’extendeur personnalisé

Il existe une classe dernière que nous avons besoin de créer pour compléter notre extendeur. Nous devons créer la classe de concepteur dans la liste 4. Cette classe est requise pour rendre l’extendeur de fonctionner correctement avec Visual Studio/Visual Web Developer Designer.

**La liste 4 - DisabledButtonDesigner.cs**

[!code-csharp[Main](creating-a-custom-ajax-control-toolkit-control-extender-cs/samples/sample4.cs)]

Le concepteur dans la liste 4 est associé à l’extendeur DisabledButton avec l’attribut de concepteur. Vous devez appliquer l’attribut de concepteur pour la classe DisabledButtonExtender comme suit :

[!code-csharp[Main](creating-a-custom-ajax-control-toolkit-control-extender-cs/samples/sample5.cs)]

## <a name="using-the-custom-extender"></a>À l’aide de l’extendeur personnalisé

Maintenant que nous avons terminé la création de l’extendeur de contrôle DisabledButton, il est temps de l’utiliser dans notre site Web ASP.NET. Tout d’abord, nous devons ajouter l’extension personnalisée à la boîte à outils. Procédez comme suit :

1. En double-cliquant sur la page dans la fenêtre de l’Explorateur de solutions, ouvrez une page ASP.NET.
2. Avec le bouton droit de la boîte à outils et sélectionnez l’option de menu **choisir des éléments de**.
3. Dans la boîte de dialogue Choisir des éléments de boîte à outils, accédez à l’assembly CustomExtenders.dll.
4. Cliquez sur le **OK** pour fermer la boîte de dialogue.

Après avoir effectué ces étapes, l’extendeur de contrôle DisabledButton doit apparaître dans la boîte à outils (voir Figure 4).


[![DisabledButton dans la boîte à outils](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image17.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image16.png)

**Figure 04**: DisabledButton dans la boîte à outils ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image18.png))


Ensuite, nous devons créer une page ASP.NET. Procédez comme suit :

1. Créer une nouvelle page ASP.NET nommée ShowDisabledButton.aspx.
2. Faites glisser un ScriptManager sur la page.
3. Faites glisser un contrôle de zone de texte sur la page.
4. Faites glisser un contrôle bouton sur la page.
5. Dans la fenêtre Propriétés, modifiez la propriété ID de bouton à la valeur *btnSave* et la propriété Text la valeur *enregistrer\**.
  

Nous avons créé une page avec un contrôle ASP.NET de zone de texte et bouton standard.

Ensuite, nous avons besoin étendre le contrôle de zone de texte avec l’extendeur DisabledButton :

1. Sélectionnez le **ajouter un extendeur** option pour ouvrir la boîte de dialogue Assistant extendeur de tâches (voir Figure 5). Notez que la boîte de dialogue inclut notre extendeur DisabledButton personnalisé.
2. Sélectionnez l’extendeur DisabledButton et cliquez sur le **OK** bouton.


[![La boîte de dialogue Assistant extendeur](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image20.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image19.png)

**Figure 05**: boîte de dialogue de l’Assistant extendeur ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image21.png))


Enfin, nous pouvons définir les propriétés de l’extendeur DisabledButton. Vous pouvez modifier les propriétés de l’extendeur DisabledButton en modifiant les propriétés du contrôle de zone de texte :

1. Sélectionnez la zone de texte dans le concepteur.
2. Dans la fenêtre Propriétés, développez le nœud d’extendeurs (voir Figure 6).
3. Affectez la valeur *enregistrer* à la propriété DisabledText et la valeur *btnSave* à la propriété TargetButtonID.


[![Définition des propriétés d’extendeur](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image23.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image22.png)

**Figure 06**: définition des propriétés d’extendeur ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image24.png))


Lorsque vous exécutez la page (en appuyant sur F5), le contrôle bouton est initialement désactivé. Dès que vous commencez la saisie de texte dans la zone de texte, le bouton de contrôle est activé (voir Figure 7).


[![L’extendeur DisabledButton en action](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image26.png)](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image25.png)

**Figure 07**: le DisabledButton l’extendeur en action ([cliquez pour afficher l’image en taille réelle](creating-a-custom-ajax-control-toolkit-control-extender-cs/_static/image27.png))


## <a name="summary"></a>Résumé

L’objectif de ce didacticiel est d’expliquer comment vous pouvez étendre les outils de contrôle AJAX avec les contrôles d’extendeur personnalisé. Dans ce didacticiel, nous avons créé un extendeur de contrôle DisabledButton simple. Nous avons implémenté cette extension en créant une classe DisabledButtonExtender, un comportement DisabledButtonBehavior JavaScript et une classe DisabledButtonDesigner. Vous suivez un ensemble similaire d’étapes chaque fois que vous créez un extendeur de contrôle personnalisé.

>[!div class="step-by-step"]
[Précédent](using-ajax-control-toolkit-controls-and-control-extenders-cs.md)
[Suivant](get-started-with-the-ajax-control-toolkit-vb.md)
