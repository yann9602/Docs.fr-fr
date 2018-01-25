---
uid: web-forms/overview/data-access/custom-button-actions/adding-and-responding-to-buttons-to-a-gridview-vb
title: "Ajout et la réponse à des boutons pour un GridView (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner comment ajouter des boutons personnalisés, pour un modèle et les champs d’un contrôle GridView ou DetailsView. En particulier, nous allons générateur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/13/2006
ms.topic: article
ms.assetid: 06c6bbd2-4bdc-435b-87a3-df2c868f4baa
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-button-actions/adding-and-responding-to-buttons-to-a-gridview-vb
msc.type: authoredcontent
ms.openlocfilehash: 8a642a9a8e25d64028df0b5d8741da3008700652
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-and-responding-to-buttons-to-a-gridview-vb"></a>Ajout et la réponse à des boutons pour un GridView (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_28_VB.exe) ou [télécharger le PDF](adding-and-responding-to-buttons-to-a-gridview-vb/_static/datatutorial28vb1.pdf)

> Dans ce didacticiel, nous allons examiner comment ajouter des boutons personnalisés, pour un modèle et les champs d’un contrôle GridView ou DetailsView. En particulier, nous allons construire une interface qui possède un FormView qui permet à l’utilisateur de parcourir les fournisseurs.


## <a name="introduction"></a>Introduction

Alors que de nombreux scénarios de création de rapports impliquent l’accès en lecture seule aux données de rapport, elle s n’est pas rare pour les rapports incluent la possibilité d’effectuer des actions en fonction des données affichées. En général, cela impliqués Ajout d’un contrôle de bouton, LinkButton ou ImageButton Web avec chaque enregistrement affiché dans le rapport qui, lorsque vous cliquez dessus, provoque une publication (postback) et appelle du code côté serveur. Modification et suppression des données sur une base de l’enregistrement par enregistrement sont l’exemple le plus courant. En fait, comme nous l’avons vu en commençant par le [vue d’ensemble de l’insertion, mise à jour et suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) didacticiel, modification et suppression est donc courant que les contrôles GridView, DetailsView et FormView peuvent prendre en charge cette fonctionnalité sans le besoin pour écrire une seule ligne de code.

En outre modifier et supprimer des boutons, le GridView, DetailsView et FormView contrôles peuvent également inclure des boutons, LinkButton ou ImageButtons qui, lorsque vous cliquez dessus, effectuer une logique côté serveur personnalisée. Dans ce didacticiel, nous allons examiner comment ajouter des boutons personnalisés, pour un modèle et les champs d’un contrôle GridView ou DetailsView. En particulier, nous allons construire une interface qui possède un FormView qui permet à l’utilisateur de parcourir les fournisseurs. Pour un fournisseur donné, le contrôle FormView affichent les informations concernant le fournisseur, ainsi que d’un contrôle bouton Web qui, si vous cliquez dessus, marque tous leurs produits associés abandonné. En outre, un GridView répertorie ces produits fournis par le fournisseur sélectionné, chaque ligne contenant augmenter les prix et les boutons de prix de remise qui, si vous cliquez dessus, augmenter ou réduire le produit s `UnitPrice` de 10 % (voir Figure 1).


[![Le FormView et GridView contiennent des boutons qui effectuent des Actions personnalisées](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image2.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image1.png)

**Figure 1**: les deux le FormView et GridView contiennent des boutons qu’effectuer des Actions personnalisées ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image3.png))


## <a name="step-1-adding-the-button-tutorial-web-pages"></a>Étape 1 : Ajout de Pages Web didacticiel bouton

