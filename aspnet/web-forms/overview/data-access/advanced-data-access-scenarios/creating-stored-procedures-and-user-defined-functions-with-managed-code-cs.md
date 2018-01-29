---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/creating-stored-procedures-and-user-defined-functions-with-managed-code-cs
title: "Création de procédures stockées et fonctions définies par l’utilisateur avec le Code (c#) managé | Documents Microsoft"
author: rick-anderson
description: "Microsoft SQL Server 2005 s’intègre avec le Common Language Runtime .NET pour permettre aux développeurs de créer des objets de base de données via le code managé. Ce didacticiel..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/03/2007
ms.topic: article
ms.assetid: 213eea41-1ab4-4371-8b24-1a1a66c515de
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/creating-stored-procedures-and-user-defined-functions-with-managed-code-cs
msc.type: authoredcontent
ms.openlocfilehash: be3e3d61a6567da3c2cd696c01661146f2da7131
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-stored-procedures-and-user-defined-functions-with-managed-code-c"></a>Création de procédures stockées et fonctions définies par l’utilisateur avec du Code managé (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_75_CS.zip) ou [télécharger le PDF](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/datatutorial75cs1.pdf)

> Microsoft SQL Server 2005 s’intègre avec le Common Language Runtime .NET pour permettre aux développeurs de créer des objets de base de données via le code managé. Ce didacticiel montre comment créer des procédures stockées managées et managées des fonctions définies par l’utilisateur avec votre code Visual Basic ou c#. Nous avons également Découvrez comment ces éditions de Visual Studio vous permet de déboguer ces objets de base de données managés.


## <a name="introduction"></a>Introduction

