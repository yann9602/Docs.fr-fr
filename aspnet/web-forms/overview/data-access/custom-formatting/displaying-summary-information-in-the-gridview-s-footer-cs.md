---
uid: web-forms/overview/data-access/custom-formatting/displaying-summary-information-in-the-gridview-s-footer-cs
title: "Affichage des informations de synthèse dans pied de page du GridView (c#) | Documents Microsoft"
author: rick-anderson
description: "Informations de résumé sont souvent affichées en bas du rapport dans une ligne de résumé. Le contrôle GridView peut inclure une ligne de pied de page dans des cellules dont nous pouvons pr..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: d50edc31-9286-4c6a-8635-be09e72752a4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-formatting/displaying-summary-information-in-the-gridview-s-footer-cs
msc.type: authoredcontent
ms.openlocfilehash: 0d3df976181a4641dbfffe77875989c77ece059d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="displaying-summary-information-in-the-gridviews-footer-c"></a>Affichage des informations de synthèse dans pied de page du GridView (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/6/9/969e5c94-dfb6-4e47-9570-d6d9e704c3c1/ASPNET_Data_Tutorial_15_CS.exe) ou [télécharger le PDF](displaying-summary-information-in-the-gridview-s-footer-cs/_static/datatutorial15cs1.pdf)

> Informations de résumé sont souvent affichées en bas du rapport dans une ligne de résumé. Le contrôle GridView peut inclure une ligne de pied de page dans des cellules dont nous pouvons par programmation injecter des données agrégées. Dans ce didacticiel, nous verrons comment afficher les données agrégées dans cette ligne de pied de page.


## <a name="introduction"></a>Introduction

Outre l’affichage de chacun des prix de produits, des unités en stock, les unités de commande et de réorganiser les niveaux, un utilisateur peut également être intéressé par les informations d’agrégation, telles que le prix moyen, le nombre total d’unités en stock et ainsi de suite. Ces informations de résumé sont souvent affichées en bas du rapport dans une ligne de résumé. Le contrôle GridView peut inclure une ligne de pied de page dans des cellules dont nous pouvons par programmation injecter des données agrégées.

Cette tâche propose trois défis :

1. Configurer le contrôle GridView pour afficher sa ligne de pied de page
2. Déterminer les données de synthèse ; Autrement dit, comment nous calcule le prix moyen ou le total des unités en stock ?
3. Les données de synthèse d’injection dans les cellules appropriées de la ligne de pied de page

Dans ce didacticiel, nous verrons comment relever ces défis. Plus précisément, nous allons créer une page qui répertorie les catégories dans une liste déroulante avec les produits de la catégorie sélectionnée affichés dans un contrôle GridView. Le contrôle GridView inclut une ligne de pied de page qui affiche le prix moyen et le nombre total d’unités en stock et en ordre pour les produits de cette catégorie.


[![Informations de résumé s’affiche dans la ligne de pied de page de GridView](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image2.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image1.png)

**Figure 1**: les informations de résumé s’affiche dans la ligne de pied de page du GridView ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image3.png))


Ce didacticiel, avec sa catégorie interface maître/détail de produits, s’appuie sur les concepts abordés dans la plus antérieure [filtrage de maître/détail avec un DropDownList](../masterdetail/master-detail-filtering-with-a-dropdownlist-cs.md) didacticiel. Si vous n’avez pas encore utilisé par le biais du didacticiel antérieur, veuillez le faire avant de poursuivre celle-ci.

## <a name="step-1-adding-the-categories-dropdownlist-and-products-gridview"></a>Étape 1 : Ajouter les catégories DropDownList et les produits GridView

Avant concernant nous-mêmes à l’ajout des informations de synthèse au pied de page du GridView, nous allons tout d’abord simplement créer le rapport maître/détail. Une fois que nous avons terminé cette première étape, nous allons examiner comment inclure des données de synthèse.

Commencez par ouvrir le `SummaryDataInFooter.aspx` page dans le `CustomFormatting` dossier. Ajouter un contrôle DropDownList et définir son `ID` à `Categories`. Ensuite, cliquez sur le lien de choisir la Source de données à partir de la balise de DropDownList et choisir d’ajouter un nouveau ObjectDataSource nommé `CategoriesDataSource` qui appelle le `CategoriesBLL` la classe `GetCategories()` (méthode).


[![Ajouter un nouveau ObjectDataSource nommé CategoriesDataSource](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image5.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image4.png)

**Figure 2**: ajouter un nouveau nommé de ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image6.png))


