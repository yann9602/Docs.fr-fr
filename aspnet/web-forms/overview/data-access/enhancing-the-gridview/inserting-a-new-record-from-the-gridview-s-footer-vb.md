---
uid: web-forms/overview/data-access/enhancing-the-gridview/inserting-a-new-record-from-the-gridview-s-footer-vb
title: "Insertion d’un nouvel enregistrement à partir d’un pied de page du GridView (VB) | Documents Microsoft"
author: rick-anderson
description: "Alors que le contrôle GridView ne fournit pas de prise en charge intégrée pour l’insertion d’un nouvel enregistrement de données, ce didacticiel montre comment augmenter le contrôle GridView à inclure un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/06/2007
ms.topic: article
ms.assetid: 528acc48-f20c-4b4e-aa16-4cc02f068ebb
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/enhancing-the-gridview/inserting-a-new-record-from-the-gridview-s-footer-vb
msc.type: authoredcontent
ms.openlocfilehash: 4d452e15ced52fd9dcac8201598146cb9ef38d7b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="inserting-a-new-record-from-the-gridviews-footer-vb"></a>Insertion d’un nouvel enregistrement à partir d’un pied de page du GridView (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_53_VB.exe) ou [télécharger le PDF](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/datatutorial53vb1.pdf)

> Alors que le contrôle GridView ne fournit pas de prise en charge intégrée pour l’insertion d’un nouvel enregistrement de données, ce didacticiel montre comment augmenter le GridView pour inclure une interface d’insertion.


## <a name="introduction"></a>Introduction

Comme indiqué dans le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) didacticiel, les contrôles GridView, DetailsView et FormView Web incluent des fonctionnalités de modification de données intégrés. Lorsqu’il est utilisé avec les contrôles de source de données déclarative, ces trois contrôles Web peuvent être rapidement et facilement configurés pour modifier des données - dans des scénarios sans avoir à écrire une seule ligne de code. Malheureusement, seuls les contrôles DetailsView et FormView fournissent intégrés insertion, modification et suppression de capacités. Le contrôle GridView offre uniquement la modification et suppression de prise en charge. Toutefois, avec un peu graisse de coude, nous pouvons augmenter le GridView pour inclure une interface d’insertion.

En ajoutant des fonctionnalités d’insertion le GridView, nous ne sommes responsables pour décider de nouveaux enregistrements est ajouté, création de l’interface d’insertion et l’écriture du code pour insérer le nouvel enregistrement. Dans ce didacticiel, que nous allons nous intéresser à l’ajout de l’interface d’insertion pour le pied de page s GridView ligne (voir Figure 1). La cellule de pied de page pour chaque colonne inclut des données appropriées collection élément d’interface utilisateur (une zone de texte pour le nom de produit s, une liste déroulante pour le fournisseur et ainsi de suite). Nous avons également besoin d’une colonne pour un ajout bouton qui, quand vous cliquez dessus, entraîne une publication (postback) et insérer un nouvel enregistrement dans le `Products` table en utilisant les valeurs fournies dans la ligne de pied de page.


[![La ligne de pied de page fournit une Interface pour l’ajout de nouveaux produits](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image1.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image1.png)

**Figure 1**: la ligne de pied de page fournit une Interface pour l’ajout de nouveaux produits ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image2.png))


## <a name="step-1-displaying-product-information-in-a-gridview"></a>Étape 1 : Affichage des informations sur les produits dans un GridView

Avant que nous nous concernent avec la création de l’interface de l’insertion dans le pied de page s GridView, permettent de s commencez par vous concentrer sur l’ajout d’un contrôle GridView à la page qui répertorie les produits dans la base de données. Commencez par ouvrir le `InsertThroughFooter.aspx` page dans le `EnhancedGridView` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, en définissant le GridView s `ID` propriété `Products`. Ensuite, utilisez la balise active de GridView s pour le lier à un nouveau ObjectDataSource nommé `ProductsDataSource`.


[![Créer un nouveau ObjectDataSource nommé ProductsDataSource](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image2.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image3.png)

**Figure 2**: créer un nouveau nommé de ObjectDataSource `ProductsDataSource` ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image4.png))


Configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe s `GetProducts()` méthode pour récupérer des informations sur le produit. Pour ce didacticiel, vous permettre s strictement sur l’ajout de fonctionnalités d’insertion et ne vous inquiétez pas sur la modification et la suppression. Par conséquent, assurez-vous que la liste déroulante dans l’onglet Insérer est définie sur `AddProduct()` et que les listes déroulantes dans les onglets UPDATE et DELETE sont définies à (None).


[![Mappage de la méthode AddProduct à la méthode de Insert() s ObjectDataSource](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image3.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image5.png)

**Figure 3**: mappage du `AddProduct` méthode au s ObjectDataSource `Insert()` (méthode) ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image6.png))


[![Définir les listes déroulantes de mise à jour et supprimer les tabulations à (None)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image4.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image7.png)

**Figure 4**: définir la mise à jour et supprimer des onglets de liste déroulante répertorie à (None) ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image8.png))


À l’issue de l’Assistant Configurer la Source de données de s ObjectDataSource, Visual Studio ajoute automatiquement les champs pour le contrôle GridView pour chacun des champs de données correspondants. Pour l’instant, laissez tous les champs ajoutés par Visual Studio. Plus loin dans ce didacticiel, nous allons y revenir et supprimer certains des champs dont les valeurs ne pas doivent être spécifiés lors de l’ajout d’un nouvel enregistrement.

Étant donné que proche de 80 produits dans la base de données, un utilisateur aura faire défiler jusqu’au bas de la page web afin d’ajouter un nouvel enregistrement. Par conséquent, permettent de s’activer la pagination rendre l’interface de l’insertion plus visibles et accessibles. Pour activer la pagination, cochez simplement la case à cocher Activer la pagination de la balise active de GridView s.

À ce stade, le balisage déclaratif s GridView et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample1.aspx)]


[![Tous les champs de données de produit sont affichés dans un contrôle GridView de réserve paginée](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image5.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image9.png)

**Figure 5**: tous les champs de données de produit sont affichés dans un GridView réserve paginée ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image10.png))


## <a name="step-2-adding-a-footer-row"></a>Étape 2 : Ajout d’une ligne de pied de page

Avec son en-tête et les lignes de données, le contrôle GridView inclut une ligne de pied de page. Les lignes d’en-tête et pied de page sont affichées en fonction des valeurs de mappage GridView [ `ShowHeader` ](https://msdn.microsoft.com/en-gb/library/system.web.ui.webcontrols.gridview.showheader.aspx) et [ `ShowFooter` ](https://msdn.microsoft.com/en-gb/library/system.web.ui.webcontrols.gridview.showfooter.aspx) propriétés. Pour afficher la ligne de pied de page, il suffit de définir la `ShowFooter` propriété `True`. Comme le montre la Figure 6, définissant la `ShowFooter` propriété `True` ajoute une ligne de pied de page à la grille.


[![Pour afficher la ligne de pied de page, définissez ShowFooter sur True](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image6.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image11.png)

**Figure 6**: pour afficher la ligne de pied de page, jeu `ShowFooter` à `True` ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image12.png))


Notez que la ligne de pied de page possède une couleur d’arrière-plan rouge foncé. Cela est dû à DataWebControls thème, nous avons créé et appliqué à toutes les pages dans le [affichage des données avec le ObjectDataSource](../basic-reporting/displaying-data-with-the-objectdatasource-vb.md) didacticiel. Plus précisément, le `GridView.skin` fichier configure le `FooterStyle` propriété de ce type utilise la `FooterStyle` classe CSS. Le `FooterStyle` classe est définie dans `Styles.css` comme suit :


[!code-css[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample2.css)]

> [!NOTE]
> Nous ve exploré à l’aide de la ligne de pied de page s GridView dans les didacticiels précédents. Si nécessaire, faire référence à la [affichage des informations de résumé dans le pied de page du GridView](../custom-formatting/displaying-summary-information-in-the-gridview-s-footer-vb.md) didacticiel pour un petit rappel.


Après avoir défini la `ShowFooter` propriété `True`, prenez un moment pour afficher la sortie dans un navigateur. Actuellement, t ne de ligne de pied de page contenir n’importe quel texte ou les contrôles Web. À l’étape 3, nous allons modifier le pied de page pour chaque champ de GridView afin qu’il inclue l’interface d’insertion appropriée.