Utilisent des bases de données comme s de Microsoft SQL Server 2005 le [Transact-Structured Query Language (T-SQL)](http://en.wikipedia.org/wiki/Transact-SQL) pour l’insertion, la modification et la récupération des données. La plupart des systèmes de base de données incluent des constructions de regroupement d’une série d’instructions SQL qui peut ensuite être exécutée en tant qu’une seule unité réutilisable. Les procédures stockées sont un exemple. Un autre est *les fonctions définies par l’utilisateur*(UDF), une construction qui nous allons examiner plus en détail à l’étape 9.

Fondamentalement, SQL est conçu pour l’utilisation des jeux de données. Le `SELECT`, `UPDATE`, et `DELETE` par nature, les instructions s’appliquent à tous les enregistrements dans la table correspondante et sont limitées uniquement par leur `WHERE` clauses. Il existe encore des nombreuses fonctionnalités de langage conçues pour travailler avec un enregistrement à la fois et de manipulation des données scalaires. [`CURSOR`s](http://www.sqlteam.com/item.asp?ItemID=553) permettant à un ensemble d’enregistrements à être bouclée via un à la fois. Comme les fonctions de manipulation de chaîne `LEFT`, `CHARINDEX`, et `PATINDEX` fonctionnent avec des données scalaires. SQL inclut également des instructions de flux de contrôle comme `IF` et `WHILE`.

Antérieures à Microsoft SQL Server 2005, les procédures stockées et les fonctions peut uniquement être définies comme une collection d’instructions T-SQL. SQL Server 2005, cependant, a été conçu pour permettre l’intégration avec les [Common Language Runtime (CLR)](https://msdn.microsoft.com/netframework/aa497266.aspx), qui est le runtime utilisé par tous les assemblys .NET. Par conséquent, les procédures stockées et les UDF dans une base de données SQL Server 2005 peuvent être créés à l’aide de code managé. Autrement dit, vous pouvez créer une procédure stockée ou fonction en tant que méthode dans une classe c#. Ainsi, ces procédures stockées et les UDF pour utiliser la fonctionnalité dans le .NET Framework et à partir de vos propres classes personnalisées.

Dans ce didacticiel, nous allons examiner comment créer managé procédures stockées et les fonctions définies par l’utilisateur et comment les intégrer dans notre base de données Northwind. Let s commencer !

> [!NOTE]
> Les objets de base de données managés offrent des avantages par rapport à leurs équivalents SQL. Richesse du langage et une connaissance et la possibilité de réutiliser la logique et le code existant sont les principaux avantages. Mais les objets de base de données managés sont susceptibles d’être moins efficace lorsque vous travaillez avec des jeux de données qui n’impliquent pas de logique procédurale. Pour une discussion plus approfondie sur les avantages de l’utilisation de code managé et T-SQL, passez en revue les [avantages d’à l’aide de Code managé pour créer des objets de base de données](https://msdn.microsoft.com/library/k2e1fb36(VS.80).aspx).


## <a name="step-1-moving-the-northwind-database-out-ofappdata"></a>Étape 1 : Déplacer la base de données Northwind de`App_Data`

Tous les nos didacticiels jusqu’ici ont utilisé un fichier de base de données Microsoft SQL Server 2005 Express Edition dans le s d’application web `App_Data` dossier. Placer la base de données `App_Data` simplifié de distribuer et utiliser ces didacticiels selon tous les fichiers ont été trouvés dans un répertoire et ne requis aucune étape de configuration supplémentaires pour tester le didacticiel.

Pour ce didacticiel, toutefois, s permettent de déplacer la base de données Northwind de `App_Data` et l’inscrire explicitement avec l’instance de base de données SQL Server 2005 Express Edition. Pendant que nous pouvons effectuer les étapes de ce didacticiel avec la base de données dans le `App_Data` dossier, un certain nombre des étapes est apportées beaucoup plus simple en inscrivant explicitement la base de données avec l’instance de base de données SQL Server 2005 Express Edition.

Le téléchargement de ce didacticiel comporte les fichiers de la base de données de deux - `NORTHWND.MDF` et `NORTHWND_log.LDF` - placé dans un dossier nommé `DataFiles`. Si vous suivez, ainsi que votre propre implémentation des didacticiels, fermez Visual Studio et déplacer le `NORTHWND.MDF` et `NORTHWND_log.LDF` fichiers depuis le site Web de s `App_Data` vers un dossier en dehors du site Web. Une fois que les fichiers de base de données ont été déplacés vers un autre dossier, que nous avons besoin d’inscrire la base de données Northwind à l’instance de base de données SQL Server 2005 Express Edition. Cela est possible à partir de SQL Server Management Studio. Si vous avez un non - Express Edition de SQL Server 2005 installé sur votre ordinateur puis vous probablement déjà Management Studio êtes installé. Si vous disposez de SQL Server 2005 Express Edition sur votre ordinateur uniquement, que vous prenez un moment pour télécharger et installer [Microsoft SQL Server Management Studio Express](https://www.microsoft.com/downloads/details.aspx?displaylang=en&amp;FamilyID=C243A5AE-4BD1-4E3D-94B8-5A0F62BF7796).

Lancez SQL Server Management Studio. Comme le montre la Figure 1, Management Studio démarre en demandant le serveur auquel se connecter. Entrez localhost\SQLExpress pour le nom du serveur, choisissez l’authentification Windows dans la liste déroulante de l’authentification et cliquez sur se connecter.


![Connectez-vous à l’Instance de base de données appropriée](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image1.png)

**Figure 1**: se connecter à l’Instance de base de données appropriée


Une fois que vous avez déjà connecté, la fenêtre de l’Explorateur d’objets répertorie les informations sur l’instance de base de données SQL Server 2005 Express Edition, y compris ses bases de données, les informations de sécurité, les options de gestion et ainsi de suite.

Nous avons besoin d’attacher la base de données Northwind dans la `DataFiles` dossier (ou, là où vous avez déplacé il) à l’instance de base de données SQL Server 2005 Express Edition. Avec le bouton droit sur le dossier bases de données et choisissez l’option d’attachement dans le menu contextuel. Cela affiche la boîte de dialogue Attacher les bases de données. Cliquez sur le bouton Ajouter, Explorer approprié `NORTHWND.MDF` fichier, puis cliquez sur OK. À ce stade votre écran doit ressembler à la Figure 2.


[![Connectez-vous à l’Instance de base de données appropriée](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image3.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image2.png)

**Figure 2**: se connecter à l’Instance de base de données appropriée ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image4.png))


> [!NOTE]
> Lors de la connexion à l’instance de SQL Server 2005 Express Edition via Management Studio la boîte de dialogue Attacher les bases de données ne vous permet pas d’approfondir les répertoires de profil utilisateur, comme Mes Documents. Par conséquent, veillez à placer le `NORTHWND.MDF` et `NORTHWND_log.LDF` fichiers dans un répertoire de profil de l’utilisateur.


Cliquez sur le bouton OK pour attacher la base de données. La boîte de dialogue Attacher les bases de données se ferme et l’Explorateur d’objets doit répertorier maintenant la base de données juste-attaché. Base de données a un nom tel que Northwind sans doute `9FE54661B32FDD967F51D71D0D5145CC_LINE ARTICLES\DATATUTORIALS\VOLUME 3\CSHARP\73\ASPNET_DATA_TUTORIAL_75_CS\APP_DATA\NORTHWND.MDF`. Renommez la base de données à Northwind en cliquant sur la base de données et en sélectionnant Renommer.


![Renommez la base de données Northwind](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image5.png)

**Figure 3**: Renommez la base de données Northwind


## <a name="step-2-creating-a-new-solution-and-sql-server-project-in-visual-studio"></a>Étape 2 : Création d’une nouvelle Solution et le projet SQL Server dans Visual Studio

Pour créer les procédures stockées managées ou UDF dans SQL Server 2005 nous écrirons la procédure stockée et la logique de l’UDF en tant que code c# dans une classe. Une fois que le code a été écrit, nous allons devoir compiler cette classe dans un assembly (un `.dll` fichier), inscrire l’assembly avec la base de données SQL Server, puis créer une procédure stockée ou un objet de fonction dans la base de données qui pointe vers la méthode correspondante dans l’assembly. Ces étapes peuvent toutes être effectuées manuellement. Nous pouvons créer le code dans n’importe quel texte éditeur, le compiler depuis la ligne de commande à l’aide du compilateur c# ([`csc.exe`](https://msdn.microsoft.com/library/ms379563(vs.80).aspx)), inscrire auprès de la base de données à l’aide de la [ `CREATE ASSEMBLY` ](https://msdn.microsoft.com/library/ms189524.aspx) commande ou de la gestion Studio et ajoutez la procédure stockée ou un objet UDF via des moyens similaires. Heureusement, les versions Professional et les systèmes de l’équipe de Visual Studio incluent un type de projet SQL Server qui automatise les tâches. Dans ce didacticiel nous Guide à l’aide du type de projet SQL Server pour créer une procédure stockée managée et un fichier UDF.

> [!NOTE]
> Si vous utilisez Visual Web Developer ou l’Édition Standard de Visual Studio, vous devez utiliser l’approche manuelle à la place. Étape 13 fournit des instructions détaillées pour effectuer ces étapes manuellement. Je vous encourage à lire les étapes 2 à 12 avant de lire l’étape 13 dans la mesure où ces étapes comprennent les instructions de configuration SQL Server importantes qui doivent être appliquées, quelle que soit la version de Visual Studio que vous utilisez.


Commencez par ouvrir Visual Studio. Dans le menu fichier, choisissez Nouveau projet pour afficher la boîte de dialogue Nouveau projet boîte (voir Figure 4). Descendre dans le type de projet de base de données et, à partir de modèles répertoriés sur la droite, puis créer un nouveau projet SQL Server. J’ai choisi nommer ce projet `ManagedDatabaseConstructs` et placé dans une Solution nommée `Tutorial75`.


[![Créer un nouveau projet SQL Server](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image7.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image6.png)

**Figure 4**: créer un nouveau projet SQL Server ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image8.png))


Cliquez sur le bouton OK dans la boîte de dialogue Nouveau projet pour créer la Solution et projet SQL Server.

Un projet SQL Server est lié à une base de données. Par conséquent, après avoir créé le nouveau projet SQL Server est immédiatement une demande pour spécifier ces informations. La figure 5 illustre la boîte de dialogue Nouvelle référence de base de données qui a été remplie pour pointer vers la base de données Northwind que nous avons enregistrés dans l’instance de base de données SQL Server 2005 Express Edition à l’étape 1.


![Associer le projet SQL Server de la base de données Northwind](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image9.png)

**Figure 5**: associer le projet SQL Server de la base de données Northwind


Pour déboguer les procédures stockées managées UDF, nous allons créer dans ce projet, il faut activer la prise en charge pour la connexion de débogage SQL/CLR. Chaque fois que l’association d’un projet SQL Server à une base de données (comme nous l’avons fait dans la Figure 5), Visual Studio nous vous demande si vous souhaitez activer le débogage SQL/CLR sur la connexion (voir Figure 6). Cliquez sur Oui.


![Activer le débogage SQL/CLR](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image10.png)

**Figure 6**: activer le débogage SQL/CLR


À ce stade, le nouveau projet SQL Server a été ajouté à la Solution. Il contient un dossier nommé `Test Scripts` avec un fichier nommé `Test.sql`, qui est utilisé pour déboguer les objets de base de données managés créés dans le projet. Nous examinerons le débogage à l’étape 12.

Nous pouvons maintenant ajouter les nouvelles procédures stockées gérés et les fonctions à ce projet, mais avant que nous ne permettent pas aux s inclure tout d’abord notre application web existante dans la Solution. Dans le menu fichier, sélectionnez l’option Ajouter et choisir un Site Web existant. Recherchez le dossier du site Web approprié, puis cliquez sur OK. Comme le montre la Figure 7, met à jour la Solution pour inclure les deux projets : le site Web et le `ManagedDatabaseConstructs` projet SQL Server.


![L’Explorateur de solutions inclut désormais deux projets](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image11.png)

**Figure 7**: l’Explorateur de solutions inclut désormais deux projets


Le `NORTHWNDConnectionString` valeur dans `Web.config` actuellement fait référence à la `NORTHWND.MDF` de fichiers dans le `App_Data` dossier. Étant donné que nous avons supprimé de cette base de données à partir de `App_Data` et enregistrés de manière explicite dans l’instance de base de données SQL Server 2005 Express Edition, nous devons mettre à jour en conséquence le `NORTHWNDConnectionString` valeur. Ouvrez le `Web.config` fichier dans le site Web et la modification de la `NORTHWNDConnectionString` afin que la chaîne de connexion lit la valeur : `Data Source=localhost\SQLExpress;Initial Catalog=Northwind;Integrated Security=True`. Après cette modification, votre `<connectionStrings>` section `Web.config` doit ressembler à ce qui suit :


[!code-xml[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample1.xml)]

> [!NOTE]
> Comme indiqué dans le [didacticiel précédent](debugging-stored-procedures-cs.md), lors du débogage d’un objet SQL Server à partir d’une application cliente, par exemple un site Web ASP.NET, nous devons désactiver le regroupement de connexions. La chaîne de connexion ci-dessus désactive le regroupement de connexions ( `Pooling=false` ). Si vous n’envisagez pas sur les procédures stockées managées UDF le débogage à partir du site Web ASP.NET, activer le regroupement de connexions.


## <a name="step-3-creating-a-managed-stored-procedure"></a>Étape 3 : Création d’un élément géré des procédure stockée

Pour ajouter une procédure stockée managée à la base de données Northwind que nous devez d’abord créer la procédure stockée en tant que méthode dans le projet SQL Server. À partir de l’Explorateur de solutions, cliquez sur le `ManagedDatabaseConstructs` nom du projet et choisissez d’ajouter un nouvel élément. Cela affiche la boîte de dialogue Ajouter un nouvel élément, qui répertorie les types d’objets de base de données managés peuvent être ajoutés au projet. Comme le montre la Figure 8, cela inclut les procédures stockées et les fonctions définies par l’utilisateur, entre autres.

Permettent de s commencez par ajouter une procédure stockée qui retourne simplement tous les produits qui ont été abandonnées. Nommez le nouveau fichier de la procédure stockée `GetDiscontinuedProducts.cs`.


[![Ajouter une nouvelle procédure stockée nommée GetDiscontinuedProducts.cs](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image13.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image12.png)

**Figure 8**: ajouter un nouveau stockées procédure nommée `GetDiscontinuedProducts.cs` ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image14.png))


Cela va créer un nouveau fichier de classe c# avec le contenu suivant :


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample2.cs)]

