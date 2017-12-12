---
uid: web-forms/overview/data-access/masterdetail/master-detail-filtering-with-a-dropdownlist-vb
title: "Maître/détail, le filtrage avec une liste déroulante (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous verrons comment afficher les enregistrements maîtres dans un contrôle DropDownList et les détails de l’élément sélectionné dans un contrôle GridView."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: ea44717e-ab2e-46cd-a692-e4a9c0de194c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/masterdetail/master-detail-filtering-with-a-dropdownlist-vb
msc.type: authoredcontent
ms.openlocfilehash: 88e5c65c100bea3cc39b1e08b1aa8a622b4ce7a6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-filtering-with-a-dropdownlist-vb"></a>Maître/détail, le filtrage avec une liste déroulante (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/5/d/7/5d7571fc-d0b7-4798-ad4a-c976c02363ce/ASPNET_Data_Tutorial_7_VB.exe) ou [télécharger le PDF](master-detail-filtering-with-a-dropdownlist-vb/_static/datatutorial07vb1.pdf)

> Dans ce didacticiel, nous verrons comment afficher les enregistrements maîtres dans un contrôle DropDownList et les détails de l’élément sélectionné dans un contrôle GridView.


## <a name="introduction"></a>Introduction

Un type commun de rapport est la *maître/détail rapport*, dans lequel le rapport commence en affichant un ensemble d’enregistrements « maîtres ». L’utilisateur peut puis accédez à un des enregistrements maîtres, donc affichage « Détails. » de cet enregistrement maître Maître/détail des rapports est un choix idéal pour visualiser les relations un-à-plusieurs, tels qu’un rapport affichant toutes les catégories et puis permettant à un utilisateur de sélectionner une catégorie particulière et ses produits associés à afficher. En outre, les rapports maître/détail sont utiles pour afficher des informations détaillées à partir des tables en particulier « larges » (ceux qui ont un grand nombre de colonnes). Par exemple, le niveau « maître » d’un rapport maître/détail peut afficher uniquement le nom et l’unité de prix du produit des produits dans la base de données, et exploration d’un produit particulier affiche les champs de produits supplémentaires (catégorie, fournisseur, quantité par unité, et ainsi de suite).

Il existe plusieurs façons avec laquelle un rapport maître/détail peut être implémenté. Cet aspect ainsi que les trois didacticiels, nous allons examiner une variété de rapports maître/détail. Dans ce didacticiel, nous verrons comment afficher les enregistrements maîtres dans un [contrôle DropDownList](https://msdn.microsoft.com/en-us/library/dtx91y0z.aspx) et les détails de l’élément sélectionné dans un contrôle GridView. En particulier, rapport de maître/détail de ce didacticiel répertorie les informations de catégorie et produit.

## <a name="step-1-displaying-the-categories-in-a-dropdownlist"></a>Étape 1 : Afficher les catégories dans une liste déroulante

Notre rapport maître/détail répertorie les catégories dans une liste déroulante, avec les produits de l’élément de liste sélectionné affichées plus bas dans la page dans un contrôle GridView. La première tâche avance us, est ensuite, pour que les catégories affichées dans une liste déroulante. Ouvrez le `FilterByDropDownList.aspx` page dans le `Filtering` dossier, faites glisser sur une liste déroulante à partir de la boîte à outils vers le Concepteur de la page et définissez son `ID` propriété `Categories`. Ensuite, cliquez sur le lien de choisir la Source de données à partir de la balise de DropDownList. L’Assistant Configuration de Source de données s’affiche.


[![Spécifiez la Source de données de DropDownList](master-detail-filtering-with-a-dropdownlist-vb/_static/image2.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image1.png)

**Figure 1**: spécifiez la Source de données de DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image3.png))


Choisir d’ajouter un nouveau ObjectDataSource nommé `CategoriesDataSource` qui appelle le `CategoriesBLL` la classe `GetCategories()` (méthode).


[![Ajouter un nouveau ObjectDataSource nommé CategoriesDataSource](master-detail-filtering-with-a-dropdownlist-vb/_static/image5.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image4.png)

**Figure 2**: ajouter un nouveau nommé de ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image6.png))


[![Choisissez d’utiliser la classe CategoriesBLL](master-detail-filtering-with-a-dropdownlist-vb/_static/image8.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image7.png)

**Figure 3**: choisissez d’utiliser le `CategoriesBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image9.png))


[![Configurer pour utiliser la méthode GetCategories() ObjectDataSource](master-detail-filtering-with-a-dropdownlist-vb/_static/image11.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image10.png)

**Figure 4**: configurer l’ObjectDataSource à utiliser le `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image12.png))


Après avoir configuré l’ObjectDataSource, nous devons encore spécifier quel champ de source de données doit être affiché dans DropDownList et un doit être associé en tant que la valeur de l’élément de liste. Avoir le `CategoryName` champ en tant que l’affichage et `CategoryID` comme valeur pour chaque élément de liste.


