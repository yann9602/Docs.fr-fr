---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/debugging-stored-procedures-vb
title: "Débogage des procédures stockées (VB) | Documents Microsoft"
author: rick-anderson
description: "Les éditions Professional de Studio et Team System Visual permettent de définir des points d’arrêt et l’étape les procédures stockées dans SQL Server, rendent le débogage stockées..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/03/2007
ms.topic: article
ms.assetid: 9ed8ccb5-5f31-4eb4-976d-cabf4b45ca09
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/debugging-stored-procedures-vb
msc.type: authoredcontent
ms.openlocfilehash: ad09847d828d02019a72e3022d035a8fbe921568
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="debugging-stored-procedures-vb"></a>Débogage des procédures stockées (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_74_VB.zip) ou [télécharger le PDF](debugging-stored-procedures-vb/_static/datatutorial74vb1.pdf)

> Les éditions Professional de Studio et Team System Visual vous permet de définir des points d’arrêt et l’étape les procédures stockées dans SQL Server, permettent de rendre le débogage des procédures stockées aussi simples que le débogage de code de l’application. Ce didacticiel illustre le débogage direct de base de données et le débogage des procédures stockées de l’application.


## <a name="introduction"></a>Introduction

Visual Studio fournit une riche expérience de débogage. Avec quelques séquences de touches ou des clics de souris, elle s possible d’utiliser des points d’arrêt pour arrêter l’exécution d’un programme et examiner son état et contrôle de flux. En même temps que le débogage de code d’application, Visual Studio offre la prise en charge pour le débogage des procédures stockées à partir de SQL Server. Tout comme les points d’arrêt peuvent être définies dans le code d’une classe code-behind ASP.NET ou une classe de la couche de logique métier, donc trop peuvent leur être placés dans des procédures stockées.

Dans ce didacticiel, nous examinerons pas à pas détaillé dans les procédures stockées à partir de l’Explorateur de serveurs dans Visual Studio, ainsi que la manière de définir des points d’arrêt qui sont atteints lorsque la procédure stockée est appelée à partir de l’application ASP.NET en cours d’exécution.

> [!NOTE]
> Malheureusement, les procédures stockées peuvent uniquement être effectuez pas à pas et de débogage dans les versions Professional et les systèmes de l’équipe de Visual Studio. Si vous utilisez Visual Web Developer ou la version standard de Visual Studio, vous avez la possibilité de lire en même temps que nous avançons dans les étapes nécessaires pour déboguer les procédures stockées, mais vous ne serez pas en mesure de répliquer ces étapes sur votre ordinateur.


## <a name="sql-server-debugging-concepts"></a>Concepts de débogage de SQL Server

