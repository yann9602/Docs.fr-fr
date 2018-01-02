---
uid: web-forms/overview/data-access/paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs
title: "Efficacement la pagination des grandes quantités de données (c#) | Documents Microsoft"
author: rick-anderson
description: "L’option de pagination par défaut une présentation du contrôle de données est inappropriée lorsque vous travaillez avec grandes quantités de données, comme son retriev de contrôle de source données sous-jacent..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/15/2006
ms.topic: article
ms.assetid: 59c01998-9326-4ecb-9392-cb9615962140
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs
msc.type: authoredcontent
ms.openlocfilehash: 5188c7119260e36eb61e9f57cadce29fefdd8016
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="efficiently-paging-through-large-amounts-of-data-c"></a>Efficacement la pagination des grandes quantités de données (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_25_CS.exe) ou [télécharger le PDF](efficiently-paging-through-large-amounts-of-data-cs/_static/datatutorial25cs1.pdf)

> L’option de pagination par défaut d’un contrôle de présentation des données ne convient pas lorsque vous travaillez avec grandes quantités de données, comme son contrôle de source de données sous-jacente récupère tous les enregistrements, bien que seul un sous-ensemble des données s’affiche. Dans ce cas, nous devons activer personnalisés à la pagination.


## <a name="introduction"></a>Introduction

Comme expliqué dans le didacticiel précédent, la pagination peut être implémentée de deux manières :

- **Pagination par défaut** peut être implémentée en simplement en activant l’option Activer la pagination dans les données de contrôle de Web s des balises actives ; Toutefois, chaque fois que l’affichage d’une page de données, ObjectDataSource extrait *tous les* des enregistrements, même Bien qu’uniquement un sous-ensemble d'entre eux sont affichés dans la page
- **La pagination personnalisée** améliore les performances de la valeur par défaut d’échange en récupérant uniquement les enregistrements à partir de la base de données qui doivent être affichés pour la page de données demandée par l’utilisateur ; Toutefois, la pagination personnalisée implique un peu plus d’effort pour implémenter que la pagination de la valeur par défaut

En raison de la facilité d’implémentation de vérification simplement une case à cocher et vous re terminé ! pagination par défaut est une option intéressante. Son approche de la ve na lors de la récupération de tous les enregistrements, cependant, rend un choix possible lors de la pagination via suffisamment grandes quantités de données ou pour les sites comportant de nombreux utilisateurs simultanés. Dans ce cas, nous devons activer la pagination afin de fournir un système réactif personnalisées.

La vérification de la pagination personnalisée est la possibilité d’écrire une requête qui retourne l’ensemble précis des enregistrements nécessaires pour une page spécifique de données. Heureusement, Microsoft SQL Server 2005 fournit un nouveau mot clé pour le classement des résultats, ce qui nous permet d’écrire une requête qui peut récupérer le sous-ensemble correct d’enregistrements. Dans ce didacticiel, nous verrons comment utiliser ce nouveau mot clé de SQL Server 2005 pour implémenter la pagination personnalisée dans un contrôle GridView. Alors que l’interface utilisateur pour la pagination personnalisée est identique à celui de la pagination par défaut, pas à pas détaillé d’une page à la suivante à l’aide de la pagination personnalisée peut être plusieurs ordres de grandeur plus rapidement que la pagination par défaut.

> [!NOTE]
> Le gain de performances exactes exposé par la pagination personnalisée varie selon le nombre total d’enregistrements avertis par l’intermédiaire et de la charge sur le serveur de base de données. À la fin de ce didacticiel, nous allons examiner certaines métriques approximative qui mettent en évidence les avantages des performances obtenues via la pagination personnalisée.


## <a name="step-1-understanding-the-custom-paging-process"></a>Étape 1 : Présentation du processus de la pagination personnalisée

Lorsque la pagination des données, les enregistrements précis affichés dans une page varient en fonction de la page de données demandées et le nombre d’enregistrements affichés par page. Par exemple, supposons que nous voulons, de parcourir les 81 produits, affichage des 10 produits par page. Lors de l’affichage de la première page, d escompté produits 1 à 10 ; lors de l’affichage de la deuxième page d nous intéresser produits 20 à 11 et ainsi de suite.

Il existe trois variables qui déterminent ce que les enregistrements doivent être récupérées et comment l’interface de pagination doit être restitué :

- **Index de ligne de début** l’index de la première ligne dans la page de données à afficher ; peut être calculée en multipliant l’index de page par les enregistrements à afficher par page et l’ajout d’un index. Par exemple, lors de la pagination dans les enregistrements de 10 à la fois, pour la première page (dont l’index de page est 0), l’Index de ligne de début est 0 \* 10 + 1 ou 1 ; pour la deuxième page (dont l’index de page est 1), l’Index de ligne de début est 1 \* 10 + 1 , ou 11.
- **Nombre maximal de lignes** le nombre maximal d’enregistrements à afficher par page. Cette variable est appelée nombre maximal de lignes de la dernière page peut-être être moins d’enregistrements retournés à la taille de page. Par exemple, lors de la pagination dans les enregistrements de 81 produits 10 par page, la page neuvième et dernière aura qu’un seul enregistrement. Cependant, aucune page, n’affiche plus d’enregistrements que la valeur du nombre maximal de lignes.
- **Nombre total d’enregistrements** le nombre total d’enregistrements en cours par le biais de pagination. Tandis que t de cette variable n’est nécessaire pour déterminer les enregistrements à extraire d’une page donnée, il impose l’interface de pagination. Par exemple, s’il existe des 81 produits avertis via, l’interface de pagination sait pour afficher les numéros de page neuf dans l’interface utilisateur de pagination.

Avec la pagination de la valeur par défaut, l’Index de ligne de début est calculée en tant que le produit de l’index de page et la taille de page plus un, tandis que le nombre maximal de lignes est simplement la taille de page. Étant donné que la pagination par défaut récupère tous les enregistrements de la base de données lors du rendu de n’importe quelle page de données, l’index pour chaque ligne est connu, rendant ainsi à la ligne d’Index de ligne de démarrer une tâche facile. En outre, le nombre Total d’enregistrements est prête, telle qu’elle s simplement le nombre d’enregistrements dans la table de données (ou tout objet est utilisé pour stocker les résultats de la base de données).

