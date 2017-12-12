---
uid: web-forms/overview/data-access/working-with-batched-data/batch-updating-vb
title: "Lot de mise à jour (VB) | Documents Microsoft"
author: rick-anderson
description: "Découvrez comment mettre à jour de plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous créons un GridView où chaque ligne est modifiable. Dans les données en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: d191a204-d7ea-458d-b81c-0b9049ecb55f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/batch-updating-vb
msc.type: authoredcontent
ms.openlocfilehash: 02df858a7ad2ccefce4717e9bb7b08fc4c8d6ace
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="batch-updating-vb"></a>Lot de mise à jour (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_64_VB.zip) ou [télécharger le PDF](batch-updating-vb/_static/datatutorial64vb1.pdf)

> Découvrez comment mettre à jour de plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous créons un GridView où chaque ligne est modifiable. Dans la couche d’accès aux données, nous encapsuler toutes les opérations de mise à jour dans une transaction pour vous assurer que les mises à jour réussissent ou les mises à jour sont restaurées.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](wrapping-database-modifications-within-a-transaction-vb.md) nous avons vu comment étendre la couche d’accès aux données pour ajouter la prise en charge des transactions de base de données. Transactions de base de données garantissent qu’une série d’instructions de modification de données sera considérée comme une opération atomique, ce qui garantit que toutes les modifications échouent ou réussissent toutes. Avec cette couche DAL fonction de bas niveau disparaître, nous re prêt à présent créer des interfaces de modification de données de lot.

Dans ce didacticiel, nous allons construire un GridView où chaque ligne est modifiable (voir Figure 1). Étant donné que chaque ligne n’est rendue dans son interface de modification, cet emplacement s aucun nécessaire pour une colonne de la modifier, mettre à jour et annuler des boutons. Au lieu de cela, il existe deux boutons de mettre à jour des produits sur la page qui, lorsque vous cliquez dessus, énumérer les lignes GridView et mettre à jour de la base de données.


[![Chaque ligne dans le contrôle GridView est modifiable](batch-updating-vb/_static/image1.gif)](batch-updating-vb/_static/image1.png)

**Figure 1**: chaque ligne dans le contrôle GridView est modifiable ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image2.png))


Let s commencer !

> [!NOTE]
> Dans le [effectuer des mises à jour par lots](../editing-and-deleting-data-through-the-datalist/performing-batch-updates-vb.md) didacticiel, nous avons créé une modification de l’interface à l’aide du contrôle DataList. Ce didacticiel diffère de la précédente est qui utilise un GridView et la mise à jour par lots est effectuée dans l’étendue d’une transaction. Quand ils auront terminé ce didacticiel je vous encourage à retourner au didacticiel antérieur et mettre à jour pour utiliser les fonctionnalités liées aux transactions de base de données ajoutées dans le didacticiel précédent.


## <a name="examining-the-steps-for-making-all-gridview-rows-editable"></a>Examiner les étapes pour effectuer toutes les lignes de GridView modifiable

Comme indiqué dans le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) (didacticiel), le contrôle GridView offre une prise en charge intégrée pour la modification de ses données sous-jacentes sur une ligne par ligne. En interne, le contrôle GridView notes quelle ligne est modifiable via son [ `EditIndex` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.editindex(VS.80).aspx). Le contrôle GridView est lié à sa source de données, il vérifie si l’index de la ligne est égale à la valeur de chaque ligne `EditIndex`. Dans ce cas, des interfaces de ligne s champs sont rendus à l’aide de leur modification. Pour BoundFields, l’interface de modification est une zone de texte dont `Text` est affectée à la valeur du champ de données spécifié par le s BoundField `DataField` propriété. Pour TemplateField, le `EditItemTemplate` est utilisé à la place de la `ItemTemplate`.