Microsoft SQL Server 2005 a été conçu pour permettre l’intégration avec les [Common Language Runtime (CLR)](https://msdn.microsoft.com/netframework/aa497266.aspx), qui est le runtime utilisé par tous les assemblys .NET. Par conséquent, SQL Server 2005 prend en charge les objets de base de données managés. Autrement dit, vous pouvez créer des objets de base de données tels que des procédures stockées et les fonctions définies par l’utilisateur (UDF) en tant que méthodes dans une classe Visual Basic. Ainsi, ces procédures stockées et les UDF pour utiliser la fonctionnalité dans le .NET Framework et à partir de vos propres classes personnalisées. Bien entendu, SQL Server 2005 prend également en charge pour les objets de base de données de T-SQL.

SQL Server 2005 offre la prise en charge du débogage pour T-SQL et les objets de base de données managés. Toutefois, ces objets peuvent uniquement être débogués via les éditions de Visual Studio 2005 Professional et les systèmes de l’équipe. Dans ce didacticiel, nous allons examiner les objets de base de données débogage T-SQL. Le didacticiel suivant ressemble au débogage d’objets de base de données managés.

Le [vue d’ensemble de T-SQL et le débogage CLR dans SQL Server 2005](https://blogs.msdn.com/sqlclr/archive/2006/06/29/651644.aspx) une entrée de blog à partir de la [équipe d’intégration CLR de SQL Server 2005](https://blogs.msdn.com/sqlclr/default.aspx) met en évidence les trois façons de déboguer des objets de SQL Server 2005 à partir de Visual Studio :

- **Diriger le débogage de base de données (DDD)** : dans l’Explorateur de serveurs nous pouvons pas à pas détaillé n’importe quel objet de base de données T-SQL, tels que des procédures stockées et les fonctions. Nous allons examiner DDD à l’étape 1.
- **Le débogage d’application** -nous pouvons définir des points d’arrêt dans un objet de base de données et puis exécutez votre application ASP.NET. Lorsque l’objet de base de données est exécuté, le point d’arrêt est atteint et le contrôle est activé le débogueur. Notez qu’avec le débogage de l’application nous ne pouvons pas étape dans un objet de base de données à partir du code d’application. Nous devons explicitement définir des points d’arrêt dans les procédures stockées ou les UDF que nous voulons le débogueur s’arrête. Débogage de l’application est examiné en commençant à l’étape 2.
- **Débogage à partir d’un projet SQL Server** -éditions Visual Studio Professional et les systèmes de l’équipe incluent un type de projet SQL Server qui est couramment utilisé pour créer des objets de base de données managés. Nous allons nous intéresser à l’aide de projets SQL Server et le débogage de leur contenu dans le didacticiel suivant.

Visual Studio peut déboguer les procédures stockées sur des instances de SQL Server locaux et distants. Une instance de SQL Server locale est est installé sur le même ordinateur que Visual Studio. Si la base de données SQL Server que vous utilisez ne se trouve pas sur votre ordinateur de développement, il est considéré comme une instance distante. Pour ces didacticiels nous avons utilisé les instances locales de SQL Server. Débogage des procédures stockées sur une instance distante de SQL server nécessite davantage d’étapes de configuration que lorsque le débogage des procédures stockées sur une instance locale.

Si vous utilisez une instance de SQL Server locale, vous pouvez commencer à l’étape 1 et parcourez ce didacticiel jusqu'à la fin. Si vous utilisez une instance distante de SQL Server, toutefois, vous ne serez pour vous assurer que lorsque le débogage, vous devez êtes connecté à votre ordinateur de développement avec un compte d’utilisateur Windows qui dispose d’une connexion de SQL Server sur l’instance distante. Moveover, cette connexion de base de données et la connexion de base de données utilisé pour se connecter à la base de données à partir de l’application ASP.NET en cours d’exécution doit être membres de la `sysadmin` rôle. Consultez les objets de base de données de débogage T-SQL dans la section des Instances distantes à la fin de ce didacticiel pour plus d’informations sur la configuration de Visual Studio et SQL Server pour déboguer une instance distante.

Enfin, comprendre que la prise en charge pour les objets de base de données de T-SQL de débogage n’est pas en tant que fonctionnalités comme la prise en charge pour les applications .NET de débogage. Par exemple, les conditions de point d’arrêt et les filtres ne sont pas pris en charge, seul un sous-ensemble des fenêtres de débogage sont disponibles, vous ne pouvez pas utiliser Modifier & Continuer, la fenêtre exécution est rendue inutile et ainsi de suite. Consultez [Limitations sur les fonctionnalités et les commandes du débogueur](https://msdn.microsoft.com/library/ms165035(VS.80).aspx) pour plus d’informations.

## <a name="step-1-directly-stepping-into-a-stored-procedure"></a>Étape 1 : Directement pas à pas détaillé dans une procédure stockée

Visual Studio facilite le déboguer directement un objet de base de données. Permettent de s examiner comment utiliser la fonctionnalité de débogage de base de données directe (DDD) à parcourir le `Products_SelectByCategoryID` procédure stockée dans la base de données Northwind. Comme son nom l’indique, `Products_SelectByCategoryID` retourne des informations de produit pour une catégorie particulière ; il a été créé dans le [à l’aide des procédures stockées existantes pour s DataSet typé TableAdapters](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) didacticiel. Démarrez en accédant à l’Explorateur de serveurs, puis développez le nœud de base de données Northwind. Ensuite, explorez le dossier Stored Procedures, avec le bouton droit sur le `Products_SelectByCategoryID` procédure stockée et choisissez l’option étape dans une procédure stockée dans le menu contextuel. Ceci démarrera le débogueur.

Étant donné que la `Products_SelectByCategoryID` procédure stockée attend un `@CategoryID` paramètre d’entrée, nous devons fournir cette valeur. Entrez 1, ce qui retourne des informations sur les boissons.


![Utilisez la valeur 1 pour le @CategoryID paramètre](debugging-stored-procedures-vb/_static/image1.png)

**Figure 1**: utiliser la valeur 1 pour le `@CategoryID` paramètre


Après avoir fourni la valeur de le `@CategoryID` paramètre, la procédure stockée est exécutée. Au lieu de s’exécuter complètement, cependant, le débogueur s’arrête l’exécution à la première instruction. Notez la flèche jaune dans la marge, indiquant l’emplacement actuel dans la procédure stockée. Vous pouvez afficher et modifier des valeurs de paramètre via la fenêtre Espion ou en pointant sur le nom du paramètre dans la procédure stockée.


[![Le débogueur s’est arrêté à la première instruction de la procédure stockée](debugging-stored-procedures-vb/_static/image3.png)](debugging-stored-procedures-vb/_static/image2.png)

**Figure 2**: le débogueur s’est arrêté à la première instruction de la procédure stockée ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image4.png))


Pour parcourir l’instruction d’une procédure stockée à la fois, cliquez sur le bouton pas à pas principal dans la barre d’outils ou appuyez sur la touche F10. Le `Products_SelectByCategoryID` procédure stockée contienne un seul `SELECT` instruction, en appuyant sur F10 allez le survol de l’instruction unique et terminer l’exécution de la procédure stockée. À l’issue de la procédure stockée, sa sortie apparaît dans la fenêtre Sortie et le débogueur va se terminer.

> [!NOTE]
> Débogage T-SQL se produit au niveau de l’instruction ; Vous ne pouvez pas passer dans un `SELECT` instruction.


## <a name="step-2-configuring-the-website-for-application-debugging"></a>Étape 2 : Configuration du site Web pour le débogage de l’Application

Pendant le débogage d’une procédure stockée directement à partir de l’Explorateur de serveurs est pratique, dans de nombreux scénarios nous intéresse plus débogage de la procédure stockée lorsqu’elle est appelée à partir de votre application ASP.NET. Nous pouvons ajouter des points d’arrêt à une procédure stockée à partir de Visual Studio et puis démarrez le débogage de l’application ASP.NET. Lorsqu’une procédure stockée avec des points d’arrêt est appelée à partir de l’application, l’exécution s’arrête au point d’arrêt et nous pouvons afficher et modifier les valeurs de paramètre de procédure stockée s et parcourir ses États, comme nous l’avons fait à l’étape 1.

Avant que nous pouvons commencer le débogage des procédures stockées appelées à partir de l’application, nous devons indiquer à l’application web ASP.NET pour intégrer avec le débogueur SQL Server. Démarrez en cliquant sur le nom de site Web dans l’Explorateur de solutions (`ASPNET_Data_Tutorial_74_VB`). Choisissez l’option de Pages de propriétés dans le menu contextuel, sélectionnez l’élément Options de démarrage sur la gauche et cochez la case de SQL Server dans la section débogueurs (voir Figure 3).


[![Cochez la case du serveur SQL dans les Pages de propriétés d’Application s](debugging-stored-procedures-vb/_static/image6.png)](debugging-stored-procedures-vb/_static/image5.png)

**Figure 3**: Vérifiez la case à cocher du serveur SQL dans le s Application Pages de propriétés ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image7.png))


