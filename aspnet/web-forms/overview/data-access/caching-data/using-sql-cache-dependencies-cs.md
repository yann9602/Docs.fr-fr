---
uid: web-forms/overview/data-access/caching-data/using-sql-cache-dependencies-cs
title: "À l’aide de dépendances de Cache SQL (c#) | Documents Microsoft"
author: rick-anderson
description: "La stratégie de mise en cache la plus simple consiste à autoriser les données mises en cache expirent après une période spécifiée. Mais cette approche simple signifie que le données mises en cache maintai..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/30/2007
ms.topic: article
ms.assetid: 0e91842c-7f10-4aed-8c23-4ee3e2774014
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/caching-data/using-sql-cache-dependencies-cs
msc.type: authoredcontent
ms.openlocfilehash: a29c77688b0179730ccb1b48e62ae28a0148f94d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-sql-cache-dependencies-c"></a>À l’aide de dépendances de Cache SQL (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_61_CS.zip) ou [télécharger le PDF](using-sql-cache-dependencies-cs/_static/datatutorial61cs1.pdf)

> La stratégie de mise en cache la plus simple consiste à autoriser les données mises en cache expirent après une période spécifiée. Mais cette approche simple signifie que les données mises en cache ne conservent aucune association avec sa source de données sous-jacente, aboutissant à des données obsolètes sont conservées trop longtemps ou les données en cours sont arrivé à expiration trop tôt. Une meilleure approche consiste à utiliser la classe SqlCacheDependency afin que les données restent en mémoire cache jusqu'à ce que ses données sous-jacentes ont été modifiées dans la base de données SQL. Ce didacticiel vous montre comment.


## <a name="introduction"></a>Introduction

Les techniques de mise en cache est examiné dans le [mise en cache des données avec ObjectDataSource](caching-data-with-the-objectdatasource-cs.md) et [mise en cache des données dans l’Architecture](caching-data-in-the-architecture-cs.md) didacticiels utilisé une expiration basée sur une heure pour supprimer les données à partir du cache après avoir spécifié période. Cette approche est la façon la plus simple pour équilibrer les gains de performance de mise en cache contre l’obsolescence des données. En sélectionnant une expiration du temps de *x* secondes, un développeur de pages concedes pour profiter des avantages de performances de mise en cache uniquement pour *x* secondes, mais peut compter que les ses données ne seront jamais obsolètes dépasse le maximum de *x* secondes. Bien entendu, pour les données statiques, *x* peut être étendu à la durée de vie de l’application web, a été examinée dans le [mise en cache des données au démarrage de l’Application](caching-data-at-application-startup-cs.md) didacticiel.

Lors de la mise en cache de la base de données, une expiration basée sur le temps est généralement utilisée pour sa facilité d’utilisation, mais est généralement une solution inadéquate. Dans l’idéal, la base de données reste en mémoire cache jusqu'à ce que les données sous-jacentes ont été modifiées dans la base de données ; alors seulement est supprimé du cache. Cette approche optimise les avantages de performances de mise en cache et réduit la durée des données périmées. Toutefois, pour profiter de ces avantages il doit être un système en place qui sait lorsque la base de données sous-jacente a été modifiée et supprime les éléments correspondants à partir du cache. Avant d’ASP.NET 2.0, les développeurs de pages ont été chargés de l’implémentation de ce système.

