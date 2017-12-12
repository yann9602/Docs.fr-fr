---
uid: web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/customizing-the-datalist-s-editing-interface-vb
title: "Personnalisation du contrôle DataList d’édition Interface (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons créer une interface plus riche de modification pour le contrôle DataList, qui inclut la compréhension des listes et une case à cocher."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2006
ms.topic: article
ms.assetid: 718628e2-224c-455f-b33a-a41efd48d5a0
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/customizing-the-datalist-s-editing-interface-vb
msc.type: authoredcontent
ms.openlocfilehash: bd31c18b9fced12feeda8ca8dea7dace0ef63573
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="customizing-the-datalists-editing-interface-vb"></a>Personnalisation de l’Interface de modification du contrôle DataList (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_40_VB.exe) ou [télécharger le PDF](customizing-the-datalist-s-editing-interface-vb/_static/datatutorial40vb1.pdf)

> Dans ce didacticiel, nous allons créer une interface plus riche de modification pour le contrôle DataList, qui inclut la compréhension des listes et une case à cocher.


## <a name="introduction"></a>Introduction

Le balisage et les contrôles Web dans le contrôle DataList s `EditItemTemplate` définir son interface modifiable. Dans tous les exemples de DataList modifiable nous ve examinés jusqu'à présent, le texte modifiable interface a été composé de contrôles Web de la zone de texte. Dans le [didacticiel précédent](adding-validation-controls-to-the-datalist-s-editing-interface-vb.md) nous améliorer l’expérience utilisateur de l’heure de modification en ajoutant des contrôles de validation.

Le `EditItemTemplate` peut également être étendue pour inclure les contrôles Web autre que la zone de texte, tels que les calendriers de compréhension des listes, RadioButtonList, et ainsi de suite. Comme avec les zones de texte, lors de la personnalisation de l’interface de modification pour inclure d’autres contrôles Web, utiliser les étapes suivantes :

1. Ajoutez le contrôle Web à le `EditItemTemplate`.
2. Utilisez la syntaxe de liaison de données pour affecter la valeur de champ de données correspondant à la propriété appropriée.
3. Dans la `UpdateCommand` contrôler la valeur de gestionnaire d’événements, accéder par programmation du Web et passez-la dans la méthode appropriée de la couche BLL.

Dans ce didacticiel, nous allons créer une interface plus riche de modification pour le contrôle DataList, qui inclut la compréhension des listes et une case à cocher. En particulier, nous allons créer un contrôle DataList qui répertorie des informations sur les produits et les rend le nom de produit s, fournisseur, catégorie et état abandonné à mettre à jour (voir Figure 1).


[![L’Interface de modification comprend une zone de texte, compréhension des deux listes et une case à cocher](customizing-the-datalist-s-editing-interface-vb/_static/image2.png)](customizing-the-datalist-s-editing-interface-vb/_static/image1.png)

**Figure 1**: l’Interface de modification comprend une zone de texte et une case à cocher compréhension des deux listes ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image3.png))


## <a name="step-1-displaying-product-information"></a>Étape 1 : Affichage des informations de produit

Avant que nous pouvons créer l’interface modifiable de DataList s, nous devons d’abord créer l’interface en lecture seule. Commencez par ouvrir le `CustomizedUI.aspx` page à partir de la `EditDeleteDataList` dossier et, dans le concepteur, ajoutez un contrôle DataList à la page, définir son `ID` propriété `Products`. À partir de la balise active de s DataList, créez un nouveau ObjectDataSource. Nommez cette nouvelle ObjectDataSource `ProductsDataSource` et configurez-le pour extraire des données à partir de la `ProductsBLL` classe s `GetProducts` (méthode). Comme avec les didacticiels de DataList modifiable précédents, nous mettrons à jour les informations produit modifié en accédant directement à la couche de logique métier. En conséquence, définissez les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None).


[![Définir les listes déroulantes de mise à jour, insérer et supprimer les tabulations à (None)](customizing-the-datalist-s-editing-interface-vb/_static/image5.png)](customizing-the-datalist-s-editing-interface-vb/_static/image4.png)

**Figure 2**: valeur (None) les mise à jour, insérer et supprimer des onglets de liste déroulante répertorie ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image6.png))