Avant d’examiner comment ajouter des boutons personnalisés, permettent de s Prenez tout d’abord créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel. Commencez par ajouter un nouveau dossier nommé `CustomButtons`. Ensuite, ajoutez les deux pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `CustomButtons.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels relatifs aux boutons personnalisés](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image4.png)

**Figure 2**: ajouter les Pages ASP.NET pour les didacticiels relatifs aux boutons personnalisés


Comme dans les autres dossiers, `Default.aspx` dans le `CustomButtons` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image6.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image5.png)

**Figure 3**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image7.png))


Enfin, ajoutez les pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après la pagination et le tri `<siteMapNode>`:


[!code-xml[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour la modification, l’insertion et la suppression des didacticiels.


![Le plan de Site comprend maintenant une entrée pour le didacticiel de boutons personnalisés](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image8.png)

**Figure 4**: le plan de Site comprend maintenant une entrée pour le didacticiel de boutons personnalisés


## <a name="step-2-adding-a-formview-that-lists-the-suppliers"></a>Étape 2 : Ajout d’un FormView qui répertorie les fournisseurs

Permettent de commencer ce didacticiel en ajoutant le FormView qui répertorie les fournisseurs s. Comme indiqué dans l’Introduction, cette FormView permet à l’utilisateur de parcourir les fournisseurs, affichant les produits fournis par le fournisseur dans un GridView. En outre, cette FormView inclut un bouton qui, lorsque vous cliquez dessus, marque tous les produits fournisseur s abandonné. Avant que nous nous concernent à l’ajout du bouton personnalisé pour le contrôle FormView, permettent de s tout d’abord créer simplement le FormView afin qu’il affiche des informations sur les fournisseur.

Commencez par ouvrir le `CustomButtons.aspx` page dans le `CustomButtons` dossier. Ajouter un FormView à la page en le faisant glisser à partir de la boîte à outils sur le concepteur et l’ensemble de ses `ID` propriété `Suppliers`. À partir de la balise active de s FormView, choisir de créer un nouveau ObjectDataSource nommé `SuppliersDataSource`.


[![Créer un nouveau ObjectDataSource nommé SuppliersDataSource](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image10.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image9.png)

**Figure 5**: créer un nouveau nommé de ObjectDataSource `SuppliersDataSource` ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image11.png))


Configurer cette nouvelle ObjectDataSource telle qu’elle demande à partir de la `SuppliersBLL` classe s `GetSuppliers()` (méthode) (voir Figure 6). Étant donné que cette FormView ne fournit pas une interface pour mettre à jour les informations de fournisseur, sélectionnez (aucun) option dans la liste déroulante sous l’onglet mise à jour.


[![Configurer la Source de données pour utiliser la classe SuppliersBLL s GetSuppliers() (méthode)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image13.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image12.png)

**Figure 6**: configurer la Source de données à utiliser le `SuppliersBLL` classe s `GetSuppliers()` (méthode) ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image14.png))


Après avoir configuré l’ObjectDataSource, Visual Studio va générer un `InsertItemTemplate`, `EditItemTemplate`, et `ItemTemplate` pour le FormView. Supprimer le `InsertItemTemplate` et `EditItemTemplate` et modifier le `ItemTemplate` afin qu’il affiche uniquement le fournisseur s entreprise nom et numéro de téléphone. Enfin, activer la pagination FormView en activant la case à cocher Activer la pagination à partir de sa balise active (ou en définissant sa `AllowPaging` propriété `True`). Après ces modifications votre balisage déclaratif s de page doit ressembler à ce qui suit :


[!code-aspx[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample2.aspx)]

La figure 7 illustre la page CustomButtons.aspx via un navigateur.


[![Le FormView répertorie les champs de téléphone du fournisseur sélectionné et le CompanyName](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image16.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image15.png)

**Figure 7**: les listes de FormView le `CompanyName` et `Phone` champs du fournisseur actuellement sélectionné ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image17.png))


## <a name="step-3-adding-a-gridview-that-lists-the-selected-supplier-s-products"></a>Étape 3 : Ajout d’un GridView qui répertorie les produits de s fournisseur sélectionné

Avant d’ajouter le bouton Arrêter tous les produits pour le modèle de s FormView, nous permettent de s d’abord ajouter un contrôle GridView sous le FormView qui répertorie les produits fournis par le fournisseur sélectionné. Pour cela, pour ajouter un contrôle GridView à la page, définissez son `ID` propriété `SuppliersProducts`, et ajouter un nouveau ObjectDataSource nommé `SuppliersProductsDataSource`.


[![Créer un nouveau ObjectDataSource nommé SuppliersProductsDataSource](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image19.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image18.png)

**Figure 8**: créer un nouveau nommé de ObjectDataSource `SuppliersProductsDataSource` ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image20.png))


Configurer cette ObjectDataSource pour utiliser la classe ProductsBLL s `GetProductsBySupplierID(supplierID)` (méthode) (voir la Figure 9). Bien que cette GridView autorise pour un prix du produit s ajustement, il a gagné t être à l’aide intégrée, modifier ou supprimer des fonctionnalités du contrôle GridView. Par conséquent, nous pouvons définir la liste déroulante (aucun) s ObjectDataSource onglets UPDATE, INSERT et DELETE.


[![Configurer la Source de données pour utiliser la classe ProductsBLL s GetProductsBySupplierID(supplierID) (méthode)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image22.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image21.png)

**Figure 9**: configurer la Source de données à utiliser le `ProductsBLL` classe s `GetProductsBySupplierID(supplierID)` (méthode) ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image23.png))


Étant donné que la `GetProductsBySupplierID(supplierID)` méthode accepte un paramètre d’entrée, l’Assistant ObjectDataSource nous demande la source de cette valeur de paramètre. Pour transmettre le `SupplierID` à partir du FormView, la liste déroulante de source paramètre contrôle et la liste déroulante ControlID à `Suppliers` (l’ID du FormView créé à l’étape 2).


[![Indiquer que supplierID paramètre doit provenir le contrôle FormView de fournisseurs](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image25.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image24.png)

**La figure 10**: indiquer que le  *`supplierID`*  paramètre doit provenir le `Suppliers` contrôle FormView ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image26.png))


À l’issue de l’Assistant ObjectDataSource, le contrôle GridView contiendra un BoundField ou le CheckBoxField pour chacun des champs de données produit s. S permettent de l’ajuster vers le bas pour afficher uniquement le `ProductName` et `UnitPrice` BoundFields avec la `Discontinued` CheckBoxField ; permettent en outre, de format s le `UnitPrice` BoundField telles que son texte est mis en forme comme une valeur monétaire. Votre GridView et `SuppliersProductsDataSource` balisage déclaratif de ObjectDataSource s doit être similaire à la balise suivante :


[!code-aspx[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample3.aspx)]

À ce stade, notre didacticiel affiche un rapport maître/détails permettant à l’utilisateur de choisir un fournisseur à partir du FormView en haut et à afficher les produits fournis par ce fournisseur via le contrôle GridView en bas. Figure 11 illustre une capture d’écran de cette page lors de la sélection du fournisseur Tokyo Traders dans le FormView.


[![Les produits de s sélectionné un fournisseur sont affichés dans le contrôle GridView](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image28.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image27.png)

**Figure 11**: le fournisseur sélectionné s produits sont affichent dans le contrôle GridView ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image29.png))


## <a name="step-4-creating-dal-and-bll-methods-to-discontinue-all-products-for-a-supplier"></a>Étape 4 : Création de la couche DAL et les méthodes de la couche BLL pour interrompre tous les produits pour un fournisseur

Avant que nous pouvons ajouter un bouton sur le FormView qui, lorsque vous cliquez dessus, interrompt tous des produits fournisseur s, nous devons d’abord ajouter une méthode à la couche DAL et BLL qui effectue cette action. En particulier, cette méthode sera nommée `DiscontinueAllProductsForSupplier(supplierID)`. Lorsque vous cliquez sur le bouton de s FormView, nous allons appeler cette méthode dans la couche de logique métier, passant dans le fournisseur sélectionné s `SupplierID`; la couche BLL puis appellera la méthode correspondante de la couche d’accès aux données, qui émet un `UPDATE` instruction la base de données interrompt les produits du fournisseur spécifié s.

Comme nous l’avons fait dans nos didacticiels précédents, nous allons utiliser une approche de bas en haut, en commençant par la création de la méthode de la couche DAL, puis la méthode de la couche BLL et implémenter les fonctionnalités dans la page ASP.NET. Ouvrir le `Northwind.xsd` DataSet typé dans les `App_Code/DAL` dossier et ajouter une nouvelle méthode de la `ProductsTableAdapter` (avec le bouton droit sur le `ProductsTableAdapter` et choisissez Ajouter une requête). Cela s’affiche l’Assistant Configuration de requête TableAdapter, nous vous guide tout au long du processus d’ajout de la nouvelle méthode. Démarrez en indiquant que notre méthode de la couche DAL utilisera une instruction SQL d’ad-hoc.


[![Créez la méthode de la couche DAL à l’aide d’une instruction SQL de Ad Hoc](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image31.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image30.png)

**Figure 12**: créer la méthode de la couche DAL à l’aide une instruction SQL de Ad-Hoc ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image32.png))


Ensuite, l’Assistant vous demande si nous concernant le type de requête à créer. Étant donné que la `DiscontinueAllProductsForSupplier(supplierID)` méthode devez mettre à jour le `Products` table de base de données, la définition la `Discontinued` 1 pour tous les produits fournis par le champ  *`supplierID`* , nous avons besoin créer une requête qui met à jour des données.


[![Choisissez le Type de requête de mise à jour](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image34.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image33.png)

**Figure 13**: choisissez le Type de requête de mise à jour ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image35.png))


Fournit de l’écran suivant de l’Assistant TableAdapter s existants `UPDATE` instruction, ce qui met à jour chacun des champs définis dans le `Products` DataTable. Remplacez ce texte de requête avec l’instruction suivante :


[!code-sql[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample4.sql)]

Après la saisie de cette requête et cliquez sur Suivant, le dernier écran de l’Assistant vous demande le nom de s nouvelle méthode utiliser `DiscontinueAllProductsForSupplier`. Terminez l’Assistant en cliquant sur le bouton Terminer. Lors du retour vers le Concepteur de DataSet, vous devez voir une nouvelle méthode dans le `ProductsTableAdapter` nommé `DiscontinueAllProductsForSupplier(@SupplierID)`.


[![Nom de la nouvelle DiscontinueAllProductsForSupplier de la couche DAL (méthode)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image37.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image36.png)

**La figure 14**: nom de la nouvelle méthode de la couche DAL `DiscontinueAllProductsForSupplier` ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image38.png))


Avec la `DiscontinueAllProductsForSupplier(supplierID)` créé dans la couche d’accès aux données, la tâche suivante consiste à créer le `DiscontinueAllProductsForSupplier(supplierID)` méthode dans la couche de logique métier. Pour ce faire, ouvrez le `ProductsBLL` fichier de classe et ajoutez le code suivant :


[!code-vb[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample5.vb)]

Cette méthode appelle simplement jusqu'à la `DiscontinueAllProductsForSupplier(supplierID)` méthode dans la couche DAL, passant fourni  *`supplierID`*  la valeur du paramètre. S’il existe des règles d’entreprise uniquement un fournisseur produits s supprimée dans certaines circonstances, ces règles doivent être implémentées ici, dans la couche BLL.

> [!NOTE]
> Contrairement à la `UpdateProduct` dans les surcharges de la `ProductsBLL` (classe), le `DiscontinueAllProductsForSupplier(supplierID)` signature de méthode n’inclut pas le `DataObjectMethodAttribute` attribut (`<System.ComponentModel.DataObjectMethodAttribute(System.ComponentModel.DataObjectMethodType.Update, Boolean)>`). Cela empêche le `DiscontinueAllProductsForSupplier(supplierID)` méthode à partir de la liste de déroulante ObjectDataSource s s d’Assistant Configurer la Source de données dans l’onglet mise à jour. Je ve omis cet attribut, car nous allons appeler la `DiscontinueAllProductsForSupplier(supplierID)` (méthode) directement à partir d’un gestionnaire d’événements dans notre page ASP.NET.


## <a name="step-5-adding-a-discontinue-all-products-button-to-the-formview"></a>Étape 5 : Ajout d’un interrompre tous les produits bouton, sur le FormView

Avec la `DiscontinueAllProductsForSupplier(supplierID)` méthode dans la couche BLL et DAL terminé, la dernière étape pour ajouter la possibilité de mettre fin à tous les produits pour le fournisseur sélectionné est pour ajouter un contrôle bouton Web au s FormView `ItemTemplate`. S permettent d’ajouter ce bouton sous le numéro de téléphone de fournisseur s avec le texte du bouton, interrompez tous les produits et un `ID` valeur de propriété `DiscontinueAllProductsForSupplier`. Vous pouvez ajouter ce contrôle bouton Web via le concepteur en cliquant sur le lien Modifier les modèles dans la balise active de s FormView (voir Figure 15), ou directement par le biais de la syntaxe déclarative.


[![Ajouter un interrompre tous les produits Web contrôle Button ItemTemplate s FormView](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image40.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image39.png)

**Figure 15**: ajouter un interrompre tous les produits Web contrôle au s FormView `ItemTemplate` ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image41.png))


Lorsque le bouton est activé par une visite de l’utilisateur s’ensuit de la page, une publication (postback) ainsi que les FormView [ `ItemCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.formview.itemcommand.aspx) se déclenche. Pour exécuter du code personnalisé en réponse à ce bouton, nous pouvons créer un gestionnaire d’événements pour cet événement. Sachez, cependant, que le `ItemCommand` événement est déclenché chaque fois que *tout* Button, LinkButton ou ImageButton Web est activé dans le FormView. Cela signifie que lorsque l’utilisateur déplace d’une page à l’autre dans le FormView, le `ItemCommand` se déclenche des événements ; même chose lorsque l’utilisateur clique sur Nouveau, modifier ou supprimer dans un FormView qui prend en charge d’insertion, de mise à jour ou la suppression.