En outre, nous devons mettre à jour la chaîne de connexion de base de données utilisée par l’application de sorte que le regroupement de connexions est désactivé. Lorsqu’une connexion à une base de données est fermée, correspondants `SqlConnection` objet est placé dans un pool de connexions disponibles. Lorsque vous établissez une connexion à une base de données, un objet de connexion disponibles peuvent être récupérées à partir de ce pool plutôt que devoir créer et établir une nouvelle connexion. Cette mise en pool des objets de connexion est une amélioration des performances et est activé par défaut. Toutefois, lors du débogage vous souhaitez désactiver le regroupement de connexions, car l’infrastructure de débogage n’est pas correctement rétablie lorsque vous travaillez avec une connexion qui a été effectuée à partir du pool.

Le regroupement de connexions désactivée, de mise à jour la `NORTHWNDConnectionString` dans `Web.config` afin qu’il inclue le paramètre `Pooling=false` .


[!code-xml[Main](debugging-stored-procedures-vb/samples/sample1.xml)]

> [!NOTE]
> Une fois que vous avez terminé de débogage de SQL Server via l’application ASP.NET veillez à rétablir le regroupement de connexions en supprimant le `Pooling` définition à partir de la chaîne de connexion (ou en lui affectant `Pooling=true` ).


À ce stade, l’application ASP.NET a été configurée pour autoriser Visual Studio déboguer des objets de base de données SQL Server lorsqu’elle est appelée par l’application web. Il ne reste plus qu’à ajouter un point d’arrêt à une procédure stockée et démarrer le débogage !

