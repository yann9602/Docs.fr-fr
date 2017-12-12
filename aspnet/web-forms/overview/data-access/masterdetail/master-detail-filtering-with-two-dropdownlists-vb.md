---
uid: web-forms/overview/data-access/masterdetail/master-detail-filtering-with-two-dropdownlists-vb
title: "Le filtrage avec compréhension (VB) des deux listes maître/détail | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel développe la relation maître/détail pour ajouter une couche de tiers, à l’aide de deux contrôles DropDownList pour sélectionner le recor parent et grand-parent souhaité..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 11ae4f64-01ba-4823-95f4-a2fe1f84f7d7
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/masterdetail/master-detail-filtering-with-two-dropdownlists-vb
msc.type: authoredcontent
ms.openlocfilehash: c345fbfe5df4d8ce06695c4dd4b88cc099ad7836
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-filtering-with-two-dropdownlists-vb"></a>Le filtrage avec compréhension (VB) des deux listes maître/détail
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/5/d/7/5d7571fc-d0b7-4798-ad4a-c976c02363ce/ASPNET_Data_Tutorial_8_VB.exe) ou [télécharger le PDF](master-detail-filtering-with-two-dropdownlists-vb/_static/datatutorial08vb1.pdf)

> Ce didacticiel développe la relation maître/détail pour ajouter une couche de tiers, à l’aide de deux contrôles DropDownList pour sélectionner les enregistrements parents et grand-parent souhaités.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](master-detail-filtering-with-a-dropdownlist-vb.md) nous avons examiné comment afficher un rapport simple maître/détails à l’aide d’un seul DropDownList rempli avec les catégories et un GridView affichant les produits qui appartiennent à la catégorie sélectionnée. Ce modèle de rapport fonctionne bien lorsque l’affichage des enregistrements qui ont une relation un-à-plusieurs et peuvent facilement être étendus pour fonctionner pour les scénarios incluant plusieurs relations un-à-plusieurs. Par exemple, un système d’entrée de commande aurait des tables qui correspondent aux clients, des commandes et des éléments de ligne de commande. Un client donné peut avoir plusieurs commandes avec chaque commande composée de plusieurs éléments. Ces données peuvent être présentées à l’utilisateur avec un GridView et de compréhension des deux listes. La première DropDownList aurait un élément de liste pour chaque client dans la base de données et de la seconde d’un autre contenu en cours de commandes passées par le client sélectionné. Un GridView répertorierait les articles de la commande sélectionnée.

Si la base de données Northwind inclut les informations détaillées de client/commande canonique son `Customers`, `Orders`, et `Order Details` aux tables, ces tables ne sont pas capturées dans notre architecture. Néanmoins, nous pouvons toujours illustrent l’utilisation deux compréhension des listes dépendants. La première DropDownList répertorie les catégories et la seconde les produits appartenant à la catégorie sélectionnée. Un DetailsView répertorie ensuite les détails du produit sélectionné.

## <a name="step-1-creating-and-populating-the-categories-dropdownlist"></a>Étape 1 : Création et remplissage de catégories DropDownList

Notre objectif premier consiste à ajouter les DropDownList qui répertorie les catégories. Ces étapes ont été examinés en détail dans le didacticiel précédent, mais sont résumées ici par souci d’exhaustivité.

Ouvrir le `MasterDetailsDetails.aspx` page dans le `Filtering` dossier, ajoutez une liste déroulante à la page, définissez son `ID` propriété `Categories`, puis cliquez sur le lien configurer la Source de données dans sa balise active. À partir de l’Assistant Configuration de Source de données choisir d’ajouter une nouvelle source de données.


[![Ajouter une nouvelle Source de données pour l’objet DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image2.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image1.png)

**Figure 1**: ajouter une nouvelle Source de données pour l’objet DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image3.png))


La nouvelle source de données doit être naturellement, un ObjectDataSource. Nommez cette nouvelle ObjectDataSource `CategoriesDataSource` et appeler le `CategoriesBLL` l’objet `GetCategories()` (méthode).


