---
uid: web-forms/overview/data-access/masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs
title: "Maître/détail à l’aide d’un GridView maître avec un DetailView détail (c#) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel ont un GridView dont les lignes incluent le nom et le prix de chaque produit, ainsi que d’un bouton de sélection. En cliquant sur le bouton de sélection pour un particu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 0f982827-f8f9-420d-b36b-57b23f5aa519
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs
msc.type: authoredcontent
ms.openlocfilehash: badf9da0e9a26d185e7532b02f53a8acea60ea91
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-using-a-selectable-master-gridview-with-a-details-detailview-c"></a>Maître/détail à l’aide d’un GridView maître avec un DetailView détail (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/6/3/463cf87c-4724-4cbc-b7b5-3f866f43ba50/ASPNET_Data_Tutorial_10_CS.exe) ou [télécharger le PDF](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/datatutorial10cs1.pdf)

> Ce didacticiel ont un GridView dont les lignes incluent le nom et le prix de chaque produit, ainsi que d’un bouton de sélection. En cliquant sur le bouton de sélection d’un produit particulier entraîne son détails complets afin d’être affichés dans un contrôle DetailsView sur la même page.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](master-detail-filtering-across-two-pages-cs.md) nous avons vu comment créer un rapport maître/détail à l’aide de deux pages web : une page web « maître », à partir de laquelle nous affiche la liste des fournisseurs ; et une page web « détails » répertorié ces produits fournis par le texte sélectionné fournisseur. Ce format de page de rapport permettre être condensé dans une page. Ce didacticiel ont un GridView dont les lignes incluent le nom et le prix de chaque produit, ainsi que d’un bouton de sélection. En cliquant sur le bouton de sélection d’un produit particulier entraîne son détails complets afin d’être affichés dans un contrôle DetailsView sur la même page.


[![En cliquant sur le bouton de sélection affiche les détails du produit](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image2.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image1.png)

**Figure 1**: en cliquant sur le bouton de sélection affiche les détails du produit ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image3.png))


## <a name="step-1-creating-a-selectable-gridview"></a>Étape 1 : Création d’un GridView sélectionnable

Souvenez-vous que dans le deux pages maître/détail de rapport que chaque enregistrement principal inclus un lien hypertexte qui, lorsque vous cliquez dessus, envoyé de l’utilisateur à la page de détails en passant de la ligne où vous avez cliquée `SupplierID` valeur dans la chaîne de requête. Cet un lien hypertexte a été ajouté à chaque ligne du contrôle GridView à l’aide d’un HyperLinkField. Pour le rapport maître/détails de la page unique, nous devons un bouton pour chaque contrôle GridView de lignes qui, lorsque vous cliquez dessus, affiche les détails. Le contrôle GridView peut être configuré pour inclure un bouton Sélectionner pour chaque ligne qui provoque une publication (postback) et marque cette ligne en tant que du GridView [SelectedRow](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedrow.aspx).

Commencez par ajouter un contrôle GridView à la `DetailsBySelecting.aspx` page dans le `Filtering` dossier, ses `ID` propriété `ProductsGrid`. Ensuite, ajoutez un nouveau ObjectDataSource nommé `AllProductsDataSource` qui appelle le `ProductsBLL` la classe `GetProducts()` (méthode).


