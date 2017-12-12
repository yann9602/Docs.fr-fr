---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/implementing-optimistic-concurrency-vb
title: "Implémentation de l’accès concurrentiel optimiste (VB) | Documents Microsoft"
author: rick-anderson
description: "Pour une application web qui permet à plusieurs utilisateurs de modifier des données, il existe un risque que deux utilisateurs peuvent modifier les mêmes données en même temps. Dans cette tutori..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 2646968c-2826-4418-b1d0-62610ed177e3
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/implementing-optimistic-concurrency-vb
msc.type: authoredcontent
ms.openlocfilehash: 6f7507bcd4f9150e4ebf239bfa9f90fce103b0d5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="implementing-optimistic-concurrency-vb"></a>Implémentation de l’accès concurrentiel optimiste (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_21_VB.exe) ou [télécharger le PDF](implementing-optimistic-concurrency-vb/_static/datatutorial21vb1.pdf)

> Pour une application web qui permet à plusieurs utilisateurs de modifier des données, il existe un risque que deux utilisateurs peuvent modifier les mêmes données en même temps. Dans ce didacticiel, nous allons implémenter le contrôle de concurrence optimiste pour gérer ce risque.


## <a name="introduction"></a>Introduction

Pour les applications web qui autorise uniquement les utilisateurs afficher les données, ou celles qui incluent un seul utilisateur qui peut modifier des données, il n’existe aucune menace de deux utilisateurs simultanés écrasent les modifications par un autre. Pour les applications web qui permettent à plusieurs utilisateurs à mettre à jour ou supprimer des données, cependant, il existe un risque pour les modifications d’un utilisateur à entrer en conflit avec un autre utilisateur simultané. Sans une stratégie d’accès concurrentiel en place, lorsque deux utilisateurs modifient simultanément un enregistrement unique, l’utilisateur qui valide les modifications dernier remplacent les modifications apportées par le premier.

Par exemple, imaginez que deux utilisateurs, Jisun et Sam, ont tous deux accédant à une page dans notre application autorisée visiteurs mettre à jour et supprimer des produits via un contrôle GridView. Cliquez sur le bouton Modifier dans le GridView en même temps. Jisun remplace le nom de produit « Thé Tran » et clique sur le bouton de mise à jour. Le résultat net est un `UPDATE` instruction est envoyée à la base de données, qui définit *tous les* de champs de mise à jour du produit (bien que Jisun mise à jour uniquement un champ, `ProductName`). À ce stade, la base de données a les valeurs « Tran thé », la catégorie boissons, le fournisseur exotiques liquides, et ainsi de suite de ce produit particulier. Toutefois, le contrôle GridView à l’écran de Sam indique toujours le nom du produit dans la ligne GridView modifiable en tant que « Martin ». Quelques secondes après que les modifications de Jisun ont été validées, Sam mises à jour de la catégorie à Condiments et clique sur la mise à jour. Cela entraîne une `UPDATE` instruction envoyée à la base de données qui définit le nom de produit à « Martin », le `CategoryID` à l’ID de catégorie boissons correspondant et ainsi de suite. Modifications du Jisun au nom du produit ont été remplacées. La figure 1 présente graphiquement cette série d’événements.


[![Lorsque deux utilisateurs mettre à jour simultanément un potentiel de l’enregistrement s’il n’y a des modifications d’un utilisateur de remplacer l’autre](implementing-optimistic-concurrency-vb/_static/image2.png)](implementing-optimistic-concurrency-vb/_static/image1.png)

**Figure 1**: lorsque deux utilisateurs simultanément mettre à jour un enregistrement il s potentiel pour un utilisateur modifie pour remplacer l’autre ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image3.png))


De même, lorsque deux utilisateurs visitent une page, un utilisateur peut être en cours de mise à jour un enregistrement lorsqu’il est supprimé par un autre utilisateur. Ou bien, lorsqu’un utilisateur charge d’une page et lorsqu’ils cliquent sur le bouton Supprimer, un autre utilisateur a modifié le contenu de cet enregistrement.

Il existe trois [contrôle d’accès concurrentiel](http://en.wikipedia.org/wiki/Concurrency_control) stratégies disponibles :

- **Ne rien faire** -si vous modifiant le même enregistrement, d’utilisateurs simultanés permettent la dernière validation win (comportement par défaut)
- [**L’accès concurrentiel optimiste** ](http://en.wikipedia.org/wiki/Optimistic_concurrency_control) -supposent que même si dans certains concurrence est en conflit tous maintenant, puis, la grande majorité du temps ces conflits ne se produiront pas ; par conséquent, en cas de conflit, simplement informer l’utilisateur que leurs modifications ne peut pas être enregistré car un autre utilisateur a modifié les mêmes données
- **L’accès simultané pessimiste** -partent du principe que les conflits d’accès concurrentiel sont courants et que les utilisateurs ne sont pas tolérer étant dit leurs modifications n’ont pas été enregistrées en raison d’une activité de simultanée d’un autre utilisateur ; par conséquent, lorsqu’un utilisateur commence la mise à jour un enregistrement, verrouiller , ce qui évite que d’autres utilisateurs de modifier ou supprimer cet enregistrement jusqu'à ce que l’utilisateur est validée leurs modifications

Tous nos didacticiels jusqu’ici ont utilisé la stratégie de résolution de concurrence par défaut, à savoir, nous avons permettent la dernière écriture win. Dans ce didacticiel, nous allons examiner comment implémenter le contrôle d’accès concurrentiel optimiste.

> [!NOTE]
> Nous ne sera pas examiner les exemples d’accès concurrentiel pessimiste dans cette série de didacticiels. L’accès simultané pessimiste est rarement utilisée, car ces verrous, si ce n’est pas correctement abandonnée, peut empêcher les autres utilisateurs de mettre à jour des données. Par exemple, si un utilisateur verrouille un enregistrement pour la modification, puis quitte pour le jour précédant la déverrouiller, aucun autre utilisateur ne sera en mesure de mettre à jour cet enregistrement jusqu'à ce que l’utilisateur d’origine retourne et termine sa mise à jour. Par conséquent, dans les situations où l’accès simultané pessimiste est utilisé, il y a généralement un délai d’attente, si atteinte, annule le verrou. Ticket client des sites Web, lequel verrouiller un emplacement particulier salle pour une courte période pendant que l’utilisateur termine le processus de commande, sont un exemple de contrôle d’accès concurrentiel pessimiste.


## <a name="step-1-looking-at-how-optimistic-concurrency-is-implemented"></a>Étape 1 : Examinant la façon dont l’accès concurrentiel optimiste est implémentée.

Contrôle d’accès concurrentiel optimiste de s’assurer que l’enregistrement en cours de mise à jour ou supprimé possède les mêmes valeurs, comme il était au moment de démarrer la mise à jour ou la suppression du processus. Par exemple, lorsque vous cliquez sur le bouton Modifier dans un GridView modifiable, les valeurs de l’enregistrement sont lues à partir de la base de données et affichés dans les zones de texte et autres contrôles Web. Ces valeurs d’origine sont enregistrés par le contrôle GridView. Plus tard, une fois que l’utilisateur modifie son et clique sur le bouton de mise à jour, les valeurs d’origine ainsi que les nouvelles valeurs sont envoyées à la couche de logique métier, puis à la couche d’accès aux données. La couche d’accès aux données doit émettre une instruction SQL qui met à jour uniquement l’enregistrement si les valeurs d’origine que l’utilisateur a commencé la modification sont identiques aux valeurs toujours dans la base de données. La figure 2 illustre cette séquence d’événements.


[![Pour la mise à jour ou la suppression aboutisse, les valeurs d’origine doivent être égales aux valeurs de base de données en cours](implementing-optimistic-concurrency-vb/_static/image5.png)](implementing-optimistic-concurrency-vb/_static/image4.png)

**Figure 2**: pour la mise à jour ou supprimer pour réussir, le d’origine valeurs doit être égale aux valeurs de base de données ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image6.png))