Rappelez-vous que le flux de travail démarre lorsqu’un utilisateur clique sur un bouton de modification de ligne s. Cela entraîne une publication, définit le s GridView `EditIndex` propriété à l’index de ligne où vous avez cliqué s et Reliaisons les données à la grille. Lorsqu’une ligne s bouton Cancel, lors de la publication du `EditIndex` est défini sur une valeur de `-1` avant les données à la grille de reliaison. Étant donné que les lignes de s GridView démarrer l’indexation à zéro, le paramètre `EditIndex` à `-1` a pour effet d’afficher le contrôle GridView en mode lecture seule.

Le `EditIndex` propriété fonctionne bien pour la modification de la ligne, mais n’est pas conçue pour la modification du lot. Pour modifier le contrôle GridView entière, nous devons disposer chaque ligne à l’aide de son interface de modification. Le moyen le plus simple pour y parvenir consiste à créer où chaque champ modifiable est implémenté comme un TemplateField avec son interface de modification est défini dans le `ItemTemplate`.

Le prochain plusieurs étapes nous allons créer un GridView complètement modifiable. À l’étape 1 nous commencez par créer le contrôle GridView et son ObjectDataSource et convertir sa BoundFields et le CheckBoxField TemplateField. Dans les étapes 2 et 3, nous allons passer les interfaces de modification à partir de la TemplateField `EditItemTemplate` s pour leurs `ItemTemplate` s.

## <a name="step-1-displaying-product-information"></a>Étape 1 : Affichage des informations de produit

Avant que nous vous inquiétez pas sur la création d’un GridView où sont les lignes sont modifiables, permettent de s commencent par simplement afficher les informations de produit. Ouvrez le `BatchUpdate.aspx` page dans le `BatchData` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur. Définir le contrôle GridView s `ID` à `ProductsGrid` et, à partir de sa balise active, choisissez de lier à un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource pour récupérer ses données à partir de la `ProductsBLL` classe s `GetProducts` (méthode).


[![Configurer pour utiliser la classe ProductsBLL ObjectDataSource](batch-updating-vb/_static/image2.gif)](batch-updating-vb/_static/image3.png)

**Figure 2**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image4.png))


[![Récupérer les données de produit à l’aide de la méthode GetProducts](batch-updating-vb/_static/image3.gif)](batch-updating-vb/_static/image5.png)

**Figure 3**: récupérer les données de produit à l’aide de la `GetProducts` (méthode) ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image6.png))


Comme le GridView, les fonctionnalités de modification ObjectDataSource s sont conçues pour fonctionner sur une ligne par ligne. Pour mettre à jour un jeu d’enregistrements, nous devrons écrire du code dans la classe code-behind de pages ASP.NET qui traite les données et le passe à la couche BLL. Par conséquent, définir les listes déroulantes dans le s ObjectDataSource onglets UPDATE, INSERT et DELETE à (None). Cliquez sur Terminer pour terminer l’Assistant.


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](batch-updating-vb/_static/image4.gif)](batch-updating-vb/_static/image7.png)

**Figure 4**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image8.png))


Après avoir terminé l’Assistant Configurer la Source de données, le balisage déclaratif de s ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](batch-updating-vb/samples/sample1.aspx)]

Fin de l’Assistant Configurer la Source de données entraîne également Visual Studio pour créer des BoundFields et un CheckBoxField pour les champs de données de produit dans le GridView. Pour ce didacticiel, permettent d’autoriser uniquement l’utilisateur afficher et modifier le nom du produit s, catégorie, prix et état abandonné s. Supprimer tout sauf la `ProductName`, `CategoryName`, `UnitPrice`, et `Discontinued` champs et renommer le `HeaderText` propriétés des trois premiers champs de produits, de catégorie et de prix, respectivement. Finalement, activez les cases à cocher Activer la pagination et activer le tri dans la balise active de GridView s.