[![Avoir ObjectDataSource Invoke (méthode) de la classe CategoriesBLL GetCategories()](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image8.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image7.png)

**Figure 3**: le ObjectDataSource appeler le `CategoriesBLL` de classe `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image9.png))


Après avoir configuré l’ObjectDataSource, l’Assistant retourne à la Configuration de Source de données de DropDownList Assistant à partir de laquelle nous devons spécifier quelle valeur de champ de données doit être affiché et l’application doit correspondre à la valeur de la DropDownList `ListItem` s. Avoir le `CategoryName` champ s’affiché et l’utilisation du `CategoryID` comme valeur.


[![Utilisez le nom de catégorie et champs CategoryID en tant que le texte et la valeur pour les ListItems,](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image11.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image10.png)

**Figure 4**: utilisez le `CategoryName` et `CategoryID` champs comme le `Text` et `Value` pour le `ListItem` s, respectivement ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image12.png))


À ce stade, nous avons un DropDownList (`Categories`) qui répertorie les catégories dans le système. Nous devons maintenant ajouter un GridView qui répertorie les produits qui appartiennent à la catégorie sélectionnée. Avant de le faire, cependant, prenez un moment pour vérifier la case à cocher Activer AutoPostBack dans la balise de DropDownList. Comme indiqué dans le *filtrage de maître/détail avec un DropDownList* (didacticiel), en définissant de DropDownList `AutoPostBack` propriété `true` la page est publiée dans chaque fois que la valeur de DropDownList est modifiée. Cela entraîne le contrôle GridView à actualiser, indiquant les produits de la catégorie qui vient d’être sélectionnée. Si le `AutoPostBack` est définie sur `false` (la valeur par défaut), la modification de la catégorie ne provoqueront pas d’une publication (postback) et par conséquent ne sera pas mettre à jour les produits répertoriés.


[![La case Activer AutoPostBack dans la balise de DropDownList](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image14.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image13.png)

**Figure 5**: case à cocher Activer AutoPostBack dans la balise de DropDownList ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image15.png))


Ajouter un contrôle GridView à la page pour afficher les produits pour la catégorie sélectionnée. Définir le contrôle de GridView `ID` à `ProductsInCategory` et la lier à un nouveau ObjectDataSource nommé `ProductsInCategoryDataSource`.


[![Ajouter un nouveau ObjectDataSource nommé ProductsInCategoryDataSource](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image17.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image16.png)

**Figure 6**: ajouter un nouveau nommé de ObjectDataSource `ProductsInCategoryDataSource` ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image18.png))


Configurer l’ObjectDataSource afin qu’il appelle le `ProductsBLL` la classe `GetProductsByCategoryID(categoryID)` (méthode).


[![Avoir ObjectDataSource à appeler la méthode GetProductsByCategoryID(categoryID)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image20.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image19.png)

**Figure 7**: le ObjectDataSource appeler le `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image21.png))


Étant donné que la `GetProductsByCategoryID(categoryID)` méthode utilise un paramètre d’entrée, à l’étape finale de l’Assistant, nous pouvons spécifier la source de la valeur du paramètre. Pour afficher les produits de la catégorie sélectionnée, ont le paramètre extrait le `Categories` DropDownList.


[![Obtenir la valeur du paramètre categoryID dans la liste déroulante de catégories sélectionnées](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image23.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image22.png)

**Figure 8**: obtenir le  *`categoryID`*  valeur du paramètre dans la liste déroulante de catégories sélectionné ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image24.png))


Après avoir terminé l’Assistant GridView aura un BoundField pour chacune des propriétés de produit. Nous allons nettoyer ces BoundFields de sorte que seuls les `ProductName`, `UnitPrice`, `UnitsInStock`, et `UnitsOnOrder` BoundFields sont affichés. Vous pouvez ajouter des paramètres au niveau du champ à la BoundFields restantes (telles que la mise en forme le `UnitPrice` sous forme de devise). Après avoir apporté ces modifications, la balise déclarative de GridView doit ressembler à ce qui suit :


[!code-aspx[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample1.aspx)]

À ce stade, nous avons un rapport entièrement fonctionnel maître/détail qui affiche le nom, le prix unitaire, les unités en stock et des unités de commande pour les produits qui appartiennent à la catégorie sélectionnée.


[![Obtenir la valeur du paramètre categoryID dans la liste déroulante de catégories sélectionnées](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image26.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image25.png)

**Figure 9**: obtenir le  *`categoryID`*  valeur du paramètre dans la liste déroulante de catégories sélectionné ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image27.png))


## <a name="step-2-displaying-a-footer-in-the-gridview"></a>Étape 2 : Afficher un pied de page dans le contrôle GridView

