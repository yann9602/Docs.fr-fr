---
uid: web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-filtering-with-a-dropdownlist-datalist-vb
title: "Maître/détail, le filtrage avec une liste déroulante (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous expliquons comment afficher les rapports maître/détail dans une page web à l’aide de la compréhension des listes pour afficher les enregistrements de 'master' et un contrôle DataList à Affic..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/18/2007
ms.topic: article
ms.assetid: ad0f1014-1eff-465f-bdc6-93058de00e44
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-filtering-with-a-dropdownlist-datalist-vb
msc.type: authoredcontent
ms.openlocfilehash: 1f480cfcfb3b02c9398b2db3e66cec432152a05d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-filtering-with-a-dropdownlist-vb"></a>Maître/détail, le filtrage avec une liste déroulante (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_33_VB.exe) ou [télécharger le PDF](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/datatutorial33vb1.pdf)

> Dans ce didacticiel, nous expliquons comment afficher les rapports maître/détail dans une page web à l’aide de la compréhension des listes pour afficher les enregistrements « maîtres » et un contrôle DataList pour afficher les « détails ».


## <a name="introduction"></a>Introduction

Le rapport maître/détail, que nous avons créé tout d’abord à l’aide d’un GridView plus haut [filtrage de maître/détail avec un DropDownList](../masterdetail/master-detail-filtering-with-a-dropdownlist-vb.md) didacticiel commence en affichant un ensemble d’enregistrements « maîtres ». L’utilisateur peut puis accédez à un des enregistrements maîtres, donc affichage « Détails. » de cet enregistrement maître Les rapports maître/détail sont un choix idéal pour visualiser les relations un-à-plusieurs et pour afficher des informations détaillées en particulier les tables « larges » (ceux qui ont un grand nombre de colonnes). Nous avons examiné comment implémenter les rapports maître/détail à l’aide des contrôles GridView et DetailsView dans les didacticiels précédents. Dans ce didacticiel et les deux, nous allons réexaminer ces concepts, mais le focus sur l’utilisation du contrôle DataList et contrôle de répéteur à la place.

Dans ce didacticiel, nous allons étudier à l’aide d’une liste déroulante pour contenir les enregistrements « maîtres », les enregistrements « details » dans un contrôle DataList.

## <a name="step-1-adding-the-masterdetail-tutorial-web-pages"></a>Étape 1 : Ajout de Pages Web didacticiel maître/détail

Avant de commencer ce didacticiel, nous allons tout d’abord long pour ajouter le dossier et les pages ASP.NET que nous aurons besoin pour ce didacticiel et les deux vous traitez des rapports maître/détail en utilisant les contrôles DataList et répéteur. Commencez par créer un nouveau dossier dans le projet nommé `DataListRepeaterFiltering`. Ensuite, ajoutez les pages ASP.NET cinq suivantes à ce dossier, configuré pour utiliser la page maître de `Site.master`:

- `Default.aspx`
- `FilterByDropDownList.aspx`
- `CategoryListMaster.aspx`
- `ProductsForCategoryDetails.aspx`
- `CategoriesAndProducts.aspx`


![Créez un dossier DataListRepeaterFiltering et ajouter les Pages ASP.NET didacticiel](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image1.png)

**Figure 1**: créer un `DataListRepeaterFiltering` dossier et ajouter les Pages ASP.NET didacticiel


Ensuite, ouvrez le `Default.aspx` page et faites glisser le `SectionLevelTutorialListing.ascx` contrôle utilisateur à partir de la `UserControls` dossier sur l’aire de conception. Ce contrôle utilisateur, que nous avons créé dans le [les Pages maîtres et Navigation du Site](../introduction/master-pages-and-site-navigation-vb.md) didacticiel, énumère le plan de site et affiche les didacticiels à partir de la section en cours dans une liste à puces.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image3.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image2.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image4.png))


Pour disposer de l’affichage de liste à puces les didacticiels maître/détail que nous allons créer, nous devons les ajouter à la carte de site. Ouvrez le `Web.sitemap` et ajoutez le balisage suivant après la balise de nœud de mappage de site « Affichage des données avec la DataList et répéteur » :

[!code-xml[Main](master-detail-filtering-with-a-dropdownlist-datalist-vb/samples/sample1.xml)]


![Mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image5.png)

**Figure 3**: mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET


## <a name="step-2-displaying-the-categories-in-a-dropdownlist"></a>Étape 2 : Afficher les catégories dans une liste déroulante

Notre rapport maître/détail répertorie les catégories dans une liste déroulante, avec les produits de l’élément de liste sélectionné affichées plus bas dans la page dans un contrôle DataList. La première tâche avance us, est ensuite, pour que les catégories affichées dans une liste déroulante. Commencez par ouvrir le `FilterByDropDownList.aspx` page dans le `DataListRepeaterFiltering` faites glisser un contrôle DropDownList à partir de la boîte à outils vers le Concepteur de la page. Ensuite, affectez de DropDownList `ID` propriété `Categories`. Cliquez sur le lien de choisir la Source de données à partir de la balise de DropDownList et créer un nouveau ObjectDataSource nommé `CategoriesDataSource`.


