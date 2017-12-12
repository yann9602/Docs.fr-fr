---
uid: web-forms/overview/data-access/custom-formatting/using-templatefields-in-the-detailsview-control-vb
title: "Utilisation de TemplateField dans le contrôle DetailsView (VB) | Documents Microsoft"
author: rick-anderson
description: "Les mêmes fonctionnalités TemplateField disponibles avec le contrôle GridView sont également disponibles avec le contrôle DetailsView. Dans ce didacticiel, nous affichons un produit..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 0b91d5f8-127d-4f6a-b204-f2e2b35ef703
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-formatting/using-templatefields-in-the-detailsview-control-vb
msc.type: authoredcontent
ms.openlocfilehash: b368a651253a569865bb92fa93d3462f88d8935f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-templatefields-in-the-detailsview-control-vb"></a>Utilisation de TemplateField dans le contrôle DetailsView (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/5/7/0/57084608-dfb3-4781-991c-407d086e2adc/ASPNET_Data_Tutorial_13_VB.exe) ou [télécharger le PDF](using-templatefields-in-the-detailsview-control-vb/_static/datatutorial13vb1.pdf)

> Les mêmes fonctionnalités TemplateField disponibles avec le contrôle GridView sont également disponibles avec le contrôle DetailsView. Dans ce didacticiel, nous affichons un produit à la fois à l’aide d’un contrôle DetailsView contenant TemplateField.


## <a name="introduction"></a>Introduction

Le TemplateField offre un degré plus élevé de flexibilité dans les données de rendu à la BoundField, CheckBoxField, HyperLinkField et autres contrôles de champ de données. Dans le [didacticiel précédent](using-templatefields-in-the-gridview-control-vb.md) nous avons examiné à l’aide de la TemplateField dans un contrôle GridView à :

- Afficher plusieurs valeurs de champ de données dans une colonne. Plus précisément, les deux le `FirstName` et `LastName` champs ont été combinés en une seule colonne GridView.
- Utilisation d’un autre contrôle de Web pour exprimer une valeur de champ de données. Nous avons vu comment afficher les `HiredDate` valeur à l’aide d’un contrôle calendrier.
- Afficher les informations d’état basées sur les données sous-jacentes. Alors que le `Employees` table ne contient pas une colonne qui retourne le nombre de jours pendant lesquels un employé a été lors de la tâche, nous avons pu afficher ces informations dans l’exemple de GridView dans le didacticiel précédent à l’aide d’un TemplateField et la méthode de mise en forme.

Les mêmes fonctionnalités TemplateField disponibles avec le contrôle GridView sont également disponibles avec le contrôle DetailsView. Dans ce didacticiel, nous affichons un produit à la fois à l’aide d’un contrôle DetailsView qui contient deux TemplateField. La première TemplateField permet de combiner le `UnitPrice`, `UnitsInStock`, et `UnitsOnOrder` des champs de données dans une ligne de DetailsView. La deuxième TemplateField affichera la valeur de la `Discontinued` champ, mais utilise une méthode de mise en forme pour afficher le « YES » si `Discontinued` est `True`et « Non » dans le cas contraire.


[![Deux TemplateField est utilisés pour personnaliser l’affichage](using-templatefields-in-the-detailsview-control-vb/_static/image2.png)](using-templatefields-in-the-detailsview-control-vb/_static/image1.png)

**Figure 1**: TemplateField deux sont utilisés pour personnaliser l’affichage ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image3.png))


C’est parti !

## <a name="step-1-binding-the-data-to-the-detailsview"></a>Étape 1 : Liaison de données au contrôle DetailsView

Comme indiqué dans le didacticiel précédent, lorsque vous travaillez avec TemplateField, il est souvent plus facile de commencer par créer le contrôle DetailsView qui contient juste BoundFields puis ajoutez TemplateField nouvelle ou convertir le BoundFields existant TemplateField en tant que nécessaire . Par conséquent, ce didacticiel de démarrage en ajoutant un contrôle DetailsView à la page via le concepteur et en liant à un ObjectDataSource qui retourne la liste des produits. Ces étapes créera un DetailsView avec BoundFields pour chacun des champs de valeur non booléenne du produit et un CheckBoxField pour ce champ valeur booléenne (indisponible).

