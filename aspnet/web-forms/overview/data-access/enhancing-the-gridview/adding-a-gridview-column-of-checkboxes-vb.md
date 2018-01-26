---
uid: web-forms/overview/data-access/enhancing-the-gridview/adding-a-gridview-column-of-checkboxes-vb
title: "Ajout d’une colonne de GridView des cases à cocher (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel explique comment ajouter une colonne de cases à cocher à un contrôle GridView pour fournir un moyen intuitif de la sélection de plusieurs lignes de G. l’utilisateur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/06/2007
ms.topic: article
ms.assetid: 39253d05-75c0-41c7-b9d4-a6c58ecf69ce
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/enhancing-the-gridview/adding-a-gridview-column-of-checkboxes-vb
msc.type: authoredcontent
ms.openlocfilehash: 4468f7e0c142fa432e58d4c686dd79d3b38612ad
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-a-gridview-column-of-checkboxes-vb"></a>Ajout d’une colonne de GridView des cases à cocher (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_52_VB.exe) ou [télécharger le PDF](adding-a-gridview-column-of-checkboxes-vb/_static/datatutorial52vb1.pdf)

> Ce didacticiel explique comment ajouter une colonne de cases à cocher à un contrôle GridView à fournir un moyen intuitif de sélectionner plusieurs lignes du contrôle GridView à l’utilisateur.


## <a name="introduction"></a>Introduction

Dans le didacticiel précédent, nous avons examiné comment ajouter une colonne de cases d’option dans le contrôle GridView à des fins de sélection d’un enregistrement particulier. Une colonne de cases d’option est une interface utilisateur appropriée lorsque l’utilisateur est limité à choisir qu’un seul élément de la grille. Dans certains cas, toutefois, nous pouvons souhaitez autoriser l’utilisateur de choisir un nombre arbitraire d’éléments de la grille. Les clients de messagerie électronique basé sur le Web, par exemple, en général, affichent la liste des messages avec une colonne de cases à cocher. L’utilisateur peut sélectionner un nombre arbitraire de messages et puis effectuer des actions, telles que le déplacement des messages électroniques vers un autre dossier ou la suppression.

Dans ce didacticiel, nous verrons comment ajouter une colonne de cases à cocher et comment déterminer les cases à cocher ont été vérifiées lors de la publication. En particulier, nous allons construire un exemple qui simule avec précision l’interface utilisateur du client sur le web. Notre exemple inclut un GridView paginé qui répertorie les produits dans le `Products` table de base de données avec une case à cocher de chaque ligne (voir Figure 1). Un bouton Supprimer les produits sélectionnés, cette option va supprimer ces produits sélectionnés.


[![Chaque ligne de produit inclut une case à cocher](adding-a-gridview-column-of-checkboxes-vb/_static/image1.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image1.png)

**Figure 1**: chaque ligne de produit inclut une case à cocher ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image2.png))


## <a name="step-1-adding-a-paged-gridview-that-lists-product-information"></a>Étape 1 : Ajout d’un GridView paginé qui répertorie des informations de produit

Nous vous inquiétez pas sur l’ajout d’une colonne de cases à cocher, avant de vous permettre s premier qui répertorie les produits dans un GridView qui prend en charge la pagination. Commencez par ouvrir le `CheckBoxField.aspx` page dans le `EnhancedGridView` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, en définissant ses `ID` à `Products`. Ensuite, choisissez de lier le contrôle GridView à une nouvelle ObjectDataSource nommé `ProductsDataSource`. Configurer ObjectDataSource à utiliser le `ProductsBLL` classe, en appelant le `GetProducts()` méthode pour retourner les données. Comme cette GridView est en lecture seule, définissez les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None).


[![Créer un nouveau ObjectDataSource nommé ProductsDataSource](adding-a-gridview-column-of-checkboxes-vb/_static/image2.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image3.png)

**Figure 2**: créer un nouveau nommé de ObjectDataSource `ProductsDataSource` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image4.png))


[![Configurer l’ObjectDataSource pour récupérer des données à l’aide de la méthode GetProducts()](adding-a-gridview-column-of-checkboxes-vb/_static/image3.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image5.png)

**Figure 3**: configurer l’ObjectDataSource pour récupérer des données à l’aide du `GetProducts()` (méthode) ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image6.png))


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](adding-a-gridview-column-of-checkboxes-vb/_static/image4.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image7.png)

**Figure 4**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image8.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio crée automatiquement BoundColumns et un CheckBoxColumn pour les champs de données liées au produit. Comme nous l’avons fait dans le didacticiel précédent, supprimez tout sauf la `ProductName`, `CategoryName`, et `UnitPrice` BoundFields et modifier les `HeaderText` propriétés pour le produit, la catégorie et prix. Configurer le `UnitPrice` BoundField afin que sa valeur est mise en forme comme une valeur monétaire. Également configurer le contrôle GridView pour prendre en charge la pagination en activant la case à cocher Activer la pagination de la balise active.