[![Ajouter un nouveau ObjectDataSource nommé CategoriesDataSource](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image7.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image6.png)

**Figure 4**: ajouter un nouveau nommé de ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image8.png))


Configurer le nouveau ObjectDataSource tel qu’il appelle le `CategoriesBLL` la classe `GetCategories()` (méthode). Après avoir configuré ObjectDataSource nous devons encore spécifier quel champ de source de données doit être affiché dans la liste DropDownList et un doit être associé en tant que la valeur de chaque élément de liste. Avoir le `CategoryName` champ en tant que l’affichage et `CategoryID` comme valeur pour chaque élément de liste.


[![A l’affichage DropDownList le champ nom de catégorie et utilisez CategoryID comme valeur](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image10.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image9.png)

**Figure 5**: affiche la DropDownList le `CategoryName` champ et utilisez `CategoryID` comme valeur ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image11.png))


À ce stade, nous avons un contrôle DropDownList qui est rempli avec les enregistrements à partir de la `Categories` table (tous accomplie dans environ six secondes). La figure 6 illustre notre progression jusque-là lorsqu’ils sont affichés via un navigateur.


[![Une liste déroulante répertorie les catégories en cours](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image13.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image12.png)

**Figure 6**: une liste déroulante répertorie les catégories actuel ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image14.png))


## <a name="step-2-adding-the-products-datalist"></a>Étape 2 : Ajout du contrôle DataList produits

La dernière étape de notre rapport maître/détail est pour répertorier les produits associés à la catégorie sélectionnée. Pour ce faire, ajoutez un contrôle DataList à la page et créer un nouveau ObjectDataSource nommé `ProductsByCategoryDataSource`. Avoir le `ProductsByCategoryDataSource` contrôle récupérer ses données à partir de la `ProductsBLL` la classe `GetProductsByCategoryID(categoryID)` (méthode). Étant donné que ce rapport maître/détail est en lecture seule, choisissez (aucun) option dans les onglets INSERT, UPDATE et DELETE.


