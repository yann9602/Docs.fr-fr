---
uid: web-forms/overview/data-access/paging-and-sorting/sorting-custom-paged-data-cs
title: "Tri personnalisé de données (c#) paginées | Documents Microsoft"
author: rick-anderson
description: "Dans le didacticiel précédent, nous avons appris comment implémenter la pagination personnalisée lorsque presentating des données sur une page web. Dans ce didacticiel, nous expliquons comment étendre l’exemple précédent..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/15/2006
ms.topic: article
ms.assetid: 778baa4e-4af8-4665-947e-7a01d1a4dff2
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting/sorting-custom-paged-data-cs
msc.type: authoredcontent
ms.openlocfilehash: f171929da3610f70f3641030d9a5fdb88f610f7f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="sorting-custom-paged-data-c"></a>Tri personnalisé paginée des données (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_26_CS.exe) ou [télécharger le PDF](sorting-custom-paged-data-cs/_static/datatutorial26cs1.pdf)

> Dans le didacticiel précédent, nous avons appris comment implémenter la pagination personnalisée lorsque presentating des données sur une page web. Dans ce didacticiel, nous expliquons comment étendre l’exemple précédent pour prendre en charge pour le tri de la pagination personnalisée.


## <a name="introduction"></a>Introduction

Par rapport à la pagination par défaut, la pagination personnalisée peut améliorer les performances de la pagination des données de plusieurs commandes de grandeur, fabrication personnalisée de la pagination le choix d’implémentation d’un échange de fait lors de la pagination de grandes quantités de données. L’implémentation de la pagination personnalisée est plus complexe que l’implémentation par défaut la pagination, toutefois, en particulier lorsque vous ajoutez à la combinaison de tri. Dans ce didacticiel, nous allons étendre l’exemple à partir de le précédent pour inclure la prise en charge pour le tri *et* la pagination personnalisée.

> [!NOTE]
> Étant donné que ce didacticiel s’appuie sur celui précédent, avant le début prenez un moment pour copier la syntaxe déclarative dans le `<asp:Content>` élément à partir de la page web de didacticiel s précédent (`EfficientPaging.aspx`) et le coller entre le `<asp:Content>` élément dans le `SortParameter.aspx` page. Faire référence à l’étape 1 de la [Ajout de contrôles de Validation à la modification et l’insertion des Interfaces](../editing-inserting-and-deleting-data/adding-validation-controls-to-the-editing-and-inserting-interfaces-cs.md) didacticiel pour une présentation plus détaillée sur la réplication de la fonctionnalité d’une page ASP.NET vers un autre.


## <a name="step-1-reexamining-the-custom-paging-technique"></a>Étape 1 : Réexamen la Technique de la pagination personnalisée

Pour la pagination personnalisée fonctionne correctement, nous devons implémenter une technique qui peut récupérer efficacement un sous-ensemble particulier d’enregistrements selon certains paramètres de l’Index de ligne de début et le nombre maximal de lignes. Il existe un certain nombre de techniques qui peut être utilisé pour atteindre cet objectif. Dans le didacticiel précédent, nous avons abordé il s’agissait à l’aide de Microsoft SQL Server 2005 s nouvelle `ROW_NUMBER()` fonction de classement. En bref, la `ROW_NUMBER()` fonction de classement affecte un numéro de ligne pour chaque ligne retournée par une requête qui est classée selon un ordre de tri spécifié. Le sous-ensemble approprié d’enregistrements est ensuite obtenu en retournant une section spécifique des résultats numérotées. La requête suivante illustre comment utiliser cette technique pour retourner les produits numérotées de 11 à 20 lorsque les résultats de classement classées par ordre alphabétique par le `ProductName`:


[!code-sql[Main](sorting-custom-paged-data-cs/samples/sample1.sql)]

Cette technique fonctionne bien pour la pagination à l’aide d’un ordre de tri spécifique (`ProductName` triées par ordre alphabétique, dans ce cas), mais la requête doit être modifiée pour afficher les résultats triés par une expression de tri différent. Dans l’idéal, la requête ci-dessus peut être réécrit pour utiliser un paramètre dans le `OVER` clause, comme suit :