Étant donné les variables de l’Index de ligne de début et le nombre maximal de lignes, une implémentation de la pagination personnalisée doit retourner uniquement le sous-ensemble précis d’enregistrements en commençant à l’Index de ligne de début et de nombre de lignes maximal d’enregistrements par la suite. La pagination personnalisée fournit deux défis :

- Nous devons pouvoir efficacement associer un index de ligne à chaque ligne de la totalité des données en cours de pagination via afin que nous pouvons commence à renvoyer des enregistrements à l’Index de ligne de début spécifié
- Nous avons besoin fournir le nombre total d’enregistrements en cours par le biais de pagination

Dans les deux étapes suivantes, nous allons examiner le script SQL nécessaire pour répondre à ces deux défis. En plus du script SQL, nous devrons également implémenter les méthodes dans la couche DAL et la couche BLL.

## <a name="step-2-returning-the-total-number-of-records-being-paged-through"></a>Étape 2 : Renvoi du nombre Total d’enregistrements en cours par le biais de pagination

Avant d’examiner comment récupérer le sous-ensemble précis d’enregistrements pour la page s’affiche, vous permettent de s d’abord examiner comment retourner le nombre total d’enregistrements en cours par le biais de pagination. Ces informations sont nécessaires pour configurer correctement l’interface utilisateur de pagination. Le nombre total d’enregistrements renvoyés par une requête SQL peut être obtenu à l’aide de la [ `COUNT` fonction d’agrégation](https://msdn.microsoft.com/en-US/library/ms175997.aspx). Par exemple, pour déterminer le nombre total d’enregistrements dans la `Products` table, nous pouvons utiliser la requête suivante :


[!code-sql[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample1.sql)]

Permettent d’ajouter une méthode à notre DAL qui retourne cette information s. En particulier, nous allons créer une méthode de la couche DAL appelée `TotalNumberOfProducts()` qui exécute la `SELECT` instruction ci-dessus.

Commencez par ouvrir le `Northwind.xsd` fichier DataSet typé dans les `App_Code/DAL` dossier. Ensuite, avec le bouton droit sur le `ProductsTableAdapter` dans le concepteur et choisissez Ajouter une requête. En tant que nous avons vu dans les didacticiels précédents, cela permet d’ajouter une nouvelle méthode à la couche DAL qui, lorsqu’elle est appelée, exécute une procédure stockée ou une instruction SQL donnée. Comme avec nos méthodes TableAdapter dans les didacticiels précédents, pour celle-ci s’abonner pour utiliser d’instruction SQL ad hoc.


![Utilisez l’instruction SQL Ad Hoc](efficiently-paging-through-large-amounts-of-data-cs/_static/image1.png)

**Figure 1**: utilisez l’instruction SQL Ad Hoc


Dans l’écran suivant, nous pouvons spécifier le type de requête à créer. Étant donné que cette requête retourne une valeur scalaire unique le nombre total d’enregistrements dans la `Products` table choisir le `SELECT` qui retourne une option de valeur unique.


![Configurer la requête pour utiliser une instruction SELECT qui retourne une valeur unique](efficiently-paging-through-large-amounts-of-data-cs/_static/image2.png)

**Figure 2**: configurer la requête pour utiliser une instruction SELECT qui retourne une valeur unique


Après avoir indiquant le type de requête à utiliser, nous devons ensuite spécifier la requête.


![Utilisez la sélection Count à partir de la requête de produits](efficiently-paging-through-large-amounts-of-data-cs/_static/image3.png)

**Figure 3**: utiliser le nombre de sélection (\*) FROM produits requête


Enfin, spécifiez le nom de la méthode. S citée plus haut, vous permettent d’utiliser `TotalNumberOfProducts`.


![Nom de la méthode de la couche DAL TotalNumberOfProducts](efficiently-paging-through-large-amounts-of-data-cs/_static/image4.png)

**Figure 4**: nom de la méthode de la couche DAL TotalNumberOfProducts


Après avoir cliqué sur Terminer, l’Assistant ajoutera les `TotalNumberOfProducts` méthode à la couche DAL. Les méthodes de retour scalaires dans la couche DAL retournent des types nullable, au cas où le résultat de la requête SQL est `NULL`. Notre `COUNT` requête, cependant, retourne toujours un non -`NULL` de valeur ; tous les cas, la méthode de la couche DAL retourne un entier nullable.

En plus de la méthode de la couche DAL, nous devons également une méthode dans la couche BLL. Ouvrir le `ProductsBLL` fichier de classe et ajoutez un `TotalNumberOfProducts` méthode qui appelle simplement vers le bas de la couche DAL s `TotalNumberOfProducts` méthode :


[!code-csharp[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample2.cs)]

La couche DAL s `TotalNumberOfProducts` méthode retourne un entier nullable ; Toutefois, nous ve créé le `ProductsBLL` classe s `TotalNumberOfProducts` méthode pour qu’elle retourne un entier standard. Par conséquent, nous devons disposer le `ProductsBLL` classe s `TotalNumberOfProducts` méthode retourne la partie de la valeur de l’entier nullable retourné par la couche DAL s `TotalNumberOfProducts` (méthode). L’appel à `GetValueOrDefault()` retourne la valeur de l’entier nullable, si elle existe ; si l’entier nullable est `null`, toutefois, elle retourne la valeur d’entier par défaut, 0.

## <a name="step-3-returning-the-precise-subset-of-records"></a>Étape 3 : Retour le sous-ensemble précis d’enregistrements

La tâche suivante consiste à créer des méthodes dans la couche DAL et de la couche BLL qui acceptent l’Index de ligne de début et de variables du nombre maximal de lignes indiqué précédemment et retournent les enregistrements appropriés. Avant de le faire, qui permettent de s d’abord examiner le script SQL nécessaire. Le défi nous est que nous devons être en mesure d’assigner un index pour chaque ligne dans tous les résultats averti via afin que nous pouvons retourner uniquement les enregistrements en commençant à l’Index de ligne de début (et jusqu’au nombre d’enregistrements maximal d’enregistrements).

Cela n’est pas difficile s’il existe déjà une colonne dans la table de base de données qui sert d’un index de ligne. À première vue nous pouvons penser que le `Products` table s `ProductID` champ suffirait, comme le premier produit a `ProductID` 1, 2, le second et ainsi de suite. Toutefois, la suppression d’un produit laisse une discontinuité dans la séquence, en annulant cette approche.

Il existe deux techniques générales utilisés pour associer efficacement un index de ligne les données à la page, permettant ainsi le sous-ensemble précis d’enregistrements à récupérer :

- **À l’aide de SQL Server 2005 s `ROW_NUMBER()` mot clé** nouvelle pour SQL Server 2005, le `ROW_NUMBER()` mot clé associe un classement de chaque enregistrement renvoyé en fonction de classement. Ce classement peut être utilisé comme un index de ligne pour chaque ligne.
- **À l’aide d’une Variable de Table et `SET ROWCOUNT`**  s de SQL Server [ `SET ROWCOUNT` instruction](https://msdn.microsoft.com/en-us/library/ms188774.aspx) peut être utilisé pour spécifier le nombre total d’enregistrements une requête doit traiter avant la fin d’exécution ; [variables de table](http://www.sqlteam.com/item.asp?ItemID=9454) sont des variables locales T-SQL qui peuvent contenir des données tabulaires, akin [tables temporaires](http://www.sqlteam.com/item.asp?ItemID=2029). Cette approche fonctionne également bien avec Microsoft SQL Server 2005 et SQL Server 2000 (tandis que les `ROW_NUMBER()` approche fonctionne uniquement avec SQL Server 2005).  
  
 L’idée consiste à créer une variable de table qui a un `IDENTITY` colonne et des colonnes pour les clés primaires de la table dont les données sont paginées via. Ensuite, le contenu de la table dont les données sont paginées via est enregistré dans la variable de table, en associant un index de ligne séquentiel (via la `IDENTITY` colonne) pour chaque enregistrement dans la table. Une fois que la variable de table a été remplie, un `SELECT` instruction sur la variable de table, jointe à la table sous-jacente, peut être exécutée pour extraire les enregistrements particuliers. La `SET ROWCOUNT` instruction est utilisée pour intelligemment et limiter le nombre d’enregistrements qui doivent être extraites dans la variable de table.  
  
 L’efficacité de cette approche s est basée sur le numéro de page demandé, comme le `SET ROWCOUNT` valeur est assignée à la valeur de l’Index de ligne de début plus le nombre maximal de lignes. Lorsque la pagination des pages de bas niveau telles que la première de plusieurs pages de données, cette approche est très efficace. Toutefois, il présente des performances de la pagination de type par défaut lors de la récupération d’une page vers la fin.

Ce didacticiel implémente la pagination personnalisée à l’aide du `ROW_NUMBER()` (mot clé). Pour plus d’informations sur l’utilisation de la variable de table et `SET ROWCOUNT` technique, consultez [la méthode plus efficace A de la pagination par le biais de grands jeux de résultats](http://www.4guysfromrolla.com/webtech/042606-1.shtml).

Le `ROW_NUMBER()` mot clé associé à un classement chaque enregistrement retournée sur un ordre particulier à l’aide de la syntaxe suivante :


[!code-sql[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample3.sql)]

`ROW_NUMBER()`Retourne une valeur numérique qui spécifie le rang de chaque enregistrement en ce qui concerne l’ordre indiqué. Par exemple, pour voir le rang de chaque produit, trié du plus cher le moins, nous pourrions utiliser la requête suivante :


[!code-sql[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample4.sql)]

La figure 5 illustre cette requête résultats s lors de l’exécution via la fenêtre de requête dans Visual Studio. Notez que les produits sont classés par prix, ainsi que d’un rang de prix pour chaque ligne.


![Le rang de prix est inclus pour chaque enregistrement retourné](efficiently-paging-through-large-amounts-of-data-cs/_static/image5.png)

**Figure 5**: le rang de prix est inclus pour chaque enregistrement retourné


> [!NOTE]
> `ROW_NUMBER()`simplement l’une des nombreuses nouvelles fonctions de classement n’est pas disponible dans SQL Server 2005. Pour une discussion plus approfondie de `ROW_NUMBER()`, ainsi que les autres fonctions de classement, lire [retournant les résultats classés avec Microsoft SQL Server 2005](http://www.4guysfromrolla.com/webtech/010406-1.shtml).


Lorsque les résultats de classement par le `ORDER BY` colonne dans la `OVER` clause (`UnitPrice`, dans l’exemple ci-dessus), SQL Server doit trier les résultats. Ceci est une opération rapide s’il existe un index cluster sur les colonnes, les résultats sont classées par, ou s’il existe une couverture d’index, mais peut être plus coûteux dans le cas contraire. Pour aider à améliorer les performances des requêtes suffisamment volumineuses, envisagez l’ajout d’un index non cluster pour la colonne par laquelle les résultats sont triés par. Consultez [fonctions de classement et de performances dans SQL Server 2005](http://www.sql-server-performance.com/ak_ranking_functions.asp) pour examiner les considérations de performances plus détaillée.

Les informations de classement retournées par `ROW_NUMBER()` ne peut pas être utilisés directement dans le `WHERE` clause. Toutefois, une table dérivée peut être utilisée pour retourner le `ROW_NUMBER()` résultat, qui peut ensuite s’afficher dans le `WHERE` clause. Par exemple, la requête suivante utilise une table dérivée pour retourner les colonnes ProductName et UnitPrice, avec la `ROW_NUMBER()` résultat, puis utilise une `WHERE` clause afin de retourner uniquement les produits dont le rang prix est compris entre 11 et 20 :


[!code-sql[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample5.sql)]

Étendez ce concept un peu plus tard, que nous pouvons utiliser cette approche pour récupérer une page spécifique de données selon les valeurs d’Index de ligne de début et le nombre maximal de lignes souhaités :


[!code-html[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample6.html)]

> [!NOTE]
> Comme nous verrons plus loin dans ce didacticiel, les  *`StartRowIndex`*  fournie par ObjectDataSource est indexée en commençant à zéro, alors que le `ROW_NUMBER()` valeur retournée par SQL Server 2005 est indexée en commençant à 1. Par conséquent, le `WHERE` clause retourne les enregistrements où `PriceRank` est strictement supérieur à  *`StartRowIndex`*  et inférieure ou égale à  *`StartRowIndex`*   +  *`MaximumRows`*.


Maintenant que nous avons indiqué comment `ROW_NUMBER()` peut être utilisée pour récupérer une page spécifique de données selon les valeurs de l’Index de ligne de début et le nombre maximal de lignes, nous devons maintenant implémenter cette logique en tant que méthodes dans la couche DAL et la couche BLL.

Lors de la création de cette requête, que nous devons déterminer l’ordre selon lequel les résultats sont classés ; permettent de trier les produits par leur nom dans l’ordre alphabétique s. Cela signifie qu’avec l’implémentation de la pagination personnalisée dans ce didacticiel, nous ne pourrez pas créer un rapport paginé personnalisé que peuvent également être triés. Cependant, dans l’étape suivante du didacticiel, nous allons voir comment ces fonctionnalités peuvent être fournies.

Dans la section précédente, nous avons créé la méthode de la couche DAL comme instruction SQL ad hoc. Malheureusement, l’Analyseur de T-SQL dans Visual Studio utilisé par t ne TableAdapter Assistant comme le `OVER` syntaxe utilisée par le `ROW_NUMBER()` (fonction). Par conséquent, nous devons créer cette méthode de la couche DAL comme une procédure stockée. Sélectionnez l’Explorateur de serveurs à partir du menu Affichage (ou appuyez sur Ctrl + Alt + S) et développez le `NORTHWND.MDF` nœud. Pour ajouter une nouvelle procédure stockée, avec le bouton droit sur le nœud procédures stockées et choisissez Ajouter une nouvelle procédure stockée (voir Figure 6).


![Ajouter une nouvelle procédure stockée pour la pagination des produits](efficiently-paging-through-large-amounts-of-data-cs/_static/image6.png)

**Figure 6**: ajouter une nouvelle procédure stockée pour la pagination des produits


Cette procédure stockée doit accepter deux paramètres d’entrée d’entier - `@startRowIndex` et `@maximumRows` et utiliser le `ROW_NUMBER()` fonction classés par le `ProductName` champ, en retournant uniquement les lignes supérieur spécifié `@startRowIndex` et inférieur à ou égal à `@startRowIndex`  +  `@maximumRow` s. Entrez le script suivant dans la nouvelle procédure stockée, puis sur l’icône Enregistrer pour ajouter la procédure stockée à la base de données.


[!code-sql[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample7.sql)]

Après avoir créé la procédure stockée, prenez un moment pour le tester. Avec le bouton droit sur le `GetProductsPaged` procédure stockée nom dans l’Explorateur de serveurs et choisissez l’option d’exécution. Visual Studio vous invite ensuite les paramètres d’entrée, `@startRowIndex` et `@maximumRow` s (voir la Figure 7). Essayez différentes valeurs et examiner les résultats.


![Entrez une valeur pour le @startRowIndex et @maximumRows paramètres](efficiently-paging-through-large-amounts-of-data-cs/_static/image7.png)

**Figure 7**: entrez une valeur pour le @startRowIndex et @maximumRows paramètres


Une fois ces choix d’entrée des valeurs de paramètres, la fenêtre Sortie affiche les résultats. La figure 8 illustre les résultats lors du passage de 10 à la fois pour le `@startRowIndex` et `@maximumRows` paramètres.


[![Les enregistrements que s’affichent dans la deuxième Page de données sont retournées.](efficiently-paging-through-large-amounts-of-data-cs/_static/image9.png)](efficiently-paging-through-large-amounts-of-data-cs/_static/image8.png)

**Figure 8**: les enregistrements que s’affichent dans la deuxième Page de données sont retournés ([cliquez pour afficher l’image en taille réelle](efficiently-paging-through-large-amounts-of-data-cs/_static/image10.png))


Avec cette procédure stockée créée, nous re prêt à créer le `ProductsTableAdapter` (méthode). Ouvrir le `Northwind.xsd` DataSet typé, avec le bouton droit dans le `ProductsTableAdapter`, puis choisissez l’option Ajouter une requête. Au lieu de créer la requête à l’aide d’une instruction SQL ad hoc, créez à l’aide d’une procédure stockée existante.


![Créez la méthode de la couche DAL à l’aide d’une procédure stockée existante](efficiently-paging-through-large-amounts-of-data-cs/_static/image11.png)

**Figure 9**: créer la méthode de la couche DAL à l’aide d’une procédure stockée existante


Ensuite, nous sommes invités à sélectionner la procédure stockée à appeler. Choisir le `GetProductsPaged` procédure stockée à partir de la liste déroulante.


![Choisissez le GetProductsPaged procédure stockée à partir de la liste déroulante](efficiently-paging-through-large-amounts-of-data-cs/_static/image12.png)

**La figure 10**: choisissez la GetProductsPaged procédure stockée à partir de la liste déroulante


L’écran suivant vous demande alors le type de données retourné par la procédure stockée : données tabulaires, une valeur unique ou aucune valeur. Étant donné que le `GetProductsPaged` procédure stockée peut retourner plusieurs enregistrements, indiquer qu’il retourne des données tabulaires.


![Indiquer que la procédure stockée renvoie des données tabulaires](efficiently-paging-through-large-amounts-of-data-cs/_static/image13.png)

**Figure 11**: indiquer que la procédure stockée renvoie des données tabulaires


Enfin, indiquez les noms des méthodes que vous souhaitez avoir créé. Comme avec nos didacticiels précédents, continuez et créez des méthodes à l’aide à la fois le remplissage un DataTable et retourner un DataTable. Nom de la première méthode `FillPaged` et le second `GetProductsPaged`.


![Nom de la FillPaged de méthodes et GetProductsPaged](efficiently-paging-through-large-amounts-of-data-cs/_static/image14.png)

**Figure 12**: nom le méthodes FillPaged et GetProductsPaged


En outre pour la création d’une méthode de la couche DAL pour retourner une page spécifique de produits, nous devons également fournir cette fonctionnalité dans la couche BLL. Comme la méthode de la couche DAL, la couche BLL GetProductsPaged (méthode) doit accepter deux entrées entier pour spécifier l’Index de ligne de début et le nombre maximal de lignes et doit retourner uniquement les enregistrements qui se trouvent dans la plage spécifiée. Créer une telle méthode BLL dans la classe ProductsBLL que simplement des appels vers le bas dans la couche DAL s GetProductsPaged (méthode), comme suit :


[!code-csharp[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample8.cs)]

Vous pouvez utiliser n’importe quel nom pour les paramètres d’entrée de méthode s BLL, mais, comme nous le verrons bientôt, vous choisissez d’utiliser `startRowIndex` et `maximumRows` nous évite une trop peu de travail lors de la configuration d’un ObjectDataSource pour utiliser cette méthode.

## <a name="step-4-configuring-the-objectdatasource-to-use-custom-paging"></a>Étape 4 : Configuration ObjectDataSource pour utiliser la pagination personnalisée

Les méthodes BLL et DAL pour accéder à un sous-ensemble spécifique d’enregistrements terminées, nous re prêt à créer un GridView de contrôler si les pages via ses enregistrements sous-jacente à l’aide de la pagination personnalisée. Commencez par ouvrir le `EfficientPaging.aspx` page dans le `PagingAndSorting` dossier, ajoutez un contrôle GridView à la page et configurez-le pour utiliser un nouveau contrôle ObjectDataSource. Dans nos didacticiels précédentes, nous avons dû souvent ObjectDataSource configuré pour utiliser le `ProductsBLL` classe s `GetProducts` (méthode). Cette fois, toutefois, nous souhaitons utiliser le `GetProductsPaged` (méthode) à la place, depuis le `GetProducts` méthode retourne *tous les* des produits dans la base de données alors que `GetProductsPaged` retourne uniquement un sous-ensemble spécifique d’enregistrements.


![Configurer pour utiliser la méthode GetProductsPaged de la classe ProductsBLL s ObjectDataSource](efficiently-paging-through-large-amounts-of-data-cs/_static/image15.png)

**Figure 13**: configurer pour utiliser la méthode GetProductsPaged de la classe ProductsBLL s ObjectDataSource


Dans la mesure où re de création d’un GridView en lecture seule, prenez un moment pour définir la liste déroulante de méthode dans l’instruction INSERT, UPDATE et supprimer des onglets à (None).

Ensuite, l’Assistant ObjectDataSource nous invite pour les sources de la `GetProductsPaged` méthode s `startRowIndex` et `maximumRows` les valeurs de paramètres d’entrée. Il vous suffit de ces paramètres d’entrée sont réellement définies par le contrôle GridView automatiquement aucun conservez la valeur de la source et cliquez sur Terminer.


![Laissez les Sources de paramètre d’entrée car aucun](efficiently-paging-through-large-amounts-of-data-cs/_static/image16.png)

**La figure 14**: laissez les Sources de paramètre d’entrée car aucun


Après avoir terminé l’Assistant ObjectDataSource, le contrôle GridView contiendra un BoundField ou le CheckBoxField pour chacun des champs de données de produit. Vous pouvez personnaliser l’apparence de s GridView comme vous le souhaitez. Je ve choisi d’afficher uniquement les `ProductName`, `CategoryName`, `SupplierName`, `QuantityPerUnit`, et `UnitPrice` BoundFields. En outre, configurer le contrôle GridView pour prendre en charge la pagination en activant la case à cocher Activer la pagination dans sa balise active. Après ces modifications, le balisage déclaratif GridView et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample9.aspx)]

Si vous visitez la page via un navigateur, toutefois, le contrôle GridView est introuvable à rechercher.


![Le contrôle GridView est pas affichée](efficiently-paging-through-large-amounts-of-data-cs/_static/image17.png)

**Figure 15**: GridView est pas affichée


Le contrôle GridView est manquant, car l’ObjectDataSource est actuellement à l’aide de 0 comme valeurs pour les deux le `GetProductsPaged` `startRowIndex` et `maximumRows` paramètres d’entrée. Par conséquent, la requête SQL qui en résulte est qui ne retourne pas d’enregistrements et par conséquent, le contrôle GridView ne figure pas.

Pour résoudre ce problème, nous devons configurer ObjectDataSource pour utiliser la pagination personnalisée. Cela peut être accompli dans les étapes suivantes :

1. **Définir le s ObjectDataSource `EnablePaging` propriété `true`**  cela indique à l’ObjectDataSource elle doit passer à la `SelectMethod` deux paramètres supplémentaires : un pour spécifier l’Index de ligne de début ([ `StartRowIndexParameterName` ](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.objectdatasource.startrowindexparametername.aspx)) et l’autre pour spécifier le nombre maximal de lignes ([`MaximumRowsParameterName`](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.objectdatasource.maximumrowsparametername.aspx)).
2. **Définir le s ObjectDataSource `StartRowIndexParameterName` et `MaximumRowsParameterName` propriétés en conséquence** le `StartRowIndexParameterName` et `MaximumRowsParameterName` propriétés indiquent les noms des paramètres d’entrée passés à la `SelectMethod` pour des raisons de la pagination personnalisée . Par défaut, ces noms de paramètre sont `startIndexRow` et `maximumRows`, c’est pourquoi, lors de la création du `GetProductsPaged` méthode dans la couche BLL, j’ai utilisé ces valeurs pour les paramètres d’entrée. Si vous avez choisi d’utiliser des noms de paramètres différents pour la couche BLL s `GetProductsPaged` méthode telle que `startIndex` et `maxRows`, pour l’exemple, vous devez définie le s ObjectDataSource `StartRowIndexParameterName` et `MaximumRowsParameterName` propriétés en conséquence (par exemple, startIndex pour `StartRowIndexParameterName` et le nombre maximal de lignes pour `MaximumRowsParameterName`).
3. **Définir le s ObjectDataSource [ `SelectCountMethod` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.objectdatasource.selectcountmethod(VS.80).aspx) au nom de la méthode qui retourne le Total nombre d’enregistrements en cours paginée via (`TotalNumberOfProducts`)** n’oubliez pas que la `ProductsBLL` classe s `TotalNumberOfProducts`méthode retourne le nombre total d’enregistrements transmise par radiomessagerie à l’aide d’une méthode de la couche DAL qui exécute un `SELECT COUNT(*) FROM Products` requête. Ces informations sont nécessaires par ObjectDataSource pour restituer correctement de l’interface de pagination.
4. **Supprimer le `startRowIndex` et `maximumRows` `<asp:Parameter>` éléments de balisage déclaratif ObjectDataSource s** lors de la configuration ObjectDataSource via l’Assistant, Visual Studio automatiquement ajouté deux `<asp:Parameter>` éléments de la `GetProductsPaged` méthode s les paramètres d’entrée. En définissant `EnablePaging` à `true`, ces paramètres seront transférés automatiquement ; si elles apparaissent également dans la syntaxe déclarative, ObjectDataSource va tenter de passer *quatre* paramètres à la `GetProductsPaged` (méthode) et les deux paramètres à la `TotalNumberOfProducts` (méthode). Si vous avez oublié de supprimer ces `<asp:Parameter>` éléments, lors de la visite de la page via un navigateur, vous obtiendrez un message d’erreur comme : *ObjectDataSource 'ObjectDataSource1' n’a pas trouvé une méthode non générique 'TotalNumberOfProducts' qui a paramètres : startRowIndex, maximumRows*.

Après avoir apporté ces modifications, la syntaxe déclarative de s ObjectDataSource doit se présenter comme suit :


[!code-aspx[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample10.aspx)]

Notez que la `EnablePaging` et `SelectCountMethod` propriétés ont été définies et `<asp:Parameter>` éléments ont été supprimés. Figure 16 présente une capture d’écran de la fenêtre Propriétés après avoir apporté ces modifications.


![Pour utiliser la pagination personnalisée, configurez le contrôle ObjectDataSource](efficiently-paging-through-large-amounts-of-data-cs/_static/image18.png)

**Figure 16**: pour utiliser la pagination personnalisée, configurez le contrôle ObjectDataSource


Après avoir apporté ces modifications, visitez cette page via un navigateur. Vous devez voir les 10 produits répertoriés, classées par ordre alphabétique. Prenez un moment pour parcourir les données d’une page à la fois. Il n’existe aucune différence visual du point de vue utilisateur final s entre la pagination par défaut et la pagination personnalisée, plus efficacement la pagination personnalisée pages de grandes quantités de données, car il récupère uniquement les enregistrements qui doivent être affichés pour une page donnée.


[![Les données, commandé par le produit s nom, est personnalisé paginée à l’aide de pagination](efficiently-paging-through-large-amounts-of-data-cs/_static/image20.png)](efficiently-paging-through-large-amounts-of-data-cs/_static/image19.png)

**Figure 17**: les données, commandé par le produit s nom, est personnalisé paginée à l’aide de pagination ([cliquez pour afficher l’image en taille réelle](efficiently-paging-through-large-amounts-of-data-cs/_static/image21.png))


> [!NOTE]
> Avec la pagination personnalisée, la page compter la valeur retournée par le s ObjectDataSource `SelectCountMethod` est stocké dans l’état d’affichage s GridView. Autres variables GridView le `PageIndex`, `EditIndex`, `SelectedIndex`, `DataKeys` collection et ainsi de suite sont stockés dans *état du contrôle*, qui est conservé, quelle que soit la valeur de mappage GridView `EnableViewState` propriété. Étant donné que le `PageCount` valeur est rendue persistante entre les publications (postback) à l’aide d’état d’affichage, lors de l’utilisation d’une interface de pagination qui inclut un lien pour accéder à la dernière page, il est impératif que l’état d’affichage s GridView soit activé. (Si votre interface de l’échange n’inclut pas un lien direct à la dernière page, puis vous pouvez désactiver l’état d’affichage).


En cliquant sur le dernier lien de page provoque une publication (postback) et fait en sorte que le contrôle GridView à mettre à jour son `PageIndex` propriété. Si vous cliquez sur le dernier lien de page, le contrôle GridView assigne son `PageIndex` propriété sur une valeur inférieure à sa `PageCount` propriété. Avec l’état d’affichage désactivé, le `PageCount` valeur est perdue entre publications (postback) et le `PageIndex` est attribué la valeur maximale entière à la place. Ensuite, le contrôle GridView tente de déterminer l’index de début de ligne en multipliant le `PageSize` et `PageCount` propriétés. Cela entraîne une `OverflowException` étant donné que le produit dépasse la taille maximum autorisée d’un entier.

## <a name="implement-custom-paging-and-sorting"></a>Implémenter la pagination personnalisée et le tri

Notre implémentation actuelle de la pagination personnalisée nécessite la définition que l’ordre selon lequel les données sont paginées via statiquement lors de la création du `GetProductsPaged` procédure stockée. Toutefois, vous avez peut-être noté que la balise active de GridView s contient une case à cocher Activer le tri en plus de l’option Activer la pagination. Malheureusement, ajout de tri de prise en charge pour le contrôle GridView avec notre implémentation de la pagination personnalisée en cours ne concerne que les enregistrements sur la page actuellement affichée de données. Par exemple, si vous configurez le contrôle GridView pour prennent également en charge la pagination et, lors de l’affichage de la première page de données, trier par nom de produit dans l’ordre décroissant, il sera inverser l’ordre des produits sur la page 1. Comme le montre la Figure 18, par exemple montre crevettes de Carnarvon comme le premier produit lors du tri par ordre alphabétique inverse, qui ignore les 71 autres produits qui suivent crevettes de Carnarvon, par ordre alphabétique ; Seuls les enregistrements sur la première page sont prises en compte dans l’ordre de tri.


[![Uniquement les données affichées sur la Page actuelle est triée.](efficiently-paging-through-large-amounts-of-data-cs/_static/image23.png)](efficiently-paging-through-large-amounts-of-data-cs/_static/image22.png)

**La figure 18**: uniquement les données affichées sur la Page actuelle est triée ([cliquez pour afficher l’image en taille réelle](efficiently-paging-through-large-amounts-of-data-cs/_static/image24.png))


L’ordre de tri s’applique uniquement à la page de données actuelle, car l’ordre de tri se produit une fois que les données ont été récupérées à partir de la couche BLL s `GetProductsPaged` (méthode) et cette méthode retourne uniquement les enregistrements de la page spécifique. Pour implémenter le tri correctement, vous devez transmettre l’expression de tri pour la `GetProductsPaged` méthode afin que les données peuvent être classées de façon appropriée avant de retourner la page spécifique de données. Nous verrons comment procéder dans notre didacticiel suivant.

## <a name="implementing-custom-paging-and-deleting"></a>Implémentation personnalisée de la pagination et la suppression

Si vous l’activation de la fonctionnalité de suppression dans un GridView dont les données sont chargées à l’aide de techniques de la pagination personnalisée vous constaterez que lors de la suppression du dernier enregistrement de la dernière page, le contrôle GridView disparaît plutôt que de décrémentation de façon appropriée la s GridView `PageIndex`. Pour reproduire ce bogue, activez la suppression pour le didacticiel que simplement nous venons de créer. Atteindre la dernière page (9), où vous devez voir un produit unique, car nous allons la pagination des 81 produits, 10 à la fois. Supprimer ce produit.

Lors de la suppression du dernier produit, le contrôle GridView *doit* accéder automatiquement à la page huitième et pagination par défaut présente ces fonctionnalités. Avec la pagination personnalisée, toutefois, après la suppression de ce dernier produit sur la dernière page, le contrôle GridView simplement disparaît de l’écran complètement. La cause exacte *pourquoi* dans ce cas est de type bit dépasse le cadre de ce didacticiel, consultez [la suppression du dernier enregistrement de la dernière Page à partir d’un GridView avec la pagination personnalisée](http://scottonwriting.net/sowblog/posts/7326.aspx) pour plus d’informations sur la source de bas niveau Ce problème. En résumé il s en raison de la séquence d’étapes effectuées par le contrôle GridView lorsque l’utilisateur clique sur le bouton Supprimer suivante :

1. Supprimer l’enregistrement
2. Obtenir les enregistrements appropriés à afficher pour le texte spécifié `PageIndex` et`PageSize`
3. Assurez-vous que le `PageIndex` ne dépasse pas le nombre de pages de données dans la source de données si le s GridView est le cas, automatiquement décrémente `PageIndex` propriété
4. Lier la page appropriée de données pour le contrôle GridView à l’aide d’enregistrements obtenus à l’étape 2

Le problème vient du fait que dans étape 2 le `PageIndex` utilisé lorsqu’en saisissant les enregistrements à afficher est toujours le `PageIndex` de la dernière page dont seul enregistrement a été simplement supprimé. Par conséquent, dans l’étape 2, *aucune* enregistrements sont retournés dans la mesure où cette dernière page de données ne contient plus d’enregistrements. Puis, à l’étape 3, le contrôle GridView se rend compte que son `PageIndex` propriété est supérieure au nombre total de pages dans la source de données (dans la mesure où ve supprimé le dernier enregistrement de la dernière page) et par conséquent décrémente son `PageIndex` propriété. À l’étape 4 GridView tente de se lier aux données récupérées à l’étape 2 ; Toutefois, à l’étape 2, aucun enregistrement ont été retournés, par conséquent, ce qui entraîne un GridView vide. Avec la pagination de la valeur par défaut, cet t ne de problème de surface, car l’étape 2 de *tous les* enregistrements sont récupérés à partir de la source de données.

Pour résoudre ce problème, nous avons deux options. La première consiste à créer un gestionnaire d’événements pour le contrôle GridView s `RowDeleted` qui détermine le nombre d’enregistrements qui ont été affiché dans la page qui a été supprimée juste le Gestionnaire d’événements. Si il y n'a qu’un seul enregistrement, l’enregistrement qui vient d’être supprimée doit avoir été dernier et nous avons besoin décrémenter le GridView s `PageIndex`. Bien entendu, nous voulons uniquement mettre à jour le `PageIndex` si l’opération de suppression a effectivement réussie, ce qui peut être déterminé en s’assurant que les `e.Exception` propriété est `null`.

Cette approche fonctionne, car elle met à jour le `PageIndex` après l’étape 1, mais avant l’étape 2. Par conséquent, à l’étape 2, l’ensemble d’enregistrements approprié est retournée. Pour ce faire, utilisez le code comme suit :


[!code-csharp[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample11.cs)]

Une autre solution consiste à créer un gestionnaire d’événements pour ObjectDataSource s `RowDeleted` événement et de définir le `AffectedRows` propriété à une valeur de 1. Après la suppression de l’enregistrement à l’étape 1 (mais avant de récupérer de nouveau les données à l’étape 2), le contrôle GridView met à jour son `PageIndex` propriété si une ou plusieurs lignes ont été affectés par l’opération. Toutefois, le `AffectedRows` propriété n’est pas définie par ObjectDataSource et par conséquent, cette étape est ignorée. Pour disposer de cette étape exécutée consiste à définir manuellement les `AffectedRows` propriété si l’opération de suppression s’effectue correctement. Cela peut être accompli à l’aide de code semblable au suivant :


[!code-csharp[Main](efficiently-paging-through-large-amounts-of-data-cs/samples/sample12.cs)]

Vous trouverez le code pour les deux de ces gestionnaires d’événements dans la classe code-behind de la `EfficientPaging.aspx` exemple.

## <a name="comparing-the-performance-of-default-and-custom-paging"></a>Comparer les performances de la valeur par défaut et la pagination personnalisée

Étant donné que la pagination personnalisée récupère uniquement les enregistrements nécessaires, tandis que la pagination par défaut retourne *tous les* des enregistrements pour chaque page affichée, elle s effacer que la pagination personnalisée est plus efficace que la pagination par défaut. Mais simplement comment beaucoup plus efficace est la pagination personnalisée ? Le tri des gains de performances sont visibles par déplacement à partir de la pagination par défaut pour la pagination personnalisée ?

Malheureusement, s’il n’y a aucune adapté à tous les répondent ici. Le gain de performances dépend de plusieurs facteurs, mises en évidence de deux le nombre d’enregistrements en cours de pagination via et la charge placée sur les canaux de communication et de serveur de base de données entre le serveur web et le serveur de base de données. Pour les tables de petite taille avec seulement quelques douzaine enregistrements, la différence de performances peut être négligeable. Cependant, pour les grandes tables, avec des milliers à des centaines de milliers de lignes, la différence de performances est élevée.

Un article de mes [pagination personnalisée dans ASP.NET 2.0 avec SQL Server 2005](http://aspnet.4guysfromrolla.com/articles/031506-1.aspx), contient quelques j’ai exécuté pour présenter les différences de performances entre ces deux techniques de pagination lorsque vous choisissez une table de base de données avec des tests de performances 50 000 enregistrements. Dans ces tests, j’ai examiné le temps pour exécuter la requête au niveau de SQL Server (à l’aide de [SQL Profiler](https://msdn.microsoft.com/en-us/library/ms173757.aspx)) et à la page ASP.NET à l’aide [fonctionnalités de traçage ASP.NET s](https://msdn.microsoft.com/en-US/library/y13fw6we.aspx). Gardez à l’esprit que ces tests ont été exécutés sur mon développement avec un seul utilisateur actif et par conséquent sont peu scientifique n’imitent pas les modèles de charge de site Web par défaut. Peu importe, les résultats illustrent les différences relatives dans le temps d’exécution pour la pagination personnalisée et par défaut lorsque vous travaillez avec suffisamment de grandes quantités de données.


|  | **Moy. Durée (s)** | **Lectures** |
| --- | --- | --- |
| **Générateur de profils SQL de la pagination par défaut** | 1.411 | 383 |
| **Générateur de pagination personnalisé** | 0.002 | 29 |
| **Par défaut de la pagination traçage ASP.NET** | 2.379 | *N/A* |
| **Suivi de ASP.NET de la pagination personnalisée** | 0.029 | *N/A* |


Comme vous pouvez le voir, la récupération d’une page de données spécifique requis 354 moins de lectures en moyenne et s’est terminée en une fraction du temps. Dans la page ASP.NET, personnalisé la page a été capable de restituer dans proche de 1/100<sup>th</sup> de la durée nécessaire lors de l’utilisation de pagination par défaut. Consultez [mon article](http://aspnet.4guysfromrolla.com/articles/031506-1.aspx) pour plus d’informations sur ces résultats, ainsi que le code et une base de données, vous pouvez télécharger pour reproduire ces tests dans votre environnement.

## <a name="summary"></a>Résumé

Pagination par défaut est un jeu d’enfant pour implémenter simplement cocher la case à cocher Activer la pagination dans la balise active de données Web contrôle s, mais cette simplicité vient au détriment des performances. Avec la pagination par défaut, lorsqu’un utilisateur demande une page de données *tous les* enregistrements sont renvoyés, même si seulement un tout petit nombre d'entre eux peut-être s’afficher. Pour faire face à cette surcharge de performances, ObjectDataSource offre une autre échange option la pagination personnalisée.

Lorsque la pagination personnalisée améliore les problèmes de performances s d’échange en récupérant uniquement les enregistrements qui doivent être affichés, par défaut il s plus complexe à implémenter la pagination personnalisée. Tout d’abord, une requête doit être écrite qui accède (correctement et efficacement) le sous-ensemble spécifique d’enregistrements demandé. Cela peut être accompli de plusieurs façons. celle que nous avons examiné dans ce didacticiel est d’utiliser SQL Server 2005 s nouvelle `ROW_NUMBER()` résultats de la fonction Rank et puis résultats pour retourner uniquement ceux dont le classement correspond à une plage spécifiée. En outre, nous devons ajouter un moyen de déterminer le nombre total d’enregistrements en cours par le biais de pagination. Après avoir créé ces méthodes de la couche DAL et la couche BLL, nous devons également configurer ObjectDataSource afin qu’elle peut déterminer le nombre total d’enregistrements sont en cours de pagination via et peuvent passer correctement les valeurs d’Index de ligne de début et le nombre maximal de lignes à la couche BLL.

Tandis que la mise en œuvre de la pagination personnalisée nécessite un nombre d’étapes est pas aussi simple que la pagination par défaut, la pagination personnalisée est une nécessité lorsque la pagination des suffisamment grandes quantités de données. Comme les résultats analysés pagination a montré, personnalisée peut émis secondes sur le temps de rendu de page ASP.NET et pouvez alléger la charge sur le serveur de base de données par une ou plusieurs commandes de grandeur.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](paging-and-sorting-report-data-cs.md)
[Suivant](sorting-custom-paged-data-cs.md)
