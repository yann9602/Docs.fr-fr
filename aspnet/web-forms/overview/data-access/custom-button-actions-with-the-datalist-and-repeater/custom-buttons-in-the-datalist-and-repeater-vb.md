---
uid: web-forms/overview/data-access/custom-button-actions-with-the-datalist-and-repeater/custom-buttons-in-the-datalist-and-repeater-vb
title: "Boutons personnalisés dans le contrôle DataList et répéteur (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons construire une interface qui utilise un répéteur pour répertorier les catégories dans le système, chaque catégorie de fournir un bouton pour afficher ses associ..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/13/2006
ms.topic: article
ms.assetid: 1afdb14d-6e49-4e1f-aead-2934730d472e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-button-actions-with-the-datalist-and-repeater/custom-buttons-in-the-datalist-and-repeater-vb
msc.type: authoredcontent
ms.openlocfilehash: fc6c297f08790cdcc74867df21e32258017c5a7d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="custom-buttons-in-the-datalist-and-repeater-vb"></a>Boutons personnalisés dans le contrôle DataList et répéteur (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_46_VB.exe) ou [télécharger le PDF](custom-buttons-in-the-datalist-and-repeater-vb/_static/datatutorial46vb1.pdf)

> Dans ce didacticiel, nous allons construire une interface qui utilise un répéteur pour répertorier les catégories dans le système, chaque catégorie de fournir un bouton pour afficher ses produits associés à l’aide d’un contrôle BulletedList.


## <a name="introduction"></a>Introduction

Dans les didacticiels dix-sept DataList et répéteur précédentes, nous ve créé à la fois des exemples en lecture seule et la modification et suppression des exemples. Pour faciliter la modification et suppression des fonctionnalités au sein de DataList, nous avons ajouté des boutons au contrôle DataList s `ItemTemplate` qui, lorsque vous cliquez dessus, a provoqué une publication (postback) et a déclenché un événement DataList correspondant au bouton s `CommandName` propriété. Par exemple, ajoutez un bouton à la `ItemTemplate` avec un `CommandName` provoque la valeur de la propriété de modification du contrôle DataList s `EditCommand` à se déclencher sur la publication (postback) ; l’autre avec la `CommandName` supprimer déclenche le `DeleteCommand`.

En outre pour modifier et supprimer des boutons, DataList et répéteur peut également inclure des boutons, LinkButton ou ImageButtons qui, lorsque vous cliquez dessus, effectuer une logique côté serveur personnalisée. Dans ce didacticiel, nous allons construire une interface qui utilise un répéteur pour répertorier les catégories dans le système. Pour chaque catégorie, répéteur inclut un bouton pour afficher la catégorie de produits s associé à l’aide d’un contrôle BulletedList (voir Figure 1).


[![En cliquant sur le lien se produits afficher affiche les produits de s catégorie dans une liste à puces](custom-buttons-in-the-datalist-and-repeater-vb/_static/image2.png)](custom-buttons-in-the-datalist-and-repeater-vb/_static/image1.png)

**Figure 1**: en cliquant sur le lien se produits afficher affiche la catégorie s produits dans une liste à puces ([cliquez pour afficher l’image en taille réelle](custom-buttons-in-the-datalist-and-repeater-vb/_static/image3.png))


## <a name="step-1-adding-the-custom-button-tutorial-web-pages"></a>Étape 1 : Ajout de Pages Web didacticiel bouton personnalisé

Avant d’examiner comment ajouter un bouton personnalisé, permettent de s Prenez tout d’abord créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel. Commencez par ajouter un nouveau dossier nommé `CustomButtonsDataListRepeater`. Ensuite, ajoutez les deux pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `CustomButtons.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels relatifs aux boutons personnalisés](custom-buttons-in-the-datalist-and-repeater-vb/_static/image4.png)

**Figure 2**: ajouter les Pages ASP.NET pour les didacticiels relatifs aux boutons personnalisés


Comme dans les autres dossiers, `Default.aspx` dans le `CustomButtonsDataListRepeater` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Ajoutez ce contrôle utilisateur à `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](custom-buttons-in-the-datalist-and-repeater-vb/_static/image6.png)](custom-buttons-in-the-datalist-and-repeater-vb/_static/image5.png)