Il existe différentes approches pour implémenter l’accès concurrentiel optimiste (consultez [Peter A. Bromberg](http://peterbromberg.net/)de [logique de mise à jour d’accès concurrentiel Optmistic](http://www.eggheadcafe.com/articles/20050719.asp) pour un certain nombre d’options brièvement). Le groupe de données typé ADO.NET fournit une implémentation qui peut être configurée avec uniquement les graduations d’une case à cocher. L’activation de l’accès concurrentiel optimiste pour un TableAdapter dans le DataSet typé augmente la taille du TableAdapter `UPDATE` et `DELETE` instructions à inclure une comparaison de toutes les valeurs d’origine dans le `WHERE` clause. Les éléments suivants `UPDATE` instruction, par exemple, des mises à jour le nom et le prix d’un produit uniquement si les valeurs actuelles de la base de données sont égales aux valeurs qui ont été récupérées à l’origine lors de la mise à jour de l’enregistrement dans le GridView. Le `@ProductName` et `@UnitPrice` paramètres contiennent les nouvelles valeurs entrées par l’utilisateur, tandis que `@original_ProductName` et `@original_UnitPrice` contiennent les valeurs qui ont été chargées à l’origine dans le GridView, lorsque l’utilisateur a cliqué sur le bouton Modifier :


[!code-sql[Main](implementing-optimistic-concurrency-vb/samples/sample1.sql)]

> [!NOTE]
> Cela `UPDATE` instruction a été simplifiée pour une meilleure lisibilité. Dans la pratique, le `UnitPrice` archiver le `WHERE` clause serait plus complexe depuis `UnitPrice` peut contenir `NULL` s et lors de la vérification `NULL = NULL` renvoie toujours la valeur False (au lieu de cela, vous devez utiliser `IS NULL`).


En plus d’utiliser un sous-jacent différents `UPDATE` instruction, configurez un TableAdapter à utiliser optimistic concurrency modifie également la signature de la base de données direct de méthodes. Rappel à partir de notre premier didacticiel, [ *création d’une couche d’accès aux données*](../introduction/creating-a-data-access-layer-cs.md), que les méthodes directs de base de données ont été ceux qui accepte une liste de valeur scalaire de valeurs comme paramètres d’entrée (au lieu d’un DataRow fortement typée ou Instance de la table de données). Lors de l’utilisation de l’accès concurrentiel optimiste, la base de données directe `Update()` et `Delete()` méthodes incluent les paramètres d’entrée pour les valeurs d’origine ainsi. En outre, le code dans la couche BLL pour le lot à l’aide de mettre à jour le modèle (le `Update()` des surcharges de méthode qui acceptent des DataRows et de tables de données plutôt que sous forme de valeurs scalaires) doit également être modifié.

Plutôt qu’étendre notre existant TableAdapters de couche DAL pour l’accès concurrentiel optimiste (qui nécessite la modification de la couche BLL pour prendre en charge), nous allons créer à la place un nouveau DataSet typé nommé `NorthwindOptimisticConcurrency`, pour lequel nous allons ajouter un `Products` TableAdapter qui utilise l’accès concurrentiel optimiste. Après cela, nous allons créer un `ProductsOptimisticConcurrencyBLL` classe couche de logique métier qui a les modifications appropriées pour prendre en charge de la couche DAL l’accès concurrentiel optimiste. Une fois que ce point de départ a été établi, nous serons prêts à créer la page ASP.NET.

## <a name="step-2-creating-a-data-access-layer-that-supports-optimistic-concurrency"></a>Étape 2 : Création d’une couche d’accès aux données qui prend en charge l’accès concurrentiel optimiste

Pour créer un nouveau DataSet typé, cliquez sur le `DAL` dossier au sein de la `App_Code` dossier et ajouter un nouveau DataSet nommé `NorthwindOptimisticConcurrency`. Comme nous l’avons vu dans le premier didacticiel, cela donc ajouterez un nouveau TableAdapter au DataSet typé, lancer automatiquement l’Assistant Configuration de TableAdapter. Dans le premier écran, nous allons vous y êtes invités à spécifier la base de données pour se connecter à, de se connecter à la même base de données Northwind à l’aide du `NORTHWNDConnectionString` définition à partir de `Web.config`.


[![Se connecter à la même base de données Northwind](implementing-optimistic-concurrency-vb/_static/image8.png)](implementing-optimistic-concurrency-vb/_static/image7.png)

**Figure 3**: se connecter à la même base de données Northwind ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image9.png))


Ensuite, nous sommes invités à comment interroger les données : via une instruction SQL ad hoc, une nouvelle procédure stockée ou existant de la procédure stockée. Étant donné que nous avons utilisé les requêtes SQL ad hoc dans notre DAL d’origine, utilisez cette option ici également.


[![Spécifier les données à récupérer à l’aide d’une instruction SQL de Ad Hoc](implementing-optimistic-concurrency-vb/_static/image11.png)](implementing-optimistic-concurrency-vb/_static/image10.png)

**Figure 4**: spécifier les données à récupérer à l’aide d’une instruction SQL de Ad-Hoc ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image12.png))


Dans l’écran suivant, entrez la requête SQL à utiliser pour récupérer les informations de produit. Nous allons utiliser la même requête SQL exacte utilisée pour le `Products` TableAdapter à partir de notre DAL d’origine, qui retourne tous les `Product` colonnes ainsi que les noms de fournisseur et la catégorie du produit :


[!code-sql[Main](implementing-optimistic-concurrency-vb/samples/sample2.sql)]


[![Utilisez la même requête SQL à partir du TableAdapter de produits dans la couche DAL d’origine](implementing-optimistic-concurrency-vb/_static/image14.png)](implementing-optimistic-concurrency-vb/_static/image13.png)

**Figure 5**: utiliser la même requête SQL à partir de la `Products` TableAdapter dans la couche DAL d’origine ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image15.png))


Avant de passer à l’écran suivant, cliquez sur le bouton Options avancées. Pour que ce contrôle de l’accès concurrentiel optimiste emploient TableAdapter, cochez simplement la case à cocher « Utiliser l’accès concurrentiel optimiste ».


[![Activer le contrôle d’accès concurrentiel optimiste par vérification du &quot;accès concurrentiel optimiste&quot; case à cocher](implementing-optimistic-concurrency-vb/_static/image17.png)](implementing-optimistic-concurrency-vb/_static/image16.png)

**Figure 6**: activer le contrôle d’accès concurrentiel optimiste en activant la case à cocher « Utiliser l’accès concurrentiel optimiste » ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image18.png))


Enfin, indiquent que le TableAdapter doit utiliser les modèles d’accès aux données à la fois remplir un DataTable et retournent un DataTable ; également indiquer que les méthodes directs de base de données doivent être créés. Changer le nom de méthode pour le retour d’un modèle de DataTable de GetData à GetProducts, afin de refléter les conventions d’affectation de noms utilisés dans notre DAL d’origine.