Après avoir configuré l’ObjectDataSource, Visual Studio crée une valeur par défaut `ItemTemplate` pour le contrôle DataList qui répertorie le nom et une valeur pour chacun des champs de données retournées. Modifier la `ItemTemplate` afin que le modèle répertorie le nom du produit dans un `<h4>` élément ainsi que le nom de la catégorie, le nom du fournisseur, le prix et l’état abandonné. En outre, ajoutez un bouton Modifier, en veillant à ce que son `CommandName` est définie sur Modifier. Le balisage déclaratif pour mon `ItemTemplate` suit :


[!code-aspx[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample1.aspx)]

Le balisage ci-dessus présente les informations de produit à l’aide un &lt;h4&gt; pour le nom de produit s et quatre colonnes `<table>` pour les champs restants. Le `ProductPropertyLabel` et `ProductPropertyValue` classes CSS, définies dans `Styles.css`, ont été présentées dans les didacticiels précédents. La figure 3 illustre notre progression lors de l’affichage via un navigateur.


[![Le nom du fournisseur, catégorie, abandonné l’état et prix de chaque produit s’affiche.](customizing-the-datalist-s-editing-interface-vb/_static/image8.png)](customizing-the-datalist-s-editing-interface-vb/_static/image7.png)

**Figure 3**: le nom, fournisseur, catégorie, abandonné l’état et le prix de chaque produit s’affichent ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image9.png))


## <a name="step-2-adding-the-web-controls-to-the-editing-interface"></a>Étape 2 : Ajout de contrôles Web à l’Interface de modification

La première étape de génération personnalisée DataList interface de modification consiste à ajouter des contrôles Web nécessaires à la `EditItemTemplate`. En particulier, nous devons DropDownList pour la catégorie, un autre pour le fournisseur et une case à cocher pour l’état abandonné. Étant donné que le prix du produit s n’est pas modifiable dans cet exemple, nous pouvons continuer à afficher à l’aide d’un contrôle Web Label.

Pour personnaliser l’interface de modification, cliquez sur le lien Modifier les modèles à partir de la balise active du contrôle DataList s et choisissez la `EditItemTemplate` option dans la liste déroulante. Ajouter une liste déroulante pour le `EditItemTemplate` et définir son `ID` à `Categories`.


[![Ajouter une liste déroulante pour les catégories](customizing-the-datalist-s-editing-interface-vb/_static/image11.png)](customizing-the-datalist-s-editing-interface-vb/_static/image10.png)

**Figure 4**: ajouter un contrôle DropDownList pour les catégories ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image12.png))


Ensuite, à partir de la balise active de s DropDownList, sélectionnez l’option de choisir la Source de données et créer un nouveau ObjectDataSource nommé `CategoriesDataSource`. Configurer cette ObjectDataSource à utiliser le `CategoriesBLL` classe s `GetCategories()` (méthode) (voir Figure 5). Ensuite, le s DropDownList Assistant de Configuration de Source de données vous invite à entrer pour les champs de données à utiliser pour chaque `ListItem` s `Text` et `Value` propriétés. Afficher le DropDownList le `CategoryName` champ de données et utilisez le `CategoryID` comme valeur, comme indiqué dans la Figure 6.


[![Créer un nouveau ObjectDataSource nommé CategoriesDataSource](customizing-the-datalist-s-editing-interface-vb/_static/image14.png)](customizing-the-datalist-s-editing-interface-vb/_static/image13.png)

**Figure 5**: créer un nouveau nommé de ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image15.png))


[![Configurer l’affichage de s DropDownList et que la valeur des champs](customizing-the-datalist-s-editing-interface-vb/_static/image17.png)](customizing-the-datalist-s-editing-interface-vb/_static/image16.png)

**Figure 6**: configurer les champs de valeur DropDownList s affichage ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image18.png))


Répétez cette série d’étapes pour créer une liste déroulante pour les fournisseurs. Définir le `ID` pour cette DropDownList pour `Suppliers` et le nom de son ObjectDataSource `SuppliersDataSource`.

