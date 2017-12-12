---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs
title: "Personnalisation de l’Interface de Modification de données (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons étudier comment personnaliser l’interface d’un GridView modifiable, en remplaçant la zone de texte standard et les contrôles de case à cocher avec secondaire..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 22e99600-8d18-4a94-a20e-a3a62bb63798
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs
msc.type: authoredcontent
ms.openlocfilehash: fb0b20ab25d87eddc0b2f9da786db469b16f861a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="customizing-the-data-modification-interface-c"></a>Personnalisation de l’Interface de Modification de données (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_20_CS.exe) ou [télécharger le PDF](customizing-the-data-modification-interface-cs/_static/datatutorial20cs1.pdf)

> Dans ce didacticiel, nous allons étudier comment personnaliser l’interface d’un GridView modifiable, en remplaçant la zone de texte standard et contrôle de case à cocher avec d’autres contrôles Web d’entrée.


## <a name="introduction"></a>Introduction

Le BoundFields et CheckBoxFields utilisée par les contrôles GridView et DetailsView simplifient le processus de modification des données en raison de leur capacité à effectuer le rendu des interfaces en lecture seule, modifiable et pouvant être insérés. Ces interfaces peuvent être rendus sans avoir besoin pour l’ajout de balisage déclaratif supplémentaire ou de code. Toutefois, les interfaces du BoundField et de CheckBoxField ne disposent pas de la personnalisation fréquemment utilisée dans les scénarios réels. Nous devons au lieu d’utiliser TemplateField afin de personnaliser l’interface modifiable ou pouvant être inséré dans un contrôle GridView ou le DetailsView.

Dans le [didacticiel précédent](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs.md) nous avons vu comment personnaliser les interfaces de modification de données en ajoutant des contrôles de validation Web. Dans ce didacticiel, nous allons examiner comment personnaliser les données réelles des contrôles Web collection, en remplaçant le BoundField et zone de texte standard de CheckBoxField et des contrôles de case à cocher avec d’autres contrôles Web d’entrée. En particulier, nous allons construire un GridView modifiable qui permet d’un produit nom, catégorie, fournisseur et état abandonné à mettre à jour. Lorsque vous modifiez une ligne particulière, les champs de catégorie et le fournisseur apparaîtront en tant que la compréhension des listes, contenant le jeu de catégories disponibles et les fournisseurs de. En outre, nous allons remplacer la valeur par défaut du CheckBoxField case à cocher avec un contrôle RadioButtonList offre deux options : « Active » et « Supprimée ».


[![Interface de modification du GridView inclut la compréhension des listes et des cases d’option](customizing-the-data-modification-interface-cs/_static/image2.png)](customizing-the-data-modification-interface-cs/_static/image1.png)

**Figure 1**: modification Interface inclut compréhension des listes et des cases d’option de GridView ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image3.png))


## <a name="step-1-creating-the-appropriateupdateproductoverload"></a>Étape 1 : Création approprié`UpdateProduct`de surcharge

Dans ce didacticiel, nous allons créer un GridView modifiable qui autorise la modification de nom d’un produit, catégorie, fournisseur et supprimé de statut. Par conséquent, nous devons un `UpdateProduct` surcharge qui accepte cinq paramètres d’entrée ces valeurs quatre produit ainsi que les `ProductID`. Comme dans nos surcharges précédentes, ce qui une permettra :

1. Récupérer les informations de produit de la base de données spécifié `ProductID`,
2. Mise à jour la `ProductName`, `CategoryID`, `SupplierID`, et `Discontinued` champs, et
3. Envoyer la demande de mise à jour à la couche DAL par le biais du TableAdapter `Update()` (méthode).

Par souci de concision, cette surcharge particulier, j’ai omis la vérification de la règle métier qui garantit un produit soit marqué comme supprimée n’est pas le seul produit offert par son fournisseur. N’hésitez pour l’ajouter dans si vous préférez, ou, dans l’idéal, refactorisez la logique à une méthode distincte.

Le code suivant illustre la nouvelle `UpdateProduct` surcharge dans les `ProductsBLL` classe :


[!code-csharp[Main](customizing-the-data-modification-interface-cs/samples/sample1.cs)]

## <a name="step-2-crafting-the-editable-gridview"></a>Étape 2 : Élaboration de GridView modifiable

