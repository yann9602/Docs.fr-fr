---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/adding-additional-datatable-columns-vb
title: "Ajout de colonnes de table de données supplémentaires (VB) | Documents Microsoft"
author: rick-anderson
description: "Lorsque vous utilisez l’Assistant TableAdapter pour créer un DataSet typé, le DataTable contient les colonnes retournées par la requête de base de données principale. Mais il n’y a..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/18/2007
ms.topic: article
ms.assetid: 1e8e65f9-fe3e-4250-810b-c90227786bed
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/adding-additional-datatable-columns-vb
msc.type: authoredcontent
ms.openlocfilehash: d357ca7bfe364090ff2c8504b2116e0d99d004bc
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-additional-datatable-columns-vb"></a>Ajouter des colonnes supplémentaires DataTable (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_70_VB.zip) ou [télécharger le PDF](adding-additional-datatable-columns-vb/_static/datatutorial70vb1.pdf)

> Lorsque vous utilisez l’Assistant TableAdapter pour créer un DataSet typé, le DataTable contient les colonnes retournées par la requête de base de données principale. Mais il existe des occasions lorsqu’un DataTable doit inclure des colonnes supplémentaires. Dans ce didacticiel, nous découvrir pourquoi les procédures stockées sont recommandées si nous avons besoin des colonnes de table de données supplémentaires.


## <a name="introduction"></a>Introduction

Lorsque vous ajoutez un TableAdapter à un DataSet typé, le schéma s DataTable correspondant est déterminé par la requête principale de TableAdapter s. Par exemple, si la requête principale renvoie les champs de données *A*, *B*, et *C*, la table de données aura trois colonnes correspondants nommés *A*, *B*, et *C*. En plus de la requête principale, un TableAdapter peut inclure des requêtes supplémentaires qui retournent, par exemple, un sous-ensemble des données en fonction de certains paramètres. Par exemple, en plus de la `ProductsTableAdapter` requête principale s, qui retourne des informations sur tous les produits, elle contient également des méthodes comme `GetProductsByCategoryID(categoryID)` et `GetProductByProductID(productID)`, qui retournent des informations de produit spécifique en fonction d’un paramètre fourni.

Le modèle d’avoir le schéma s DataTable refléter la requête principale de TableAdapter s fonctionne bien si toutes les méthodes TableAdapter s retournent la même ou à moins de champs de données que celles spécifiées dans la requête principale. Si une méthode du TableAdapter doit retourner les champs de données supplémentaires, nous devons développer le schéma s DataTable en conséquence. Dans le [maître/détail à l’aide d’une liste à puces des enregistrements principaux avec détails DataList](../filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb.md) didacticiel, nous avons ajouté une méthode à la `CategoriesTableAdapter` qui a retourné le `CategoryID`, `CategoryName`, et `Description` définies dans les champs de données la requête principale plue `NumberOfProducts`, un champ de données supplémentaires qui a signalé le nombre de produits associés à chaque catégorie. Nous avons ajouté manuellement une nouvelle colonne à la `CategoriesDataTable` afin de capturer le `NumberOfProducts` valeur à partir de cette nouvelle méthode de champ de données.

Comme indiqué dans le [téléchargement de fichiers](../working-with-binary-files/uploading-files-vb.md) didacticiel, vous devez veiller avec des TableAdapters qui utilisent des instructions SQL ad hoc et ont des méthodes dont les champs de données ne correspondent pas précisément la requête principale. Si l’Assistant Configuration de TableAdapter est relancée, elle sera mise à jour toutes les méthodes TableAdapter s afin que leur liste de champs de données correspond à la requête principale. Par conséquent, toutes les méthodes avec des listes de colonnes personnalisées seront revenir à la liste de colonnes de la requête principale s et ne retourne pas les données attendues. Ce problème ne se pose pas lors de l’utilisation de procédures stockées.

Dans ce didacticiel, nous allons examiner comment étendre un schéma de s DataTable pour inclure des colonnes supplémentaires. En raison de la fragilité du TableAdapter lors de l’utilisation des instructions SQL ad hoc, dans ce didacticiel, nous allons utiliser des procédures stockées. Reportez-vous à la [création de nouvelles procédures stockées s DataSet typé TableAdapters](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) et [à l’aide des procédures stockées existantes pour s DataSet typé TableAdapters](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) didacticiels pour plus d’informations sur configuration d’un TableAdapter pour utiliser des procédures stockées.