Ouvrez le `DetailsViewTemplateField.aspx` page et faites glisser un contrôle DetailsView à partir de la boîte à outils vers le concepteur. À partir de la balise active du DetailsView choisir d’ajouter un nouveau contrôle ObjectDataSource qui appelle le `ProductsBLL` la classe `GetProducts()` (méthode).


[![Ajouter un nouveau contrôle ObjectDataSource qui appelle la méthode GetProducts()](using-templatefields-in-the-detailsview-control-vb/_static/image5.png)](using-templatefields-in-the-detailsview-control-vb/_static/image4.png)

**Figure 2**: ajouter un nouveau contrôle ObjectDataSource qu’Invoke le `GetProducts()` (méthode) ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image6.png))


Pour ce rapport, supprimez le `ProductID`, `SupplierID`, `CategoryID`, et `ReorderLevel` BoundFields. Ensuite, réorganiser le BoundFields afin que le `CategoryName` et `SupplierName` BoundFields se trouveront immédiatement après le `ProductName` BoundField. N’hésitez pas à ajuster le `HeaderText` propriétés et les propriétés de mise en forme pour la BoundFields comme vous le souhaitez. Comme avec le GridView, ces modifications au niveau de BoundField peuvent être effectuées via la boîte de dialogue champs (accessible en cliquant sur le lien Modifier les champs dans la balise active du DetailsView) ou via la syntaxe déclarative. Enfin, effacer du DetailsView `Height` et `Width` des valeurs de propriété afin de permettre le contrôle DetailsView contrôle sont développés en fonction des données affichées et cochez la case Activer la pagination dans la balise active.

Après avoir apporté ces modifications, la balise déclarative de votre contrôle DetailsView doit ressembler à ce qui suit :


[!code-aspx[Main](using-templatefields-in-the-detailsview-control-vb/samples/sample1.aspx)]

Prenez un moment pour afficher la page via un navigateur. À ce stade, vous devez voir un seul produit répertorié (Tran) avec des lignes indiquant le nom du produit, catégorie, fournisseur, prix, les unités en stock, unités commandées et son état abandonné.


[![Les détails du produit sont affichés à l’aide d’une série de BoundFields](using-templatefields-in-the-detailsview-control-vb/_static/image8.png)](using-templatefields-in-the-detailsview-control-vb/_static/image7.png)

**Figure 3**: détails du produit sont affichés à l’aide d’une série de BoundFields ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image9.png))


## <a name="step-2-combining-the-price-units-in-stock-and-units-on-order-into-one-row"></a>Étape 2 : Combinant les prix, les unités en Stock et les unités commandées dans une ligne

Le contrôle DetailsView a une ligne pour le `UnitPrice`, `UnitsInStock`, et `UnitsOnOrder` champs. Nous pouvons combiner ces champs de données dans une seule ligne avec TemplateField en ajoutant un nouveau TemplateField ou en convertissant un existants `UnitPrice`, `UnitsInStock`, et `UnitsOnOrder` BoundFields en TemplateField. Alors que je préfère conversion BoundFields existant, entraînez-vous en ajoutant un nouveau TemplateField.

Commencez par cliquer sur le lien Modifier les champs dans la balise du DetailsView pour afficher la boîte de dialogue champs. Ensuite, ajoutez un nouveau TemplateField et définissez son `HeaderText` à la propriété « Inventaire et de prix » et déplacement du nouveau TemplateField afin qu’il est positionné au-dessus de la `UnitPrice` BoundField.


[![Ajouter un nouveau TemplateField au contrôle DetailsView](using-templatefields-in-the-detailsview-control-vb/_static/image11.png)](using-templatefields-in-the-detailsview-control-vb/_static/image10.png)

**Figure 4**: ajouter un nouveau TemplateField au contrôle DetailsView ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image12.png))


