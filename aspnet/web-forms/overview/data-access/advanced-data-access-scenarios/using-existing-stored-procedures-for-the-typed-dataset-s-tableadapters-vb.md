---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb
title: "À l’aide d’existante des procédures stockées pour TableAdapters le groupe de données typé (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans le didacticiel précédent, nous avons appris à utiliser l’Assistant TableAdapter pour générer des procédures stockées. Dans ce didacticiel, nous apprendre comment le TableAdapter même..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/18/2007
ms.topic: article
ms.assetid: 2da25f6a-757e-4e7b-a812-1575288d8f7a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb
msc.type: authoredcontent
ms.openlocfilehash: 5d1be6c30cda5a06087516210a77f48b6a3fe45b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-existing-stored-procedures-for-the-typed-datasets-tableadapters-vb"></a>À l’aide d’existante des procédures stockées pour TableAdapters le groupe de données typé (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_68_VB.zip) ou [télécharger le PDF](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/datatutorial68vb1.pdf)

> Dans le didacticiel précédent, nous avons appris à utiliser l’Assistant TableAdapter pour générer des procédures stockées. Dans ce didacticiel, nous apprendre comment l’Assistant TableAdapter même peut fonctionner avec des procédures stockées existantes. Nous avons également apprendre à ajouter manuellement les nouvelles procédures stockées à notre base de données.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) nous avons vu comment les s DataSet typé TableAdapters peut être configuré afin d’utiliser des procédures stockées accès plutôt que de données ad hoc des instructions SQL. En particulier, nous avons examiné comment l’Assistant TableAdapter créer automatiquement ces procédures stockées. Quand vous portez une application héritée vers ASP.NET 2.0 ou lors de la création d’un site Web d’ASP.NET 2.0 autour d’un modèle de données existant, sans doute que la base de données contient déjà les procédures stockées que nous avons besoin. Vous pouvez également créer vos procédures stockées manuellement ou par le biais d’un outil autre que l’Assistant TableAdapter qui génère automatiquement les procédures stockées.

Dans ce didacticiel, nous allons examiner comment configurer le TableAdapter pour utiliser des procédures stockées existantes. Étant donné que la base de données Northwind comporte seulement un petit ensemble de procédures stockées intégrées, nous examinerons également les étapes nécessaires pour ajouter manuellement les nouvelles procédures stockées dans la base de données via l’environnement Visual Studio. Let s commencer !

> [!NOTE]
> Dans le [encapsulant les Modifications de base de données d’une Transaction](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) didacticiel, nous avons ajouté des méthodes au TableAdapter pour prendre en charge des transactions (`BeginTransaction`, `CommitTransaction`, et ainsi de suite). Vous pouvez également les transactions peuvent être gérées entièrement dans une procédure stockée, qui ne requiert aucune modification pour le code de la couche d’accès aux données. Dans ce didacticiel, nous allons explorer les commandes T-SQL utilisées pour exécuter une procédure stockée s les instructions dans l’étendue d’une transaction.


## <a name="step-1-adding-stored-procedures-to-the-northwind-database"></a>Étape 1 : Ajouter des procédures stockées à la base de données Northwind

Visual Studio facilite l’ajout de nouvelles procédures stockées pour une base de données. S permettent d’ajouter une nouvelle procédure stockée à la base de données Northwind qui retourne toutes les colonnes à partir de la `Products` table pour celles qui ont un particulier `CategoryID` valeur. Dans la fenêtre Explorateur de serveurs, développez la base de données Northwind afin que ses dossiers - les schémas de base de données, Tables, vues et ainsi de suite - sont affichés. Comme nous l’avons vu dans le didacticiel précédent, le dossier Stored Procedures contient les base de données s des procédures stockées existantes. Pour ajouter une nouvelle procédure stockée, cliquez simplement avec le bouton droit le dossier Stored Procedures et choisissez l’option Ajouter une nouvelle procédure stockée dans le menu contextuel.