## <a name="step-1-adding-apricequartilecolumn-to-theproductsdatatable"></a>Étape 1 : Ajout d’un`PriceQuartile`colonne à la`ProductsDataTable`

Dans le *création de nouvelles procédures stockées s DataSet typé TableAdapters* didacticiel, nous avons créé un DataSet typé nommé `NorthwindWithSprocs`. Ce jeu de données contient deux tables de données : `ProductsDataTable` et `EmployeesDataTable`. Le `ProductsTableAdapter` a trois méthodes suivantes :

- `GetProducts`-la requête principale, qui retourne tous les enregistrements à partir de la `Products` table
- `GetProductsByCategoryID(categoryID)`-Retourne tous les produits avec les *categoryID*.
- `GetProductByProductID(productID)`-Retourne le produit avec l’objet *productID*.

La requête principale et toutes les deux méthodes supplémentaires retournent le même ensemble de champs de données, à savoir toutes les colonnes à partir de la `Products` table. Il n’y aucun sous-requêtes en corrélation ou `JOIN` s extrayant des données connexes à partir de la `Categories` ou `Suppliers` tables. Par conséquent, le `ProductsDataTable` a une colonne correspondante pour chaque champ dans le `Products` table.

Pour ce didacticiel, s permettent d’ajouter une méthode à la `ProductsTableAdapter` nommé `GetProductsWithPriceQuartile` qui retourne tous les produits. En plus des champs de données de produit standard, `GetProductsWithPriceQuartile` inclut également un `PriceQuartile` champ de données qui indique dans quel quartile se situe le prix du produit s. Par exemple, les produits dont les prix sont dans les plus coûteuses 25 % aura un `PriceQuartile` la valeur 1, tandis que ceux dont le prix est comprise dans la partie inférieure de 25 % aura une valeur de 4. Avant que nous vous inquiétez pas sur la création de la procédure stockée pour retourner ces informations, toutefois, nous devons d’abord mettre à jour le `ProductsDataTable` pour inclure une colonne contenant le `PriceQuartile` résultats lorsque la `GetProductsWithPriceQuartile` méthode est utilisée.

Ouvrez le `NorthwindWithSprocs` jeu de données et avec le bouton droit sur le `ProductsDataTable`. Choisissez Ajouter dans le menu contextuel et choisissez la colonne.


[![Ajouter une nouvelle colonne à la ProductsDataTable](adding-additional-datatable-columns-vb/_static/image2.png)](adding-additional-datatable-columns-vb/_static/image1.png)

**Figure 1**: ajouter une nouvelle colonne à la `ProductsDataTable` ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image3.png))


Cela ajoutera une nouvelle colonne dans le DataTable nommé Column1 de type `System.String`. Nous devons mettre à jour ce nom de colonne s à PriceQuartile et son type `System.Int32` , car il sera utilisé pour contenir un nombre compris entre 1 et 4. Sélectionnez la colonne nouvellement ajoutée dans le `ProductsDataTable` et, à partir de la fenêtre Propriétés, définissez la `Name` propriété PriceQuartile et la `DataType` propriété `System.Int32`.


[![Définir les propriétés de type de données et le nouveau nom de colonne s](adding-additional-datatable-columns-vb/_static/image5.png)](adding-additional-datatable-columns-vb/_static/image4.png)

**Figure 2**: définir la nouvelle colonne s `Name` et `DataType` propriétés ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image6.png))


Comme le montre la Figure 2, il n’y a des propriétés supplémentaires qui peuvent être définies, comme si les valeurs dans la colonne doivent être uniques, si la colonne est une colonne à incrémentation automatique, ou non de la base de données `NULL` valeurs sont autorisées et ainsi de suite. Laissez ces valeurs définies à leurs valeurs par défaut.

## <a name="step-2-creating-thegetproductswithpricequartilemethod"></a>Étape 2 : Création du`GetProductsWithPriceQuartile`(méthode)