ASP.NET 2.0 fournit un [ `SqlCacheDependency` classe](https://msdn.microsoft.com/library/system.web.caching.sqlcachedependency.aspx) et l’infrastructure nécessaire pour déterminer quand une modification s’est produite dans la base de données afin que les éléments correspondants mis en cache peut être supprimée. Il existe deux techniques pour déterminer quand les données sous-jacentes ont changé : notification et interrogation. Après avoir discuté les différences entre la notification et d’interrogation, nous allons créer l’infrastructure nécessaire pour prendre en charge l’interrogation et puis examiner comment utiliser la `SqlCacheDependency` classe déclarative et par programme des scénarios.

## <a name="understanding-notification-and-polling"></a>L’interrogation et la présentation des notifications

Il existe deux techniques qui peuvent être utilisés pour déterminer quand les données dans une base de données a été modifiées : notification et interrogation. Avec notification, la base de données des alertes automatiquement le runtime ASP.NET lorsque les résultats d’une requête particulière ont été modifiés depuis la dernière exécution de la requête, à partir de laquelle les éléments mis en cache associés à la requête sont supprimés. Avec l’interrogation, le serveur de base de données conserve les informations sur quand des tables dernier mis à jour. Le runtime ASP.NET interroge régulièrement la base de données pour vérifier que les tableaux ont changé dans la mesure où ils ont été entrés dans le cache. Les tables dont les données ont été modifiées ont leurs éléments de cache associé supprimés.

L’option de notification requiert moins d’installation que l’interrogation et est plus précise, car il suit les modifications apportées au niveau de la requête, plutôt qu’au niveau de la table. Malheureusement, les notifications sont uniquement disponibles dans les éditions complètes de Microsoft SQL Server 2005 (par exemple, les éditions non Express). Toutefois, l’option d’interrogation peut être utilisée pour toutes les versions de Microsoft SQL Server 7.0 pour 2005. Étant donné que ces didacticiels utilisent l’édition Express de SQL Server 2005, nous allons nous concentrer sur la configuration et à l’aide de l’option d’interrogation. Consultez la section davantage la lecture à la fin de ce didacticiel pour davantage de ressources sur les fonctionnalités de notification de SQL Server 2005 s.

Avec l’interrogation, la base de données doit être configuré pour inclure une table nommée `AspNet_SqlCacheTablesForChangeNotification` qui a trois colonnes - `tableName`, `notificationCreated`, et `changeId`. Cette table contient une ligne pour chaque table qui comporte des données qui doivent être utilisées dans une dépendance de cache SQL dans l’application web. Le `tableName` colonne indique le nom de la table lors de la `notificationCreated` indique les date et heure de la ligne a été ajoutée à la table. Le `changeId` colonne est de type `int` et possède une valeur initiale de 0. Sa valeur est incrémentée à chaque modification de la table.

Outre la `AspNet_SqlCacheTablesForChangeNotification` table, la base de données doit également inclure des déclencheurs sur chacune des tables qui peuvent apparaître dans une dépendance de cache SQL. Ces déclencheurs sont exécutés à chaque fois qu’une ligne est insérée, mise à jour ou supprimée et incrémenter la table s `changeId` valeur dans `AspNet_SqlCacheTablesForChangeNotification`.

Le runtime ASP.NET effectue le suivi actuel `changeId` pour une table lors de la mise en cache de données à l’aide un `SqlCacheDependency` objet. La base de données est régulièrement vérifiée et n’importe quel `SqlCacheDependency` objets dont `changeId` diffère de la valeur dans la base de données sont supprimés depuis une différentes `changeId` valeur indique qu’il a été une modification de la table, car les données a été mise en cache.

## <a name="step-1-exploring-theaspnetregsqlexecommand-line-program"></a>Étape 1 : Explorer le`aspnet_regsql.exe`programme de ligne de commande

Avec l’approche d’interrogation de la base de données doit être configuré pour contenir l’infrastructure décrite ci-dessus : une table prédéfinie (`AspNet_SqlCacheTablesForChangeNotification`), un certain nombre de procédures stockées et les déclencheurs sur chacune des tables qui peuvent être utilisées dans les dépendances de cache SQL dans le site web application. Ces tables, les procédures stockées et les déclencheurs peuvent être créés via le programme de ligne de commande `aspnet_regsql.exe`, qui se trouve dans le `$WINDOWS$\Microsoft.NET\Framework\version` dossier. Pour créer le `AspNet_SqlCacheTablesForChangeNotification` table et des procédures stockées associées, exécutez la commande suivante à partir de la ligne de commande :


[!code-console[Main](using-sql-cache-dependencies-cs/samples/sample1.cmd)]

> [!NOTE]
> Pour exécuter ces commandes de la connexion de base de données spécifiée doit être dans le [ `db_securityadmin` ](https://msdn.microsoft.com/library/ms188685.aspx) et [ `db_ddladmin` ](https://msdn.microsoft.com/library/ms190667.aspx) rôles. Pour examiner le T-SQL envoyé à la base de données par le `aspnet_regsql.exe` programme de ligne de commande, consultez [cette entrée de blog](http://scottonwriting.net/sowblog/posts/10709.aspx).


Par exemple, pour ajouter l’infrastructure pour l’interrogation à une base de données Microsoft SQL Server nommé `pubs` sur un serveur de base de données nommé `ScottsServer` à l’aide de l’authentification Windows, accédez au répertoire approprié et, à partir de la ligne de commande, entrez :


[!code-console[Main](using-sql-cache-dependencies-cs/samples/sample2.cmd)]

Après avoir ajouté l’infrastructure de niveau base de données, nous devons ajouter les déclencheurs aux tables qui seront utilisées dans les dépendances de cache SQL. Utiliser le `aspnet_regsql.exe` programme de ligne de commande, mais spécifier le nom de table à l’aide de la `-t` basculer et au lieu d’utiliser le `-ed` basculer l’utilisation `-et`, comme suit :


[!code-html[Main](using-sql-cache-dependencies-cs/samples/sample3.html)]

Pour ajouter les déclencheurs à la `authors` et `titles` tables sur le `pubs` sur la base de données `ScottsServer`, utilisez :


[!code-console[Main](using-sql-cache-dependencies-cs/samples/sample4.cmd)]

Pour ce didacticiel, ajouter les déclencheurs à la `Products`, `Categories`, et `Suppliers` tables. Nous allons nous intéresser à la syntaxe de ligne de commande particulier à l’étape 3.

## <a name="step-2-referencing-a-microsoft-sql-server-2005-express-edition-database-inappdata"></a>Étape 2 : Faisant référence à un Microsoft SQL Server 2005 Express Edition de base de données dans`App_Data`

Le `aspnet_regsql.exe` programme de ligne de commande nécessite le nom du serveur et de base de données afin d’ajouter l’infrastructure nécessaire d’interrogation. Mais ce qui est le nom du serveur et de base de données pour une base de données Microsoft SQL Server 2005 Express qui se trouve dans le `App_Data` dossier ? Au lieu de devoir découvrir quelles sont les noms de base de données et de serveur, je ve détecté que l’approche la plus simple pour attacher la base de données pour le `localhost\SQLExpress` instance de base de données et de renommer les données à l’aide [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Si vous disposez d’une des versions complètes de SQL Server 2005 est installé sur votre ordinateur, puis probablement avoir déjà installé sur votre ordinateur de SQL Server Management Studio. Si vous avez uniquement l’édition Express, vous pouvez télécharger gratuitement le [Microsoft SQL Server Management Studio Express Edition](https://www.microsoft.com/downloads/details.aspx?displaylang=en&amp;FamilyID=C243A5AE-4BD1-4E3D-94B8-5A0F62BF7796).

Commencez par fermer Visual Studio. Ensuite, ouvrez SQL Server Management Studio et choisissez se connecter à la `localhost\SQLExpress` serveur à l’aide de l’authentification Windows.


![Attacher à la localhost\SQLExpress serveur](using-sql-cache-dependencies-cs/_static/image1.gif)

**Figure 1**: attacher le `localhost\SQLExpress` Server


Après vous être connecté au serveur, Management Studio affiche le serveur et les sous-dossiers pour les bases de données, sécurité et ainsi de suite. Avec le bouton droit sur le dossier bases de données et choisissez l’option d’attachement. Cela affiche la boîte de dialogue Attacher les bases de données boîte (voir Figure 2). Cliquez sur le bouton Ajouter et sélectionnez le `NORTHWND.MDF` dossier de base de données dans votre s d’application web `App_Data` dossier.


[![Attacher le fichier NORTHWND. MDF de base de données à partir du dossier App_Data](using-sql-cache-dependencies-cs/_static/image2.gif)](using-sql-cache-dependencies-cs/_static/image1.png)

**Figure 2**: attacher le `NORTHWND.MDF` de la base de données à partir de la `App_Data` dossier ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image2.png))


Cette opération ajoute la base de données dans le dossier de bases de données. Le nom de la base de données peut être le chemin d’accès complet au fichier de base de données ou le chemin d’accès complet séparé par un [GUID](http://en.wikipedia.org/wiki/Globally_Unique_Identifier). Pour éviter d’avoir à taper ce nom long de la base de données lorsque vous utilisez le compte aspnet\_outil de ligne de commande regsql.exe, renommer un nom plus convivial en cliquant simplement sur la base de données à la base de données attaché et en choisissant le renommer. Je ve renommé DataTutorials ma base de données.


![Renommer la base de données attachée à un nom plus convivial](using-sql-cache-dependencies-cs/_static/image3.gif)

**Figure 3**: renommer la base de données attachée à un nom plus convivial


## <a name="step-3-adding-the-polling-infrastructure-to-the-northwind-database"></a>Étape 3 : Ajout de l’Infrastructure d’interrogation de la base de données Northwind

Maintenant que nous avons attaché le `NORTHWND.MDF` de la base de données à partir de la `App_Data` dossier, nous re prêt à ajouter l’infrastructure d’interrogation. En supposant que vous avez déjà renommé la base de données DataTutorials, exécutez les quatre commandes suivantes :


[!code-console[Main](using-sql-cache-dependencies-cs/samples/sample5.cmd)]

Après avoir exécuté ces quatre commandes, avec le bouton droit sur le nom de la base de données dans Management Studio, accédez au sous-menu tâches et choisissez de détachement. Fermez Management Studio, puis rouvrez Visual Studio.

Une fois que Visual Studio a été rouvert, explorez la base de données via l’Explorateur de serveurs. Notez la nouvelle table (`AspNet_SqlCacheTablesForChangeNotification`), les nouvelles procédures stockées et les déclencheurs sur la `Products`, `Categories`, et `Suppliers` tables.


![La base de données inclut désormais l’Infrastructure nécessaire d’interrogation](using-sql-cache-dependencies-cs/_static/image4.gif)

**Figure 4**: la base de données inclut désormais l’Infrastructure nécessaire d’interrogation


## <a name="step-4-configuring-the-polling-service"></a>Étape 4 : Configuration du Service d’interrogation

Après avoir créé les tables nécessaires, les déclencheurs et les procédures stockées dans la base de données, l’étape finale consiste à configurer le service d’interrogation, ce qui est effectué par le biais `Web.config` en spécifiant les bases de données à utiliser et la fréquence d’interrogation en millisecondes. Le balisage suivant interroge la base de données Northwind une fois par seconde.


[!code-xml[Main](using-sql-cache-dependencies-cs/samples/sample6.xml)]

Le `name` valeur dans le `<add>` élément (NorthwindDB) associe un nom explicite à une base de données. Lorsque vous travaillez avec des dépendances de cache SQL, nous devrons faire référence au nom de base de données défini ici, ainsi que la table basée sur les données mises en cache. Nous verrons comment utiliser la `SqlCacheDependency` classe à associer par programme les dépendances de cache SQL avec mise en cache des données à l’étape 6.

Une fois qu’une dépendance de cache SQL a été établie, le système d’interrogation se connecte aux bases de données définies dans le `<databases>` éléments chaque `pollTime` millisecondes et exécuter le `AspNet_SqlCachePollingStoredProcedure` procédure stockée. Cette procédure stockée - qui a été ajoutée de nouveau à l’aide de l’étape 3 du `aspnet_regsql.exe` outil de ligne de commande - renvoie la `tableName` et `changeId` valeurs pour chaque enregistrement dans `AspNet_SqlCacheTablesForChangeNotification`. Les dépendances de cache SQL obsolètes sont supprimés du cache.

Le `pollTime` paramètre introduit un compromis entre les performances et l’obsolescence des données. Un petit `pollTime` valeur augmente le nombre de demandes à la base de données, mais plus supprime rapidement les données obsolètes à partir du cache. Une valeur plus élevée `pollTime` valeur réduit le nombre de demandes de base de données, mais augmente le délai entre la modification des données de serveur principal et lorsque les éléments connexes du cache sont supprimés. Heureusement, la requête de base de données s’exécute une procédure stockée simple s retournant uniquement quelques lignes d’une table simple et léger. Mais faire des essais avec différents `pollTime` péremption access et les données de votre application de base de données pour trouver un équilibre idéal entre les valeurs. Le plus petit `pollTime` valeur autorisée est de 500.

> [!NOTE]
> L’exemple ci-dessus fournit un seul `pollTime` valeur dans le `<sqlCacheDependency>` l’élément, mais vous pouvez éventuellement spécifier le `pollTime` valeur dans le `<add>` élément. Cela est utile si vous disposez de plusieurs bases de données spécifiés et que vous souhaitez personnaliser la fréquence d’interrogation par base de données.


## <a name="step-5-declaratively-working-with-sql-cache-dependencies"></a>Étape 5 : Utilisation de façon déclarative avec les dépendances de Cache SQL

Les étapes 1 à 4, nous avons étudié comment le programme d’installation de l’infrastructure de base de données nécessaires et de configurer le système d’interrogation. Grâce à cette infrastructure en place, nous pouvons maintenant ajouter des éléments au cache de données avec une dépendance de cache SQL associée à l’aide de techniques de programmation ou déclaratifs. Dans cette étape, nous allons examiner l’utilisation de façon déclarative des dépendances de cache SQL. Dans l’étape 6, nous allons étudier l’approche par programmation.

Le [mise en cache des données avec ObjectDataSource](caching-data-with-the-objectdatasource-cs.md) didacticiel exploré les fonctionnalités de mise en cache déclaratives de ObjectDataSource. En définissant simplement le `EnableCaching` propriété `true` et le `CacheDuration` propriété à un intervalle de temps, ObjectDataSource mettra automatiquement en cache les données retournées à partir de son objet sous-jacent pour l’intervalle spécifié. ObjectDataSource permettre également utiliser une ou plusieurs dépendances de cache SQL.

Pour illustrer la façon déclarative à l’aide de dépendances de cache SQL, ouvrez le `SqlCacheDependencies.aspx` page dans le `Caching` faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur. Définir le contrôle GridView s `ID` à `ProductsDeclarative` et, à partir de sa balise active, choisissez de lier à un nouveau ObjectDataSource nommé `ProductsDataSourceDeclarative`.


[![Créer un nouveau ObjectDataSource nommé ProductsDataSourceDeclarative](using-sql-cache-dependencies-cs/_static/image5.gif)](using-sql-cache-dependencies-cs/_static/image3.png)

**Figure 5**: créer un nouveau nommé de ObjectDataSource `ProductsDataSourceDeclarative` ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image4.png))


Configurer l’ObjectDataSource à utiliser le `ProductsBLL` puis définissez la liste déroulante dans l’onglet Sélection à `GetProducts()`. Dans l’onglet mise à jour, choisissez le `UpdateProduct` surcharger avec trois paramètres d’entrée - `productName`, `unitPrice`, et `productID`. Définir les listes déroulantes (None) dans les onglets INSERT et DELETE.


[![Utilisez la surcharge de UpdateProduct avec trois paramètres d’entrée](using-sql-cache-dependencies-cs/_static/image6.gif)](using-sql-cache-dependencies-cs/_static/image5.png)

**Figure 6**: utiliser la surcharge UpdateProduct avec trois paramètres d’entrée ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image6.png))