[![Avoir le TableAdapter utilisent tous les modèles d’accès aux données](implementing-optimistic-concurrency-vb/_static/image20.png)](implementing-optimistic-concurrency-vb/_static/image19.png)

**Figure 7**: ont le TableAdapter utilisent toutes les données de modèles d’accès ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image21.png))


Après la fin de l’Assistant, le Concepteur de DataSet comprend des fortement typé `Products` DataTable et TableAdapter. Prenez un moment pour renommer la table de données à partir de `Products` à `ProductsOptimisticConcurrency`, ce que vous pouvez faire en cliquant sur la barre de titre de la table de données et en sélectionnant Renommer dans le menu contextuel.


[![Une table de données et un TableAdapter ont été ajoutés au DataSet typé](implementing-optimistic-concurrency-vb/_static/image23.png)](implementing-optimistic-concurrency-vb/_static/image22.png)

**Figure 8**: un DataTable et TableAdapter ont été ajoutés au DataSet typé ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image24.png))


Pour voir les différences entre les `UPDATE` et `DELETE` interroge entre le `ProductsOptimisticConcurrency` TableAdapter (qui utilise l’accès concurrentiel optimiste) et le TableAdapter de produits (qui ne), cliquez sur le TableAdapter et accédez à la fenêtre Propriétés. Dans le `DeleteCommand` et `UpdateCommand` propriétés `CommandText` des sous-propriétés, vous pouvez voir la syntaxe SQL réelle qui est envoyée à la base de données lors de la mise à jour ou les méthodes de suppression de la couche DAL sont appelées. Pour le `ProductsOptimisticConcurrency` TableAdapter la `DELETE` instruction utilisée est :


[!code-sql[Main](implementing-optimistic-concurrency-vb/samples/sample3.sql)]

Alors que la `DELETE` instruction du TableAdapter de produit dans notre DAL d’origine est la plus simple :


[!code-sql[Main](implementing-optimistic-concurrency-vb/samples/sample4.sql)]

Comme vous pouvez le voir, la `WHERE` clause dans la `DELETE` instruction pour le TableAdapter qui utilise l’accès concurrentiel optimiste inclut une comparaison entre chaque le `Product` table existant des valeurs de colonne et les valeurs d’origine au moment le Dernier remplissage GridView (ou DetailsView ou FormView). Depuis tous les champs autres que `ProductID`, `ProductName`, et `Discontinued` peut avoir `NULL` valeurs, les paramètres supplémentaires et les contrôles sont inclus à comparer correctement `NULL` des valeurs dans le `WHERE` clause.

Nous n’ajouter toutes les tables de données supplémentaires pour le DataSet d’accès concurrentiel optimisé pour ce didacticiel, comme notre page ASP.NET fournit uniquement la mise à jour et suppression des informations sur le produit. Toutefois, nous toujours besoin d’ajouter le `GetProductByProductID(productID)` méthode à la `ProductsOptimisticConcurrency` TableAdapter.

Pour ce faire, cliquez sur la barre de titre du TableAdapter (droite zone au-dessus de la `Fill` et `GetProducts` les noms de méthode) et choisissez Ajouter une requête dans le menu contextuel. Cette action lance l’Assistant Configuration de requête TableAdapter. Comme avec la configuration initiale de notre TableAdapter, choisissez de créer la `GetProductByProductID(productID)` à l’aide d’une instruction SQL d’ad-hoc (méthode) (voir Figure 4). Étant donné que la `GetProductByProductID(productID)` méthode retourne des informations sur un produit particulier, d’indiquer que cette requête est un `SELECT` type qui retourne des lignes de requête.


[![Marquez le Type de requête comme un &quot;SELECT qui retourne des lignes&quot;](implementing-optimistic-concurrency-vb/_static/image26.png)](implementing-optimistic-concurrency-vb/_static/image25.png)

**Figure 9**: Marquez le Type de requête comme un «`SELECT` qui retourne des lignes » ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image27.png))


Dans l’écran suivant, nous mettons invités pour la requête SQL à utiliser avec la requête du TableAdapter par défaut prédéfinie. Augmenter la requête existante pour inclure la clause `WHERE ProductID = @ProductID`, comme illustré dans la Figure 10.


[![Ajouter un WHERE Clause à la requête préchargée renvoie un enregistrement de produit spécifique](implementing-optimistic-concurrency-vb/_static/image29.png)](implementing-optimistic-concurrency-vb/_static/image28.png)

**La figure 10**: ajouter un `WHERE` Clause à la requête Pre-Loaded renvoie un enregistrement de produit spécifique ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image30.png))


Enfin, modifiez les noms de méthode générée à `FillByProductID` et `GetProductByProductID`.


[![Renommez les méthodes FillByProductID et GetProductByProductID](implementing-optimistic-concurrency-vb/_static/image32.png)](implementing-optimistic-concurrency-vb/_static/image31.png)

**Figure 11**: Renommez les méthodes à `FillByProductID` et `GetProductByProductID` ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image33.png))


Avec cet Assistant terminé, le TableAdapter contient désormais deux méthodes pour récupérer des données : `GetProducts()`, qui retourne *tous les* produits ; et `GetProductByProductID(productID)`, qui retourne le produit spécifié.

## <a name="step-3-creating-a-business-logic-layer-for-the-optimistic-concurrency-enabled-dal"></a>Étape 3 : Création d’une couche de logique métier pour la couche DAL accès concurrentiel optimiste

Notre existant `ProductsBLL` classe a des exemples d’utilisation de la mise à jour par lots et les modèles directs de base de données. Le `AddProduct` (méthode) et `UpdateProduct` surcharges pour les deux utilisent le modèle de mise à jour par lots, en passant un `ProductRow` instance à la méthode du TableAdapter mise à jour. Le `DeleteProduct` méthode, utilise en revanche, le modèle direct de base de données, appel du TableAdapter `Delete(productID)` (méthode).