Notez que la procédure stockée est implémentée comme un `static` méthode dans un `partial` fichier de classe nommé `StoredProcedures`. En outre, le `GetDiscontinuedProducts` est décorée avec le `SqlProcedure attribute`, qui marque la méthode comme une procédure stockée.

Le code suivant crée un `SqlCommand` objet et les jeux de son `CommandText` à un `SELECT` requête qui retourne toutes les colonnes à partir de la `Products` de table pour les produits dont `Discontinued` ont la valeur 1. Ensuite, il exécute la commande et renvoie les résultats à l’application cliente. Ajoutez ce code à la `GetDiscontinuedProducts` (méthode).


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample3.cs)]

Tous les objets de base de données managés ont accès à un [ `SqlContext` objet](https://msdn.microsoft.com/library/ms131108.aspx) qui représente le contexte de l’appelant. Le `SqlContext` fournit l’accès à un [ `SqlPipe` objet](https://msdn.microsoft.com/library/microsoft.sqlserver.server.sqlpipe.aspx) via son [ `Pipe` propriété](https://msdn.microsoft.com/library/microsoft.sqlserver.server.sqlcontext.pipe.aspx). Cela `SqlPipe` objet est utilisé pour transbordeur des informations entre la base de données SQL Server et l’application appelante. Comme son nom l’indique, le [ `ExecuteAndSend` méthode](https://msdn.microsoft.com/library/microsoft.sqlserver.server.sqlpipe.executeandsend.aspx) exécute un passé dans `SqlCommand` objet et renvoient les résultats à l’application cliente.

> [!NOTE]
> Les objets de base de données managés sont mieux adaptées pour les procédures stockées et les fonctions qui utilisent la logique procédurale plutôt que d’une logique basée sur un jeu. Logique procédurale implique l’utilisation des jeux de données sur une ligne par ligne de base ou l’utilisation des données scalaires. Le `GetDiscontinuedProducts` n’implique la méthode que nous venons de créer, toutefois, aucune logique procédurale. Par conséquent, il serait dans l’idéal, être implémenté en tant qu’une procédure stockée T-SQL. Il est implémenté comme une procédure stockée managée pour illustrer les étapes nécessaires pour créer et déployer géré des procédures stockées.


## <a name="step-4-deploying-the-managed-stored-procedure"></a>Étape 4 : Déploiement de la procédure.

Ce code est terminé, nous sommes prêts à le déployer sur la base de données Northwind. Déploiement d’un projet SQL Server compile le code dans un assembly inscrit l’assembly avec la base de données et crée les objets correspondants dans la base de données, en les liant à des méthodes appropriées de l’assembly. Le jeu exact des tâches effectuées par l’option de déploiement est indiquée plus précisément à l’étape 13. Avec le bouton droit sur le `ManagedDatabaseConstructs` nom dans l’Explorateur de solutions du projet et choisissez l’option de déploiement. Toutefois, le déploiement échoue avec l’erreur suivante : syntaxe incorrecte près de 'Externe'. Vous devrez peut-être définir le niveau de compatibilité de la base de données actuelle une valeur plus élevée pour activer cette fonctionnalité. Consultez l’aide de la procédure stockée `sp_dbcmptlevel`.

Ce message d’erreur se produit lorsque vous tentez d’inscrire l’assembly avec la base de données Northwind. Pour inscrire un assembly avec une base de données SQL Server 2005, vous devez définir le niveau de compatibilité de base de données s à 90. Par défaut, les nouvelles bases de données SQL Server 2005 ont un niveau de compatibilité 90. Toutefois, les bases de données créées à l’aide de Microsoft SQL Server 2000 ont un niveau de compatibilité par défaut 80. Étant donné que la base de données Northwind a été initialement une base de données Microsoft SQL Server 2000, son niveau de compatibilité est actuellement définie sur 80 et doit donc être augmentée à 90 pour inscrire des objets de base de données managés.

Pour mettre à jour le niveau de compatibilité de base de données s, ouvrez une fenêtre de nouvelle requête dans Management Studio, puis entrez :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample4.sql)]

Cliquez sur l’icône exécuter dans la barre d’outils pour exécuter la requête ci-dessus.


[![Mettre à jour le niveau de compatibilité de base de données Northwind s](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image16.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image15.png)

**Figure 9**: mettre à jour la base de données Northwind s au niveau de compatibilité ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image17.png))


Après la mise à jour le niveau de compatibilité, redéployez le projet SQL Server. Cette fois le déploiement doit se terminer sans erreur.

Revenir à SQL Server Management Studio, avec le bouton droit sur la base de données Northwind dans l’Explorateur d’objets et cliquez sur Actualiser. Ensuite, explorez le dossier programmabilité et puis développez le dossier des assemblys. Comme le montre la Figure 10, la base de données Northwind inclut désormais l’assembly généré par le `ManagedDatabaseConstructs` projet.


![Le ManagedDatabaseConstructs Assembly est maintenant inscrit avec la base de données Northwind](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image18.png)

**La figure 10**: le `ManagedDatabaseConstructs` Assembly est maintenant inscrit avec la base de données Northwind


Également développer le dossier Stored Procedures. Vous y trouverez une procédure stockée nommée `GetDiscontinuedProducts`. Cette procédure stockée a été créée par le processus de déploiement pointe vers le `GetDiscontinuedProducts` méthode dans le `ManagedDatabaseConstructs` assembly. Lorsque le `GetDiscontinuedProducts` procédure stockée est exécutée, elle, à son tour, exécute le `GetDiscontinuedProducts` (méthode). Comme il s’agit d’une procédure stockée managée il ne peut pas être modifié via Management Studio (par conséquent, l’icône de verrou en regard du nom de la procédure stockée).


![La procédure stockée GetDiscontinuedProducts est répertoriée dans le dossier de procédures stockées](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image19.png)

**Figure 11**: le `GetDiscontinuedProducts` la procédure stockée est répertoriée dans le dossier de procédures stockées


Il existe toujours une entrave supplémentaire, nous devons résoudre avant que nous pouvons appeler la procédure stockée managée : la base de données est configuré pour empêcher l’exécution du code managé. Le vérifier en ouvrant une nouvelle fenêtre de requête et de l’exécution de la `GetDiscontinuedProducts` procédure stockée. Vous recevez le message d’erreur suivant : l’exécution du code utilisateur dans le .NET Framework est désactivée. Activez l’option de configuration de clr activé.