[![Définition de la liste déroulante (aucun) à l’insertion et de supprimer les tabulations](using-sql-cache-dependencies-cs/_static/image7.gif)](using-sql-cache-dependencies-cs/_static/image7.png)

**Figure 7**: définir la liste déroulante (None) pour l’insérer et supprimer des onglets ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image8.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio créera BoundFields et CheckBoxFields dans le GridView pour chacun des champs de données. Supprimez tous les champs, mais `ProductName`, `CategoryName`, et `UnitPrice`et mettre en forme ces champs comme vous le souhaitez. À partir de la balise active de s GridView, activez les cases à cocher Activer la pagination, activer le tri et activer la modification. Visual Studio définit les opérations de mappage ObjectDataSource `OldValuesParameterFormatString` propriété `original_{0}`. Dans l’ordre pour la fonctionnalité d’édition GridView s fonctionne correctement, supprimez cette propriété entièrement de la syntaxe déclarative ou réaffectez-lui la valeur par défaut, `{0}`.

Enfin, ajoutez un contrôle Web Label ci-dessus le GridView et son `ID` propriété `ODSEvents` et son `EnableViewState` propriété `false`. Après avoir apporté ces modifications, votre balisage déclaratif s de page doit ressembler à ce qui suit. Notez que lorsque ve apportées à un certain nombre de personnalisations esthétiques aux champs GridView qui ne sont pas nécessaires pour illustrer la fonctionnalité de dépendance de cache SQL.


