---
uid: web-forms/overview/data-access/masterdetail/master-detail-filtering-across-two-pages-cs
title: "Le filtrage sur deux Pages (c#) maître/détail | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons implémenter ce modèle à l’aide d’un GridView pour répertorier les fournisseurs dans la base de données. Chaque ligne du fournisseur dans le GridView contiendra une vue en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 552d2d50-fe73-4153-9a7f-2b379bec4625
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/masterdetail/master-detail-filtering-across-two-pages-cs
msc.type: authoredcontent
ms.openlocfilehash: 3411272896dee0da4d5f89aa2bdda0999d660423
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="masterdetail-filtering-across-two-pages-c"></a>Maître/détail, le filtrage entre les deux Pages (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/6/3/463cf87c-4724-4cbc-b7b5-3f866f43ba50/ASPNET_Data_Tutorial_9_CS.exe) ou [télécharger le PDF](master-detail-filtering-across-two-pages-cs/_static/datatutorial09cs1.pdf)

> Dans ce didacticiel, nous allons implémenter ce modèle à l’aide d’un GridView pour répertorier les fournisseurs dans la base de données. Chaque ligne du fournisseur dans le GridView contiendra un lien Afficher les produits que, lorsque vous cliquez sur, dirige l’utilisateur vers une page distincte qui répertorie les produits pour le fournisseur sélectionné.


## <a name="introduction"></a>Introduction