Avec la `UpdateProduct` surcharge ajouté, nous sommes prêts à créer notre GridView modifiable. Ouvrez le `CustomizedUI.aspx` page dans le `EditInsertDelete` dossier et ajouter un contrôle GridView au concepteur. Ensuite, créez un nouveau ObjectDataSource à partir de la balise active du GridView. Configurer ObjectDataSource pour récupérer des informations de produit via le `ProductBLL` la classe `GetProducts()` (méthode) et mettre à jour les données de produit à l’aide du `UpdateProduct` surcharge que nous venons de créer. Dans les onglets INSERT et DELETE, sélectionnez (aucun) dans la liste déroulante.


[![Configurer l’ObjectDataSource pour utiliser la surcharge UpdateProduct venez de créer](customizing-the-data-modification-interface-cs/_static/image5.png)](customizing-the-data-modification-interface-cs/_static/image4.png)

**Figure 2**: configurer l’ObjectDataSource à utiliser le `UpdateProduct` surcharge venez de créer ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image6.png))


Comme nous l’avons vu dans les didacticiels de modification de données, la syntaxe déclarative pour ObjectDataSource créé par Visual Studio affecte la `OldValuesParameterFormatString` propriété `original_{0}`. Cette opération, bien entendu, ne fonctionne pas avec notre couche de logique métier car nos méthodes ne prévoyez pas d’origine `ProductID` valeur à passer dans. Par conséquent, comme nous l’avons fait dans les didacticiels précédents, prenez le temps de supprimer cette affectation de la propriété de la syntaxe déclarative ou, au lieu de cela, définissez la valeur de cette propriété `{0}`.

Après cette modification, balisage déclaratif de l’ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample2.aspx)]

Notez que la `OldValuesParameterFormatString` propriété a été supprimée et qu’il existe un `Parameter` dans les `UpdateParameters` collection pour chacun des paramètres d’entrée attendus par notre `UpdateProduct` de surcharge.

Pendant la configuration de ObjectDataSource pour mettre à jour uniquement un sous-ensemble des valeurs de produit, le contrôle GridView affiche actuellement *tous les* des champs de produit. Prenez un moment pour modifier le contrôle GridView afin que :

- Elle inclut uniquement le `ProductName`, `SupplierName`, `CategoryName` BoundFields et `Discontinued` CheckBoxField
- Le `CategoryName` et `SupplierName` champs devant apparaître avant (à gauche de) la `Discontinued` CheckBoxField
- Le `CategoryName` et `SupplierName` 'BoundFields `HeaderText` est définie sur « Catégorie » et « Fournisseur », respectivement
- Prise en charge de la modification est activée (case à cocher Activer la modification dans la balise active du GridView)

Après ces modifications, le concepteur doit ressembler à la Figure 3, avec la syntaxe déclarative de GridView indiquée ci-dessous.


[![Supprimer les champs inutiles du contrôle GridView](customizing-the-data-modification-interface-cs/_static/image8.png)](customizing-the-data-modification-interface-cs/_static/image7.png)

**Figure 3**: supprime les champs inutiles de GridView ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image9.png))


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample3.aspx)]

À ce stade le comportement de lecture seule du GridView est terminée. Lorsque vous affichez les données, chaque produit est restitué sous la forme d’une ligne dans le GridView, affichant le nom du produit, catégorie, fournisseur et abandonné l’état.


[![Interface d’en lecture seule du GridView est terminée.](customizing-the-data-modification-interface-cs/_static/image11.png)](customizing-the-data-modification-interface-cs/_static/image10.png)

**Figure 4**: Interface d’en lecture seule du GridView est terminée ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image12.png))