Avec la nouvelle `ProductsOptimisticConcurrency` TableAdapter, la base de données direct de méthodes exigent que les valeurs d’origine également être passé. Par exemple, le `Delete` méthode attend maintenant dix paramètres d’entrée : l’original `ProductID`, `ProductName`, `SupplierID`, `CategoryID`, `QuantityPerUnit`, `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, `ReorderLevel`, et `Discontinued`. Il utilise les valeurs de ces paramètres d’entrée supplémentaires dans `WHERE` clause de la `DELETE` instruction envoyée à la base de données, uniquement la suppression de l’enregistrement spécifié si les valeurs actuelles de la base de données correspondent à ceux d’origine.

Lors de la signature de méthode pour du TableAdapter `Update` méthode utilisé dans le modèle de mise à jour de lot n’a pas changé, le code nécessaire pour enregistrer les valeurs d’origine et les nouvelles a. Par conséquent, au lieu de tenter d’utiliser la couche DAL accès concurrentiel optimiste avec notre existant `ProductsBLL` de classe, nous allons créer une nouvelle classe de la couche de logique métier pour travailler avec nos nouvelles DAL.

Ajoutez une classe nommée `ProductsOptimisticConcurrencyBLL` à la `BLL` dossier dans le `App_Code` dossier.


![Ajoutez la classe ProductsOptimisticConcurrencyBLL dans le dossier de la couche BLL](implementing-optimistic-concurrency-vb/_static/image34.png)

**Figure 12**: ajouter la `ProductsOptimisticConcurrencyBLL` classe dans le dossier de la couche BLL


Ensuite, ajoutez le code suivant à la `ProductsOptimisticConcurrencyBLL` classe :


[!code-vb[Main](implementing-optimistic-concurrency-vb/samples/sample5.vb)]

Notez l’utilisation `NorthwindOptimisticConcurrencyTableAdapters` instruction au-dessus du début de la déclaration de classe. Le `NorthwindOptimisticConcurrencyTableAdapters` espace de noms contient le `ProductsOptimisticConcurrencyTableAdapter` (classe), qui fournit des méthodes de la couche DAL. Également avant la déclaration de classe, vous pouvez trouver le `System.ComponentModel.DataObject` attribut, qui fait en sorte que Visual Studio pour inclure cette classe dans la liste déroulante de l’Assistant ObjectDataSource.

Le `ProductsOptimisticConcurrencyBLL`de `Adapter` propriété fournit un accès rapide à une instance de la `ProductsOptimisticConcurrencyTableAdapter` de classe et suit le modèle utilisé dans nos classes d’origine de la couche BLL (`ProductsBLL`, `CategoriesBLL`, et ainsi de suite). Enfin, le `GetProducts()` méthode appelle simplement vers le bas de la couche DAL `GetProducts()` méthode et retourne un `ProductsOptimisticConcurrencyDataTable` objet rempli avec une `ProductsOptimisticConcurrencyRow` instance pour chaque enregistrement de produit dans la base de données.

## <a name="deleting-a-product-using-the-db-direct-pattern-with-optimistic-concurrency"></a>Suppression d’un produit à l’aide du modèle Direct de base de données avec l’accès concurrentiel optimiste

Lorsque vous utilisez le modèle direct de base de données par rapport à une couche DAL qui utilise l’accès concurrentiel optimiste, les méthodes doivent être passés les valeurs nouvelles et d’origine. Pour la suppression, il n’existe aucune nouvelle valeur, par conséquent, seules les valeurs d’origine doivent être passés dans. Dans notre BLL, ensuite, nous devons accepter tous les paramètres d’origine en tant que paramètres d’entrée. Examinons à présent le `DeleteProduct` méthode dans la `ProductsOptimisticConcurrencyBLL` classe utilisent la méthode directe de base de données. Cela signifie que cette méthode doit prendre dans tous les champs de données produit dix en tant que paramètres d’entrée et de passer à la couche DAL, comme indiqué dans le code suivant :


[!code-vb[Main](implementing-optimistic-concurrency-vb/samples/sample6.vb)]

Si les valeurs d’origine - ces derniers chargés dans le GridView (ou DetailsView ou FormView) - diffèrent des valeurs dans la base de données lorsque l’utilisateur clique sur le bouton Supprimer le `WHERE` clause ne correspond à n’importe quel enregistrement de la base de données et qu’aucun enregistrement seront affectées. Par conséquent, le TableAdapter `Delete` méthode retournera `0` et de la couche BLL `DeleteProduct` méthode retournera `false`.

## <a name="updating-a-product-using-the-batch-update-pattern-with-optimistic-concurrency"></a>Mise à jour d’un produit à l’aide du modèle de mise à jour par lots avec l’accès concurrentiel optimiste

Comme indiqué précédemment, le TableAdapter `Update` méthode pour le modèle de mise à jour par lots a la même signature de méthode, quelle que soit ou non l’accès concurrentiel optimiste est employé. À savoir, le `Update` méthode attend un DataRow, un tableau de DataRows, un DataTable ou un DataSet typé. Il n’existe pas de paramètres d’entrée supplémentaires permettant de spécifier les valeurs d’origine. Cela est possible, car la table de données effectue le suivi des valeurs d’origine et modifiés pour son DataRow(s). Lorsque la couche DAL émet son `UPDATE` instruction, le `@original_ColumnName` paramètres sont remplis avec les valeurs d’origine de DataRow, tandis que le `@ColumnName` paramètres sont remplis avec les valeurs modifiées de DataRow.

Dans la `ProductsBLL` classe (qui utilise notre d’accès concurrentiel d’origine, non optimiste DAL), lorsque vous utilisez le modèle de mise à jour par lots pour mettre à jour les informations produit notre code exécute la séquence d’événements suivante :

1. Lire les informations de produit de base de données en cours dans un `ProductRow` instance à l’aide du TableAdapter `GetProductByProductID(productID)` (méthode)
2. Affecter les nouvelles valeurs à la `ProductRow` instance à l’étape 1
3. Appelez la `Update` méthode, en passant le `ProductRow` instance

Cette séquence d’étapes, toutefois, ne sont pas correctement prendre en charge l’accès concurrentiel optimiste, car le `ProductRow` remplie dans étape 1 est rempli directement à partir de la base de données, ce qui signifie que les valeurs d’origine utilisés par l’objet DataRow sont ceux qui existent actuellement dans le base de données et non ceux qui ont été liées au GridView au début du processus de modification. Au lieu de cela, lors de l’utilisation optimiste accès concurrentiel DAL, nous devons modifier le `UpdateProduct` surcharges de méthode à utiliser comme suit :

1. Lire les informations de produit de base de données en cours dans un `ProductsOptimisticConcurrencyRow` instance à l’aide du TableAdapter `GetProductByProductID(productID)` (méthode)
2. Affecter le *d’origine* valeurs à la `ProductsOptimisticConcurrencyRow` instance à l’étape 1
3. Appelez le `ProductsOptimisticConcurrencyRow` l’instance `AcceptChanges()` (méthode), qui indique à l’objet DataRow que ses valeurs actuelles sont ceux d’origine » »
4. Affecter le *nouveau* valeurs à la `ProductsOptimisticConcurrencyRow` instance
5. Appelez la `Update` méthode, en passant le `ProductsOptimisticConcurrencyRow` instance

Étape 1 des lectures dans toutes les valeurs actuelles de la base de données pour l’enregistrement de produit spécifié. Cette étape est superflue dans le `UpdateProduct` surcharge qui met à jour *tous les* des colonnes de produit (en tant que ces valeurs sont remplacées à l’étape 2), mais est essentiel pour ces surcharges où seul un sous-ensemble des valeurs de colonnes sont transmis comme paramètres d’entrée. Une fois que les valeurs d’origine ont été attribuées à la `ProductsOptimisticConcurrencyRow` instance, le `AcceptChanges()` méthode est appelée, ce qui indique que les valeurs de DataRow actuels en tant que les valeurs d’origine à utiliser dans le `@original_ColumnName` paramètres dans la `UPDATE` instruction. Ensuite, les nouvelles valeurs de paramètre sont affectés à la `ProductsOptimisticConcurrencyRow` et, enfin, le `Update` méthode est appelée, en passant l’objet DataRow.

Le code suivant illustre la `UpdateProduct` surcharge qui accepte toutes les données de produit champs comme paramètres d’entrée. Tandis que les ne pas ici, le `ProductsOptimisticConcurrencyBLL` classe inclus dans le téléchargement pour ce didacticiel contient également un `UpdateProduct` surcharge qui accepte du uniquement le produit nom et prix en tant que paramètres d’entrée.


[!code-vb[Main](implementing-optimistic-concurrency-vb/samples/sample7.vb)]

## <a name="step-4-passing-the-original-and-new-values-from-the-aspnet-page-to-the-bll-methods"></a>Étape 4 : En passant les valeurs nouvelles et d’origine à partir de la Page ASP.NET pour les méthodes de la couche BLL

Avec la couche DAL et de la couche BLL terminée, reste qu’à créer une page ASP.NET qui peut utiliser la logique d’accès concurrentiel optimiste intégrée dans le système. Plus précisément, les données de contrôle Web (le GridView, DetailsView ou FormView) doivent mémoriser que ses valeurs d’origine et ObjectDataSource doivent passer les deux ensembles de valeurs à la couche de logique métier. En outre, la page ASP.NET doit être configurée pour gérer correctement les violations d’accès concurrentiel.

Commencez par ouvrir le `OptimisticConcurrency.aspx` page dans le `EditInsertDelete` dossier et en ajoutant un GridView vers le concepteur, en définissant ses `ID` propriété `ProductsGrid`. À partir de la balise de GridView, choisir de créer un nouveau ObjectDataSource nommé `ProductsOptimisticConcurrencyDataSource`. Étant donné que nous voulons que ce ObjectDataSource à utiliser la couche DAL qui prend en charge l’accès concurrentiel optimiste, configurez-le pour utiliser le `ProductsOptimisticConcurrencyBLL` objet.


[![Que l’utilisation de ObjectDataSource l’objet de ProductsOptimisticConcurrencyBLL](implementing-optimistic-concurrency-vb/_static/image36.png)](implementing-optimistic-concurrency-vb/_static/image35.png)

**Figure 13**: l’utilisation de ObjectDataSource le `ProductsOptimisticConcurrencyBLL` objet ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image37.png))


Choisissez le `GetProducts`, `UpdateProduct`, et `DeleteProduct` méthodes à partir des listes déroulantes dans l’Assistant. Pour la méthode UpdateProduct, utilisez la surcharge qui accepte tous les champs de données du produit.

## <a name="configuring-the-objectdatasource-controls-properties"></a>Configuration des propriétés du contrôle ObjectDataSource

Après la fin de l’Assistant, balisage déclaratif de l’ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](implementing-optimistic-concurrency-vb/samples/sample8.aspx)]

Comme vous pouvez le voir, la `DeleteParameters` collection contient un `Parameter` instance pour chacun des dix paramètres d’entrée dans le `ProductsOptimisticConcurrencyBLL` la classe `DeleteProduct` (méthode). De même, la `UpdateParameters` collection contient un `Parameter` instance pour chacun des paramètres d’entrée dans `UpdateProduct`.

Pour ces didacticiels précédents impliquant la modification des données, nous supprimons l’ObjectDataSource `OldValuesParameterFormatString` propriété à ce stade, étant donné que cette propriété indique que la méthode de la couche BLL prévoit les valeurs anciennes (ou d’origine) à passer dans, ainsi que les nouvelles valeurs. En outre, cette valeur de propriété indique les noms de paramètre d’entrée pour les valeurs d’origine. Étant donné que nous sommes en passant les valeurs d’origine dans la couche BLL, faire *pas* supprimez cette propriété.

> [!NOTE]
> La valeur de la `OldValuesParameterFormatString` propriété doit correspondre aux noms de paramètre d’entrée de la couche BLL qui attendent les valeurs d’origine. Étant donné que nous avons nommé ces paramètres `original_productName`, `original_supplierID`, et ainsi de suite, vous pouvez laisser le `OldValuesParameterFormatString` en tant que valeur de la propriété `original_{0}`. Si, toutefois, les paramètres d’entrée des méthodes de la couche BLL avaient des noms comme `old_productName`, `old_supplierID`, et ainsi de suite, vous devez mettre à jour le `OldValuesParameterFormatString` propriété `old_{0}`.


Il existe un paramètre de propriété final qui doit être effectuée dans l’ordre pour ObjectDataSource correctement passer les valeurs d’origine pour les méthodes de la couche BLL. ObjectDataSource a un [propriété ConflictDetection](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.objectdatasource.conflictdetection.aspx) pouvant être assigné à [une des deux valeurs](https://msdn.microsoft.com/en-US/library/system.web.ui.conflictoptions.aspx):

- `OverwriteChanges`-la valeur par défaut ; n’envoie pas les valeurs d’origine pour les paramètres d’entrée d’origine des méthodes de la couche BLL
- `CompareAllValues`-envoie les valeurs d’origine pour les méthodes de la couche BLL ; Choisissez cette option lors de l’utilisation de l’accès concurrentiel optimiste

Prenez un moment pour définir le `ConflictDetection` propriété `CompareAllValues`.

## <a name="configuring-the-gridviews-properties-and-fields"></a>Configuration des propriétés et des champs du contrôle GridView

Avec les propriétés de l’ObjectDataSource correctement configurées, penchons-nous à configurer le contrôle GridView. Tout d’abord, étant donné que nous voulons que le contrôle GridView pour prendre en charge la modification et la suppression, cliquez sur les cases à cocher Activer la modification et activer la suppression à partir de la balise active du GridView. Cette opération ajoute une CommandField dont `ShowEditButton` et `ShowDeleteButton` sont toutes deux définies sur `true`.

Lorsqu’elle est liée à la `ProductsOptimisticConcurrencyDataSource` ObjectDataSource, le contrôle GridView contient un champ pour chacun des champs de données du produit. Alors que cet un GridView peut être modifié, l’expérience utilisateur est acceptable n’est pas défini. Le `CategoryID` et `SupplierID` BoundFields restitue sous forme de zones de texte, demandant à l’utilisateur à entrer la catégorie appropriée et le fournisseur en tant que numéros d’identification. Il n’y a aucune mise en forme pour les champs numériques et aucun contrôle de validation pour vous assurer que le nom du produit a été fourni et que le prix unitaire unités en stock, les unités sur l’ordre et les valeurs de niveau de regroupement sont les deux valeurs numériques appropriées et sont supérieures à ou égal à à zéro.

Comme expliqué dans la *Ajout de contrôles de Validation à la modification et l’insertion des Interfaces* et *personnalisation de l’Interface de Modification de données* didacticiels, l’interface utilisateur peut être personnalisé par en remplaçant le BoundFields avec TemplateField. J’ai modifié cette GridView et son interface de modification de plusieurs manières :

- Supprimer le `ProductID`, `SupplierName`, et `CategoryName` BoundFields
- Convertir le `ProductName` BoundField en TemplateField et d’ajouter un contrôle RequiredFieldValidation.
- Convertir le `CategoryID` et `SupplierID` BoundFields à TemplateField et ajustée de l’interface de modification pour utiliser la compréhension des listes plutôt que des zones de texte. Dans ces TemplateField `ItemTemplates`, le `CategoryName` et `SupplierName` des champs de données sont affichées.
- Convertir le `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, et `ReorderLevel` BoundFields TemplateField et contrôles CompareValidator ajoutés.