Après avoir ajouté les compréhension des deux listes, ajoutez une case à cocher pour l’état abandonné et une zone de texte Nom de produit s. Définir le `ID` s pour la case à cocher et zone de texte pour `Discontinued` et `ProductName`, respectivement. Ajoutez un contrôle RequiredFieldValidator pour vous assurer que l’utilisateur fournit une valeur pour le nom de produit s.

Enfin, ajoutez les boutons Annuler et de mise à jour. Souvenez-vous que pour ces deux boutons, il est impératif que leur `CommandName` propriétés sont définies pour mettre à jour et Annuler, respectivement.

N’hésitez pas à disposer de l’interface de modification comme vous le souhaitez. Je ve choisi d’utiliser le même quatre colonnes `<table>` illustre la disposition de l’interface en lecture seule, comme la syntaxe déclarative suivante et la capture d’écran :


[!code-aspx[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample2.aspx)]


[![L’Interface de modification est définie comme l’Interface en lecture seule](customizing-the-datalist-s-editing-interface-vb/_static/image20.png)](customizing-the-datalist-s-editing-interface-vb/_static/image19.png)

**Figure 7**: l’Interface de modification est définie comme l’Interface en lecture seule ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image21.png))


## <a name="step-3-creating-the-editcommand-and-cancelcommand-event-handlers"></a>Étape 3 : Créer les gestionnaires d’événements de CancelCommand EditCommand

Actuellement, il n’existe aucune syntaxe de liaison de données dans le `EditItemTemplate` (à l’exception de la `UnitPriceLabel`, qui a été copié sur textuellement dans le `ItemTemplate`). Nous allons ajouter momentanément la syntaxe de liaison de données, mais permettent de première s créer les gestionnaires d’événements pour le contrôle DataList s `EditCommand` et `CancelCommand` les événements. N’oubliez pas que la responsabilité de la `EditCommand` est de gestionnaire d’événements pour afficher l’interface de modification pour l’élément DataList dont bouton Modifier, alors que le `CancelCommand` s consiste à retourner du contrôle DataList à son état avant la modification.

Créer ces deux gestionnaires d’événements et les utiliser le code suivant :


[!code-vb[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample3.vb)]

Avec ces deux gestionnaires d’événements en place, en cliquant sur le bouton Modifier affiche l’interface de modification et en cliquant sur le bouton Annuler retourne l’élément modifié en mode lecture seule. La figure 8 illustre du contrôle DataList après est effectué sur le bouton Modifier pour Chef Anton s Gumbo Mix. Dans la mesure où ve encore pour ajouter une syntaxe de liaison de données à l’interface de modification, le `ProductName` zone de texte est vide, le `Discontinued` case à cocher est désactivée et les premiers éléments sélectionnés à partir de la `Categories` et `Suppliers` compréhension des listes.


[![En cliquant sur le bouton Modifier s’affiche l’Interface de modification](customizing-the-datalist-s-editing-interface-vb/_static/image23.png)](customizing-the-datalist-s-editing-interface-vb/_static/image22.png)

**Figure 8**: en cliquant sur le bouton Modifier affiche l’Interface de modification ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image24.png))


## <a name="step-4-adding-the-databinding-syntax-to-the-editing-interface"></a>Étape 4 : Ajout de la syntaxe de liaison de données à l’Interface de modification

Pour afficher les valeurs de s produit actuel dans l’interface de modification, que nous devons utiliser la syntaxe de liaison de données pour affecter les valeurs de champ de données pour les valeurs de contrôle Web appropriés. La syntaxe de liaison de données peut être appliquée via le concepteur en accédant à l’écran Modifier les modèles et en sélectionnant le lien Modifier les DataBindings à partir du Web de contrôle des balises actives. Vous pouvez également la syntaxe de liaison de données peut être ajoutée directement à la balise déclarative.

Affecter le `ProductName` valeur de champ de données le `ProductName` s de la zone de texte `Text` propriété, le `CategoryID` et `SupplierID` valeurs de champ de données le `Categories` et `Suppliers` compréhension des listes `SelectedValue` propriétés et la `Discontinued` valeur de champ de données le `Discontinued` case à cocher s `Checked` propriété. Après avoir apporté ces modifications, par le biais du concepteur ou directement le balisage déclaratif, revoir la page via un navigateur, puis cliquez sur le bouton Modifier pour Chef Anton s Gumbo Mix. Comme le montre la Figure 9, la syntaxe de liaison de données a ajouté les valeurs actuelles dans la zone de texte, la compréhension des listes et la case à cocher.