> [!NOTE]
> Comme indiqué dans [une vue d’ensemble d’insertion, de mise à jour et de suppression des données didacticiel](an-overview-of-inserting-updating-and-deleting-data-cs.md), il est essentiel que le contrôle GridView s vue état activé (comportement par défaut). Si vous définissez le GridView s `EnableViewState` propriété `false`, vous courez le risque d’avoir des utilisateurs simultanés involontairement supprimer ou de modifier des enregistrements. Consultez [Avertissement : problème de concurrence avec ASP.NET 2.0 contrôles GridView/DetailsView/FormViews cette modification de la prise en charge et/ou la suppression et la dont l’état d’affichage est désactivé](http://scottonwriting.net/sowblog/posts/10054.aspx) pour plus d’informations.


## <a name="step-3-using-a-dropdownlist-for-the-category-and-supplier-editing-interfaces"></a>Étape 3 : Utiliser un DropDownList pour la catégorie et la modification des Interfaces de fournisseur

N’oubliez pas que le `ProductsRow` objet contient `CategoryID`, `CategoryName`, `SupplierID`, et `SupplierName` propriétés qui fournissent les valeurs d’ID de clé étrangère réelles dans le `Products` correspondant et la table de base de données `Name` les valeurs dans le `Categories` et `Suppliers` tables. Le `ProductRow`de `CategoryID` et `SupplierID` peuvent tous deux être lu et écrits, tandis que la `CategoryName` et `SupplierName` propriétés sont marquées en lecture seule.

En raison de l’état en lecture seule de la `CategoryName` et `SupplierName` propriétés, les BoundFields correspondants ont leurs `ReadOnly` propriété la valeur `true`, empêche que ces valeurs en cours de modification lorsqu’une ligne est modifiée. Pendant que nous puissions définir le `ReadOnly` propriété `false`, rendu le `CategoryName` et `SupplierName` BoundFields comme des zones de texte lors de la modification, une telle approche entraîne une exception lorsque l’utilisateur tente de mettre à jour du produit, car il est aucun `UpdateProduct` surcharge dans `CategoryName` et `SupplierName` entrées. En fait, nous ne voulons pas créer une telle surcharge pour deux raisons :

- Le `Products` table ne contient pas `SupplierName` ou `CategoryName` champs, mais `SupplierID` et `CategoryID`. Par conséquent, nous souhaitons notre méthode à passer les valeurs ID, pas les valeurs des tables de leur choix.
- Demandant à l’utilisateur à taper le nom du fournisseur ou la catégorie est inférieure à la solution idéale, car il nécessite de l’utilisateur, à savoir les catégories disponibles et les fournisseurs et leurs orthographes.

Les champs de catégorie et le fournisseur doivent afficher la catégorie et les noms de fournisseurs en mode lecture seule (comme il le fait maintenant) et une liste déroulante des options applicables quand en cours de modification. À l’aide d’une liste déroulante, l’utilisateur final peut voir rapidement les catégories et les fournisseurs sont disponibles pour choisir entre et plus facilement faire en conséquence.

Pour fournir ce comportement, nous devons convertir les `SupplierName` et `CategoryName` BoundFields dans TemplateField dont `ItemTemplate` émet le `SupplierName` et `CategoryName` valeurs et dont `EditItemTemplate` utilise un contrôle DropDownList pour la liste la catégories disponibles et les fournisseurs.

## <a name="adding-thecategoriesandsuppliersdropdownlists"></a>Ajout de la`Categories`et`Suppliers`compréhension des listes

Commencez par convertir le `SupplierName` et `CategoryName` BoundFields dans TemplateField par : en cliquant sur le lien Modifier les colonnes à partir de la balise active du GridView ; en sélectionnant le BoundField à partir de la liste dans l’angle inférieur gauche ; et en cliquant sur le « convertir ce champ en un Lien de TemplateField ». Le processus de conversion créera TemplateField avec à la fois un `ItemTemplate` et un `EditItemTemplate`, comme illustré dans la syntaxe déclarative ci-dessous :


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample4.aspx)]

Étant donné que le BoundField a été marqué en lecture seule, à la fois le `ItemTemplate` et `EditItemTemplate` contiennent un site Web d’étiquette contrôle dont `Text` propriété est liée au champ de données applicable (`CategoryName`, dans la syntaxe ci-dessus). Nous devons modifier le `EditItemTemplate`, en remplaçant le contrôle Web Label avec un contrôle DropDownList.

Comme nous l’avons vu dans les didacticiels précédents, le modèle peut être modifié via le concepteur ou directement à partir de la syntaxe déclarative. Pour le modifier via le concepteur, cliquez sur le lien Modifier les modèles à partir de la balise active du GridView, choisissez de travailler avec le champ de catégorie `EditItemTemplate`. Supprimer le contrôle Web Label et remplacez-le par un contrôle DropDownList, propriété d’ID de DropDownList `Categories`.