[![La ligne de pied de page vide est affiché au-dessus de la pagination contrôles d’Interface](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image7.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image13.png)

**Figure 7**: la ligne vide est affiché au-dessus de la pagination contrôles d’Interface ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image14.png))


## <a name="step-3-customizing-the-footer-row"></a>Étape 3 : Personnalisation de la ligne de pied de page

Dans le [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-vb.md) didacticiel, nous avons vu comment considérablement personnaliser l’affichage d’une colonne GridView à l’aide de TemplateField (par opposition à BoundFields ou CheckBoxFields) ; dans [ Personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-vb.md) , nous avons étudié à l’aide de TemplateField pour personnaliser l’interface de modification dans un GridView. Souvenez-vous que TemplateField est composé d’un nombre de modèles qui définit la combinaison de balisage, les contrôles Web et la syntaxe de liaison de données utilisé pour certains types de lignes. Le `ItemTemplate`, par exemple, spécifie le modèle utilisé pour les lignes en lecture seule, tandis que le `EditItemTemplate` définit le modèle pour la ligne modifiable.

Avec la `ItemTemplate` et `EditItemTemplate`, le TemplateField inclut également un `FooterTemplate` qui spécifie le contenu de la ligne de pied de page. Par conséquent, nous pouvons ajouter des contrôles Web nécessaires pour chaque champ s insertion d’interface dans le `FooterTemplate`. Pour démarrer, tous les champs dans le GridView convertir TemplateField. Cela est possible cliquant sur le lien Modifier les colonnes dans le GridView s balise active, en sélectionnant chaque champ dans l’angle inférieur gauche et en cliquant sur la convertir ce champ en TemplateField lien.


![Convertir chaque champ en TemplateField](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image8.gif)

**Figure 8**: convertir chaque champ en TemplateField


En cliquant sur la convertir ce champ en TemplateField transforme le type de champ actuelle en un équivalent TemplateField. Par exemple, chaque BoundField est remplacé par un TemplateField avec un `ItemTemplate` qui contient une étiquette qui affiche le champ de données correspondant et un `EditItemTemplate` qui affiche le champ de données dans une zone de texte. Le `ProductName` BoundField a été converti dans le balisage TemplateField suivant :


[!code-aspx[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample3.aspx)]

De même, la `Discontinued` CheckBoxField a été converti en TemplateField dont `ItemTemplate` et `EditItemTemplate` contiennent un contrôle de case à cocher Web (avec le `ItemTemplate` s case à cocher désactivée). En lecture seule `ProductID` BoundField a été converti en TemplateField avec un contrôle Label à la fois dans le `ItemTemplate` et `EditItemTemplate`. En bref, champ en TemplateField convertir un GridView existant est un moyen simple et rapide pour basculer vers le plus personnalisable TemplateField sans perdre aucune fonctionnalité champ s existant.

Depuis le contrôle GridView nous re utilisation ne t prise en charge d’édition, n’hésitez à supprimer la `EditItemTemplate` à partir de chaque TemplateField, en laissant seulement le `ItemTemplate`. Après cette opération, votre balisage déclaratif de GridView s doit se présenter comme suit :


[!code-aspx[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample4.aspx)]

Maintenant que chaque champ du GridView a été converti en TemplateField, nous pouvons entrer l’interface appropriée de l’insertion dans chaque champ s `FooterTemplate`. Certains champs ne disposent d’une interface d’insertion (`ProductID`, par exemple) ; d’autres peuvent varier dans les contrôles Web permet de collecter les nouvelles informations de produit s.

Pour créer l’interface de modification, cliquez sur le lien Modifier les modèles à partir de la balise active de GridView s. Puis, dans la liste déroulante, sélectionnez le champ approprié s `FooterTemplate` et faites glisser le contrôle approprié à partir de la boîte à outils vers le concepteur.


[![Ajouter l’Interface d’insertion appropriée à chaque FooterTemplate s de champ](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image9.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image15.png)

**Figure 9**: ajouter l’Interface appropriée d’insertion pour chaque champ s `FooterTemplate` ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image16.png))