À ce stade du GridView a trois BoundFields (`ProductName`, `CategoryName`, et `UnitPrice`) et un CheckBoxField (`Discontinued`). Nous devons convertir ces quatre champs TemplateField, puis déplacez l’interface de modification s TemplateField `EditItemTemplate` à son `ItemTemplate`.

> [!NOTE]
> Nous avons exploré création et personnalisation de TemplateField dans le [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) didacticiel. Nous examinerons les étapes de conversion de la BoundFields et CheckBoxField en TemplateField et définir leur modification des interfaces dans leurs `ItemTemplate` s, mais si vous êtes bloqué ou devez un rappel, don t hésitent à faire référence à ce didacticiel antérieur.


À partir de la balise active de s GridView, cliquez sur le lien Modifier les colonnes pour ouvrir la boîte de dialogue champs. Ensuite, sélectionnez chaque champ et cliquez sur la convertir ce champ en TemplateField lien.


![Convertir le BoundFields existant et CheckBoxField TemplateField](batch-updating-vb/_static/image5.gif)

**Figure 5**: convertir le BoundFields existant et CheckBoxField TemplateField


Maintenant que chaque champ est TemplateField, nous re prêt à déplacer la modification de l’interface à partir de la `EditItemTemplate` s pour le `ItemTemplate` s.

## <a name="step-2-creating-theproductnameunitprice-anddiscontinuedediting-interfaces"></a>Étape 2 : Création du`ProductName`,`UnitPrice`, et`Discontinued`modification des Interfaces

Création de la `ProductName`, `UnitPrice`, et `Discontinued` modification des interfaces sont la rubrique de cette étape et sont relativement simples, comme chaque interface est déjà défini dans le s TemplateField `EditItemTemplate`. Création de la `CategoryName` modification d’interface est un peu plus complexe, car nous avons besoin créer une liste déroulante des catégories applicables. Cela `CategoryName` modification d’interface est abordé à l’étape 3.

S permettent de démarrer avec la `ProductName` TemplateField. Cliquez sur le lien Modifier les modèles à partir de la balise active de GridView s et accéder à la `ProductName` TemplateField s `EditItemTemplate`. Sélectionnez la zone de texte, copiez-le dans le Presse-papiers, puis collez-le dans le `ProductName` TemplateField s `ItemTemplate`. Modifier la zone de texte s `ID` propriété `ProductName`.

Ensuite, ajoutez un contrôle RequiredFieldValidator à la `ItemTemplate` pour vous assurer que l’utilisateur fournit une valeur pour chaque nom de produit s. Définir le `ControlToValidate` propriété ProductName, le `ErrorMessage` propriété, vous devez fournir le nom du produit. et le `Text` propriété \*. Après avoir apporté ces ajouts à la `ItemTemplate`, votre écran doit ressembler à la Figure 6.


[![ProductName TemplateField maintenant comprend une zone de texte et un contrôle RequiredFieldValidator](batch-updating-vb/_static/image6.gif)](batch-updating-vb/_static/image9.png)

**Figure 6**: le `ProductName` TemplateField inclut désormais une zone de texte et un RequiredFieldValidator ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image10.png))


Pour le `UnitPrice` édition interface, commencer par la copie de la zone de texte à partir de la `EditItemTemplate` à la `ItemTemplate`. Ensuite, placez un $ devant la zone de texte et le jeu de son `ID` propriété UnitPrice et son `Columns` propriété à 8.

Également ajouter un contrôle CompareValidator à la `UnitPrice` s `ItemTemplate` pour vous assurer que la valeur entrée par l’utilisateur est une valeur de devise valide supérieure ou égale à 0,00 $. Définir le validateur s `ControlToValidate` propriété UnitPrice, son `ErrorMessage` propriété, vous devez entrer une valeur de devise valide. Veuillez omettre les symboles de devises., son `Text` propriété \*, ses `Type` propriété `Currency`, ses `Operator` propriété `GreaterThanEqual`et son `ValueToCompare` 0 à la propriété.