[![A l’affichage DropDownList le champ nom de catégorie et utilisez CategoryID comme valeur](master-detail-filtering-with-a-dropdownlist-vb/_static/image14.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image13.png)

**Figure 5**: affiche la DropDownList le `CategoryName` champ et utilisez `CategoryID` comme valeur ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image15.png))


À ce stade, nous avons un contrôle DropDownList qui est rempli avec les enregistrements à partir de la `Categories` table (tous accomplie dans environ six secondes). La figure 6 illustre notre progression jusque-là lorsqu’ils sont affichés via un navigateur.


[![Une liste déroulante répertorie les catégories en cours](master-detail-filtering-with-a-dropdownlist-vb/_static/image17.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image16.png)

**Figure 6**: une liste déroulante répertorie les catégories actuel ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image18.png))


## <a name="step-2-adding-the-products-gridview"></a>Étape 2 : Ajout de produits GridView

Cette dernière étape de notre rapport maître/détail consiste à répertorier les produits associés à la catégorie sélectionnée. Pour ce faire, ajoutez un contrôle GridView à la page et créer un nouveau ObjectDataSource nommé `productsDataSource`. Avoir le `productsDataSource` contrôle réduire ses données à partir de la `ProductsBLL` la classe `GetProductsByCategoryID(categoryID)` (méthode).