Étant donné que le `ItemCommand` se déclenche, quelle que soit le bouton est activé, dans le gestionnaire, nous devons déterminer si utilisateur a cliqué sur le bouton Arrêter tous les produits des événements, ou s’il s’agissait d’une autre bouton. Pour ce faire, nous pouvons définir le contrôle de bouton Web s `CommandName` propriété à une valeur d’identification. Lorsque le bouton est activé, cela `CommandName` valeur passée dans le `ItemCommand` Gestionnaire d’événements, ce qui nous permet de déterminer si le bouton Arrêter tous les produits est le bouton cliqué. Définir le s interrompre le bouton tous les produits `CommandName` propriété DiscontinueProducts.

Enfin, permettent de s utilisent une boîte de dialogue de confirmation du côté client pour vous assurer que l’utilisateur souhaite vraiment interrompre les produits du fournisseur sélectionné s. Comme nous l’avons vu dans la [Ajout côté Client Confirmation lors de la suppression](../editing-inserting-and-deleting-data/adding-client-side-confirmation-when-deleting-vb.md) didacticiel, cela peut être accompli avec un peu de JavaScript. En particulier, la valeur la propriété de OnClientClick s du contrôle bouton Web`return confirm('This will mark _all_ of this supplier\'s products as discontinued. Are you certain you want to do this?');`