[![Ajouter un CompareValidator pour garantir le prix entré est une valeur de devise Non négative](batch-updating-vb/_static/image7.gif)](batch-updating-vb/_static/image11.png)

**Figure 7**: ajouter un CompareValidator pour garantir le prix entré est une valeur de devise Non négative ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image12.png))


Pour le `Discontinued` TemplateField, vous pouvez utiliser la case à cocher déjà définie dans le `ItemTemplate`. Il suffit de définir son `ID` à abandonné et son `Enabled` propriété `True`.

## <a name="step-3-creating-thecategorynameediting-interface"></a>Étape 3 : Création du`CategoryName`modification d’Interface

L’interface de modification dans le `CategoryName` TemplateField s `EditItemTemplate` contient une zone de texte qui affiche la valeur de la `CategoryName` champ de données. Nous devons remplacer par une liste déroulante qui répertorie les catégories possibles.

> [!NOTE]
> Le [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) didacticiel contient une discussion plus approfondie et complète sur la personnalisation d’un modèle pour inclure une liste déroulante par opposition à une zone de texte. Alors que les étapes sont terminées, elles sont présentées tersely. Pour une plus approfondies sur la création et la configuration des catégories DropDownList, faire référence à la [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) didacticiel.


Faites glisser un contrôle DropDownList à partir de la boîte à outils vers la `CategoryName` TemplateField s `ItemTemplate`, ce qui affecte ses `ID` à `Categories`. À ce stade nous serait définissent généralement la source de données s compréhension des listes via sa balise active, création d’un nouveau ObjectDataSource. Toutefois, cette opération ajoute le ObjectDataSource dans le `ItemTemplate`, ce qui entraînera une instance de ObjectDataSource créée pour chaque ligne GridView. Au lieu de cela, permettre de s ObjectDataSource en dehors de la s GridView TemplateField. Fin de la modification de modèle et faites glisser un ObjectDataSource à partir de la boîte à outils vers le concepteur sous la `ProductsDataSource` ObjectDataSource. Nommez le nouveau ObjectDataSource `CategoriesDataSource` et configurez-le pour utiliser le `CategoriesBLL` classe s `GetCategories` (méthode).


[![Configurer pour utiliser la classe CategoriesBLL ObjectDataSource](batch-updating-vb/_static/image8.gif)](batch-updating-vb/_static/image13.png)

**Figure 8**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` classe ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image14.png))


[![Récupérer les données de catégorie à l’aide de la méthode GetCategories](batch-updating-vb/_static/image9.gif)](batch-updating-vb/_static/image15.png)

**Figure 9**: récupérer les données de catégorie à l’aide de la `GetCategories` (méthode) ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image16.png))


Étant donné que cette ObjectDataSource est utilisé simplement pour récupérer des données, définir les listes déroulantes dans les onglets UPDATE et DELETE à (None). Cliquez sur Terminer pour terminer l’Assistant.


[![Ensemble, les listes déroulantes dans la mise à jour et supprimer les tabulations à (None)](batch-updating-vb/_static/image10.gif)](batch-updating-vb/_static/image17.png)

**La figure 10**: définir les listes déroulantes dans la mise à jour et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image18.png))


Après la fin de l’Assistant, le `CategoriesDataSource` balisage déclaratif de s doit ressembler à ce qui suit :


[!code-aspx[Main](batch-updating-vb/samples/sample2.aspx)]

Avec la `CategoriesDataSource` créé et configuré, revenir à la `CategoryName` TemplateField s `ItemTemplate` et, à partir de la balise active de s DropDownList, cliquez sur le lien de choisir la Source de données. Dans l’Assistant Configuration de Source de données, sélectionnez le `CategoriesDataSource` option dans la première liste déroulante et choisissez d’avoir `CategoryName` utilisé pour l’affichage et `CategoryID` comme valeur.


[![Lier le CategoriesDataSource DropDownList](batch-updating-vb/_static/image11.gif)](batch-updating-vb/_static/image19.png)

**Figure 11**: lier DropDownList pour le `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image20.png))


