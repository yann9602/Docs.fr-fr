---
uid: web-forms/overview/ajax-control-toolkit/combobox/how-do-i-use-the-combobox-control-vb
title: "Utilisation du contrôle ComboBox (VB) | Documents Microsoft"
author: microsoft
description: "Zone de liste déroulante est un contrôle ASP.NET AJAX qui combine la souplesse d’une zone de texte avec une liste d’options à partir de laquelle les utilisateurs peuvent choisir."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: e887e7b2-a6e7-4a28-a134-ba334494badb
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/combobox/how-do-i-use-the-combobox-control-vb
msc.type: authoredcontent
ms.openlocfilehash: 54e36cf275dcc4b85253dc3b8bb5b0dbb027af77
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="how-do-i-use-the-combobox-control-vb"></a>Utilisation du contrôle ComboBox (VB)
====================
par [Microsoft](https://github.com/microsoft)

> Zone de liste déroulante est un contrôle ASP.NET AJAX qui combine la souplesse d’une zone de texte avec une liste d’options à partir de laquelle les utilisateurs peuvent choisir.


L’objectif de ce didacticiel est d’expliquer le contrôle ComboBox de boîte à outils de contrôle AJAX. Le contrôle ComboBox fonctionne comme une combinaison entre un contrôle ASP.NET DropDownList standard et un contrôle de zone de texte. Vous pouvez sélectionner dans une liste déjà existante d’éléments ou entrez un nouvel élément.

La zone de liste déroulante est similaire à l’extendeur de contrôle de la saisie semi-automatique, mais les contrôles sont utilisés dans des scénarios différents. L’extendeur AutoComplete interroge un service web pour obtenir des entrées correspondantes. Le contrôle de zone de liste déroulante, en revanche, est initialisé avec un ensemble d’éléments. Le fait d’extendeur la saisie semi-automatique sens lorsque vous travaillez avec un grand ensemble de données (des millions de pièces de voiture) lors de l’utilisation du contrôle ComboBox vaut utiliser lorsque vous travaillez avec un petit ensemble de données (des dizaines de pièces de voiture).

## <a name="selecting-from-a-static-list-of-items"></a>Sélection d’une liste statique d’éléments

Permettent de commencer par un exemple simple d’utilisation du contrôle ComboBox s. Imaginez que vous souhaitez afficher une liste statique d’éléments dans une liste déroulante. Toutefois, vous souhaitez laisser ouverte la possibilité que la liste n’est pas terminée. Vous souhaitez autoriser un utilisateur à entrer une valeur personnalisée dans la liste.

Nous ll créer une page Web Forms ASP.NET et utilisez le contrôle de zone de liste déroulante de la page. Ajouter la nouvelle page ASP.NET à votre projet et passez en mode Design.

Si vous souhaitez utiliser le contrôle de zone de liste déroulante dans la page vous devez ajouter un contrôle ScriptManager à la page. Faites glisser le contrôle ScriptManager sous l’onglet Extensions AJAX vers l’aire du concepteur. Vous devez ajouter le contrôle ScriptManager en haut de la page ; Vous pouvez l’ajouter immédiatement au-dessous de l’ouverture côté serveur &lt;formulaire&gt; balise.

Ensuite, faites glisser le contrôle de zone de liste déroulante sur la page. Vous trouverez le contrôle de zone de liste déroulante dans la boîte à outils avec les autres contrôles de boîte à outils de contrôle AJAX et extensions de contrôle (voir figure 1).


[![Formulaire simple pour la création d’une carte de visite](how-do-i-use-the-combobox-control-vb/_static/image1.jpg)](how-do-i-use-the-combobox-control-vb/_static/image1.png)

**Figure 01**: en sélectionnant le contrôle ComboBox à partir de la boîte à outils ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image2.png))


Nous ll utiliser le contrôle de zone de liste déroulante pour afficher une liste statique de choix. L’utilisateur peut sélectionner un niveau particulier de spiciness pour leurs produits alimentaires à partir d’une liste de trois choix : doux, support et à chaud (voir Figure 2).


[![Sélection d’une liste statique d’éléments](how-do-i-use-the-combobox-control-vb/_static/image2.jpg)](how-do-i-use-the-combobox-control-vb/_static/image3.png)

**Figure 02**: sélection d’une liste statique d’éléments ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image4.png))


Il existe deux façons que vous pouvez ajouter ces choix sur le contrôle ComboBox. Tout d’abord, vous sélectionnez l’option de la tâche Modifier les Options lorsque vous pointez votre souris sur le contrôle en mode Création et ouvrez l’éditeur d’élément (voir Figure 3).


[![Modification des éléments de la zone de liste déroulante](how-do-i-use-the-combobox-control-vb/_static/image3.jpg)](how-do-i-use-the-combobox-control-vb/_static/image5.png)