Après avoir apporté ces modifications, la syntaxe déclarative de s FormView doit se présenter comme suit :


[!code-aspx[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample6.aspx)]

Ensuite, créez un gestionnaire d’événements pour les opérations de mappage FormView `ItemCommand` événement. Dans ce gestionnaire d’événements, nous devons d’abord déterminer si le bouton Arrêter tous les produits a été activé. Si nous voulons que par conséquent créer une instance de la `ProductsBLL` de classe et d’appeler son `DiscontinueAllProductsForSupplier(supplierID)` méthode, en passant le `SupplierID` du FormView sélectionné :


[!code-vb[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample7.vb)]

Notez que la `SupplierID` du fournisseur sélectionné en cours dans le FormView sont accessibles en utilisant le FormView s [ `SelectedValue` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.formview.selectedvalue.aspx). Le `SelectedValue` propriété retourne la première clé de données valeur de l’enregistrement s’affiche dans le FormView. Le FormView s [ `DataKeyNames` propriété](https://msdn.microsoft.com/system.web.ui.webcontrols.formview.datakeynames.aspx), ce qui indique les données sont extraites les champs à partir de laquelle les valeurs de clé de données est automatiquement définie pour `SupplierID` par Visual Studio lors de la liaison ObjectDataSource à l’arrière du FormView à l’étape 2.

Avec la `ItemCommand` Gestionnaire d’événements créé, prenez un moment pour tester la page. Accédez à la Quesos de Cooperativa 'Las Cabras' fournisseur (il s le cinquième fournisseur dans le FormView pour moi). Ce fournisseur fournit deux produits, Queso Cabrales et Queso Manchego La Pastora, qui sont tous deux *pas* abandonné.

Imaginez que Cooperativa de Quesos 'Las Cabras' existe plus et par conséquent ses produits doivent être supprimées. Cliquez sur l’interrompre tous les produits bouton. Cela affiche la boîte de dialogue de confirmation du côté client boîte (voir Figure 16).


[![Cooperativa de Quesos Las Cabras fournit deux produits actifs](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image43.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image42.png)

**Figure 16**: Cooperativa de Quesos Las Cabras fournit deux produits actifs ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image44.png))