Étant donné que nous avons déjà examiné comment accomplir ces tâches dans les didacticiels précédents, je simplement répertorier la dernière syntaxe déclarative et laissez l’implémentation est recommandé.


[!code-aspx[Main](implementing-optimistic-concurrency-vb/samples/sample9.aspx)]

Nous sommes très proche présentant un exemple entièrement fonctionnel. Toutefois, il existe quelques subtilités CA file et provoquer des problèmes. En outre, nous devons encore une interface qui avertit l’utilisateur qu’une violation d’accès concurrentiel s’est produite.

> [!NOTE]
> Pour un contrôle Web de données puisse passer correctement les valeurs d’origine ObjectDataSource (qui sont ensuite transmis à la couche BLL), il est essentiel que du GridView `EnableViewState` est définie sur `true` (la valeur par défaut). Si vous désactivez l’état d’affichage, les valeurs d’origine sont perdus en cas de publication (postback).


## <a name="passing-the-correct-original-values-to-the-objectdatasource"></a>En passant les valeurs d’origine correctes pour ObjectDataSource

Il existe quelques problèmes avec la façon dont le contrôle GridView a été configuré. Si l’ObjectDataSource `ConflictDetection` est définie sur `CompareAllValues` (en l’état nôtres), lorsque l’ObjectDataSource `Update()` ou `Delete()` les méthodes sont appelées par le contrôle GridView (ou DetailsView ou FormView), ObjectDataSource tente de copier le Les valeurs d’origine du contrôle GridView dans son approprié `Parameter` instances. Faire référence à la Figure 2 pour une représentation graphique de ce processus.