Dans les deux didacticiels précédents, nous avons vu comment [afficher les rapports maître/détail dans une page web à l’aide de la compréhension des listes](master-detail-filtering-with-a-dropdownlist-cs.md) à [afficher les enregistrements « maîtres » et un contrôle GridView ou DetailsView](master-detail-filtering-with-two-dropdownlists-cs.md) pour afficher la » plus d’informations. » Un autre modèle commun utilisé pour les rapports maître/détails doit avoir les enregistrements principaux sur une page web et les détails affichés sur un autre. Un site Web forum, comme [les Forums ASP.NET](https://forums.asp.net/), est un parfait exemple de ce modèle dans la pratique. Les Forums ASP.NET sont composés d’une variété de prise en main, forums Web Forms, les contrôles de présentation de données et ainsi de suite. Chaque forum est composé de plusieurs threads et chaque thread est composé d’un nombre de publications. Sur la page d’accueil des Forums ASP.NET, les forums sont répertoriés. En cliquant sur un forum vous emmène à un `ShowForum.aspx` page qui répertorie tous les threads du forum. De même, en cliquant sur un thread permet d’accéder à `ShowPost.aspx`, qui affiche les publications pour le thread qui l’utilisateur a cliqué.

Dans ce didacticiel, nous allons implémenter ce modèle à l’aide d’un GridView pour répertorier les fournisseurs dans la base de données. Chaque ligne du fournisseur dans le GridView contiendra un lien Afficher les produits que, lorsque vous cliquez sur, dirige l’utilisateur vers une page distincte qui répertorie les produits pour le fournisseur sélectionné.

## <a name="step-1-addingsupplierlistmasteraspxandproductsforsupplierdetailsaspxpages-to-thefilteringfolder"></a>Étape 1 : Ajout de`SupplierListMaster.aspx`et`ProductsForSupplierDetails.aspx`des Pages à la`Filtering`dossier

Lors de la définition de la mise en page dans le troisième didacticiel nous avons ajouté un nombre de pages « starter » dans le `BasicReporting`, `Filtering`, et `CustomFormatting` dossiers. Toutefois, nous n’avez pas ajouté une page d’accueil pour ce didacticiel à ce stade, par conséquent, prenez un moment pour ajouter deux nouvelles pages à la `Filtering` dossier : `SupplierListMaster.aspx` et `ProductsForSupplierDetails.aspx`. `SupplierListMaster.aspx`Répertorie les enregistrements « maîtres » (fournisseurs) lors de la `ProductsForSupplierDetails.aspx` affichera les produits pour le fournisseur sélectionné.

Lorsque la création de ces deux nouvelles pages veillez à les associer avec le `Site.master` page maître.


![Ajouter les Pages de ProductsForSupplierDetails.aspx SupplierListMaster.aspx dans le dossier de filtrage](master-detail-filtering-across-two-pages-cs/_static/image1.png)

**Figure 1**: ajouter la `SupplierListMaster.aspx` et `ProductsForSupplierDetails.aspx` des Pages à la `Filtering` dossier


En outre, lorsque vous ajoutez de nouvelles pages au projet, veillez à mettre à jour le fichier de mappage de site, `Web.sitemap`, en conséquence. Pour ce didacticiel simplement ajouter la `SupplierListMaster.aspx` page pour le plan de site à l’aide du contenu XML suivant en tant qu’enfant de rapports de filtrage `<siteMapNode>` élément :


[!code-xml[Main](master-detail-filtering-across-two-pages-cs/samples/sample1.xml)]

> [!NOTE]
> Vous pouvez aider à automatiser le processus de mise à jour le fichier de mappage de site lors de l’ajout de nouveaux ASP.NET à l’aide des pages [K. Scott Allen](http://odetocode.com/Blogs/scott/)de libérer de Visual Studio [Macro de mappage de Site](http://odetocode.com/Blogs/scott/archive/2005/11/29/2537.aspx).


## <a name="step-2-displaying-the-supplier-list-insupplierlistmasteraspx"></a>Étape 2 : Afficher la liste des fournisseurs dans`SupplierListMaster.aspx`

Avec la `SupplierListMaster.aspx` et `ProductsForSupplierDetails.aspx` pages créées, l’étape suivante consiste à créer le contrôle GridView des fournisseurs dans `SupplierListMaster.aspx`. Ajouter un contrôle GridView à la page et la lier à un nouveau ObjectDataSource. Cette ObjectDataSource doit utiliser le `SuppliersBLL` de classe `GetSuppliers()` méthode pour retourner tous les fournisseurs.


[![Sélectionnez la classe SuppliersBLL](master-detail-filtering-across-two-pages-cs/_static/image3.png)](master-detail-filtering-across-two-pages-cs/_static/image2.png)

**Figure 2**: sélectionnez le `SuppliersBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image4.png))


[![Configurer pour utiliser la méthode GetSuppliers() ObjectDataSource](master-detail-filtering-across-two-pages-cs/_static/image6.png)](master-detail-filtering-across-two-pages-cs/_static/image5.png)

**Figure 3**: configurer l’ObjectDataSource à utiliser le `GetSuppliers()` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image7.png))


Nous devons inclure un lien intitulé d’afficher les produits dans chaque ligne GridView qui, lorsque vous cliquez dessus, dirige l’utilisateur vers `ProductsForSupplierDetails.aspx` en passant de la ligne sélectionnée `SupplierID` valeur via la chaîne de requête. Par exemple, si l’utilisateur clique sur le lien Afficher les produits pour le fournisseur de Tokyo Traders (qui a un `SupplierID` valeur 4), ils doivent être envoyés à `ProductsForSupplierDetails.aspx?SupplierID=4`.

Pour ce faire, ajoutez un [HyperLinkField](https://msdn.microsoft.com/library/system.web.ui.webcontrols.hyperlinkfield.aspx) au GridView, qui ajoute un lien hypertexte à chaque ligne GridView. Démarrez en cliquant sur le lien Modifier les colonnes à partir de la balise active du GridView. Ensuite, sélectionnez le HyperLinkField dans la liste en haut à gauche et cliquez sur Ajouter pour inclure la HyperLinkField dans la liste de champs de GridView.


[![Ajouter un HyperLinkField au contrôle GridView](master-detail-filtering-across-two-pages-cs/_static/image9.png)](master-detail-filtering-across-two-pages-cs/_static/image8.png)

**Figure 4**: ajouter un HyperLinkField au GridView ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image10.png))


Le HyperLinkField peut être configuré pour utiliser le même texte ou URL du lien dans chaque ligne GridView des valeurs ou peut ces valeurs sur les valeurs des données liées à chaque ligne particulière. Pour spécifier un mappage statique valeur toutes les lignes utilisent de la HyperLinkField `Text` ou `NavigateUrl` propriétés. Étant donné que nous voulons que le texte du lien vers les mêmes pour toutes les lignes, défini de la HyperLinkField `Text` propriété pour afficher les produits.


[![Définir la propriété de texte de la HyperLinkField pour afficher les produits](master-detail-filtering-across-two-pages-cs/_static/image12.png)](master-detail-filtering-across-two-pages-cs/_static/image11.png)

**Figure 5**: définir le HyperLinkField `Text` propriété pour afficher les produits ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image13.png))


Pour définir le texte ou les valeurs de l’URL doit être basé sur les données sous-jacentes liées à la ligne GridView, spécifiez le texte des champs de données ou de valeurs de l’URL doivent être extraite en le `DataTextField` ou `DataNavigateUrlFields` propriétés. `DataTextField`peut uniquement être définie sur un champ de données ; `DataNavigateUrlFields`, toutefois, peut être définie à une liste délimitée par des virgules des champs de données. Nous devons fréquemment le texte ou l’URL d’une combinaison de la valeur de champ de données de la ligne active et certaines balises statique de base. Dans ce didacticiel, par exemple, nous souhaitons l’URL de liens de la HyperLinkField être `ProductsForSupplierDetails.aspx?SupplierID=supplierID`, où  *`supplierID`*  de ligne du contrôle GridView `SupplierID` valeur. Notez que nous devons statiques et pilotés par les données les valeurs ici : le `ProductsForSupplierDetails.aspx?SupplierID=` partie de l’URL du lien est statique, tandis que la  *`supplierID`*  partie est orientées données car sa valeur est de chaque ligne propre `SupplierID` valeur.

Pour indiquer une combinaison de valeurs statiques et pilotés par les données, utilisez la `DataTextFormatString` et `DataNavigateUrlFormatString` propriétés. Dans ces propriétés, entrez le balisage statique en fonction des besoins, puis utilisez le marqueur `{0}` où vous souhaitez que la valeur du champ spécifié dans le `DataTextField` ou `DataNavigateUrlFields` propriétés à afficher. Si le `DataNavigateUrlFields` propriété possède plusieurs utilisation de champs spécifiée `{0}` où vous souhaitez que la première valeur du champ insérée, `{1}` pour la deuxième valeur de champ et ainsi de suite.

Appliquer ceci à notre didacticiel, nous devons définir le `DataNavigateUrlFields` propriété `SupplierID`, car c’est le champ de données dont nous avons besoin de personnaliser sur une ligne par ligne, la valeur et le `DataNavigateUrlFormatString` propriété `ProductsForSupplierDetails.aspx?SupplierID={0}`.


[![Configurer le HyperLinkField pour inclure l’URL du lien approprié en fonction de SupplierID](master-detail-filtering-across-two-pages-cs/_static/image15.png)](master-detail-filtering-across-two-pages-cs/_static/image14.png)

**Figure 6**: configurer le HyperLinkField pour inclure l’approprié lien URL basée sur le `SupplierID` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image16.png))


Après avoir ajouté le HyperLinkField, n’hésitez pas à personnaliser et réorganiser les champs de GridView. La balise suivante montre le contrôle GridView une fois que j’ai apporté des personnalisations au niveau du champ secondaires.


[!code-aspx[Main](master-detail-filtering-across-two-pages-cs/samples/sample2.aspx)]

Prenez un moment pour afficher la `SupplierListMaster.aspx` page via un navigateur. Comme le montre la Figure 7, la page répertorie actuellement tous les fournisseurs, notamment un lien Afficher les produits. En cliquant sur les produits de la vue de lien vous conduira à `ProductsForSupplierDetails.aspx`, en passant le long du fournisseur `SupplierID` dans la chaîne de requête.


[![Chaque ligne du fournisseur contient un lien de produits d’affichage](master-detail-filtering-across-two-pages-cs/_static/image18.png)](master-detail-filtering-across-two-pages-cs/_static/image17.png)

**Figure 7**: chaque ligne du fournisseur contient un lien d’affichage des produits ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image19.png))


## <a name="step-3-listing-the-suppliers-products-inproductsforsupplierdetailsaspx"></a>Étape 3 : Liste des produits du fournisseur dans`ProductsForSupplierDetails.aspx`

À ce stade le `SupplierListMaster.aspx` page envoie aux utilisateurs de `ProductsForSupplierDetails.aspx`, en passant par le fournisseur sélectionné `SupplierID` dans la chaîne de requête. Du didacticiel final consiste à afficher les produits dans un GridView dans `ProductsForSupplierDetails.aspx` dont `SupplierID` est égal à la `SupplierID` passé dans la chaîne de requête. Pour accomplir ce guide de démarrage en ajoutant un contrôle GridView à la `ProductsForSupplierDetails.aspx` page, à l’aide d’un nouveau contrôle ObjectDataSource nommé `ProductsBySupplierDataSource` qui appelle le `GetProductsBySupplierID(supplierID)` méthode à partir de la `ProductsBLL` classe.


[![Ajouter un nouveau ObjectDataSource nommé ProductsBySupplierDataSource](master-detail-filtering-across-two-pages-cs/_static/image21.png)](master-detail-filtering-across-two-pages-cs/_static/image20.png)

**Figure 8**: ajouter un nouveau nommé de ObjectDataSource `ProductsBySupplierDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image22.png))


