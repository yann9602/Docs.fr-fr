---
uid: web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/formatting-the-datalist-and-repeater-based-upon-data-cs
title: "Mise en forme du contrôle DataList et répéteur en fonction des données (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allez parcourir les exemples de la façon dont nous mettre en forme l’apparence des contrôles DataList et répéteur, à l’aide des fonctions de mise en forme avec..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/13/2006
ms.topic: article
ms.assetid: 83e3d759-82b8-41e6-8d62-f0f4b3edec41
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/formatting-the-datalist-and-repeater-based-upon-data-cs
msc.type: authoredcontent
ms.openlocfilehash: 604aa63919a881e828b6a3620360c3d1133c5830
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="formatting-the-datalist-and-repeater-based-upon-data-c"></a>Mise en forme du contrôle DataList et répéteur en fonction des données (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_30_CS.exe) ou [télécharger le PDF](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/datatutorial30cs1.pdf)

> Dans ce didacticiel, nous allez parcourir les exemples de la façon dont nous mettre en forme l’apparence des contrôles DataList et répéteur, à l’aide des fonctions de mise en forme dans des modèles ou en gérant l’événement lié aux données.


## <a name="introduction"></a>Introduction

Comme nous l’avons vu dans le didacticiel précédent, le contrôle DataList offre un nombre de propriétés associées au style qui affectent son apparence. En particulier, nous avons vu comment affecter des classes CSS par défaut du contrôle DataList s `HeaderStyle`, `ItemStyle`, `AlternatingItemStyle`, et `SelectedItemStyle` propriétés. En plus de ces quatre propriétés DataList comprend d’autres propriétés associées au style, tel que `Font`, `ForeColor`, `BackColor`, et `BorderWidth`, à titre d’exemple. Le contrôle du répéteur ne contienne pas toutes les propriétés associées au style. Tous les paramètres de style ce type doivent être effectuées directement dans le balisage dans les modèles de s répéteur.

Souvent, cependant, comment les données doivent être mis en forme dépend des données lui-même. Par exemple, la liste des produits souhaitions afficher les informations de produit dans une couleur de police en gris clair si elle n’est plus disponible, ou nous pouvons choisir de mettre en surbrillance le `UnitsInStock` valeur si elle est égale à zéro. Comme nous l’avons vu dans les didacticiels précédents, le GridView, DetailsView et FormView offrent à mettre en forme leur apparence en fonction de leurs données de deux façons :

- **Le `DataBound` événement** créer un gestionnaire d’événements approprié `DataBound` événement qui se déclenche lorsque les données a été liées à chaque élément (pour le contrôle GridView qu’il a été le `RowDataBound` événement ; pour le contrôle DataList et répéteur est la `ItemDataBound`événement). Dans ce cas, gestionnaire, les données simplement liées peut être examinée et mise en forme des décisions. Nous avons examiné cette technique dans la [personnalisé de mise en forme à données](../custom-formatting/custom-formatting-based-upon-data-cs.md) didacticiel.
- **Mise en forme de fonctions dans les modèles de** lors de l’utilisation de TemplateField dans les contrôles DetailsView ou GridView, ou un modèle dans le contrôle FormView, nous pouvons ajouter une fonction de mise en forme pour la classe code-behind de pages ASP.NET, la couche de logique métier ou un autre bibliothèque de classes qui est accessible à partir de l’application web. Cette fonction de mise en forme peut accepter un nombre arbitraire de paramètres d’entrée, mais elle doit retourner le code HTML à restituer dans le modèle. Tout d’abord, les fonctions de mise en forme ont été examinées dans le [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-cs.md) didacticiel.

Les deux de ces techniques de mise en forme sont disponibles avec les contrôles DataList et répéteur. Dans ce didacticiel, nous allez parcourir les exemples d’utilisation de ces deux techniques pour les deux contrôles.

## <a name="using-theitemdataboundevent-handler"></a>À l’aide de la`ItemDataBound`Gestionnaire d’événements