Si vous cliquez sur OK dans la boîte de dialogue de confirmation du côté client, l’envoi du formulaire se poursuit, à l’origine d’une publication (postback) dans lequel le s FormView `ItemCommand` événement se déclenche. Nous avons créé le Gestionnaire d’événements s’exécute, appelez le `DiscontinueAllProductsForSupplier(supplierID)` (méthode) et abandon les Queso Cabrales Queso Manchego La Pastora produits et.

Si vous avez désactivé l’état d’affichage s GridView, le contrôle GridView est en cours reliée à la banque de données sous-jacente à chaque publication et par conséquent sera immédiatement mise à jour pour refléter que ces deux produits ont été supprimées (voir Figure 17). Si, toutefois, vous avez désactivé pas les afficher l’état dans le GridView, vous devez lier manuellement de nouveau les données au contrôle GridView après avoir apporté cette modification. Pour ce faire, simplement effectuer un appel au GridView s `DataBind()` méthode immédiatement après l’appel de la `DiscontinueAllProductsForSupplier(supplierID)` (méthode).


[![Après avoir cliqué sur le bouton Arrêter tous les produits, les produits du fournisseur s sont mis à jour en conséquence](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image46.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image45.png)

**Figure 17**: après avoir cliqué sur le bouton Arrêter tous les produits, les produits du fournisseur s sont mis à jour en conséquence ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image47.png))


