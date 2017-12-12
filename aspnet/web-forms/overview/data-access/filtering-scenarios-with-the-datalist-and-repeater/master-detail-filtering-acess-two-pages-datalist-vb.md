---
uid: web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-filtering-acess-two-pages-datalist-vb
title: "Maître/détail, le filtrage entre les deux Pages (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons comment séparer un rapport maître/détail sur deux pages. Dans la page « master », nous utilisons un contrôle du répéteur pour afficher une liste de categ..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2010
ms.topic: article
ms.assetid: f1a1be2c-6fd9-4a09-916e-aa1b98d5cf17
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-filtering-acess-two-pages-datalist-vb
msc.type: authoredcontent
ms.openlocfilehash: 3f43fa998b81800cb1a2b7796ebb3922fc1caeb8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-filtering-across-two-pages-vb"></a>Maître/détail, le filtrage entre les deux Pages (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_34_VB.exe) ou [télécharger le PDF](master-detail-filtering-acess-two-pages-datalist-vb/_static/datatutorial34vb1.pdf)

> Dans ce didacticiel, nous allons comment séparer un rapport maître/détail sur deux pages. Dans la page « maître », nous utilisons un contrôle du répéteur pour afficher une liste de catégories qui, lorsque vous cliquez sur, dirige l’utilisateur à la page « Détails » où un contrôle DataList deux colonnes affiche les produits appartenant à la catégorie sélectionnée.


## <a name="introduction"></a>Introduction

Dans le [filtrage de maître/détail entre les deux Pages](../masterdetail/master-detail-filtering-across-two-pages-vb.md) didacticiel, nous avons examiné ce modèle à l’aide d’un contrôle GridView pour afficher tous les fournisseurs dans le système. Ce contrôle GridView inclus un HyperLinkField, ce qui est restitué sous la forme d’un lien vers une deuxième page, en passant le `SupplierID` dans la chaîne de requête. La deuxième page permet de répertorier les produits fournis par le fournisseur sélectionné un GridView.

Ces rapports de deux pages maître/détail peuvent être accomplies à l’aide de contrôles DataList et répéteur. La seule différence est que le contrôle DataList, ni le répéteur prend en charge le contrôle HyperLinkField. Au lieu de cela, nous devons ajouter un contrôle de lien hypertexte Web ou un élément d’ancrage HTML (`<a>`) au sein du contrôle `ItemTemplate`. Du lien hypertexte `NavigateUrl` propriété ou le point d’ancrage `href` attribut peut ensuite être personnalisé à l’aide d’approches déclaratives ou par programme.

Dans ce didacticiel, nous allons explorer un exemple qui répertorie les catégories dans une liste à puces sur une page à l’aide d’un contrôle du répéteur. Chaque élément de liste inclut le nom et la description, de la catégorie avec le nom de catégorie affiché sous la forme d’un lien vers une deuxième page. En cliquant sur ce lien sera tous l’utilisateur à la deuxième page, où un contrôle DataList affiche les produits qui appartiennent à la catégorie sélectionnée.

## <a name="step-1-displaying-the-categories-in-a-bulleted-list"></a>Étape 1 : Afficher les catégories dans une liste à puces

La première étape dans la création de n’importe quel rapport maître/détail consiste à démarrer en affichant les enregistrements « maîtres ». Par conséquent, la première tâche est pour afficher les catégories dans la page « maître ». Ouvrez le `CategoryListMaster.aspx` page dans le `DataListRepeaterFiltering` dossier, ajoutez un contrôle du répéteur et, à partir de la balise active, choisir d’ajouter un nouveau ObjectDataSource. Configurer le nouveau ObjectDataSource afin qu’il accède à ses données à partir de la `CategoriesBLL` la classe `GetCategories` (méthode) (voir Figure 1).