[![Sélectionnez la classe ProductsBLL](master-detail-filtering-across-two-pages-cs/_static/image24.png)](master-detail-filtering-across-two-pages-cs/_static/image23.png)

**Figure 9**: sélectionnez le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image25.png))


[![Avoir ObjectDataSource à appeler la méthode GetProductsBySupplierID(supplierID)](master-detail-filtering-across-two-pages-cs/_static/image27.png)](master-detail-filtering-across-two-pages-cs/_static/image26.png)

**La figure 10**: le ObjectDataSource appeler le `GetProductsBySupplierID(supplierID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image28.png))


L’étape finale de l’Assistant Configurer la Source de données nous demande de fournir la source de la `GetProductsBySupplierID(supplierID)` la méthode  *`supplierID`*  paramètre. Pour utiliser la valeur de chaîne de requête, définissez la source de paramètre de chaîne de requête et entrez le nom de la valeur de chaîne de requête à utiliser dans la zone de texte QueryStringField (`SupplierID`).


[![Remplir la valeur du paramètre de la valeur de chaîne de requête SupplierID supplierID](master-detail-filtering-across-two-pages-cs/_static/image30.png)](master-detail-filtering-across-two-pages-cs/_static/image29.png)

**Figure 11**: remplir le  *`supplierID`*  valeur du paramètre de la `SupplierID` valeur Querystring ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image31.png))


C’est tout cela ! Figure 12 montre la `ProductsForSupplierDetails.aspx` page lors de navigation en cliquant sur le lien de Tokyo Traders de `SupplierListMaster.aspx`.


[![Les produits fournis par Tokyo Traders sont affichés.](master-detail-filtering-across-two-pages-cs/_static/image33.png)](master-detail-filtering-across-two-pages-cs/_static/image32.png)

**Figure 12**: les produits fournis par Tokyo Traders sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image34.png))


## <a name="displaying-supplier-information-inproductsforsupplierdetailsaspx"></a>Affichage des informations sur le fournisseur dans`ProductsForSupplierDetails.aspx`

Comme le montre la Figure 12, le `ProductsForSupplierDetails.aspx` page répertorie simplement les produits qui sont fournis par le `SupplierID` spécifié dans la chaîne de requête. Une personne envoyées directement à cette page, toutefois, ne saura pas que Figure 12 affiche les produits de Tokyo Traders. Pour résoudre ce problème, nous pouvons afficher des informations sur le fournisseur dans cette page.

Commencez par ajouter un FormView ci-dessus les produits GridView. Créer un contrôle ObjectDataSource nommé `SuppliersDataSource` qui appelle le `SuppliersBLL` la classe `GetSupplierBySupplierID(supplierID)` (méthode).


[![Sélectionnez la classe SuppliersBLL](master-detail-filtering-across-two-pages-cs/_static/image36.png)](master-detail-filtering-across-two-pages-cs/_static/image35.png)

**Figure 13**: sélectionnez le `SuppliersBLL` classe ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image37.png))


[![Avoir ObjectDataSource à appeler la méthode GetSupplierBySupplierID(supplierID)](master-detail-filtering-across-two-pages-cs/_static/image39.png)](master-detail-filtering-across-two-pages-cs/_static/image38.png)

**La figure 14**: le ObjectDataSource appeler le `GetSupplierBySupplierID(supplierID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image40.png))


