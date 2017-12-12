---
uid: web-forms/overview/data-access/custom-formatting/using-the-formview-s-templates-cs
title: "À l’aide de modèles FormView (c#) | Documents Microsoft"
author: rick-anderson
description: "À la différence du DetailsView, le FormView n’est pas composé de champs. Au lieu de cela, le contrôle FormView est rendu à l’aide de modèles. Dans ce didacticiel, nous allons examiner à l’aide de la F...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: d3f062af-88cf-426d-af44-e41f32c41672
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-formatting/using-the-formview-s-templates-cs
msc.type: authoredcontent
ms.openlocfilehash: 18e76a763e22c0d1046acc60e095bbd11960c5e6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-formviews-templates-c"></a>À l’aide de modèles FormView (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/6/9/969e5c94-dfb6-4e47-9570-d6d9e704c3c1/ASPNET_Data_Tutorial_14_CS.exe) ou [télécharger le PDF](using-the-formview-s-templates-cs/_static/datatutorial14cs1.pdf)

> À la différence du DetailsView, le FormView n’est pas composé de champs. Au lieu de cela, le contrôle FormView est rendu à l’aide de modèles. Dans ce didacticiel, que nous allons examiner utilisant le contrôle FormView pour présenter un affichage moins rigid de données.


## <a name="introduction"></a>Introduction

Dans les deux derniers didacticiels, nous avons vu comment personnaliser les sorties de contrôles GridView et DetailsView à l’aide de TemplateField. TemplateField permettre le contenu d’un champ spécifique être hautement personnalisé, mais au final le GridView et DetailsView ont une apparence bruts au lieu de cela, la grille. Pour de nombreux scénarios, une mise en page de grille est idéale, mais parfois un affichage plus fluide, moins rigid est nécessaire. Lorsque vous affichez un enregistrement unique, telle une mise en page fluide est possible en utilisant le contrôle FormView.

À la différence du DetailsView, le FormView n’est pas composé de champs. Impossible d’ajouter un BoundField ou un TemplateField à un FormView. Au lieu de cela, le contrôle FormView est rendu à l’aide de modèles. Considérez le FormView comme un contrôle DetailsView contenant TemplateField unique. Le FormView prend en charge les modèles suivants :

- `ItemTemplate`utilisé pour restituer l’enregistrement particulier affiché dans le contrôle FormView
- `HeaderTemplate`permet de spécifier une ligne d’en-tête facultatif
- `FooterTemplate`permet de spécifier une ligne de pied de page facultatif
- `EmptyDataTemplate`Lorsque FormView `DataSource` ne dispose pas de tous les enregistrements, les `EmptyDataTemplate` est utilisé à la place de la `ItemTemplate` pour restituer le balisage du contrôle
- `PagerTemplate`peut être utilisé pour personnaliser l’interface de pagination pour FormViews ayant une pagination activée
- `EditItemTemplate` / `InsertItemTemplate`permet de personnaliser l’interface de modification ou une interface insertion pour FormViews qui prennent en charge ces fonctionnalités.

Dans ce didacticiel, que nous allons examiner utilisant le contrôle FormView pour présenter un affichage moins rigid de produits. Au lieu d’avoir des champs pour le nom, catégorie, fournisseur et d’ainsi de suite, le contrôle FormView `ItemTemplate` affichent ces valeurs à l’aide d’une combinaison d’un élément d’en-tête et un `<table>` (voir Figure 1).


[![Le FormView apparaît de la disposition de grille affichée dans le contrôle DetailsView](using-the-formview-s-templates-cs/_static/image2.png)](using-the-formview-s-templates-cs/_static/image1.png)

**Figure 1**: le FormView s’arrête en dehors de la mise en page Grid-Like apparaît dans le contrôle DetailsView ([cliquez pour afficher l’image en taille réelle](using-the-formview-s-templates-cs/_static/image3.png))


## <a name="step-1-binding-the-data-to-the-formview"></a>Étape 1 : Liaison des données pour le contrôle FormView

Ouvrez le `FormView.aspx` page et faites glisser un FormView à partir de la boîte à outils vers le concepteur. Lorsque vous ajoutez le contrôle FormView il apparaît comme une zone grisée, nous demandant qu’un `ItemTemplate` est nécessaire.