Plus précisément, les valeurs d’origine du GridView affectés les valeurs dans les instructions de liaison de données bidirectionnelle chaque fois que les données sont liées au GridView. Par conséquent, il est essentiel que les toutes les valeurs d’origine requises sont capturés par la liaison de données bidirectionnelle et qu’elles sont fournies dans un format convertible.

Pour voir à quoi cela est important, prenez un moment pour consulter notre page dans un navigateur. Comme prévu, le contrôle GridView répertorie chaque produit avec le bouton Modifier et supprimer dans la colonne de gauche.


[![Les produits répertoriés dans un contrôle GridView](implementing-optimistic-concurrency-vb/_static/image39.png)](implementing-optimistic-concurrency-vb/_static/image38.png)

**La figure 14**: les produits sont répertoriés dans un GridView ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image40.png))


Si vous cliquez sur le bouton Supprimer pour tout produit, un `FormatException` est levée.


[![Tentative de suppression de tous les résultats dans une exception FormatException produit](implementing-optimistic-concurrency-vb/_static/image42.png)](implementing-optimistic-concurrency-vb/_static/image41.png)

**Figure 15**: tentative de supprimer n’importe quel produit entraîne un `FormatException` ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image43.png))


Le `FormatException` est déclenché quand l’ObjectDataSource tente de lire dans l’original `UnitPrice` valeur. Étant donné que la `ItemTemplate` a le `UnitPrice` la forme d’une devise (`<%# Bind("UnitPrice", "{0:C}") %>`), il inclut un symbole monétaire, comme 19,95 $. Le `FormatException` tombe ObjectDataSource tente de convertir cette chaîne en un `decimal`. Pour contourner ce problème, nous vous proposons un certain nombre d’options :

- Suppression de la devise à partir de la mise en forme le `ItemTemplate`. Autrement dit, au lieu d’utiliser `<%# Bind("UnitPrice", "{0:C}") %>`, utilisez simplement `<%# Bind("UnitPrice") %>`. L’inconvénient de ce est que le prix est n’est plus mis en forme.
- Afficher le `UnitPrice` mis en forme comme une valeur monétaire dans le `ItemTemplate`, mais utiliser le `Eval` mot clé pour ce faire. N’oubliez pas que `Eval` effectue la liaison de données à sens unique. Nous devons encore fournir le `UnitPrice` valeur pour les valeurs d’origine, afin de nous aurez toujours besoin d’une instruction de la liaison de données bidirectionnelle dans le `ItemTemplate`, mais cela peut être placé dans un contrôle Web Label dont `Visible` est définie sur `false`. Nous pouvons utiliser le balisage suivant dans le modèle ItemTemplate :


[!code-aspx[Main](implementing-optimistic-concurrency-vb/samples/sample10.aspx)]

- Suppression de la devise à partir de la mise en forme le `ItemTemplate`, à l’aide `<%# Bind("UnitPrice") %>`. Dans du GridView `RowDataBound` Gestionnaire d’événements, par programmation l’accès Web de l’étiquette de contrôle dans lequel le `UnitPrice` valeur est affichée et définir son `Text` propriété vers la version mise en forme.
- Laissez le `UnitPrice` mis en forme comme une valeur monétaire. Dans du GridView `RowDeleting` Gestionnaire d’événements, remplacez la version d’origine existante `UnitPrice` valeur (19,95$) avec une valeur décimale réelle à l’aide de `Decimal.Parse`. Nous avons vu comment faire quelque chose de similaire dans le `RowUpdating` Gestionnaire d’événements dans le [ *BLL - gestion et des Exceptions au niveau de la couche DAL dans une Page ASP.NET* ](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-cs.md) didacticiel.

Pour mon exemple j’ai opté pour la deuxième approche, ajout d’un site Web étiquette masquée contrôle dont `Text` propriété est liées à la non mise en forme de données bidirectionnelle `UnitPrice` valeur.

Après la résolution de ce problème, essayez de cliquer sur le bouton Supprimer pour tous les produits à nouveau. Cette fois, vous obtiendrez une `InvalidOperationException` lorsque ObjectDataSource tente d’appeler de la couche BLL `UpdateProduct` (méthode).


[![ObjectDataSource ne peut pas trouver une méthode avec les paramètres d’entrée, il souhaite envoyer](implementing-optimistic-concurrency-vb/_static/image45.png)](implementing-optimistic-concurrency-vb/_static/image44.png)

**Figure 16**: ObjectDataSource ne peut pas trouver une méthode avec les paramètres d’entrée, il souhaite envoyer ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image46.png))


Examinez le message de l’exception, il est clair que ObjectDataSource souhaite appeler une couche BLL `DeleteProduct` méthode inclut `original_CategoryName` et `original_SupplierName` paramètres d’entrée. C’est parce que le `ItemTemplate` s pour le `CategoryID` et `SupplierID` TemplateField contient des instructions de liaison bidirectionnelles avec le `CategoryName` et `SupplierName` des champs de données. Au lieu de cela, nous devons inclure `Bind` instructions avec les `CategoryID` et `SupplierID` des champs de données. Pour ce faire, remplacez les instructions de liaison existantes avec `Eval` instructions, puis ajoutez une étiquette masquée contrôles dont `Text` propriétés sont liées à la `CategoryID` et `SupplierID` des champs de données à l’aide de la liaison de données bidirectionnelle, comme indiqué ci-dessous :


[!code-aspx[Main](implementing-optimistic-concurrency-vb/samples/sample11.aspx)]

Avec ces modifications, nous sommes en mesure de supprimer correctement et de modifier les informations de produit ! À l’étape 5, nous allons examiner comment vérifier que les violations d’accès concurrentiel sont détectées. Mais pour l’instant, prendre quelques minutes pour essayer de mettre à jour et suppression des enregistrements pour vous assurer que la mise à jour et suppression d’un utilisateur unique fonctionne comme prévu.

## <a name="step-5-testing-the-optimistic-concurrency-support"></a>Étape 5 : Test de la prise en charge l’accès concurrentiel optimiste

Pour vérifier que les violations d’accès concurrentiel sont détectées (plutôt que de données qui doit être remplacées à l’aveugle), nous devons ouvrez deux fenêtres de navigateur à cette page. Dans les deux instances du navigateur, cliquez sur le bouton Modifier pour Tran. Puis, dans simplement un des navigateurs, remplacez le nom « Thé Tran » et cliquez sur la mise à jour. La mise à jour doit réussir et retourner le contrôle GridView à son état avant modification, avec « Tran thé » en tant que le nouveau nom de produit.