[![Sélectionnez la méthode GetProductsByCategoryID(categoryID)](master-detail-filtering-with-a-dropdownlist-vb/_static/image20.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image19.png)

**Figure 7**: sélectionnez le `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image21.png))


Après le choix de cette méthode, l’Assistant ObjectDataSource nous invite pour la valeur de la méthode  *`categoryID`*  paramètre. Pour utiliser la valeur de l’élément sélectionné `categories` DropDownList élément définie la source de paramètre pour le contrôle et le ControlID à `Categories`.


[![Affectez à la valeur de DropDownList catégories categoryID paramètre](master-detail-filtering-with-a-dropdownlist-vb/_static/image23.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image22.png)

**Figure 8**: définir le  *`categoryID`*  paramètre à la valeur de la `Categories` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image24.png))


Prenez un moment à consulter notre progression dans un navigateur. Lors de la première visite de la page, les produits appartenant à la catégorie sélectionnée (boissons) sont affichées (comme indiqué dans la Figure 9), mais la modification DropDownList ne mettre à jour les données. Il s’agit, car une publication (postback) doit survenir pour que le contrôle GridView à mettre à jour. Pour ce faire, nous avons deux options (qui ne requiert l’écriture de code) :

- **Définir les catégories DropDownList**[propriété AutoPostBack](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.listcontrol.autopostback%28VS.80%29.aspx)**sur True.** (Vous pouvez pour cela en activant l’option Activer AutoPostBack dans la balise de DropDownList.) Cela déclenchera une publication (postback) lorsque vous sélectionnez de DropDownList élément est modifié par l’utilisateur. Par conséquent, lorsque l’utilisateur sélectionne une nouvelle catégorie dans la liste déroulante résulte d’une publication (postback) et le contrôle GridView sera mise à jour avec les produits de la catégorie qui vient d’être sélectionnée. (Il s’agit de l’approche que j’ai utilisé dans ce didacticiel.)
- **Ajouter un contrôle bouton en regard de DropDownList.** Définir son `Text` propriété de l’actualisation ou quelque chose de similaire. Avec cette approche, l’utilisateur doit sélectionner une nouvelle catégorie et puis cliquez sur le bouton. Cliquez sur le bouton entraîne une publication (postback) et mettre à jour le contrôle GridView pour répertorier les produits de la catégorie sélectionnée.

Figures 9 et 10 illustrent le rapport maître/détail en action.


[![Lors de la première visite de la Page, les produits de boissons sont affichés.](master-detail-filtering-with-a-dropdownlist-vb/_static/image26.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image25.png)

**Figure 9**: lors de la première visite de la Page, les produits de boissons sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image27.png))


[![Sélection d’un nouveau produit (produit) automatiquement provoque une publication (postback), la mise à jour le contrôle GridView](master-detail-filtering-with-a-dropdownlist-vb/_static/image29.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image28.png)

**La figure 10**: sélection d’un nouveau produit (produit) automatiquement provoque une publication (postback), la mise à jour le contrôle GridView ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image30.png))


## <a name="adding-a----choose-a-category----list-item"></a>Ajout d’un élément de liste «--Choisir une catégorie : »

Lors de la première visite le `FilterByDropDownList.aspx` page les catégories premier élément de liste du DropDownList (boissons) est sélectionnée par défaut, indiquant les produits de boissons dans le GridView. Plutôt que montrant les produits de la première catégorie, que nous pouvons adopter plutôt créer un élément de DropDownList sélectionné, qui indique que quelque chose comme «--choisir une catégorie-- ».

Pour ajouter un nouvel élément de liste à DropDownList, accédez à la fenêtre Propriétés, puis cliquez sur le bouton de sélection dans le `Items` propriété. Ajouter un nouvel élément de liste avec la `Text` «--choisir une catégorie-- » et le `Value` `-1`.


[![Ajouter un--élément de liste, choisissez une catégorie :](master-detail-filtering-with-a-dropdownlist-vb/_static/image32.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image31.png)

**Figure 11**: ajouter un--élément de liste, choisissez une catégorie--([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image33.png))


Ou bien, vous pouvez ajouter l’élément de liste en ajoutant le balisage suivant pour DropDownList :


[!code-aspx[Main](master-detail-filtering-with-a-dropdownlist-vb/samples/sample1.aspx)]

En outre, nous devons définir le contrôle de DropDownList `AppendDataBoundItems` à True, car lorsque les catégories sont liés aux contrôles DropDownList de ObjectDataSource elles remplaceront tous les éléments ajoutés manuellement la liste si `AppendDataBoundItems` n’est pas la valeur True.


![La valeur de la propriété AppendDataBoundItems True](master-detail-filtering-with-a-dropdownlist-vb/_static/image34.png)

**Figure 12**: définir le `AppendDataBoundItems` True à la propriété


Après ces modifications, lors de la première visite la page de l’option «--choisir une catégorie-- » est sélectionnée et aucun produit n’est affichés.


[![Sur le chargement de Page Initial aucun produit n’est affichés](master-detail-filtering-with-a-dropdownlist-vb/_static/image36.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image35.png)

**Figure 13**: sur l’initiale Page charge non produits sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image37.png))


Est de la raison pour laquelle aucun produit n’est affichés lorsque, car l’élément de liste «--Choisir une catégorie-- » est sélectionné, car sa valeur est `-1` et il n’y aucun produit dans la base de données avec un `CategoryID` de `-1`. S’il s’agit du comportement voulu, puis que vous avez terminé à ce stade ! Si, toutefois, vous souhaitez afficher *tous les* des catégories lorsque l’élément de liste «--Choisir une catégorie-- » est sélectionné, revenir à la `ProductsBLL` de classe et de personnaliser la `GetProductsByCategoryID(categoryID)` méthode afin qu’elle appelle la `GetProducts()` (méthode) si passé dans  *`categoryID`*  paramètre est inférieur à zéro :


[!code-vb[Main](master-detail-filtering-with-a-dropdownlist-vb/samples/sample2.vb)]

La technique utilisée ici est similaire à l’approche que nous avons utilisé pour afficher tous les fournisseurs dans la [paramètres déclaratifs](../basic-reporting/declarative-parameters-cs.md) (didacticiel), bien que dans cet exemple, nous utilisons la valeur `-1` pour indiquer que tous les enregistrements doivent être au lieu de récupérer `Nothing`. C’est parce que le  *`categoryID`*  paramètre de la `GetProductsByCategoryID(categoryID)` méthode attend comme valeur entière passée, tandis que dans le didacticiel de paramètres déclaratifs nous étions en passant un paramètre de chaîne d’entrée.

La figure 14 illustre une capture d’écran de `FilterByDropDownList.aspx` lorsque l’option «--choisir une catégorie-- » est activée. Ici, tous les produits sont affichés par défaut, et l’utilisateur peut limiter l’affichage en choisissant une catégorie spécifique.


[![Tous les produits sont désormais répertoriés par défaut](master-detail-filtering-with-a-dropdownlist-vb/_static/image39.png)](master-detail-filtering-with-a-dropdownlist-vb/_static/image38.png)

**La figure 14**: tous les produits sont désormais répertoriés par défaut ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-vb/_static/image40.png))


## <a name="summary"></a>Résumé

Lors de l’affichage des données hiérarchiquement relatives, il vous aide souvent à présenter les données à l’aide de rapports maître/détail, à partir de laquelle l’utilisateur peut démarrer vous parcourez attentivement les données à partir du haut de la hiérarchie et afficher des détails. Dans ce didacticiel, nous avons examiné création d’un rapport maître/détail simple montrant les produits d’une catégorie sélectionnée. Cela a été accompli en utilisant une liste déroulante pour obtenir la liste des catégories et un GridView pour les produits appartenant à la catégorie sélectionnée.

Dans le [didacticiel suivant](master-detail-filtering-with-two-dropdownlists-vb.md) nous allons l’une étape interface de DropDownList en outre, à l’aide de la compréhension des deux listes.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs.md)
[Suivant](master-detail-filtering-with-two-dropdownlists-vb.md)