Le contrôle GridView peut afficher un en-tête et le pied de page de ligne. Ces lignes sont affichées en fonction des valeurs de la `ShowHeader` et `ShowFooter` propriétés, respectivement, avec `ShowHeader` par défaut `true` et `ShowFooter` à `false`. Pour inclure un pied de page dans le GridView simplement définie sa `ShowFooter` propriété `true`.


[![La propriété du GridView ShowFooter la valeur true](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image29.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image28.png)

**La figure 10**: définir le contrôle de GridView `ShowFooter` propriété `true` ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image30.png))


La ligne de pied de page comporte une cellule pour chacun des champs définis dans le GridView ; Toutefois, ces cellules sont vides par défaut. Prenez un moment pour afficher la progression dans un navigateur. Avec la `ShowFooter` propriété maintenant la valeur `true`, le contrôle GridView inclut une ligne de pied de page vide.


[![Le contrôle GridView comprend désormais une ligne de pied de page](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image32.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image31.png)

**Figure 11**: le contrôle GridView inclut désormais une ligne de pied de page ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image33.png))


La ligne de pied de page dans la Figure 11 ne se distinguent, car elle a un arrière-plan blanc. Nous allons créer un `FooterStyle` de classe CSS dans `Styles.css` qui spécifie un arrière-plan rouge foncé, puis configurez le `GridView.skin` fichier d’apparence dans le `DataWebControls` thème pour affecter cette classe CSS pour le contrôle du GridView `FooterStyle`de `CssClass` propriété. Si vous souhaitez rafraîchir vos connaissances sur les apparences et thèmes, faire référence à la [affichage des données avec le ObjectDataSource](../basic-reporting/displaying-data-with-the-objectdatasource-cs.md) didacticiel.

Commencez par ajouter la classe CSS suivante pour `Styles.css`:


[!code-css[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample2.css)]

Le `FooterStyle` classe CSS est similaire dans un style à la `HeaderStyle` classe, bien que le `HeaderStyle`de couleur d’arrière-plan est plusieurs plus sombre et son texte s’affiche dans une police en gras. En outre, le texte du pied de page est aligné à droite tandis que le texte de l’en-tête est centré.

Ensuite, pour associer cette classe CSS à un pied de page de chaque GridView, ouvrez le `GridView.skin` de fichiers dans le `DataWebControls` thème et jeu le `FooterStyle`de `CssClass` propriété. Après l’ajout de ce balisage du fichier doit ressembler à :


[!code-aspx[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample3.aspx)]

Comme la capture d’écran ci-dessous illustre cette modification rend le pied de page ressortir plus clairement.


[![Ligne de pied de page du GridView a maintenant une couleur d’arrière-plan tirent](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image35.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image34.png)

**Figure 12**: ligne de pied de page du GridView a maintenant une couleur d’arrière-plan tirent ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image36.png))


## <a name="step-3-computing-the-summary-data"></a>Étape 3 : Calcul de données du résumé

Pied de page du GridView affiché, le défi suivant nos est comment calculer les données de synthèse. Il existe deux façons de calculer ces informations d’agrégation :

1. Via une requête SQL, nous aurions pu émettre une requête supplémentaire à la base de données pour calculer les données de synthèse pour une catégorie particulière. SQL inclut des fonctions d’agrégation avec un `GROUP BY` clause pour spécifier les données sur laquelle les données doivent être résumées. La requête SQL suivante serait ramener les informations nécessaires :  

    [!code-sql[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample4.sql)]

    Bien sûr vous ne souhaitez pas émettre de cette requête directement à partir de la `SummaryDataInFooter.aspx` page, mais plutôt en créant une méthode dans le `ProductsTableAdapter` et `ProductsBLL`.
2. Ces informations de calcul comme il est ajouté au contrôle GridView comme indiqué dans [personnalisé de mise en forme à données](custom-formatting-based-upon-data-cs.md) du (didacticiel), le contrôle GridView `RowDataBound` Gestionnaire d’événements se déclenche une fois pour chaque ligne ajoutée au contrôle GridView après son été lié aux données. En créant un gestionnaire d’événements pour cet événement, nous pouvons conserver en cours d’exécution totale des valeurs que vous souhaitez agréger. Après la dernière ligne de données a été liée au contrôle GridView que nous avons les totaux et les informations nécessaires pour calculer la moyenne.

J’utilise généralement la deuxième approche qu’il enregistre un voyage dans la base de données et les efforts requis pour implémenter les fonctionnalités de synthèse dans la couche d’accès aux données et la couche de logique métier, mais les deux approches suffirait. Pour ce didacticiel nous allons utiliser la deuxième option et suivre le total en cours d’exécution à l’aide du `RowDataBound` Gestionnaire d’événements.