À ce stade le `Categories` DropDownList répertorie toutes les catégories, mais il ne pas encore automatiquement sélectionne la catégorie appropriée pour le produit lié à la ligne GridView. Pour cela nous devons définir le `Categories` DropDownList s `SelectedValue` au produit s `CategoryID` valeur. Cliquez sur le lien Modifier les DataBindings à partir de la balise active de s DropDownList et associer le `SelectedValue` propriété avec le `CategoryID` de champ de données comme indiqué dans la Figure 12.


![Lier la valeur de CategoryID produit s à la propriété SelectedValue de s DropDownList](batch-updating-vb/_static/image12.gif)

**Figure 12**: lier le produit s `CategoryID` valeur au s DropDownList `SelectedValue` propriété


Une dernière reste de problème : si le produit n’a un `CategoryID` valeur spécifiées, l’instruction de la liaison de données sur `SelectedValue` entraîne une exception. Effet, DropDownList contient uniquement les éléments pour les catégories et n’offre pas une option pour les produits qui ont un `NULL` valeur pour la base de données `CategoryID`. Pour résoudre ce problème, définissez le s DropDownList `AppendDataBoundItems` propriété `True` et ajouter un nouvel élément à DropDownList, l’omission de la `Value` propriété à partir de la syntaxe déclarative. Autrement dit, assurez-vous que le `Categories` la syntaxe déclarative DropDownList s ressemble à celui-ci :


[!code-aspx[Main](batch-updating-vb/samples/sample3.aspx)]

Notez comment la `<asp:ListItem Value="">` a--sélectionner un--son `Value` attribut défini explicitement sur une chaîne vide. Faire référence à la [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) didacticiel pour une discussion plus approfondie sur la raison pour laquelle cet élément DropDownList supplémentaire est nécessaire pour gérer le `NULL` cas et pourquoi l’attribution du `Value` propriété à une chaîne vide est essentielle.

> [!NOTE]
> Il existe un potentiel performances et évolutivité problème ici qui est important de mentionner. Étant donné que chaque ligne a une liste déroulante qui utilise le `CategoriesDataSource` comme source de données, le `CategoriesBLL` classe s `GetCategories` méthode sera appelée  *n*  fois par page à consulter, où  *n*  est le nombre de lignes dans le GridView. Ces  *n*  appelle à `GetCategories` entraîner  *n*  requêtes à la base de données. Cet impact sur la base de données peut être atténué par la mise en cache les catégories retournés dans un cache par demande ou via la couche de mise en cache à l’aide d’une dépendance ou une expiration très courte temporels de la mise en cache de SQL. Pour plus d’informations sur la demande par la mise en cache d’option, consultez [ `HttpContext.Items` un magasin de Cache par demande](http://aspnet.4guysfromrolla.com/articles/060904-1.aspx).


## <a name="step-4-completing-the-editing-interface"></a>Étape 4 : Fin de l’Interface de modification

Nous ve effectué un nombre de modifications aux modèles s GridView sans marquer de pause pour afficher la progression. Prenez un moment pour afficher la progression via un navigateur. Comme le montre la Figure 13, chaque ligne est restitué à l’aide de son `ItemTemplate`, qui contient le s cellule modification d’interface.


[![Chaque ligne GridView est modifiable](batch-updating-vb/_static/image13.gif)](batch-updating-vb/_static/image21.png)

**Figure 13**: chaque ligne GridView est modifiable ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image22.png))


Il existe quelques problèmes mineurs mise en forme qui nous devons prendre en charge à ce stade. Tout d’abord, notez que le `UnitPrice` valeur contient quatre décimales. Pour résoudre ce problème, revenez à la `UnitPrice` TemplateField s `ItemTemplate` et, à partir de la balise active s de zone de texte, cliquez sur le lien Modifier les DataBindings. Ensuite, spécifiez que le `Text` propriété doit être mis en forme en tant que nombre.