[![Supprimer le TextBox et ajouter une liste déroulante pour le modèle EditItemTemplate](customizing-the-data-modification-interface-cs/_static/image14.png)](customizing-the-data-modification-interface-cs/_static/image13.png)

**Figure 5**: supprimez le TextBox et ajouter une liste déroulante pour le `EditItemTemplate` ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image15.png))


Nous devons ensuite remplir DropDownList avec les catégories disponibles. Cliquez sur le lien de choisir la Source de données à partir de la balise de DropDownList et choisir de créer un nouveau ObjectDataSource nommé `CategoriesDataSource`.


[![Créer un contrôle ObjectDataSource nommé CategoriesDataSource](customizing-the-data-modification-interface-cs/_static/image17.png)](customizing-the-data-modification-interface-cs/_static/image16.png)

**Figure 6**: créer un nouveau contrôle ObjectDataSource nommé `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image18.png))


Pour que cette ObjectDataSource retourner toutes les catégories, liez-le à la `CategoriesBLL` la classe `GetCategories()` (méthode).


[![Lier GetCategories() méthode du CategoriesBLL ObjectDataSource](customizing-the-data-modification-interface-cs/_static/image20.png)](customizing-the-data-modification-interface-cs/_static/image19.png)

**Figure 7**: lier le ObjectDataSource à la `CategoriesBLL`de `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image21.png))


Enfin, configurez les paramètres de DropDownList telles que la `CategoryName` champ s’affiche dans chaque DropDownList `ListItem` avec la `CategoryID` utilisée comme valeur de champ.


[![Avoir le champ CategoryName affiché et CategoryID utilisée comme valeur](customizing-the-data-modification-interface-cs/_static/image23.png)](customizing-the-data-modification-interface-cs/_static/image22.png)

**Figure 8**: ont le `CategoryName` champ affiché et `CategoryID` utilisée comme valeur ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image24.png))


Une fois ces modifications le balisage déclaratif pour le `EditItemTemplate` dans le `CategoryName` TemplateField inclut une liste déroulante et un ObjectDataSource :


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample5.aspx)]

> [!NOTE]
> DropDownList dans le `EditItemTemplate` doit avoir son état d’affichage. Syntaxe de liaison de données sera bientôt ajoutée à la syntaxe déclarative de DropDownList et commandes de la liaison de données telles que `Eval()` et `Bind()` ne peut apparaître dans les contrôles dont l’état d’affichage est activé.


Répétez ces étapes pour ajouter une liste déroulante nommée `Suppliers` à la `SupplierName` de TemplateField `EditItemTemplate`. Il s’agira d’ajout d’une liste déroulante pour la `EditItemTemplate` et la création d’un autre ObjectDataSource. Le `Suppliers` ObjectDataSource de DropDownList, toutefois, doit être configuré pour appeler le `SuppliersBLL` la classe `GetSuppliers()` (méthode). En outre, configurez le `Suppliers` DropDownList pour afficher le `CompanyName` champ et utilisez le `SupplierID` champ comme valeur pour son `ListItem` s.

Après avoir ajouté les compréhension des listes pour les deux `EditItemTemplate` s, charger la page dans un navigateur et cliquez sur le bouton Modifier pour le produit de Cajun Seasoning du Chef Anton. Comme le montre la Figure 9, les colonnes de catégorie et le fournisseur du produit sont rendus sous forme de liste déroulante contenant les catégories disponibles et les fournisseurs de. Toutefois, notez que le *premier* éléments dans les deux listes déroulantes sont sélectionnées par défaut (boissons pour la catégorie) et liquides exotiques en tant que fournisseur, bien que Cajun Seasoning de Chef Anton est un condiments fournie par New Orleans Cajun Delights.


[![Le premier élément dans la liste déroulante répertorie est sélectionné par défaut](customizing-the-data-modification-interface-cs/_static/image26.png)](customizing-the-data-modification-interface-cs/_static/image25.png)

**Figure 9**: le premier élément dans la liste déroulante répertorie est sélectionné par défaut ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image27.png))


En outre, si vous cliquez sur Mettre à jour, vous constatez que du produit `CategoryID` et `SupplierID` ont la valeur `NULL`. Ces deux éléments indésirables comportements sont dû aux compréhension des listes dans le `EditItemTemplate` s ne sont pas liés à tous les champs de données à partir des données de produit sous-jacent.