[![Cliquez sur le dossier de procédures stockées et ajouter une nouvelle procédure stockée](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image2.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image1.png)

**Figure 1**: cliquez sur le dossier de procédures stockées et ajouter une nouvelle procédure stockée ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image3.png))


Comme le montre la Figure 1, en sélectionnant l’option Ajouter une nouvelle procédure stockée ouvre une fenêtre de script dans Visual Studio avec le contour du script SQL nécessaire pour créer la procédure stockée. Notre travail consiste à développer ce script et exécutez-le, auquel cas, la procédure stockée sera ajoutée à la base de données.

Entrez le script suivant :


[!code-sql[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample1.sql)]

Ce script, lors de l’exécution, ajoutera une nouvelle procédure stockée à la base de données Northwind nommé `Products_SelectByCategoryID`. Cette procédure stockée accepte un paramètre d’entrée unique (`@CategoryID`, de type `int`) et elle retourne tous les champs de ces produits avec une mise en correspondance `CategoryID` valeur.

Pour exécuter cette `CREATE PROCEDURE` de script et ajouter de la procédure stockée à la base de données, cliquez sur l’icône Enregistrer dans la barre d’outils ou appuyez sur Ctrl + S. Après cela, les actualisations de dossier de procédures stockées, montrant nouvellement créé la procédure stockée. En outre, le script dans la fenêtre modifiera plusieurs à partir de `CREATE PROCEDURE dbo.Products_SelectProductByCategoryID` à `ALTER PROCEDURE` `dbo.Products_SelectProductByCategoryID`. `CREATE PROCEDURE`Ajoute une nouvelle procédure stockée à la base de données, tandis que `ALTER PROCEDURE` met à jour un existant. Étant donné que le début du script a changé `ALTER PROCEDURE`, modifier les procédures stockées d’entrée paramètres ou des instructions SQL et en cliquant sur l’icône Enregistrer met à jour la procédure stockée avec ces modifications.

La figure 2 illustre Visual Studio après le `Products_SelectByCategoryID` procédure stockée a été enregistrée.


[![La procédure stockée Products_SelectByCategoryID a été ajouté à la base de données](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image5.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image4.png)

**Figure 2**: la procédure stockée `Products_SelectByCategoryID` a été ajouté à la base de données ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image6.png))


## <a name="step-2-configuring-the-tableadapter-to-use-an-existing-stored-procedure"></a>Étape 2 : Configuration du TableAdapter pour utiliser une procédure stockée existante

Maintenant que le `Products_SelectByCategoryID` procédure stockée a été ajouté à la base de données, nous pouvons configurer notre couche d’accès aux données pour utiliser cette procédure stockée lorsqu’une de ses méthodes est appelée. En particulier, nous allons ajouter un `GetProducstByCategoryID(<_i22_>categoryID)<!--_i22_-->` méthode à la `ProductsTableAdapter` dans les `NorthwindWithSprocs` DataSet typé qui appelle le `Products_SelectByCategoryID` nous venons de créer de procédure stockée.

Commencez par ouvrir le `NorthwindWithSprocs` jeu de données. Avec le bouton droit sur le `ProductsTableAdapter` et choisissez Ajouter une requête pour lancer l’Assistant Configuration de requête TableAdapter. Dans le [didacticiel précédent](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) nous avons choisi le TableAdapter pour créer une nouvelle procédure stockée pour nous. Pour ce didacticiel, toutefois, nous souhaitons relier la nouvelle méthode TableAdapter à l’objet existant `Products_SelectByCategoryID` procédure stockée. Par conséquent, choisissez l’option de procédure stockée existante utiliser à partir de l’Assistant s première étape et puis cliquez sur Suivant.


[![Choisissez l’utilisation existant de la procédure stockée Option](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image8.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image7.png)

**Figure 3**: choisissez utilisation existants procédure Option ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image9.png))


L’écran suivant fournit qu'une liste déroulante remplie avec la base de données s, les procédures stockées. Sélection d’une procédure stockée répertorie ses paramètres d’entrée à gauche et les champs de données retournés (le cas échéant) à droite. Choisissez le `Products_SelectByCategoryID` procédure stockée à partir de la liste et cliquez sur Suivant.