## <a name="step-3-adding-a-breakpoint-and-debugging"></a>Étape 3 : Ajout d’un point d’arrêt et débogage

Ouvrez le `Products_SelectByCategoryID` procédure stockée et définissez un point d’arrêt au début de la `SELECT` instruction en cliquant dans la marge à l’emplacement approprié ou en plaçant votre curseur au début de la `SELECT` instruction et en appuyant sur F9. Comme l’illustre la Figure 4, le point d’arrêt apparaît sous la forme d’un cercle rouge dans la marge.


[![Définir un point d’arrêt dans le Products_SelectByCategoryID procédure stockée](debugging-stored-procedures-vb/_static/image9.png)](debugging-stored-procedures-vb/_static/image8.png)

**Figure 4**: définir un point d’arrêt dans le `Products_SelectByCategoryID` la procédure stockée ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image10.png))


Dans l’ordre pour un objet de base de données SQL doit être débogué via une application cliente, il est impératif que la base de données est configuré pour prendre en charge le débogage de l’application. Lorsque vous définissez tout d’abord un point d’arrêt, ce paramètre doit être automatiquement activé, mais il est préférable de procéder à une. Avec le bouton droit sur le `NORTHWND.MDF` nœud dans l’Explorateur de serveurs. Le menu contextuel doit inclure un menu activé appelé débogage de l’Application.


![Vérifiez que l’Option de débogage de l’Application est activée.](debugging-stored-procedures-vb/_static/image11.png)

**Figure 5**: Vérifiez que l’Option de débogage de l’Application est activée.


Le jeu de point d’arrêt et l’option de débogage de l’Application activée, nous sommes prêts déboguer la procédure stockée lorsqu’elle est appelée à partir de l’application ASP.NET. Démarrez le débogueur en accédant au menu Déboguer, et en choisissant Démarrer le débogage en appuyant sur F5 ou en cliquant sur le vert lire icône dans la barre d’outils. Cela démarre le débogueur et lancer le site Web.

Le `Products_SelectByCategoryID` procédure stockée a été créée dans le [à l’aide des procédures stockées existantes pour s DataSet typé TableAdapters](using-existing-stored-procedures-for-the-typed-dataset-s-tableadapters-vb.md) didacticiel. La page web correspondante (`~/AdvancedDAL/ExistingSprocs.aspx`) contient un GridView qui affiche les résultats retournés par cette procédure stockée. Visitez cette page via le navigateur. Une fois que la page, le point d’arrêt dans le `Products_SelectByCategoryID` procédure stockée est atteint et que le contrôle retourné à Visual Studio. Comme à l’étape 1, vous pouvez parcourir les instructions de s de procédure stockée et les afficher et modifier les valeurs de paramètre.