[!code-aspx[Main](using-sql-cache-dependencies-cs/samples/sample7.aspx)]

Ensuite, créez un gestionnaire d’événements pour ObjectDataSource s `Selecting` événement et dans le code suivant :


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample8.cs)]

N’oubliez pas que les opérations de mappage ObjectDataSource `Selecting` événement est déclenché uniquement lorsque la récupération des données à partir de son objet sous-jacent. Si l’ObjectDataSource accède aux données à partir de son propre cache, cet événement n’est pas déclenché.

Maintenant, visitez cette page via un navigateur. Dans la mesure où ve encore pour implémenter la mise en cache, chaque fois que la page, de trier ou de modifier la grille de la page doit afficher le texte, l’événement Selecting déclenché, comme le montre la Figure 8.


[![L’événement Selecting de s ObjectDataSource déclenche chaque fois que le contrôle GridView est paginé, modifié, ou le Sorted](using-sql-cache-dependencies-cs/_static/image8.gif)](using-sql-cache-dependencies-cs/_static/image9.png)

**Figure 8**: ObjectDataSource le s `Selecting` événement se déclenche à chaque fois le contrôle GridView est paginé, modifiée ou Sorted ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image10.png))


Comme nous l’avons vu dans la [mise en cache des données avec ObjectDataSource](caching-data-with-the-objectdatasource-cs.md) (didacticiel), définition de la `EnableCaching` propriété `true` provoque l’ObjectDataSource pour mettre en cache ses données pour la durée spécifiée par son `CacheDuration` propriété. ObjectDataSource possède également un [ `SqlCacheDependency` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.objectdatasource.sqlcachedependency.aspx), qui ajoute une ou plusieurs dépendances de cache SQL pour les données mises en cache à l’aide du modèle :