**Figure 3**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](custom-buttons-in-the-datalist-and-repeater-vb/_static/image7.png))


Enfin, ajoutez les pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après la pagination et le tri avec le contrôle DataList et répéteur `<siteMapNode>`:


[!code-xml[Main](custom-buttons-in-the-datalist-and-repeater-vb/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour la modification, l’insertion et la suppression des didacticiels.


![Le plan de Site comprend maintenant une entrée pour le didacticiel de boutons personnalisés](custom-buttons-in-the-datalist-and-repeater-vb/_static/image8.png)

**Figure 4**: le plan de Site comprend maintenant une entrée pour le didacticiel de boutons personnalisés


## <a name="step-2-adding-the-list-of-categories"></a>Étape 2 : Ajout de la liste des catégories

Pour ce didacticiel, nous devons créer un répéteur qui répertorie toutes les catégories, ainsi que d’un LinkButton produits afficher qui, lorsque vous cliquez dessus, affiche les produits de la catégorie associée s dans une liste à puces. Permettent de s tout d’abord créer un répéteur simple qui répertorie les catégories dans le système. Commencez par ouvrir le `CustomButtons.aspx` page dans le `CustomButtonsDataListRepeater` dossier. Faites glisser un répéteur à partir de la boîte à outils sur le concepteur et l’ensemble de ses `ID` propriété `Categories`. Ensuite, créez un nouveau contrôle de source de données à partir de la balise s de répéteur. En particulier, créez un nouveau contrôle ObjectDataSource nommé `CategoriesDataSource` qui sélectionne ses données à partir de la `CategoriesBLL` classe s `GetCategories()` (méthode).


[![Configurer pour utiliser la méthode de GetCategories() CategoriesBLL classe s ObjectDataSource](custom-buttons-in-the-datalist-and-repeater-vb/_static/image10.png)](custom-buttons-in-the-datalist-and-repeater-vb/_static/image9.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` classe s `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](custom-buttons-in-the-datalist-and-repeater-vb/_static/image11.png))


Contrairement au contrôle DataList, pour laquelle Visual Studio crée une valeur par défaut `ItemTemplate` en fonction de la source de données, les modèles de répéteur s doivent être définies manuellement. En outre, les modèles de répéteur s doivent être créés et modifiés de façon déclarative (autrement dit, s’il n’y a pas modifier les modèles d’option dans la balise active de répéteur s).

Cliquez sur l’onglet Source dans l’angle inférieur gauche et ajoutez une `ItemTemplate` qui affiche le nom de catégorie s dans une `<h3>` élément et sa description dans un paragraphe de balise ; incluent un `SeparatorTemplate` qui affiche une barre horizontale (`<hr />`) entre chaque catégorie. Également ajouter un bouton de lien avec son `Text` produits d’afficher la valeur de propriété. Après avoir effectué ces étapes, votre balisage déclaratif s de page doit ressembler à ce qui suit :


[!code-aspx[Main](custom-buttons-in-the-datalist-and-repeater-vb/samples/sample2.aspx)]

La figure 6 illustre la page via un navigateur. Chaque nom de catégorie et la description sont répertorié. Le bouton Afficher les produits, lorsque vous cliquez dessus, entraîne une publication, mais n’effectue pas encore aucune action.


[![Chaque nom de catégorie et la Description s’affiche, ainsi que d’un LinkButton produits afficher](custom-buttons-in-the-datalist-and-repeater-vb/_static/image13.png)](custom-buttons-in-the-datalist-and-repeater-vb/_static/image12.png)

**Figure 6**: catégorie s nom et la Description s’affiche, ainsi que d’un LinkButton produits afficher ([cliquez pour afficher l’image en taille réelle](custom-buttons-in-the-datalist-and-repeater-vb/_static/image14.png))


## <a name="step-3-executing-server-side-logic-when-the-show-products-linkbutton-is-clicked"></a>Étape 3 : Exécution côté serveur logique lors de l’afficher produits LinkButton est activé.

Chaque fois qu’un clic sur un bouton, LinkButton ou ImageButton dans un modèle dans un contrôle DataList ou un répéteur, une publication (postback) se produit et le s DataList ou répéteur `ItemCommand` se déclenche des événements. Outre la `ItemCommand` événement, du contrôle DataList contrôle peut-être également déclencher des événements plus spécifique, une autre si le bouton s `CommandName` est définie sur une des chaînes réservés (supprimer, modifier, Annuler, mise à jour ou sélectionnez), mais la `ItemCommand` événement est *toujours* déclenché.

Lorsqu’un clic est effectué dans un contrôle DataList ou un répéteur, il faut souvent passer le long d’un utilisateur a cliqué sur le bouton (dans le cas qu’il peut y avoir plusieurs boutons dans le contrôle, tels que les deux une modification et bouton Supprimer) et éventuellement des informations supplémentaires (telles que la valeur de clé primaire de l’élément dont bouton). Le bouton, LinkButton et ImageButton fournissent deux propriétés dont les valeurs sont passées à la `ItemCommand` Gestionnaire d’événements :

- `CommandName`une chaîne, généralement utilisée pour identifier chaque bouton dans le modèle
- `CommandArgument`généralement utilisée pour contenir la valeur d’un champ de données, telles que la valeur de clé primaire

Pour cet exemple, définissez le LinkButton s `CommandName` propriété ShowProducts et lier la valeur de clé primaire enregistrement s actuel `CategoryID` à la `CommandArgument` propriété à l’aide de la syntaxe de liaison de données `CategoryArgument='<%# Eval("CategoryID") %>'`. Après la spécification de ces deux propriétés, la syntaxe déclarative de s LinkButton doit se présenter comme suit :


[!code-aspx[Main](custom-buttons-in-the-datalist-and-repeater-vb/samples/sample3.aspx)]

Lorsque le bouton est activé, une publication (postback) se produit et le s DataList ou répéteur `ItemCommand` se déclenche des événements. Le Gestionnaire d’événements est passé le bouton s `CommandName` et `CommandArgument` valeurs.

Créer un gestionnaire d’événements pour le répéteur s `ItemCommand` événement et notez le second paramètre passé dans le Gestionnaire d’événements (nommé `e`). Cet deuxième paramètre est de type [ `RepeaterCommandEventArgs` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.repeatercommandeventargs.aspx) et contient les quatre propriétés suivantes :

- `CommandArgument`la valeur du bouton concerné s `CommandArgument` propriété
- `CommandName`la valeur du bouton s `CommandName` propriété
- `CommandSource`une référence au contrôle de bouton l’utilisateur a cliqué
- `Item`une référence à la [ `RepeaterItem` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.repeateritem.aspx) qui contient le bouton l’utilisateur a cliqué ; chaque enregistrement lié à la répétition est reconnu comme un`RepeaterItem`

Depuis la catégorie sélectionnée s `CategoryID` est transmise le `CommandArgument` propriété, nous pouvons obtenir l’ensemble des produits associés à la catégorie sélectionnée dans le `ItemCommand` Gestionnaire d’événements. Ces produits peuvent alors être liés à un contrôle BulletedList dans le `ItemTemplate` (qui nous ve encore à ajouter). Tout ce qui reste, puis ajoutez BulletedList, référencez-la dans le `ItemCommand` Gestionnaire d’événements et le lier l’ensemble de produits pour la catégorie sélectionnée, ce qui nous allons neutraliser à l’étape 4.

> [!NOTE]
> DataList s `ItemCommand` un objet de type est passé au gestionnaire d’événements [ `DataListCommandEventArgs` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalistcommandeventargs.aspx), qui offre les quatre propriétés en tant que mêmes le `RepeaterCommandEventArgs` classe.


## <a name="step-4-displaying-the-selected-category-s-products-in-a-bulleted-list"></a>Étape 4 : Affichage des produits s catégorie sélectionnée dans une liste à puces

Les produits de la catégorie sélectionnée s peuvent être affichées dans le répéteur s `ItemTemplate` à l’aide de n’importe quel nombre de contrôles. Nous pourrions ajouter qu'un autre imbriqués répéteur, un contrôle DataList, une liste déroulante, un GridView et ainsi de suite. Cependant, étant donné que nous souhaitons afficher les produits sous forme de liste à puces, nous allons utiliser le contrôle BulletedList. Retour à la `CustomButtons.aspx` balisage déclaratif de s, ajoutez un contrôle BulletedList à la `ItemTemplate` après le LinkButton produits afficher. Définir le s BulletedLists `ID` à `ProductsInCategory`. BulletedList affiche la valeur du champ de données spécifié via la `DataTextField` propriété ; étant donné que ce contrôle aura des informations sur les produits qui lui est associée, définissez la `DataTextField` propriété `ProductName`.


[!code-aspx[Main](custom-buttons-in-the-datalist-and-repeater-vb/samples/sample4.aspx)]

Dans le `ItemCommand` Gestionnaire d’événements, faire référence à ce contrôle à l’aide de `e.Item.FindControl("ProductsInCategory")` et la lier à l’ensemble des produits associés à la catégorie sélectionnée.


[!code-vb[Main](custom-buttons-in-the-datalist-and-repeater-vb/samples/sample5.vb)]

Avant d’effectuer une action dans le `ItemCommand` Gestionnaire d’événements, il s prudent de vérifier la valeur d’entrant `CommandName`. Étant donné que la `ItemCommand` Gestionnaire d’événements déclenche lorsque *tout* bouton est activé, s’il existe plusieurs boutons dans l’utilisation du modèle le `CommandName` valeur pour déterminer l’action à entreprendre. Vérification de la `CommandName` ici est résolu, que nous disposons d’un seul bouton, mais il s’agit d’une bonne habitude à l’écran. Ensuite, le `CategoryID` de la catégorie sélectionnée est récupérée à partir de la `CommandArgument` propriété. Le contrôle BulletedList dans le modèle est ensuite référencé et lié aux résultats de la `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode).

Dans les didacticiels précédents utiliser les boutons dans un contrôle DataList, par exemple [une vue d’ensemble de modification et suppression des données dans le contrôle DataList](../editing-and-deleting-data-through-the-datalist/an-overview-of-editing-and-deleting-data-in-the-datalist-vb.md), nous avons déterminé la valeur de clé primaire d’un élément donné via le `DataKeys` collection. Cette approche fonctionne bien avec le contrôle DataList, répéteur ne dispose pas un `DataKeys` propriété. Au lieu de cela, nous devons utiliser une autre approche pour fournir la valeur de clé primaire, telles que via le bouton s `CommandArgument` propriété ou en assignant la valeur de clé primaire pour un contrôle Web Label masqué dans le modèle et en lire sa valeur dans la `ItemCommand`Gestionnaire d’événements à l’aide `e.Item.FindControl("LabelID")`.

Après avoir effectué la `ItemCommand` Gestionnaire d’événements, prenez un moment pour tester cette page dans un navigateur. Comme le montre la Figure 7, en cliquant sur les produits afficher lien provoque une publication (postback) et affiche les produits de la catégorie sélectionnée dans une BulletedList. En outre, notez que ces informations produit restent, même si d’autres catégories de liens de produits d’afficher un bouton.

> [!NOTE]
> Si vous souhaitez modifier le comportement de ce rapport, telles que les produits qu’une seule catégorie s sont répertoriés à la fois, définissez simplement le contrôle BulletedList s `EnableViewState` propriété `False`.


[![Un BulletedList est utilisé pour afficher les produits de la catégorie sélectionnée](custom-buttons-in-the-datalist-and-repeater-vb/_static/image16.png)](custom-buttons-in-the-datalist-and-repeater-vb/_static/image15.png)

**Figure 7**: A BulletedList est utilisé pour afficher les produits de la catégorie sélectionnée ([cliquez pour afficher l’image en taille réelle](custom-buttons-in-the-datalist-and-repeater-vb/_static/image17.png))


## <a name="summary"></a>Récapitulatif

Les contrôles DataList et répéteur peuvent inclure n’importe quel nombre de boutons, LinkButton ou ImageButtons dans leurs modèles. Ces boutons, lorsque vous cliquez dessus, provoquent une publication et de déclencher la `ItemCommand` événement. Pour associer l’action personnalisée de côté serveur avec un bouton, créez un gestionnaire d’événements pour le `ItemCommand` événement. Dans ce gestionnaire d’événements, vérifiez tout d’abord entrant `CommandName` valeur pour déterminer quel bouton a été utilisé. Des informations supplémentaires peuvent éventuellement être fournies par le bouton s `CommandArgument` propriété.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Dennis Patterson. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](custom-buttons-in-the-datalist-and-repeater-cs.md)