[![Choisir le Products_SelectByCategoryID procédure stockée](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image11.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image10.png)

**Figure 4**: choisir le `Products_SelectByCategoryID` la procédure stockée ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image12.png))


L’écran suivant nous demande de type de données retourné par la procédure stockée et notre réponse ici détermine le type retourné par la méthode s TableAdapter. Par exemple, si nous indiquent que les données tabulaires sont retournées, la méthode retourne un `ProductsDataTable` instance remplie avec les enregistrements retournés par la procédure stockée. En revanche, si nous indiquent que cette procédure stockée renvoie une valeur unique TableAdapter retournera un `Object` qui est assignée à la valeur de la première colonne du premier enregistrement retourné par la procédure stockée.

Étant donné que le `Products_SelectByCategoryID` procédure stockée retourne tous les produits qui appartiennent à une catégorie particulière, choisissez la première réponse - données tabulaires - et cliquez sur Suivant.


[![Indiquer que la procédure stockée renvoie des données tabulaires](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image14.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image13.png)

**Figure 5**: indiquer que la procédure stockée renvoie des données tabulaires ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image15.png))


Il ne reste que pour indiquer que les modèles de méthode à utiliser suivie des noms de ces méthodes. Laissez les deux le remplissage un DataTable et retourner un DataTable des options vérifiée. Cependant, renommez les méthodes à `FillByCategoryID` et `GetProductsByCategoryID`. Puis cliquez sur Suivant pour consulter un résumé des tâches que l’Assistant va effectuer. Si tout semble correct, cliquez sur Terminer.


[![Nom de la FillByCategoryID de méthodes et GetProductsByCategoryID](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image17.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image16.png)

**Figure 6**: nommez les méthodes `FillByCategoryID` et `GetProductsByCategoryID` ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image18.png))


> [!NOTE]
> Les méthodes TableAdapter que nous venons de créer, `FillByCategoryID` et `GetProductsByCategoryID`, un paramètre d’entrée de type attendu `Integer`. Cette valeur de paramètre d’entrée est passée dans la procédure stockée via son `@CategoryID` paramètre. Si vous modifiez le `Products_SelectByCategory` stockées des paramètres de procédure s, vous devez également mettre à jour les paramètres de ces méthodes TableAdapter. Comme indiqué dans le [didacticiel précédent](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md), cela est possible de deux manières : en manuellement ajoutant ou en supprimant des paramètres à partir de la collection de paramètres ou en réexécutant l’Assistant TableAdapter.


## <a name="step-3-adding-agetproductsbycategoryidcategoryidmethod-to-the-bll"></a>Étape 3 : Ajout d’un`GetProductsByCategoryID(categoryID)`à la couche BLL (méthode)

Avec la `GetProductsByCategoryID` DAL terminée, l’étape suivante consiste à fournir l’accès à cette méthode dans la couche de logique métier. Ouvrez le `ProductsBLLWithSprocs` fichier de classe et ajoutez la méthode suivante :


[!code-vb[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample2.vb)]

Cette méthode de la couche BLL retourne simplement le `ProductsDataTable` retourné à partir de la `ProductsTableAdapter` s `GetProductsByCategoryID` (méthode). Le `DataObjectMethodAttribute` attribut fournit des métadonnées utilisée par l’Assistant Configurer la Source de données de s ObjectDataSource. En particulier, cette méthode s’affiche dans la liste déroulante de s onglet sélection.

## <a name="step-4-displaying-products-by-category"></a>Étape 4 : Affichage des produits par catégorie

Pour tester récemment ajouté `Products_SelectByCategoryID` procédure stockée et les méthodes de la couche DAL et la couche BLL correspondantes, s permettent de créer une page ASP.NET qui contient une liste déroulante et un GridView. DropDownList va répertorier toutes les catégories dans la base de données pendant que le contrôle GridView affiche les produits appartenant à la catégorie sélectionnée.