Créer un `RowDataBound` Gestionnaire d’événements pour le contrôle GridView en sélectionnant le contrôle GridView dans le concepteur, en cliquant sur l’icône d’éclair à partir de la fenêtre Propriétés, en double-cliquant sur le `RowDataBound` événement. Cela crée un gestionnaire d’événements nommé `ProductsInCategory_RowDataBound` dans la `SummaryDataInFooter.aspx` classe code-behind de la page.


[!code-csharp[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample5.cs)]

Pour gérer un cumul, nous devons définir des variables en dehors de la portée du Gestionnaire d’événements. Créez les quatre variables au niveau des pages suivantes :

- `_totalUnitPrice`, de type`decimal`
- `_totalNonNullUnitPriceCount`, de type`int`
- `_totalUnitsInStock`, de type`int`
- `_totalUnitsOnOrder`, de type`int`

Ensuite, écrivez le code pour incrémenter de ces trois variables pour chaque ligne de données a rencontré dans le `RowDataBound` Gestionnaire d’événements.


[!code-csharp[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample6.cs)]

Le `RowDataBound` Gestionnaire d’événements démarre en veillant à ce que nous manipulons un DataRow. Une fois qu’est établie, le `Northwind.ProductsRow` instance lié à la `GridViewRow` dans l’objet `e.Row` est stocké dans la variable `product`. Les variables total en cours d’exécution suivants sont incrémentées par les valeurs correspondantes du produit en cours (en supposant qu’elles ne contiennent pas une base de données `NULL` valeur). Nous assurer le suivi de l’exécution de ces deux `UnitPrice` total et le nombre de non -`NULL` `UnitPrice` enregistre, car le prix moyen est le quotient de ces deux nombres.

## <a name="step-4-displaying-the-summary-data-in-the-footer"></a>Étape 4 : Afficher les données de synthèse dans le pied de page

Avec les données de synthèse totalisées, la dernière étape consiste à afficher dans la ligne de pied de page du GridView. Cette tâche, trop, peut être réalisée par programme via le `RowDataBound` Gestionnaire d’événements. N’oubliez pas que le `RowDataBound` Gestionnaire d’événements se déclenche pour *chaque* ligne qui est lié au GridView, y compris la ligne de pied de page. Par conséquent, nous pouvons améliorer notre gestionnaire d’événements pour afficher les données dans la ligne de pied de page à l’aide du code suivant :


[!code-csharp[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample7.cs)]

Étant donné que la ligne de pied de page est ajoutée au contrôle GridView après ont ajouté toutes les lignes de données, nous pouvons être certain qu’au moment où nous sommes prêts afficher les données de synthèse dans le pied de page que les calculs en cours d’exécution totales seront terminés. La dernière étape, est ensuite, pour définir ces valeurs dans les cellules du pied de page.

Pour afficher le texte dans une cellule de pied de page particulière, utilisez `e.Row.Cells[index].Text = value`, où le `Cells` indexation démarre à 0. Le code suivant calcule le prix moyen (le prix total divisé par le nombre de produits) et l’affiche, ainsi que le nombre total d’unités en stock et unités commandées dans les cellules de pied de page appropriée du contrôle GridView.


[!code-csharp[Main](displaying-summary-information-in-the-gridview-s-footer-cs/samples/sample8.cs)]

Figure 13 illustre le rapport une fois ce code a été ajouté. Notez comment le `ToString("c")` entraîne les prix moyen des informations de résumé à mettre en forme comme une devise.


[![Ligne de pied de page du GridView a maintenant une couleur d’arrière-plan tirent](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image38.png)](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image37.png)

**Figure 13**: ligne de pied de page du GridView a maintenant une couleur d’arrière-plan tirent ([cliquez pour afficher l’image en taille réelle](displaying-summary-information-in-the-gridview-s-footer-cs/_static/image39.png))


## <a name="summary"></a>Résumé

Affichage des données de synthèse est une exigence courante de rapport, et le contrôle GridView facilite l’utilisation d’inclure ces informations dans sa ligne de pied de page. La ligne de pied de page s’affiche lors du contrôle GridView `ShowFooter` est définie sur `true` et peut avoir le texte dans ses cellules définies par programme via le `RowDataBound` Gestionnaire d’événements. Calculer les données de synthèse soit faire en interrogeant de nouveau la base de données ou à l’aide de code dans la classe code-behind de la page ASP.NET pour les données de synthèse de calcul par programmation.

Ce didacticiel conclut notre examen de la mise en forme personnalisée avec les contrôles GridView, DetailsView et FormView. Notre didacticiel suivant démarre, notre exploration d’insertion, de mise à jour et de suppression des données à l’aide de ces mêmes contrôles.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](using-the-formview-s-templates-cs.md)
[Suivant](custom-formatting-based-upon-data-vb.md)