[![Le FormView ne peut pas être rendu dans le concepteur jusqu'à ce qu’un ItemTemplate est fourni.](using-the-formview-s-templates-cs/_static/image5.png)](using-the-formview-s-templates-cs/_static/image4.png)

**Figure 2**: le de FormView ne peut pas être restitué dans le concepteur jusqu'à ce qu’un `ItemTemplate` est fourni ([cliquez pour afficher l’image en taille réelle](using-the-formview-s-templates-cs/_static/image6.png))


Le `ItemTemplate` peuvent être créés manuellement (via la syntaxe déclarative) ou peuvent être créées automatiquement par le FormView de liaison à un contrôle de source de données par le biais du concepteur. Cela créées automatiquement `ItemTemplate` contient du code HTML que répertorie le nom de chaque champ et une étiquette de contrôle dont `Text` propriété est liée à la valeur du champ. Cette approche auto-crée également un `InsertItemTemplate` et `EditItemTemplate`, qui sont remplis avec des contrôles d’entrée pour chacun des champs de données retournés par le contrôle de source de données.

Si vous souhaitez créer automatiquement le modèle, à partir de la balise active du FormView ajouter un nouveau contrôle ObjectDataSource qui appelle le `ProductsBLL` la classe `GetProducts()` (méthode). Cette opération crée un FormView avec un `ItemTemplate`, `InsertItemTemplate`, et `EditItemTemplate`. Dans la vue de Source, supprimez le `InsertItemTemplate` et `EditItemTemplate` , car nous ne sommes pas intéressés par la création d’un FormView prend en charge modifier ou insérer des encore. Ensuite, vider le balisage dans le `ItemTemplate` afin que nous avons vierge pour travailler à partir de.

Si vous deviez générer plutôt la `ItemTemplate` manuellement, vous pouvez ajouter et configurer l’ObjectDataSource en le faisant glisser à partir de la boîte à outils vers le concepteur. Toutefois, ne définissez pas source de données FormView à partir du concepteur. Au lieu de cela, accédez à la vue de Source et de définir manuellement les FormView `DataSourceID` propriété le `ID` valeur ObjectDataSource. Ensuite, ajoutez manuellement le `ItemTemplate`.

Quelle que soit quelle approche vous décidé de prendre, à ce stade balisage déclaratif de votre FormView doit ressembler :


[!code-aspx[Main](using-the-formview-s-templates-cs/samples/sample1.aspx)]

Prenez un moment pour vérifier la case à cocher Activer la pagination dans la balise active du FormView ; Cette opération ajoute le `AllowPaging="True"` d’attribut à la syntaxe déclarative FormView. En outre, définissez le `EnableViewState` propriété à False.

## <a name="step-2-defining-theitemtemplates-markup"></a>Étape 2 : Définir le`ItemTemplate`du balisage

Avec le contrôle FormView liée au contrôle ObjectDataSource et configuré pour prendre en charge la pagination, nous sommes prêts spécifier le contenu pour le `ItemTemplate`. Pour ce didacticiel, nous allons ont le nom du produit affiché dans un `<h3>` titre. Après cela, nous allons utiliser HTML `<table>` pour afficher les propriétés restantes de produit dans une table en quatre colonnes où les premier et troisième colonnes répertorient les noms de propriété et la deuxième et la quatrième liste de leurs valeurs.

Ce balisage peut être entré dans via l’interface de modification de modèle FormView dans le concepteur ou manuellement via la syntaxe déclarative. Lorsque vous travaillez avec des modèles I généralement s’avérer plus rapide de travailler directement avec la syntaxe déclarative, mais n’hésitez pas à utiliser la technique, vous êtes plus à l’aise avec.

Le balisage suivant montre le balisage déclaratif FormView après le `ItemTemplate`de structure a été effectuée :


[!code-aspx[Main](using-the-formview-s-templates-cs/samples/sample2.aspx)]

Notez que la syntaxe de liaison de données - `<%# Eval("ProductName") %>`, pour l’exemple peut être injectée directement dans la sortie du modèle. Autrement dit, elle ne doive pas être attribuée à un contrôle d’étiquette `Text` propriété. Par exemple, nous avons la `ProductName` valeur affichée dans un `<h3>` à l’aide de l’élément `<h3><%# Eval("ProductName") %></h3>`, dont le pour le produit sera rendu Tran `<h3>Chai</h3>`.

Le `ProductPropertyLabel` et `ProductPropertyValue` classes CSS sont utilisées pour spécifier le style des noms de propriété de produit et les valeurs dans le `<table>`. Ces classes CSS sont définies dans `Styles.css` et les noms de propriété gras et aligné à droite et ajouter un droit de remplissage pour les valeurs de propriété.

Étant donné qu’aucun CheckBoxFields disponible avec le FormView, afin d’afficher le `Discontinued` valeur comme une case à cocher, nous devons ajouter votre propre contrôle de case à cocher. Le `Enabled` est définie sur False, ce qui en lecture seule et la case à cocher `Checked` propriété est liée à la valeur de la `Discontinued` champ de données.

Avec la `ItemTemplate` terminée, les informations de produit s’affichent de manière beaucoup plus fluide. Comparez la sortie de DetailsView du didacticiel dernier (Figure 3) avec la sortie générée par le contrôle FormView dans ce didacticiel (Figure 4).


[![La sortie de DetailsView rigide](using-the-formview-s-templates-cs/_static/image8.png)](using-the-formview-s-templates-cs/_static/image7.png)

**Figure 3**: la sortie de DetailsView rigide ([cliquez pour afficher l’image en taille réelle](using-the-formview-s-templates-cs/_static/image9.png))


[![La sortie FormView FLUIDE](using-the-formview-s-templates-cs/_static/image11.png)](using-the-formview-s-templates-cs/_static/image10.png)

**Figure 4**: la sortie de FormView fluide ([cliquez pour afficher l’image en taille réelle](using-the-formview-s-templates-cs/_static/image12.png))


## <a name="summary"></a>Résumé

Alors que les contrôles GridView et DetailsView peuvent avoir leur sortie personnalisé à l’aide de TemplateField, les deux présentent toujours leurs données dans un format de grille, bruts. Dans les cas lorsqu’un enregistrement unique doit être indiqué à l’aide d’une disposition moins rigide, le FormView est idéale. Comme le contrôle DetailsView, le FormView restitue un enregistrement unique à partir de son `DataSource`, mais à la différence du DetailsView est composé uniquement de modèles et ne prend pas en charge les champs.

Comme nous l’avons vu dans ce didacticiel, le FormView permet une mise en page plus souple lors de l’affichage d’un enregistrement unique. Dans les futures didacticiels nous allons aborder les contrôles DataList et répéteur, qui fournissent le même niveau de flexibilité que la FormsView, mais sont en mesure d’afficher plusieurs enregistrements (par exemple, le contrôle GridView).

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été E.R. Gillain. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](using-templatefields-in-the-detailsview-control-cs.md)
[Suivant](displaying-summary-information-in-the-gridview-s-footer-cs.md)