[!code-css[Main](using-sql-cache-dependencies-cs/samples/sample9.css)]

Où *databaseName* est le nom de la base de données comme spécifié dans le `name` attribut de la `<add>` élément `Web.config`, et *tableName* est le nom de la table de base de données. Par exemple, pour créer un ObjectDataSource qui met en cache les données indéfiniment basée sur une dépendance de cache SQL sur les opérations de mappage Northwind `Products` table, définissez le s ObjectDataSource `EnableCaching` propriété `true` et son `SqlCacheDependency` propriété NorthwindDB:Products.

> [!NOTE]
> Vous pouvez utiliser une dépendance de cache SQL *et* une expiration basée sur le temps en définissant `EnableCaching` à `true`, `CacheDuration` à l’intervalle de temps, et `SqlCacheDependency` pour l’ou les noms de base de données et la table. ObjectDataSource supprimez ses données lors de l’expiration de la durée est atteinte, ou lorsque le système d’interrogation de commentaires que la base de données sous-jacente a changé, selon ce qui se produit en premier.


Le contrôle GridView dans `SqlCacheDependencies.aspx` affiche des données provenant de deux tables - `Products` et `Categories` (le produit s `CategoryName` champ est récupéré un `JOIN` sur `Categories`). Par conséquent, nous voulons spécifier deux dépendances de cache SQL : NorthwindDB:Products ; NorthwindDB:Categories.


[![Configurer l’ObjectDataSource pour prendre en charge de la mise en cache à l’aide des dépendances de Cache SQL sur les produits et les catégories](using-sql-cache-dependencies-cs/_static/image9.gif)](using-sql-cache-dependencies-cs/_static/image11.png)

**Figure 9**: configurer l’ObjectDataSource pour la prise en charge la mise en cache à l’aide de dépendances de Cache SQL sur `Products` et `Categories` ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image12.png))