## <a name="step-6-creating-an-updateproduct-overload-in-the-business-logic-layer-for-adjusting-a-product-s-price"></a>Étape 6 : Création d’une surcharge UpdateProduct dans la couche de logique métier pour régler un prix du produit s

Comme avec le bouton Arrêter tous les produits dans le FormView, afin d’ajouter des boutons pour augmenter et de diminuer le prix d’un produit dans le GridView nous devons d’abord ajouter les méthodes appropriées de couche d’accès aux données et de la couche de logique métier. Étant donné que nous avons déjà une méthode qui met à jour une ligne de produit unique dans la couche DAL, nous pouvons fournir ces fonctionnalités en créant une nouvelle surcharge pour la `UpdateProduct` méthode dans la couche BLL.

Notre passé `UpdateProduct` surcharges ont pris dans une combinaison des champs de produits en tant que valeurs d’entrée scalaires et avez mise à jour uniquement les champs pour le produit spécifié. Pour cette surcharge, nous allons varier légèrement en fonction de cette norme et passer à la place dans le produit s `ProductID` et le pourcentage par lequel ajuster le `UnitPrice` (par opposition à passer dans la nouvelle, ajustée `UnitPrice` lui-même). Cette approche simplifie le code que nous avons besoin d’écrire dans la classe code-behind de page ASP.NET, étant donné que nous n t avoir à vous soucier de déterminer le produit actuel s `UnitPrice`.

Le `UpdateProduct` de surcharge pour ce didacticiel est indiqué ci-dessous :


[!code-vb[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample8.vb)]

Cette surcharge récupère des informations sur le produit spécifié via la couche DAL s `GetProductByProductID(productID)` (méthode). Il vérifie ensuite si le produit s `UnitPrice` est attribué à une base de données `NULL` valeur. Si elle est, le prix reste inchangée. Si, toutefois, il est non -`NULL` `UnitPrice` valeur, la méthode met à jour le produit s `UnitPrice` par le pourcentage spécifié (`unitPriceAdjustmentPercent`).