[![Configurer pour utiliser méthode la classe CategoriesBLL GetCategories ObjectDataSource](master-detail-filtering-acess-two-pages-datalist-vb/_static/image2.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image1.png)

**Figure 1**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` de classe `GetCategories` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image3.png))


Ensuite, définissez les modèles de répéteur tel qu’il affiche chaque nom de catégorie et la description sous la forme d’un élément dans une liste à puces. Nous allons pas encore soucier chaque catégorie de lien vers la page de détails. Vous trouverez ci-dessous le balisage déclaratif pour le répéteur et ObjectDataSource :

[!code-aspx[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample1.aspx)]

Avec ce balisage terminé, prenez un moment pour afficher la progression via un navigateur. Comme le montre la Figure 2, la répétition est rendu sous la forme d’une liste à puces montrant le nom et la description de chaque catégorie.


[![Chaque catégorie est affiché comme un élément de liste à puces](master-detail-filtering-acess-two-pages-datalist-vb/_static/image5.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image4.png)

**Figure 2**: chaque catégorie est affiché comme un élément de liste à puces ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image6.png))


## <a name="step-2-turning-the-category-name-into-a-link-to-the-details-page"></a>Étape 2 : Activer le nom de catégorie dans un lien vers la Page de détails

Pour autoriser un utilisateur afficher les informations de « détails » pour une catégorie donnée, nous devons ajouter un lien à chaque liste à puces d’élément qui, lorsque vous cliquez sur, dirige l’utilisateur à la deuxième page (`ProductsForCategoryDetails.aspx`). Cette seconde page puis affichera les produits pour la catégorie sélectionnée à l’aide d’un contrôle DataList. Afin de déterminer la catégorie dont le lien de l’utilisateur a cliqué, vous devez transmettre la catégorie d’un clic sur `CategoryID` à la deuxième page via un mécanisme. La façon la plus simple, plus simple pour transférer des données scalaires à partir d’une page à l’autre est dans la chaîne de requête, qui est l’option que nous allons utiliser dans ce didacticiel. En particulier, la `ProductsForCategoryDetails.aspx` page attendra sélectionné  *`categoryID`*  valeur doit être passé à un champ de chaîne de requête nommé `CategoryID`. Par exemple, pour afficher les produits pour la catégorie boissons, qui a un `CategoryID` 1, un utilisateur visite `ProductsForCategoryDetails.aspx?CategoryID=1`.

Pour créer un lien hypertexte pour chaque élément de liste à puces dans le répéteur, nous devons ajouter un contrôle de lien hypertexte Web ou un élément d’ancrage HTML (`<a>`) pour le `ItemTemplate`. Dans les scénarios où le lien hypertexte est affichent la même pour chaque ligne, une de ces méthodes seront suffisante. Pour répéteurs, je préfère à l’aide de l’élément d’ancrage. Pour utiliser l’élément d’ancrage, mise à jour ItemTemplate de répéteur :

[!code-aspx[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample2.aspx)]

Notez que la `CategoryID` peuvent être ajoutées directement au sein de l’élément d’ancrage `href` attribut ; Toutefois, pour veillez donc à délimiter la `href` valeur de l’attribut avec les apostrophes (et notez les guillemets) depuis le `Eval` (méthode) dans le `href` attribut délimite sa chaîne (`"CategoryID"`) avec des guillemets. Un contrôle de lien hypertexte Web peut également être utilisé à la place :

[!code-aspx[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample3.aspx)]

Notez comment la partie statique de l’URL : `ProductsForCategoryDetails.aspx?CategoryID` , est ajouté au résultat de `Eval("CategoryID")` directement dans la syntaxe de liaison de données à l’aide de la concaténation de chaînes.

L’un des avantages de l’utilisation du contrôle de lien hypertexte sont qu’il par programmation accessibles à partir de répéteur `ItemDataBound` Gestionnaire d’événements, si nécessaire. Par exemple, vous souhaiterez afficher le nom de catégorie sous forme de texte plutôt que comme un lien pour les catégories avec aucun produit associé. Cette vérification puisse être effectuée par programmation dans le `ItemDataBound` Gestionnaire d’événements pour des catégories sans aucune associés produits, le lien hypertexte `NavigateUrl` propriété peut être définie sur une chaîne vide, entraînant de ce nom de catégorie rendu en tant que texte brut (plutôt que sous forme de lien). Faire référence à la [mise en forme le contrôle DataList et données répéteur en fonction de](../displaying-data-with-the-datalist-and-repeater/formatting-the-datalist-and-repeater-based-upon-data-vb.md) didacticiel pour plus d’informations sur la mise en forme le contrôle DataList et du répéteur contenu basé sur la logique de programmation via la `ItemDataBound` Gestionnaire d’événements.

Si vous suivez le long, n’hésitez pas à utiliser un élément d’ancrage ou une approche de contrôle de lien hypertexte dans votre page. Quelle que soit l’approche, lors de l’affichage de la page via un navigateur chaque nom de catégorie doit être restitué sous la forme d’un lien vers `ProductsForCategoryDetails.aspx`, en passant l’applicable `CategoryID` valeur (voir Figure 3).


[![Les noms de catégorie maintenant lier à ProductsForCategoryDetails.aspx](master-detail-filtering-acess-two-pages-datalist-vb/_static/image8.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image7.png)

**Figure 3**: la catégorie noms lien maintenant à `ProductsForCategoryDetails.aspx` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image9.png))


## <a name="step-3-listing-the-products-that-belong-to-the-selected-category"></a>Étape 3 : Liste des produits qui appartiennent à la catégorie sélectionnée

Avec la `CategoryListMaster.aspx` page terminée, nous pouvons activer notre attention sur l’implémentation de la page « Détails », `ProductsForCategoryDetails.aspx`. Ouvrir cette page, faites glisser un contrôle DataList à partir de la boîte à outils vers le concepteur et définir son `ID` propriété `ProductsInCategory`. Ensuite, choisissez de balise du contrôle DataList ajouter un nouveau ObjectDataSource à la page, en le nommant `ProductsInCategoryDataSource`. Configurez-le de sorte qu’elle appelle la `ProductsBLL` de classe `GetProductsByCategoryID(categoryID)` méthode ; l’ensemble de la liste déroulante répertorie dans les onglets INSERT, UPDATE et DELETE à (None).


[![Configurer pour utiliser GetProductsByCategoryID(categoryID) méthode la classe ProductsBLL ObjectDataSource](master-detail-filtering-acess-two-pages-datalist-vb/_static/image11.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image10.png)

**Figure 4**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` de classe `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image12.png))


Étant donné que la `GetProductsByCategoryID(categoryID)` méthode accepte un paramètre d’entrée (*`categoryID`*), l’Assistant de choisir la Source de données nous offre la possibilité de spécifier la source du paramètre. Définissez la source de paramètre de chaîne de requête à l’aide de la QueryStringField `CategoryID`.


[![Utilisez la champ Querystring CategoryID en tant que la Source du paramètre](master-detail-filtering-acess-two-pages-datalist-vb/_static/image14.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image13.png)

**Figure 5**: Querystring Field `CategoryID` en tant que la Source du paramètre ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image15.png))


Comme nous l’avons vu dans les didacticiels précédents, après la fin de l’Assistant de choisir la Source de données, Visual Studio crée automatiquement un `ItemTemplate` pour le contrôle DataList qui répertorie chaque nom de champ de données et la valeur. Remplacez ce modèle par un qui répertorie uniquement du produit nom du fournisseur et prix. En outre, valeur de DataList `RepeatColumns` propriété à 2. Après ces modifications, votre DataList et un du ObjectDataSource balisage déclaratif doit ressembler à ce qui suit :

[!code-aspx[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample4.aspx)]

Pour afficher cette page en action, démarrez à partir de la `CategoryListMaster.aspx` page ; ensuite, cliquez sur un lien dans la liste à puces de catégories. Cela vous accédez à `ProductsForCategoryDetails.aspx`, en passant le long de le `CategoryID` via la chaîne de requête. Le `ProductsInCategoryDataSource` ObjectDataSource dans `ProductsForCategoryDetails.aspx` ensuite obtenir uniquement les produits de la catégorie spécifiée et les afficher dans le contrôle DataList, ce qui rend les deux produits par ligne. La figure 6 illustre une capture d’écran de `ProductsForCategoryDetails.aspx` lorsque vous affichez les boissons.


[![Les boissons sont affichés, deux par ligne](master-detail-filtering-acess-two-pages-datalist-vb/_static/image17.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image16.png)

**Figure 6**: les boissons sont affichés, deux par ligne ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image18.png))


## <a name="step-4-displaying-category-information-on-productsforcategorydetailsaspx"></a>Étape 4 : Affichage des informations de catégorie sur ProductsForCategoryDetails.aspx

Lorsqu’un utilisateur clique sur une catégorie dans `CategoryListMaster.aspx`, il est dirigé vers `ProductsForCategoryDetails.aspx` et affiche les produits qui appartiennent à la catégorie sélectionnée. Toutefois, dans `ProductsForCategoryDetails.aspx` il n’y aucun des signaux visuels à quelle catégorie a été sélectionné. Un utilisateur destiné à cliquer sur les boissons, mais les Condiments accidentellement un clic sur, qui n’a aucun moyen de se rendre compte de son erreur une fois qu’ils atteignent `ProductsForCategoryDetails.aspx`. Pour atténuer ce problème potentiel, nous pouvons afficher plus d’informations sur la catégorie sélectionnée, son nom et sa description, en haut de la `ProductsForCategoryDetails.aspx` page.

Pour ce faire, ajoutez un FormView au-dessus du contrôle du répéteur dans `ProductsForCategoryDetails.aspx`. Ensuite, ajoutez un nouveau ObjectDataSource à la page à partir de la balise FormView nommé `CategoryDataSource` et configurez-le pour utiliser le `CategoriesBLL` la classe `GetCategoryByCategoryID(categoryID)` (méthode).


[![Accéder aux informations sur la catégorie par le biais GetCategoryByCategoryID(categoryID) (méthode de la classe CategoriesBLL)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image20.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image19.png)

**Figure 7**: accéder aux informations sur la catégorie via la `CategoriesBLL` de classe `GetCategoryByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image21.png))