Après avoir configuré l’ObjectDataSource pour prendre en charge la mise en cache, revoir la page via un navigateur. Là encore, l’événement de sélection de texte déclenché doit apparaître sur la première visite de page, mais devrait disparaître lorsque la pagination, de tri ou en cliquant sur le bouton Modifier ou annuler. Il s’agit, car une fois que les données sont chargées dans le cache de s ObjectDataSource, il y reste jusqu'à ce que le `Products` ou `Categories` les tables sont modifiées ou les données sont mis à jour via le contrôle GridView.

Après la mise en page de la grille et en indiquant l’absence de l’événement Selecting déclenchement texte, ouvrez une nouvelle fenêtre de navigateur et accédez au didacticiel dans la modification, l’insertion et la suppression de section principes de base (`~/EditInsertDelete/Basics.aspx`). Cette mise à jour le nom ou le prix d’un produit. Puis, à partir de la première fenêtre de navigateur, afficher une autre page de données, trier la grille ou cliquez sur un bouton de modification de ligne s. Cette fois-ci, le déclenchement d’événement Selecting doit réapparaître, car la base de données sous-jacente données a été modifié (voir Figure 10). Si le texte n’apparaît pas, attendez quelques instants, puis réessayez. Souvenez-vous que le service d’interrogation recherche des modifications apportées à la `Products` table chaque `pollTime` millisecondes, par conséquent, il est un délai entre lorsque les données sous-jacentes sont mis à jour et lorsque les données mises en cache sont supprimées.


[![Modification de la Table produits supprime les données mises en cache de produit](using-sql-cache-dependencies-cs/_static/image10.gif)](using-sql-cache-dependencies-cs/_static/image13.png)

**La figure 10**: modification de la Table produits supprime les données mises en cache du produit ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image14.png))


## <a name="step-6-programmatically-working-with-thesqlcachedependencyclass"></a>Étape 6 : Manipuler par programme la`SqlCacheDependency`classe

Le [mise en cache des données dans l’Architecture](caching-data-in-the-architecture-cs.md) didacticiel examiné les avantages de l’utilisation d’une couche distincte de la mise en cache de l’architecture au lieu d’une association étroite entre la mise en cache avec ObjectDataSource. Dans ce didacticiel, nous avons créé un `ProductsCL` pour illustrer l’utilisation par programme avec le cache de données. Pour utiliser les dépendances de cache SQL dans la couche de mise en cache, utilisez la `SqlCacheDependency` classe.

Avec le système d’interrogation, un `SqlCacheDependency` objet doit être associé à une paire de base de données et de table particulier. Le code suivant, par exemple, crée un `SqlCacheDependency` objet basé sur la base de données Northwind s `Products` table :


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample10.cs)]

Les deux paramètres d’entrée au `SqlCacheDependency` constructeur de s sont les noms de base de données et de table, respectivement. Comme avec s ObjectDataSource `SqlCacheDependency` propriété, le nom de la base de données utilisé est identique à la valeur spécifiée dans le `name` attribut de la `<add>` élément `Web.config`. Le nom de la table est le nom réel de la table de base de données.

Pour associer un `SqlCacheDependency` avec un élément ajouté au cache de données, utilisez une de la `Insert` surcharges de méthode qui accepte une dépendance. Le code suivant ajoute *valeur* au cache de données pour une durée indéterminée, mais associe un `SqlCacheDependency` sur la `Products` table. En bref, *valeur* resteront dans le cache jusqu'à ce qu’il est supprimé en raison de contraintes de mémoire ou parce que le système d’interrogation a détecté que le `Products` table a été modifiée, car elle a été mise en cache.


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample11.cs)]

La mise en cache de couche s `ProductsCL` classe met actuellement en cache les données à partir de la `Products` de table à l’aide d’une expiration basée sur le temps de 60 secondes. Permettent de mettre à jour de cette classe afin qu’il utilise à la place des dépendances de cache SQL s. Le `ProductsCL` classe s `AddCacheItem` (méthode), qui est chargé d’ajouter les données dans le cache, contient actuellement le code suivant :


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample12.cs)]

Mettre à jour de ce code pour utiliser un `SqlCacheDependency` de l’objet au lieu du `MasterCacheKeyArray` la dépendance de cache :


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample13.cs)]

Pour tester cette fonctionnalité, ajoutez un contrôle GridView à la page sous existants `ProductsDeclarative` GridView. Définissez ce nouveau s de GridView `ID` à `ProductsProgrammatic` et via sa balise active, la lier à un nouveau ObjectDataSource nommé `ProductsDataSourceProgrammatic`. Configurer l’ObjectDataSource à utiliser le `ProductsCL` (classe), définir les listes déroulantes dans l’instruction SELECT et les onglets de mise à jour à `GetProducts` et `UpdateProduct`, respectivement.


[![Configurer pour utiliser la classe ProductsCL ObjectDataSource](using-sql-cache-dependencies-cs/_static/image11.gif)](using-sql-cache-dependencies-cs/_static/image15.png)

**Figure 11**: configurer l’ObjectDataSource à utiliser le `ProductsCL` classe ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image16.png))


[![Sélectionnez la méthode GetProducts dans la liste déroulante de s onglet Sélection](using-sql-cache-dependencies-cs/_static/image12.gif)](using-sql-cache-dependencies-cs/_static/image17.png)