Étant donné que cette nouvelle TemplateField contiendra les valeurs actuellement affichées dans le `UnitPrice`, `UnitsInStock`, et `UnitsOnOrder` BoundFields, nous allons les supprimer.

La dernière tâche de cette étape consiste à définir le `ItemTemplate` balisage pour le prix et l’inventaire TemplateField, ce qui peut être effectuée par le biais du DetailsView modification de modèle interface dans le concepteur ou manuellement via la syntaxe déclarative du contrôle. Comme avec le GridView, interface de modification de modèle du DetailsView est accessible en cliquant sur le lien Modifier les modèles dans la balise active. À ce stade, vous pouvez sélectionner le modèle de la modifier dans la liste déroulante, puis ajouter des contrôles Web à partir de la boîte à outils.

Pour ce didacticiel, commencez par ajouter un contrôle Label au prix et de stock TemplateField `ItemTemplate`. Ensuite, cliquez sur le lien Modifier les DataBindings à partir de la balise du contrôle Web Label et lier le `Text` propriété le `UnitPrice` champ.


[![Lier la propriété de texte de l’étiquette pour le champ des données de prix unitaire](using-templatefields-in-the-detailsview-control-vb/_static/image14.png)](using-templatefields-in-the-detailsview-control-vb/_static/image13.png)

**Figure 5**: lier l’étiquette `Text` propriété le `UnitPrice` champ de données ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image15.png))


## <a name="formatting-the-price-as-a-currency"></a>Le prix de la mise en forme comme une devise

Avec cet ajout, l’étiquette Web prix et le contrôle TemplateField d’inventaire seront affichent désormais uniquement le prix du produit sélectionné. La figure 6 illustre une capture d’écran de notre progression jusque-là lorsqu’ils sont affichés via un navigateur.


[![Le prix et l’inventaire TemplateField affiche le prix](using-templatefields-in-the-detailsview-control-vb/_static/image17.png)](using-templatefields-in-the-detailsview-control-vb/_static/image16.png)

**Figure 6**: le prix et inventaire TemplateField affiche le prix ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image18.png))


Notez que le prix du produit n’est pas mis en forme comme une valeur monétaire. Avec un BoundField, mise en forme est possible en définissant le `HtmlEncode` propriété `False` et `DataFormatString` propriété `{0:formatSpecifier}`. Pour un TemplateField, toutefois, les instructions de mise en forme doivent être spécifiées dans la syntaxe de liaison de données ou via l’utilisation d’une méthode de mise en forme défini quelque part dans le code de l’application (comme classe code-behind de la page ASP.NET).

Pour spécifier la mise en forme pour la syntaxe de liaison de données utilisée dans le contrôle Web Label, retourner à la boîte de dialogue DataBindings en cliquant sur le lien Modifier les DataBindings à partir de la balise active de l’étiquette. Vous pouvez taper les instructions de mise en forme directement dans la liste déroulante Format ou sélectionnez une des chaînes de format définie. Par exemple lors de la BoundField `DataFormatString` propriété, la mise en forme est spécifié à l’aide de `{0:formatSpecifier}`.

Pour le `UnitPrice` champ Utilisation de la mise en forme de devise spécifié en sélectionnant la valeur de la liste déroulante ou en tapant dans `{0:C}` manuellement.


[![Mettre en forme le prix sous forme de devise](using-templatefields-in-the-detailsview-control-vb/_static/image20.png)](using-templatefields-in-the-detailsview-control-vb/_static/image19.png)

**Figure 7**: mettre en forme le prix sous forme de devise ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image21.png))


De façon déclarative, la spécification de mise en forme est indiquée en tant que deuxième paramètre dans le `Bind` ou `Eval` méthodes. Les paramètres simplement effectuées via le résultat de concepteur dans l’expression de liaison de données suivante dans le balisage déclaratif :


[!code-aspx[Main](using-templatefields-in-the-detailsview-control-vb/samples/sample2.aspx)]

## <a name="adding-the-remaining-data-fields-to-the-templatefield"></a>Ajout d’autres champs de données à le TemplateField