![Mettre en forme la propriété de texte sous forme de nombre](batch-updating-vb/_static/image14.gif)

**La figure 14**: Format le `Text` propriété sous forme de nombre


En second lieu, s permettent de centre de la case à cocher dans la `Discontinued` colonne (plutôt qu’en fait, il aligné à gauche). Cliquez sur Modifier les colonnes à partir de la balise active de GridView s et sélectionnez le `Discontinued` TemplateField dans la liste de champs dans l’angle inférieur gauche. Explorez `ItemStyle` et définir le `HorizontalAlign` propriété Center, comme illustré dans la Figure 15.


![Centre de la case à cocher abandonné](batch-updating-vb/_static/image15.gif)

**Figure 15**: centre la `Discontinued` case à cocher


Ensuite, ajoutez un contrôle ValidationSummary à la page et définissez ses `ShowMessageBox` propriété `True` et son `ShowSummary` propriété `False`. Également ajouter que le Web Button des contrôles qui, lorsque vous cliquez sur, met à jour les modifications de s utilisateur. En particulier, ajoutez deux contrôles de bouton Web, un premier ci-dessus le GridView et en dessous, définissant les deux contrôles `Text` propriétés de la mise à jour de produits.

Depuis le s GridView modification d’interface est définie dans ses TemplateField `ItemTemplate` s, le `EditItemTemplate` s sont superflus et peuvent être supprimées.

Une fois que ce qui précède mentionné mise en forme, ajouter les contrôles bouton et suppression superflu `EditItemTemplate` s, la syntaxe déclarative votre page s doit se présenter comme suit :


[!code-aspx[Main](batch-updating-vb/samples/sample4.aspx)]

La figure 16 montre cette page lors de l’affichage via un navigateur une fois que les contrôles de bouton Web ont été ajoutés et les modifications de mise en forme.


[![La Page maintenant inclut deux boutons de produits de mise à jour](batch-updating-vb/_static/image16.gif)](batch-updating-vb/_static/image23.png)

**Figure 16**: la Page maintenant inclut deux mise à jour de produits boutons ([cliquez pour afficher l’image en taille réelle](batch-updating-vb/_static/image24.png))


## <a name="step-5-updating-the-products"></a>Étape 5 : Mise à jour des produits

Lorsqu’un utilisateur visite cette page, ils seront apporter leurs modifications et puis cliquez sur un des deux boutons de produits de mise à jour. À ce stade, nous devons d’une certaine manière enregistrer les valeurs entrées par l’utilisateur pour chaque ligne dans un `ProductsDataTable` de l’instance, puis passez-le à une méthode de la couche BLL qui transmet ensuite qui `ProductsDataTable` instance à la couche DAL s `UpdateWithTransaction` (méthode). Le `UpdateWithTransaction` (méthode), que nous avons créé dans le [didacticiel précédent](wrapping-database-modifications-within-a-transaction-vb.md), garantit que le lot de modifications sera mise à jour comme une opération atomique.

Créez une méthode nommée `BatchUpdate` dans `BatchUpdate.aspx.vb` et ajoutez le code suivant :


[!code-vb[Main](batch-updating-vb/samples/sample5.vb)]

Cette méthode commence par la mise en route de tous les produits dans un `ProductsDataTable` via un appel à la couche BLL s `GetProducts` (méthode). Il énumère ensuite la `ProductGrid` GridView s [ `Rows` collection](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.rows(VS.80).aspx). Le `Rows` collection contient un [ `GridViewRow` instance](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridviewrow.aspx) pour chaque ligne affichée dans le GridView. Étant donné que nous montrons au plus dix lignes par page, le contrôle GridView s `Rows` collection auront pas plus de dix éléments.