## <a name="step-7-adding-the-increase-and-decrease-buttons-to-the-gridview"></a>Étape 7 : Ajout de l’augmentation et la diminution de boutons au contrôle GridView

Le GridView (et DetailsView) sont tous deux constituées d’une collection de champs. En plus de BoundFields, CheckBoxFields et TemplateField, ASP.NET inclut ButtonField, ce qui, comme son nom l’indique, le rendu sous la forme d’une colonne avec un bouton, LinkButton ou ImageButton pour chaque ligne. Similaire à FormView, en cliquant sur *tout* dans le GridView boutons de pagination, modifier ou supprimer boutons, boutons de tri et ainsi de suite avec le bouton provoque une publication (postback) et déclenche le GridView s [ `RowCommand` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview.rowcommand.aspx).

Le ButtonField a un `CommandName` propriété qui affecte la valeur spécifiée à chacun de ses boutons `CommandName` propriétés. Comme avec le contrôle FormView, le `CommandName` valeur est utilisée par le `RowCommand` Gestionnaire d’événements pour déterminer quel bouton a été utilisé.

S permettent d’ajouter deux ButtonFields nouveau au GridView, une avec un texte de bouton prix + 10 % et l’autre avec le texte de prix -10 %. Pour ajouter ces ButtonFields, cliquez sur le lien Modifier les colonnes à partir de la balise active de GridView s, sélectionnez le type de champ ButtonField dans la liste en haut à gauche et cliquez sur le bouton Ajouter.


![Ajoutez deux ButtonFields pour le contrôle GridView](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image48.png)

**La figure 18**: ajouter deux ButtonFields pour le contrôle GridView


Déplacer les deux ButtonFields afin qu’ils apparaissent en tant que les deux premiers champs de GridView. Ensuite, définissez la `Text` propriétés de ces deux ButtonFields à + 10 % de prix et de prix -10 % et la `CommandName` propriétés IncreasePrice et DecreasePrice, respectivement. Par défaut, un ButtonField rend sa colonne de boutons en tant que LinkButton. Cela peut être modifié, toutefois, via le s ButtonField [ `ButtonType` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.buttonfieldbase.buttontype.aspx). S permettent de disposer de ces deux ButtonFields rendus sous forme de boutons de commande standards ; Par conséquent, définir le `ButtonType` propriété `Button`. Figure 19 montre les champs de la boîte de dialogue après ont apporté ces modifications ; qui est le balisage déclaratif de GridView s.


![Configurer le texte de ButtonFields, CommandName et ButtonType propriétés](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image49.png)

**Figure 19**: configurer le ButtonFields `Text`, `CommandName`, et `ButtonType` propriétés


[!code-aspx[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample9.aspx)]

Avec ces ButtonFields créés, la dernière étape consiste à créer un gestionnaire d’événements pour le contrôle GridView s `RowCommand` événement. Ce gestionnaire d’événements, si déclenché parce que le prix + 10 boutons de prix -10 % ou % clic, les besoins pour déterminer le `ProductID` pour la ligne dont bouton l’utilisateur a cliqué, puis appelez le `ProductsBLL` classe s `UpdateProduct` méthode, en passant les paramètres appropriés `UnitPrice` ajustement du pourcentage avec le `ProductID`. Le code suivant effectue ces tâches :

[!code-vb[Main](adding-and-responding-to-buttons-to-a-gridview-vb/samples/sample10.vb)]

Afin de déterminer la `ProductID` pour la ligne dont le prix + 10 % ou bouton de prix -10 % a été activé, nous avons besoin de consulter le s GridView `DataKeys` collection. Cette collection contient les valeurs des champs spécifiés dans le `DataKeyNames` propriété pour chaque ligne GridView. Depuis le GridView s `DataKeyNames` propriété a la valeur ProductID par Visual Studio lors de la liaison ObjectDataSource au GridView, `DataKeys(rowIndex).Value` fournit le `ProductID` spécifié *rowIndex*.