**Figure 03**: éléments de l’édition de ComboBox ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image6.png))


La deuxième option consiste à ajouter la liste d’éléments entre l’ouverture et la fermeture de &lt;asp : ComboBox&gt; balises en mode Source. La page de liste 1 contient le ComboBox mis à jour qui contient la liste d’éléments.

**La liste 1 - Static.aspx**

[!code-aspx[Main](how-do-i-use-the-combobox-control-vb/samples/sample1.aspx)]

Lorsque vous ouvrez la page dans la liste 1, vous pouvez sélectionner une des options existantes dans la liste déroulante. En d’autres termes, le contrôle ComboBox fonctionne comme un contrôle DropDownList.

Toutefois, vous avez également la possibilité d’entrer un nouveau choix (par exemple, Super épicée) qui n’est pas dans la liste existante. Par conséquent, le contrôle ComboBox fonctionne également comme un contrôle de zone de texte.

Indépendamment de si vous choisissez un préexistant élément ou que vous entrez un élément personnalisé, lorsque vous envoyez le formulaire, votre choix s’affiche dans le contrôle label. Lorsque vous envoyez le formulaire, le btnSubmit\_cliquez sur Gestionnaire s’exécute et met à jour l’étiquette (voir Figure 4).


[![Affichage de l’élément sélectionné](how-do-i-use-the-combobox-control-vb/_static/image4.jpg)](how-do-i-use-the-combobox-control-vb/_static/image7.png)

**Figure 04**: affichage de l’élément sélectionné ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image8.png))


La zone de liste déroulante prend en charge les mêmes propriétés que le contrôle DropDownList pour la récupération de l’élément sélectionné après l’envoi du formulaire :

- SelectedItem.Text - affiche la valeur de la propriété de texte de l’élément sélectionné.
- SelectedItem.Value - affiche la valeur de la propriété Value de l’élément sélectionné ou le texte tapé dans la zone de liste déroulante.
- SelectedValue - identique à l’option SelectedItem.Value sauf que cette propriété vous permet de spécifier l’élément sélectionné de valeur par défaut (initial).

Si vous tapez un choix personnalisé dans le contrôle ComboBox puis le choix personnalisé est affecté aux propriétés SelectedItem.Text et de SelectedItem.Value.

## <a name="selecting-the-list-of-items-from-the-database"></a>Sélection de la liste des éléments à partir de la base de données

Vous pouvez récupérer la liste des éléments de la zone de liste déroulante affiche à partir d’une base de données. Par exemple, vous pouvez lier le contrôle ComboBox pour un contrôle SqlDataSource, un contrôle ObjectDataSource, un LinqDataSource ou un contrôle EntityDataSource.

Imaginez que vous souhaitez afficher une liste de films dans une zone de liste déroulante. Vous souhaitez récupérer la liste de films à partir de la table de base de données de films. Procédez comme suit :

1. Créer une page nommée Movies.aspx
2. Ajouter un contrôle ScriptManager à la page en faisant glisser sous l’onglet Extensions AJAX ScriptManager dans la boîte à outils vers la page.
3. Ajouter un contrôle ComboBox à la page en faisant glisser la zone de liste déroulante sur la page.
4. En mode conception, pointez votre souris sur le contrôle de zone de liste déroulante et sélectionnez le **choisir la Source de données** option de tâches (voir Figure 5). L’Assistant Configuration de Source de données est lancé.
5. Dans le **choisir une Source de données** étape, sélectionnez le &lt;nouvelle source de données&gt; option.
6. Dans le **choisir un Type de Source de données** étape, sélectionnez la base de données.
7. Dans le **choisir votre connexion de données** étape, sélectionnez votre base de données (par exemple, MoviesDB.mdf).
8. Dans le **enregistrer la chaîne de connexion dans le fichier de Configuration d’Application** pas à pas, sélectionnez l’option pour enregistrer votre chaîne de connexion.
9. Dans le **configurer l’instruction Select** étape, sélectionnez la table de base de données de films et sélectionnez toutes les colonnes.
10. Dans le **tester la requête** étape, cliquez sur le bouton Terminer.
11. Dans le **choisir la Source de données** étape, sélectionnez la colonne de titre pour le champ à afficher et de la colonne d’Id pour les données de champ (voir Figure).
12. Cliquez sur le bouton OK pour fermer l’Assistant.


[![Choix d’une source de données](how-do-i-use-the-combobox-control-vb/_static/image5.jpg)](how-do-i-use-the-combobox-control-vb/_static/image9.png)

**Figure 05**: choix d’une source de données ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image10.png))


[![Choisir les champs de texte et la valeur des données](how-do-i-use-the-combobox-control-vb/_static/image6.jpg)](how-do-i-use-the-combobox-control-vb/_static/image11.png)

**Figure 06**: choisir les champs de texte et la valeur des données ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image12.png))