> [!NOTE]
> Nous les interfaces maître/détail ve créé à l’aide de la compréhension des listes dans les didacticiels précédents. Pour une plus approfondies sur l’implémentation d’un tel rapport maître/détail, reportez-vous à la [filtrage de maître/détail avec un DropDownList](../masterdetail/master-detail-filtering-with-a-dropdownlist-vb.md) didacticiel.


Ouvrez le `ExistingSprocs.aspx` page dans le `AdvancedDAL` faites glisser un contrôle DropDownList à partir de la boîte à outils vers le concepteur. Définir le s DropDownList `ID` propriété `Categories` et son `AutoPostBack` propriété `True`. Ensuite, à partir de sa balise active, lier à un nouveau ObjectDataSource nommé DropDownList `CategoriesDataSource`. Configurer l’ObjectDataSource afin qu’il récupère ses données à partir de la `CategoriesBLL` classe s `GetCategories` (méthode). Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None).


[![Récupérer des données à partir de la méthode GetCategories CategoriesBLL classe s](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image20.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image19.png)

**Figure 7**: récupérer les données à partir de la `CategoriesBLL` classe s `GetCategories` (méthode) ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image21.png))


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image23.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image22.png)

**Figure 8**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image24.png))


Après avoir effectué l’Assistant ObjectDataSource, configurez DropDownList pour afficher les `CategoryName` champ de données et d’utiliser le `CategoryID` champ en tant que le `Value` pour chaque `ListItem`.

À ce stade, le balisage déclaratif s DropDownList et ObjectDataSource doit similaire au suivant :


[!code-aspx[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample3.aspx)]

Ensuite, faites glisser un contrôle GridView dans le concepteur, placer sous DropDownList. Définir le contrôle GridView s `ID` à `ProductsByCategory` et, à partir de sa balise active, la lier à un nouveau ObjectDataSource nommé `ProductsByCategoryDataSource`. Configurer le `ProductsByCategoryDataSource` ObjectDataSource à utiliser le `ProductsBLLWithSprocs` (classe), en fait, il récupérer ses données à l’aide de la `GetProductsByCategoryID(categoryID)` (méthode). Étant donné que ce contrôle GridView ne doit pas être utilisé pour afficher des données, définir les listes déroulantes dans la mise à jour, insérer, supprimer des onglets à (None) et cliquez sur Suivant.


[![Configurer pour utiliser la classe ProductsBLLWithSprocs ObjectDataSource](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image26.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image25.png)

**Figure 9**: configurer l’ObjectDataSource à utiliser le `ProductsBLLWithSprocs` classe ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image27.png))


