---
uid: web-forms/overview/data-access/custom-formatting/custom-formatting-based-upon-data-cs
title: "Mise en forme personnalisée en fonction des données (c#) | Documents Microsoft"
author: rick-anderson
description: "L’ajustement du format du GridView, DetailsView ou FormView basé sur les données qui lui est associées peut être accompli de plusieurs façons. Dans ce didacticiel, nous allons l..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 871a4574-f89c-4214-b786-79253ed3653b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-formatting/custom-formatting-based-upon-data-cs
msc.type: authoredcontent
ms.openlocfilehash: 606721b01fae34a7bce85d497a442cb110f1b51e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="custom-formatting-based-upon-data-c"></a>Mise en forme personnalisée en fonction des données (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/6/9/969e5c94-dfb6-4e47-9570-d6d9e704c3c1/ASPNET_Data_Tutorial_11_CS.exe) ou [télécharger le PDF](custom-formatting-based-upon-data-cs/_static/datatutorial11cs1.pdf)

> L’ajustement du format du GridView, DetailsView ou FormView basé sur les données qui lui est associées peut être accompli de plusieurs façons. Dans ce didacticiel, nous allons examiner comment accomplir lié aux données via l’utilisation de gestionnaires d’événements liés aux données et RowDataBound de mise en forme.


## <a name="introduction"></a>Introduction

L’apparence des contrôles GridView, DetailsView et FormView peut être personnalisé à une multitude de propriétés associées au style. Propriétés, telles que `CssClass`, `Font`, `BorderWidth`, `BorderStyle`, `BorderColor`, `Width`, et `Height`, entre autres, déterminent l’aspect général du contrôle rendu. Propriétés, y compris `HeaderStyle`, `RowStyle`, `AlternatingRowStyle`, et d’autres autoriser ces mêmes paramètres de style à appliquer à des sections particulières. De même, ces paramètres de style peuvent être appliqués au niveau du champ.

Dans de nombreux scénarios, les exigences de mise en forme varient en fonction de la valeur des données affichées. Par exemple, pour attirer l’attention de stock des produits, un rapport qui répertorie les informations de produit peut définir la couleur d’arrière-plan à jaune pour les produits dont `UnitsInStock` et `UnitsOnOrder` les champs sont toutes deux égales à 0. Pour mettre en évidence les produits les plus chers, nous pouvons adopter afficher les prix de ces produits d’évaluation des coûts plus de 75,00 $ en caractères gras.

L’ajustement du format du GridView, DetailsView ou FormView basé sur les données qui lui est associées peut être accompli de plusieurs façons. Dans ce didacticiel, nous allons étudier comment accomplir lié aux données de mise en forme à l’aide de la `DataBound` et `RowDataBound` gestionnaires d’événements. Dans l’étape suivante du didacticiel, nous allons Explorer une approche alternative.

## <a name="using-the-detailsview-controlsdataboundevent-handler"></a>À l’aide du contrôle DetailsView`DataBound`Gestionnaire d’événements

Lorsque les données sont liées à un contrôle DetailsView, à partir d’un contrôle de source de données ou par le biais de par programmation les données du contrôle `DataSource` propriété et en appelant sa `DataBind()` survenir de méthode, la séquence d’étapes suivante :

1. Les données contrôle Web `DataBinding` se déclenche des événements.
2. Les données sont liées aux données de contrôle Web.
3. Les données contrôle Web `DataBound` se déclenche des événements.

Une logique personnalisée peut être introduite immédiatement après les étapes 1 et 3 via un gestionnaire d’événements. En créant un gestionnaire d’événements pour le `DataBound` événement, nous pouvons déterminer par programme les données qui a été liée au contrôle Web données et ajuster la mise en forme en fonction des besoins. Pour illustrer cela, nous allons créer un contrôle DetailsView qui répertorie des informations générales sur un produit, mais affichera le `UnitPrice` valeur dans un ***police gras, italique*** si elle dépasse 75,00 $.

## <a name="step-1-displaying-the-product-information-in-a-detailsview"></a>Étape 1 : Afficher les informations de produit dans un contrôle DetailsView