[![Créer un ObjectDataSource nommé AllProductsDataSource](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image5.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image4.png)

**Figure 2**: créer un ObjectDataSource nommé `AllProductsDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image6.png))


[![Utilisez la classe ProductsBLL](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image8.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image7.png)

**Figure 3**: utilisez le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image9.png))


[![Configurer l’ObjectDataSource pour appeler la méthode GetProducts()](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image11.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image10.png)

**Figure 4**: configurer l’ObjectDataSource pour appeler le `GetProducts()` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image12.png))


Modifier les champs de GridView supprimer tout sauf la `ProductName` et `UnitPrice` BoundFields. En outre, n’hésitez pas à personnaliser ces BoundFields en fonction des besoins, telles que la mise en forme le `UnitPrice` BoundField sous forme de devise et en modifiant le `HeaderText` propriétés de la BoundFields. Ces étapes peuvent être accomplies de sous forme de graphique, en cliquant sur le lien Modifier les colonnes à partir de la balise active du GridView, soit en configurant manuellement la syntaxe déclarative.


[![Supprimez tous sauf le ProductName et UnitPrice BoundFields](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image14.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image13.png)

**Figure 5**: supprimez tout sauf la `ProductName` et `UnitPrice` BoundFields ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image15.png))


Le balisage final pour le contrôle GridView est la suivante :


[!code-aspx[Main](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/samples/sample1.aspx)]

Ensuite, nous avons besoin marquer le contrôle GridView comme sélectionnables, qui ajoute un bouton Sélectionner pour chaque ligne. Pour ce faire, cochez simplement la case à cocher Activer la sélection dans la balise active du GridView.


[![Rendre les lignes du GridView sélectionnables](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image17.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image16.png)

**Figure 6**: rendre sélectionnables de lignes du GridView ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image18.png))


L’option de sélection de l’activation de la vérification de l’ajoute un CommandField à la `ProductsGrid` GridView avec son `ShowSelectButton` propriété la valeur True. Cela entraîne un bouton Sélectionner pour chaque ligne du contrôle GridView, comme le montre la Figure 6. Par défaut, les boutons de sélection sont rendus sous la forme de LinkButton, mais vous pouvez utiliser à la place des boutons ou ImageButtons par le biais de la CommandField `ButtonType` propriété.


[!code-aspx[Main](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/samples/sample2.aspx)]

Lorsque vous cliquez sur le bouton de sélection d’une ligne GridView une publication (postback) s’ensuit et du GridView `SelectedRow` propriété est mise à jour. Outre la `SelectedRow` propriété, le contrôle GridView fournit le [SelectedIndex](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedindex%28VS.80%29.aspx), [SelectedValue](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedvalue%28VS.80%29.aspx), et [SelectedDataKey](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selecteddatakey%28VS.80%29.aspx) propriétés. Le `SelectedIndex` propriété retourne l’index de la ligne sélectionnée, alors que le `SelectedValue` et `SelectedDataKey` propriétés retournent des valeurs en fonction du GridView [propriété DataKeyNames](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.datakeynames%28VS.80%29.aspx).

Le `DataKeyNames` propriété est utilisée pour associer un ou plusieurs champs de données, les valeurs de chaque ligne et est couramment utilisé pour l’attribut des informations d’identification de manière unique à partir des données sous-jacentes avec chaque ligne GridView. Le `SelectedValue` propriété retourne la valeur de la première `DataKeyNames` champ de données pour la ligne sélectionnée alors que le `SelectedDataKey` propriété retourne la ligne sélectionnée `DataKey` objet qui contient toutes les valeurs pour les champs clés de données spécifié pour Cette ligne.

Le `DataKeyNames` est automatiquement définie sur le champ de données en identifiant de manière unique lorsque vous liez une source de données à un contrôle GridView, DetailsView ou FormView via le concepteur. Lorsque cette propriété a été définie pour nous automatiquement dans les didacticiels précédents, les exemples aurait fonctionné sans le `DataKeyNames` propriété spécifiée. Toutefois, pour le GridView sélectionnable dans ce didacticiel, ainsi que pour les futures didacticiels dans lequel nous allons examiner insertion, de mise à jour et de suppression, le `DataKeyNames` propriété doit être définie correctement. Prenez un moment pour vous assurer que votre contrôle de GridView `DataKeyNames` est définie sur `ProductID`.

Examinons notre progression jusqu’ici via un navigateur. Notez que le contrôle GridView répertorie le nom et le prix de tous les produits avec un LinkButton sélectionnez. En cliquant sur le bouton de sélection provoque une publication (postback). À l’étape 2, nous verrons comment avoir un contrôle DetailsView en réponse à cette publication en affichant les détails pour le produit sélectionné.


[![Chaque ligne de produit contient un LinkButton Select](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image20.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image19.png)

**Figure 7**: chaque ligne de produit contient un LinkButton sélectionnez ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image21.png))


### <a name="highlighting-the-selected-row"></a>Mise en surbrillance de la ligne sélectionnée

Le `ProductsGrid` GridView a un `SelectedRowStyle` propriété qui peut être utilisée pour dicter le style visuel pour la ligne sélectionnée. Utilisée correctement, cela peut améliorer l’expérience utilisateur en plus clairement indiquant quelle ligne du contrôle GridView est actuellement sélectionné. Pour ce didacticiel, nous allons ont la ligne sélectionnée mis en surbrillance sur fond jaune.

Comme avec nos didacticiels antérieure, nous allons vous efforcer de conserver les paramètres liés à esthétiques définis en tant que classes CSS. Par conséquent, créez une classe CSS dans `Styles.css` nommé `SelectedRowStyle`.


[!code-css[Main](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/samples/sample3.css)]

Pour appliquer cette classe CSS pour les `SelectedRowStyle` propriété de *tous les* contrôles GridView dans notre série de didacticiels, modifier le `GridView.skin` d’apparence dans le `DataWebControls` thème à inclure le `SelectedRowStyle` paramètres comme indiqué ci-dessous :


[!code-aspx[Main](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/samples/sample4.aspx)]

Avec cet ajout, la ligne sélectionnée de GridView est maintenant mis en surbrillance avec une couleur d’arrière-plan jaune.


[![Personnaliser l’apparence de la ligne sélectionnée à l’aide de la propriété de SelectedRowStyle de GridView](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image23.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image22.png)

**Figure 8**: personnaliser une apparence d’à l’aide la ligne sélectionnée de GridView `SelectedRowStyle` propriété ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image24.png))


## <a name="step-2-displaying-the-selected-products-details-in-a-detailsview"></a>Étape 2 : Afficher les détails du produit sélectionné dans un contrôle DetailsView

Avec la `ProductsGrid` terminer GridView, tout ce qui reste consiste à ajouter un contrôle DetailsView qui affiche des informations sur le produit sélectionné. Ajouter un contrôle DetailsView ci-dessus le GridView et créer un nouveau ObjectDataSource nommé `ProductDetailsDataSource`. Étant donné que nous voulons que ce contrôle DetailsView pour afficher des informations spécifiques sur le produit sélectionné, configurer le `ProductDetailsDataSource` à utiliser le `ProductsBLL` la classe `GetProductByProductID(productID)` (méthode).


[![GetProductByProductID(productID) méthode la classe ProductsBLL Invoke](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image26.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image25.png)

**Figure 9**: appeler le `ProductsBLL` de classe `GetProductByProductID(productID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image27.png))