La liste ci-dessous énumère les champs GridView, en spécifiant l’interface insertion à ajouter :

- `ProductID`Aucun.
- `ProductName`Ajoutez une zone de texte et définissez son `ID` à `NewProductName`. Ajoutez un contrôle RequiredFieldValidator pour vous assurer que l’utilisateur entre une valeur pour le nouveau nom de produit s.
- `SupplierID`Aucun.
- `CategoryID`Aucun.
- `QuantityPerUnit`Ajouter une zone de texte, en définissant ses `ID` à `NewQuantityPerUnit`.
- `UnitPrice`Ajouter une zone de texte nommé `NewUnitPrice` et un CompareValidator qui garantit que la valeur entrée est une valeur monétaire supérieure ou égale à zéro.
- `UnitsInStock`utiliser une zone de texte dont `ID` a la valeur `NewUnitsInStock`. Inclure un CompareValidator qui garantit que la valeur entrée est une valeur entière supérieure ou égale à zéro.
- `UnitsOnOrder`utiliser une zone de texte dont `ID` a la valeur `NewUnitsOnOrder`. Inclure un CompareValidator qui garantit que la valeur entrée est une valeur entière supérieure ou égale à zéro.
- `ReorderLevel`utiliser une zone de texte dont `ID` a la valeur `NewReorderLevel`. Inclure un CompareValidator qui garantit que la valeur entrée est une valeur entière supérieure ou égale à zéro.
- `Discontinued`Ajouter une case à cocher, la définition de sa `ID` à `NewDiscontinued`.
- `CategoryName`Ajouter une liste déroulante et définir son `ID` à `NewCategoryID`. Lier à un nouveau ObjectDataSource nommé `CategoriesDataSource` et configurez-le pour utiliser le `CategoriesBLL` classe s `GetCategories()` (méthode). Avoir le s DropDownList `ListItem` complet de s la `CategoryName` données champ, à l’aide de la `CategoryID` champ de données en tant que leurs valeurs.
- `SupplierName`Ajouter une liste déroulante et définir son `ID` à `NewSupplierID`. Lier à un nouveau ObjectDataSource nommé `SuppliersDataSource` et configurez-le pour utiliser le `SuppliersBLL` classe s `GetSuppliers()` (méthode). Avoir le s DropDownList `ListItem` complet de s la `CompanyName` données champ, à l’aide de la `SupplierID` champ de données en tant que leurs valeurs.

Pour chacun des contrôles de validation, effacer le `ForeColor` propriété afin que le `FooterStyle` couleur de premier plan blanc classe s CSS sera utilisée à la place de la valeur par défaut est rouge. Également utiliser le `ErrorMessage` propriété pour obtenir une description détaillée, mais définissez la `Text` propriété un astérisque. Pour éviter que le texte du contrôle s validation à l’origine de l’interface d’insertion encapsuler à deux lignes, définissez la `FooterStyle` s `Wrap` à false pour chaque propriété le `FooterTemplate` qui utilisent un contrôle de validation. Enfin, ajoutez un contrôle ValidationSummary sous le GridView et son `ShowMessageBox` propriété `True` et son `ShowSummary` propriété `False`.

Lorsque vous ajoutez un nouveau produit, nous devons fournir le `CategoryID` et `SupplierID`. Ces informations sont capturées par les compréhension des listes dans les cellules de pied de page pour le `CategoryName` et `SupplierName` champs. J’ai choisi d’utiliser ces champs par opposition à la `CategoryID` et `SupplierID` TemplateField, car les lignes de l’utilisateur dans la grille s données est probablement plus intéressé à voir les noms de catégorie et fournisseur plutôt que leurs valeurs d’ID. Étant donné que la `CategoryID` et `SupplierID` maintenant les valeurs sont capturées dans les `CategoryName` et `SupplierName` interfaces insertion de champ s, nous pouvons supprimer la `CategoryID` et `SupplierID` TemplateField du contrôle GridView.