Ouvrir le `CustomColors.aspx` page dans le `CustomFormatting` dossier, faites glisser un contrôle DetailsView à partir de la boîte à outils vers le concepteur, définissez son `ID` valeur de propriété à `ExpensiveProductsPriceInBoldItalic`et le lier à un nouveau contrôle ObjectDataSource qui appelle le `ProductsBLL` la classe `GetProducts()` (méthode). Les étapes détaillées pour cette tâche sont omis ici par souci de concision, car nous avons examiné les détails dans les didacticiels précédents.

Une fois que vous avez lié ObjectDataSource au contrôle DetailsView, prenez un moment pour modifier la liste de champs. J’ai choisi de supprimer le `ProductID`, `SupplierID`, `CategoryID`, `UnitsInStock`, `UnitsOnOrder`, `ReorderLevel`, et `Discontinued` BoundFields et renommé et remis en forme les BoundFields restants. J’ai également supprimées le `Width` et `Height` paramètres. Étant donné que le contrôle DetailsView affiche un seul enregistrement, il faut activer la pagination afin d’autoriser l’utilisateur final afficher tous les produits. En activant la case à cocher Activer la pagination dans la balise active du DetailsView à le faire.


[![La case Activer la pagination dans la balise active du DetailsView](custom-formatting-based-upon-data-cs/_static/image2.png)](custom-formatting-based-upon-data-cs/_static/image1.png)

**Figure 1**: case à cocher Activer la pagination dans la balise active du DetailsView ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image3.png))


Après ces modifications, le balisage de DetailsView sera :


[!code-aspx[Main](custom-formatting-based-upon-data-cs/samples/sample1.aspx)]

Prenez un moment pour tester cette page dans votre navigateur.


[![Le contrôle DetailsView affiche un produit à la fois](custom-formatting-based-upon-data-cs/_static/image5.png)](custom-formatting-based-upon-data-cs/_static/image4.png)

**Figure 2**: le contrôle DetailsView contrôle affiche un produit à la fois ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image6.png))


## <a name="step-2-programmatically-determining-the-value-of-the-data-in-the-databound-event-handler"></a>Étape 2 : Déterminer par programme la valeur des données dans le Gestionnaire d’événements de liaison de données

Pour afficher le prix d’une police en gras, italique pour les produits dont `UnitPrice` valeur dépasse $75,00, nous devons d’abord être en mesure de déterminer par programme le `UnitPrice` valeur. Pour le contrôle DetailsView, cela peut être accompli dans le `DataBound` Gestionnaire d’événements. Pour créer l’événement Gestionnaire cliquez sur le contrôle DetailsView dans le concepteur, puis accédez à la fenêtre Propriétés. Appuyez sur F4 pour le réactiver, si elle n’est pas visible, ou accédez au menu Affichage et sélectionnez l’option de menu de la fenêtre Propriétés. Dans la fenêtre Propriétés, cliquez sur l’icône d’éclair pour répertorier les événements du DetailsView. Ensuite, double-cliquez sur le `DataBound` événement ou tapez le nom du Gestionnaire d’événements que vous souhaitez créer.


![Créer un gestionnaire d’événements pour l’événement lié aux données](custom-formatting-based-upon-data-cs/_static/image7.png)

**Figure 3**: créer un gestionnaire d’événements pour le `DataBound` événement


Cette opération sera automatiquement créer le Gestionnaire d’événements et accéder à la partie du code où il a été ajouté. À ce stade vous voyez :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample2.cs)]

Les données liées au contrôle DetailsView est accessible via la `DataItem` propriété. Rappelez-vous que nous créons une liaison nos contrôles à un DataTable fortement typée, qui est constitué d’une collection d’instances de DataRow fortement typée. Lorsque la table de données est lié au contrôle DetailsView, le premier DataRow du DataTable est affecté à de DetailsView `DataItem` propriété. Plus précisément, le `DataItem` est affectée à un `DataRowView` objet. Nous pouvons utiliser le `DataRowView`de `Row` propriété à obtenir l’accès à l’objet DataRow sous-jacent, ce qui est réellement un `ProductsRow` instance. Une fois cela `ProductsRow` instance nous pouvons notre décision en examinant simplement les valeurs de propriété de l’objet.

Le code suivant illustre comment déterminer si le `UnitPrice` valeur liée au contrôle DetailsView est supérieur à $75,00 :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample3.cs)]