À ce stade nous avons affichée et mise en forme le `UnitPrice` données champ de prix et de l’inventaire TemplateField, mais devront quand même afficher le `UnitsInStock` et `UnitsOnOrder` champs. Nous allons les afficher sur une ligne sous le prix et entre parenthèses. À partir de l’interface de modification de modèle dans le concepteur, ces balises peuvent être ajoutés en positionnant le curseur dans le modèle et en tapant simplement dans le texte à afficher. Vous pouvez également ce balisage peut être entré directement dans la syntaxe déclarative.

Ajoutez le balisage statique, les contrôles Web Label et syntaxe de liaison de données afin que le prix et l’inventaire TemplateField affiche des informations de prix et d’inventaire comme suit :

*Prix unitaire*  
(**En Stock / de commande :** *UnitsInStock* / *UnitsOnOrder*)

Après avoir effectué cette tâche balisage déclaratif de votre contrôle DetailsView doit ressembler à ce qui suit :


[!code-aspx[Main](using-templatefields-in-the-detailsview-control-vb/samples/sample3.aspx)]

Avec ces modifications, nous avons consolidées les informations de prix et de stock en une seule ligne de DetailsView.


[![Le prix et les informations d’inventaire s’affiche dans une ligne unique](using-templatefields-in-the-detailsview-control-vb/_static/image23.png)](using-templatefields-in-the-detailsview-control-vb/_static/image22.png)

**Figure 8**: le prix et les informations d’inventaire s’affiche dans une ligne unique ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image24.png))


## <a name="step-3-customizing-the-discontinued-field-information"></a>Étape 3 : Personnaliser les informations de champ éponyme a

Le `Products` la table `Discontinued` colonne est une valeur de bit qui indique si le produit a été abandonné. Lorsque vous liez un contrôle DetailsView (ou GridView) à un contrôle de source de données, les champs de la valeur booléenne, tels que `Discontinued`, sont implémentés en tant que CheckBoxFields tandis que les champs de valeur non Boolean, tels que `ProductID`, `ProductName`, et ainsi de suite, sont implémentés en tant que BoundFields. Le CheckBoxField rendu sous la forme d’une case à cocher désactivée qui est activée si la valeur du champ de données est défini sur True et elle est désactivée dans le cas contraire.

Au lieu d’afficher le CheckBoxField nous pouvons adopter à afficher à la place texte indiquant le produit n’est plus disponible ou non. Pour ce faire, nous avons Retirez le CheckBoxField DetailsView et puis ajoutez un BoundField dont `DataField` a pris la valeur de propriété `Discontinued`. Prenez un moment pour ce faire. Une fois cette modification DetailsView affiche le texte « True » pour les produits abandonnés et « False » pour les produits qui sont toujours actifs.


[![Les chaînes True et False sont utilisées pour afficher l’état abandonné](using-templatefields-in-the-detailsview-control-vb/_static/image26.png)](using-templatefields-in-the-detailsview-control-vb/_static/image25.png)

**Figure 9**: les chaînes True et False sont utilisées pour afficher l’état Abandonné ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image27.png))


Imaginons que nous souhaitons n’a pas que les chaînes « True » ou « False » à utiliser, mais « Oui » et « Non », à la place. Cette personnalisation peut être effectuée à l’aide d’un TemplateField et une méthode de mise en forme. Une méthode de mise en forme peut prendre n’importe quel nombre de paramètres d’entrée, mais elle doit retourner le code HTML (en tant que chaîne) pour injecter dans le modèle.

Ajoutez une méthode de mise en forme à la `DetailsViewTemplateField.aspx` classe code-behind de la page nommée `DisplayDiscontinuedAsYESorNO` qui accepte un `Northwind.ProductsRow` objet comme paramètre d’entrée et retourne une chaîne. Comme indiqué dans le didacticiel précédent, cette méthode *doit* être marqué comme `Protected` ou `Public` afin d’être accessible à partir du modèle.


[!code-vb[Main](using-templatefields-in-the-detailsview-control-vb/samples/sample4.vb)]

Cette méthode vérifie le paramètre d’entrée (`discontinued`) et retourne « Oui » si elle est `True`, « Non » dans le cas contraire.