Pour examiner les informations de configuration de base de données s Northwind, entrez et exécutez la commande `exec sp_configure` dans la fenêtre de requête. Cela indique que le clr activé définition est actuellement défini sur 0.


[![Le clr activé paramètre est défini à 0](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image21.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image20.png)

**Figure 12**: le clr activé paramètre est actuellement définie sur 0 ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image22.png))


Notez que chaque paramètre de configuration dans la Figure 12 a quatre valeurs répertoriées avec celle-ci : la valeur minimale et valeurs maximales la configuration et valeurs d’exécution. Pour mettre à jour la valeur de configuration pour le paramètre clr activé, exécutez la commande suivante :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample5.sql)]

Si vous exécutez de nouveau la `exec sp_configure` vous verrez que l’instruction ci-dessus mis à jour la valeur de configuration clr activé paramètre s à 1, mais que la valeur d’exécution est toujours définie à 0. Pour que cette modification de configuration prennent effet, nous devons exécuter le [ `RECONFIGURE` commande](https://msdn.microsoft.com/library/ms176069.aspx), qui définit la valeur d’exécution à la valeur actuelle de la configuration. Entrez simplement `RECONFIGURE` dans la fenêtre de requête et cliquez sur l’icône exécuter dans la barre d’outils. Si vous exécutez `exec sp_configure` maintenant vous devez voir une valeur de 1 dans la configuration du paramètre de clr activé s et exécuter les valeurs.

La configuration du clr activé terminée, nous sommes prêts à exécuter la `GetDiscontinuedProducts` procédure stockée. Dans la fenêtre de requête, entrez et exécutez la commande `exec` `GetDiscontinuedProducts`. Le code managé correspondant dans l’appel à la procédure stockée entraîne le `GetDiscontinuedProducts` méthode à exécuter. Ce code émet une `SELECT` requête pour retourner tous les produits qui sont supprimées et retourne ces données à l’application appelante, qui est SQL Server Management Studio dans cette instance. Management Studio reçoit ces résultats et les affiche dans la fenêtre des résultats.


[![Le GetDiscontinuedProducts procédure stockée retourne tous les abandonné produits](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image24.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image23.png)

**Figure 13**: le `GetDiscontinuedProducts` stockées procédure retourne tous les abandonné produits ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image25.png))


## <a name="step-5-creating-managed-stored-procedures-that-accept-input-parameters"></a>Étape 5 : Création géré des procédures stockées qui acceptent des paramètres d’entrée

La plupart des requêtes et des procédures stockées que nous avons créé dans l’ensemble de ces didacticiels ont utilisé *paramètres*. Par exemple, dans le [création de nouvelles procédures stockées s DataSet typé TableAdapters](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-cs.md) didacticiel, nous avons créé une procédure stockée nommée `GetProductsByCategoryID` qui accepté un paramètre d’entrée nommé `@CategoryID`. La procédure stockée, retournée tous les produits dont `CategoryID` champ correspond à la valeur de l’élément `@CategoryID` paramètre.

Pour créer une procédure stockée managée qui accepte des paramètres d’entrée, vous devez simplement spécifier ces paramètres dans la définition de méthode s. Pour illustrer cela, s permettent d’ajouter une autre procédure stockée managée pour la `ManagedDatabaseConstructs` projet nommé `GetProductsWithPriceLessThan`. Cette procédure stockée managée accepte un paramètre d’entrée, en spécifiant un prix et retourne tous les produits dont `UnitPrice` champ est inférieure à la valeur du paramètre s.

Pour ajouter une nouvelle procédure stockée pour le projet, cliquez sur le `ManagedDatabaseConstructs` nom du projet et choisissez d’ajouter une nouvelle procédure stockée. Nommez le fichier `GetProductsWithPriceLessThan.cs`. Comme nous l’avons vu à l’étape 3, cela va créer un nouveau fichier de classe c# avec une méthode nommée `GetProductsWithPriceLessThan` placé dans le `partial` classe `StoredProcedures`.

Mise à jour la `GetProductsWithPriceLessThan` définition de méthode s afin qu’il accepte un [ `SqlMoney` ](https://msdn.microsoft.com/library/system.data.sqltypes.sqlmoney.aspx) paramètre d’entrée nommé `price` , écrivez le code pour exécuter et renvoyer les résultats de requête :


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample6.cs)]

Le `GetProductsWithPriceLessThan` définition de méthode s et le code ressemble étroitement à la définition et le code de la `GetDiscontinuedProducts` méthode créée à l’étape 3. Les seules différences sont qui le `GetProductsWithPriceLessThan` méthode accepte comme paramètre d’entrée (`price`), la `SqlCommand` requête de s inclut un paramètre (`@MaxPrice`), et un paramètre est ajouté à la `SqlCommand` s `Parameters` collection est et la valeur affectée à la `price` variable.

Après avoir ajouté ce code, redéployez le projet SQL Server. Ensuite, revenez à SQL Server Management Studio et actualiser le dossier Stored Procedures. Vous devez voir une nouvelle entrée `GetProductsWithPriceLessThan`. À partir d’une fenêtre de requête, entrez et exécutez la commande `exec GetProductsWithPriceLessThan 25`, qui sera liste tous les produits inférieur à 25 $, comme le montre la Figure 14.


[![Produits sous 25 $ sont affichés.](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image27.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image26.png)

**La figure 14**: produits sous 25 $ sont affichés ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image28.png))


## <a name="step-6-calling-the-managed-stored-procedure-from-the-data-access-layer"></a>Étape 6 : Appel de la procédure stockée managée à partir de la couche d’accès aux données

À ce stade, nous avons ajouté la `GetDiscontinuedProducts` et `GetProductsWithPriceLessThan` gérés des procédures stockées pour la `ManagedDatabaseConstructs` de projet et les avez inscrit avec la base de données Northwind SQL Server. Nous avons appelé également ces procédures stockées managées à partir de SQL Server Management Studio (voir Figure s 13 et 14). Dans l’ordre pour notre ASP.NET gérées l’application pour utiliser ces procédures stockées, toutefois, nous avons besoin pour les ajouter à l’accès aux données et les couches de logique métier dans l’architecture. Dans cette étape, nous allons ajouter deux nouvelles méthodes pour le `ProductsTableAdapter` dans les `NorthwindWithSprocs` DataSet typée, qui a été initialement créé dans le [création de nouvelles procédures stockées s DataSet typé TableAdapters](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-cs.md) didacticiel. À l’étape 7, nous allons ajouter des méthodes correspondantes à la couche BLL.

Ouvrez le `NorthwindWithSprocs` DataSet typé dans Visual Studio, puis en ajoutant une nouvelle méthode pour le `ProductsTableAdapter` nommé `GetDiscontinuedProducts`. Pour ajouter une nouvelle méthode à un TableAdapter, avec le bouton droit sur le nom de s TableAdapter dans le concepteur et choisissez l’option Ajouter une requête dans le menu contextuel.

> [!NOTE]
> Étant donné que nous avons déplacé la base de données Northwind à partir de la `App_Data` dossier à l’instance de base de données SQL Server 2005 Express Edition, il est impératif que la chaîne de connexion correspondante dans le fichier Web.config est mis à jour pour refléter cette modification. À l’étape 2, nous avons abordée mise à jour la `NORTHWNDConnectionString` valeur dans `Web.config`. Si vous avez oublié de rendre cette mise à jour, vous verrez le message d’erreur n’a pas pu ajouter la requête. Impossible de trouver la connexion `NORTHWNDConnectionString` pour l’objet `Web.config` dans une boîte de dialogue lorsque vous tentez d’ajouter une nouvelle méthode au TableAdapter. Pour résoudre cette erreur, cliquez sur OK, puis accédez à `Web.config` et mettre à jour le `NORTHWNDConnectionString` valeur comme indiqué dans l’étape 2. Essayez de vous rajoutez la méthode au TableAdapter. Cette fois, il doit fonctionner sans erreur.