[!code-sql[Main](sorting-custom-paged-data-cs/samples/sample2.sql)]

Malheureusement, paramétrables `ORDER BY` clauses ne sont pas autorisés. Au lieu de cela, nous devons créer une procédure stockée qui accepte un `@sortExpression` paramètre d’entrée, mais utilise une des solutions suivantes :

- Écrire des requêtes de codé en dur pour chacune des expressions de tri qui peuvent être utilisées ; Ensuite, utilisez `IF/ELSE` instructions T-SQL pour déterminer la requête à exécuter.
- Utilisez un `CASE` instruction pour fournir dynamique `ORDER BY` expressions en fonction de la `@sortExpressio` n paramètre d’entrée ; consultez la section dynamiquement les résultats de requête de tri dans permet [la puissance de SQL `CASE` instructions](http://www.4guysfromrolla.com/webtech/102704-1.shtml) Pour plus d’informations.
- Concevoir la requête appropriée sous forme de chaîne dans la procédure stockée, puis utilisez [le `sp_executesql` procédure stockée système](https://msdn.microsoft.com/en-us/library/ms188001.aspx) pour exécuter la requête dynamique.

Chacune de ces solutions de contournement présente certains inconvénients. La première option n’est pas aussi facile à gérer en tant que les deux autres car il nécessite que vous créez une requête pour chaque expression de tri possibles. Par conséquent, si vous décidez ultérieurement ajouter des champs de nouveau, pouvant être triées au GridView vous devrez également revenir en arrière et mettre à jour la procédure stockée. La deuxième approche a certaines subtilités qui présentent des problèmes de performances lors du tri des colonnes de la base de données autre qu’une chaîne et souffre également les mêmes problèmes de facilité de gestion en tant que la première. Et la troisième option, qui utilise des instructions SQL dynamiques, présente le risque d’attaque par injection SQL si un intrus est en mesure d’exécuter la procédure stockée, en passant les valeurs de paramètre d’entrée de leur choix.

Si aucune de ces approches est parfait, je pense que la troisième option est la meilleure des trois. Avec l’utilisation d’instructions SQL dynamiques, il offre un niveau de flexibilité que les deux autres ne le font pas. En outre, d’une attaque par injection SQL ne peut être exploitée que si un intrus est en mesure d’exécuter la procédure stockée, en passant les paramètres d’entrée de son choix. Étant donné que la couche DAL utilise des requêtes paramétrables, ADO.NET protéger ces paramètres sont envoyés à la base de données à travers l’architecture, ce qui signifie que la vulnérabilité d’attaque par injection SQL existe uniquement si la personne malveillante peut exécuter directement la procédure stockée.

Pour implémenter cette fonctionnalité, créez une nouvelle procédure stockée dans la base de données Northwind nommé `GetProductsPagedAndSorted`. Cette procédure stockée doit disposer de trois paramètres d’entrée : `@sortExpression`, un paramètre d’entrée de type `nvarchar(100`) qui spécifie la façon dont les résultats doivent être triées et est injecté directement après le `ORDER BY` texte dans le `OVER` clause ; et `@startRowIndex` et `@maximumRows`, les mêmes paramètres d’entrée de deux entiers à partir de la `GetProductsPaged` examinée dans le didacticiel précédent de procédure stockée. Créer le `GetProductsPagedAndSorted` utilisant le script suivant de procédure stockée :


[!code-sql[Main](sorting-custom-paged-data-cs/samples/sample3.sql)]

La procédure stockée commence en garantissant une valeur pour le `@sortExpression` paramètre a été spécifié. S’il est manquant, les résultats sont classés par `ProductID`. Ensuite, la requête SQL dynamique est construite. Notez que la requête SQL dynamique ici diffère légèrement des nos requêtes précédentes permet d’extraire toutes les lignes de la table Products. Dans les exemples précédentes, nous avons obtenu de chaque catégorie associée produit s s et fournisseur noms s à l’aide d’une sous-requête. Cette décision a été effectuée dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-cs.md) didacticiel et a été effectué au lieu d’utiliser `JOIN` s, car le TableAdapter ne peut pas créer automatiquement associé insert, update et delete méthodes tel requêtes. Le `GetProductsPagedAndSorted` une procédure stockée, cependant, doit utiliser `JOIN` s pour les résultats par les noms de catégorie ou le fournisseur.