[![Choisissez d’utiliser la classe CategoriesBLL](master-detail-filtering-with-two-dropdownlists-vb/_static/image5.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image4.png)

**Figure 2**: choisissez d’utiliser le `CategoriesBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image6.png))


[![Configurer pour utiliser la méthode GetCategories() ObjectDataSource](master-detail-filtering-with-two-dropdownlists-vb/_static/image8.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image7.png)

**Figure 3**: configurer l’ObjectDataSource à utiliser le `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image9.png))


Après avoir configuré l’ObjectDataSource, nous devons encore spécifier le champ de source de données doit être affiché dans le `Categories` DropDownList et celui qui doit être configuré en tant que la valeur de l’élément de liste. Définir le `CategoryName` champ en tant que l’affichage et `CategoryID` comme valeur pour chaque élément de liste.


[![A l’affichage DropDownList le champ nom de catégorie et utilisez CategoryID comme valeur](master-detail-filtering-with-two-dropdownlists-vb/_static/image11.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image10.png)

**Figure 4**: affiche la DropDownList le `CategoryName` champ et utilisez `CategoryID` comme valeur ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image12.png))


À ce stade, nous avons un contrôle DropDownList (`Categories`) qui est remplie avec les enregistrements à partir de la `Categories` table. Lorsque l’utilisateur choisit une nouvelle catégorie dans la liste déroulante, nous devrons une publication afin d’actualiser le produit DropDownList que nous allons créer à l’étape 2. Par conséquent, vérifiez l’option Activer AutoPostBack à partir de la `categories` la balise active de DropDownList.


[![Activer AutoPostBack pour DropDownList catégories](master-detail-filtering-with-two-dropdownlists-vb/_static/image14.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image13.png)

**Figure 5**: activer les AutoPostBack pour le `Categories` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image15.png))


## <a name="step-2-displaying-the-selected-categorys-products-in-a-second-dropdownlist"></a>Étape 2 : Affichage des produits de la catégorie sélectionnée dans un deuxième DropDownList

Avec la `Categories` DropDownList terminée, l’étape suivante consiste à afficher une liste déroulante des produits appartenant à la catégorie sélectionnée. Pour ce faire, ajoutez un autre DropDownList à la page nommée `ProductsByCategory`. Comme avec la `Categories` DropDownList, créez un nouveau ObjectDataSource pour le `ProductsByCategory` DropDownList nommé `ProductsByCategoryDataSource`.


[![Ajouter une nouvelle Source de données pour ProductsByCategory DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image17.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image16.png)

**Figure 6**: ajouter une nouvelle Source de données pour le `ProductsByCategory` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image18.png))


[![Créer un nouveau ObjectDataSource nommé ProductsByCategoryDataSource](master-detail-filtering-with-two-dropdownlists-vb/_static/image20.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image19.png)

**Figure 7**: créer un nouveau nommé de ObjectDataSource `ProductsByCategoryDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image21.png))


Étant donné que la `ProductsByCategory` besoins DropDownList pour afficher uniquement les produits appartenant à la catégorie sélectionnée que ObjectDataSource appelle la `GetProductsByCategoryID(categoryID)` méthode à partir de la `ProductsBLL` objet.


[![Choisissez d’utiliser la classe ProductsBLL](master-detail-filtering-with-two-dropdownlists-vb/_static/image23.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image22.png)

**Figure 8**: choisissez d’utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image24.png))


[![Configurer pour utiliser la méthode GetProductsByCategoryID(categoryID) ObjectDataSource](master-detail-filtering-with-two-dropdownlists-vb/_static/image26.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image25.png)

**Figure 9**: configurer l’ObjectDataSource à utiliser le `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image27.png))


Dans la dernière étape de l’Assistant, nous devons spécifier la valeur de la  *`categoryID`*  paramètre. Attribuer ce paramètre à l’élément sélectionné de la `Categories` DropDownList.


