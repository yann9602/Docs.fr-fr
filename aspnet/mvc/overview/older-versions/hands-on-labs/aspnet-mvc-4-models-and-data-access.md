---
uid: mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-models-and-data-access
title: "Accès aux données et les modèles ASP.NET MVC 4 | Documents Microsoft"
author: rick-anderson
description: "Remarque : Cet atelier pratique suppose que vous avez une connaissance élémentaire d’ASP.NET MVC. Si vous n’avez pas utilisé ASP.NET MVC avant, nous vous recommandons de vous pencher sur ASP.NET MVC 4..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 634ea84b-f904-4afe-b71b-49cccef4d9cc
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-models-and-data-access
msc.type: authoredcontent
ms.openlocfilehash: bf4bb5c6f9ad8339c3597b0d6666c7077ac476e0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-4-models-and-data-access"></a>Accès aux données et les modèles ASP.NET MVC 4
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> [!NOTE]
> Cet atelier pratique suppose que vous avez une connaissance élémentaire des **ASP.NET MVC**. Si vous n’avez pas utilisé **ASP.NET MVC** auparavant, nous vous recommandons de dépasser **notions de base ASP.NET MVC 4** atelier pratique.
> 
> Ce laboratoire présente les améliorations et nouvelles fonctionnalités décrites précédemment en appliquant les modifications mineures à un exemple d’application Web dans le dossier Source.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://www.microsoft.com/en-us/download/29843](https://www.microsoft.com/en-us/download/29843).


Dans **notions de base ASP.NET MVC** atelier pratique, vous avez été passant données codées en dur à partir des contrôleurs pour les modèles d’affichage. Toutefois, pour générer une application Web réelle, vous pouvez souhaiter utiliser une base de données réel.

Cet atelier pratique vous montrerai d’utiliser un moteur de base de données afin de stocker et récupérer les données nécessaires pour l’application de magasin de musique. Pour ce faire, vous démarrez avec une base de données, créer l’Entity Data Model à partir de celui-ci. Tout au long de cet atelier, vous répondra le **Database First** approche, ainsi que les **Code First** approche.

Toutefois, vous pouvez également utiliser le **Model First** approche, créer le même modèle à l’aide des outils, puis générer la base de données à partir de celui-ci.

![Vs premier de la base de données. Modèle premier](aspnet-mvc-4-models-and-data-access/_static/image1.png "vs première base de données. Tout d’abord de modèle")

*Vs premier de la base de données. Tout d’abord de modèle*

Après avoir généré le modèle, vous allez apporter les ajustements appropriés dans le StoreController pour fournir des affichages du magasin avec les données provenant de la base de données, au lieu d’utiliser des données codées en dur. Vous devrez pas modifiez les modèles d’affichage, car le StoreController retournera les ViewModel mêmes pour les modèles d’affichage, bien que cette fois les données proviennent la base de données.

**La première approche de Code**

L’approche Code First permet de définir le modèle à partir du code sans générer des classes qui sont généralement associés avec l’infrastructure.

Dans le code en premier lieu, les objets de modèle sont définis avec POCOs, &quot;Plain Old CLR Objects&quot;. POCOs sont des classes de bruts simples qui n’ont aucun héritage et n’implémentent pas d’interfaces. Nous pouvons générer automatiquement la base de données à partir de celles-ci, ou nous pouvons utiliser une base de données et générer le mappage de classe à partir du code.

Les avantages de l’utilisation de cette approche est que le modèle reste indépendant de l’infrastructure de persistance (dans ce cas, Entity Framework), comme les classes POCOs ne sont pas associées à l’infrastructure de mappage.