## <a name="binding-the-dropdownlists-to-thecategoryidandsupplieriddata-fields"></a>Les compréhension des listes à la liaison la`CategoryID`et`SupplierID`des champs de données

Pour disposer de catégorie et le fournisseur du produit modifié listes déroulantes, définir les valeurs appropriées et que ces valeurs renvoyées à la couche BLL `UpdateProduct` méthode lorsque vous cliquez sur la mise à jour, nous avons besoin lier la compréhension des listes `SelectedValue` propriétés pour le `CategoryID` et `SupplierID` des champs de données à l’aide de la liaison de données bidirectionnelle. Pour y parvenir avec la `Categories` DropDownList, vous pouvez ajouter `SelectedValue='<%# Bind("CategoryID") %>'` directement à la syntaxe déclarative.

Vous pouvez également définir les databindings du DropDownList en la modification du modèle par le biais du concepteur en cliquant sur le lien Modifier les DataBindings à partir de la balise de DropDownList. Ensuite, indiquent que le `SelectedValue` propriété doit être liée à la `CategoryID` champ à l’aide de la liaison de données bidirectionnelle (voir la Figure 10). Répétez le processus déclaratif ou de conception pour lier le `SupplierID` champ de données à le `Suppliers` DropDownList.


[![Lier la CategoryID à la propriété SelectedValue de DropDownList à l’aide de la liaison de données bidirectionnelle](customizing-the-data-modification-interface-cs/_static/image29.png)](customizing-the-data-modification-interface-cs/_static/image28.png)

**La figure 10**: lier le `CategoryID` à de DropDownList `SelectedValue` propriété à l’aide de bidirectionnel de liaison de données ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image30.png))


Une fois que les liaisons ont été appliqués à la `SelectedValue` des propriétés des compréhension des deux listes, les colonnes de catégorie et le fournisseur du produit modifié une valeur par défaut les valeurs actuelles du produit. Lorsque vous cliquez sur la mise à jour, le `CategoryID` et `SupplierID` les valeurs de l’élément de liste déroulante sélectionnée seront passées à la `UpdateProduct` (méthode). La figure 11 illustre le didacticiel après ont ajouté les instructions de liaison de données ; Notez la façon dont les éléments de liste déroulante sélectionnée pour Cajun Seasoning du Chef Anton sont correctement condiments et New Orleans Cajun Delights.


[![Catégorie actuelle du produit modifié et des valeurs du fournisseur sont sélectionnées par défaut](customizing-the-data-modification-interface-cs/_static/image32.png)](customizing-the-data-modification-interface-cs/_static/image31.png)

**Figure 11**: la modification du produit catégorie actuelle et valeurs du fournisseur sont sélectionnées par défaut ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image33.png))


## <a name="handlingnullvalues"></a>La gestion des`NULL`valeurs

Le `CategoryID` et `SupplierID` colonnes dans le `Products` table peut être `NULL`, mais les compréhension des listes dans le `EditItemTemplate` s n’incluez pas un élément de liste pour représenter un `NULL` valeur. Cela a deux conséquences :

- Utilisateur ne peut pas utiliser notre interface pour modifier la catégorie d’un produit ou le fournisseur de non -`NULL` valeur un `NULL` un
- Si un produit a un `NULL` `CategoryID` ou `SupplierID`, en cliquant sur le bouton Modifier entraîne une exception. C’est parce que le `NULL` valeur retournée par `CategoryID` (ou `SupplierID`) dans le `Bind()` instruction ne mappe pas à une valeur dans la liste DropDownList (DropDownList lève une exception lors de son `SelectedValue` est définie sur une valeur *pas* dans sa collection d’éléments de liste).