Dans l’autre instance de fenêtre de navigateur, toutefois, la zone de texte Nom du produit indique toujours « Martin ». Dans cette deuxième fenêtre de navigateur, mettez à jour le `UnitPrice` à `25.00`. Sans prise en charge de l’accès concurrentiel optimiste, en cliquant sur la mise à jour dans la deuxième instance du navigateur serait rétablir le nom du produit « Martin », en écrasant les modifications apportées par la première instance du navigateur. Avec les employés l’accès concurrentiel optimiste, toutefois, en cliquant sur le bouton de mise à jour dans la deuxième instance du navigateur entraîne un [DBConcurrencyException](https://msdn.microsoft.com/en-us/library/system.data.dbconcurrencyexception.aspx).


[![Lorsqu’une Violation d’accès concurrentiel est détectée, un objet DBConcurrencyException est levé.](implementing-optimistic-concurrency-vb/_static/image48.png)](implementing-optimistic-concurrency-vb/_static/image47.png)

**Figure 17**: lors de la Violation d’accès concurrentiel a est détectée, un `DBConcurrencyException` est levée ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image49.png))


Le `DBConcurrencyException` n’est levée lorsque le modèle de mise à jour de la couche DAL lot est utilisé. Le modèle direct de base de données ne lève pas d’exception, il indique simplement qu’aucune ligne n’ont été affectés. Pour illustrer cela, retourner les deux instances du navigateur GridView à leur état de modification préalable. Ensuite, dans la première instance du navigateur, cliquez sur le bouton Modifier et modifiez le nom du produit à partir de « Thé Tran » à « Martin » puis cliquez sur mise à jour. Dans la deuxième fenêtre de navigateur, cliquez sur le bouton Supprimer pour Tran.

Lorsque vous cliquez sur Supprimer, publication de la page, le contrôle GridView appelle l’ObjectDataSource `Delete()` méthode et ObjectDataSource des appels vers le bas dans la `ProductsOptimisticConcurrencyBLL` de classe `DeleteProduct` méthode, en passant les valeurs d’origine. La version d’origine `ProductName` valeur de la deuxième instance de navigateur est « Thé Tran », qui ne correspond pas avec actuel `ProductName` valeur dans la base de données. Par conséquent la `DELETE` instruction émise à la base de données n’affecte aucune ligne, car il n’existe aucun enregistrement dans la base de données qui le `WHERE` clause satisfait. Le `DeleteProduct` méthode renvoie `false` et les données de l’ObjectDataSource sont reliées au GridView.

Du point de vue de l’utilisateur final, cliquez sur le bouton de suppression de thé Tran dans la deuxième fenêtre de navigateur a provoqué l’écran pour flash et à fidéliser, le produit est toujours là, bien qu’il est maintenant répertorié en tant que « Martin » (le produit changement de nom effectué par le premier navigateur instance). Si l’utilisateur clique sur le bouton Supprimer à nouveau, la suppression réussit, que le contrôle GridView d’origine de le `ProductName` valeur (« Martin ») correspond maintenant à la valeur dans la base de données.

Dans ces deux cas, l’expérience utilisateur est loin d’être idéal. Nous clairement ne souhaitons montrer l’utilisateur les moindres détails de la `DBConcurrencyException` exception lors de l’utilisation du modèle de mise à jour par lots. Et le comportement lors de l’utilisation du modèle direct de base de données est prêter à confusion, comme l’échec de la commande d’utilisateurs, mais il n’a aucune indication précise de la raison pour laquelle.

Pour résoudre ces deux problèmes, nous pouvons créer des contrôles Web Label sur la page qui fournissent une explication à pourquoi une mise à jour ou l’échec de la suppression. Le modèle de mise à jour par lots, nous pouvons déterminer si Oui ou non une `DBConcurrencyException` exception s’est produite dans le Gestionnaire d’événements postérieurs au niveau du GridView affichant l’étiquette en fonction des besoins. Pour la méthode directe de base de données, nous pouvons examiner la valeur de retour de la méthode de la couche BLL (c'est-à-dire `true` si une ligne a été affectée, `false` sinon) et afficher un message d’information en fonction des besoins.

## <a name="step-6-adding-informational-messages-and-displaying-them-in-the-face-of-a-concurrency-violation"></a>Étape 6 : Ajout de Messages d’information et les afficher en cas de défaillance une Violation d’accès concurrentiel

Lorsqu’une violation d’accès concurrentiel se produit, le comportement présenté varie selon que la mise à jour du lot ou le modèle direct de base de données de la couche DAL a été utilisée. Notre didacticiel utilise les deux modèles, avec le modèle de mise à jour par lots utilisée pour le modèle direct de base de données permet de supprimer et de mise à jour. Pour commencer, vous allez ajouter deux contrôles Label à notre page expliquant qu’une violation d’accès concurrentiel s’est produite lors de la tentative de supprimer ou mettre à jour des données. Définir le contrôle d’étiquette `Visible` et `EnableViewState` propriétés `false`; cela entraîne leur être masqué sur chaque visite de la page, sauf pour ceux page particulière visite où leurs `Visible` propriété est définie par programme `true`.


[!code-aspx[Main](implementing-optimistic-concurrency-vb/samples/sample12.aspx)]

En plus du paramètre leurs `Visible`, `EnabledViewState`, et `Text` propriétés, j’ai également défini le `CssClass` propriété `Warning`, ce qui entraîne l’étiquette du à afficher dans une police de grande taille, rouge, italique, gras. CSS `Warning` classe a été défini et ajouté à Styles.css dans le *examiner les événements associés insertion, mise à jour et suppression* didacticiel.

Après avoir ajouté ces étiquettes, le concepteur dans Visual Studio doit ressembler à la Figure 18.


[![Deux contrôles Label qui ont été ajoutés à la Page](implementing-optimistic-concurrency-vb/_static/image51.png)](implementing-optimistic-concurrency-vb/_static/image50.png)

**La figure 18**: deux étiquette contrôles ont été ajoutés à la Page ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image52.png))


Avec ces contrôles Web Label en place, nous pouvons examiner comment déterminer quand une violation d’accès concurrentiel a eu lieu, à laquelle l’étiquette appropriée de point `Visible` propriété peut être définie `true`, affichant le message d’information.

## <a name="handling-concurrency-violations-when-updating"></a>La gestion des Violations d’accès simultané lors de la mise à jour

Examinons tout d’abord comment gérer les violations d’accès simultané lors de l’utilisation du modèle de mise à jour par lots. Étant donné que ces violations avec le traitement par lots Mettre à jour la cause de modèle un `DBConcurrencyException` exception levée, nous devons ajouter du code à notre page ASP.NET pour déterminer si un `DBConcurrencyException` exception s’est produite pendant le processus de mise à jour. Si par conséquent, nous devons afficher un message à l’utilisateur qui explique que leurs modifications n’ont pas été enregistrées, car un autre utilisateur a modifié des données entre lorsqu’ils ont démarré la modification de l’enregistrement et lorsqu’ils cliquent sur le bouton de mise à jour.

Comme nous l’avons vu dans la *BLL - gestion et des Exceptions au niveau de la couche DAL dans une Page ASP.NET* (didacticiel), ces exceptions peuvent être détectées et supprimées dans les gestionnaires d’événements postérieurs au niveau du contrôle Web de données. Par conséquent, nous devons créer un gestionnaire d’événements pour le contrôle du GridView `RowUpdated` événements vérifie si un `DBConcurrencyException` exception a été levée. Ce gestionnaire d’événements est passé à une référence à n’importe quelle exception a été levée pendant le processus de mise à jour, comme indiqué dans l’événement Gestionnaire de code ci-dessous :


[!code-vb[Main](implementing-optimistic-concurrency-vb/samples/sample13.vb)]