[![Récupérer des données à partir de la méthode GetProductsByCategoryID(categoryID)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image29.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image28.png)

**La figure 10**: récupérer les données à partir de la `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image30.png))


La méthode choisie dans l’onglet Sélection attend un paramètre, donc la dernière étape de l’Assistant nous invite pour le paramètre source de s. Définir le paramètre source déroulant et choisissez la `Categories` contrôle à partir de la liste déroulante ControlID. Cliquez sur Terminer pour terminer l’Assistant.


[![Utiliser des catégories DropDownList comme Source de paramètre categoryID](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image32.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image31.png)

**Figure 11**: utilisez le `Categories` DropDownList comme Source de la `categoryID` paramètre ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image33.png))


À la fin de l’Assistant ObjectDataSource, Visual Studio ajoute BoundFields et un CheckBoxField pour chacun des champs de données de produit. Vous pouvez personnaliser ces champs comme vous le souhaitez.

Visitez la page via un navigateur. Lors de la visite de la page de que la catégorie boissons est sélectionnée et les produits correspondants répertoriées dans la grille. La modification de la liste déroulante à une autre catégorie, comme Figure 12 montre, provoque une publication (postback) et recharge la grille avec les produits de la catégorie qui vient d’être sélectionnée.


[![Les produits dans la catégorie de produit sont affichés.](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image35.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image34.png)

**Figure 12**: les produits dans la catégorie de produit sont affichés ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image36.png))


## <a name="step-5-wrapping-a-stored-procedure-s-statements-within-the-scope-of-a-transaction"></a>Étape 5 : Encapsulant une procédure stockée s les instructions dans l’étendue d’une Transaction

Dans le [encapsulant les Modifications de base de données d’une Transaction](../working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb.md) didacticiel, nous avons parlé des techniques permettant d’effectuer une série d’instructions de modification de base de données dans l’étendue d’une transaction. Souvenez-vous que les modifications effectuées dans le cadre d’une transaction, soit toutes les réussissent ou échouent, garantir l’atomicité. Techniques d’utilisation des transactions sont les suivantes :

- L’utilisation des classes dans le `System.Transactions` espace de noms
- Avec la couche d’accès aux données d’utiliser les classes ADO.NET, comme `SqlTransaction`, et
- Ajouter les commandes de transaction T-SQL directement dans la procédure stockée

Le *encapsulant les Modifications de base de données d’une Transaction* didacticiel utilisé les classes ADO.NET dans la couche DAL. Le reste de ce didacticiel explique comment gérer une transaction à l’aide des commandes T-SQL à partir d’une procédure stockée.

Les trois commandes SQL clés pour un démarrage manuel, la validation et la restauration d’une transaction sont `BEGIN TRANSACTION`, `COMMIT TRANSACTION`, et `ROLLBACK TRANSACTION`, respectivement. Comme avec l’approche ADO.NET, lors de l’utilisation de transactions à partir d’une procédure stockée, il faut appliquer le modèle suivant :

1. Indiquer le début d’une transaction.
2. Exécutez les instructions SQL qui composent la transaction.
3. S’il existe une erreur dans l’une des instructions à l’étape 2, annuler la transaction.
4. Si toutes les instructions à l’étape 2 se termine sans erreur, validez la transaction.

Ce modèle peut être implémenté dans la syntaxe T-SQL en utilisant le modèle suivant :


[!code-sql[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample4.sql)]

Le modèle de démarrage en définissant un `TRY...CATCH` bloquer, une construction de nouveau vers SQL Server 2005. Comme avec `Try...Catch` bloque en Visual Basic, l’instruction SQL `TRY...CATCH` bloc exécute les instructions dans le `TRY` bloc. Si n’importe quelle instruction génère une erreur, le contrôle est immédiatement transféré vers le `CATCH` bloc.

Si aucun message d’erreur l’exécution des instructions SQL que la transaction, de la composition du `COMMIT TRANSACTION` instruction valide les modifications et termine la transaction. Si, toutefois, une des instructions entraîne une erreur, le `ROLLBACK TRANSACTION` dans le `CATCH` bloc retourne la base de données à son état avant le début de la transaction. La procédure stockée génère également une erreur à l’aide de la [commande RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx), ce qui entraîne un `SqlException` pour être déclenchés dans l’application.

> [!NOTE]
> Étant donné que le `TRY...CATCH` bloc est une nouveauté dans SQL Server 2005, le modèle ci-dessus ne fonctionnera pas si vous utilisez des versions antérieures de Microsoft SQL Server. Si vous n’utilisez pas SQL Server 2005, consultez [la gestion des Transactions dans les procédures stockées SQL Server](http://www.4guysfromrolla.com/webtech/080305-1.shtml) pour un modèle qui fonctionne avec d’autres versions de SQL Server.


Permettent d’examiner un exemple concret s. Une contrainte de clé étrangère existe entre la `Categories` et `Products` tables, ce qui signifie que chaque `CategoryID` champ dans le `Products` table doit correspondre à un `CategoryID` valeur dans le `Categories` table. Toute action qui viole cette contrainte, tels que la tentative de suppression d’une catégorie qui est associé à des produits, entraîne une violation de contrainte de clé étrangère. Pour vérifier cela, revisiter l’exemple de mise à jour et suppression des données binaires existant dans l’utilisation de la section de données binaires (`~/BinaryData/UpdatingAndDeleting.aspx`). Cette page répertorie chaque catégorie dans le système ainsi que les boutons Modifier et supprimer (voir la Figure 13), mais si vous tentez de supprimer une catégorie qui est associé à des produits - telles que des boissons - la suppression échoue en raison d’une violation de contrainte de clé étrangère (voir Figure 14).


[![Chaque catégorie est affichée dans un GridView avec les boutons Modifier et supprimer](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image38.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image37.png)

**Figure 13**: catégorie s’affiche dans un GridView avec les boutons Modifier et supprimer ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image39.png))


[![Vous ne pouvez pas supprimer une catégorie de produits existants](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image41.png)](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image40.png)

**La figure 14**: vous ne pouvez pas supprimer une catégorie de produits existants ([cliquez pour afficher l’image en taille réelle](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image42.png))


Cependant, imaginez que vous voulez autoriser des catégories pour être supprimée indépendamment de si elles sont associés à des produits. Supprimer une catégorie de produits, imaginez que vous voulez également supprimer ses produits existantes (même si une autre option consisterait à simplement définir ses produits `CategoryID` valeurs `NULL`). Cette fonctionnalité peut être implémentée via les règles en cascade de la contrainte de clé étrangère. Ou bien, nous pourrions créer une procédure stockée qui accepte un `@CategoryID` paramètre d’entrée et, lorsqu’elle est appelée, supprime de manière explicite tous les produits associés, puis la catégorie spécifiée.

Notre première tentative de ce type de procédure stockée peut se présenter comme suit :


[!code-sql[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample5.sql)]

Alors que cette opération supprimera définitivement de la catégorie de produits associés, il ne fera pas dans le cadre d’une transaction. Imaginez qu’il existe une autre contrainte de clé étrangère sur `Categories` qui serait interdire la suppression d’un particulier `@CategoryID` valeur. Le problème est que dans ce cas tous les produits sont supprimés avant que nous tentons de supprimer la catégorie. Le résultat net est que pour une catégorie de ce type, cette procédure stockée supprime toutes ses produits alors que la catégorie qui restait à effectuer, car il contient encore des enregistrements dans une autre table.

Si la procédure stockée ont été encapsulée dans l’étendue d’une transaction, toutefois, les suppressions à la `Products` table seront annulée en cas d’échec de la suppression `Categories`. Le script suivant de procédure stockée utilise une transaction afin de garantir l’atomicité entre les deux `DELETE` instructions :


[!code-sql[Main](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/samples/sample6.sql)]

Prenez le temps d’ajouter le `Categories_Delete` une procédure stockée à la base de données Northwind. Font référence à l’étape 1 pour obtenir des instructions sur l’ajout de procédures stockées pour une base de données.

## <a name="step-6-updating-thecategoriestableadapter"></a>Étape 6 : Mettre à jour le`CategoriesTableAdapter`

Durant la nous avons ajouté la `Categories_Delete` une procédure stockée à la base de données, la couche DAL est actuellement configurée pour utiliser des instructions SQL ad hoc pour effectuer la suppression. Nous devons mettre à jour le `CategoriesTableAdapter` et lui demander d’utiliser le `Categories_Delete` procédure stockée à la place.

> [!NOTE]
> Plus haut dans ce didacticiel, nous voulions travailler avec les `NorthwindWithSprocs` jeu de données. Mais que le jeu de données n’a qu’une seule entité, `ProductsDataTable`, et nous avons besoin travailler avec des catégories. Par conséquent, pour le reste de ce didacticiel lorsque parler de m données accès couche I faisant référence à la `Northwind` jeu de données, celui que nous avons tout d’abord créés dans le [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) didacticiel.


Ouvrez le Northwind DataSet, sélectionnez le `CategoriesTableAdapter`et accédez à la fenêtre Propriétés. Les listes de la fenêtre Propriétés du `InsertCommand`, `UpdateCommand`, `DeleteCommand`, et `SelectCommand` utilisé par le TableAdapter, ainsi que ses informations de connexion et de nom. Développez le `DeleteCommand` propriété pour afficher ses détails. Comme le montre la Figure 15, le `DeleteCommand` s `ComamndType` propriété a la valeur de texte, qui fait en sorte que d’envoyer le texte le `CommandText` propriété en tant que requête SQL ad hoc.


![Sélectionnez le CategoriesTableAdapter dans le concepteur pour afficher ses propriétés dans la fenêtre Propriétés](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image43.png)

**Figure 15**: sélectionnez le `CategoriesTableAdapter` dans le concepteur pour afficher ses propriétés dans la fenêtre Propriétés


Pour modifier ces paramètres, sélectionnez le texte (DeleteCommand) dans la fenêtre Propriétés, puis choisissez (nouveau) dans la liste déroulante. Cela va effacer les paramètres pour le `CommandText`, `CommandType`, et `Parameters` propriétés. Ensuite, définissez la `CommandType` propriété `StoredProcedure` puis tapez le nom de la procédure stockée pour le `CommandText` (`dbo.Categories_Delete`). Si vous vous assurez entrer les propriétés dans cet ordre : tout d’abord le `CommandType` , puis le `CommandText` -Visual Studio remplit automatiquement la collection de paramètres. Si vous n’entrez pas de ces propriétés dans cet ordre, vous devez ajouter manuellement les paramètres via l’éditeur de collections Parameters. Dans les deux cas, il s préférable de cliquer sur le bouton de sélection dans la propriété de paramètres pour afficher l’éditeur de Collection de paramètres pour vérifier que les modifications des paramètres appropriés pour les paramètres ont été apportées (voir Figure 16). Si vous ne voyez pas tous les paramètres dans la boîte de dialogue, ajoutez le `@CategoryID` paramètre manuellement (vous n’avez pas besoin ajouter le `@RETURN_VALUE` paramètre).


![Assurez-vous que les paramètres, les paramètres sont corrects](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb/_static/image44.png)

**Figure 16**: Assurez-vous que les paramètres, les paramètres sont corrects


Une fois que la couche DAL a été mis à jour, suppression d’une catégorie automatiquement supprimer toutes ses produits associés et le faire dans le cadre d’une transaction. Pour vérifier cela, revenir à la page de mise à jour et suppression des données binaires existante, puis cliquez sur le bouton Supprimer pour l’une des catégories. Avec un simple clic de la souris, la catégorie et tous ses produits associés seront supprimés.

> [!NOTE]
> Avant de tester le `Categories_Delete` procédure stockée, ce qui supprimera un certain nombre de produits en même temps que la catégorie sélectionnée, il est recommandé de faire une copie de sauvegarde de votre base de données. Si vous utilisez la `NORTHWND.MDF` dans la base de données `App_Data`, simplement fermer Visual Studio et copiez les fichiers MDF et LDF dans `App_Data` à un autre dossier. Après avoir testé les fonctionnalités, vous pouvez restaurer la base de données par la fermeture de Visual Studio et les fichiers en remplaçant l’actuel MDF et LDF dans `App_Data` avec les copies de sauvegarde.


## <a name="summary"></a>Récapitulatif

Si l’Assistant TableAdapter s génère automatiquement les procédures stockées pour nous, sont reprises quand nous pouvons déjà avoir ces procédures stockées créées ou à les créer manuellement ou avec d’autres outils à la place. Pour prendre en charge ces scénarios, le TableAdapter peut également être configuré pour pointer vers une procédure stockée existante. Dans ce didacticiel, nous avons étudié comment ajouter manuellement des procédures stockées pour une base de données via l’environnement Visual Studio et comment associer les méthodes de s TableAdapter à ces procédures stockées. Aussi, nous avons examiné les commandes T-SQL et le modèle de script utilisé pour le démarrage, validation et annulation de transactions à partir d’une procédure stockée.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Hilton Geisenow, S ren Jacob Lauritsen et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md)
[Suivant](updating-the-tableadapter-to-use-joins-vb.md)