Comme avec le `ProductsBySupplierDataSource`, ont le  *`supplierID`*  paramètre reçoivent la valeur de la `SupplierID` valeur de chaîne de requête.


[![Remplir la valeur du paramètre de la valeur de chaîne de requête SupplierID supplierID](master-detail-filtering-across-two-pages-cs/_static/image42.png)](master-detail-filtering-across-two-pages-cs/_static/image41.png)

**Figure 15**: remplir le  *`supplierID`*  valeur du paramètre de la `SupplierID` valeur Querystring ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image43.png))


Lorsque vous liez le contrôle FormView à ObjectDataSource en mode Design, Visual Studio crée automatiquement les FormView `ItemTemplate`, `InsertItemTemplate`, et `EditItemTemplate` avec les contrôles Web Label et zone de texte pour chacun des champs de données retournés par le ObjectDataSource. Étant donné que nous voulons simplement afficher le fournisseur d’informations hésitez pas à supprimer la `InsertItemTemplate` et `EditItemTemplate`. Modifiez ensuite le ItemTemplate afin qu’elle affiche le nom de la société du fournisseur dans un `<h3>` élément et l’adresse, ville, pays et numéro de téléphone sous le nom de la société. Ou bien, vous pouvez définir manuellement les FormView `DataSourceID` et créer le `ItemTemplate` balisage, comme nous l’avons fait dans le «[affichant des données avec le ObjectDataSource](../basic-reporting/displaying-data-with-the-objectdatasource-cs.md)» didacticiel.