Cette requête dynamique est constituée en concaténant les parties de requête statique et `@sortExpression`, `@startRowIndex`, et `@maximumRows` paramètres. Étant donné que `@startRowIndex` et `@maximumRows` sont des paramètres d’entiers, celles-ci doivent être converties en nvarchars pour pouvoir être concaténés correctement. Une fois que cette requête SQL dynamique a été construite, elle est exécutée `sp_executesql`.

Prenez un moment pour tester cette procédure stockée avec des valeurs différentes pour le `@sortExpression`, `@startRowIndex`, et `@maximumRows` paramètres. À partir de l’Explorateur de serveurs, avec le bouton droit sur le nom de la procédure stockée et choisissez Exécuter. Cela affiche la boîte de dialogue Exécuter la procédure stockée dans laquelle vous pouvez entrer les paramètres d’entrée (voir Figure 1). Pour trier les résultats par le nom de catégorie, utiliser le nom de catégorie pour le `@sortExpression` valeur du paramètre ; pour trier par nom de société fournisseur s, utilisez CompanyName. Après avoir entré les valeurs de paramètres, cliquez sur OK. Les résultats sont affichés dans la fenêtre Sortie. La figure 2 illustre les résultats de retourner des produits classées 11 à 20 lors de la commande par le `UnitPrice` dans l’ordre décroissant.


![Essayer différentes valeurs pour la procédure stockée s trois paramètres d’entrée](sorting-custom-paged-data-cs/_static/image1.png)

**Figure 1**: essayer différentes valeurs pour la procédure stockée s trois paramètres d’entrée


[![La procédure stockée s les résultats s’affichent dans la fenêtre Sortie](sorting-custom-paged-data-cs/_static/image3.png)](sorting-custom-paged-data-cs/_static/image2.png)

**Figure 2**: s de la procédure stockée, les résultats s’affichent dans la fenêtre Sortie ([cliquez pour afficher l’image en taille réelle](sorting-custom-paged-data-cs/_static/image4.png))