Maintenant que le `ProductsDataTable` a été mis à jour pour inclure la `PriceQuartile` colonne, vous êtes prêt à créer le `GetProductsWithPriceQuartile` (méthode). Démarrez en cliquant sur le TableAdapter et en choisissant Ajouter une requête dans le menu contextuel. L’Assistant Configuration de requête TableAdapter, qui demande tout d’abord nous si nous souhaitons utiliser des instructions SQL ad hoc ou une procédure stockée nouveau ou existante s’affiche. Étant donné que nous ne pas mais une procédure stockée qui retourne les données de quartile prix, permettent de s autoriser le TableAdapter créer cette procédure stockée pour nous. Sélectionnez l’option de procédure stockée nouveau de créer et cliquez sur Suivant.


[![Demandez à l’Assistant TableAdapter pour créer la procédure stockée pour nous](adding-additional-datatable-columns-vb/_static/image8.png)](adding-additional-datatable-columns-vb/_static/image7.png)

**Figure 3**: demander à l’Assistant TableAdapter pour créer le stockées procédure pour nous ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image9.png))


Dans l’écran suivant, illustré dans la Figure 4, l’Assistant nous demande de quel type de requête à ajouter. Étant donné que la `GetProductsWithPriceQuartile` méthode retournera toutes les colonnes et les enregistrements de la `Products` de table, sélectionnez l’instruction SELECT qui retourne des lignes, puis cliquez sur Suivant.


[![La requête est une instruction SELECT qui retourne plusieurs lignes](adding-additional-datatable-columns-vb/_static/image11.png)](adding-additional-datatable-columns-vb/_static/image10.png)

**Figure 4**: requête notre sera un `SELECT` instruction qui retourne plusieurs lignes ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image12.png))


Ensuite, nous sommes invités pour le `SELECT` requête. Dans l’Assistant, entrez la requête suivante :


[!code-sql[Main](adding-additional-datatable-columns-vb/samples/sample1.sql)]

La requête ci-dessus utilise SQL Server 2005 s nouveau [ `NTILE` fonction](https://msdn.microsoft.com/library/ms175126.aspx) pour diviser les résultats en quatre groupes où les groupes sont déterminées par la `UnitPrice` valeurs triées dans l’ordre décroissant.

Malheureusement, le Générateur de requêtes ne sait pas comment analyser le `OVER` (mot clé) et affiche une erreur lors de l’analyse de la requête ci-dessus. Par conséquent, entrez la requête ci-dessus directement dans la zone de texte dans l’Assistant sans utiliser le Générateur de requêtes.

> [!NOTE]
> Pour plus d’informations sur SQL Server 2005 et de NTILE autres fonctions de classement, consultez [retournant les résultats classés avec Microsoft SQL Server 2005](http://www.4guysfromrolla.com/webtech/010406-1.shtml) et [section relative aux fonctions de classement](https://msdn.microsoft.com/library/ms189798.aspx) à partir de la [SQL Documentation en ligne de Server 2005](https://msdn.microsoft.com/library/ms189798.aspx).


Après avoir entré la `SELECT` requête et cliquez sur Suivant, l’Assistant nous invite à fournir un nom pour la procédure stockée est créé. Nommez la nouvelle procédure stockée `Products_SelectWithPriceQuartile` et cliquez sur Suivant.


[![Nom de la procédure stockée Products_SelectWithPriceQuartile](adding-additional-datatable-columns-vb/_static/image14.png)](adding-additional-datatable-columns-vb/_static/image13.png)

**Figure 5**: nom de la procédure stockée `Products_SelectWithPriceQuartile` ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image15.png))


Enfin, nous sommes invités pour nommer les méthodes TableAdapter. Laissez les deux le remplissage un DataTable et retourner un nom et cases à cocher DataTable vérifié les méthodes `FillWithPriceQuartile` et `GetProductsWithPriceQuartile`.


[![Nom du TableAdapter s méthodes et cliquez sur Terminer](adding-additional-datatable-columns-vb/_static/image17.png)](adding-additional-datatable-columns-vb/_static/image16.png)

**Figure 6**: nommer les méthodes du TableAdapter que s et cliquez sur Terminer ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image18.png))


Avec la `SELECT` requête spécifiée et la procédure stockée et les méthodes TableAdapter nommés, cliquez sur Terminer pour terminer l’Assistant. À ce stade, vous pouvez obtenir un avertissement ou deux à partir de l’Assistant, indiquant que le `OVER` SQL construction ou l’instruction n’est pas pris en charge. Vous pouvez ignorer ces avertissements.