**Figure 12**: sélectionnez le `GetProducts` méthode à partir de la liste déroulante, sélectionnez l’onglet s ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image18.png))


[![Choisissez la méthode UpdateProduct dans la liste déroulante de mise à jour onglet s](using-sql-cache-dependencies-cs/_static/image13.gif)](using-sql-cache-dependencies-cs/_static/image19.png)

**Figure 13**: choisissez la méthode UpdateProduct à partir de la liste déroulante de s onglet mise à jour ([cliquez pour afficher l’image en taille réelle](using-sql-cache-dependencies-cs/_static/image20.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio créera BoundFields et CheckBoxFields dans le GridView pour chacun des champs de données. Comme avec la première GridView ajouté à cette page, supprimez tous les champs, mais `ProductName`, `CategoryName`, et `UnitPrice`et mettre en forme ces champs comme vous le souhaitez. À partir de la balise active de s GridView, activez les cases à cocher Activer la pagination, activer le tri et activer la modification. Comme avec la `ProductsDataSourceDeclarative` ObjectDataSource, Visual Studio définit les `ProductsDataSourceProgrammatic` ObjectDataSource s `OldValuesParameterFormatString` propriété `original_{0}`. Dans l’ordre pour la fonctionnalité d’édition GridView s fonctionne correctement, définissez cette propriété passe au `{0}` (ou supprimez l’assignation de propriété à partir de la syntaxe déclarative entièrement).

Après avoir effectué ces tâches, le balisage déclaratif GridView et ObjectDataSource résultant doit se présenter comme suit :


[!code-aspx[Main](using-sql-cache-dependencies-cs/samples/sample14.aspx)]

Pour tester le code SQL dans la couche de mise en cache de la dépendance de cache définie un point d’arrêt dans le `ProductCL` classe s `AddCacheItem` méthode et puis démarrer le débogage. Lorsque vous visitez `SqlCacheDependencies.aspx`, le point d’arrêt doit être atteint pour que les données sont demandées pour la première fois et placées dans le cache. Ensuite, déplacer vers une autre page dans le GridView, ou l’une des colonnes de tri. Cela entraîne le contrôle GridView à actualiser ses données, mais les données doivent se trouver dans le cache depuis le `Products` table de base de données n’a pas été modifié. Si les données sont à plusieurs reprises introuvable dans le cache, assurez-vous que suffisamment de mémoire est disponible sur votre ordinateur, puis réessayez.

Après la pagination de quelques pages du contrôle GridView, ouvrez une deuxième fenêtre de navigateur et accédez au didacticiel dans la modification, l’insertion et la suppression de section principes de base (`~/EditInsertDelete/Basics.aspx`). Mettre à jour un enregistrement de la table Products et ensuite, affichez une nouvelle page à partir de la première fenêtre de navigateur ou cliquez sur l’un des en-têtes de tri.

Dans ce scénario vous verrez deux choses : le point d’arrêt est atteint, indiquant que les données mises en cache a été supprimées en raison de la modification de la base de données ; ou, le point d’arrêt n’est pas atteints, ce qui signifie que `SqlCacheDependencies.aspx` s’affiche maintenant les données périmées. Si le point d’arrêt n’est pas atteint, il est probable, car le service d’interrogation n’a pas encore supprimé, car les données ont été modifiées. Souvenez-vous que le service d’interrogation recherche des modifications apportées à la `Products` table chaque `pollTime` millisecondes, par conséquent, il est un délai entre lorsque les données sous-jacentes sont mis à jour et lorsque les données mises en cache sont supprimées.

> [!NOTE]
> Ce délai est plus susceptible d’apparaître lorsque vous modifiez un de ces produits via le contrôle GridView dans `SqlCacheDependencies.aspx`. Dans le [mise en cache des données dans l’Architecture](caching-data-in-the-architecture-cs.md) didacticiel, nous avons ajouté la `MasterCacheKeyArray` la dépendance pour vous assurer que les données en cours de modification par le biais de cache le `ProductsCL` classe s `UpdateProduct` méthode a été supprimée du cache. Toutefois, nous avons remplacé cette dépendance de cache lors de la modification la `AddCacheItem` méthode plus haut dans cette étape et par conséquent la `ProductsCL` classe continue d’afficher les données mises en cache jusqu'à ce que le système d’interrogation de notes de la modification apportée à la `Products` table. Nous verrons comment rétablir le `MasterCacheKeyArray` la dépendance à l’étape 7 de cache.


## <a name="step-7-associating-multiple-dependencies-with-a-cached-item"></a>Étape 7 : Associant plusieurs dépendances avec un élément mis en cache

N’oubliez pas que le `MasterCacheKeyArray` la dépendance de cache est utilisée pour garantir que *tous les* relatives au produit des données sont éliminées du cache lorsqu’un élément associé champdans est mis à jour. Par exemple, le `GetProductsByCategoryID(categoryID)` méthode caches `ProductsDataTables` instances pour chaque unique *categoryID* valeur. Si un de ces objets est supprimé, le `MasterCacheKeyArray` la dépendance de cache permet de s’assurer que les autres sont également supprimés. Sans cette dépendance de cache lorsque les données mises en cache sont modifiées. il est possible que d’autres données de produit de mise en cache peuvent être obsolètes. Par conséquent, il s importants que nous mettons en place la `MasterCacheKeyArray` dépendance de cache lors de l’utilisation des dépendances de cache SQL. Toutefois, les données mises en cache s `Insert` méthode permet uniquement d’un objet de dépendance unique.

En outre, lorsque vous travaillez avec des dépendances de cache SQL nous devons associer plusieurs tables de base de données en tant que dépendances. Par exemple, le `ProductsDataTable` mis en cache dans le `ProductsCL` classe contient les noms de catégorie et le fournisseur pour chaque produit, mais la `AddCacheItem` méthode utilise uniquement une dépendance sur `Products`. Dans ce cas, si l’utilisateur met à jour le nom d’une catégorie ou le fournisseur, les données de produit de mise en cache reste dans le cache et être obsolètes. Par conséquent, nous voulons que les données mises en cache de produit dépend non seulement le `Products` de table, mais sur le `Categories` et `Suppliers` également à des tables.

Le [ `AggregateCacheDependency` classe](https://msdn.microsoft.com/library/system.web.caching.aggregatecachedependency.aspx) fournit un moyen d’associer plusieurs dépendances avec un élément de cache. Commencez par créer un `AggregateCacheDependency` instance. Ensuite, ajoutez le jeu de dépendances à l’aide de la `AggregateCacheDependency` s `Add` (méthode). Lorsque vous insérez l’élément dans le cache de données par la suite, passez le `AggregateCacheDependency` instance. Lorsque *tout* de la `AggregateCacheDependency` modifier les dépendances de l’instance s, l’élément mis en cache est supprimé.

L’exemple suivant montre le code de mise à jour de la `ProductsCL` classe s `AddCacheItem` (méthode). La méthode crée le `MasterCacheKeyArray` cache dépendance avec `SqlCacheDependency` des objets pour le `Products`, `Categories`, et `Suppliers` tables. Ils sont combinés en un seul `AggregateCacheDependency` objet nommé `aggregateDependencies`, qui est ensuite passé à la `Insert` (méthode).


[!code-csharp[Main](using-sql-cache-dependencies-cs/samples/sample15.cs)]

Ce nouveau code de test. S’affiche désormais le `Products`, `Categories`, ou `Suppliers` tables provoquent les données mises en cache à supprimer. En outre, le `ProductsCL` classe s `UpdateProduct` (méthode), qui est appelée lors de la modification d’un produit par le biais du GridView, supprime la `MasterCacheKeyArray` la dépendance, ce qui entraîne la mise en cache de cache `ProductsDataTable` à supprimer et les données à récupérer de nouveau le prochain demande.

> [!NOTE]
> Dépendances de cache SQL peuvent également être utilisés avec [mise en cache de sortie](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/caching/output.aspx). Pour une démonstration de cette fonctionnalité, consultez : [à l’aide du cache de sortie ASP.NET avec SQL Server](https://msdn.microsoft.com/library/e3w8402y(VS.80).aspx).


## <a name="summary"></a>Récapitulatif

Lors de la mise en cache de la base de données, les données resteront dans l’idéal, dans le cache jusqu'à ce qu’il est modifié dans la base de données. Avec ASP.NET 2.0, les dépendances de cache SQL peuvent être créées et utilisées dans les scénarios déclaratives et par programme. L’une des difficultés avec cette approche est dans la découverte lorsque les données ont été modifiées. La version complète de Microsoft SQL Server 2005 fournit des fonctionnalités de notification qui peuvent avertir une application lorsqu’un résultat de requête a été modifiée. Pour l’édition Express de SQL Server 2005 et les versions antérieures de SQL Server, un système d’interrogation doit être utilisé à la place. Heureusement, la configuration de l’infrastructure nécessaire d’interrogation est assez simple.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [À l’aide de Notifications de requête dans Microsoft SQL Server 2005](https://msdn.microsoft.com/library/ms175110.aspx)
- [Création d’une Notification de requête](https://msdn.microsoft.com/library/ms188669.aspx)
- [Mise en cache dans ASP.NET avec la `SqlCacheDependency` classe](https://msdn.microsoft.com/library/ms178604(VS.80).aspx)
- [Outil d’inscription de serveur SQL d’ASP.NET (`aspnet_regsql.exe`)](https://msdn.microsoft.com/library/ms229862(vs.80).aspx)
- [Vue d’ensemble de`SqlCacheDependency`](http://www.aspnetresources.com/blog/sql_cache_depedency_overview.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Réviseurs tête pour ce didacticiel ont été Marko Rangel Teresa Murphy et Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](caching-data-at-application-startup-cs.md)
[Suivant](caching-data-with-the-objectdatasource-vb.md)