[![En cliquant sur le bouton Modifier s’affiche l’Interface de modification](customizing-the-datalist-s-editing-interface-vb/_static/image26.png)](customizing-the-datalist-s-editing-interface-vb/_static/image25.png)

**Figure 9**: en cliquant sur le bouton Modifier affiche l’Interface de modification ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image27.png))


## <a name="step-5-saving-the-user-s-changes-in-the-updatecommand-event-handler"></a>Étape 5 : L’enregistrement des modifications s utilisateur dans le Gestionnaire d’événements de UpdateCommand

Lorsque l’utilisateur modifie un produit et clique sur le bouton de mise à jour, une publication (postback) et s DataList `UpdateCommand` se déclenche des événements. Dans l’événement gestionnaire, nous devons lire les valeurs de contrôles Web dans le `EditItemTemplate` et l’interface avec la couche BLL mise à jour du produit dans la base de données. Comme nous ve vu dans les didacticiels précédents, le `ProductID` du produit mis à jour est accessible via la `DataKeys` collection. Les champs entré par l’utilisateur sont accessibles en référençant par programmation des contrôles Web à l’aide de `FindControl("controlID")`, comme le montre le code suivant :


[!code-vb[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample4.vb)]

Le code commence en consultant le `Page.IsValid` propriété afin de garantir que tous les contrôles de validation sur la page sont valides. Si `Page.IsValid` est `True`, le produit modifié s `ProductID` valeur est lue à partir de la `DataKeys` collection et l’entrée de données dans les contrôles Web le `EditItemTemplate` sont référencés par programmation. Ensuite, les valeurs à partir de ces contrôles Web sont lues dans les variables qui sont ensuite transférées en approprié `UpdateProduct` de surcharge. Après la mise à jour les données, le contrôle DataList est retournée à son état avant la modification.

> [!NOTE]
> Je ve omis ajoutée dans la logique de gestion des exceptions la [BLL - gestion et Exceptions au niveau de la couche DAL](handling-bll-and-dal-level-exceptions-vb.md) axée sur le didacticiel afin de conserver le code et l’exemple suivant. En guise d’exercice, ajoutez cette fonctionnalité quand ils auront terminé ce didacticiel.


## <a name="step-6-handling-null-categoryid-and-supplierid-values"></a>Étape 6 : La gestion des CategoryID NULL et les valeurs SupplierID

Permet à la base de données Northwind pour `NULL` les valeurs pour le `Products` table s `CategoryID` et `SupplierID` colonnes. Toutefois, notre édition t de n interface adapter `NULL` valeurs. Si vous essayez de modifier un produit qui a un `NULL` valeur pour une son `CategoryID` ou `SupplierID` colonnes, nous obtenons une `ArgumentOutOfRangeException` avec un message d’erreur semblable à : *« Catégories » a un SelectedValue qui n’est pas valide, car il ne n’existe pas dans la liste des éléments.* En outre, s’il n’y a actuellement aucun moyen pour modifier une catégorie de produit s ou d’un fournisseur de valeur non -`NULL` valeur un `NULL` une.

Pour prendre en charge `NULL` valeurs pour la catégorie et la compréhension des listes de fournisseur, nous devons ajouter un autre `ListItem`. Je ve choisi d’utiliser (aucun), comme le `Text` valeur `ListItem`, mais vous pouvez le modifier de quelque chose d’autre (par exemple, une chaîne vide) si vous d comme. Enfin, n’oubliez pas de définir les compréhension des listes `AppendDataBoundItems` à `True`; si vous oubliez de le faire, les catégories et fournisseurs liés à DropDownList remplacera le-ajoutés de façon statique `ListItem`.

Après avoir apporté ces modifications, le balisage de compréhension des listes dans le contrôle DataList s `EditItemTemplate` doit ressembler à ce qui suit :


[!code-aspx[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample5.aspx)]