Une fois l’Assistant terminé, le TableAdapter doit inclure le `FillWithPriceQuartile` et `GetProductsWithPriceQuartile` méthodes et la base de données doivent inclure une procédure stockée nommée `Products_SelectWithPriceQuartile`. Prenez un moment pour vérifier que le TableAdapter ne contient en effet de cette nouvelle méthode et que la procédure stockée a été correctement ajoutée à la base de données. Lors de la vérification de la base de données, si vous ne voyez pas la procédure stockée try clic droit sur le dossier Stored Procedures et en choisissant l’actualisation.


![Vérifiez qu’une nouvelle méthode a été ajoutée au TableAdapter](adding-additional-datatable-columns-vb/_static/image19.png)

**Figure 7**: Vérifiez qu’une nouvelle méthode a été ajoutée au TableAdapter


[![Assurez-vous que la base de données contienne le Products_SelectWithPriceQuartile procédure stockée](adding-additional-datatable-columns-vb/_static/image21.png)](adding-additional-datatable-columns-vb/_static/image20.png)

**Figure 8**: Vérifiez que la base de données contient le `Products_SelectWithPriceQuartile` la procédure stockée ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image22.png))


> [!NOTE]
> Un des avantages de l’utilisation de procédures stockées au lieu d’instructions SQL ad hoc est que l’exécuter à nouveau l’Assistant Configuration de TableAdapter ne modifie pas les listes de colonnes de procédures stockées. Le vérifier en cliquant sur le TableAdapter, en choisissant l’option de configuration dans le menu contextuel pour démarrer l’Assistant, puis en cliquant sur Terminer pour terminer. Ensuite, accédez à la base de données et la vue du `Products_SelectWithPriceQuartile` procédure stockée. Notez que sa liste de colonnes n’a pas été modifié. Avions nous été à l’aide des instructions SQL ad hoc, réexécuter l’Assistant Configuration de TableAdapter aurait été rétablies cette liste de colonnes de la requête s pour correspondre à la liste des colonnes requête principale, en supprimant l’instruction NTILE à partir de la requête utilisée par le `GetProductsWithPriceQuartile` (méthode).


Lorsque le s de la couche d’accès aux données `GetProductsWithPriceQuartile` méthode est appelée, le TableAdapter exécute le `Products_SelectWithPriceQuartile` procédure stockée et ajoute une ligne à la `ProductsDataTable` pour chaque enregistrement retourné. Les champs de données retournés par la procédure stockée sont mappées à la `ProductsDataTable` des colonnes de s. Étant donné un `PriceQuartile` champ des données retournées à partir de la procédure stockée, sa valeur est assignée à la `ProductsDataTable` s `PriceQuartile` colonne.

Pour les méthodes TableAdapter dont les requêtes ne retournent pas un `PriceQuartile` champ de données, le `PriceQuartile` valeur de la colonne s est la valeur spécifiée par son `DefaultValue` propriété. Comme le montre la Figure 2, cette valeur est définie sur `DBNull`, la valeur par défaut. Si vous préférez une valeur par défaut différente, définissez simplement le `DefaultValue` propriété en conséquence. Assurez-vous simplement que le `DefaultValue` la valeur est valide selon la colonne s `DataType` (c'est-à-dire, `System.Int32` pour la `PriceQuartile` colonne).

À ce stade, nous avons effectué les étapes nécessaires pour l’ajout d’une colonne supplémentaire à un DataTable. Pour vérifier que cette colonne supplémentaire fonctionne comme prévu, permettent de créer une page ASP.NET qui affiche chaque nom de produit, prix et quartile de prix s. Avant cela, cependant, vous devez d’abord mettre à jour de la couche de logique métier pour inclure une méthode qui appelle vers le bas de la couche DAL s `GetProductsWithPriceQuartile` (méthode). Nous sera mise à jour de la couche BLL ensuite, à l’étape 3, puis créez la page ASP.NET à l’étape 4.

## <a name="step-3-augmenting-the-business-logic-layer"></a>Étape 3 : Augmentation de la couche de logique métier

Avant de pouvoir utiliser la nouvelle `GetProductsWithPriceQuartile` méthode à partir de la couche de présentation, nous devons d’abord ajouter une méthode correspondante à la couche BLL. Ouvrez le `ProductsBLLWithSprocs` fichier de classe et ajoutez le code suivant :


[!code-vb[Main](adding-additional-datatable-columns-vb/samples/sample2.vb)]

Comme les autres méthodes de récupération de données de `ProductsBLLWithSprocs`, le `GetProductsWithPriceQuartile` méthode appelle simplement la couche DAL s correspondant `GetProductsWithPriceQuartile` méthode et retourne ses résultats.

## <a name="step-4-displaying-the-price-quartile-information-in-an-aspnet-web-page"></a>Étape 4 : Afficher les informations sur les prix Quartile dans une Page Web ASP.NET

Avec l’ajout de la couche BLL terminer nous re prêt à créer une page ASP.NET qui montre le quartile prix pour chaque produit. Ouvrir le `AddingColumns.aspx` page dans le `AdvancedDAL` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, en définissant son `ID` propriété `Products`. À partir de la balise active de s GridView, lier à un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource à utiliser le `ProductsBLLWithSprocs` classe s `GetProductsWithPriceQuartile` (méthode). Dans la mesure où il s’agit d’une grille en lecture seule, définissez les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None).