Après ces modifications au balisage déclaratif du FormView doit ressembler à ce qui suit :


[!code-aspx[Main](master-detail-filtering-across-two-pages-cs/samples/sample3.aspx)]

La figure 16 présente une capture d’écran de la `ProductsForSupplierDetails.aspx` page une fois que les informations de fournisseur mentionnées ci-dessus a été incluses.


[![La liste des produits inclut un résumé sur le fournisseur](master-detail-filtering-across-two-pages-cs/_static/image45.png)](master-detail-filtering-across-two-pages-cs/_static/image44.png)

**Figure 16**: la liste des produits inclut un résumé sur le fournisseur ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image46.png))


## <a name="applying-the-final-touches-for-theproductsforsupplierdetailsaspxui"></a>Application de la dernière touche pour le`ProductsForSupplierDetails.aspx`l’interface utilisateur

Pour améliorer l’utilisateur expérience pour ce rapport, il sont quelques ajouts nous doivent apporter à la `ProductsForSupplierDetails.aspx` page. Actuellement la seule façon un utilisateur peut accéder à partir de la `ProductsForSupplierDetails.aspx` page sur la liste des fournisseurs consiste à cliquer sur le bouton précédent de leur navigateur. Vous allez ajouter un contrôle de lien hypertexte pour le `ProductsForSupplierDetails.aspx` page qui revient à `SupplierListMaster.aspx`, en fournissant un autre moyen de l’utilisateur revenir à la liste principale.