De même, la `ProductID` n’est pas utilisé lors de l’ajout d’un nouveau produit, donc la `ProductID` TemplateField peut également être supprimé. Toutefois, permettent de s laisser le `ProductID` champ dans la grille. Outre les zones de texte, compréhension des listes, les cases à cocher et des contrôles de validation qui composent l’interface d’insertion, nous devons également un ajout bouton qui, lorsque vous cliquez dessus, exécute la logique pour ajouter le nouveau produit à la base de données. À l’étape 4 nous allons inclure un bouton Ajouter dans l’interface de l’insertion dans la `ProductID` TemplateField s `FooterTemplate`.

N’hésitez pas à améliorer l’apparence des différents champs GridView. Par exemple, vous souhaiterez peut-être mettre en forme le `UnitPrice` Aligner à droite les valeurs sous forme de devise, le `UnitsInStock`, `UnitsOnOrder`, et `ReorderLevel` des champs et mise à jour le `HeaderText` valeurs pour le TemplateField.

Après avoir créé la transposition d’insertion des interfaces dans les `FooterTemplate` s, suppression de la `SupplierID`, et `CategoryID` TemplateField et améliorer l’esthétique de la grille par le biais de mise en forme et d’alignement de la TemplateField, votre s GridView déclarative balisage doit ressembler à ce qui suit :


[!code-aspx[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample5.aspx)]

Lorsqu’ils sont affichés via un navigateur, la ligne de pied de page s GridView inclut désormais l’insertion de l’interface (voir la Figure 10). À ce stade, l’insertion interface n’inclure un moyen de l’utilisateur indiquer que s she entré les données pour le nouveau produit et souhaite insérer un nouvel enregistrement dans la base de données. En outre, nous ve encore à l’adresse comment traduire les données entrées dans le pied de page dans un nouvel enregistrement dans le `Products` base de données. À l’étape 4, nous allons examiner comment inclure un bouton d’ajout à l’interface d’insertion et d’exécuter du code sur publication (postback) quand il s cliqué sur. Étape 5 montre comment insérer un nouvel enregistrement à l’aide des données à partir du pied de page.


[![Le pied de page GridView fournit une Interface pour l’ajout d’un nouvel enregistrement](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image10.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image17.png)

**La figure 10**: le pied de page GridView fournit une Interface pour l’ajout d’un nouvel enregistrement ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image18.png))


## <a name="step-4-including-an-add-button-in-the-inserting-interface"></a>Étape 4 : Y compris un bouton Ajouter dans l’Interface d’insertion

Nous devons inclure un bouton Ajouter quelque part dans l’interface d’insertion dans la mesure où le s de ligne de pied de page insertion actuellement d’interface n’a pas les moyens de l’utilisateur indiquer qu’ils ont terminé d’entrer les nouvelles informations de produit s. Cela peut être placé dans un existants `FooterTemplate` s nous pouvons ajouter ou une nouvelle colonne à la grille pour cet effet. Pour ce didacticiel, s permettent de placer le bouton Ajouter dans le `ProductID` TemplateField s `FooterTemplate`.

À partir du concepteur, cliquez sur le lien Modifier les modèles dans la balise active de GridView s, puis choisissez le `ProductID` champ s `FooterTemplate` dans la liste déroulante. Ajouter un contrôle bouton Web (ou d’un LinkButton ou ImageButton, si vous préférez) pour le paramètre de modèle, son ID `AddProduct`, ses `CommandName` à insérer et son `Text` propriété à ajouter comme indiqué dans la Figure 11.


[![Placez le bouton Ajouter dans FooterTemplate s ProductID TemplateField](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image11.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image19.png)

**Figure 11**: placez le bouton Ajouter dans le `ProductID` TemplateField s `FooterTemplate` ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image20.png))


Une fois que vous avez déjà inclus le bouton Ajouter, tester la page dans un navigateur. Notez que lorsque vous cliquez sur le bouton Ajouter avec des données non valides dans l’interface d’insertion, la publication est court circuite et le contrôle ValidationSummary indique les données non valides (voir Figure 12). Avec les données appropriées sont entrées, en cliquant sur le bouton Ajouter provoque une publication (postback). Aucun enregistrement n’est ajouté à la base de données, cependant. Vous devrez écrire un peu de code pour effectuer l’insertion.


[![Le bouton Ajouter publication (postback) est circuit court s’il existe des données non valides dans l’Interface d’insertion](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image12.gif)](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image21.png)