[![Configurer pour utiliser la classe ProductsBLLWithSprocs ObjectDataSource](adding-additional-datatable-columns-vb/_static/image24.png)](adding-additional-datatable-columns-vb/_static/image23.png)

**Figure 9**: configurer l’ObjectDataSource à utiliser le `ProductsBLLWithSprocs` classe ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image25.png))


[![Récupérer des informations sur les produits à partir de la méthode GetProductsWithPriceQuartile](adding-additional-datatable-columns-vb/_static/image27.png)](adding-additional-datatable-columns-vb/_static/image26.png)

**La figure 10**: récupérer des informations sur le produit à partir de la `GetProductsWithPriceQuartile` (méthode) ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image28.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio ajoute automatiquement un BoundField ou le CheckBoxField au GridView pour chacun des champs de données retournés par la méthode. Un de ces champs de données est `PriceQuartile`, qui est la colonne que nous avons ajouté à la `ProductsDataTable` à l’étape 1.

Modifier les champs de s GridView, suppression de tout sauf la `ProductName`, `UnitPrice`, et `PriceQuartile` BoundFields. Configurer le `UnitPrice` BoundField à mettre en forme sa valeur sous forme de devise et avoir le `UnitPrice` et `PriceQuartile` BoundFields et center-aligné à droite, respectivement. Enfin, mettre à jour les autres BoundFields `HeaderText` propriétés de produits, les prix et les prix Quartile, respectivement. Vérifiez également la case à cocher Activer le tri de la balise active de GridView s.

Après ces modifications, le balisage déclaratif s GridView et ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](adding-additional-datatable-columns-vb/samples/sample3.aspx)]

La figure 11 illustre cette page lors de la visite via un navigateur. Notez que, au départ, les produits sont classés par leur prix dans l’ordre décroissant à chaque produit attribué approprié `PriceQuartile` valeur. Bien entendu ces données peuvent être triées par d’autres critères, avec la valeur de colonne prix Quartile toujours reflétant le classement du produit s en ce qui concerne les prix (voir Figure 12).


[![Les produits sont classés par leurs prix](adding-additional-datatable-columns-vb/_static/image30.png)](adding-additional-datatable-columns-vb/_static/image29.png)

**Figure 11**: les produits sont classés par leurs prix ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image31.png))


[![Les produits sont classés par nom](adding-additional-datatable-columns-vb/_static/image33.png)](adding-additional-datatable-columns-vb/_static/image32.png)

**Figure 12**: les produits sont classés par nom ([cliquez pour afficher l’image en taille réelle](adding-additional-datatable-columns-vb/_static/image34.png))


> [!NOTE]
> Avec quelques lignes de code nous pourrions augmenter le contrôle GridView afin qu’il colorées sur les lignes de produits en fonction de leur `PriceQuartile` valeur. Nous pouvons les produits dans le premier quartile vert, celles figurant dans le deuxième quartile un jaune clair, de couleur et ainsi de suite. Je vous encourage à prendre un moment pour ajouter cette fonctionnalité. Si vous avez besoin de mémoire sur la mise en forme un GridView, consultez le [personnalisé de mise en forme à données](../custom-formatting/custom-formatting-based-upon-data-vb.md) didacticiel.