Ajout d’une nouvelle méthode lance l’Assistant Configuration de requête TableAdapter, lequel nous avons utilisé plusieurs fois dans ces didacticiels. La première étape nous demande de spécifier comment le TableAdapter doit-il accéder à la base de données : via une instruction de SQL ad hoc ou une procédure stockée nouveau ou existante. Étant donné que nous avons déjà créé et inscrit le `GetDiscontinuedProducts` une procédure stockée managée avec la base de données, choisissez l’utiliser l’existant stocké l’option de procédure et d’accès suivant.


[![Choisissez l’utilisation existant de la procédure stockée Option](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image30.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image29.png)

**Figure 15**: choisissez utilisation existants procédure Option ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image31.png))


L’écran suivant vous invite à entrer us pour la procédure stockée qui qu'appelle la méthode. Choisissez le `GetDiscontinuedProducts` procédure stockée managée à partir de la liste déroulante et appuyez sur Suivant.


[![Sélectionnez le GetDiscontinuedProducts de procédure stockée managée](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image33.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image32.png)

**Figure 16**: sélectionnez le `GetDiscontinuedProducts` gérés la procédure stockée ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image34.png))


Nous sommes alors invités à spécifier si la procédure stockée retourne des lignes, une seule valeur ou rien du tout. Étant donné que `GetDiscontinuedProducts` retourne l’ensemble de lignes de SKU du produit, choisissez la première option (données tabulaires) et cliquez sur Suivant.


[![Sélectionnez l’Option de données tabulaires](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image36.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image35.png)

**Figure 17**: sélectionnez l’Option de données tabulaires ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image37.png))


L’écran finale de l’Assistant permet de spécifier les modèles d’accès aux données utilisées et les noms des méthodes qui en résulte. Laissez les cases à cocher activée et nom les méthodes `FillByDiscontinued` et `GetDiscontinuedProducts`. Cliquez sur Terminer pour terminer l’Assistant.


[![Nom de la FillByDiscontinued de méthodes et GetDiscontinuedProducts](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image39.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image38.png)

**La figure 18**: nommez les méthodes `FillByDiscontinued` et `GetDiscontinuedProducts` ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image40.png))


Répétez ces étapes pour créer des méthodes nommées `FillByPriceLessThan` et `GetProductsWithPriceLessThan` dans les `ProductsTableAdapter` pour la `GetProductsWithPriceLessThan` procédure stockée managée.

Figure 19 montre une capture d’écran du Concepteur de DataSet après avoir ajouté les méthodes à le `ProductsTableAdapter` pour le `GetDiscontinuedProducts` et `GetProductsWithPriceLessThan` gérés des procédures stockées.


[![Le ProductsTableAdapter inclut les nouvelles méthodes ajoutées dans cette étape](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image42.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image41.png)

**Figure 19**: le `ProductsTableAdapter` inclut les méthodes ajoutées dans cette étape ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image43.png))


## <a name="step-7-adding-corresponding-methods-to-the-business-logic-layer"></a>Étape 7 : Ajout de méthodes correspondantes à la couche de logique métier

Maintenant que nous avons mis à jour de la couche d’accès aux données pour inclure des méthodes pour appeler les procédures stockées managées ajoutés aux étapes 4 et 5, nous devons ajouter des méthodes correspondants à la couche de logique métier. Ajoutez les deux méthodes suivantes pour la `ProductsBLLWithSprocs` classe :


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample7.cs)]

Les deux méthodes simplement appeler la méthode correspondante de la couche DAL et retourner le `ProductsDataTable` instance. Le `DataObjectMethodAttribute` balisage au-dessus de chaque méthode provoque ces méthodes à inclure dans la liste déroulante dans l’onglet Sélection de l’Assistant Configurer la Source de données de s ObjectDataSource.

## <a name="step-8-invoking-the-managed-stored-procedures-from-the-presentation-layer"></a>Étape 8 : Appel managé stockée procédures à partir de la couche de présentation

Avec la logique métier et les couches d’accès aux données augmentée pour inclure la prise en charge pour appeler le `GetDiscontinuedProducts` et `GetProductsWithPriceLessThan` gérés des procédures stockées, nous pouvons maintenant afficher ces stocké les résultats de procédures via une page ASP.NET.

Ouvrez le `ManagedFunctionsAndSprocs.aspx` page dans le `AdvancedDAL` dossier et, dans la boîte à outils, faites glisser un GridView sur le concepteur. Définir le contrôle GridView s `ID` propriété `DiscontinuedProducts` et, à partir de sa balise active, la lier à un nouveau ObjectDataSource nommé `DiscontinuedProductsDataSource`. Configurer l’ObjectDataSource à extraire ses données à partir de la `ProductsBLLWithSprocs` classe s `GetDiscontinuedProducts` (méthode).


[![Configurer pour utiliser la classe ProductsBLLWithSprocs ObjectDataSource](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image45.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image44.png)

**Figure 20**: configurer l’ObjectDataSource à utiliser le `ProductsBLLWithSprocs` classe ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image46.png))


[![Choisissez la méthode GetDiscontinuedProducts dans la liste déroulante dans l’onglet Sélection](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image48.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image47.png)

**Figure 21**: choisissez la `GetDiscontinuedProducts` dans la liste déroulante sous l’onglet SELECT (méthode) ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image49.png))


Étant donné que cette grille doit être utilisé pour afficher uniquement les informations de produit, définir les listes déroulantes dans la mise à jour, insérer, supprimer des onglets à (None) et puis cliquez sur Terminer.

À la fin de l’Assistant, Visual Studio ajoute automatiquement un BoundField ou le CheckBoxField pour chaque champ de données dans le `ProductsDataTable`. Prenez un moment pour supprimer tous ces champs à l’exception de `ProductName` et `Discontinued`, à laquelle le GridView de point et le balisage déclaratif de ObjectDataSource s doit ressembler à ce qui suit :


[!code-aspx[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample8.aspx)]

Prenez un moment pour afficher cette page via un navigateur. Lorsque la page est visitée, les appels ObjectDataSource le `ProductsBLLWithSprocs` classe s `GetDiscontinuedProducts` (méthode). Comme nous l’avons vu à l’étape 7, cette méthode appelle vers le bas de la couche DAL s `ProductsDataTable` classe s `GetDiscontinuedProducts` (méthode), qui permet d’appeler le `GetDiscontinuedProducts` procédure stockée. Cette procédure stockée est une procédure stockée et exécute le code que nous avons créé à l’étape 3, retourner les produits abandonnés.

Les résultats retournés par la procédure stockée managée sont regroupés dans un `ProductsDataTable` par la couche DAL et retournées à la couche BLL, lequel puis les renvoie à la couche de présentation, où elles sont liées au GridView et affichées. Comme prévu, la grille répertorie les produits qui ont été abandonnées.


[![Les produits supprimées sont répertoriées.](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image51.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image50.png)

**La figure 22**: les produits supprimées sont répertoriées ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image52.png))


Pour plus d’exercice, ajoutez une zone de texte et un autre contrôle GridView à la page. Utiliser cette GridView pour afficher les produits inférieure à la quantité d’entrées dans la zone de texte en appelant le `ProductsBLLWithSprocs` classe s `GetProductsWithPriceLessThan` (méthode).