> [!NOTE]
> Étant donné que `UnitPrice` peut avoir un `NULL` valeur dans la base de données, nous Vérifiez tout d’abord vous assurer que nous n’avons pas affaire à un `NULL` valeur avant d’accéder à la `ProductsRow`de `UnitPrice` propriété. Cette vérification est importante, car si nous tentons pour accéder à la `UnitPrice` propriété lorsqu’il a un `NULL` valeur le `ProductsRow` objet lèvera une [StrongTypingException exception](https://msdn.microsoft.com/library/system.data.strongtypingexception.aspx).


## <a name="step-3-formatting-the-unitprice-value-in-the-detailsview"></a>Étape 3 : Mise en forme la valeur de UnitPrice dans le contrôle DetailsView

À ce stade, nous pouvons déterminer si le `UnitPrice` valeur liée au contrôle DetailsView a une valeur qui dépasse $75,00, mais nous avons encore pour voir comment ajustez par programmation le contrôle DetailsView de mise en forme en conséquence. Pour modifier la mise en forme d’une ligne entière dans le contrôle DetailsView, accéder par programmation à la ligne à l’aide de `DetailsViewID.Rows[index]`; pour modifier une cellule spécifique, utilisez d’accès `DetailsViewID.Rows[index].Cells[index]`. Une fois que nous avons une référence à la cellule ou ligne nous pouvons ensuite adapter son apparence en définissant ses propriétés associées au style.

Accès par programme à une ligne nécessite que vous connaissez l’index de la ligne, qui commence à 0. Le `UnitPrice` ligne est la cinquième ligne dans le contrôle DetailsView, en lui donnant un index de 4 et le rendre accessible par programmation à l’aide de `ExpensiveProductsPriceInBoldItalic.Rows[4]`. À ce stade, nous aurions du contenu de la ligne entière affiché dans une police en gras, italique en utilisant le code suivant :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample4.cs)]

Toutefois, cela rend *les deux* l’étiquette (prix) et la valeur en gras et italique. Si nous voulons simplement la valeur en gras et italique nous avons besoin d’appliquer la mise en forme à la deuxième cellule de la ligne, ce qui peut être effectuée à l’aide de ce qui suit :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample5.cs)]

Nos didacticiels ont jusqu’ici utilisé des feuilles de style pour maintenir une séparation nette entre le balisage rendu et les informations relatives au style, plutôt que de définir les propriétés de style spécifiques comme indiqué ci-dessus nous allons au lieu de cela, utilisez une classe CSS. Ouvrez le `Styles.css` feuille de style et ajoutez une nouvelle classe CSS nommée `ExpensivePriceEmphasis` avec la définition suivante :


[!code-css[Main](custom-formatting-based-upon-data-cs/samples/sample6.css)]

Puis, dans le `DataBound` Gestionnaire d’événements, définir la cellule `CssClass` propriété `ExpensivePriceEmphasis`. Le code suivant illustre la `DataBound` Gestionnaire d’événements dans son intégralité :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample7.cs)]

Lors de l’affichage Tran, ce qui est inférieur à $75,00 des coûts, le prix est affiché dans une police normale (voir Figure 4). Toutefois, lors de l’affichage Mishi Kobe Niku, qui a un prix de 97.00 $, le prix est affiché dans une police en gras, italique (voir Figure 5).


[![Prix inférieur à $75,00 sont affichés dans une police normale](custom-formatting-based-upon-data-cs/_static/image9.png)](custom-formatting-based-upon-data-cs/_static/image8.png)

**Figure 4**: prix inférieur à $75,00 sont affichés dans une police normale ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image10.png))


[![Les prix des produits chers sont affichés dans un gras, italique police](custom-formatting-based-upon-data-cs/_static/image12.png)](custom-formatting-based-upon-data-cs/_static/image11.png)

**Figure 5**: les prix des produits chers sont affichés dans un gras, italique police ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image13.png))


## <a name="using-the-formview-controlsdataboundevent-handler"></a>À l’aide du contrôle FormView`DataBound`Gestionnaire d’événements