> [!NOTE]
> Statique `ListItem` peuvent être ajoutées à une liste déroulante dans le concepteur ou directement par le biais de la syntaxe déclarative. Lorsque vous ajoutez un élément DropDownList pour représenter une base de données `NULL` valeur, assurez-vous d’ajouter le `ListItem` via la syntaxe déclarative. Si vous utilisez la `ListItem` éditeur de collections dans le concepteur, la syntaxe déclarative générée omet le `Value` paramètre complètement lorsque affecté à une chaîne vide, la création d’un balisage déclaratif comme : `<asp:ListItem>(None)</asp:ListItem>`. Alors que cela peut vous paraître inoffensif, manquants `Value` provoque DropDownList utiliser le `Text` valeur de la propriété à la place. Que signifie que si cela `NULL` `ListItem` est sélectionnée, la valeur (None) sera tentée à affecter au champ de données de produit (`CategoryID` ou `SupplierID`, dans ce didacticiel), ce qui entraînera une exception. En définissant explicitement `Value=""`, un `NULL` valeur sera affectée au produit de champ de données lorsque la `NULL` `ListItem` est sélectionnée.


Prenez un moment pour afficher la progression via un navigateur. Lors de la modification d’un produit, notez que le `Categories` et `Suppliers` compréhension des listes les deux ont la valeur (aucune) option au début de DropDownList.


[![Les catégories et la compréhension des listes fournisseurs comprennent un (aucun) Option](customizing-the-datalist-s-editing-interface-vb/_static/image29.png)](customizing-the-datalist-s-editing-interface-vb/_static/image28.png)

**La figure 10**: le `Categories` et `Suppliers` compréhension des listes incluent un (aucun) Option ([cliquez pour afficher l’image en taille réelle](customizing-the-datalist-s-editing-interface-vb/_static/image30.png))


Pour enregistrer option (aucun) en tant qu’une base de données `NULL` valeur, nous devons revenir à la `UpdateCommand` Gestionnaire d’événements. Modifier la `categoryIDValue` et `supplierIDValue` variables nullables entiers et les assigner une valeur autre que `Nothing` uniquement si le s DropDownList `SelectedValue` n’est pas une chaîne vide :


[!code-vb[Main](customizing-the-datalist-s-editing-interface-vb/samples/sample6.vb)]

Avec cette modification, la valeur `Nothing` sera passé dans le `UpdateProduct` option de méthode BLL si l’utilisateur sélectionné (aucun) à partir des listes de liste déroulante, qui correspond à un `NULL` de base de données de valeur.

## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons vu comment créer une interface de modification DataList plus complexe incluant trois contrôles Web d’entrée différents, une case à cocher, ainsi que des contrôles de validation, une zone de texte et compréhension des deux listes. Lors de la création de l’interface de modification, les étapes sont identiques pour tous les contrôles Web utilisés : commencez par ajouter les contrôles Web au contrôle DataList s `EditItemTemplate`; syntaxe de liaison de données permet d’attribuer les valeurs de champ de données correspondantes avec le site Web approprié Propriétés du contrôle ; puis, dans le `UpdateCommand` Gestionnaire d’événements, accéder par programmation à des contrôles Web et leurs propriétés appropriées, en passant leurs valeurs dans la couche BLL.

Lorsque vous créez une interface de modification, si elle s comprend des zones de texte ou une collection de contrôles Web différents, veillez à gérer correctement la base de données `NULL` valeurs. Pour la comptabilité de `NULL` s, il est impératif que vous n’est pas uniquement correctement Affrichez existant `NULL` valeur dans l’interface de modification, mais également que vous offrent un moyen de marquer une valeur en tant que `NULL`. Pour la compréhension des listes de DataList, en général, cela signifie Ajout statique `ListItem` dont `Value` propriété est définie explicitement sur une chaîne vide (`Value=""`) et en ajoutant un peu de code pour le `UpdateCommand` Gestionnaire d’événements pour déterminer ou non le `NULL``ListItem` a été sélectionné.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Randy Schmidt Dennis Patterson et David Suru. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](adding-validation-controls-to-the-datalist-s-editing-interface-vb.md)