[![Ajouter un contrôle de lien hypertexte pour rétablir l’utilisateur en SupplierListMaster.aspx](master-detail-filtering-across-two-pages-cs/_static/image48.png)](master-detail-filtering-across-two-pages-cs/_static/image47.png)

**Figure 17**: ajouter un contrôle de lien hypertexte pour reprendre l’utilisateur à `SupplierListMaster.aspx` ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image49.png))


Si l’utilisateur clique sur le lien Afficher les produits pour un fournisseur qui n’a pas tous les produits, les `ProductsBySupplierDataSource` ObjectDataSource dans `ProductsForSupplierDetails.aspx` ne retourne aucun résultat. Le contrôle GridView lié à ObjectDataSource ne sera pas affiché tout balisage résultant dans une zone vide dans la page dans le navigateur de l’utilisateur. Pour indiquer clairement à l’utilisateur qu’il n’y a aucun produit associée au fournisseur sélectionné, nous pouvons définir de GridView `EmptyDataText` propriété au message nous à afficher lorsqu’une telle situation se produit. J’ai défini cette propriété sur « Il n’existe aucune produits fournis par ce fournisseur »

Par défaut, tous les fournisseurs dans la base de données Northwind fournissent au moins un produit. Toutefois, pour ce didacticiel j’ai modifié manuellement le `Products` afin que le fournisseur Escargots Nouveaux n’est plus associé avec des produits de la table. Figure 18 montre la page de détails pour les Nouveaux Escargots après que cette modification a été apportée.


[![Les utilisateurs sont informés que le fournisseur ne fournit pas tous les produits](master-detail-filtering-across-two-pages-cs/_static/image51.png)](master-detail-filtering-across-two-pages-cs/_static/image50.png)

**La figure 18**: les utilisateurs sont informés que le fournisseur ne fournit pas tous les produits ([cliquez pour afficher l’image en taille réelle](master-detail-filtering-across-two-pages-cs/_static/image52.png))


## <a name="summary"></a>Récapitulatif

Alors que les rapports maître/détail peuvent afficher les enregistrements maître et détail sur une seule page, dans de nombreux sites Web qu’ils sont séparés sur deux pages web. Dans ce didacticiel, nous avons étudié comment implémenter un tel rapport maître/détail en ayant les fournisseurs répertoriés dans un contrôle GridView dans la page web « maître » et les produits associés, répertoriés dans la page « Détails ». Chaque ligne du fournisseur dans la page maître web contenait un lien vers la page de détails passé le long de la ligne `SupplierID` valeur. Ces liens à certaines lignes peuvent être ajoutées facilement à l’aide HyperLinkField de GridView.

Dans la page Détails de la récupération de ces produits pour le fournisseur spécifié a été effectuée en appelant le `ProductsBLL` la classe `GetProductsBySupplierID(supplierID)` (méthode). Le  *`supplierID`*  la valeur du paramètre a été spécifiée de façon déclarative à l’aide de la chaîne de requête en tant que le paramètre source. Nous avons également comment afficher les détails du fournisseur dans la page de détails à l’aide d’un FormView.

Notre [didacticiel suivant](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs.md) est la dernière sur les rapports maître/détail. Nous allons examiner comment afficher une liste de produits dans un GridView où chaque ligne comporte un bouton de sélection. En cliquant sur le bouton de sélection affichera les détails de ce produit dans un contrôle DetailsView sur la même page.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-filtering-with-two-dropdownlists-cs.md)
[Suivant](master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs.md)