**Figure 12**: le bouton Ajouter publication (postback) est un circuit court s’il existe des données non valides dans l’Interface d’insertion ([cliquez pour afficher l’image en taille réelle](inserting-a-new-record-from-the-gridview-s-footer-vb/_static/image22.png))


> [!NOTE]
> Les contrôles de validation dans l’interface d’insertion n’étaient pas affectés à un groupe de validation. Cela fonctionne correctement tant que l’interface d’insertion est le jeu uniquement des contrôles de validation sur la page. Si, toutefois, il existe d’autres contrôles de validation sur la page (tels que les contrôles de validation dans l’interface de modification de grille s), les contrôles de validation de l’insertion de l’interface et ajouter le bouton s `ValidationGroup` propriétés doivent être assignées à la même valeur de manière à associer ces contrôles à un groupe de validation particulier. Consultez [ayant été Disséqué les contrôles de Validation dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/112305-1.aspx) pour plus d’informations sur les contrôles de validation et les boutons sur une page de partitionnement des groupes de validation.


## <a name="step-5-inserting-a-new-record-into-theproductstable"></a>Étape 5 : Insertion d’un nouvel enregistrement dans le`Products`Table

Lorsque vous utilisez les fonctionnalités d’édition intégrées du contrôle GridView, le contrôle GridView gère automatiquement tout le travail nécessaire pour effectuer la mise à jour. En particulier, lorsque l’utilisateur clique sur le bouton de mise à jour il copie les valeurs entrées à partir de l’interface de modification aux paramètres dans le s ObjectDataSource `UpdateParameters` collection et plaisir désactiver la mise à jour en appelant le s ObjectDataSource `Update()` (méthode). Étant donné que le contrôle GridView ne fournit pas ces fonctionnalités intégrées d’insertion, nous devons implémenter le code qui appelle le s ObjectDataSource `Insert()` (méthode) et copie les valeurs de l’insertion de l’interface avec le s ObjectDataSource `InsertParameters` collection .

Cette logique d’insertion doit être exécutée une fois que le bouton Ajouter a été utilisé. Comme indiqué dans le [Ajout et la réponse aux boutons dans un GridView](../custom-button-actions/adding-and-responding-to-buttons-to-a-gridview-vb.md) (didacticiel), chaque fois qu’un bouton, LinkButton ou ImageButton dans un GridView est activé, le s GridView `RowCommand` événement se déclenche lors de la publication. Cet événement se déclenche si le bouton, LinkButton ou ImageButton a été explicitement ajoutée telles que le bouton Ajouter dans la ligne de pied de page ou si elle a été ajouté automatiquement par le contrôle GridView (telles que le LinkButton en haut de chaque colonne quand activer le tri est sélectionné, ou LinkButton dans l’interface de pagination lorsque vous activez la pagination est activée).

Par conséquent, pour répondre à l’utilisateur clique sur le bouton Ajouter, nous devons créer un gestionnaire d’événements pour le contrôle GridView s `RowCommand` événement. Étant donné que cet événement se déclenche chaque fois que *tout* bouton, LinkButton ou ImageButton dans le GridView est activé, il s vital que nous ne poursuivez l’opération avec la logique d’insertion si le `CommandName` propriété passé dans les mappages de gestionnaire d’événements pour le `CommandName` valeur du bouton Ajouter (Insert). En outre, nous devons également uniquement poursuivre si les contrôles de validation des données valides de rapport. Pour ce faire, créez un gestionnaire d’événements pour le `RowCommand` événement avec le code suivant :


[!code-vb[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample6.vb)]

> [!NOTE]
> Vous vous demandez peut-être pourquoi le Gestionnaire d’événements souhaitez les supprimer la vérification du `Page.IsValid` propriété. Après tout, t gagné / publication supprimées si des données non valides sont fournies dans l’interface insertion ? Cette hypothèse est correcte en tant que l’utilisateur n’a pas désactivé JavaScript ou a pris des mesures pour contourner la logique de validation côté client. En bref, une devez jamais vous appuyer exclusivement la validation côté client ; une vérification côté serveur pour la validité doit toujours être effectuée avant d’utiliser les données.