## <a name="step-9-creating-and-calling-t-sql-udfs"></a>Étape 9 : Création et appel de T-SQL UDF

Fonctions définies par l’utilisateur ou UDF, sont d’objets de base de l’étroitement reproduisent la sémantique des fonctions dans les langages de programmation. Comme une fonction en c#, UDF peuvent inclure un nombre variable de paramètres d’entrée et retournent une valeur d’un type particulier. Un fichier UDF peut retourner des données scalaires - une chaîne, un entier et ainsi de suite - ou des données tabulaires. Permettent de prendre un coup de œil les deux types d’UDF, en commençant par une fonction qui retourne un type de données scalaire s.

L’UDF suivante calcule la valeur estimée de l’inventaire d’un produit particulier. Il permet la prise de trois paramètres d’entrée - le `UnitPrice`, `UnitsInStock`, et `Discontinued` les valeurs pour un produit particulier - et retourne une valeur de type `money`. Il calcule la valeur estimée de l’inventaire en multipliant le `UnitPrice` par le `UnitsInStock`. Pour les articles abandonnés, cette valeur est réduit de moitié.


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample9.sql)]

Une fois cette UDF a été ajoutée à la base de données, il sont accessibles via Management Studio, développez le dossier programmabilité, puis les fonctions et les fonctions de valeur scalaire. Il peut être utilisé dans un `SELECT` requête comme suit :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample10.sql)]

J’ai ajouté le `udf_ComputeInventoryValue` UDF à la base de données Northwind ; Figure 23 illustre la sortie ci-dessus `SELECT` de requête lors de son affichage dans Management Studio. Notez également que l’UDF est répertorié sous le dossier de fonctions de valeur scalaire dans l’Explorateur d’objets.


[![Chaque produit s valeurs en stock est répertorié.](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image54.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image53.png)

**Figure 23**: s chaque produit des valeurs de stock est répertorié ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image55.png))


UDF peuvent également retourner des données tabulaires. Par exemple, nous pouvons créer un fichier UDF qui renvoie les produits qui appartiennent à une catégorie particulière :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample11.sql)]

Le `udf_GetProductsByCategoryID` UDF accepte un `@CategoryID` paramètre d’entrée et retourne les résultats de l’objet `SELECT` requête. Une fois créé, cette fonction peut être référencée dans la `FROM` (ou `JOIN`) clause d’un `SELECT` requête. L’exemple suivant retourne le `ProductID`, `ProductName`, et `CategoryID` valeurs pour chacun des boissons.


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample12.sql)]

J’ai ajouté le `udf_GetProductsByCategoryID` UDF à la base de données Northwind ; Figure 24 montre la sortie de la commande ci-dessus `SELECT` de requête lors de son affichage dans Management Studio. Vous trouverez des UDF qui retournent des données tabulaires dans le dossier de fonctions Table s l’Explorateur d’objets.


[![Le ProductID, ProductName et CategoryID sont répertoriés pour chaque boissons](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image57.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image56.png)

**Figure 24**: le `ProductID`, `ProductName`, et `CategoryID` sont répertoriés pour chaque boisson ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image58.png))


> [!NOTE]
> Pour plus d’informations sur la création et l’utilisation des UDF, passez en revue [introduction sur les fonctions définies par l’utilisateur](http://www.sqlteam.com/item.asp?ItemID=1955). Consultez également [avantages et les fonctions de Drawbacks of User-Defined](http://www.samspublishing.com/articles/article.asp?p=31724&amp;rl=1).


## <a name="step-10-creating-a-managed-udf"></a>Étape 10 : Création d’un fichier UDF géré

Le `udf_ComputeInventoryValue` et `udf_GetProductsByCategoryID` UDF créés dans les exemples ci-dessus sont des objets de base de données de T-SQL. SQL Server 2005 prend également en charge UDF managés, qui peuvent être ajoutés à la `ManagedDatabaseConstructs` projet comme managé des procédures stockées à partir des étapes 3 et 5. Pour cette étape, permettent de s implémenter le `udf_ComputeInventoryValue` UDF dans du code managé.

Pour ajouter un code UDF géré pour le `ManagedDatabaseConstructs` de projet, avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et choisissez d’ajouter un nouvel élément. Sélectionnez le modèle défini par l’utilisateur à partir de la boîte de dialogue Ajouter un nouvel élément et nommez le nouveau fichier UDF `udf_ComputeInventoryValue_Managed.cs`.


[![Ajouter un nouveau code UDF géré au projet ManagedDatabaseConstructs](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image60.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image59.png)

**Figure 25**: ajouter un nouveau UDF géré pour le `ManagedDatabaseConstructs` projet ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image61.png))


Le modèle de fonction de définis par l’utilisateur crée un `partial` classe nommée `UserDefinedFunctions` avec une méthode dont le nom est le même que le nom de fichier s classe (`udf_ComputeInventoryValue_Managed`, dans cette instance). Cette méthode est décorée à l’aide de la [ `SqlFunction` attribut](https://msdn.microsoft.com/library/microsoft.sqlserver.server.sqlfunctionattribute.aspx), qui marque la méthode comme étant un fichier UDF géré.


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample13.cs)]

Le `udf_ComputeInventoryValue` méthode retourne actuellement un [ `SqlString` objet](https://msdn.microsoft.com/library/system.data.sqltypes.sqlstring.aspx) et n’accepte pas les paramètres d’entrée. Nous devons mettre à jour la définition de méthode afin qu’il accepte trois paramètres - d’entrée `UnitPrice`, `UnitsInStock`, et `Discontinued` - et retourne un `SqlMoney` objet. La logique de calcul de la valeur d’inventaire est identique à celle de T-SQL `udf_ComputeInventoryValue` UDF.


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample14.cs)]

Notez que les paramètres d’entrée de méthode s UDF sont de leurs types SQL correspondantes : `SqlMoney` pour le `UnitPrice` champ, [ `SqlInt16` ](https://msdn.microsoft.com/library/system.data.sqltypes.sqlint16.aspx) pour `UnitsInStock`, et [ `SqlBoolean` ](https://msdn.microsoft.com/library/system.data.sqltypes.sqlboolean.aspx) pour `Discontinued`. Ces types de données reflètent les types définis dans le `Products` table : le `UnitPrice` colonne est de type `money`, le `UnitsInStock` colonne de type `smallint`et le `Discontinued` colonne de type `bit`.

Le code commence par créer un `SqlMoney` instance nommée `inventoryValue` qui est assignée à la valeur 0. Le `Products` permet de table de base de données `NULL` des valeurs dans le `UnitsInPrice` et `UnitsInStock` colonnes. Par conséquent, nous devons d’abord pour vérifier si ces valeurs contiennent `NULL` s, ce qui nous faire la `SqlMoney` objet s [ `IsNull` propriété](https://msdn.microsoft.com/library/system.data.sqltypes.sqlmoney.isnull.aspx). Si les deux `UnitPrice` et `UnitsInStock` contenir non -`NULL` des valeurs, puis nous calculons la `inventoryValue` le produit des deux. Ensuite, si `Discontinued` a la valeur true, nous réduire de moitié la valeur.

> [!NOTE]
> Le `SqlMoney` objet permet uniquement de deux `SqlMoney` instances multiplication ensemble. Il n’autorise pas un `SqlMoney` instance par un nombre à virgule flottante littéral. Par conséquent, pour réduire de moitié `inventoryValue` nous multipliez-le par un nouveau `SqlMoney` instance qui a la valeur 0,5.


## <a name="step-11-deploying-the-managed-udf"></a>Étape 11 : Déploiement de l’UDF géré

Maintenant que que l’UDF managé a été créé, vous êtes prêt à déployer dans la base de données Northwind. Comme nous l’avons vu à l’étape 4, les objets gérés dans un projet SQL Server sont déployés en cliquant sur le nom du projet dans l’Explorateur de solutions et en choisissant l’option de déploiement dans le menu contextuel.

Une fois que vous avez déployé le projet, retournez à SQL Server Management Studio et actualisez le dossier de fonctions scalaires. Vous devez maintenant voir deux entrées :

- `dbo.udf_ComputeInventoryValue`-l’UDF T-SQL créé à l’étape 9, et
- `dbo.udf ComputeInventoryValue_Managed`-l’UDF géré créé à l’étape 10 que vous venez de déployer.

Pour tester cette UDF géré, exécutez la requête suivante à partir de Management Studio :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample15.sql)]