Permettent de s également ajouter l’interface utilisateur pour la suppression des produits sélectionnés. Ajouter un contrôle bouton Web sous le GridView, en définissant ses `ID` à `DeleteSelectedProducts` et son `Text` propriété à supprimer les produits sélectionnés. Au lieu de réellement supprimer les produits à partir de la base de données, pour cet exemple nous allons simplement affiche un message indiquant les produits qui aurait été supprimés. Pour ce faire, ajoutez un contrôle Web Label sous le bouton. Affectez à son ID `DeleteResults`, désactivez les sa `Text` propriété et définissez son `Visible` et `EnableViewState` propriétés à `False`.

Après avoir apporté ces modifications, le balisage déclaratif s GridView, ObjectDataSource, bouton et étiquette doit similaire au suivant :


[!code-aspx[Main](adding-a-gridview-column-of-checkboxes-vb/samples/sample1.aspx)]

Prenez un moment pour afficher la page dans un navigateur (voir Figure 5). À ce stade, vous devez voir le nom, la catégorie et le prix des dix premiers produits.


[![Le nom, la catégorie et le prix des dix premiers produits sont répertoriés.](adding-a-gridview-column-of-checkboxes-vb/_static/image5.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image9.png)

**Figure 5**: le nom, catégorie et les prix des dix premiers produits répertoriés ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image10.png))


## <a name="step-2-adding-a-column-of-checkboxes"></a>Étape 2 : Ajout d’une colonne de cases à cocher

Étant donné que ASP.NET 2.0 comprend un CheckBoxField, on pourrait penser qu’il peut être utilisé pour ajouter une colonne de cases à cocher à un GridView. Malheureusement, qui n’est pas le cas, comme le CheckBoxField est conçu pour fonctionner avec un champ de données booléen. Autrement dit, pour pouvoir utiliser le CheckBoxField, nous devons spécifier le champ de données sous-jacente, dont la valeur est consultée pour déterminer si le rendu de la case à cocher est activée. Nous ne pouvons pas utiliser le CheckBoxField simplement inclure une colonne de cases à cocher est désactivée.

Au lieu de cela, nous devons ajouter TemplateField et ajouter un contrôle de case à cocher Web à son `ItemTemplate`. Pour commencer, ajoutez-en un TemplateField pour le `Products` GridView et rendre le premier champ (extrême gauche). À partir de la balise active de s GridView, cliquez sur le lien Modifier les modèles et puis faites glisser un contrôle de case à cocher Web à partir de la boîte à outils dans le `ItemTemplate`. Définissez cet case à cocher s `ID` propriété `ProductSelector`.


[![Ajouter un contrôle de case à cocher Web nommé ProductSelector à ItemTemplate s TemplateField](adding-a-gridview-column-of-checkboxes-vb/_static/image6.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image11.png)

**Figure 6**: ajouter un contrôle de case à cocher Web nommé `ProductSelector` au s TemplateField `ItemTemplate` ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image12.png))


Avec le contrôle TemplateField et la case à cocher Web ajouté, chaque ligne inclut désormais une case à cocher. La figure 7 illustre cette page, lorsqu’ils sont affichés via un navigateur, après ont ajouté le TemplateField et la case à cocher.


[![Chaque ligne de produit comprend désormais une case à cocher](adding-a-gridview-column-of-checkboxes-vb/_static/image7.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image13.png)

**Figure 7**: chaque ligne de produit comprend désormais une case à cocher ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image14.png))


## <a name="step-3-determining-what-checkboxes-were-checked-on-postback"></a>Étape 3 : Déterminer les cases à cocher ont été vérifiées lors de la publication

À ce stade, nous avons une colonne de cases à cocher, mais aucun moyen de déterminer les cases à cocher ont été vérifiées lors de la publication. Lorsque l’utilisateur clique sur le bouton Supprimer les produits sélectionnés, cependant, nous devons savoir les cases à cocher ont été contrôlées afin de supprimer les produits.

Le contrôle GridView s [ `Rows` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview.rows.aspx) fournit l’accès aux lignes de données dans le GridView. Nous pouvons parcourir ces lignes, accéder par programme le contrôle de case à cocher, puis consultez son `Checked` propriété pour déterminer si la case à cocher a été sélectionné.

Créer un gestionnaire d’événements pour le `DeleteSelectedProducts` le contrôle bouton Web s `Click` événement et ajoutez le code suivant :


[!code-vb[Main](adding-a-gridview-column-of-checkboxes-vb/samples/sample2.vb)]

Le `Rows` propriété retourne une collection de `GridViewRow` instances utilisés les lignes de données de GridView s. Le `For Each` boucle ici énumère cette collection. Pour chaque `GridViewRow` de l’objet, la ligne s case à cocher est accessible par programmation à l’aide de `row.FindControl("controlID")`. Si la case à cocher est activée, la ligne s correspondant `ProductID` valeur est extraite de la `DataKeys` collection. Dans cet exercice, nous avons simplement afficher un message d’information dans le `DeleteResults` d’étiquette, bien que dans une application opérationnelle nous d à la place un appel à la `ProductsBLL` classe s `DeleteProduct(productID)` (méthode).