> [!NOTE]
> Ce laboratoire repose sur ASP.NET MVC 4 et une version de l’application d’exemple de magasin de musique personnalisé et réduite pour s’ajuster à uniquement les fonctionnalités présentées dans cet atelier pratique.
> 
> Si vous souhaitez Explorer l’ensemble **magasin de musique** application du didacticiel vous le trouverez dans [magasin de musique MVC](https://github.com/evilDave/MVC-Music-Store).


<a id="Prerequisites"></a>

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Vous devez disposer des éléments suivants pour effectuer ce laboratoire :

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe A](#AppendixA) pour obtenir des instructions sur l’installation).

<a id="Setup"></a>

<a id="Setup"></a>
### <a name="setup"></a>Installation

**L’installation des extraits de Code**

Pour plus de commodité, une grande partie du code que vous gérez le long de cet atelier est disponible sous les extraits de code Visual Studio. Pour installer les extraits de code exécuter **.\Source\Setup\CodeSnippets.vsi** fichier.

Si vous n’êtes pas familiarisé avec les extraits de Code Visual Studio et vous souhaitez savoir comment les utiliser, vous pouvez faire référence à l’annexe de ce document &quot; [extraits de Code à l’aide de l’annexe c :](#AppendixC)&quot;.

* * *

<a id="Exercises"></a>

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique est constitué par les exercices suivants :

1. [Exercice 1 : Ajout d’une base de données](#Exercise1)
2. [Exercice 2 : Création d’une base de données à l’aide de Code First](#Exercise2)
3. [Exercice 3 : Interrogation de la base de données avec des paramètres](#Exercise3)

> [!NOTE]
> Chaque exercice est accompagné par un **fin** dossier qui contient la solution résultante, vous devez obtenir après avoir effectué les exercices. Si vous avez besoin d’aide fonctionne via les exercices, vous pouvez utiliser cette solution comme guide.


Durée estimée pour effectuer ce laboratoire : **35 minutes**.

<a id="Exercise1"></a>

<a id="Exercise_1_Adding_a_Database"></a>
### <a name="exercise-1-adding-a-database"></a>Exercice 1 : Ajout d’une base de données

Dans cet exercice, vous allez apprendre à ajouter une base de données avec les tables de l’application MusicStore à la solution pour pouvoir utiliser ses données. Une fois la base de données est généré avec le modèle et ajouté à la solution, vous allez modifier la classe StoreController pour fournir le modèle d’affichage avec les données provenant de la base de données, au lieu d’utiliser des valeurs codées en dur.

<a id="Ex1Task1"></a>

<a id="Task_1_-_Adding_a_Database"></a>
#### <a name="task-1---adding-a-database"></a>Tâche 1 : ajout d’une base de données

Dans cette tâche, vous allez ajouter une base de données déjà créée avec les tables principales de l’application MusicStore à la solution.

1. Ouvrez le **commencer** solution situé dans **début/AddingADatabaseDBFirst-Ex1/Source/** dossier.

    1. Vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ajouter **MvcMusicStore** fichier de base de données. Dans cet atelier pratique, vous allez utiliser une base de données déjà créée appelé **MvcMusicStore.mdf**. Pour ce faire, cliquez sur **application\_données** dossier, pointez sur **ajouter** puis cliquez sur **élément existant**. Accédez à **\Source\Assets** et sélectionnez le **MvcMusicStore.mdf** fichier.

    ![Ajout d’un élément existant](aspnet-mvc-4-models-and-data-access/_static/image2.png "Ajout d’un élément existant")

    *Ajout d’un élément existant*

    ![Fichier de base de données MvcMusicStore.mdf](aspnet-mvc-4-models-and-data-access/_static/image3.png "fichier de base de données MvcMusicStore.mdf")

    *Fichier de base de données MvcMusicStore.mdf*

    La base de données a été ajouté au projet. Même lorsque la base de données se trouve à l’intérieur de la solution, vous pouvez interroger et mettre à jour comme il était hébergé sur un serveur de base de données différente.

    ![MvcMusicStore de base de données dans l’Explorateur de solutions](aspnet-mvc-4-models-and-data-access/_static/image4.png "MvcMusicStore de base de données dans l’Explorateur de solutions")

    *MvcMusicStore de base de données dans l’Explorateur de solutions*
3. Vérifiez la connexion à la base de données. Pour ce faire, double-cliquez sur **MvcMusicStore.mdf** pour établir une connexion.

    ![Connexion à MvcMusicStore.mdf](aspnet-mvc-4-models-and-data-access/_static/image5.png "la connexion à MvcMusicStore.mdf")

    *Connexion à MvcMusicStore.mdf*

<a id="Ex1Task2"></a>

<a id="Task_2_-_Creating_a_Data_Model"></a>
#### <a name="task-2---creating-a-data-model"></a>Tâche 2 : création d’un modèle de données

Dans cette tâche, vous allez créer un modèle de données pour interagir avec la base de données ajouté dans la tâche précédente.

1. Créer un modèle de données qui représente la base de données. Pour ce faire, dans le droit de l’Explorateur de solutions le **modèles** dossier, pointez sur **ajouter** puis cliquez sur **un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez le **données** modèle, puis le **ADO.NET Entity Data Model** élément. Modifier le nom du modèle de données pour **StoreDB.edmx** et cliquez sur **ajouter**.

    ![Ajout de StoreDB ADO.NET Entity Data Model](aspnet-mvc-4-models-and-data-access/_static/image6.png "Ajout StoreDB ADO.NET Entity Data Model")

    *Ajout de StoreDB ADO.NET Entity Data Model*
2. Le **Assistant Entity Data Model** s’affiche. Cet Assistant vous guidera tout au long de la création de la couche de modèle. Étant donné que le modèle doit être créé en fonction de la recentyl de base de données existante ajouté, sélectionnez **générer à partir de la base de données** et cliquez sur **suivant**.

    ![Choisir le contenu du modèle](aspnet-mvc-4-models-and-data-access/_static/image7.png "en choisissant le contenu du modèle")

    *Choisir le contenu du modèle*
3. Étant donné que vous générez un modèle à partir d’une base de données, vous devez spécifier la connexion à utiliser. Cliquez sur **nouvelle connexion**.
4. Sélectionnez **fichier de base de données Microsoft SQL Server** et cliquez sur **continuer**.

    ![Choisir les sources de données](aspnet-mvc-4-models-and-data-access/_static/image8.png "choisir les sources de données")

    *Choisissez de dialogue source de données*
5. Cliquez sur **Parcourir** et sélectionnez la base de données **MvcMusicStore.mdf** vous avez localisé à le **application\_données** dossier puis cliquez sur **OK**.

    ![Propriétés de connexion](aspnet-mvc-4-models-and-data-access/_static/image9.png "propriétés de connexion")

    *Propriétés de connexion*
6. La classe générée doit avoir le même nom que la chaîne de connexion d’entité, donc modifier son nom à **MusicStoreEntities** et cliquez sur **suivant**.

    ![Choix de la connexion de données](aspnet-mvc-4-models-and-data-access/_static/image10.png "choix de la connexion de données")

    *Choix de la connexion de données*
7. Choisissez les objets de base de données à utiliser. Comme le modèle d’entité utilisera uniquement les tables de la base de données, sélectionnez le **Tables** option et vous assurer que le **inclure les colonnes clés étrangères dans le modèle** et **mettre au pluriel ou au singulier généré noms d’objets** options sont également sélectionnées. Modifier le modèle Namespace pour **MvcMusicStore.Model** et cliquez sur **Terminer**.

    ![Choisir les objets de base de données](aspnet-mvc-4-models-and-data-access/_static/image11.png "en choisissant les objets de base de données")

    *Choisir les objets de base de données*

    > [!NOTE]
    > Si une boîte de dialogue Avertissement de sécurité s’affiche, cliquez sur **OK** pour exécuter le modèle et de générer les classes pour les entités du modèle.
8. Un diagramme d’entité pour la base de données s’affiche pendant la création d’une classe distincte qui mappe chaque table à la base de données. Par exemple, le **Albums** table est représentée par une **Album** (classe), où chaque colonne dans la table mappera à une propriété de classe. Cela vous permettra d’interroger et manipuler des objets qui représentent des lignes dans la base de données.

    ![Diagramme de l’entité](aspnet-mvc-4-models-and-data-access/_static/image12.png "diagramme d’entité")

    *Diagramme d’entité*

> [!NOTE]
> Les modèles T4 (.tt) exécuter du code pour générer les classes d’entités et remplacement les classes existantes portant le même nom. Dans cet exemple, les classes &quot;Album&quot;, &quot;Genre&quot; et &quot;artiste&quot; ont été remplacés par le code généré.


<a id="Ex1Task3"></a>

<a id="Task_3_-_Building_the_Application"></a>
#### <a name="task-3---building-the-application"></a>Tâche 3 : génération de l’Application

Dans cette tâche, vous allez vérifier que, bien que la génération du modèle ont supprimé le **Album**, **Genre** et **artiste** classes de modèle, le projet est généré avec succès à l’aide de les nouvelles classes de modèle de données.

1. Générez le projet en sélectionnant le **générer** élément de menu, puis **MvcMusicStore de Build**.

    ![Génération du projet](aspnet-mvc-4-models-and-data-access/_static/image13.png "génération du projet")

    *Génération du projet*
2. Le projet est généré avec succès. Pourquoi toujours fonctionne-t-il ? Cela fonctionne parce que les tables de base de données ont des champs qui incluent les propriétés que vous utilisiez dans les classes supprimés **Album** et **Genre**.

    ![Réussi des builds](aspnet-mvc-4-models-and-data-access/_static/image14.png "Builds a réussi")

    *Réussi des builds*
3. Tandis que le concepteur affiche les entités sous forme de diagramme, elles sont vraiment des classes c#. Développez le **StoreDB.edmx** nœud dans l’Explorateur de solutions, puis **StoreDB.tt**, vous verrez les nouvelles entités générées.

    ![Les fichiers générés](aspnet-mvc-4-models-and-data-access/_static/image15.png "les fichiers générés")

    *Fichiers générés*

<a id="Ex1Task4"></a>

<a id="Task_4_-_Querying_the_Database"></a>
#### <a name="task-4---querying-the-database"></a>Tâche 4 : interrogation de la base de données

Dans cette tâche, la classe StoreController met à jour, afin que, au lieu d’utiliser les données codées en dur, il interrogera la base de données pour récupérer les informations.

1. Ouvrez **Controllers\StoreController.cs** et ajoutez le champ suivant à la classe pour contenir une instance de la **MusicStoreEntities** classe, nommée **storeDB**:

    (Code d’extrait de code - *modèles et accès aux données - Ex1 storeDB*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample1.cs)]
2. Le **MusicStoreEntities** classe expose une propriété de collection pour chaque table dans la base de données. Mise à jour **Parcourir** méthode d’action pour récupérer un Genre avec tous les **Albums**.

    (Code d’extrait de code - *modèles et accès aux données - Ex1 magasin Parcourir*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample2.cs)]

    > [!NOTE]
    > Vous utilisez une fonctionnalité de .NET appelée **LINQ** (language integrated query) pour écrire des expressions de requête fortement typée par rapport à ces regroupements - qui exécutera le code par rapport à la base de données et retourner des objets que vous pouvez programmer par rapport aux.
    > 
    > Pour plus d’informations à propos de LINQ, visitez le [site msdn](https://msdn.microsoft.com/en-us/library/bb397926&amp;#040;v=vs.110&amp;#041;.aspx).
3. Mise à jour **Index** pour récupérer tous les genres des méthode d’action.

    (Code d’extrait de code - *modèles et accès aux données - Index de magasin Ex1*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample3.cs)]
4. Mise à jour **Index** méthode d’action pour récupérer tous les genres et de transformer une liste à la collection.

    (Code d’extrait de code - *modèles et accès aux données - Ex1 magasin GenreMenu*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample4.cs)]

<a id="Ex1Task5"></a>

<a id="Task_5_-_Running_the_Application"></a>
#### <a name="task-5---running-the-application"></a>Tâche 5 : exécution de l’Application

Dans cette tâche, vous allez vérifier que la page d’Index du magasin s’affichent désormais les Genres stockées dans la base de données au lieu de ceux qui sont codées. Il est inutile de modifier le modèle d’affichage, car le **StoreController** retourne les mêmes entités comme avant, bien que cette fois les données proviennent la base de données.

1. Régénérez la solution et appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Vérifiez que le menu de **Genres** n’est plus une liste codée en dur, et les données sont récupérées directement à partir de la base de données.

    ![BrowsingGenresFromDataBase](aspnet-mvc-4-models-and-data-access/_static/image16.png)

    *Genres de navigation à partir de la base de données*
3. Maintenant, accédez à n’importe quel genre et vérifier que les albums sont remplis à partir de la base de données.

    ![Exploration des Albums à partir de la base de données](aspnet-mvc-4-models-and-data-access/_static/image17.png "Albums de navigation à partir de la base de données")

    *Exploration des Albums à partir de la base de données*

<a id="Exercise2"></a>

<a id="Exercise_2_Creating_a_Database_Using_Code_First"></a>
### <a name="exercise-2-creating-a-database-using-code-first"></a>Exercice 2 : Création d’une base de données à l’aide de Code tout d’abord

Dans cet exercice, vous allez apprendre comment utiliser l’approche Code First pour créer une base de données avec les tables de l’application MusicStore et comment accéder à ses données.

Une fois que le modèle est généré, vous allez modifier le StoreController pour fournir le modèle d’affichage avec les données provenant de la base de données, au lieu d’utiliser des valeurs codées en dur.

> [!NOTE]
> Si vous avez terminé l’exercice 1 et que vous avez déjà travaillé avec la première base de données approche, vous allez maintenant apprendre à obtenir les mêmes résultats avec un autre processus. Les tâches qui sont en commun avec exercice 1 ont été marquées pour faciliter la lecture. Si vous n’avez pas effectué exercice 1, mais pour en savoir l’approche Code First, vous pouvez démarrer à partir de cet exercice et obtenir une couverture complète de la rubrique.


<a id="Ex2Task1"></a>

<a id="Task_1_-_Populating_Sample_Data"></a>
#### <a name="task-1---populating-sample-data"></a>Tâche 1 - remplissage exemples de données

Dans cette tâche, vous allez renseigner la base de données avec des exemples de données lors de sa création exécuter initialement à l’aide de Code en premier.

1. Ouvrez le **commencer** solution situé dans **début/CreatingADatabaseCodeFirst-Ex2/Source/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ajouter le **SampleData.cs** de fichiers à la **modèles** dossier. Pour ce faire, cliquez sur **modèles** dossier, pointez sur **ajouter** puis cliquez sur **élément existant**. Accédez à **\Source\Assets** et sélectionnez le **SampleData.cs** fichier.

    ![Exemples de données remplissent code](aspnet-mvc-4-models-and-data-access/_static/image18.png "code remplissent les exemples de données")

    *Exemples de données remplissent le code*
3. Ouvrez le **Global.asax.cs** de fichiers et ajoutez le code suivant *à l’aide de* instructions.

    (Code d’extrait de code - *modèles et accès aux données - Ex2 les instructions Using Asax Global*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample5.cs)]
4. Dans le **Application\_Start()** méthode ajoute la ligne suivante pour définir l’initialiseur de base de données.

    (Code d’extrait de code - *modèles et accès aux données - Ex2 Global Asax SetInitializer*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample6.cs)]

<a id="Ex2Task2"></a>

<a id="Task_2_-_Configuring_the_connection_to_the_Database"></a>
#### <a name="task-2---configuring-the-connection-to-the-database"></a>Tâche 2 : configuration de la connexion à la base de données

Maintenant que vous avez déjà ajouté une base de données à notre projet, vous permet d’écrire dans le **Web.config** la chaîne de connexion de fichiers.

1. Ajouter une chaîne de connexion à **Web.config**. Pour ce faire, ouvrez **Web.config** à la racine du projet et de remplacer la chaîne de connexion nommée DefaultConnection par cette ligne dans le  **&lt;connectionStrings&gt;**  section :

    ![Emplacement du fichier Web.config](aspnet-mvc-4-models-and-data-access/_static/image19.png "emplacement du fichier Web.config")

    *Emplacement du fichier Web.config*


    [!code-xml[Main](aspnet-mvc-4-models-and-data-access/samples/sample7.xml)]

<a id="Ex2Task3"></a>

<a id="Task_3_-_Working_with_the_Model"></a>
#### <a name="task-3---working-with-the-model"></a>Tâche 3 : utilisation du modèle

Maintenant que vous avez déjà configuré la connexion à la base de données, vous allez lier le modèle avec les tables de base de données. Dans cette tâche, vous allez créer une classe qui sera liée à la base de données Code First. N’oubliez pas qu’il existe une classe de modèle POCO existante qui doit être modifiée.

> [!NOTE]
> Si vous terminé l’exercice 1, vous noterez que cette étape a été effectuée par un Assistant. Vous créerez manuellement en procédant comme Code First, les classes qui seront liés à des entités de données.


1. Ouvrez la classe de modèle POCO **Genre** de **modèles** dossier de projet et d’inclure un code. Utiliser une propriété de type int avec le nom **GenreId**.

    (Code d’extrait de code - *modèles et accès aux données - Genre de premier Code Ex2*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample8.cs)]

    > [!NOTE]
    > Pour utiliser des conventions de Code First, la Genre de classe doit avoir une propriété de clé primaire sera automatiquement détectée.
    > 
    > Vous pouvez en savoir plus sur les Conventions de premier Code de cette [article msdn](https://msdn.microsoft.com/en-us/library/hh161541&amp;#040;v=vs.103&amp;#041;.aspx).
2. Maintenant, ouvrez la classe de modèle POCO **Album** de **modèles** dossier de projet et inclure les clés étrangères, créer des propriétés avec les noms **GenreId** et  **ArtistId**. Cette classe possède déjà le **GenreId** pour la clé primaire.

    (Code d’extrait de code - *modèles et accès aux données - Ex2 Code premier Album*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample9.cs)]
3. Ouvrez la classe de modèle POCO **artiste** et inclure le **ArtistId** propriété.

    (Code d’extrait de code - *modèles et accès aux données - artiste premier Code de Ex2*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample10.cs)]
4. Cliquez sur le **modèles** dossier du projet et sélectionnez **ajouter | Classe**. Nommez le fichier **MusicStoreEntities.cs**. Ensuite, cliquez sur **ajouter.**

    ![Ajout d’une classe](aspnet-mvc-4-models-and-data-access/_static/image20.png "Ajout d’une classe")

    *Ajouter un nouvel élément*

    ![Ajout d’un classe2](aspnet-mvc-4-models-and-data-access/_static/image21.png "ajoutant un classe2")

    *Ajout d’une classe*
5. Ouvrez la classe que vous venez de créer, **MusicStoreEntities.cs**et incluez les espaces de noms **System.Data.Entity** et **System.Data.Entity.Infrastructure**.


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample11.cs)]
6. Remplacez la déclaration de classe pour étendre le **DbContext** classe : déclarer un public **DBSet** et remplacez **OnModelCreating** (méthode). Après cette étape, vous obtiendrez une classe de domaine qui établit un lien votre modèle avec Entity Framework. Pour ce faire, remplacez le code de classe avec les éléments suivants :

    (Code d’extrait de code - *modèles et accès aux données - MusicStoreEntities premier Code de Ex2*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample12.cs)]

    > [!NOTE]
    > Avec Entity Framework **DbContext** et **DBSet** vous serez en mesure d’interroger la classe POCO Genre. En étendant **OnModelCreating** (méthode), vous spécifiez dans le **code** comment Genre est mappée à une table de base de données. Vous trouverez plus d’informations sur DBContext et DBSet dans cet article msdn : [lien](https://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=vs.103).aspx)

<a id="Ex2Task4"></a>

<a id="Task_4_-_Querying_the_Database"></a>
#### <a name="task-4---querying-the-database"></a>Tâche 4 : interrogation de la base de données

Dans cette tâche, la classe StoreController met à jour, afin que, au lieu d’utiliser les données codées en dur, il sera le récupérer à partir de la base de données.

> [!NOTE]
> Cette tâche est en commun avec l’exercice 1.
> 
> Si vous avez terminé l’exercice 1 vous noterez que ces étapes sont identiques dans les deux approches (tout d’abord la base de données ou Code first). Elles sont différentes dans la façon dont les données sont liées avec le modèle, mais l’accès à des entités de données est encore transparent à partir du contrôleur.


1. Ouvrez **Controllers\StoreController.cs** et ajoutez le champ suivant à la classe pour contenir une instance de la **MusicStoreEntities** classe, nommée **storeDB**:

    (Code d’extrait de code - *modèles et accès aux données - Ex1 storeDB*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample13.cs)]
2. Le **MusicStoreEntities** classe expose une propriété de collection pour chaque table dans la base de données. Mise à jour **Parcourir** méthode d’action pour récupérer un Genre avec tous les **Albums**.

    (Code d’extrait de code - *modèles et accès aux données - Ex2 magasin Parcourir*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample14.cs)]

    > [!NOTE]
    > Vous utilisez une fonctionnalité de .NET appelée **LINQ** (language integrated query) pour écrire des expressions de requête fortement typée par rapport à ces regroupements - qui exécutera le code par rapport à la base de données et retourner des objets que vous pouvez programmer par rapport aux.
    > 
    > Pour plus d’informations à propos de LINQ, visitez le [site msdn](https://msdn.microsoft.com/en-us/library/bb397926(v=vs.110).aspx).
3. Mise à jour **Index** pour récupérer tous les genres des méthode d’action.

    (Code d’extrait de code - *modèles et accès aux données - Index de magasin Ex2*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample15.cs)]
4. Mise à jour **Index** méthode d’action pour récupérer tous les genres et de transformer une liste à la collection.

    (Code d’extrait de code - *modèles et accès aux données - Ex2 magasin GenreMenu*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample16.cs)]

<a id="Ex2Task5"></a>

<a id="Task_5_-_Running_the_Application"></a>
#### <a name="task-5---running-the-application"></a>Tâche 5 : exécution de l’Application

Dans cette tâche, vous allez vérifier que la page d’Index du magasin s’affichent désormais les Genres stockées dans la base de données au lieu de ceux qui sont codées. Il est inutile de modifier le modèle d’affichage, car le **StoreController** retourne le même **StoreIndexViewModel** comme avant, mais cette fois les données proviennent de la base de données.

1. Régénérez la solution et appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Vérifiez que le menu de **Genres** n’est plus une liste codée en dur, et les données sont récupérées directement à partir de la base de données.

    ![BrowsingGenresFromDataBase](aspnet-mvc-4-models-and-data-access/_static/image22.png)

    *Genres de navigation à partir de la base de données*
3. Maintenant, accédez à n’importe quel genre et vérifier que les albums sont remplis à partir de la base de données.

    ![Exploration des Albums à partir de la base de données](aspnet-mvc-4-models-and-data-access/_static/image23.png "Albums de navigation à partir de la base de données")

    *Exploration des Albums à partir de la base de données*

<a id="Exercise3"></a>

<a id="Exercise_3_Querying_the_Database_with_Parameters"></a>
### <a name="exercise-3-querying-the-database-with-parameters"></a>Exercice 3 : Interrogation de la base de données avec des paramètres

Dans cet exercice, vous allez apprendre comment interroger la base de données à l’aide de paramètres et comment utiliser la mise en forme du résultat de requête, une fonctionnalité qui permet de réduire la base de données nombre accède à la récupération des données de manière plus efficace.

> [!NOTE]
> Pour plus d’informations sur la mise en forme du résultat de requête, visitez [article msdn](https://msdn.microsoft.com/en-us/library/bb896272&amp;#040;v=vs.100&amp;#041;.aspx).


<a id="Ex3Task1"></a>

<a id="Task_1_-_Modifying_StoreController_to_Retrieve_Albums_from_Database"></a>
#### <a name="task-1---modifying-storecontroller-to-retrieve-albums-from-database"></a>Tâche 1 - modification StoreController pour récupérer des Albums à partir de la base de données

Dans cette tâche, vous allez modifier le **StoreController** classe pour accéder à la base de données pour récupérer des albums d’un genre spécifique.

1. Ouvrez le **commencer** solution situé dans le **Source\Ex3-QueryingTheDatabaseWithParametersCodeFirst\Begin** dossier si vous souhaitez utiliser l’approche Code ou **Source\ Ex3-QueryingTheDatabaseWithParametersDBFirst\Begin** dossier si vous souhaitez utiliser l’approche de base de données. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez le **StoreController** classe pour modifier le **Parcourir** méthode d’action. Pour ce faire, dans le **l’Explorateur de solutions**, développez le **contrôleurs** et double-cliquez sur **StoreController.cs**.
3. Modifier la **Parcourir** méthode d’action pour récupérer les albums pour un genre spécifique. Pour ce faire, remplacez le code suivant :

    (Code d’extrait de code - *modèles et accès aux données - Ex3 StoreController BrowseMethod*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample17.cs)]

    > [!NOTE]
    > Pour remplir une collection de l’entité, vous devez utiliser le **Include** méthode pour spécifier à extraire les albums trop. Vous pouvez utiliser le. **Single()** extension dans LINQ, car dans ce cas qu’un seul genre est attendu pour un album. Le **Single()** méthode prend une expression Lambda en tant que paramètre, qui dans ce cas spécifie un seul objet Genre telles que son nom correspond à la valeur définie.
    > 
    > Vous tirera parti d’une fonctionnalité qui permet d’indiquer d’autres entités associées, vous souhaitez également chargées lorsque l’objet de Genre est récupéré. Cette fonctionnalité est appelée **mise en forme du résultat de requête**et vous permet de réduire le nombre de fois que nécessaire pour accéder à la base de données pour récupérer des informations. Dans ce scénario, vous devez au préalable les Albums pour le Genre que vous récupérez.
    > 
    > La requête inclut **Genres.Include (&quot;Albums&quot;)** pour indiquer que vous voulez également les albums associés. Cela entraîne une application plus efficace, car il récupère les données de Genre et Album dans une requête de base de données unique.

<a id="Ex3Task2"></a>

<a id="Task_2_-_Running_the_Application"></a>
#### <a name="task-2---running-the-application"></a>Tâche 2 : exécution de l’Application

Dans cette tâche, vous exécutez l’application et récupérer des albums d’un genre spécifique à partir de la base de données.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/magasin/Parcourir ? genre = Pop** pour vérifier que les résultats sont récupérées à partir de la base de données.

    ![Navigation par genre](aspnet-mvc-4-models-and-data-access/_static/image24.png "navigation par genre")

    *Navigation magasin/Parcourir ? genre = Pop*

<a id="Ex3Task3"></a>

<a id="Task_3_-_Accessing_Albums_by_Id"></a>
#### <a name="task-3---accessing-albums-by-id"></a>Tâche 3 : l’accès à des Albums par Id

Dans cette tâche, vous recommence la procédure précédente pour obtenir des albums par leur Id.

1. Fermez le navigateur si nécessaire, pour revenir à Visual Studio. Ouvrez le **StoreController** classe pour modifier le **détails** méthode d’action. Pour ce faire, dans le **l’Explorateur de solutions**, développez le **contrôleurs** et double-cliquez sur **StoreController.cs**.
2. Modifier la **détails** méthode d’action pour récupérer les détails des albums selon leur **Id**. Pour ce faire, remplacez le code suivant :

    (Code d’extrait de code - *modèles et accès aux données - Ex3 StoreController DetailsMethod*)


    [!code-csharp[Main](aspnet-mvc-4-models-and-data-access/samples/sample18.cs)]

<a id="Ex3Task4"></a>

<a id="Task_4_-_Running_the_Application"></a>
#### <a name="task-4---running-the-application"></a>Tâche 4 : exécution de l’Application

Dans cette tâche, vous exécutez l’Application dans un navigateur web et obtenir des détails de l’album par leur Id.

1. Appuyez sur **F5** pour exécuter l’Application.
2. Le projet de démarrage dans la page d’accueil. Modifier l’URL pour **/Store/Details/51** ou parcourir les genres et sélectionnez un album pour vérifier que les résultats sont récupérées à partir de la base de données.

    ![Exploration des détails](aspnet-mvc-4-models-and-data-access/_static/image25.png "exploration des détails")

    *Navigation /Store/Details/51*

> [!NOTE]
> En outre, vous pouvez déployer cette application à Sites Web Windows Azure suit [annexe b : publication une Application ASP.NET MVC 4, à l’aide de Web Deploy](#AppendixB).


* * *

<a id="Summary"></a>

<a id="Summary"></a>
## <a name="summary"></a>Résumé

En fin de cet atelier pratique que vous avez appris les notions de base de l’accès aux données et les modèles ASP.NET MVC, à l’aide de la **Database First** approche, ainsi que les **Code First** approche :

- L’ajout d’une base de données à la solution pour pouvoir utiliser ses données
- Comment mettre à jour les contrôleurs pour fournir des modèles d’affichage avec les données provenant de la base de données et non codées en dur
- Comment interroger la base de données à l’aide de paramètres
- L’utilisation de la requête résultat mise en forme, une fonctionnalité qui permet de réduire le nombre d’accès de la base de données, la récupération des données de manière plus efficace.
- Comment utiliser les approches de base de données et le Code First dans Microsoft Entity Framework pour lier la base de données avec le modèle

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Windows Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](aspnet-mvc-4-models-and-data-access/_static/image26.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](aspnet-mvc-4-models-and-data-access/_static/image27.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](aspnet-mvc-4-models-and-data-access/_static/image28.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](aspnet-mvc-4-models-and-data-access/_static/image29.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](aspnet-mvc-4-models-and-data-access/_static/image30.png)

    *VS Express pour la vignette du Web*

<a id="AppendixB"></a>

<a id="Appendix_B_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
## <a name="appendix-b-publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Annexe b : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

Cette annexe sera vous montrent comment créer un nouveau site web à partir du portail de gestion Windows Azure et de publier l’application que vous avez obtenu en suivant le laboratoire, en tirant parti de la fonctionnalité de publication Web Deploy fournie par Windows Azure.

<a id="ApxBTask1"></a>

<a id="Task_1_-_Creating_a_New_Web_Site_from_the_Windows_Azure_Portal"></a>
#### <a name="task-1---creating-a-new-web-site-from-the-windows-azure-portal"></a>Tâche 1 - Création d’un Site Web à partir de Windows Azure Portal

1. Accédez à la [portail de gestion Windows Azure](https://manage.windowsazure.com/) et connectez-vous en utilisant les informations d’identification Microsoft associées à votre abonnement.

    > [!NOTE]
    > Avec Windows Azure, vous pouvez héberger des Sites Web ASP.NET 10 gratuitement et puis faire évoluer à mesure que votre trafic augmente. Vous pouvez vous inscrire [ici](http://aka.ms/aspnet-hol-azure).

    ![Ouvrez une session sur le portail Windows Azure](aspnet-mvc-4-models-and-data-access/_static/image31.png "connectez-vous au portail Windows Azure")

    *Ouvrez une session sur le portail de gestion Azure Windows*
2. Cliquez sur **nouveau** sur la barre de commandes.

    ![Création d’un Site Web](aspnet-mvc-4-models-and-data-access/_static/image32.png "création d’un Site Web")

    *Création d’un Site Web*
3. Cliquez sur **de calcul** | **Site Web**. Puis sélectionnez **création rapide** option. Fournir une URL disponible pour le nouveau site web et cliquez sur **créer un Site Web**.

    > [!NOTE]
    > Un Site Web de Windows Azure est l’hôte pour une application web en cours d’exécution dans le cloud que vous pouvez contrôler et gérer. L’option Création rapide vous permet de déployer une application web complète pour le Site Web Windows Azure à partir d’à l’extérieur du portail. Il n’inclut pas les étapes de configuration d’une base de données.

    ![Création d’un Site Web à l’aide de la création rapide](aspnet-mvc-4-models-and-data-access/_static/image33.png "création d’un Site Web à l’aide de la création rapide")

    *Création d’un Site Web à l’aide de la création rapide*
4. Attendez que la nouvelle **Site Web** est créé.
5. Une fois que le Site Web est créé, cliquez sur le lien situé sous le **URL** colonne. Vérifiez que le nouveau Site Web fonctionne.

    ![Navigation vers le nouveau site web](aspnet-mvc-4-models-and-data-access/_static/image34.png "exploration vers le nouveau site web")

    *Navigation vers le nouveau site web*

    ![Site Web en cours d’exécution](aspnet-mvc-4-models-and-data-access/_static/image35.png "site Web en cours d’exécution")

    *Site Web en cours d’exécution*
6. Revenez au portail et cliquez sur le nom du site web sous le **nom** colonne pour afficher les pages de gestion.

    ![Ouvrir les pages de gestion du site web](aspnet-mvc-4-models-and-data-access/_static/image36.png "ouvrir les pages de gestion de site web")

    *Ouvrir les pages de gestion de Site Web*
7. Dans le **tableau de bord** sous le **coup de œil rapide** , cliquez sur le **télécharger le profil de publication** lien.

    > [!NOTE]
    > Le *le profil de publication* contient toutes les informations nécessaires pour publier une application web sur un site Web de Windows Azure pour chaque méthode de publication activée. Le profil de publication contient les URL, les informations d’identification utilisateur et les chaînes de base de données requis pour se connecter à et de s’authentifier auprès de chacun des points de terminaison pour laquelle une méthode de publication est activée. **Microsoft WebMatrix 2**, **Microsoft Visual Studio Express pour Web** et **Microsoft Visual Studio 2012** prise en charge la lecture de publier les profils pour automatiser la configuration de ces programmes pour publication d’applications web pour les sites Web Windows Azure.

    ![Profil de publication de téléchargement du site web](aspnet-mvc-4-models-and-data-access/_static/image37.png "profil de publication de téléchargement du site web")

    *Profil de publication de téléchargement du Site Web*
8. Téléchargez le fichier de profil de publication à un emplacement connu. Davantage dans cet exercice, vous verrez comment utiliser ce fichier pour publier une application web à un Sites Web Windows Azure à partir de Visual Studio.

    ![L’enregistrement du fichier de profil de publication](aspnet-mvc-4-models-and-data-access/_static/image38.png "l’enregistrement du profil de publication")

    *L’enregistrement du fichier de profil de publication*

<a id="ApxBTask2"></a>

<a id="Task_2_-_Configuring_the_Database_Server"></a>
#### <a name="task-2---configuring-the-database-server"></a>Tâche 2 : configuration du serveur de base de données

Si votre application se sert de SQL Server vous devez créer un serveur de base de données SQL des bases de données. Si vous souhaitez déployer une application simple qui n’utilise pas de SQL Server, vous pouvez ignorer cette tâche.

1. Vous devez un serveur de base de données SQL pour stocker la base de données de l’application. Vous pouvez afficher les serveurs de base de données SQL à partir de votre abonnement dans le portail de gestion Windows Azure à **bases de données Sql** | **serveurs** | **du serveur Tableau de bord**. Si vous ne disposez pas d’un serveur créé, vous pouvez créer un à l’aide de la **ajouter** bouton sur la barre de commandes. Prenez note de la **nom du serveur et les URL, nom de connexion d’administrateur et un mot de passe**, comme vous allez l’utiliser dans les tâches suivantes. Ne créez pas encore, la base de données telle qu’elle sera créée dans une étape ultérieure.

    ![Tableau de bord de serveur SQL de base de données](aspnet-mvc-4-models-and-data-access/_static/image39.png "tableau de bord de serveur SQL de base de données")

    *Tableau de bord de serveur SQL de base de données*
2. Dans la tâche suivante, vous allez tester la connexion de base de données à partir de Visual Studio, pour cette raison, vous devez inclure votre adresse IP locale dans la liste du serveur de **adresses IP autorisées**. Pour ce faire, cliquez sur **configurer**, sélectionnez l’adresse IP à partir de **adresse IP du Client actuel** et le coller dans le **adresse IP de début** et **adresse IP de fin** zones de texte et cliquez sur le ![add-client-ip-address-ok-button](aspnet-mvc-4-models-and-data-access/_static/image40.png) bouton.

    ![Ajout d’adresse IP du Client](aspnet-mvc-4-models-and-data-access/_static/image41.png)

    *Ajout d’adresse IP du Client*
3. Une fois la **adresse IP du Client** est ajouté aux adresses IP autorisées de liste, cliquez sur **enregistrer** pour confirmer les modifications.

    ![Confirmer les modifications](aspnet-mvc-4-models-and-data-access/_static/image42.png)

    *Confirmer les modifications*

<a id="ApxBTask3"></a>

<a id="Task_3_-_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
#### <a name="task-3---publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Tâche 3 : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

1. Revenez à la solution ASP.NET MVC 4. Dans le **l’Explorateur de solutions**, cliquez sur le projet de site web et sélectionnez **publier**.

    ![Publication de l’Application](aspnet-mvc-4-models-and-data-access/_static/image43.png "publication de l’Application")

    *Publier le site web*
2. Importer le profil de publication que vous avez enregistré dans la première tâche.

    ![L’importation du profil de publication](aspnet-mvc-4-models-and-data-access/_static/image44.png "l’importation du profil de publication")

    *Importation du profil de publication*
3. Cliquez sur **valider la connexion**. Une fois la Validation terminée. Cliquez sur **suivant**.

    > [!NOTE]
    > La validation est terminée une fois que vous voyez une coche verte apparaît en regard du bouton Valider la connexion.

    ![Validation de la connexion](aspnet-mvc-4-models-and-data-access/_static/image45.png "validation de la connexion")

    *Validation de la connexion*
4. Dans le **paramètres** sous le **bases de données** , cliquez sur le bouton en regard de la zone de texte de la connexion de votre base de données (par exemple, **DefaultConnection**).

    ![Configuration de déploiement Web](aspnet-mvc-4-models-and-data-access/_static/image46.png "configuration de déploiement Web")

    *Configuration de déploiement Web*
5. Configurer la connexion de base de données comme suit :

    - Dans le **nom du serveur** tapez votre URL de base de données SQL server à l’aide du *tcp :* préfixe.
    - Dans **nom d’utilisateur** tapez le nom de connexion de votre administrateur de serveur.
    - Dans **mot de passe** votre mot de passe du compte de connexion administrateur serveur.
    - Tapez un nouveau nom de base de données.

    ![Configuration de chaîne de connexion de destination](aspnet-mvc-4-models-and-data-access/_static/image47.png "configuration de chaîne de connexion de destination")

    *Configuration de chaîne de connexion de destination*
6. Cliquez ensuite sur **OK**. Lorsque vous êtes invité à créer la base de données, cliquez sur **Oui**.

    ![Création de la base de données](aspnet-mvc-4-models-and-data-access/_static/image48.png "création de la chaîne de la base de données")

    *Création de la base de données*
7. La chaîne de connexion que vous allez utiliser pour se connecter à la base de données SQL dans Windows Azure est indiquée dans la zone de texte par défaut de connexion. Cliquez ensuite sur **Suivant**.

    ![Chaîne de connexion pointant vers la base de données SQL](aspnet-mvc-4-models-and-data-access/_static/image49.png "chaîne de connexion pointant vers la base de données SQL")

    *Chaîne de connexion pointant vers la base de données SQL*
8. Dans le **aperçu** , cliquez sur **publier**.

    ![Publication de l’application web](aspnet-mvc-4-models-and-data-access/_static/image50.png "publication de l’application web")

    *Publication de l’application web*
9. Une fois le processus de publication terminé, votre navigateur par défaut s’ouvre le site web publié.

<a id="AppendixC"></a>

<a id="Appendix_C_Using_Code_Snippets"></a>
## <a name="appendix-c-using-code-snippets"></a>Annexe c : à l’aide d’extraits de Code

Avec des extraits de code, vous avez tout le code que vous avez besoin. Le document lab vous indique exactement quand vous pouvez les utiliser, comme indiqué dans l’illustration suivante.

![À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet](aspnet-mvc-4-models-and-data-access/_static/image51.png "des extraits de code à l’aide de Visual Studio pour insérer du code dans votre projet")

*À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet*

***Pour ajouter un extrait de code à l’aide du clavier (c# uniquement)***

1. Placez le curseur où vous souhaitez insérer le code.
2. Commencez à taper le nom de l’extrait de code (sans espaces ou des traits d’union).
3. Observez comment IntelliSense affiche les noms des extraits de code de mise en correspondance.
4. Sélectionnez l’extrait de code correct (ou continuez à taper jusqu'à ce que le nom de l’extrait de code entier est sélectionné).
5. Appuyez sur la touche Tab à deux reprises pour insérer l’extrait de code à l’emplacement du curseur.

![Commencez à taper le nom de l’extrait de code](aspnet-mvc-4-models-and-data-access/_static/image52.png "commencez à taper le nom de l’extrait de code")

*Commencez à taper le nom de l’extrait de code*

![Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance](aspnet-mvc-4-models-and-data-access/_static/image53.png "appuyez sur Tab pour sélectionner l’extrait de code en surbrillance")

*Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance*

![Appuyez sur Tab à nouveau et l’extrait de code sont développés](aspnet-mvc-4-models-and-data-access/_static/image54.png "appuyez sur Tab à nouveau et l’extrait de code seront développe.")

*Appuyez sur Tab à nouveau et l’extrait de code seront développe.*

***Pour ajouter un extrait de code à l’aide de la souris (c#, Visual Basic et XML)*** 1. Clic droit où vous souhaitez insérer l’extrait de code.

1. Sélectionnez **insérer un extrait** suivie **mes extraits de Code**.
2. Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus.

![Avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait](aspnet-mvc-4-models-and-data-access/_static/image55.png "avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait")

*Avec le bouton droit sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait*

![Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus](aspnet-mvc-4-models-and-data-access/_static/image56.png "choisir l’extrait de code approprié dans la liste, en cliquant sur celle-ci")

*Sélectionnez l’extrait de code approprié dans la liste, en cliquant sur celle-ci*