Comme avec la `ProductsInCategoryDataSource` ObjectDataSource ajouté à l’étape 3, le `CategoryDataSource`d’Assistant Configurer la Source de données nous invite pour une source de la `GetCategoryByCategoryID(categoryID)` paramètre d’entrée (méthode). Utiliser les mêmes paramètres comme précédemment, la définition de la source de paramètre à la chaîne de requête et à la valeur QueryStringField `CategoryID` (voir la Figure 5).

Après la fin de l’Assistant, Visual Studio crée automatiquement un `ItemTemplate`, `EditItemTemplate`, et `InsertItemTemplate` pour le FormView. Étant donné que nous fournissons une interface en lecture seule, n’hésitez pas à supprimer la `EditItemTemplate` et `InsertItemTemplate`. En outre, n’hésitez pas à personnaliser les FormView `ItemTemplate`. Après avoir supprimé les modèles superflus et personnalisation de l’ItemTemplate, les FormView ObjectDataSource balisage déclaratif doit ressembler à ce qui suit :

[!code-aspx[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample5.aspx)]

La figure 8 illustre un capture d’écran lors afficher cette page via un navigateur.

> [!NOTE]
> Outre le FormView, j’ai également ajouté un contrôle de lien hypertexte au-dessus du FormView qui dirige l’utilisateur à la liste des catégories (`CategoryListMaster.aspx`). N’hésitez pas à placer ce lien ailleurs ou à ne pas l’utiliser.