Avec l’ajout de ce gestionnaire d’événements, en cliquant sur le bouton Supprimer des produits sélectionnés maintenant affiche le `ProductID` s des produits sélectionnés.


[![Clic sur le bouton Supprimer de produits sélectionnée le ProductID de produits sélectionnée sont répertoriés.](adding-a-gridview-column-of-checkboxes-vb/_static/image8.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image15.png)

**Figure 8**: lors de la supprimer sélectionné produits bouton les produits sélectionnés `ProductID` s sont répertoriés ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image16.png))


## <a name="step-4-adding-check-all-and-uncheck-all-buttons"></a>Étape 4 : Ajout vérifie toutes les et désactivez tous les boutons

Si un utilisateur souhaite supprimer tous les produits sur la page actuelle, ils doivent vérifier chaque dix case. Nous pouvons aider à accélérer ce processus en ajoutant un tout bouton qui, lorsque vous cliquez dessus, sélectionne toutes les cases à cocher dans la grille. Un bouton désélectionner tout serait utile.

Ajoutez deux contrôles bouton à la page, en les plaçant au-dessus le GridView. Définir le premier s `ID` à `CheckAll` et son `Text` propriété tout ; définir la seconde s `ID` à `UncheckAll` et son `Text` propriété désélectionner tout.


[!code-aspx[Main](adding-a-gridview-column-of-checkboxes-vb/samples/sample3.aspx)]

Ensuite, créez une méthode dans la classe code-behind nommée `ToggleCheckState(checkState)` qui, lorsqu’elle est appelée, énumère les `Products` GridView s `Rows` collection et définit chaque case à cocher s `Checked` à la valeur de la propriété dans *checkState*  paramètre.


[!code-vb[Main](adding-a-gridview-column-of-checkboxes-vb/samples/sample4.vb)]

Ensuite, créez `Click` gestionnaires d’événements pour le `CheckAll` et `UncheckAll` boutons. Dans `CheckAll` Gestionnaire d’événements s, il vous suffit d’appel `ToggleCheckState(True)`; dans `UncheckAll`, appelez `ToggleCheckState(False)`.


[!code-vb[Main](adding-a-gridview-column-of-checkboxes-vb/samples/sample5.vb)]

Avec ce code, en cliquant sur le bouton Vérifier tout entraîne une publication et vérifie toutes les cases à cocher dans le GridView. De même, en cliquant sur Désélectionner tout Désélectionne toutes les cases. La figure 9 illustre l’écran après que le bouton Vérifier tout a été vérifié.


[![En cliquant sur le contrôle que bouton tous sélectionne toutes les cases à cocher](adding-a-gridview-column-of-checkboxes-vb/_static/image9.gif)](adding-a-gridview-column-of-checkboxes-vb/_static/image17.png)

**Figure 9**: en cliquant sur les vérifier tous les bouton sélectionne toutes les cases à cocher ([cliquez pour afficher l’image en taille réelle](adding-a-gridview-column-of-checkboxes-vb/_static/image18.png))


> [!NOTE]
> Affichage d’une colonne de cases à cocher, une approche en sélectionnant ou désélectionnant toutes les cases à cocher lorsque est via une case à cocher dans la ligne d’en-tête. En outre, en cours vérifie toutes les / décochez toutes les implémentations requiert une publication (postback). Les cases à cocher activée ou désactivée, cependant, peut être entièrement par le biais d’un script côté client, en fournissant une expérience utilisateur plus active. Pour Explorer à l’aide d’une case à cocher de la ligne en-tête pour tout et désélectionner tout en détail, ainsi que d’en savoir plus sur l’utilisation des techniques de côté client, consultez [la vérification toutes les cases à cocher dans un Script côté Client à l’aide de GridView et une case à cocher tous les vérifier](http://aspnet.4guysfromrolla.com/articles/053106-1.aspx).


## <a name="summary"></a>Récapitulatif

Dans les cas où vous avez besoin pour permettre aux utilisateurs de choisir un nombre arbitraire de lignes à partir d’un GridView, avant de continuer, l’ajout d’une colonne de cases à cocher est une option. Comme nous l’avons vu dans ce didacticiel, y compris une colonne de cases à cocher dans le GridView implique l’ajout de TemplateField avec un contrôle de case à cocher Web. À l’aide d’un contrôle Web (par opposition à l’injection de balisage directement dans le modèle, comme nous l’avons fait dans le didacticiel précédent) ASP.NET automatiquement se souvient de cases à cocher ont été et n’ont pas été vérifiées via la publication (postback). Nous pouvons également accéder par programme les cases à cocher dans le code pour déterminer si une case à cocher donnée est activé, ou pour modifier l’état activé.

Ce didacticiel et la dernière étudié d’ajout d’une colonne de sélecteur de ligne pour le contrôle GridView. Dans notre étape suivante du didacticiel, nous allons examiner comment, avec un peu de travail, nous pouvons ajouter des fonctionnalités de l’insertion au GridView.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](adding-a-gridview-column-of-radio-buttons-vb.md)
[Suivant](inserting-a-new-record-from-the-gridview-s-footer-vb.md)