## <a name="an-alternative-approach---creating-another-tableadapter"></a>Une autre approche - création d’un autre TableAdapter

Comme nous l’avons vu dans ce didacticiel, lors de l’ajout d’une méthode à un TableAdapter qui renvoie les champs de données autres que ceux décrits par la requête principale, nous pouvons ajouter des colonnes correspondantes dans le DataTable. Une telle approche, toutefois, fonctionne bien uniquement s’il existe un petit nombre de méthodes du TableAdapter qui retournent des champs de données différents et si ces champs de données de remplacement ne varient pas trop de la requête principale.

Au lieu d’ajouter des colonnes à la table de données, vous pouvez ajouter un autre TableAdapter au jeu de données qui contient les méthodes à partir de la première TableAdapter qui retournent des champs de données différents. Pour ce didacticiel, plutôt que d’ajouter le `PriceQuartile` colonne à la `ProductsDataTable` (où il est utilisé uniquement par le `GetProductsWithPriceQuartile` (méthode)), nous avons ajouté un TableAdapter supplémentaire pour le DataSet nommé `ProductsWithPriceQuartileTableAdapter` servant le `Products_SelectWithPriceQuartile` stockées procédure en tant que la requête principale. Utilisent les pages ASP.NET que nécessaire pour obtenir des informations de produit avec le quartile prix le `ProductsWithPriceQuartileTableAdapter`, tandis que ceux qui n’ont pas pu continuer à utiliser le `ProductsTableAdapter`.

En ajoutant un nouveau TableAdapter, les tables de données restent untarnished et leurs colonnes reflètent précisément les champs de données retournés par leurs méthodes du TableAdapter que s. Toutefois, les TableAdapters supplémentaires peuvent introduire des fonctionnalités et les tâches répétitives. Par exemple, si les pages ASP.NET qui affiche le `PriceQuartile` colonne également nécessaire pour fournir insert, update et delete prise en charge, le `ProductsWithPriceQuartileTableAdapter` doit avoir son `InsertCommand`, `UpdateCommand`, et `DeleteCommand` propriétés correctement configuré. Lors de la mise en miroir de ces propriétés sont le `ProductsTableAdapter` s, cette configuration présente une étape supplémentaire. En outre, il existe maintenant deux façons de mettre à jour, supprimer ou ajouter un produit à la base de données - via la `ProductsTableAdapter` et `ProductsWithPriceQuartileTableAdapter` classes.

Le téléchargement de ce didacticiel inclut un `ProductsWithPriceQuartileTableAdapter` classe dans le `NorthwindWithSprocs` DataSet qui illustre cette approche alternative.

## <a name="summary"></a>Récapitulatif

Dans la plupart des scénarios, toutes les méthodes dans un TableAdapter retournera le même ensemble de champs de données, mais il existe certains cas, une méthode particulière ou les deux peut-être retourner un champ supplémentaire. Par exemple, dans le [maître/détail à l’aide d’une liste à puces des enregistrements principaux avec DataList détails](../filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb.md) didacticiel, nous avons ajouté une méthode à la `CategoriesTableAdapter` qui, en plus des champs de données de la requête principale s, retournés un `NumberOfProducts` champ a signalé le nombre de produits associés à chaque catégorie. Dans ce didacticiel, nous avons étudié Ajout d’une méthode dans le `ProductsTableAdapter` qui a retourné un `PriceQuartile` champ en plus des champs de données de la requête principale s. Pour capturer des données supplémentaires champs retournés par les méthodes du TableAdapter s que nous devons ajouter des colonnes correspondantes dans le DataTable.

Si vous prévoyez d’ajouter manuellement des colonnes dans le DataTable, il est recommandé que le TableAdapter utiliser des procédures stockées. Si le TableAdapter utilise des instructions SQL ad hoc, n’importe quel moment de l’Assistant Configuration de TableAdapter est exécuté toutes les méthodes rétablir des listes de champs de données aux champs de données retournés par la requête principale. Ce problème n’étend pas à des procédures stockées, c’est pourquoi ils sont recommandés et ont été utilisés dans ce didacticiel.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Randy Schmidt, Jeremy Goor, Bernadette Leigh et Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](updating-the-tableadapter-to-use-joins-vb.md)
[Suivant](working-with-computed-columns-vb.md)