Avoir le  *`productID`*  valeur du paramètre obtenue à partir du contrôle GridView `SelectedValue` propriété. Comme indiqué précédemment, le contrôle du GridView `SelectedValue` propriété retourne la valeur de la ligne sélectionnée de la clé les premières données. Par conséquent, il est impératif que du GridView `DataKeyNames` est définie sur `ProductID`, de sorte que la ligne sélectionnée `ProductID` valeur est retournée par `SelectedValue`.


[![Définir le paramètre productID à la propriété SelectedValue de GridView](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image29.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image28.png)

**La figure 10**: définir le  *`productID`*  paramètre du GridView `SelectedValue` propriété ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image30.png))


Une fois la `productDetailsDataSource` ObjectDataSource a été correctement configuré et lié au contrôle DetailsView, ce didacticiel est terminé ! Lorsque la page est visitée en premier, aucune ligne n’est sélectionnée, donc du GridView `SelectedValue` propriété renvoie `null`. Dans la mesure où aucun produit avec un `NULL` `ProductID` valeur, aucun enregistrement n’est retournés par la `GetProductByProductID(productID)` méthode, ce qui signifie que le contrôle DetailsView n’est pas affiché (voir Figure 11). Après avoir cliqué sur le bouton de sélection d’une ligne GridView, une publication (postback) s’ensuit et le contrôle DetailsView est actualisé. Cette fois de GridView `SelectedValue` propriété retourne le `ProductID` de la ligne sélectionnée, le `GetProductByProductID(productID)` méthode retourne un `ProductsDataTable` avec des informations sur ce produit et le contrôle DetailsView montre ces détails (voir Figure 12).


[![Lorsque visité première, mais uniquement le contrôle GridView est affiché](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image32.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image31.png)

**Figure 11**: lors de la première visite, uniquement le contrôle GridView est affiché ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image33.png))


[![Lors de la sélection d’une ligne, les détails du produit sont affichés](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image35.png)](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image34.png)

**Figure 12**: lors de la sélection d’une ligne, les détails du produit sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs/_static/image36.png))


## <a name="summary"></a>Résumé

Dans cette section et les didacticiels de trois précédents, nous avons vu un certain nombre de techniques pour l’affichage de rapports maître/détail. Dans ce didacticiel, que nous avons examiné l’aide d’un GridView sélectionnable pour héberger les enregistrements maîtres et un contrôle DetailsView pour afficher des détails sur l’enregistrement principal sélectionné sur la même page. Les didacticiels précédentes, nous avons étudié comment afficher les rapports maître/détails à l’aide de la compréhension des listes et l’affichage des enregistrements principaux sur une page web et les enregistrements de détail sur une autre.

Ce didacticiel conclut notre examen des rapports maître/détail. En commençant par le tutorialwe suivant commence notre exploration de la mise en forme personnalisée avec le GridView, DetailsView et FormView. Nous verrons comment personnaliser l’apparence de ces contrôles basés sur les données liées à leur synthétiser les données dans le pied de page du GridView et comment utiliser des modèles pour obtenir un degré plus élevé de contrôle sur la disposition.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-filtering-across-two-pages-cs.md)
[Suivant](master-detail-filtering-with-a-dropdownlist-vb.md)