Les étapes permettant de déterminer les données sous-jacentes liées à un FormView sont identiques à celles de la création d’un contrôle DetailsView un `DataBound` effectuer un cast du Gestionnaire d’événements, le `DataItem` propriété pour le type d’objet approprié liée au contrôle et déterminer la marche à suivre. Le FormView et DetailsView diffèrent, toutefois, dans comment les apparence de l’interface utilisateur sont mise à jour.

Le FormView ne contient pas de n’importe quel BoundFields et par conséquent ne dispose pas de la `Rows` collection. Au lieu de cela, un FormView est composé des modèles, qui peuvent contenir une combinaison de fichiers HTML statiques, contrôles Web et la syntaxe de liaison de données. Ajuster le style d’un FormView généralement consiste à ajuster le style d’un ou plusieurs des contrôles Web dans les modèles FormView.

Pour illustrer cela, nous allons utiliser un FormView à répertorient les produits comme dans l’exemple précédent, mais cette fois, nous allons afficher seulement le nom de produit et les unités en stock avec les unités en stock affichée en rouge si elle est inférieure ou égale à 10.

## <a name="step-4-displaying-the-product-information-in-a-formview"></a>Étape 4 : Afficher les informations de produit dans un FormView

Ajouter un FormView à le `CustomColors.aspx` page sous le contrôle DetailsView et son `ID` propriété `LowStockedProductsInRed`. Lier le contrôle FormView au contrôle ObjectDataSource créé à partir de l’étape précédente. Cela crée un `ItemTemplate`, `EditItemTemplate`, et `InsertItemTemplate` pour le FormView. Supprimer le `EditItemTemplate` et `InsertItemTemplate` et simplifier la `ItemTemplate` à inclure uniquement les `ProductName` et `UnitsInStock` valeurs, chacune de leurs propres contrôles Label nommé de manière adéquate. Comme avec le contrôle DetailsView à partir de l’exemple précédent, également vérifier la case à cocher Activer la pagination dans la balise active du FormView.

Après ces modifications au balisage de votre FormView doit ressembler à ce qui suit :


[!code-aspx[Main](custom-formatting-based-upon-data-cs/samples/sample8.aspx)]

Notez que le `ItemTemplate` contient :

- **HTML statique** le texte « produit : » et « unités en Stock : » avec le `<br />` et `<b>` éléments.
- **Contrôles Web** deux contrôles Label, `ProductNameLabel` et `UnitsInStockLabel`.
- **Syntaxe de liaison de données** le `<%# Bind("ProductName") %>` et `<%# Bind("UnitsInStock") %>` syntaxe, ce qui affecte les valeurs de ces champs à des contrôles Label `Text` propriétés.

## <a name="step-5-programmatically-determining-the-value-of-the-data-in-the-databound-event-handler"></a>Étape 5 : Déterminer par programme la valeur des données dans le Gestionnaire d’événements de liaison de données

Avec le balisage FormView terminé, l’étape suivante consiste à déterminer par programme si le `UnitsInStock` valeur est inférieure ou égale à 10. Pour cela dans la même manière avec le contrôle FormView telle qu’elle était avec le contrôle DetailsView. Commencez par créer un gestionnaire d’événements pour les FormView `DataBound` événement.


![Créer le Gestionnaire d’événements de liaison de données](custom-formatting-based-upon-data-cs/_static/image14.png)

**Figure 6**: créer le `DataBound` Gestionnaire d’événements


Dans l’événement Gestionnaire effectué FormView `DataItem` propriété un `ProductsRow` de l’instance et de déterminer si le `UnitsInPrice` valeur est telle que nous avons besoin pour l’afficher dans une police rouge.


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample9.cs)]

## <a name="step-6-formatting-the-unitsinstocklabel-label-control-in-the-formviews-itemtemplate"></a>Étape 6 : Mettre en forme le contrôle Label de UnitsInStockLabel dans ItemTemplate de FormView

L’étape finale consiste à mettre en forme le texte affiché `UnitsInStock` valeur en rouge si la valeur est inférieur ou égal à 10. Pour cela nous devons accéder par programmation à la `UnitsInStockLabel` contrôler dans le `ItemTemplate` et définissez ses propriétés de style afin que son texte s’affiche en rouge. Pour accéder à un contrôle Web dans un modèle, utilisez la `FindControl("controlID")` méthode comme suit :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample10.cs)]