[![Extraire la valeur du paramètre categoryID dans la liste déroulante de catégories](master-detail-filtering-with-two-dropdownlists-vb/_static/image29.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image28.png)

**La figure 10**: extraire le  *`categoryID`*  valeur du paramètre de la `Categories` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image30.png))


Avec ObjectDataSource configuré, il reste que pour spécifier les champs de source de données sont utilisées pour l’affichage et la valeur d’éléments de DropDownList. Afficher le `ProductName` champ et utilisez le `ProductID` champ comme valeur.


[![Spécifiez les champs de Source de données utilisées pour le texte et les propriétés de la valeur des ListItems de DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image32.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image31.png)

**Figure 11**: spécifiez les champs de Source de données utilisée pour de DropDownList `ListItem` s' `Text` et `Value` propriétés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image33.png))


Avec ObjectDataSource et `ProductsByCategory` DropDownList configuré notre page affichera la compréhension des deux listes : la première va répertorier toutes les catégories tandis que la deuxième répertorie les produits appartenant à la catégorie sélectionnée. Lorsque l’utilisateur sélectionne une nouvelle catégorie dans la liste déroulante première, résulte d’une publication (postback) et le deuxième DropDownList sera reliée, affichant les produits qui appartiennent à la catégorie qui vient d’être sélectionnée. Figures 12 et 13 afficher `MasterDetailsDetails.aspx` en action lors de l’affichage via un navigateur.


[![Lors de la première visite de la Page, la catégorie boissons est sélectionnée.](master-detail-filtering-with-two-dropdownlists-vb/_static/image35.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image34.png)

**Figure 12**: lors de la première visite de la Page, la catégorie boissons est sélectionnée ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image36.png))


[![Choix d’une autre catégorie affiche produits de la nouvelle catégorie](master-detail-filtering-with-two-dropdownlists-vb/_static/image38.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image37.png)

**Figure 13**: choix d’un autre catégorie s’affiche produits de la nouvelle catégorie ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image39.png))


Actuellement le `productsByCategory` DropDownList, en cas de modification, est *pas* provoquer une publication (postback). Toutefois, nous voudrons une publication (postback) se produit une fois que nous avons ajouté un contrôle DetailsView pour afficher les détails du produit sélectionné (étape 3). Par conséquent, vérifiez la case à cocher Activer AutoPostBack à partir de la `productsByCategory` la balise active de DropDownList.


[![Activer la fonctionnalité de AutoPostBack pour le productsByCategory DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image41.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image40.png)

**La figure 14**: activer la fonctionnalité AutoPostBack pour le `productsByCategory` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image42.png))


## <a name="step-3-using-a-detailsview-to-display-details-for-the-selected-product"></a>Étape 3 : Utiliser un contrôle DetailsView pour afficher les détails pour le produit sélectionné

L’étape finale est pour afficher les détails pour le produit sélectionné dans un contrôle DetailsView. Pour ce faire, d’ajouter un contrôle DetailsView à la page, définissez son `ID` propriété `ProductDetails`et créer un nouveau ObjectDataSource pour celui-ci. Configurer cette ObjectDataSource à extraire ses données à partir de la `ProductsBLL` de classe `GetProductByProductID(productID)` méthode à l’aide de la valeur sélectionnée de la `ProductsByCategory` DropDownList pour la valeur de la  *`productID`*  paramètre.


[![Choisissez d’utiliser la classe ProductsBLL](master-detail-filtering-with-two-dropdownlists-vb/_static/image44.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image43.png)

**Figure 15**: choisissez d’utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image45.png))