Pour chaque ligne le `ProductID` est retiré de la `DataKeys` approprié et collection `ProductsRow` est sélectionné dans le `ProductsDataTable`. Les contrôles d’entrée TemplateField quatre sont référencés par programmation et leurs valeurs assignées à le `ProductsRow` s propriétés de l’instance. Après chaque GridView les valeurs de ligne s ont été utilisées pour mettre à jour le `ProductsDataTable`, il s passé à la couche BLL s `UpdateWithTransaction` méthode qui, comme nous l’avons vu dans le didacticiel précédent, appelle simplement vers le bas dans la couche DAL s `UpdateWithTransaction` (méthode).

L’algorithme de mise à jour de lot utilisé pour ce didacticiel met à jour chaque ligne dans le `ProductsDataTable` qui correspond à une ligne dans le GridView, indépendamment de si les informations de produit s a été modifiées. Alors que ce blind des mises à jour ne sont pas toujours généralement un problème de performances, elles peuvent entraîner des enregistrements superflus si vous re l’audit des modifications à la table de base de données. Dans le [effectuer des mises à jour par lots](../editing-and-deleting-data-through-the-datalist/performing-batch-updates-vb.md) didacticiel nous exploré un lot de mise à jour d’interface avec le contrôle DataList et ajouté du code qui serait met à jour uniquement les enregistrements qui ont été réellement modifiés par l’utilisateur. N’hésitez pas à utiliser les techniques de [effectuer des mises à jour par lots](../editing-and-deleting-data-through-the-datalist/performing-batch-updates-vb.md) pour mettre à jour le code dans ce didacticiel, si vous le souhaitez.

> [!NOTE]
> Lors de la liaison de la source de données pour le contrôle GridView via sa balise active, Visual Studio assigne automatiquement les valeurs de clé primaire source s données au GridView s `DataKeyNames` propriété. Si vous n’avez pas lié ObjectDataSource au GridView via la balise active de GridView s comme indiqué à l’étape 1, vous devez définir manuellement le GridView s `DataKeyNames` propriété ProductID afin d’accéder à la `ProductID` valeur pour chaque ligne via le `DataKeys` collection.


Le code utilisé dans `BatchUpdate` est similaire à celle utilisée dans la couche BLL s `UpdateProduct` méthodes, la principale différence est que, dans le `UpdateProduct` méthodes qu’un seul `ProductRow` instance est récupérée à partir de l’architecture. Le code qui affecte les propriétés de la `ProductRow` est le même entre le `UpdateProducts` méthodes et le code dans le `For Each` boucle dans la `BatchUpdate`, que le modèle global.

Pour effectuer ce didacticiel, nous devons disposer le `BatchUpdate` méthode appelée lorsque vous cliquez sur un des boutons de produits de mise à jour. Créer des gestionnaires d’événements pour le `Click` événements de ces deux contrôles de bouton et ajoutez le code suivant dans les gestionnaires d’événements :


[!code-vb[Main](batch-updating-vb/samples/sample6.vb)]

Tout d’abord un appel est fait à `BatchUpdate`. Ensuite, le [ `ClientScript` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.page.clientscript(VS.80).aspx) est utilisée pour injecter du code JavaScript qui affiche un messagebox qui lit les produits ont été mis à jour.

Prenez une minute pour tester ce code. Visitez `BatchUpdate.aspx` via un navigateur, modifier un nombre de lignes et cliquez sur un des boutons de produits de mise à jour. En supposant qu’il n’y a aucune erreur de validation d’entrée, vous devez voir un messagebox qui lit que les produits ont été mis à jour. Pour vérifier la cohérence de la mise à jour, envisagez d’ajouter un élément aléatoire `CHECK` contrainte, qui n’autorise pas une `UnitPrice` valeurs de 1234.56. Puis, à partir de `BatchUpdate.aspx`, modifier un nombre d’enregistrements, en veillant à définir l’un des produit s `UnitPrice` valeur à la valeur interdite (1234.56). Cela doit entraîner une erreur lors de l’en cliquant sur les produits de mise à jour avec les autres modifications apportées au cours de cette opération de traitement par lots est revenu à leurs valeurs d’origine.