À l’étape 1, nous avons créé la `ProductsDataSource` ObjectDataSource telles que son `Insert()` méthode mappée à la `ProductsBLL` classe s `AddProduct` (méthode). Pour insérer le nouvel enregistrement dans le `Products` table, nous pouvons simplement appeler le s ObjectDataSource `Insert()` méthode :


[!code-vb[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample7.vb)]

Maintenant que le `Insert()` méthode a été appelée, tout ce qui reste consiste à copier les valeurs à partir de l’interface d’insertion pour les paramètres passés à la `ProductsBLL` classe s `AddProduct` (méthode). Comme nous l’avons vu dans la [examiner les événements associés insertion, mise à jour et suppression](../editing-inserting-and-deleting-data/examining-the-events-associated-with-inserting-updating-and-deleting-vb.md) didacticiel, cela peut être obtenue via le s ObjectDataSource `Inserting` événement. Dans le `Inserting` événement référencer par programme les contrôles à partir de la `Products` pied de page GridView s de ligne et affecter leurs valeurs à la `e.InputParameters` collection. Si l’utilisateur l’omet une valeur telle que de laisser le `ReorderLevel` vide de zone de texte nous devons spécifier que la valeur insérée dans la base de données doit être `NULL`. Étant donné que la `AddProducts` méthode accepte les types nullable pour les champs de la base de données accepte les valeurs NULL, simplement en utiliser un type nullable et définissez sa valeur sur `Nothing` dans le cas où l’entrée d’utilisateur est omise.


[!code-vb[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample8.vb)]

Avec la `Inserting` Gestionnaire d’événements s’est terminée, les nouveaux enregistrements peuvent être ajoutés à la `Products` table de base de données via la ligne de pied de page s GridView. Pour commencer, essayez d’ajouter plusieurs produits.

## <a name="enhancing-and-customizing-the-add-operation"></a>Améliorer et la personnalisation de l’opération d’ajout

Actuellement, en cliquant sur le bouton Ajouter ajoute un nouvel enregistrement à la table de base de données mais ne fournit pas de fournir une rétroaction visuelle quelconque que l’enregistrement a été ajoutée. Dans l’idéal, une boîte d’alerte Web Label contrôle ou côté client serait informe l’utilisateur qui leur insertion est terminée avec succès. J’ai laisser ce champ comme un exercice pour le lecteur.

Le contrôle GridView utilisé dans ce didacticiel ne s’applique pas un ordre de tri pour les produits répertoriés, ni de l’utilisateur final trier les données. Par conséquent, les enregistrements sont classés comme ils se trouvent dans la base de données par leur champ de clé primaire. Étant donné que chaque nouvel enregistrement a un `ProductID` valeur supérieure à la dernière, chaque fois qu’un nouveau produit est ajouté, il a été ajouté à la fin de la grille. Par conséquent, vous souhaiterez envoyer automatiquement vers la dernière page du contrôle GridView à l’utilisateur après avoir ajouté un nouvel enregistrement. Cela peut être accompli en ajoutant la ligne de code suivante après l’appel à `ProductsDataSource.Insert()` dans le `RowCommand` Gestionnaire d’événements pour indiquer que l’utilisateur doit être envoyé à la dernière page après la liaison des données au contrôle GridView :


[!code-vb[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample9.vb)]

`SendUserToLastPage`est une variable booléenne au niveau de la page qui est initialement affectée à un `False`. Dans le GridView s `DataBound` Gestionnaire d’événements, si `SendUserToLastPage` a la valeur false, le `PageIndex` propriété est mise à jour pour envoyer l’utilisateur vers la dernière page.


[!code-vb[Main](inserting-a-new-record-from-the-gridview-s-footer-vb/samples/sample10.vb)]