Cette commande utilise la `udf ComputeInventoryValue_Managed` UDF au lieu de T-SQL `udf_ComputeInventoryValue` UDF, mais la sortie est la même. Faire référence à la Figure 23 pour voir une capture d’écran de la sortie de s UDF.

## <a name="step-12-debugging-the-managed-database-objects"></a>Étape 12 : Déboguer les objets de base de données managés

Dans le [le débogage des procédures stockées](debugging-stored-procedures-cs.md) didacticiel évoqué les trois options pour le débogage SQL Server via Visual Studio : débogage Direct de base de données, le débogage de l’Application et le débogage à partir d’un projet SQL Server. Géré par base de données, les objets ne peuvent pas être déboguées via le débogage Direct de base de données, mais ils peuvent être débogués à partir d’une application cliente et directement à partir du projet SQL Server. Pour le débogage de travail, toutefois, la base de données SQL Server 2005 doit autoriser le débogage SQL/CLR. N’oubliez pas que, lorsque nous avons créé tout d’abord le `ManagedDatabaseConstructs` projet Visual Studio nous ont demandé si nous voulions activer (voir Figure 6 à l’étape 2) le débogage SQL/CLR. Ce paramètre peut être modifié en cliquant sur la base de données à partir de la fenêtre de l’Explorateur de serveurs.


![Vérifiez que la base de données autorise le débogage SQL/CLR](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image62.png)

**Figure 26**: Vérifiez que la base de données autorise le débogage SQL/CLR


Supposons que nous voulons déboguer le `GetProductsWithPriceLessThan` procédure stockée managée. Nous commence en définissant un point d’arrêt dans le code de la `GetProductsWithPriceLessThan` (méthode).


[![Définir un point d’arrêt dans la méthode GetProductsWithPriceLessThan](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image64.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image63.png)

**Figure 27**: définir un point d’arrêt dans le `GetProductsWithPriceLessThan` (méthode) ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image65.png))


Permettent de s d’abord examiner les objets de base de données managés à partir du projet SQL Server de débogage. Étant donné que notre Solution inclut deux projets : le `ManagedDatabaseConstructs` projet SQL Server, ainsi que notre site Web - pour déboguer à partir du projet SQL Server, nous avons besoin pour demander à Visual Studio à lancer le `ManagedDatabaseConstructs` projet lorsque nous démarrer le débogage de SQL Server. Cliquez sur le `ManagedDatabaseConstructs` projet dans l’Explorateur de solutions, puis choisissez le jeu en tant qu’option de projet de démarrage dans le menu contextuel.

Lorsque le `ManagedDatabaseConstructs` projet est lancé à partir du débogueur, il exécute les instructions SQL dans le `Test.sql` fichier, qui se trouve dans le `Test Scripts` dossier. Par exemple, pour tester le `GetProductsWithPriceLessThan` géré d’une procédure stockée, remplacer la `Test.sql` fichier de contenu avec l’instruction suivante, qui permet d’appeler le `GetProductsWithPriceLessThan` en passant de procédure stockée managée la `@CategoryID` valeur 14,95 :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample16.sql)]

Une fois que vous avez déjà entré le script ci-dessus dans `Test.sql`, démarrez le débogage en accédant au menu Déboguer et en choisissant Démarrer le débogage ou en appuyant sur F5 ou le vert lire l’icône dans la barre d’outils. Cela sera générer les projets dans la Solution, déployer les objets de base de données managés sur la base de données Northwind, puis exécutez le `Test.sql` script. À ce stade, le point d’arrêt est atteint et que nous pouvons parcourir le `GetProductsWithPriceLessThan` méthode, examinez les valeurs des paramètres d’entrée et ainsi de suite.


[![Le point d’arrêt dans la méthode GetProductsWithPriceLessThan a été atteinte.](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image67.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image66.png)

**Figure 28**: le point d’arrêt dans le `GetProductsWithPriceLessThan` méthode a été atteint ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image68.png))


Dans l’ordre pour un objet de base de données SQL doit être débogué via une application cliente, il est impératif que la base de données est configuré pour prendre en charge le débogage de l’application. Avec le bouton droit sur la base de données dans l’Explorateur de serveurs et assurez-vous que l’option de débogage de l’Application est activée. En outre, nous devons configurer l’application ASP.NET à intégrer avec le débogueur SQL et à désactiver le regroupement de connexions. Ces étapes ont été présentées en détail à l’étape 2 de la [le débogage des procédures stockées](debugging-stored-procedures-cs.md) didacticiel.

Une fois que vous avez configuré l’application ASP.NET et la base de données, définir le site Web ASP.NET en tant que projet de démarrage et démarrer le débogage. Si vous visitez une page qui appelle l’une de ces objets ayant un point d’arrêt, l’application s’arrête et le contrôle est activé le débogueur, où vous pouvez parcourir le code comme indiqué dans la Figure 28.

## <a name="step-13-manually-compiling-and-deploying-managed-database-objects"></a>Étape 13 : Compilation et déploiement gérés manuellement les objets de base de données

Projets SQL Server facilitent la création, compiler et déployer les objets de base de données managés. Malheureusement, les projets SQL Server ne sont disponibles dans les éditions Professional et les systèmes de l’équipe de Visual Studio. Si vous utilisez Visual Web Developer ou l’Édition Standard de Visual Studio et à utiliser des objets de base de données managés, vous devez créer manuellement et de les déployer. Cela implique quatre étapes :

1. Créer un fichier qui contient le code source pour l’objet de base de données managés,
2. Compilez l’objet dans un assembly,
3. Inscrire l’assembly avec la base de données SQL Server 2005, et
4. Créer un objet de base de données dans SQL Server qui pointe vers la méthode appropriée dans l’assembly.

Pour illustrer ces tâches, permettent de créer un nouveau s gérés procédure stockée qui retourne les produits dont `UnitPrice` est supérieure à une valeur spécifiée. Créer un nouveau fichier sur votre ordinateur nommé `GetProductsWithPriceGreaterThan.cs` et entrez le code suivant dans le fichier (vous pouvez utiliser Visual Studio, le bloc-notes ou un éditeur de texte pour y parvenir) :


[!code-csharp[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample17.cs)]

Ce code est presque identique à celui de la `GetProductsWithPriceLessThan` méthode créée à l’étape 5. Les seules différences sont les noms de méthode, le `WHERE` clause et le nom du paramètre utilisé dans la requête. Dans le `GetProductsWithPriceLessThan` (méthode), la `WHERE` clause lire : `WHERE UnitPrice < @MaxPrice`. Ici, dans `GetProductsWithPriceGreaterThan`, nous utilisons : `WHERE UnitPrice > @MinPrice` .