[![Sélectionnez la méthode GetProductsByCategoryID(categoryID)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image16.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image15.png)

**Figure 7**: sélectionnez le `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image17.png))


Après avoir cliqué sur Suivant, l’Assistant ObjectDataSource nous invite pour la source de la valeur pour le `GetProductsByCategoryID(categoryID)` la méthode  *`categoryID`*  paramètre. Pour utiliser la valeur de l’élément sélectionné `categories` DropDownList élément définie la source de paramètre pour le contrôle et le ControlID à `Categories`.


[![Affectez à la valeur de DropDownList catégories categoryID paramètre](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image19.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image18.png)

**Figure 8**: définir le  *`categoryID`*  paramètre à la valeur de la `Categories` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image20.png))


À la fin de l’Assistant Configurer la Source de données, Visual Studio génère automatiquement un `ItemTemplate` pour le contrôle DataList qui affiche le nom et la valeur de chaque champ de données. Nous allons améliorer du contrôle DataList au lieu d’utiliser un `ItemTemplate` qui affiche simplement le nom du produit, catégorie, fournisseur, quantité par unité et le prix avec un `SeparatorTemplate` qui injecte un `<hr>` élément entre chaque élément. Je vais utiliser la `ItemTemplate` à partir d’un exemple dans le [affichage des données avec les contrôles de répéteur DataList](../displaying-data-with-the-datalist-and-repeater/displaying-data-with-the-datalist-and-repeater-controls-vb.md) didacticiel, mais n’hésitez à utiliser le balisage de modèle, vous trouvez plus attrayante.

Après avoir apporté ces modifications, votre DataList et le balisage de son ObjectDataSource doivent ressembler à ce qui suit :

[!code-aspx[Main](master-detail-filtering-with-a-dropdownlist-datalist-vb/samples/sample2.aspx)]

Prenez un moment à consulter notre progression dans un navigateur. Lors de la première visite de la page, les produits appartenant à la catégorie sélectionnée (boissons) sont affichées (comme indiqué dans la Figure 9), mais la modification DropDownList ne mettre à jour les données. Il s’agit, car une publication (postback) doit survenir pour que le contrôle DataList à mettre à jour. Pour cela vous pouvez soit définie de DropDownList `AutoPostBack` propriété `true` ou ajouter un contrôle bouton Web à la page. Pour ce didacticiel, j’ai choisi pour définir de DropDownList `AutoPostBack` propriété `true`.

Figures 9 et 10 illustrent le rapport maître/détail en action.


[![Lors de la première visite de la Page, les produits de boissons sont affichés.](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image22.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image21.png)

**Figure 9**: lors de la première visite de la Page, les produits de boissons sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image23.png))


[![Sélection d’un nouveau produit (produit) automatiquement provoque une publication (postback), la mise à jour du contrôle DataList](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image25.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image24.png)

**La figure 10**: sélection d’un nouveau produit (produit) automatiquement provoque une publication (postback), la mise à jour du contrôle DataList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image26.png))


## <a name="adding-a----choose-a-category----list-item"></a>Ajout d’un élément de liste «--Choisir une catégorie : »

Lors de la première visite le `FilterByDropDownList.aspx` page les catégories premier élément de liste du DropDownList (boissons) est sélectionnée par défaut, indiquant les produits de boissons dans le contrôle DataList. Dans le *filtrage de maître/détail avec un DropDownList* didacticiel, nous avons ajouté une option «--choisir une catégorie-- » à DropDownList qui a été sélectionnée par défaut et, lorsque sélectionné, affiche *tous les* de la produits de la base de données. Une telle approche était facile à gérer la liste des produits dans un GridView, chaque ligne de produit s’élevait à une petite quantité d’espace d’écran. Avec le contrôle DataList, toutefois, les informations de chaque produit consomment une quantité partie supérieure de l’écran. Nous allons quand même ajouter une option «--choisir une catégorie-- » et qu’elle est sélectionnée par défaut ont, mais il lieu d’afficher tous les produits lorsque sélectionné, nous allons configurer pour qu’il n’affiche aucun produit.

Pour ajouter un nouvel élément de liste à DropDownList, accédez à la fenêtre Propriétés, puis cliquez sur le bouton de sélection dans le `Items` propriété. Ajouter un nouvel élément de liste avec la `Text` «--choisir une catégorie-- » et le `Value` `0`.


![Ajouter une](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image27.png)

**Figure 11**: ajouter un élément de liste «--Choisir une catégorie : »


Ou bien, vous pouvez ajouter l’élément de liste en ajoutant le balisage suivant pour DropDownList :

[!code-aspx[Main](master-detail-filtering-with-a-dropdownlist-datalist-vb/samples/sample3.aspx)]

En outre, nous devons définir le contrôle de DropDownList `AppendDataBoundItems` à `true` , car si elle est définie sur `false` (la valeur par défaut), lorsque les catégories sont liés aux contrôles DropDownList de ObjectDataSource, elles remplaceront toute liste ajoutés manuellement éléments.


![La valeur de la propriété AppendDataBoundItems True](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image28.png)

**Figure 12**: définir le `AppendDataBoundItems` True à la propriété


La raison pour laquelle nous avons choisi la valeur `0` pour obtenir la liste «--Choisir une catégorie-- » élément est, car il n’y pas de catégories dans le système avec une valeur de `0`, par conséquent, aucun enregistrement de produit n’est retournées lorsque l’élément de liste «--Choisir une catégorie-- » est sélectionné. Pour confirmer cela, prenez un moment pour consulter la page via un navigateur. Comme le montre la Figure 13, lorsque initialement l’affichage de la page de l’élément de liste «--Choisir une catégorie-- » est sélectionné et aucun produit n’est affichés.


[![Lors de la](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image30.png)](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image29.png)

**Figure 13**: lorsque l’élément de liste «--Choisir une catégorie-- » est sélectionnée, les produits non sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-a-dropdownlist-datalist-vb/_static/image31.png))


Si vous affichez plutôt *tous les* des produits lorsque l’option «--choisir une catégorie-- » est sélectionnée, utilisez la valeur `-1` à la place. Le lecteur perspicace souvenez-vous que précédent dans le *filtrage de maître/détail avec un DropDownList* didacticiel, nous avons mis à jour le `ProductsBLL` de classe `GetProductsByCategoryID(categoryID)` (méthode) afin que si un  *`categoryID`*  valeur de `-1` a été passé, produit tous les enregistrements retournés.

## <a name="summary"></a>Résumé

Lors de l’affichage des données hiérarchiquement relatives, il vous aide souvent à présenter les données à l’aide de rapports maître/détail, à partir de laquelle l’utilisateur peut démarrer vous parcourez attentivement les données à partir du haut de la hiérarchie et afficher des détails. Dans ce didacticiel, nous avons examiné création d’un rapport maître/détail simple montrant les produits d’une catégorie sélectionnée. Cela a été accompli en utilisant une liste déroulante pour obtenir la liste des catégories et un contrôle DataList pour les produits appartenant à la catégorie sélectionnée.

Dans l’étape suivante du didacticiel, nous allons examiner de séparer les enregistrements maître et des détails sur deux pages. Dans la première page, une liste d’enregistrements « maîtres » s’affichera, avec un lien pour afficher les détails. En cliquant sur le lien, l’utilisateur de la deuxième page, qui affiche les détails de l’enregistrement principal sélectionné est tous.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Randy Schmidt. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-cs.md)
[Suivant](master-detail-filtering-acess-two-pages-datalist-vb.md)