[![La Page ExistingSprocs.aspx affiche initialement les boissons](debugging-stored-procedures-vb/_static/image13.png)](debugging-stored-procedures-vb/_static/image12.png)

**Figure 6**: le `ExistingSprocs.aspx` Page affiche initialement les boissons ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image14.png))


[![La procédure stockée s point d’arrêt a été atteint.](debugging-stored-procedures-vb/_static/image16.png)](debugging-stored-procedures-vb/_static/image15.png)

**Figure 7**: s de la procédure stockée le point d’arrêt a été atteint ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image17.png))


En tant que la fenêtre Espion dans la Figure 7 montre, la valeur de le `@CategoryID` paramètre est 1. C’est parce que le `ExistingSprocs.aspx` page affiche initialement les produits dans la catégorie boissons, ce qui a un `CategoryID` la valeur 1. Dans la liste déroulante, choisissez une autre catégorie. Cela provoque une publication (postback) et réexécute la `Products_SelectByCategoryID` procédure stockée. Le point d’arrêt est atteint, mais cette fois le `@CategoryID` valeur du paramètre s reflète l’élément de liste déroulante sélectionnée s `CategoryID`.


[![Choisissez une autre catégorie dans la liste déroulante](debugging-stored-procedures-vb/_static/image19.png)](debugging-stored-procedures-vb/_static/image18.png)

**Figure 8**: choisissez une autre catégorie dans la liste déroulante ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image20.png))


[![Le @CategoryID paramètre reflète la catégorie sélectionnée à partir de la Page Web](debugging-stored-procedures-vb/_static/image22.png)](debugging-stored-procedures-vb/_static/image21.png)

**Figure 9**: le `@CategoryID` paramètre reflète la catégorie sélectionnée dans la Page Web ([cliquez pour afficher l’image en taille réelle](debugging-stored-procedures-vb/_static/image23.png))


> [!NOTE]
> Si le point d’arrêt dans le `Products_SelectByCategoryID` procédure stockée n’est pas atteint lors de la visite du `ExistingSprocs.aspx` , assurez-vous que la case à cocher de SQL Server a été vérifiée dans la section débogueurs de l’application ASP.NET s de Page de propriétés, que le regroupement de connexions a été désactivé, et que la base de données s option de débogage de l’Application est activée. Si vous re toujours des difficultés, redémarrez Visual Studio et réessayez.


## <a name="debugging-t-sql-database-objects-on-remote-instances"></a>Débogage des objets de base de données de T-SQL sur des Instances distantes

Débogage d’objets de base de données via Visual Studio est assez simple lorsque l’instance de base de données SQL Server se trouve sur le même ordinateur que Visual Studio. Toutefois, si SQL Server et Visual Studio se trouvent sur des ordinateurs différents alors une configuration minutieuse est requise afin que tout fonctionne correctement. Il existe deux principales tâches que se posent :

- Assurez-vous que la connexion utilisée pour se connecter à la base de données via ADO.NET appartienne à la `sysadmin` rôle.
- Assurez-vous que le compte d’utilisateur Windows utilisé par Visual Studio sur l’ordinateur de développement est un compte de connexion SQL Server valid qui appartient à la `sysadmin` rôle.

La première étape est relativement simple. Tout d’abord, identifiez le compte d’utilisateur utilisé pour se connecter à la base de données à partir de l’application ASP.NET et puis, à partir de SQL Server Management Studio, ajoutez ce compte de connexion à la `sysadmin` rôle.

La seconde tâche requiert que le compte d’utilisateur Windows que vous permet de déboguer l’application soit une connexion valide sur la base de données distante. Toutefois, sans doute le compte Windows que vous ouvert une session votre station de travail n’est pas une connexion valide sur SQL Server. Au lieu d’ajouter votre compte de connexion spécifique à SQL Server, un meilleur choix serait pour désigner un compte d’utilisateur Windows en tant que le compte de débogage de SQL Server. Ensuite, pour déboguer les objets de base de données d’une instance distante de SQL Server, vous exécuteriez Visual Studio à l’aide de ce informations d’identification de compte s de la connexion Windows.

