---
uid: web-forms/overview/data-access/working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb
title: "Encapsule les Modifications de base de données dans une Transaction (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel est la première des quatre examine la mise à jour, suppression et l’insertion des lots de données. Dans ce didacticiel, nous savoir comment autoriser les transactions de base de données..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: 7d821db5-6cbb-4b38-af14-198f9155fc82
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/wrapping-database-modifications-within-a-transaction-vb
msc.type: authoredcontent
ms.openlocfilehash: 53887e2cf7b9a60596e2aca16fd1717799ab04f4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="wrapping-database-modifications-within-a-transaction-vb"></a>Modifications de base de données de renvoi à la ligne d’une Transaction (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_63_VB.zip) ou [télécharger le PDF](wrapping-database-modifications-within-a-transaction-vb/_static/datatutorial63vb1.pdf)

> Ce didacticiel est la première des quatre examine la mise à jour, suppression et l’insertion des lots de données. Ce didacticiel explique comment les transactions de base de données permettent les modifications de lot effectués comme une opération atomique, ce qui garantit que toutes les étapes réussissent ou échouent de toutes les étapes.


## <a name="introduction"></a>Introduction

Comme nous l’avons vu en commençant par le [une vue d’ensemble d’insertion, de mise à jour et de suppression des données](../editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb.md) (didacticiel), le contrôle GridView prend en charge la modification au niveau des lignes et suppression. En quelques clics de souris, il est possible de créer une interface de modification des données riches sans écrire une ligne de code, tant que vous êtes avec la modification et suppression sur une ligne par ligne. Toutefois, dans certains scénarios, cela est insuffisant, et nous avons besoin fournir aux utilisateurs la possibilité de modifier ou supprimer un lot d’enregistrements.

Par exemple, basée sur le web plus de clients de messagerie utilisent une grille pour répertorier chaque message dans lequel chaque ligne inclut une case à cocher et des informations de s messagerie (objet, expéditeur et ainsi de suite). Cette interface permet à l’utilisateur de supprimer plusieurs messages en les archivant et puis en cliquant sur un bouton Supprimer les Messages. Une interface de modification d’une sélection est idéale dans les situations où les utilisateurs modifier fréquemment nombre d’enregistrements. Au lieu d’obliger l’utilisateur à cliquer sur Modifier, apporter leur modification et puis cliquez sur mise à jour pour chaque enregistrement qui doit être modifiée, une interface de modification d’une sélection restitue chaque ligne avec son interface de modification. L’utilisateur peut modifier rapidement l’ensemble de lignes qui doivent être modifiées et puis enregistrer ces modifications en cliquant sur un bouton tout mettre à jour. Dans cet ensemble de didacticiels, nous allons examiner comment créer des interfaces pour l’insertion, la modification et la suppression des lots de données.

Lorsque vous effectuez des opérations par lots il important de déterminer si elle doit être possible de certaines opérations dans le lot réussissent alors que d’autres échouent. Envisagez d’un lot de suppression d’interface - ce qui doit se produire si le premier enregistrement sélectionné est supprimé avec succès, mais la deuxième échoue, par exemple, en raison d’une violation de contrainte de clé étrangère ? Doit d’abord le supprimer enregistrement s être annulée ou est acceptable pour le premier enregistrement de rester supprimée ?