Lorsque les données sont liées à un contrôle DataList, à partir d’un contrôle de source de données ou par le biais de par programmation les données au contrôle s `DataSource` propriété et en appelant sa `DataBind()` (méthode), du contrôle DataList s `DataBinding` événement est déclenché, la source de données énumérée, et chaque enregistrement de données est lié au contrôle DataList. Pour chaque enregistrement dans la source de données, le contrôle DataList crée un [ `DataListItem` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalistitem.aspx) de l’objet qui est ensuite lié à l’enregistrement actif. Pendant ce processus, le contrôle DataList déclenche deux événements :

- **`ItemCreated`**se déclenche après la `DataListItem` a été créé.
- **`ItemDataBound`**se déclenche lorsque l’enregistrement actif a été liée à la`DataListItem`

Les étapes suivantes décrivent le processus de liaison de données pour le contrôle DataList.

1. DataList s [ `DataBinding` événement](https://msdn.microsoft.com/library/system.web.ui.control.databinding.aspx) se déclenche
2. Les données sont liées au contrôle DataList  
  
 Pour chaque enregistrement dans la source de données 

    1. Créer un `DataListItem` objet
    2. Déclencher la [ `ItemCreated` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.itemcreated.aspx)
    3. Lier l’enregistrement à la`DataListItem`
    4. Déclencher la [ `ItemDataBound` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.itemdatabound.aspx)
    5. Ajouter le `DataListItem` à la `Items` collection

Lors de la liaison de données pour le contrôle du répéteur, il passe via exactement la même séquence d’étapes. La seule différence est qu’au lieu de `DataListItem` utilise des instances en cours de création, le répéteur [ `RepeaterItem` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.repeateritem(VS.80).aspx)s.

> [!NOTE]
> Le lecteur perspicace avez peut-être remarqué une légère anomalie entre la séquence d’étapes passer lorsque la DataList et répéteur liés aux données et lorsque le contrôle GridView est lié aux données. À la fin du processus de liaison de données, le contrôle GridView déclenche le `DataBound` événement ; Toutefois, le contrôle du répéteur ni DataList ont un tel événement. Il s’agit, car les contrôles DataList et répéteur ont été créés dans le délai d’exécution ASP.NET 1.x, avant que le modèle de gestionnaire d’événements antérieurs et postérieurs au niveau était devenu commun.


Comme avec le GridView, une option de mise en forme de basés sur les données pour créer un gestionnaire d’événements pour le `ItemDataBound` événement. Ce gestionnaire d’événements serait inspecter les données qui avaient simplement été liées à la `DataListItem` ou `RepeaterItem` et affectent la mise en forme du contrôle en fonction des besoins.

Pour le contrôle DataList, mise en forme pour l’élément entier peut être implémentée à l’aide de la `DataListItem` associées au style propriétés, notamment la norme `Font`, `ForeColor`, `BackColor`, `CssClass`, et ainsi de suite. Pour affecter la mise en forme de contrôles Web particuliers dans le modèle de s DataList, nous devons accéder par programme et de modifier le style de ces contrôles Web. Nous avons vu comment accomplir cette sauvegarde dans le *personnalisé de mise en forme à données* didacticiel. Comme le contrôle du répéteur, le `RepeaterItem` classe n’a pas de propriétés associées au style ; par conséquent, toutes les modifications associées au style apportées à un `RepeaterItem` dans le `ItemDataBound` Gestionnaire d’événements doit être réalisée par l’accès à et de contrôles Web au sein de la mise à jour par programme le modèle.

Étant donné que la `ItemDataBound` technique de mise en forme pour le contrôle DataList et répéteur sont virtuellement identiques, notre exemple se concentrera sur l’utilisation du contrôle DataList.

## <a name="step-1-displaying-product-information-in-the-datalist"></a>Étape 1 : Affichage des informations de produit du contrôle DataList

Avant de nous soucier de la mise en forme, s permettent de créer une page qui utilise un contrôle DataList pour afficher des informations sur le produit. Dans le [didacticiel précédent](displaying-data-with-the-datalist-and-repeater-controls-cs.md) , nous avons créé un contrôle DataList dont `ItemTemplate` affiche chaque nom de produit s, la catégorie, la fournisseur, la quantité par unité et le prix. Permettent de s Répétez cette fonctionnalité ici dans ce didacticiel. Pour ce faire, vous pouvez soit recréer DataList et son ObjectDataSource à partir de zéro, ou vous pouvez copier sur ces contrôles à partir de la page créée dans le didacticiel précédent (`Basics.aspx`) et les coller dans la page pour ce didacticiel (`Formatting.aspx`).

Une fois que vous n’ayez répliqué les fonctionnalités DataList et ObjectDataSource à `Basics.aspx` dans `Formatting.aspx`, prenez un moment pour modifier le contrôle DataList s `ID` propriété à partir de `DataList1` à plus descriptif `ItemDataBoundFormattingExample`. Dans un navigateur, affichage du contrôle DataList. Comme le montre la Figure 1, la seule différence de mise en forme entre chaque produit est que la couleur d’arrière-plan alterne.


[![Les produits répertoriés dans le contrôle DataList](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image2.png)](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image1.png)

**Figure 1**: les produits répertoriés dans le contrôle DataList ([cliquez pour afficher l’image en taille réelle](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image3.png))


Pour ce didacticiel, permettent de mettre en forme du contrôle DataList telles que tous les produits dont le prix est inférieur à $20,00 aura à la fois son nom et le prix unitaire affiché en surbrillance jaune s.

## <a name="step-2-programmatically-determining-the-value-of-the-data-in-the-itemdatabound-event-handler"></a>Étape 2 : Déterminer par programme la valeur des données dans le Gestionnaire d’événement ItemDataBound

Uniquement les produits dont le prix sous $20,00 ayant la mise en forme personnalisée appliquée, nous devons être en mesure de déterminer le prix de chaque s produit. Lors de la liaison de données à un contrôle DataList, DataList énumère les enregistrements dans sa source de données et, pour chaque enregistrement, crée un `DataListItem` instance, la liaison de l’enregistrement de source de données pour le `DataListItem`. Une fois l’enregistrement particulier s des données a été liées à actuel `DataListItem` objet, du contrôle DataList s `ItemDataBound` événement est déclenché. Nous pouvons créer un gestionnaire d’événements pour cet événement inspecter les valeurs de données en cours `DataListItem` et, en fonction de ces valeurs, effectuez toutes les modifications de mise en forme nécessaires.

Créer un `ItemDataBound` événement du contrôle DataList et ajoutez le code suivant :


[!code-csharp[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample1.cs)]

Alors que le concept et la sémantique derrière du contrôle DataList s `ItemDataBound` Gestionnaire d’événements sont les mêmes que celles utilisées par les s GridView `RowDataBound` Gestionnaire d’événements dans le *personnalisé de mise en forme lors de données* didacticiel, la syntaxe est différente. légèrement. Lorsque le `ItemDataBound` se déclenche des événements, le `DataListItem` simplement lié aux données a été passée dans le Gestionnaire d’événements correspondant via `e.Item` (au lieu de `e.Row`, comme avec le contrôle GridView s `RowDataBound` Gestionnaire d’événements). DataList s `ItemDataBound` Gestionnaire d’événements se déclenche pour *chaque* ligne ajoutée au contrôle DataList, y compris les lignes d’en-tête, des lignes de pied de page et des lignes de séparation. Toutefois, les informations de produit sont uniquement liées aux lignes de données. Par conséquent, lorsque vous utilisez le `ItemDataBound` pour inspecter les données d’événement lié à du contrôle DataList, nous devons d’abord vous assurer que nous re fonctionne avec un élément de données. Cela peut être accompli en vérifiant la `DataListItem` s [ `ItemType` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalistitem.itemtype.aspx), qui peut avoir une des [huit valeurs suivantes](https://msdn.microsoft.com/library/system.web.ui.webcontrols.listitemtype.aspx):

- `AlternatingItem`
- `EditItem`
- `Footer`
- `Header`
- `Item`
- `Pager`
- `SelectedItem`
- `Separator`

Les deux `Item` et `AlternatingItem``DataListItem` les éléments de données de composition s DataList s. En supposant que nous re fonctionne avec un `Item` ou `AlternatingItem`, nous avons accès réel `ProductsRow` instance qui a été liée à actuel `DataListItem`. Le `DataListItem` s [ `DataItem` propriété](https://msdn.microsoft.com/system.web.ui.webcontrols.datalistitem.dataitem.aspx) contient une référence à la `DataRowView` objet, dont `Row` propriété fournit une référence au véritable `ProductsRow` objet.

Ensuite, nous vérifions le `ProductsRow` instance s `UnitPrice` propriété. Depuis la table Products s `UnitPrice` champ permet de `NULL` valeurs, avant d’accéder à la `UnitPrice` propriété nous devons Vérifiez tout d’abord pour voir s’il possède un `NULL` valeur à l’aide de la `IsUnitPriceNull()` (méthode). Si le `UnitPrice` valeur n’est pas `NULL`, nous devez vérifier si elle s $20,00 inférieure. S’il s’agit effectivement sous $20,00, nous devons ensuite appliquer la mise en forme personnalisée.

## <a name="step-3-highlighting-the-product-s-name-and-price"></a>Étape 3 : Mise en surbrillance le nom de produit et le prix

Une fois que nous savons que le prix d’un produit s est inférieur à 20,00 $, il ne reste qu’à mettre en évidence son nom et le prix. Pour ce faire, nous devons tout d’abord faire référence par programme les contrôles Label dans le `ItemTemplate` qui affichent le nom de produit s et le prix. Ensuite, nous avons besoin de les afficher un arrière-plan jaune. Ces informations de mise en forme peuvent être appliquées en modifiant directement les étiquettes `BackColor` propriétés (`LabelID.BackColor = Color.Yellow`) ; dans l’idéal, cependant, tous les aspects liés à l’affichage doivent être exprimés via des feuilles de style en cascade. En fait, nous avons déjà une feuille de style qui fournit la mise en forme souhaité défini dans `Styles.css`  -  `AffordablePriceEmphasis`, qui a été créé et présenté dans le *personnalisé de mise en forme lors de données* didacticiel.

Pour appliquer la mise en forme, il suffit de définir les deux contrôles Web Label `CssClass` propriétés `AffordablePriceEmphasis`, comme illustré dans le code suivant :


[!code-csharp[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample2.cs)]

Avec la `ItemDataBound` Gestionnaire d’événements terminé, réexaminez le `Formatting.aspx` page dans un navigateur. Comme le montre la Figure 2, les produits dont le prix est inférieur à 20,00 € ont leur nom et le prix mis en surbrillance.


[![Ces produits moins 20,00 $ sont mis en surbrillance](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image5.png)](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image4.png)

**Figure 2**: ceux produits inférieur à $20,00 sont mis en surbrillance ([cliquez pour afficher l’image en taille réelle](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image6.png))


> [!NOTE]
> Étant donné que le contrôle DataList est restituée sous la forme d’un élément HTML `<table>`, ses `DataListItem` instances possèdent des propriétés associées au style qui peuvent être définies pour appliquer un style spécifique à l’élément entier. Par exemple, si nous voulions mettre en surbrillance le *entière* élément jaune lorsque son prix était inférieur à $20,00, nous avons ont remplacé le code qui référence les étiquettes et définir leurs `CssClass` propriétés avec la ligne de code suivante : `e.Item.CssClass = "AffordablePriceEmphasis"` (voir Figure 3).


Le `RepeaterItem` qui composent le contrôle du répéteur, toutefois, don t offrent ces propriétés au niveau du style. Par conséquent, appliquer une mise en forme personnalisée pour le répéteur nécessite que l’application de propriétés de style pour les contrôles Web dans les modèles de répéteur s, comme nous l’avons fait dans la Figure 2.


[![L’élément de produit entier est mis en surbrillance pour les produits sous 20,00 $](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image8.png)](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image7.png)

**Figure 3**: l’élément de produit entier est mis en surbrillance pour les produits sous 20,00 $ ([cliquez pour afficher l’image en taille réelle](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image9.png))


## <a name="using-formatting-functions-from-within-the-template"></a>À l’aide de fonctions mise en forme à partir du modèle

Dans le *à l’aide de TemplateField dans le contrôle GridView* didacticiel, nous avons vu comment utiliser une fonction de mise en forme dans GridView TemplateField pour appliquer une mise en forme personnalisées basées sur les données liées aux lignes s GridView. Une fonction de mise en forme est une méthode qui peut être appelée à partir d’un modèle et retourne le code HTML à être émis à la place. Fonctions de mise en forme peut se trouver dans la classe code-behind de pages ASP.NET ou peuvent être centralisées dans des fichiers de classe dans le `App_Code` dossier ou dans un projet de bibliothèque de classes distinct. Déplacement de la fonction de mise en forme en dehors de la classe de code-behind ASP.NET page s est idéale si vous prévoyez d’utiliser la même fonction de mise en forme dans plusieurs pages ASP.NET ou dans d’autres applications web ASP.NET.

Pour illustrer les fonctions de mise en forme, s permettent de disposer des informations produit incluent le texte [DISCONTINUED] en regard du nom de produit s si elle s abandonné. En outre, permettent de s ont if jaune en surbrillance prix il s inférieur à $20,00 (comme nous l’avons fait le `ItemDataBound` exemple de gestionnaire d’événements) ; si le prix est 20,00 $ ou s plus élevé, vous permettent pas d’afficher le prix réel, mais le texte, veuillez appeler à la place de devis. Figure 4 présente une capture d’écran des produits répertoriant ces règles de mise en forme appliquée.


[![Pour les produits coûteuse, le prix est remplacé par le texte, appelez de devis](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image11.png)](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image10.png)

**Figure 4**: pour les produits coûteuses, le prix est remplacé par le texte, appelez de devis ([cliquez pour afficher l’image en taille réelle](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image12.png))


## <a name="step-1-create-the-formatting-functions"></a>Étape 1 : Créer les fonctions de mise en forme

Pour cet exemple nous avons besoin de deux fonctions de mise en forme, un qui affiche le nom du produit, ainsi que le texte [DISCONTINUED], si nécessaire et l’autre qui affiche soit un prix en surbrillance si elle s inférieure 20,00 $ ou le texte, appelez de devis dans le cas contraire. Permettent de s créer ces fonctions dans la classe code-behind de pages ASP.NET et nommez-les `DisplayProductNameAndDiscontinuedStatus` et `DisplayPrice`. Les deux méthodes doivent retourner le code HTML à restituer sous forme de chaîne et les deux doivent être marqués `Protected` (ou `Public`) pour pouvoir être appelée à partir de la partie de la syntaxe déclarative de pages ASP.NET. Le code pour ces deux méthodes suivante :


[!code-csharp[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample3.cs)]

Notez que la `DisplayProductNameAndDiscontinuedStatus` méthode accepte les valeurs de la `productName` et `discontinued` champs de données sous forme de valeurs scalaires, alors que le `DisplayPrice` méthode accepte un `ProductsRow` instance (plutôt qu’un `unitPrice` valeur scalaire). Chacune de ces approches fonctionneront ; Toutefois, si la fonction de mise en forme fonctionne avec des valeurs scalaires qui peuvent contenir de base de données `NULL` valeurs (tel que `UnitPrice`; ni `ProductName` ni `Discontinued` autoriser `NULL` valeurs), une attention particulière doit prendre dans la gestion de ces entrées scalaires.

En particulier, le paramètre d’entrée doit être de type `Object` étant donné que la valeur entrante peut être un `DBNull` instance au lieu du type de données attendu. En outre, un contrôle doit être effectué pour déterminer si la valeur entrante est une base de données `NULL` valeur. Autrement dit, si nous voulions le `DisplayPrice` méthode pour accepter le prix comme une valeur scalaire, nous d avoir à utiliser le code suivant :


[!code-csharp[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample4.cs)]

Notez que la `unitPrice` paramètre d’entrée est de type `Object` et que l’instruction conditionnelle a été modifiée afin de déterminer si `unitPrice` est `DBNull` ou non. En outre, depuis le `unitPrice` paramètre d’entrée est passé comme un `Object`, il doit être converti en une valeur décimale.

## <a name="step-2-calling-the-formatting-function-from-the-datalist-s-itemtemplate"></a>Étape 2 : Appel de la fonction de mise en forme à partir de l’ItemTemplate DataList s

Avec les fonctions de mise en forme ajoutées à la classe code-behind ASP.NET page s, il ne reste qu’à appeler ces fonctions à partir du contrôle DataList s de mise en forme `ItemTemplate`. Pour appeler une fonction de mise en forme à partir d’un modèle, placez l’appel de fonction dans la syntaxe de liaison de données :


[!code-aspx[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample5.aspx)]

Dans le contrôle DataList s `ItemTemplate` le `ProductNameLabel` contrôle Web Label actuellement, affiche le nom du produit s en attribuant son `Text` propriété le résultat de `<%# Eval("ProductName") %>`. Pour qu’il affiche le nom et le texte [DISCONTINUED], si nécessaire, mettez à jour la syntaxe déclarative afin qu’il affecte à la place la `Text` la valeur de la propriété de la `DisplayProductNameAndDiscontinuedStatus` (méthode). Dans ce cas, vous devez passer le nom de produit s et les valeurs supprimées à l’aide de la `Eval("columnName")` syntaxe. `Eval`Retourne une valeur de type `Object`, mais la `DisplayProductNameAndDiscontinuedStatus` méthode attend des paramètres d’entrée de type `String` et `Boolean`; par conséquent, nous devons convertir les valeurs retournées par la `Eval` méthode pour les types de paramètre d’entrée attendu, comme suit :


[!code-aspx[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample6.aspx)]

Pour afficher le prix, nous pouvons simplement définir le `UnitPriceLabel` étiquette s `Text` propriété à la valeur retournée par la `DisplayPrice` méthode, comme nous l’avez fait pour afficher le nom du produit s et [supprimé] texte. Toutefois, au lieu de passer dans le `UnitPrice` comme paramètre d’entrée scalaire, nous passons à la place dans l’ensemble `ProductsRow` instance :


[!code-aspx[Main](formatting-the-datalist-and-repeater-based-upon-data-cs/samples/sample7.aspx)]

Avec les appels aux fonctions de mise en forme en place, prenez un moment pour afficher la progression dans un navigateur. Votre écran doit ressembler à la Figure 5, avec les produits supprimées, y compris le texte [DISCONTINUED] et les produits d’évaluation des coûts plus les 20,00 $ ayant leur prix remplacé par le texte, l’appel de devis.


[![Pour les produits coûteuse, le prix est remplacé par le texte, appelez de devis](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image14.png)](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image13.png)

**Figure 5**: pour les produits coûteuses, le prix est remplacé par le texte, appelez de devis ([cliquez pour afficher l’image en taille réelle](formatting-the-datalist-and-repeater-based-upon-data-cs/_static/image15.png))


## <a name="summary"></a>Récapitulatif

Mise en forme le contenu d’un contrôle DataList ou répéteur basées sur les données peut être accomplie à l’aide de deux techniques. La première technique consiste à créer un gestionnaire d’événements pour le `ItemDataBound` événement qui se déclenche que chaque enregistrement dans la source de données est lié à un nouveau `DataListItem` ou `RepeaterItem`. Dans le `ItemDataBound` Gestionnaire d’événements, les données actuelles de l’élément s peuvent être examinées et mise en forme peut être appliqué au contenu du modèle ou, pour `DataListItem` s, à l’élément lui-même.

Vous pouvez également la mise en forme personnalisée peut être réalisée via les fonctions de mise en forme. Une fonction de mise en forme est une méthode qui peut être appelée à partir du contrôle DataList ou modèles s répéteur qui retourne le code HTML à émettre à la place. Souvent, le code HTML retourné par une fonction de mise en forme est déterminé par les valeurs liées à l’élément actuel. Ces valeurs peuvent être passées dans la fonction de mise en forme, comme des valeurs scalaires ou en passant l’objet entier qui est lié à l’élément (comme le `ProductsRow` instance).

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Yaakov Ellis Randy Schmidt et Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](displaying-data-with-the-datalist-and-repeater-controls-cs.md)
[Suivant](showing-multiple-records-per-row-with-the-datalist-control-cs.md)