Un exemple doit aider à clarifier les choses. Imaginez qu’il existe un compte Windows nommé `SQLDebug` au sein du domaine Windows. Ce compte devra être ajouté à l’instance distante de SQL Server en tant qu’une connexion valide et en tant que membre de la `sysadmin` rôle. Ensuite, pour déboguer l’instance distante de SQL Server à partir de Visual Studio, nous devez exécuter Visual Studio en tant que la `SQLDebug` utilisateur. Cette opération peut être effectuée en vous connectant en dehors de la station de travail, connectant en tant que `SQLDebug`, et ensuite lancer Visual Studio, mais une approche plus simple pour se connecter à notre station de travail à l’aide de ses propres informations d’identification, puis utiliser `runas.exe` pour lancer Visual Studio en tant que le `SQLDebug` utilisateur. `runas.exe`permet à une application spécifique doit être exécuté sous la forme d’un compte d’utilisateur différent. Pour lancer Visual Studio en tant que `SQLDebug`, vous pouvez entrer l’instruction suivante à la ligne de commande :


[!code-console[Main](debugging-stored-procedures-vb/samples/sample2.cmd)]

Pour plus d’informations sur ce processus, consultez [William R. Vaughn](http://betav.com/BLOG/billva/) s *Hitchhiker s s Guide sur Visual Studio et SQL Server, Édition septième* ainsi que [comment faire : définir des autorisations SQL Server pour le débogage](https://msdn.microsoft.com/library/w1bhybwz(VS.80).aspx).

> [!NOTE]
> Si votre ordinateur de développement s’exécute Windows XP Service Pack 2, vous devez configurer le pare-feu pour autoriser le débogage distant. [La procédure pour : activer le débogage de SQL Server 2005](https://msdn.microsoft.com/library/s0fk6z6e(VS.80).aspx) article note que cela implique deux étapes : (a) sur l’ordinateur hôte Visual Studio, vous devez ajouter `Devenv.exe` à la liste des Exceptions et ouvrir le port TCP 135 ; et (b) sur l’ordinateur distant (SQL), vous devez ouvrir TCP 135 de port et ajoutez `sqlservr.exe` à la liste des Exceptions. Si votre stratégie de domaine requiert que la communication réseau via IPSec, vous devez ouvrir les ports UDP 4500 et UDP 500.


## <a name="summary"></a>Récapitulatif

En plus de la prise en charge du débogage pour le code d’application .NET, Visual Studio fournit également une variété d’options de débogage pour SQL Server 2005. Dans ce didacticiel, nous avons étudié deux de ces options : débogage Direct de base de données et de débogage de l’application. Pour déboguer directement un objet de base de données T-SQL, recherchez l’objet via l’Explorateur de serveurs, puis avec le bouton droit dessus et choisissez pas à pas détaillé. Cela démarre le débogueur et s’arrête à la première instruction dans l’objet de base de données, à partir de laquelle vous pouvez parcourir les instructions de s d’objet et de la vue et modifier les valeurs de paramètre. À l’étape 1, nous avons utilisé cette approche à parcourir le `Products_SelectByCategoryID` procédure stockée.

Débogage de l’application permet de points d’arrêt à définir directement dans les objets de base de données. Lorsqu’un objet de base de données avec des points d’arrêt est appelé à partir d’une application cliente (par exemple, une application de web ASP.NET), le programme s’arrête comme adopte par le débogueur. Débogage de l’application est utile, car elle indique plus clairement l’action de l’application provoque un objet de base de données particulière à appeler. Toutefois, il requiert un peu plus d’une configuration et le programme d’installation de débogage Direct de base de données.

Les objets de base de données peuvent également être débogués via des projets SQL Server. Nous allons nous intéresser à l’aide de projets SQL Server et comment les utiliser pour créer et déboguer gérés des objets de base de données dans le didacticiel suivant.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](protecting-connection-strings-and-other-configuration-information-vb.md)
[Suivant](creating-stored-procedures-and-user-defined-functions-with-managed-code-vb.md)