Dans notre exemple, nous souhaitons accéder à une étiquette de contrôle dont `ID` valeur est `UnitsInStockLabel`, donc nous utiliseriez :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample11.cs)]

Une fois que nous disposons d’une référence de programmation pour le contrôle Web, nous pouvons modifier ses propriétés associées au style en fonction des besoins. Comme avec l’exemple précédent, j’ai créé une classe CSS dans `Styles.css` nommé `LowUnitsInStockEmphasis`. Pour appliquer ce style pour le contrôle Web Label, définissez son `CssClass` propriété en conséquence.


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample12.cs)]

> [!NOTE]
> La syntaxe de mise en forme d’un modèle par programme l’accès à l’utilisation de contrôle Web `FindControl("controlID")` et puis en définissant ses propriétés associées au style peut également être utilisé lors de l’utilisation [TemplateField](https://msdn.microsoft.com/library/system.web.ui.webcontrols.templatefield(VS.80).aspx) dans le DetailsView ou GridView contrôles. Nous allons examiner TemplateField dans notre didacticiel suivant.


Figures 7 montre le FormView lors de l’affichage d’un produit dont `UnitsInStock` valeur est supérieure à 10, alors que le produit dans la Figure 8 a sa valeur inférieure à 10.


[![Pour les produits avec un suffisamment grandes unités en Stock, aucune mise en forme de personnalisé est appliqué](custom-formatting-based-upon-data-cs/_static/image16.png)](custom-formatting-based-upon-data-cs/_static/image15.png)

**Figure 7**: pour les produits avec un suffisamment grand Units In Stock, aucune mise en forme de personnalisé est appliqué ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image17.png))


[![Les unités en Stock nombre est indiqué en rouge pour les produits avec des valeurs de 10 ou moins](custom-formatting-based-upon-data-cs/_static/image19.png)](custom-formatting-based-upon-data-cs/_static/image18.png)

**Figure 8**: les unités dans le numéro de Stock est affichée en rouge pour les produits avec des valeurs de 10 ou moins ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image20.png))


## <a name="formatting-with-the-gridviewsrowdataboundevent"></a>Mise en forme avec le contrôle de GridView`RowDataBound`événement

Précédemment, nous avons examiné la séquence d’étapes DetailsView et FormView contrôle sa progression via lors de la liaison de données. Examinons sur ces étapes qu’une seule fois à nouveau en tant qu’un rappel.

1. Les données contrôle Web `DataBinding` se déclenche des événements.
2. Les données sont liées aux données de contrôle Web.
3. Les données contrôle Web `DataBound` se déclenche des événements.

Ces trois étapes simples suffisent pour le contrôle DetailsView et FormView parce qu’ils affichent un seul enregistrement. Pour le contrôle GridView, qui affiche *tous les* enregistrements liés à ce dernier (et pas seulement la première), l’étape 2 est un peu plus complexe.

Dans l’étape 2, le contrôle GridView énumère la source de données et, pour chaque enregistrement, crée un `GridViewRow` de l’instance et lie l’enregistrement en cours à ce dernier. Pour chaque `GridViewRow` ajouté au contrôle GridView, deux événements sont déclenchés :

- **`RowCreated`**se déclenche après la `GridViewRow` a été créé.
- **`RowDataBound`**se déclenche lorsque l’enregistrement actif a été liée à la `GridViewRow`.

Ensuite, pour le contrôle GridView, liaison de données est décrite plus précisément par la séquence d’étapes suivante :

1. Le contrôle du GridView `DataBinding` se déclenche des événements.
2. Les données sont liées au GridView.   
  
 Pour chaque enregistrement dans la source de données 

    1. Créer un `GridViewRow` objet
    2. Déclencher la `RowCreated` événement
    3. Lier l’enregistrement à la`GridViewRow`
    4. Déclencher la `RowDataBound` événement
    5. Ajouter le `GridViewRow` à la `Rows` collection
3. Le contrôle du GridView `DataBound` se déclenche des événements.

Pour personnaliser le format des enregistrements individuels du GridView, ensuite, nous devons créer un gestionnaire d’événements pour le `RowDataBound` événement. Pour illustrer cela, vous allez ajouter un contrôle GridView à la `CustomColors.aspx` page qui affiche le nom, la catégorie et le prix de chaque produit, mise en surbrillance les produits dont le prix est inférieur à $10,00 avec une couleur d’arrière-plan jaune.