Après avoir effectué les étapes ci-dessus, la zone de liste déroulante est lié à un contrôle SqlDataSource qui représente les films à partir de la table de base de données de films. La source de la page ressemble à liste 2 (j’ai nettoyé un peu la mise en forme).

**Listing 2 - Movies.aspx**

[!code-aspx[Main](how-do-i-use-the-combobox-control-vb/samples/sample2.aspx)]

Notez que le contrôle ComboBox a une propriété DataSourceID qui pointe vers le contrôle SqlDataSource. Lorsque vous ouvrez la page dans un navigateur, la liste de films à partir de la base de données s’affiche (voir la Figure 7). Vous pouvez soit un choix un film à partir de la liste ou entrez un nouveau film en tapant le film dans le contrôle ComboBox.


[![Affichage d’une liste de films](how-do-i-use-the-combobox-control-vb/_static/image7.jpg)](how-do-i-use-the-combobox-control-vb/_static/image13.png)

**Figure 07**: affichage d’une liste de films ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image14.png))


## <a name="setting-the-dropdownstyle"></a>Définir le DropDownStyle

Vous pouvez utiliser la propriété DropDownStyle de ComboBox pour modifier le comportement du contrôle ComboBox. Cette propriété accepte il les valeurs possibles :

- Liste déroulante - affiche de la zone de liste déroulante (valeur par défaut) une liste déroulante liste lorsque vous cliquez sur la flèche et que vous pouvez entrer une valeur personnalisée.
- Simple - la zone de liste déroulante s’affiche automatiquement une liste déroulante, et vous pouvez entrer une valeur personnalisée.
- DropDownList - le ComboBox fonctionne comme un contrôle DropDownList.

La différence entre la liste déroulante et Simple est lorsque la liste des éléments est affichée. Dans le cas Simple, la liste s’affiche immédiatement lorsque vous déplacez le focus vers la zone de liste déroulante. Dans le cas de liste déroulante, vous devez cliquer sur la flèche pour afficher la liste des éléments.

La valeur de DropDownList, le contrôle de zone de liste déroulante de fonctionner comme un contrôle DropDownList standard. Toutefois, il est ici une différence importante près. Les versions antérieures d’Internet Explorer affichent un contrôle de DropDownList avec un z-index infini, le contrôle s’affiche devant n’importe quel contrôle placé placés devant lui. Étant donné que le contrôle ComboBox effectue le rendu HTML &lt;div&gt; balise au lieu d’un élément HTML &lt;sélectionnez&gt; balise, le contrôle ComboBox correctement respecte ordre de plan.

## <a name="setting-the-autocompletemode"></a>Définir le AutoCompleteMode

La propriété ComboBox AutoCompleteMode vous permet de spécifier que se passe-t-il quand un utilisateur tape un texte dans le contrôle ComboBox. Cette propriété accepte les valeurs possibles suivantes :

- Aucun - (valeur par défaut) le ComboBox ne fournit pas de comportement de saisie semi-automatique.
- Suggérer - la zone de liste déroulante affiche la liste, et il met en surbrillance l’élément correspondant dans la liste (voir la Figure 8).
- Ajouter - la zone de liste déroulante n’affiche pas la liste, et il ajoute l’élément correspondant dans la liste sur ce que vous avez tapé (voir la Figure 9).
- SuggestAppend - la zone de liste déroulante affiche la liste et ajoute l’élément correspondant dans la liste sur ce que vous avez tapé (voir la Figure 10).


[![La zone de liste déroulante affiche une suggestion](how-do-i-use-the-combobox-control-vb/_static/image8.jpg)](how-do-i-use-the-combobox-control-vb/_static/image15.png)

**Figure 08**: la zone de liste déroulante affiche une suggestion ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image16.png))


[![ComboBox ajoute du texte correspondant](how-do-i-use-the-combobox-control-vb/_static/image9.jpg)](how-do-i-use-the-combobox-control-vb/_static/image17.png)

**Figure 09**: ComboBox ajoute du texte correspondant ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image18.png))


[![Le contrôle ComboBox suggère et ajoute](how-do-i-use-the-combobox-control-vb/_static/image10.jpg)](how-do-i-use-the-combobox-control-vb/_static/image19.png)

**La figure 10**: ComboBox le suggère et ajoute ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-combobox-control-vb/_static/image20.png))


## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à utiliser le contrôle de zone de liste déroulante pour afficher un ensemble fixe d’éléments. Nous lié le contrôle de zone de liste déroulante à la fois un ensemble d’éléments de statique et à une table de base de données. Enfin, vous avez appris comment modifier le comportement du contrôle ComboBox en définissant ses propriétés DropDownStyle et que AutoCompleteMode.

>[!div class="step-by-step"]
[Précédent](how-do-i-use-the-combobox-control-cs.md)