## <a name="an-alternativebatchupdatemethod"></a>Une Alternative`BatchUpdate`(méthode)

Le `BatchUpdate` méthode que nous avons examiné récupère *tous les* des produits à partir de la couche BLL s `GetProducts` méthode puis met à jour uniquement les enregistrements qui s’affichent dans le GridView. Cette approche est idéale si le contrôle GridView n’utilise pas la pagination, mais dans ce cas, il est peut-être des centaines, des milliers ou des dizaines de milliers de produits, mais seulement dix lignes dans le GridView. Dans ce cas, tous les produits à partir de la base de données uniquement pour modifier les 10 est loin d’être idéal.

Pour ces types de situations, envisagez d’utiliser les éléments suivants `BatchUpdateAlternate` méthode à la place :


[!code-vb[Main](batch-updating-vb/samples/sample7.vb)]

`BatchMethodAlternate`démarre en créant un nouveau vide `ProductsDataTable` nommé `products`. Parcourt les s GridView ensuite `Rows` collection et, pour chaque ligne Obtient les informations de produit spécifique à l’aide de la couche BLL s `GetProductByProductID(productID)` (méthode). Extraites `ProductsRow` instance possède des propriétés mises à jour de la même manière que `BatchUpdate`, mais après la mise à jour de la ligne, il est importé dans le `products` `ProductsDataTable` via le DataTable s [ `ImportRow(DataRow)` méthode](https://msdn.microsoft.com/en-us/library/system.data.datatable.importrow(VS.80).aspx).

Après le `For Each` boucle se termine, `products` contient un `ProductsRow` instance pour chaque ligne dans le GridView. Depuis chacun de la `ProductsRow` instances ont été ajoutés à la `products` (au lieu de mise à jour), si nous passer à l’aveugle à la `UpdateWithTransaction` (méthode) le `ProductsTableAdatper` essaie d’insérer des enregistrements dans la base de données. Au lieu de cela, nous devons spécifier que chacune de ces lignes ont été modifiée (ne pas ajouté).

Cela peut être accompli en ajoutant une nouvelle méthode à la couche BLL nommée `UpdateProductsWithTransaction`. `UpdateProductsWithTransaction`, jeux illustré ci-dessous, le `RowState` de chacune de la `ProductsRow` instances dans le `ProductsDataTable` à `Modified` , puis passe le `ProductsDataTable` à la couche DAL s `UpdateWithTransaction` (méthode).


[!code-vb[Main](batch-updating-vb/samples/sample8.vb)]

## <a name="summary"></a>Résumé

Le contrôle GridView fournit des fonctionnalités d’édition intégrées par ligne, mais ne dispose pas de prise en charge pour la création d’interfaces totalement modifiables. Comme nous l’avons vu dans ce didacticiel, ces interfaces sont possibles, mais nécessitent un peu de travail. Pour créer un GridView où chaque ligne est modifiable, nous devons convertir les champs de s GridView TemplateField et définir l’interface de modification dans le `ItemTemplate` s. En outre, mettre à jour tout - type de contrôle de bouton Web doit être ajouté à la page, distincte du contrôle GridView. Ces boutons `Click` gestionnaires d’événements doivent énumérer le GridView s `Rows` collecte, stocke les modifications dans un `ProductsDataTable`et passer les informations de mise à jour dans la méthode appropriée de la couche BLL.

Dans l’étape suivante du didacticiel, nous verrons comment créer une interface pour la suppression par lots. En particulier, chaque ligne GridView inclut une case à cocher et à la place de tout mettre à jour-type des boutons, nous allons comporter des boutons de supprimer les lignes sélectionnées.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et David Suru. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](wrapping-database-modifications-within-a-transaction-vb.md)
[Suivant](batch-deleting-vb.md)