## <a name="step-7-displaying-product-information-in-a-gridview"></a>Étape 7 : Affichage des informations sur les produits dans un GridView

Ajouter un contrôle GridView sous le contrôle FormView à partir de l’exemple précédent et définir son `ID` propriété `HighlightCheapProducts`. Étant donné que nous avons déjà un ObjectDataSource qui retourne tous les produits sur la page, lier le contrôle GridView à qui. Enfin, modifiez BoundFields du GridView pour inclure uniquement leurs noms, les catégories et les prix. Après ces modifications au balisage de la GridView doit ressembler à :


[!code-aspx[Main](custom-formatting-based-upon-data-cs/samples/sample13.aspx)]

La figure 9 illustre notre avancement à ce point à travers un navigateur.


[![Le contrôle GridView répertorie le nom, la catégorie et le prix de chaque produit](custom-formatting-based-upon-data-cs/_static/image22.png)](custom-formatting-based-upon-data-cs/_static/image21.png)

**Figure 9**: le contrôle GridView répertorie le nom, la catégorie et le prix de chaque produit ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image23.png))


## <a name="step-8-programmatically-determining-the-value-of-the-data-in-the-rowdatabound-event-handler"></a>Étape 8 : Déterminer par programme la valeur des données dans le Gestionnaire d’événements de RowDataBound

Lorsque le `ProductsDataTable` est lié au GridView son `ProductsRow` instances sont énumérés et pour chaque `ProductsRow` un `GridViewRow` est créé. Le `GridViewRow`de `DataItem` est affectée à la particulier `ProductRow`, après laquelle du GridView `RowDataBound` Gestionnaire d’événement est déclenché. Pour déterminer le `UnitPrice` valeur pour chaque produit liée au contrôle GridView, puis, nous devons créer un gestionnaire d’événements pour le contrôle du GridView `RowDataBound` événement. Dans ce gestionnaire d’événements, nous pouvons examiner la `UnitPrice` valeur en cours `GridViewRow` et prendre une décision de mise en forme pour cette ligne.

Ce gestionnaire d’événements peut être créé à l’aide de la même série d’étapes en tant qu’avec le FormView et DetailsView.


![Créer un gestionnaire d’événements pour l’événement de RowDataBound du GridView](custom-formatting-based-upon-data-cs/_static/image24.png)

**La figure 10**: créer un gestionnaire d’événements pour le contrôle du GridView `RowDataBound` événement


Création du Gestionnaire d’événements de cette manière entraîne le code suivant à automatiquement ajoutée à la partie du code de la page ASP.NET :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample14.cs)]

Lorsque le `RowDataBound` se déclenche des événements, le Gestionnaire d’événements est passé comme deuxième paramètre un objet de type `GridViewRowEventArgs`, qui a une propriété nommée `Row`. Cette propriété retourne une référence à la `GridViewRow` qui a été liées seulement les données. Pour accéder à la `ProductsRow` instance liée à la `GridViewRow` , nous utilisons le `DataItem` propriété comme suit :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample15.cs)]

Lorsque vous travaillez avec le `RowDataBound` Gestionnaire d’événements qu’il est important de garder à l’esprit que le contrôle GridView est composé de différents types de lignes et que cet événement est déclenché pour *tous les* les types de lignes. A `GridViewRow`de type peut être déterminé par ses `RowType` propriété et peut prendre l’une des valeurs possibles :

- `DataRow`une ligne qui est liée à un enregistrement à partir du contrôle de GridView`DataSource`
- `EmptyDataRow`la ligne affichée si le contrôle de GridView `DataSource` est vide
- `Footer`la ligne de pied de page ; affiché, si le contrôle de GridView `ShowFooter` est définie sur`true`
- `Header`la ligne d’en-tête ; indiqué si le contrôle de GridView ShowHeader est définie sur `true` (la valeur par défaut)
- `Pager`pour du contrôle GridView qui implémentent la pagination, la ligne qui affiche l’interface de pagination
- `Separator`ne pas utilisé pour le contrôle GridView, mais par le `RowType` propriétés de DataList et répéteur des contrôles, deux contrôles Web nous discuterons avenir didacticiels de données