Le ButtonField passe automatiquement dans le *rowIndex* de la ligne dont clic via le `e.CommandArgument` paramètre. Par conséquent, pour déterminer le `ProductID` pour la ligne dont le prix + 10 % ou bouton de prix -10 % a été activé, nous utilisons : `Convert.ToInt32(SuppliersProducts.DataKeys(Convert.ToInt32(e.CommandArgument)).Value)`.

Comme avec le bouton Arrêter tous les produits, si vous avez désactivé l’état d’affichage GridView s, le contrôle GridView est en cours reconnectés dans le magasin de données sous-jacent à chaque publication et par conséquent sera immédiatement mise à jour pour refléter une modification de prix qui se produit de cliquer sur un des boutons. Si, toutefois, vous avez désactivé pas les afficher l’état dans le GridView, vous devez lier manuellement de nouveau les données au contrôle GridView après avoir apporté cette modification. Pour ce faire, simplement effectuer un appel au GridView s `DataBind()` méthode immédiatement après l’appel de la `UpdateProduct` (méthode).

Figure 20 montre la page lors de l’affichage des produits de grand-mère Kelly Homestead. Figure 21 montre les résultats après le prix + 10 % a cliqué deux fois pour Boysenberry Spread s et le bouton % le prix -10 fois pour Northwoods Airelles.


[![Le contrôle GridView inclut prix + 10 % et les boutons de prix -10 %](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image51.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image50.png)

**Figure 20**: le prix d’inclut GridView + 10 % et prix -10 % boutons ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image52.png))


[![Les prix du produit de la première et la troisième ont été mis à jour via le prix + 10 % et les boutons de prix -10 %](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image54.png)](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image53.png)

**Figure 21**: le prix pour la première et la troisième produit ont été mis à jour via le prix + 10 % et prix -10 % boutons ([cliquez pour afficher l’image en taille réelle](adding-and-responding-to-buttons-to-a-gridview-vb/_static/image55.png))


> [!NOTE]
> Le GridView (DetailsView) peut également avoir des boutons, LinkButton ou ImageButtons ajouté à leur TemplateField. Comme avec la BoundField, ces boutons, cette option seront induire une publication (postback), le déclenchement de s GridView `RowCommand` événement. Lorsque Ajout de boutons dans TemplateField, toutefois, le bouton s `CommandArgument` n’est pas automatiquement définie à l’index de la ligne comme c’est lors de l’utilisation de ButtonFields. Si vous avez besoin déterminer l’index de ligne du bouton sur lequel l’utilisateur a cliqué dans le `RowCommand` Gestionnaire d’événements, vous devez définir manuellement le bouton s `CommandArgument` propriété dans sa syntaxe déclarative dans le TemplateField, à l’aide de code similaire à :  
> `<asp:Button runat="server" ... CommandArgument='<%# CType(Container, GridViewRow).RowIndex %>' />`.


## <a name="summary"></a>Récapitulatif

Tous les contrôles GridView, DetailsView et FormView peuvent inclure des boutons, LinkButton ou ImageButtons. Ces boutons, lorsque vous cliquez dessus, provoquent une publication et de déclencher la `ItemCommand` événements dans les contrôles FormView et DetailsView et `RowCommand` événement dans le GridView. Ces contrôles Web de données ont des fonctionnalités intégrées pour gérer les actions courantes liées à la commande, telles que la suppression ou modification d’enregistrements. Toutefois, nous pouvons également utiliser les boutons qui, lorsque vous cliquez sur, de réagir à l’exécution de notre propre code personnalisé.

Pour ce faire, nous devons créer un gestionnaire d’événements pour le `ItemCommand` ou `RowCommand` événement. Dans ce gestionnaire d’événements, nous Vérifiez tout d’abord entrant `CommandName` valeur pour déterminer quel bouton a été utilisé, puis d’exécuter une action personnalisée appropriée. Dans ce didacticiel, nous avons vu comment utiliser les boutons et ButtonFields pour interrompre tous les produits pour un fournisseur spécifié ou pour augmenter ou diminuer le prix d’un produit particulier de 10 %.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](adding-and-responding-to-buttons-to-a-gridview-cs.md)