> [!NOTE]
> Lorsque les résultats de classement par le `ORDER BY` colonne dans la `OVER` clause, SQL Server doit trier les résultats. Ceci est une opération rapide s’il existe un index cluster sur les colonnes, les résultats sont classées par ou s’il existe une couverture d’index, mais peut être plus coûteux dans le cas contraire. Pour améliorer les performances des requêtes suffisamment volumineuses, envisagez l’ajout d’un index non cluster pour la colonne par laquelle les résultats sont triés par. Reportez-vous à [fonctions de classement et de performances dans SQL Server 2005](http://www.sql-server-performance.com/ak_ranking_functions.asp) pour plus d’informations.


## <a name="step-2-augmenting-the-data-access-and-business-logic-layers"></a>Étape 2 : Augmentation de l’accès aux données et les couches de logique métier

Avec la `GetProductsPagedAndSorted` une procédure stockée créée, l’étape suivante consiste à fournir un moyen pour exécuter cette procédure stockée via notre architecture de l’application. Cela implique l’ajout d’une méthode appropriée pour la couche DAL et la couche BLL. Permettent de s commencez par ajouter une méthode à la couche DAL. Ouvrir le `Northwind.xsd` DataSet typé, avec le bouton droit sur le `ProductsTableAdapter`, puis choisissez l’option Ajouter une requête dans le menu contextuel. Comme nous l’avons fait dans le didacticiel précédent, nous souhaitons configurer cette nouvelle méthode de la couche DAL pour utiliser une procédure stockée existante - `GetProductsPagedAndSorted`, dans ce cas. Démarrez en indiquant que vous souhaitez que la nouvelle méthode du TableAdapter pour utiliser une procédure stockée existante.


![Choisissez d’utiliser une procédure stockée existante](sorting-custom-paged-data-cs/_static/image5.png)

**Figure 3**: choisissez d’utiliser une procédure stockée existante


Pour spécifier la procédure stockée à utiliser, sélectionnez le `GetProductsPagedAndSorted` procédure dans la liste déroulante stockée dans l’écran suivant.


![Utilisez le GetProductsPagedAndSorted procédure stockée](sorting-custom-paged-data-cs/_static/image6.png)

**Figure 4**: utiliser le GetProductsPagedAndSorted procédure stockée


Cette procédure stockée renvoie un jeu d’enregistrements que pour ses résultats par conséquent, dans l’écran suivant, indiquent qu’il retourne des données tabulaires.


![Indiquer que la procédure stockée renvoie des données tabulaires](sorting-custom-paged-data-cs/_static/image7.png)

**Figure 5**: indiquer que la procédure stockée renvoie des données tabulaires


Enfin, créez des méthodes de la couche DAL qui utilisent à la fois le remplissage un DataTable et retourner un DataTable des modèles, les méthodes d’affectation de noms `FillPagedAndSorted` et `GetProductsPagedAndSorted`, respectivement.


![Choisissez les noms de méthodes](sorting-custom-paged-data-cs/_static/image8.png)

**Figure 6**: choisissez les noms de méthodes


Maintenant que nous avons étendu la couche DAL, nous re prêt à activer à la couche BLL. Ouvrez le `ProductsBLL` fichier de classe et ajoutez une nouvelle méthode `GetProductsPagedAndSorted`. Cette méthode accepte trois paramètres d’entrée doit `sortExpression`, `startRowIndex`, et `maximumRows` et doit simplement appeler vers le bas dans la couche DAL s `GetProductsPagedAndSorted` (méthode), comme suit :


[!code-csharp[Main](sorting-custom-paged-data-cs/samples/sample4.cs)]

## <a name="step-3-configuring-the-objectdatasource-to-pass-in-the-sortexpression-parameter"></a>Étape 3 : Configuration ObjectDataSource à passer dans le paramètre SortExpression

Avoir complété la couche DAL et la couche BLL pour inclure des méthodes qui utilisent la `GetProductsPagedAndSorted` une procédure stockée, tout ce qui reste consiste à configurer l’ObjectDataSource dans le `SortParameter.aspx` page à utiliser la nouvelle méthode de la couche BLL et pour passer le `SortExpression` paramètre basé sur le colonne de l’utilisateur a demandé à trier les résultats.

Commencez par modifier le s ObjectDataSource `SelectMethod` de `GetProductsPaged` à `GetProductsPagedAndSorted`. Cela est possible via l’Assistant Configurer la Source de données, à partir de la fenêtre Propriétés, ou directement par le biais de la syntaxe déclarative. Ensuite, nous avons besoin fournir une valeur pour ObjectDataSource s [ `SortParameterName` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.sortparametername.aspx). Si cette propriété est définie, ObjectDataSource essaie de passer dans le GridView s `SortExpression` propriété le `SelectMethod`. En particulier, ObjectDataSource recherche un paramètre d’entrée dont le nom est égal à la valeur de la `SortParameterName` propriété. Depuis la couche BLL s `GetProductsPagedAndSorted` méthode possède le paramètre d’entrée de tri expression nommé `sortExpression`, définissez le s ObjectDataSource `SortExpression` propriété SortExpression.

Après avoir apporté ces deux modifications, la syntaxe déclarative de s ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](sorting-custom-paged-data-cs/samples/sample5.aspx)]

> [!NOTE]
> Comme avec le didacticiel précédent, vérifiez que l’ObjectDataSource ne *pas* incluent les paramètres d’entrée sortExpression, startRowIndex ou maximumRows dans sa collection SelectParameters.


Pour activer le tri dans le GridView, cochez simplement la case à cocher Activer le tri dans la balise active de s GridView, qui définit le GridView s `AllowSorting` propriété `true` et entraîne le texte d’en-tête pour chaque colonne se présentent sous la forme d’un bouton de lien. Lorsque l’utilisateur final clique sur l’un de l’en-tête LinkButton, se poursuivent d’une publication (postback), les étapes suivantes s’écouler :