Pour prendre en charge `NULL` `CategoryID` et `SupplierID` valeurs, nous devons ajouter une autre `ListItem` à chaque DropDownList pour représenter la `NULL` valeur. Dans le [filtrage de maître/détail avec un DropDownList](../masterdetail/master-detail-filtering-with-a-dropdownlist-cs.md) didacticiel, nous avons vu comment ajouter un autre `ListItem` à un DropDownList lié aux données, qui impliquait la définition de DropDownList `AppendDataBoundItems` propriété `true` et Ajout manuel supplémentaires `ListItem`. Dans ce didacticiel précédent, toutefois, nous avons ajouté un `ListItem` avec un `Value` de `-1`. Toutefois, la logique de liaison de données dans ASP.NET, convertit automatiquement une chaîne vide pour un `NULL` valeur et inversement a. Par conséquent, pour ce didacticiel que nous souhaitons le `ListItem`de `Value` pour être une chaîne vide.

Commencez par définir les deux compréhension des listes `AppendDataBoundItems` propriété `true`. Ensuite, ajoutez le `NULL` `ListItem` en ajoutant le code suivant `<asp:ListItem>` élément à chaque DropDownList afin que le balisage déclaratif ressemble, tels que :


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample6.aspx)]

J’ai choisi d’utiliser « (None) » en tant que la valeur de texte pour ce `ListItem`, mais vous pouvez modifier pour également être une chaîne vide si vous le souhaitez.

> [!NOTE]
> Comme nous l’avons vu dans la *filtrage de maître/détail avec un DropDownList* (didacticiel), `ListItem` peuvent être ajoutées à une liste déroulante dans le concepteur en cliquant sur de DropDownList `Items` propriété dans la fenêtre Propriétés (qui Affiche la `ListItem` éditeur de Collection). Toutefois, veillez à ajouter le `NULL` `ListItem` pour ce didacticiel via la syntaxe déclarative. Si vous utilisez la `ListItem` éditeur de collections, la syntaxe déclarative générée omet le `Value` paramètre complètement lorsque affecté à une chaîne vide, la création d’un balisage déclaratif comme : `<asp:ListItem>(None)</asp:ListItem>`. Si cela peut vous paraître inoffensif, la valeur manquante n’entraîne DropDownList à utiliser le `Text` valeur de la propriété à la place. Qui signifie que si cela `NULL` `ListItem` est sélectionnée, la valeur « (None) » sera tentée à affecter à la `CategoryID`, ce qui entraînera une exception. En définissant explicitement `Value=""`, un `NULL` valeur sera assignée à `CategoryID` lors de la `NULL` `ListItem` est sélectionnée.


Répétez ces étapes pour les fournisseurs DropDownList.

Avec cette supplémentaires `ListItem`, l’interface de modification peut désormais affecter `NULL` valeurs d’un produit `CategoryID` et `SupplierID` des champs, comme indiqué dans la Figure 12.


[![Choisissez (aucun) pour affecter une valeur NULL pour la catégorie d’un produit ou le fournisseur](customizing-the-data-modification-interface-cs/_static/image35.png)](customizing-the-data-modification-interface-cs/_static/image34.png)

**Figure 12**: choisissez (aucun) à affecter un `NULL` valeur pour la catégorie d’un produit ou le fournisseur ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image36.png))


## <a name="step-4-using-radiobuttons-for-the-discontinued-status"></a>Étape 4 : À l’aide de cases d’option pour l’état abandonné

Actuellement les produits `Discontinued` champ de données est exprimée à l’aide d’un CheckBoxField, qui restitue une case à cocher désactivée pour les lignes en lecture seule et une case à cocher activée pour la ligne en cours de modification. Alors que cette interface utilisateur s’avère souvent appropriée, nous pouvons le personnaliser si nécessaire à l’aide d’un TemplateField. Pour ce didacticiel, nous allons modifier le CheckBoxField en TemplateField qui utilise un contrôle RadioButtonList avec deux options « Actives » et « Supprimé » à partir de laquelle l’utilisateur peut spécifier du produit `Discontinued` valeur.

Commencez par convertir le `Discontinued` CheckBoxField en TemplateField, ce qui créera un TemplateField avec un `ItemTemplate` et `EditItemTemplate`. Les deux modèles incluent une case à cocher avec son `Checked` propriété liée à la `Discontinued` de champ de données, la seule différence entre les deux en cours qui le `ItemTemplate`de la case à cocher `Enabled` est définie sur `false`.