[![Les informations de catégorie sont maintenant affichée en haut de la Page](master-detail-filtering-acess-two-pages-datalist-vb/_static/image23.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image22.png)

**Figure 8**: les informations de catégorie est maintenant affichée en haut de la Page ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image24.png))


## <a name="step-5-displaying-a-message-if-no-products-belong-to-the-selected-category"></a>Étape 5 : Affichage d’un Message si aucun produit n’appartiennent à la catégorie sélectionnée

Le `CategoryListMaster.aspx` page répertorie toutes les catégories dans le système, quel que soit s’il existe des associés des produits. Si un utilisateur clique sur une catégorie avec aucune produits associés, du contrôle DataList dans `ProductsForCategoryDetails.aspx` ne seront pas rendues, comme sa source de données n’aura pas tous les éléments. Comme nous l’avons vu dans ces didacticiels, le contrôle GridView fournit un `EmptyDataText` propriété qui peut être utilisée pour spécifier un message texte à afficher si aucun enregistrement n’est dans sa source de données. Malheureusement, répéteur ni DataList a une telle propriété.

Pour afficher un message informant l’utilisateur qu’aucun produit correspondant pour la catégorie sélectionnée, nous devons ajouter une étiquette de contrôle à la page dont `Text` le message à afficher dans le cas où aucun produit correspondant est assignée à la propriété. Nous devons définir par programmation son `Visible` propriété basée sur ou non du contrôle DataList contienne des éléments.

Pour ce faire, commencez par ajouter une étiquette en dessous du contrôle DataList. Définir son `ID` propriété `NoProductsMessage` et son `Text` propriété « Il n’existe aucun produit de la catégorie sélectionnée... » Ensuite, nous devons définir par programme de cette étiquette `Visible` propriété basée sur ou non des données a été liées à la `ProductsInCategory` DataList. Cette attribution doit être effectuée après que les données a été liées au contrôle DataList. Pour le contrôle GridView, DetailsView et FormView, nous pourrions créer un gestionnaire d’événements du contrôle `DataBound` événement qui se déclenche lorsque la liaison de données est terminée. Toutefois, le contrôle DataList, ni le répéteur a un `DataBound` événements disponibles.