1. Les mises à jour de GridView son [ `SortExpression` propriété](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.gridview.sortexpression.aspx) à la valeur de la `SortExpression` du champ dont le lien en-tête utilisateur a cliqué
2. ObjectDataSource appelle la couche BLL s `GetProductsPagedAndSorted` méthode, en passant le contrôle GridView s `SortExpression` propriété en tant que la valeur de la méthode s `sortExpression` paramètre d’entrée (ainsi que les `startRowIndex` et `maximumRows` les valeurs de paramètre d’entrée)
3. La couche BLL appelle la couche DAL s `GetProductsPagedAndSorted` (méthode)
4. La couche DAL exécute le `GetProductsPagedAndSorted` une procédure stockée, passant dans le `@sortExpression` paramètre (avec la `@startRowIndex` et `@maximumRows` les valeurs de paramètre d’entrée)
5. La procédure stockée retourne un sous-ensemble approprié de données à la couche BLL, qui renvoie à l’ObjectDataSource ; ces données sont ensuite liées au GridView rendues en HTML. et envoyées à l’utilisateur final

La figure 7 illustre la première page de résultats triés par le `UnitPrice` dans l’ordre croissant.


[![Les résultats sont triés par le prix unitaire](sorting-custom-paged-data-cs/_static/image10.png)](sorting-custom-paged-data-cs/_static/image9.png)

**Figure 7**: les résultats sont triés par le prix unitaire ([cliquez pour afficher l’image en taille réelle](sorting-custom-paged-data-cs/_static/image11.png))


Alors que l’implémentation actuelle trier correctement les résultats par nom de produit, nom de catégorie, quantité par unité et le prix unitaire, une tentative classer les résultats par le fournisseur nom aboutit à une exception d’exécution (voir la Figure 8).


![Une tentative trier les résultats les résultats de fournisseur de l’Exception de Runtime suivante](sorting-custom-paged-data-cs/_static/image12.png)

**Figure 8**: tentative trier les résultats les résultats de fournisseur de l’Exception de Runtime suivante


Cette exception se produit parce que le `SortExpression` des s GridView `SupplierName` BoundField a la valeur `SupplierName`. Toutefois, le fournisseur s nom dans la `Suppliers` table est réellement appelée `CompanyName` nous avons un alias du nom de cette colonne en tant que `SupplierName`. Toutefois, le `OVER` clause utilisée par le `ROW_NUMBER()` fonction ne peut pas utiliser l’alias et devez utiliser le nom de colonne réelle. Par conséquent, modifiez le `SupplierName` BoundField s `SortExpression` à partir du nom de fournisseur CompanyName (voir la Figure 9). Comme le montre la Figure 10, après cette modification, que les résultats peuvent être triés par le fournisseur.


![Modifier la SortExpression s intitulée BoundField CompanyName](sorting-custom-paged-data-cs/_static/image13.png)

**Figure 9**: modifier la SortExpression s intitulée BoundField CompanyName


[![Les résultats peuvent maintenant être triés par fournisseur](sorting-custom-paged-data-cs/_static/image15.png)](sorting-custom-paged-data-cs/_static/image14.png)

**La figure 10**: les résultats peuvent maintenant être triés par fournisseur ([cliquez pour afficher l’image en taille réelle](sorting-custom-paged-data-cs/_static/image16.png))


## <a name="summary"></a>Résumé

L’implémentation de la pagination personnalisée, que nous avons examiné dans le didacticiel précédent requis que l’ordre selon lequel les résultats ont été triées être spécifiées au moment du design. En résumé, cela signifie que l’implémentation de la pagination personnalisée, que nous avons implémenté pas pu, en même temps, fournir des fonctionnalités de tri. Dans ce didacticiel nous ont surmonté cette limitation en étendant la procédure stockée à partir de la première à inclure un `@sortExpression` paramètre d’entrée par laquelle les résultats peuvent être triées.

Après la création de cette procédure stockée et création de nouvelles méthodes dans la couche DAL et la couche BLL, nous ont été en mesure d’implémenter un GridView qui proposés à la fois le tri et personnalisée en configurant l’ObjectDataSource à passer dans le GridView/s en cours d’échange `SortExpression` propriété à la couche BLL `SelectMethod`.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Carlos Santos. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](efficiently-paging-through-large-amounts-of-data-cs.md)
[Suivant](creating-a-customized-sorting-user-interface-cs.md)