Remplacez la case à cocher à la fois dans le `ItemTemplate` et `EditItemTemplate` avec un contrôle RadioButtonList, paramètre à la fois RadioButtonList `ID` propriétés `DiscontinuedChoice`. Ensuite, indiquer la RadioButtonList doit contenant chacun deux cases d’option, un étiqueté « actif » avec la valeur « False » et un intitulé « Supprimé » avec la valeur « True ». Pour cela que vous pouvez soit entrer le `<asp:ListItem>` éléments directement par le biais de la syntaxe déclarative ou utilisez le `ListItem` éditeur de collections à partir du concepteur. Figure 13 illustre le `ListItem` éditeur de Collection une fois que les deux cases d’option options de bouton ont été spécifiés.


[![Ajouter](customizing-the-data-modification-interface-cs/_static/image38.png)](customizing-the-data-modification-interface-cs/_static/image37.png)

**Figure 13**: ajouter des « Actif » et « Discontinued » Options à RadioButtonList ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image39.png))


Depuis RadioButtonList dans le `ItemTemplate` ne doit pas être modifiée, définir son `Enabled` propriété `false`, laissant le `Enabled` propriété `true` (la valeur par défaut) pour RadioButtonList dans le `EditItemTemplate`. Cela rendra les cases d’option dans la ligne non modifiée en lecture seule, mais permet à l’utilisateur à modifier les valeurs de la case d’option pour la ligne modifiée.

Nous devons encore affecter des contrôles RadioButtonList `SelectedValue` afin que le bouton radio approprié est sélectionné en fonction du produit `Discontinued` champ de données. Comme avec la compréhension des listes examiné précédemment dans ce didacticiel, cette syntaxe de liaison de données peut être ajoutée directement dans le balisage déclaratif ou via le lien Modifier les DataBindings dans les balises actives du RadioButtonList.

Après avoir ajouté les deux RadioButtonList et de les configurer, le `Discontinued` balisage déclaratif de TemplateField doit ressembler à :


[!code-aspx[Main](customizing-the-data-modification-interface-cs/samples/sample7.aspx)]

Avec ces modifications, la `Discontinued` colonne ont été transformée dans une liste de cases à cocher pour une liste de paires de bouton radio (voir Figure 14). Lorsque vous modifiez un produit, le bouton radio approprié est sélectionné et l’état du produit supprimées peut être mises à jour en cochant la case autres en cliquant sur la mise à jour.


[![Les cases à cocher abandonnés ont été remplacés par des paires de bouton Radio](customizing-the-data-modification-interface-cs/_static/image41.png)](customizing-the-data-modification-interface-cs/_static/image40.png)

**La figure 14**: l’abandonné cases à cocher ont été remplacés par les paires de bouton Radio ([cliquez pour afficher l’image en taille réelle](customizing-the-data-modification-interface-cs/_static/image42.png))


> [!NOTE]
> Étant donné que la `Discontinued` colonne dans la `Products` base de données ne peut pas avoir `NULL` valeurs, nous n’avez pas besoin à vous soucier de capture `NULL` informations dans l’interface. If, toutefois, `Discontinued` colonne peut contenir `NULL` valeurs nous voulons ajouter une troisième case d’option à la liste avec ses `Value` définie sur une chaîne vide (`Value=""`), à l’instar d’avec la catégorie et la compréhension des listes de fournisseur.


## <a name="summary"></a>Résumé

Tandis que les BoundField et CheckBoxField automatiquement sont affichés en lecture seule, modification, insertion d’interfaces, ils n’ont pas la possibilité de personnalisation. Souvent, cependant, nous allons devoir personnaliser la modification ou insertion d’interface, peut-être en ajoutant des contrôles de validation (comme nous l’avons vu dans le didacticiel précédent) ou en personnalisant l’interface utilisateur de collecte de données (comme nous l’avons vu dans ce didacticiel). Personnalisation de l’interface avec TemplateField peut être résumée dans les étapes suivantes :

1. Ajouter TemplateField ou convertir un BoundField existant ou le CheckBoxField en TemplateField
2. Augmenter l’interface en fonction des besoins
3. Lier les champs de données approprié pour les contrôles Web qui vient d’être ajoutés à l’aide de la liaison de données bidirectionnelle

Outre l’utilisation de contrôles Web ASP.NET intégrés, vous pouvez également personnaliser les modèles de TemplateField avec les contrôles serveur personnalisé, compilé et les contrôles utilisateur.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs.md)
[Suivant](implementing-optimistic-concurrency-cs.md)