Si vous souhaitez l’opération de traitement par lots est traitée comme une [opération atomique](http://en.wikipedia.org/wiki/Atomic_operation), un où soit toutes les étapes de réussissent ou échouent de toutes les étapes et doit être augmenté pour inclure la prise en charge de la couche d’accès aux données [base de données transactions](http://en.wikipedia.org/wiki/Database_transaction). Transactions de base de données garantissent l’atomicité pour l’ensemble de `INSERT`, `UPDATE`, et `DELETE` instructions exécutées dans le cadre de la transaction et sont une fonctionnalité prise en charge par la plupart des tous les systèmes de base de données modernes.

Dans ce didacticiel, nous allons examiner comment étendre la couche DAL pour utiliser des transactions de base de données. Les didacticiels suivants examine les pages web application pour le lot d’insertion, de mise à jour et de suppression des interfaces. Let s commencer !

> [!NOTE]
> Lors de la modification des données dans une transaction par lot, atomicité n’est pas toujours nécessaire. Dans certains scénarios, il peut être acceptable que certaines modifications de données réussisse, et d’autres dans le même lot échouent, par exemple lorsque la suppression d’un ensemble de messages électroniques à partir d’un client de messagerie électronique basé sur le web. Si le s’il n’y a un base de données erreur au milieu de la suppression de traitement, il s acceptable que ces enregistrements traités sans erreur restent supprimés. Dans ce cas, la couche Data Access n’a pas besoin être modifiés pour prendre en charge les transactions de base de données. Il existe d’autres lot opération scénarios, cependant, où l’atomicité est essentiel. Lorsqu’un client met son fonds d’un compte bancaire vers un autre, les deux opérations doivent être exécutées : les fonds doivent être déduits à partir du premier compte et ensuite ajoutées à la seconde. Bien que la banque ne peut pas peur la première étape réussir, mais la deuxième étape d’échouer, ses clients, serait énervés. Je vous encourage à utiliser dans ce didacticiel et implémenter les améliorations apportées à la couche DAL pour prendre en charge les transactions de base de données, même si vous n’envisagez pas de leur utilisation dans le lot d’insertion, mise à jour et suppression des interfaces que nous allons créer dans les didacticiels suivants de trois.


## <a name="an-overview-of-transactions"></a>Une vue d’ensemble des Transactions

La plupart des bases de données prennent en charge les *transactions*, qui permettent à plusieurs commandes de base de données d’être regroupés en une seule unité logique de travail. Sont garanti que les commandes de base de données qui composent une transaction atomique, ce qui signifie que toutes les commandes échouent ou réussissent toutes.

En règle générale, les transactions sont implémentées par le biais des instructions SQL à l’aide du modèle suivant :

1. Indiquer le début d’une transaction.
2. Exécutez les instructions SQL qui composent la transaction.
3. S’il existe une erreur dans une des instructions à l’étape 2, annuler la transaction.
4. Si toutes les instructions à l’étape 2 se termine sans erreur, validez la transaction.

Les instructions SQL utilisées pour créer, valider et restaurer la transaction peut être entrée manuellement lors de l’écriture de scripts SQL ou la création de procédures stockées ou par programmation au moyen d’ADO.NET ou les classes dans le [ `System.Transactions` espace de noms](https://msdn.microsoft.com/en-us/library/system.transactions.aspx). Dans ce didacticiel, nous allons examiner uniquement la gestion des transactions à l’aide d’ADO.NET. Dans un didacticiel futur, nous allons examiner comment utiliser des procédures stockées dans la couche d’accès aux données, à ce moment-là que nous allons explorer les instructions SQL pour la création, de restauration et de validation des transactions. En attendant, consultez [la gestion des Transactions dans les procédures stockées SQL Server](http://www.4guysfromrolla.com/webtech/080305-1.shtml) pour plus d’informations.

> [!NOTE]
> Le [ `TransactionScope` classe](https://msdn.microsoft.com/en-us/library/system.transactions.transactionscope.aspx) dans le `System.Transactions` espace de noms permet aux développeurs de par programmation encapsuler une série d’instructions dans l’étendue d’une transaction et inclut la prise en charge pour les transactions complexes qui impliquent plusieurs sources, telles que les deux bases de données différentes ou hétérogènes types de banques de données, comme une base de données Microsoft SQL Server, une base de données Oracle et un service Web. Je ve décidé d’utiliser des transactions ADO.NET pour ce didacticiel, au lieu du `TransactionScope` classe ADO.NET est plus spécifique pour les transactions de base de données et, dans de nombreux cas, étant donné que nécessite beaucoup moins de ressources. En outre, dans certains cas le `TransactionScope` classe utilise Microsoft Distributed Transaction Coordinator (MSDTC). Les problèmes de configuration, l’implémentation et performances MSDTC environnante rend une rubrique plutôt spécialisée et avancée et dépasse le cadre de ces didacticiels.


Lorsque vous travaillez avec le fournisseur SqlClient dans ADO.NET, les transactions sont lancées par un appel à la [ `SqlConnection` classe](https://msdn.microsoft.com/en-US/library/system.data.sqlclient.sqlconnection.aspx) s [ `BeginTransaction` méthode](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlconnection.begintransaction.aspx), qui retourne un [ `SqlTransaction` objet](https://msdn.microsoft.com/en-US/library/system.data.sqlclient.sqltransaction.aspx). Les instructions de modification de données la transaction de composition sont placés dans un `try...catch` bloc. Si une erreur se produit dans une instruction dans le `try` bloquer, les transferts de l’exécution à la `catch` bloc dans lequel la transaction peut être annulée via le `SqlTransaction` objet s [ `Rollback` (méthode)](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqltransaction.rollback.aspx). Si toutes les instructions terminent correctement, un appel à la `SqlTransaction` objet s [ `Commit` méthode](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqltransaction.commit.aspx) à la fin de la `try` bloc valide la transaction. L’extrait de code suivant illustre ce modèle. Consultez [maintenance de la cohérence de base de données avec des Transactions](http://aspnet.4guysfromrolla.com/articles/072705-1.aspx) de syntaxe supplémentaire et des exemples d’utilisation de transactions avec ADO.NET.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample1.vb)]

Par défaut, les TableAdapters dans un DataSet typé n’utilisez pas de transactions. Pour prendre en charge pour les transactions que nous avons besoin d’augmenter les classes TableAdapter pour inclure des méthodes supplémentaires qui utilisent le modèle ci-dessus pour effectuer une série d’instructions de modification de données dans l’étendue d’une transaction. À l’étape 2, nous verrons comment utiliser des classes partielles pour ajouter ces méthodes.

## <a name="step-1-creating-the-working-with-batched-data-web-pages"></a>Étape 1 : Création de la plage de travail avec des Pages Web de données par lot

Avant de commencer à Explorer la manière d’améliorer la couche DAL pour prendre en charge les transactions de base de données, permettent de s Prenez tout d’abord créer les pages web ASP.NET qui sera nécessaire pour ce didacticiel et les trois qui suivent. Commencez par ajouter un nouveau dossier nommé `BatchData` , puis ajoutez les pages ASP.NET suivantes, en associant chaque page avec le `Site.master` page maître.

- `Default.aspx`
- `Transactions.aspx`
- `BatchUpdate.aspx`
- `BatchDelete.aspx`
- `BatchInsert.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels de SqlDataSource](wrapping-database-modifications-within-a-transaction-vb/_static/image1.gif)

**Figure 1**: ajouter les Pages ASP.NET pour les didacticiels de SqlDataSource


Comme avec les autres dossiers, `Default.aspx` utilisera le `SectionLevelTutorialListing.ascx` contrôle utilisateur pour répertorier les didacticiels dans sa section. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur la page s mode Création.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](wrapping-database-modifications-within-a-transaction-vb/_static/image2.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image1.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image2.png))


Enfin, ajoutez ces quatre pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après la personnalisation sitemap `<siteMapNode>`:


[!code-xml[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample2.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments de la plage de travail dans les didacticiels de données par lot.


![Le plan de Site inclut maintenant des entrées pour la plage de travail dans les didacticiels de données par lot](wrapping-database-modifications-within-a-transaction-vb/_static/image3.gif)

**Figure 3**: le plan de Site inclut maintenant des entrées pour la plage de travail dans les didacticiels de données par lot


## <a name="step-2-updating-the-data-access-layer-to-support-database-transactions"></a>Étape 2 : Mise à jour de la couche d’accès aux données pour prendre en charge les Transactions de base de données

Comme expliqué dans le premier didacticiel [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md), le DataSet typé dans notre DAL se compose de tables de données et les TableAdapters. Les tables de données contiennent des données tandis que les TableAdapters fournissent les fonctionnalités permettant de lire les données à partir de la base de données dans les tables de données, mettre à jour la base de données avec les modifications apportées aux tables de données et ainsi de suite. Rappelez-vous que les TableAdapters fournissent deux modèles de mise à jour des données, j’ai appelé mise à jour par lots et Direct à la base de données. Avec le modèle de mise à jour par lots, le TableAdapter est passé d’un DataSet, DataTable ou collection de DataRows. Ces données est énumérées et pour chaque inséré, modifié ou supprimé des lignes, le `InsertCommand`, `UpdateCommand`, ou `DeleteCommand` est exécutée. Avec le modèle de base de données en Direct, le TableAdapter est passé à la place des valeurs des colonnes nécessaires pour l’insertion, la mise à jour ou suppression d’un enregistrement unique. La méthode Direct de base de données utilise ensuite ces valeurs dans le passé pour exécuter la commande appropriée `InsertCommand`, `UpdateCommand`, ou `DeleteCommand` instruction.

Quel que soit le modèle de mise à jour utilisé, les méthodes générées automatiquement des TableAdapters n’utilisent pas de transactions. Par défaut, chaque insert, update ou delete exécutée par le TableAdapter est traité comme une seule opération discrète. Par exemple, imaginez que le modèle de base de données en Direct est utilisé par du code dans la couche BLL pour insérer des dix enregistrements dans la base de données. Ce code appelle le TableAdapter s `Insert` méthode dix fois. Si les cinq premiers insertions réussissent, mais celui sixième a entraîné une exception, les cinq premiers enregistrements insérés reste dans la base de données. De même, si le modèle de mise à jour par lots est utilisé pour effectuer des insertions, mises à jour et des suppressions inséré, modifié et supprimé des lignes dans un DataTable, si les modifications de plusieurs première a réussi, mais une version plus récente a rencontré une erreur, les modifications antérieures qui terminée reste dans la base de données.

Dans certains scénarios, vous souhaitez garantir l’atomicité sur une série de modifications. Pour cela nous devons étendre manuellement le TableAdapter en ajoutant de nouvelles méthodes qui s’exécutent la `InsertCommand`, `UpdateCommand`, et `DeleteCommand` s dans le cadre d’une transaction. Dans [création d’une couche d’accès aux données](../introduction/creating-a-data-access-layer-vb.md) , nous avons étudié à l’aide de [classes partielles](http://en.wikipedia.org/wiki/Partial_type) pour étendre les fonctionnalités des tables de données dans le DataSet typé. Cette technique peut également être utilisée avec des TableAdapters.

Le DataSet typé `Northwind.xsd` se trouve dans le `App_Code` dossier s `DAL` sous-dossier. Créer un sous-dossier dans le `DAL` dossier nommé `TransactionSupport` et ajoutez un nouveau fichier de classe nommé `ProductsTableAdapter.TransactionSupport.vb` (voir Figure 4). Ce fichier contiendra l’implémentation partielle de la `ProductsTableAdapter` qui inclut des méthodes permettant d’effectuer des modifications de données à l’aide d’une transaction.


![Ajouter un dossier nommé TransactionSupport et un fichier de classe nommé ProductsTableAdapter.TransactionSupport.vb](wrapping-database-modifications-within-a-transaction-vb/_static/image4.gif)

**Figure 4**: ajouter un dossier nommé `TransactionSupport` et un fichier de classe nommé`ProductsTableAdapter.TransactionSupport.vb`


Entrez le code suivant dans le `ProductsTableAdapter.TransactionSupport.vb` fichier :


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample3.vb)]

Le `Partial` mot clé dans la déclaration de classe ici indique au compilateur que les membres ajoutés doivent être ajoutés à la `ProductsTableAdapter` classe dans le `NorthwindTableAdapters` espace de noms. Remarque la `Imports System.Data.SqlClient` instruction en haut du fichier. Puisque le TableAdapter a été configuré pour utiliser le fournisseur SqlClient, en interne il utilise un [ `SqlDataAdapter` ](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqldataadapter.aspx) objet pour émettre ses commandes à la base de données. Par conséquent, nous devons utiliser la `SqlTransaction` classe pour commencer la transaction, puis de valider ou restaurer. Si vous utilisez un magasin de données autre que Microsoft SQL Server, vous devez utiliser le fournisseur approprié.

Ces méthodes fournissent les blocs de construction nécessaires au démarrage, de restauration et de valider une transaction. Ils sont marqués `Public`, ce qui leur permet d’être utilisé à partir du `ProductsTableAdapter`, à partir d’une autre classe dans la couche DAL, ou à partir d’une autre couche de l’architecture, telles que la couche BLL. `BeginTransaction`Ouvre le TableAdapter interne `SqlConnection` (si nécessaire), démarre la transaction et l’affecte à la `Transaction` propriété et joint la transaction au interne `SqlDataAdapter` s `SqlCommand` objets. `CommitTransaction`et `RollbackTransaction` appeler le `Transaction` objet s `Commit` et `Rollback` méthodes, respectivement, avant de fermer interne `Connection` objet.

## <a name="step-3-adding-methods-to-update-and-delete-data-under-the-umbrella-of-a-transaction"></a>Étape 3 : Ajout de méthodes pour mettre à jour et supprimer des données dans le cadre d’une Transaction

Avec ces méthodes terminées, nous re prêt pour ajouter des méthodes à `ProductsDataTable` ou la couche BLL qui effectuent une série de commandes dans le cadre d’une transaction. La méthode suivante utilise le modèle de mise à jour par lot pour mettre à jour un `ProductsDataTable` instance à l’aide d’une transaction. Il démarre une transaction en appelant le `BeginTransaction` (méthode), puis utilise une `Try...Catch` bloc pour émettre les instructions de modification de données. Si l’appel à la `Adapter` objet s `Update` méthode entraîne une exception, l’exécution est transférés vers le `catch` bloc où la transaction sera restaurée et l’exception levée à nouveau. N’oubliez pas que le `Update` méthode implémente le modèle de mise à jour par lots en énumérant les lignes de l’élément `ProductsDataTable` et d’exécution nécessaires `InsertCommand`, `UpdateCommand`, et `DeleteCommand` s. Si l’une de ces commandes une erreur, la transaction est restaurée, annuler les modifications précédentes effectuées pendant la durée de vie de s de transaction. Doit la `Update` instruction se termine sans erreur, la transaction est validée dans son intégralité.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample4.vb)]

Ajouter le `UpdateWithTransaction` méthode à la `ProductsTableAdapter` classe via la classe partielle dans `ProductsTableAdapter.TransactionSupport.vb`. Sinon, cette méthode peut être ajoutée à la couche de logique métier s `ProductsBLL` classe avec quelques modifications syntaxiques mineures. À savoir, le mot clé `Me` dans `Me.BeginTransaction()`, `Me.CommitTransaction()`, et `Me.RollbackTransaction()` devra être remplacé par `Adapter` (n’oubliez pas que `Adapter` est le nom d’une propriété dans `ProductsBLL` de type `ProductsTableAdapter`).

Le `UpdateWithTransaction` méthode utilise le modèle de mise à jour par lots, mais une série d’appels de base de données en Direct peut également être utilisée dans l’étendue d’une transaction, comme dans l’exemple de méthode suivant. Le `DeleteProductsWithTransaction` méthode accepte comme entrée un `List(Of T)` de type `Integer`, qui sont le `ProductID` s à supprimer. La méthode lance la transaction via un appel à `BeginTransaction` , puis, dans le `Try` de bloc, effectue une itération dans la liste fournie, le modèle de base de données en Direct de l’appel `Delete` méthode pour chaque `ProductID` valeur. Si un des appels à `Delete` échoue, le contrôle est transféré vers le `Catch` bloc dans lequel la transaction est annulée et l’exception levée à nouveau. Si tous les appels à `Delete` réussisse, puis de la transaction est validée. Ajoutez cette méthode à la `ProductsBLL` classe.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample5.vb)]

## <a name="applying-transactions-across-multiple-tableadapters"></a>Application de Transactions entre plusieurs TableAdapters

Le code lié à la transaction examiné dans ce didacticiel permet pour plusieurs instructions par rapport à la `ProductsTableAdapter` est traitée comme une opération atomique. Mais que se passe-t-il si plusieurs modifications aux tables de base de données différente doivent être effectuées de manière atomique ? Par exemple, lorsque vous supprimez une catégorie, nous pouvons tout d’abord souhaitez réaffecter ses produits en cours à une autre catégorie. Ces deux étapes, en réaffectant les produits et de suppression de la catégorie doivent être exécutés comme une opération atomique. Mais la `ProductsTableAdapter` inclut uniquement les méthodes permettant de modifier le `Products` table et la `CategoriesTableAdapter` inclut uniquement les méthodes de modification le `Categories` table. Comment une transaction peut englober les TableAdapters ?

Une option consiste à ajouter une méthode à la `CategoriesTableAdapter` nommé `DeleteCategoryAndReassignProducts(categoryIDtoDelete, reassignToCategoryID)` et faites en sorte que cette méthode appelle une procédure stockée qui supprime la catégorie dans l’étendue d’une transaction définie dans la procédure stockée et réaffecte les produits. Nous allons examiner comment commencer, valider et annuler les transactions dans les procédures stockées dans un didacticiel futures.

Une autre option consiste à créer une classe d’assistance dans la couche DAL qui contient le `DeleteCategoryAndReassignProducts(categoryIDtoDelete, reassignToCategoryID)` (méthode). Cette méthode créerait une instance de la `CategoriesTableAdapter` et `ProductsTableAdapter` et définir ces deux TableAdapters `Connection` propriétés à la même `SqlConnection` instance. À ce stade, l’une des deux TableAdapters lancer la transaction avec un appel à `BeginTransaction`. Les méthodes des TableAdapters pour réaffecter les produits et la suppression de la catégorie sont appelés dans un `Try...Catch` bloc avec la transaction validée ou annulée précédent en fonction des besoins.

## <a name="step-4-adding-theupdatewithtransactionmethod-to-the-business-logic-layer"></a>Étape 4 : Ajout de la`UpdateWithTransaction`à la couche de logique métier (méthode)

À l’étape 3, nous avons ajouté une `UpdateWithTransaction` méthode à la `ProductsTableAdapter` dans la couche DAL. Nous devons ajouter une méthode correspondante à la couche BLL. Même si la couche de présentation peut appeler directement à la couche DAL pour appeler le `UpdateWithTransaction` (méthode), ces didacticiels entièrement définir une architecture multiniveau qui isole la couche DAL à partir de la couche de présentation. Par conséquent, il nous pour continuer cette approche appartient.

Ouvrez le `ProductsBLL` fichier de classe et ajoutez une méthode nommée `UpdateWithTransaction` qui appelle simplement à la méthode correspondante de la couche DAL. Il convient maintenant deux nouvelles méthodes de `ProductsBLL`: `UpdateWithTransaction`, ce qui vous venez d’ajouter, et `DeleteProductsWithTransaction`, qui a été ajouté à l’étape 3.


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample6.vb)]

> [!NOTE]
> Ces méthodes n’incluent pas la `DataObjectMethodAttribute` attribut assigné à la plupart des autres méthodes dans le `ProductsBLL` , car nous allons appeler ces méthodes directement à partir des classes ASP.NET pages code-behind de la classe. N’oubliez pas que `DataObjectMethodAttribute` est utilisé pour marquer les méthodes qui doivent apparaître dans le s ObjectDataSource configurer la Source de données Assistant et sous l’onglet (SELECT, UPDATE, INSERT ou DELETE). Étant donné que le contrôle GridView ne dispose pas de toute prise en charge intégrée pour la modification ou la suppression de lot, nous allons devoir appeler ces méthodes par programmation au lieu d’utiliser l’approche sans code déclaratif.


## <a name="step-5-atomically-updating-database-data-from-the-presentation-layer"></a>Étape 5 : Mise à jour de manière atomique des données de base de données à partir de la couche de présentation

Pour illustrer les effets de la transaction lors de la mise à jour d’un lot d’enregistrements, s permettent de créer une interface utilisateur qui répertorie tous les produits dans un GridView et inclut un bouton de serveur Web de contrôle qui, lorsque vous cliquez dessus, réaffecte les produits `CategoryID` valeurs. En particulier, de progression de la réaffectation de catégorie afin que la première série de produits est assignées valide `CategoryID` valeur alors que d’autres sont volontairement attribué un inexistant `CategoryID` valeur. Si nous tentons de mettre à jour de la base de données avec un produit dont `CategoryID` ne correspond pas à une catégorie existante s `CategoryID`, se produit une violation de contrainte de clé étrangère et une exception sera levée. Nous verrons dans cet exemple est que, lorsque vous à l’aide d’une transaction, l’exception levée à partir de la violation de contrainte de clé étrangère entraînera la précédente valide `CategoryID` modifications être annulées. Lorsque vous N'utilisez pas une transaction, toutefois, les modifications aux catégories initiales reste.

Commencez par ouvrir le `Transactions.aspx` page dans le `BatchData` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur. Définir son `ID` à `Products` et, à partir de sa balise active, la lier à un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource à extraire ses données à partir de la `ProductsBLL` classe s `GetProducts` (méthode). Ceci est un GridView en lecture seule, par conséquent, définie les listes déroulantes dans la mise à jour, insérer et supprimer des onglets (aucun) et cliquez sur Terminer.


[![Configurer pour utiliser la méthode GetProducts ProductsBLL classe s ObjectDataSource](wrapping-database-modifications-within-a-transaction-vb/_static/image5.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image3.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe s `GetProducts` (méthode) ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image4.png))


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](wrapping-database-modifications-within-a-transaction-vb/_static/image6.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image5.png)

**Figure 6**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image6.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio créera BoundFields et un CheckBoxField pour les champs de données de produit. Supprimez tous ces champs à l’exception de `ProductID`, `ProductName`, `CategoryID`, et `CategoryName` et renommer le `ProductName` et `CategoryName` BoundFields `HeaderText` propriétés produit et de catégorie, respectivement. À partir de la balise active, activez l’option Activer la pagination. Après avoir apporté ces modifications, le balisage déclaratif s GridView et ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample7.aspx)]

Ensuite, ajoutez trois contrôles de bouton Web ci-dessus le GridView. Définir le premier bouton s propriété Text pour actualiser la grille, le deuxième s afin de modifier les catégories (avec TRANSACTION) et le troisième un s pour modifier les catégories (sans TRANSACTION).


[!code-aspx[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample8.aspx)]

À ce stade la vue de conception dans Visual Studio doit ressembler à l’écran : WordGame indiqué dans la Figure 7.


[![La Page contient un GridView et trois contrôles Web](wrapping-database-modifications-within-a-transaction-vb/_static/image7.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image7.png)

**Figure 7**: la Page contient un GridView et les trois contrôles Web ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image8.png))


Créer des gestionnaires d’événements pour chaque du bouton trois s `Click` événements et utiliser le code suivant :


[!code-vb[Main](wrapping-database-modifications-within-a-transaction-vb/samples/sample9.vb)]

L’actualisation du bouton s `Click` Gestionnaire d’événements relie simplement les données au contrôle GridView en appelant le `Products` GridView s `DataBind` (méthode).

Le second gestionnaire d’événements réaffecte les produits `CategoryID` s et utilise la nouvelle méthode de transaction à partir de la couche BLL pour effectuer la base de données met à jour dans le cadre d’une transaction. Notez que chaque produit s `CategoryID` est arbitrairement définie sur la même valeur que sa `ProductID`. Cela fonctionne correctement pour la première certains produits, étant donné que ces produits `ProductID` les valeurs qui se produisent à mapper à valide `CategoryID` s. Mais une fois la `ProductID` début s devient trop volumineux, ce chevauchement une coïncidence de `ProductID` s et `CategoryID` s ne s’applique plus.

La troisième `Click` Gestionnaire d’événements met à jour les produits `CategoryID` s de la même manière, mais envoie la mise à jour la base de données à l’aide de la `ProductsTableAdapter` par défaut de s `Update` (méthode). Cela `Update` n’encapsule pas la série de commandes dans une transaction d’une méthode, afin de ces modifications sont apportées avant la première erreur de violation de contrainte de clé étrangère a été rencontrée seront conservés.

Pour illustrer ce comportement, visitez cette page via un navigateur. Au départ, vous devez voir la première page de données comme indiqué dans la Figure 8. Ensuite, cliquez sur le bouton Modifier les catégories (avec TRANSACTION). Cela provoque une publication (postback) et tentez de mettre à jour tous les produits `CategoryID` des valeurs, mais entraîne une violation de contrainte de clé étrangère (voir la Figure 9).


[![Les produits sont affichés dans un GridView paginable](wrapping-database-modifications-within-a-transaction-vb/_static/image8.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image9.png)

**Figure 8**: les produits sont affichés dans un GridView paginable ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image10.png))


[![Réaffecter les résultats de catégories une violation de contrainte de clé étrangère](wrapping-database-modifications-within-a-transaction-vb/_static/image9.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image11.png)

**Figure 9**: en réaffectant les résultats de catégories dans une Violation de contrainte de clé étrangère ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image12.png))


Maintenant atteint votre bouton précédent de navigateur s et puis cliquez sur le bouton Actualiser la grille. Lors de l’actualisation des données, vous devez voir le même résultat exactement comme indiqué dans la Figure 8. Autrement dit, même si certains produits `CategoryID` s ont les valeurs modifiées pour juridique et mis à jour dans la base de données, ils ont été restaurées lors de la violation de contrainte de clé étrangère s’est produite.

Essayez maintenant en cliquant sur le bouton Modifier les catégories (sans TRANSACTION). Cela entraîne la même erreur de violation de contrainte de clé étrangère (voir Figure 9), mais cette fois, les produits dont `CategoryID` valeurs ont été modifiées pour une juridiques valeur ne sera pas restaurée. Appuyez sur le bouton précédent de navigateur s, puis sur le bouton Actualiser la grille. Comme le montre la Figure 10, le `CategoryID` s des huit premiers produits ont été réassigné. Par exemple, dans la Figure 8, suivi modifications avaient un `CategoryID` 1, mais dans la Figure 10 informatique s réaffectée à 2.


[![Certaines valeurs de CategoryID produits ont été mis à jour tandis que d’autres ont été pas](wrapping-database-modifications-within-a-transaction-vb/_static/image10.gif)](wrapping-database-modifications-within-a-transaction-vb/_static/image13.png)

**La figure 10**: certains produits `CategoryID` valeurs ont été mis à jour tandis que d’autres ont été pas ([cliquez pour afficher l’image en taille réelle](wrapping-database-modifications-within-a-transaction-vb/_static/image14.png))


## <a name="summary"></a>Résumé

Par défaut, les méthodes de s TableAdapter n’imbriquez pas les instructions de la base de données exécutée dans l’étendue d’une transaction, mais avec un peu de travail, nous pouvons ajouter les méthodes que vous allez créer, commit et rollback une transaction. Dans ce didacticiel, nous avons créé trois de ces méthodes dans les `ProductsTableAdapter` classe : `BeginTransaction`, `CommitTransaction`, et `RollbackTransaction`. Nous avons vu comment utiliser ces méthodes avec un `Try...Catch` bloc pour effectuer une série d’instructions de modification de données atomique. En particulier, nous avons créé la `UpdateWithTransaction` méthode dans le `ProductsTableAdapter`, qui utilise le modèle de mise à jour par lots pour effectuer les modifications nécessaires aux lignes d’une `ProductsDataTable`. Nous avons également ajouté la `DeleteProductsWithTransaction` méthode à la `ProductsBLL` classe dans la couche BLL, qui accepte un `List` de `ProductID` les valeurs en entrée et appelle la méthode de modèle Direct à la base de données `Delete` pour chaque `ProductID`. Les deux méthodes démarrer en créant une transaction et puis d’exécuter les instructions de modification de données dans un `Try...Catch` bloc. Si une exception se produit, la transaction est restaurée, dans le cas contraire, il est validé.

Étape 5 illustre l’effet des mises à jour de lot transactionnel et mises à jour par lots a négligé d’utiliser une transaction. Dans les trois didacticiels nous reposent sur la base de ce didacticiel et créer des interfaces utilisateur pour effectuer des insertions, suppressions et mises à jour par lots.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Maintenir la cohérence de base de données avec des Transactions](http://aspnet.4guysfromrolla.com/articles/072705-1.aspx)
- [Procédures stockées de gestion des Transactions dans SQL Server](http://www.4guysfromrolla.com/webtech/080305-1.shtml)
- [Transactions effectuées simples :`System.Transactions`](https://blogs.msdn.com/florinlazar/archive/2004/07/23/192239.aspx)
- [TransactionScope et DataAdapters](http://andyclymer.blogspot.com/2007/01/transactionscope-and-dataadapters.html)
- [À l’aide de Transactions de bases de données Oracle dans .NET](http://www.oracle.com/technology/pub/articles/price_dbtrans_dotnet.html)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Dave Gardner, Hilton Giesenow et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](batch-inserting-cs.md)
[Suivant](batch-updating-vb.md)