[![Configurer pour utiliser la méthode GetProductByProductID(productID) ObjectDataSource](master-detail-filtering-with-two-dropdownlists-vb/_static/image47.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image46.png)

**Figure 16**: configurer l’ObjectDataSource à utiliser le `GetProductByProductID(productID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image48.png))


[![Extraire la valeur du paramètre productID ProductsByCategory DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image50.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image49.png)

**Figure 17**: extraire le  *`productID`*  valeur du paramètre de la `ProductsByCategory` DropDownList ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image51.png))


Vous pouvez choisir d’afficher tous les champs disponibles dans le `ProductDetails` DetailsView. J’ai choisi de supprimer le `ProductID`, `SupplierID`, et `CategoryID` champs et réorganisées et mis en forme les champs restants. En outre, vous nettoyez du DetailsView `Height` et `Width` propriétés, ce qui permet le contrôle DetailsView étendre à la largeur nécessaire pour un meilleur affichage ses données au lieu d’utiliser il limité à une taille spécifiée. Le balisage complet apparaît ci-dessous :


[!code-aspx[Main](master-detail-filtering-with-two-dropdownlists-vb/samples/sample1.aspx)]

Prenez un moment pour essayer la `MasterDetailsDetails.aspx` page dans un navigateur. À première vue, il peut sembler que tout fonctionne comme vous le souhaitez, mais il existe un problème subtil. Lorsque vous choisissez une nouvelle catégorie de la `ProductsByCategory` DropDownList est mis à jour pour inclure les produits de la catégorie sélectionnée, mais la `ProductDetails` DetailsView a continué à afficher les informations de produit précédentes. Le contrôle DetailsView est mis à jour lorsque vous choisissez un autre produit de la catégorie sélectionnée. En outre, si vous testez minutieusement suffisamment, vous constatez que si vous choisissez en permanence de nouvelles catégories (tel que le choix des boissons à partir de la `Categories` DropDownList, puis Condiments, friandises puis) chaque sélection de la catégorie autres provoque la `ProductDetails`DetailsView à actualiser.

Pour aider à concrétiser la ce problème, examinons un exemple spécifique. Lorsque vous visitez tout d’abord la page de la catégorie boissons est sélectionnée et les produits connexes sont chargés dans le `ProductsByCategory` DropDownList. Tran est le produit sélectionné et ses détails sont affichent dans le `ProductDetails` DetailsView, comme indiqué dans la Figure 18.


[![Détails du produit sélectionné sont affichés dans un contrôle DetailsView](master-detail-filtering-with-two-dropdownlists-vb/_static/image53.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image52.png)

**La figure 18**: détails du sélectionné du produit sont affichés dans un contrôle DetailsView ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image54.png))


Si vous modifiez la sélection de catégorie à partir de boissons pour Condiments, une publication (postback) se produit et le `ProductsByCategory` DropDownList est mis à jour en conséquence, mais le contrôle DetailsView affiche toujours les détails de Tran.


[![Détails de précédemment sélectionné du produit sont toujours affichées](master-detail-filtering-with-two-dropdownlists-vb/_static/image56.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image55.png)

**Figure 19**: détails du produit sélectionné précédemment le sont toujours affichées ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image57.png))


Sélection d’un nouveau produit dans la liste d’actualise le contrôle DetailsView comme prévu. Si vous sélectionnez une nouvelle catégorie après avoir modifié le produit, le contrôle DetailsView ne sera pas actualiser à nouveau. Toutefois, si au lieu de choisir un nouveau produit, vous avez sélectionné une nouvelle catégorie, le contrôle DetailsView serait Actualiser. Dans le monde s’ici ?

Il s’agit d’un problème de synchronisation dans le cycle de vie de la page. Chaque fois qu’une page est demandée qu'elle parcoure un nombre d’étapes en tant que son rendu. Dans un de ces étapes ObjectDataSource contrôle vérifie si un de leurs `SelectParameters` valeurs ont changé. Si, par conséquent, le contrôle Web de données lié à ObjectDataSource sait qu’il doit actualiser son affichage. Par exemple, lorsqu’une nouvelle catégorie est sélectionnée, le `ProductsByCategoryDataSource` ObjectDataSource détecte une modification des valeurs de ses paramètres et le `ProductsByCategory` DropDownList lui-même, relie les produits pour la catégorie sélectionnée.

Le problème qui survient dans cette situation est que le point dans le cycle de vie de page qui les ObjectDataSources vérifier les paramètres modifiés se produit *avant* la nouvelle liaison de données associé à des contrôles Web. Par conséquent, lorsque vous sélectionnez une nouvelle catégorie la `ProductsByCategoryDataSource` ObjectDataSource détecte une modification de sa valeur de paramètre. ObjectDataSource utilisé par le `ProductDetails` DetailsView, toutefois, ne notez de telles modifications, car le `ProductsByCategory` DropDownList doit être reliée. Plus loin dans le cycle de vie du `ProductsByCategory` DropDownList relie à son ObjectDataSource, en saisissant les produits de la catégorie qui vient d’être sélectionnée. Alors que le `ProductsByCategory` les valeur de DropDownList a changé, le `ProductDetails` ObjectDataSource du contrôle DetailsView a déjà effectué son contrôle de valeur de paramètre ; par conséquent, le contrôle DetailsView affiche ses résultats précédents. Cette interaction est représentée dans la Figure 20.


[![La valeur ProductsByCategory DropDownList est modifiée une fois ObjectDataSource du ProductDetails DetailsView vérifie les modifications](master-detail-filtering-with-two-dropdownlists-vb/_static/image59.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image58.png)

**Figure 20**: le `ProductsByCategory` DropDownList valeur modifications après la `ProductDetails` ObjectDataSource vérifie du contrôle DetailsView les modifications apportées ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image60.png))


Pour remédier à cela nous devez relier explicitement le `ProductDetails` DetailsView après le `ProductsByCategory` DropDownList a été lié. Pour ce faire, nous pouvons appeler le `ProductDetails` du DetailsView `DataBind()` (méthode) lorsque le `ProductsByCategory` de DropDownList `DataBound` se déclenche des événements. Ajouter le code de gestionnaire d’événements suivant au `MasterDetailsDetails.aspx` classe code-behind de la page (reportez-vous à la «[définition par programme des valeurs de paramètres de l’ObjectDataSource](../basic-reporting/programmatically-setting-the-objectdatasource-s-parameter-values-cs.md)» pour en savoir plus sur l’ajout d’un gestionnaire d’événements) :


[!code-vb[Main](master-detail-filtering-with-two-dropdownlists-vb/samples/sample2.vb)]

Après cet appel explicit à la `ProductDetails` du DetailsView `DataBind()` méthode a été ajoutée, le didacticiel fonctionne comme prévu. Met en évidence figure 21 comment cela modifiée de remédier à notre problème antérieur.


[![ProductDetails DetailsView est lié aux données d’événement se déclenche d’explicitement actualisé lorsque le ProductsByCategory DropDownList](master-detail-filtering-with-two-dropdownlists-vb/_static/image62.png)](master-detail-filtering-with-two-dropdownlists-vb/_static/image61.png)

**Figure 21**: le `ProductDetails` DetailsView est explicitement actualisé lors de la `ProductsByCategory` de DropDownList `DataBound` se déclenche des événements ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-with-two-dropdownlists-vb/_static/image63.png))


## <a name="summary"></a>Résumé

Le DropDownList constitue un élément d’interface utilisateur idéal pour les rapports maître/détail s’il existe une relation un-à-plusieurs entre les enregistrements maître / détail. Dans le didacticiel précédent, nous avons vu comment utiliser un seul DropDownList pour filtrer les produits affichés par la catégorie sélectionnée. Dans ce didacticiel, nous remplacé le GridView de produits avec une liste déroulante et un contrôle DetailsView permet d’afficher les détails du produit sélectionné. Les concepts abordés dans ce didacticiel peuvent facilement être étendus aux modèles de données impliquant plusieurs relations un-à-plusieurs, telles que les clients, les commandes et les éléments de la commande. En règle générale, vous pouvez toujours ajouter un contrôle DropDownList pour chacune des entités de « un » dans les relations un-à-plusieurs.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-filtering-with-a-dropdownlist-vb.md)
[Suivant](master-detail-filtering-across-two-pages-vb.md)