Étant donné que la `EmptyDataRow`, `Header`, `Footer`, et `Pager` lignes ne sont pas associées à un `DataSource` enregistrement, ils ont toujours une `null` valeur pour leur `DataItem` propriété. Pour cette raison, avant de tenter d’utiliser actuel `GridViewRow`de `DataItem` propriété, nous devons Assurez-vous d’abord que nous manipulons un `DataRow`. Cela peut être accompli en vérifiant la `GridViewRow`de `RowType` propriété comme suit :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample16.cs)]

## <a name="step-9-highlighting-the-row-yellow-when-the-unitprice-value-is-less-than-1000"></a>Étape 9 : Mise en surbrillance la ligne jaune lorsque la valeur UnitPrice est inférieur à $10,00

La dernière étape consiste pour mettre en évidence par programmation de l’ensemble du `GridViewRow` si le `UnitPrice` valeur de cette ligne est inférieur à $10,00. La syntaxe permettant d’accéder à des lignes ou des cellules d’un contrôle GridView est le même que sur le contrôle DetailsView `GridViewID.Rows[index]` pour accéder à la ligne entière, `GridViewID.Rows[index].Cells[index]` pour accéder à une cellule particulière. Toutefois, lorsque le `RowDataBound` Gestionnaire d’événements déclenche à la limite de données `GridViewRow` doit encore être ajouté au contrôle de GridView `Rows` collection. Par conséquent, vous ne pouvez pas accéder actuel `GridViewRow` instance à partir de la `RowDataBound` Gestionnaire d’événements à l’aide de la collection de lignes.

Au lieu de `GridViewID.Rows[index]`, nous pouvons faire référence actuel `GridViewRow` d’instance dans le `RowDataBound` Gestionnaire d’événements à l’aide `e.Row`. Autrement dit, afin de mettre en surbrillance actuel `GridViewRow` instance à partir de la `RowDataBound` Gestionnaire d’événements, permettraient d’utiliser :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample17.cs)]

Au lieu de définir la `GridViewRow`de `BackColor` propriété directement, tenons-nous en à l’aide des classes CSS. J’ai créé une classe CSS nommée `AffordablePriceEmphasis` qui définit la couleur d’arrière-plan jaune. Terminé `RowDataBound` Gestionnaire d’événements suit :


[!code-csharp[Main](custom-formatting-based-upon-data-cs/samples/sample18.cs)]


[![Les produits les plus abordable sont mis en surbrillance en jaune](custom-formatting-based-upon-data-cs/_static/image26.png)](custom-formatting-based-upon-data-cs/_static/image25.png)

**Figure 11**: The Most des produits abordables sont mis en surbrillance en jaune ([cliquez pour afficher l’image en taille réelle](custom-formatting-based-upon-data-cs/_static/image27.png))


## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons vu comment mettre en forme le GridView, DetailsView et FormView basé sur les données liées au contrôle. Pour cela que nous avons créé un gestionnaire d’événements pour le `DataBound` ou `RowDataBound` événements, où les données sous-jacentes a été examinées avec une modification de mise en forme, si nécessaire. Pour accéder aux données liées à un contrôle DetailsView ou les FormView, nous utilisons le `DataItem` propriété dans le `DataBound` Gestionnaire d’événements ; pour un GridView, chaque `GridViewRow` l’instance `DataItem` propriété contient les données liées à cette ligne, qui est disponible dans le `RowDataBound` Gestionnaire d’événements.

La syntaxe pour l’ajustement par programmation de la mise en forme du contrôle Web de données varie selon le contrôle Web et la façon dont les données à mettre en forme s’affiche. DetailsView et GridView pour les contrôles, les lignes et les cellules accessibles par index ordinal. Pour le contrôle FormView, qui utilise des modèles, le `FindControl("controlID")` méthode est couramment utilisée pour localiser un contrôle Web à partir du modèle.

Dans l’étape suivante du didacticiel, nous allons examiner comment utiliser des modèles avec GridView et DetailsView. En outre, nous verrons une autre technique pour la personnalisation de la mise en forme basée sur les données sous-jacentes.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été E.R. Gillain, Dennis Patterson et Dan Jagers. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](using-templatefields-in-the-gridview-control-cs.md)