La raison pour laquelle le `PageIndex` propriété est définie dans le `DataBound` Gestionnaire d’événements (par opposition à la `RowCommand` Gestionnaire d’événements) est, car lorsque le `RowCommand` Gestionnaire d’événements se déclenche nous ve encore à ajouter le nouvel enregistrement dans le `Products` table de base de données. Par conséquent, dans le `RowCommand` le dernier index de la page Gestionnaire d’événements (`PageCount - 1`) représente le dernier index de page *avant* le nouveau produit a été ajouté. Pour la majorité des produits ajouté, le dernier index de page est le même après avoir ajouté le nouveau produit. Mais lorsque le produit ajouté un nouvel index dernière page, si nous mettre correctement à jour le `PageIndex` dans le `RowCommand` Gestionnaire d’événements, puis nous sont dirigés vers la seconde à la dernière page (le dernier index de page avant d’ajouter le nouveau produit) par opposition à la dernière page nouvelle i Actualiser. Étant donné que la `DataBound` Gestionnaire d’événements est déclenchée lorsque le nouveau produit a été ajouté et les données reliée à la grille, en définissant le `PageIndex` propriété il nous savons que nous re mise en route de l’index de page dernière correct.

Enfin, le contrôle GridView utilisé dans ce didacticiel est assez large en raison du nombre de champs qui doivent être collectés pour l’ajout d’un nouveau produit. En raison de cette largeur, une disposition verticale de DetailsView s peut être préférée. Les s GridView largeur globale peut être réduit en collectant les entrées moins. Peut-être nous n’avez pas besoin pour collecter les `UnitsOnOrder`, `UnitsInStock`, et `ReorderLevel` champs lors de l’ajout d’un nouveau produit, auquel cas ces champs pu être supprimés du contrôle GridView.

Pour ajuster les données collectées, nous pouvons utiliser une des deux approches :

- Continuer à utiliser le `AddProduct` méthode qui attend des valeurs pour le `UnitsOnOrder`, `UnitsInStock`, et `ReorderLevel` champs. Dans la `Inserting` Gestionnaire d’événements, fournir codé en dur, les valeurs à utiliser pour ces entrées ont été supprimées de l’interface d’insertion par défaut.
- Créer une nouvelle surcharge de la `AddProduct` méthode dans le `ProductsBLL` classe qui n’accepte pas les entrées pour le `UnitsOnOrder`, `UnitsInStock`, et `ReorderLevel` champs. Ensuite, dans la page ASP.NET, vous devez configurer ObjectDataSource pour utiliser cette nouvelle surcharge.

Soit l’option fonctionnera également ainsi. Dans au-delà de didacticiels nous avons utilisé la dernière option, créer plusieurs surcharges pour la `ProductsBLL` classe s `UpdateProduct` (méthode).

## <a name="summary"></a>Résumé

Le contrôle GridView ne dispose pas de fonctionnalités intégrées d’insertion trouvées dans le DetailsView et FormView, mais avec un peu d’effort, une interface d’insertion peut être ajoutée à la ligne de pied de page. Pour afficher la ligne de pied de page dans un GridView simplement définie sa `ShowFooter` propriété `True`. Le contenu de ligne de pied de page peut être personnalisé pour chaque champ, en convertissant le champ en TemplateField et ajout de l’insertion de l’interface pour le `FooterTemplate`. Comme nous l’avons vu dans ce didacticiel, le `FooterTemplate` peut contenir des boutons, zones de texte, compréhension des listes, les cases à cocher, les contrôles de source de données utilisé pour remplir les contrôles Web pilotés par les données (par exemple, la compréhension des listes) et des contrôles de validation. En même temps que les contrôles pour la collecte de l’entrée d’utilisateur s, un bouton Ajouter, LinkButton ou ImageButton est nécessaire.

Lorsque vous cliquez sur le bouton Ajouter, s ObjectDataSource `Insert()` méthode est appelée pour démarrer le flux de travail de l’insertion. ObjectDataSource appelle ensuite la méthode d’insertion configuré (le `ProductsBLL` classe s `AddProduct` (méthode), dans ce didacticiel). Nous devons copier les valeurs d’insertion d’interface avec le s ObjectDataSource s GridView `InsertParameters` collection avant de la méthode d’insertion qui est appelée. Cela peut être accompli en référençant par programmation des contrôles Web interface insertion dans le s ObjectDataSource `Inserting` Gestionnaire d’événements.

Ce didacticiel termine la présentation des techniques permettant d’améliorer l’apparence de s GridView. L’ensemble suivant de didacticiels examine comment travailler avec des données binaires comme des images, des fichiers PDF, des documents Word et ainsi de suite et les données de contrôles Web.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Bernadette Leigh. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](adding-a-gridview-column-of-checkboxes-vb.md)