En face d’un `DBConcurrencyException` exception, ce gestionnaire d’événements affiche les `UpdateConflictMessage` Label, contrôle et indique que l’exception a été gérée. Avec ce code en place, lorsqu’une violation d’accès concurrentiel se produit lors de la mise à jour un enregistrement, les modifications de l’utilisateur sont perdues, car il seraient remplacé les modifications d’un autre utilisateur en même temps. En particulier, le contrôle GridView est rétabli à son état avant modification et lié à la base de données actuelle. Cela met à jour la ligne GridView avec les modifications de l’autre utilisateur, ont été précédemment non visibles. En outre, le `UpdateConflictMessage` contrôle Label explique à l’utilisateur que s’est-il passé uniquement. Cette séquence est détaillée dans la Figure 19.


[![Un utilisateur s mises à jour sont perdues dans l’image d’une Violation d’accès concurrentiel](implementing-optimistic-concurrency-vb/_static/image54.png)](implementing-optimistic-concurrency-vb/_static/image53.png)

**Figure 19**: un utilisateur s mises à jour sont perdues dans l’image d’une Violation d’accès concurrentiel ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image55.png))


> [!NOTE]
> Ou bien, plutôt que de retourner le contrôle GridView à l’état avant modification, nous pouvons conserver le contrôle GridView dans son état de modification en définissant le `KeepInEditMode` propriété du passé en `GridViewUpdatedEventArgs` objet à la valeur true. Si vous adoptez cette approche, toutefois, veillez à lier de nouveau les données au contrôle GridView (en appelant son `DataBind()` méthode) afin que les valeurs de l’autre utilisateur sont chargés dans l’interface de modification. Le code téléchargeable de ce didacticiel possède ces deux lignes de code dans le `RowUpdated` commenté de gestionnaire d’événements ; simplement ne pas commenter ces lignes de code pour que le contrôle GridView restent en mode édition après une violation d’accès concurrentiel.


## <a name="responding-to-concurrency-violations-when-deleting"></a>Répondre à des Violations d’accès concurrentiel lors de la suppression

Avec le modèle direct de base de données, il n’existe aucune exception levée en cas de défaillance une violation d’accès concurrentiel. Au lieu de cela, l’instruction de base de données n’affecte tout simplement aucun enregistrement, comme la clause WHERE ne correspond pas à un enregistrement. Toutes les méthodes de modification de données créés dans la couche BLL ont été conçus telles qu’elles retournent une valeur booléenne indiquant si elles affectées précisément un enregistrement. Par conséquent, pour déterminer si une violation d’accès concurrentiel s’est produite lors de la suppression d’un enregistrement, nous pouvons examiner la valeur de retour de la couche BLL `DeleteProduct` (méthode).

La valeur de retour pour une méthode de la couche BLL peut être examinée de gestionnaires d’événements postérieurs au niveau de l’ObjectDataSource via la `ReturnValue` propriété de la `ObjectDataSourceStatusEventArgs` objet passé dans le Gestionnaire d’événements. Étant donné que nous sommes intéressés par la détermination de la valeur de retour à partir de la `DeleteProduct` (méthode), nous devons créer un gestionnaire d’événements pour l’ObjectDataSource `Deleted` événement. Le `ReturnValue` propriété est de type `object` et peut être `null` si une exception a été levée et la méthode a été interrompue avant qu’il peut retourner une valeur. Par conséquent, nous devons d’abord vérifier que le `ReturnValue` propriété n’est pas `null` et est une valeur booléenne. En supposant que la réussite, nous affichons les `DeleteConflictMessage` Label, contrôle si le `ReturnValue` est `false`. Cela peut être accompli en utilisant le code suivant :


[!code-vb[Main](implementing-optimistic-concurrency-vb/samples/sample14.vb)]

En dépit d’une violation d’accès concurrentiel, une demande de suppression de l’utilisateur est annulée. Le contrôle GridView est actualisé, affichant les modifications qui se sont produites pour cet enregistrement entre le moment où l’utilisateur chargement de la page et quand il a cliqué sur le bouton de suppression. Lorsqu’une infraction apparaît, le `DeleteConflictMessage` étiquette ne s’affiche, expliquant que s’est-il passé juste la (voir Figure 20).


[![Un utilisateur s Delete est annulé en cas de défaillance une Violation d’accès concurrentiel](implementing-optimistic-concurrency-vb/_static/image57.png)](implementing-optimistic-concurrency-vb/_static/image56.png)

**Figure 20**: un utilisateur s Delete est annulée en cas de défaillance une Violation d’accès concurrentiel ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-vb/_static/image58.png))


## <a name="summary"></a>Résumé

Il existe des possibilités de violations d’accès concurrentiel dans chaque application qui permet à plusieurs utilisateurs simultanés pour mettre à jour ou supprimer des données. Si ces violations ne sont pas prises en compte pour, lorsque deux utilisateurs mettre à jour simultanément les mêmes données, la personne qui obtient de la dernière écriture « wins », remplacement de l’utilisateur modifie les modifications. Vous pouvez également, les développeurs peuvent implémenter un contrôle d’accès concurrentiel optimiste ou pessimiste. Contrôle d’accès concurrentiel optimiste part du principe que les violations d’accès concurrentiel sont peu fréquentes simplement n’autorise pas une mise à jour ou supprimer des commandes qui constitue une violation d’accès concurrentiel. Contrôle d’accès concurrentiel pessimiste suppose l’accès concurrentiel violations sont fréquentes et simplement rejet d’un utilisateur de mettre à jour ou de supprimer la commande n’est pas acceptable. Avec le contrôle d’accès concurrentiel pessimiste, mise à jour un enregistrement implique le verrouillage, ce qui évite que d’autres utilisateurs de modifier ou supprimer l’enregistrement lorsqu’il est verrouillé.

Le DataSet typé dans .NET fournit des fonctionnalités pour prendre en charge le contrôle d’accès concurrentiel optimiste. En particulier, la `UPDATE` et `DELETE` instructions envoyées à la base de données incluent toutes les colonnes de la table, ce qui garantit que la mise à jour ou suppression seulement se produira si les données actuelles de l’enregistrement correspondant à avec les données d’origine de l’utilisateur que lorsque exécution de leur mise à jour ou les supprimer. Une fois que la couche DAL a été configurée pour prendre en charge l’accès concurrentiel optimiste, les méthodes de la couche BLL doivent être mis à jour. En outre, la page ASP.NET qui effectue des appels vers le bas dans la couche BLL doit être configurée de sorte que ObjectDataSource récupère les valeurs d’origine à partir de son contrôle Web de données et les transfère vers le bas dans la couche BLL.

Comme nous l’avons vu dans ce didacticiel, l’implémentation de contrôle d’accès concurrentiel optimiste dans une application web ASP.NET implique la mise à jour de la couche DAL et la couche BLL et ajout de prise en charge dans la page ASP.NET. Indique si ce travail ajouté est un investissement judicieux de votre temps dépend de votre application. Si vous avez peu d’utilisateurs simultanés mise à jour des données, ou les données qu’ils mettent à jour sont différentes entre eux, contrôle d’accès concurrentiel n’est pas un problème de clé. Si, toutefois, vous devez régulièrement plusieurs utilisateurs sur votre site fonctionne avec les mêmes données, contrôle d’accès concurrentiel peut empêcher les mises à jour d’un utilisateur ou des suppressions d’involontairement en remplacement d’un autre.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](customizing-the-data-modification-interface-vb.md)
[Suivant](adding-client-side-confirmation-when-deleting-vb.md)