Nous devons maintenant compiler cette classe dans un assembly. À partir de la ligne de commande, accédez au répertoire où vous avez enregistré le `GetProductsWithPriceGreaterThan.cs` de fichiers et utiliser le compilateur c# (`csc.exe`) pour compiler le fichier de classe dans un assembly :


[!code-console[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample18.cmd)]

Si le dossier contenant `csc.exe` dans pas dans le système de s `PATH`, vous devrez référencer complet de son chemin d’accès, `%WINDOWS%\Microsoft.NET\Framework\version\`, comme suit :


[!code-console[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample19.cmd)]


[![Compiler GetProductsWithPriceGreaterThan.cs dans un Assembly](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image70.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image69.png)

**Figure 29**: compiler `GetProductsWithPriceGreaterThan.cs` dans un Assembly ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image71.png))


Le `/t` indicateur indique que le fichier de classe c# doit être compilé dans une DLL (plutôt que dans un fichier exécutable). Le `/out` indicateur spécifie le nom de l’assembly résultant.

> [!NOTE]
> Au lieu de la compilation de la `GetProductsWithPriceGreaterThan.cs` fichier de classe à partir de la ligne de commande que vous pouvez également utiliser [Visual c# Express Edition](https://msdn.microsoft.com/vstudio/express/visualcsharp/) ou créez un projet de bibliothèque de classes distinct dans l’Édition Standard de Visual Studio. S ren Jacob Lauritsen a fourni bien un projet de Visual c# Express Edition avec le code pour le `GetProductsWithPriceGreaterThan` procédure stockée et les deux géré des procédures stockées et UDF créé dans les étapes 3, 5 et 10. Projet de s S ren inclut également les commandes T-SQL nécessaires pour ajouter les objets de base de données correspondants.


Le code est compilé dans un assembly, nous sommes prêts à inscrire l’assembly dans la base de données SQL Server 2005. Cela peut être effectué via T-SQL, à l’aide de la commande `CREATE ASSEMBLY`, ou via SQL Server Management Studio. Vous permettre s à l’aide de Management Studio.

À partir de Management Studio, développez le dossier programmabilité dans la base de données Northwind. Un de ses sous-dossiers est assemblys. Pour ajouter manuellement un nouvel Assembly à la base de données, avec le bouton droit sur le dossier Assemblies et choisissez le nouvel Assembly dans le menu contextuel. Cette affiche la boîte de dialogue Assembly nouvelle zone (voir Figure 30). Cliquez sur le bouton Parcourir, sélectionnez le `ManuallyCreatedDBObjects.dll` assembly nous simplement compilé, puis cliquez sur OK pour ajouter l’Assembly à la base de données. Vous ne devriez pas voir le `ManuallyCreatedDBObjects.dll` assembly dans l’Explorateur d’objets.


[![Ajouter l’Assembly ManuallyCreatedDBObjects.dll à la base de données](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image73.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image72.png)

**La figure 30**: ajouter la `ManuallyCreatedDBObjects.dll` Assembly à la base de données ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image74.png))


![Le ManuallyCreatedDBObjects.dll est répertorié dans l’Explorateur d’objets](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image75.png)

**Figure 31**: le `ManuallyCreatedDBObjects.dll` est répertorié dans l’Explorateur d’objets


Pendant que nous avons ajouté l’assembly à la base de données Northwind, nous devons encore associer une procédure stockée avec la `GetProductsWithPriceGreaterThan` méthode dans l’assembly. Pour ce faire, ouvrez une nouvelle fenêtre de requête et exécutez le script suivant :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample20.sql)]

Cette opération crée une nouvelle procédure stockée dans la base de données Northwind nommé `GetProductsWithPriceGreaterThan` et l’associe à la méthode managée `GetProductsWithPriceGreaterThan` (qui se trouve dans la classe `StoredProcedures`, qui se trouve dans l’assembly `ManuallyCreatedDBObjects`).

Après avoir exécuté le script ci-dessus, actualisez le dossier de procédures stockées dans l’Explorateur d’objets. Vous devez voir une nouvelle entrée de la procédure stockée - `GetProductsWithPriceGreaterThan` -qui a une icône de verrou en regard de celui-ci. Pour tester cette procédure stockée, entrez et exécutez le script suivant dans la fenêtre de requête :


[!code-sql[Main](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/samples/sample21.sql)]

Comme le montre la Figure 32, la commande ci-dessus affiche des informations pour les produits avec un `UnitPrice` supérieur 24,95.


[![Le ManuallyCreatedDBObjects.dll est répertorié dans l’Explorateur d’objets](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image77.png)](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image76.png)

**La figure 32**: le `ManuallyCreatedDBObjects.dll` est répertorié dans l’Explorateur d’objets ([cliquez pour afficher l’image en taille réelle](creating-stored-procedures-and-user-defined-functions-with-managed-code-cs/_static/image78.png))


## <a name="summary"></a>Récapitulatif

Microsoft SQL Server 2005 fournit l’intégration avec le Common Language Runtime (CLR), ce qui permet aux objets de base de données à l’aide de code managé. Auparavant, ces objets de base de données peuvent uniquement être créés à l’aide de T-SQL, mais nous pouvons maintenant créer ces objets à l’aide de langages tels que c# de programmation .NET. Dans ce didacticiel, nous avons créé deux gérés les procédures stockées et une fonction définie par l’utilisateur managés.

S de type de projet SQL Server de Visual Studio facilite la création, la compilation et déploiement d’objets de base de données managés. En outre, elle offre une prise en charge du débogage enrichie. Toutefois, les types de projet SQL Server sont uniquement disponibles dans les éditions Professional et les systèmes de l’équipe de Visual Studio. Pour ceux à l’aide de Visual Web Developer ou l’Édition Standard de Visual Studio, la création, la compilation et les étapes de déploiement doit être effectuée manuellement, comme nous l’avons vu à l’étape 13.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Avantages et inconvénients des fonctions définies par l’utilisateur](http://www.samspublishing.com/articles/article.asp?p=31724&amp;rl=1)
- [Création d’objets SQL Server 2005 dans le Code managé](https://channel9.msdn.com/Showpost.aspx?postid=142413)
- [Création de déclencheurs à l’aide de Code managé dans SQL Server 2005](http://www.15seconds.com/issue/041006.htm)
- [Comment : Créer et exécuter un CLR SQL Server de procédure stockée](https://msdn.microsoft.com/library/5czye81z(VS.80).aspx)
- [Comment : Créer et exécuter une fonction définie par l’utilisateur de CLR SQL Server](https://msdn.microsoft.com/library/w2kae45k(VS.80).aspx)
- [Comment : Modifier le `Test.sql` Script à exécuter des objets SQL](https://msdn.microsoft.com/library/ms233682(VS.80).aspx)
- [Fonctions définies par l’introduction à l’utilisateur](http://www.sqlteam.com/item.asp?ItemID=1955)
- [Le Code managé et SQL Server 2005 (vidéo)](https://channel9.msdn.com/Showpost.aspx?postid=142413)
- [Référence Transact-SQL](https://msdn.microsoft.com/library/aa299742(SQL.80).aspx)
- [Procédure pas à pas : Création d’une procédure stockée dans le Code managé](https://msdn.microsoft.com/library/zxsa8hkf(VS.80).aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été S ren Jacob Lauritsen. En plus d’étudier de cet article, le S ren également créé le projet Visual c# Express inclus dans ce téléchargement de l’article %s pour compiler manuellement les objets de base de données managés. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](debugging-stored-procedures-cs.md)
[Suivant](creating-new-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md)