> [!NOTE]
> Dans la méthode de mise en forme examinée dans le rappel de didacticiel précédent qui nous étions en passant dans un champ de données qui peut-être contenir `NULL` s et par conséquent, si nécessaire pour vérifier si l’employé `HiredDate` valeur de la propriété avait une base de données `NULL` valeur avant l’accès à la `EmployeesRow`de `HiredDate` propriété. Cette vérification n’est pas nécessaire ici depuis la `Discontinued` colonne ne peut jamais avoir de base de données `NULL` valeurs affectées. En outre, il s’agit raison pour laquelle la méthode peut accepter une valeur booléenne d’entrée de paramètre au lieu de devoir accepter un `ProductsRow` instance ou un paramètre de type `Object`.


Cette méthode de mise en forme terminée, il ne reste qu’à appeler à partir de la TemplateField `ItemTemplate`. Pour créer le TemplateField soit supprimer la `Discontinued` BoundField et ajouter un nouveau TemplateField ou de convertir le `Discontinued` BoundField en TemplateField. Puis, dans la vue de balisage déclaratif, modifiez le TemplateField pour qu’il contienne uniquement un ItemTemplate qui appelle le `DisplayDiscontinuedAsYESorNO` méthode, en passant la valeur de la `ProductRow` l’instance `Discontinued` propriété. Vous pouvez y accéder le `Eval` (méthode). Plus précisément, les balises de la TemplateField doivent ressembler à :


[!code-aspx[Main](using-templatefields-in-the-detailsview-control-vb/samples/sample5.aspx)]

Cela entraîne le `DisplayDiscontinuedAsYESorNO` méthode à appeler lors du rendu du DetailsView, en passant le `ProductRow` l’instance `Discontinued` valeur. Étant donné que la `Eval` méthode retourne une valeur de type `Object`, mais la `DisplayDiscontinuedAsYESorNO` méthode attend un paramètre d’entrée de type `Boolean`, nous avons effectué la `Eval` méthodes retournent la valeur à `Boolean`. Le `DisplayDiscontinuedAsYESorNO` méthode puis retourne « Oui » ou « Non » selon la valeur qu’il reçoit. La valeur retournée est ce qui est affiché dans ce contrôle DetailsView (voir la Figure 10) de la ligne.


[![Valeurs Oui ou non sont maintenant affichés dans la ligne de fin de série](using-templatefields-in-the-detailsview-control-vb/_static/image29.png)](using-templatefields-in-the-detailsview-control-vb/_static/image28.png)

**La figure 10**: valeurs Oui ou non sont maintenant affichés dans la ligne de fin de série ([cliquez pour afficher l’image en taille réelle](using-templatefields-in-the-detailsview-control-vb/_static/image30.png))


## <a name="summary"></a>Résumé

Le TemplateField dans le contrôle DetailsView permet un degré plus élevé de flexibilité dans l’affichage des données qu’est disponible avec les autres contrôles de champ et sont idéales pour les situations où :

- Plusieurs champs de données doivent être affichés dans une colonne de GridView
- La date est exprimée mieux à l’aide d’un contrôle Web plutôt qu’en texte brut
- La sortie varie selon les données sous-jacentes, telles que l’affichage des métadonnées ou de reformater les données

Alors que TemplateField permettre une plus grande souplesse dans le rendu des données sous-jacentes du DetailsView, la sortie de DetailsView est toujours bruts un peu comme chaque champ est restitué sous la forme d’une ligne dans le code HTML `<table>`.

Le contrôle FormView offre une plus grande souplesse dans la configuration de la sortie rendue. Le FormView ne contient pas de champs, mais plutôt qu’une série de modèles (`ItemTemplate`, `EditItemTemplate`, `HeaderTemplate`, et ainsi de suite). Nous allons voir comment utiliser le contrôle FormView pour obtenir davantage de contrôle de la disposition de rendu dans notre didacticiel suivant.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Dan Jagers. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](using-templatefields-in-the-gridview-control-vb.md)
[Suivant](using-the-formview-s-templates-vb.md)