Pour cet exemple particulier, nous pouvons attribuer l’étiquette `Visible` propriété dans le `Page_Load` Gestionnaire d’événements, dans la mesure où les données seront ont reçu du contrôle DataList avant de la page `Load` événement. Toutefois, cette approche ne fonctionne pas dans le cas général, comme les données à partir de ObjectDataSource peuvent être liées à du contrôle DataList plus loin dans le cycle de vie de la page. Par exemple, si les données affichées sont basées sur la valeur dans un autre contrôle, telle qu’elle est lors de l’affichage d’un rapport maître/détail à l’aide d’une liste déroulante contenant les enregistrements « maîtres », les données ne peuvent pas reliée au contrôle Web de données jusqu'à ce que le `PreRender` à l’étape dans le cycle de vie de la page.

Une solution qui fonctionne pour tous les cas consiste à affecter la `Visible` propriété `False` dans le contrôle de DataList `ItemDataBound` (ou `ItemCreated`) lors de la liaison d’un type d’élément de gestionnaire d’événements `Item` ou `AlternatingItem`. Dans ce cas nous savons qu’il existe au moins un élément dans la source de données et qu’elle peut par conséquent masquer le `NoProductsMessage` étiquette. En plus de ce gestionnaire d’événements, nous devons également un gestionnaire d’événements pour le contrôle de DataList `DataBinding` événement, où nous initialiser l’étiquette `Visible` propriété `True`. Étant donné que la `DataBinding` événement est déclenché avant la `ItemDataBound` d’événements, l’étiquette `Visible` propriété sera initialement définie `True`; s’il existe des éléments de données, toutefois, elle sera définie `False`. Le code suivant met en œuvre cette logique :

[!code-vb[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample6.vb)]

Toutes les catégories dans la base de données Northwind sont associés à un ou plusieurs produits. Pour tester cette fonctionnalité, j’ai ajustée manuellement la base de données Northwind pour ce didacticiel, la réaffectation de tous les produits de la catégorie de produit (`CategoryID` = 7) à la catégorie Seafood (`CategoryID` = 8). Cela peut être accompli en choisissant nouvelle requête et à l’aide de ce qui suit à partir de l’Explorateur de serveurs `UPDATE` instruction :

[!code-sql[Main](master-detail-filtering-acess-two-pages-datalist-vb/samples/sample7.sql)]

Après la mise à jour de la base de données en conséquence, revenir à la `CategoryListMaster.aspx` page, puis cliquez sur le lien du produit. Étant donné que n’est plus tous les produits appartenant à la catégorie de produit, vous devez voir le « Il n’existe aucun produit de la catégorie sélectionnée... » message, comme indiqué dans la Figure 9.


[![Un Message s’affiche s’il n’y aucune faisant partie de produits à la catégorie sélectionnée](master-detail-filtering-acess-two-pages-datalist-vb/_static/image26.png)](master-detail-filtering-acess-two-pages-datalist-vb/_static/image25.png)

**Figure 9**: un Message s’affiche s’il n’y aucune faisant partie de produits à la catégorie sélectionnée ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-acess-two-pages-datalist-vb/_static/image27.png))


## <a name="summary"></a>Résumé

Alors que les rapports maître/détail peuvent afficher les enregistrements maître et détail sur une seule page, dans de nombreux sites Web qu’ils sont séparés sur deux pages web. Dans ce didacticiel, nous avons étudié comment implémenter un tel rapport maître/détail en ayant des catégories répertoriées dans une liste à puces à l’aide d’un répéteur dans la page web « maître » et les produits associés, répertoriés dans la page « Détails ». Chaque élément de liste dans la page maître web contenait un lien vers la page de détails passé le long de la ligne `CategoryID` valeur.

Dans la page Détails de la récupération de ces produits pour le fournisseur spécifié a été effectuée via la `ProductsBLL` la classe `GetProductsByCategoryID(categoryID)` (méthode). Le  *`categoryID`*  la valeur du paramètre a été spécifiée de façon déclarative à l’aide de la `CategoryID` valeur querystring comme source du paramètre. Nous avons étudié également comment afficher les détails de catégorie dans la page de détails à l’aide d’un FormView et comment afficher un message si aucun produit appartenant à la catégorie sélectionnée.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Zack Jones et Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-filtering-with-a-dropdownlist-datalist-vb.md)
[Suivant](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb.md)
