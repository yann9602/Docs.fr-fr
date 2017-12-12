---
uid: mvc/overview/older-versions-1/models-data/creating-model-classes-with-the-entity-framework-cs
title: "Création de Classes de modèle avec Entity Framework (c#) | Documents Microsoft"
author: microsoft
description: "Dans ce didacticiel, vous allez apprendre à utiliser ASP.NET MVC avec Microsoft Entity Framework. Vous apprenez à utiliser l’Assistant pour créer un Da d’entité ADO.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 61644169-e8b1-45dd-bf96-9c2301b69879
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/creating-model-classes-with-the-entity-framework-cs
msc.type: authoredcontent
ms.openlocfilehash: 8a897f671de73d9991189e32a5d86b513051ef05
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-model-classes-with-the-entity-framework-c"></a>Création de Classes de modèle avec Entity Framework (c#)
====================
par [Microsoft](https://github.com/microsoft)

> Dans ce didacticiel, vous allez apprendre à utiliser ASP.NET MVC avec Microsoft Entity Framework. Vous apprenez à utiliser l’Assistant pour créer un ADO.NET Entity Data Model. Au cours de ce didacticiel, nous générer une application web qui montre comment sélectionner, insérer, mettre à jour et supprimer des données de la base de données à l’aide d’Entity Framework.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez créer des classes d’accès aux données à l’aide d’Entity Framework Microsoft lors de la création d’une application ASP.NET MVC. Ce didacticiel ne suppose aucune connaissance préalable de Microsoft Entity Framework. Avant la fin de ce didacticiel, vous allez apprendre à utiliser Entity Framework pour sélectionner, insérer, mettre à jour et supprimer des enregistrements de base de données.

Entity Framework Microsoft est un outil de mappage relationnel objet (O/RM) qui vous permet de générer une couche d’accès aux données à partir d’une base de données automatiquement. Entity Framework vous permet de vous permet d’éviter le travail fastidieux de création de vos classes d’accès aux données manuellement.

Pour illustrer la façon dont vous pouvez utiliser Microsoft Entity Framework avec ASP.NET MVC, nous allons construire un exemple simple d’application. Nous allons créer une application de base de données de film qui vous permet d’afficher et modifier des enregistrements de base de données de film.

Ce didacticiel suppose que vous disposez de Visual Studio 2008 ou Visual Web Developer 2008 avec Service Pack 1. Vous devez le Service Pack 1 pour pouvoir utiliser Entity Framework. Vous pouvez télécharger Visual Studio 2008 Service Pack 1 ou Visual Web Developer, avec Service Pack 1 à partir de l’adresse suivante :

> [https://www.ASP.NET/downloads/](https://www.asp.net/downloads)


> [!NOTE] 
> 
> Il n’existe aucune connexion essentielle entre ASP.NET MVC et Microsoft Entity Framework. Il existe plusieurs alternatives à Entity Framework que vous pouvez utiliser avec ASP.NET MVC. Par exemple, vous pouvez générer vos classes de modèle MVC à l’aide d’autres outils O/RM telles que Microsoft LINQ to SQL, NHibernate ou SubSonic.


## <a name="creating-the-movie-sample-database"></a>Création de la base de données vidéo

L’application de base de données de film utilise une table de base de données nommée films qui contient les colonnes suivantes :

| Nom de la colonne | Type de données | Autoriser les valeurs null ? | Est la clé primaire ? |
| --- | --- | --- | --- |
| Id | int | False | True |
| Titre | Nvarchar (100) | False | False |
| Directeur | Nvarchar (100) | False | False |

Vous pouvez ajouter cette table à un projet ASP.NET MVC en procédant comme suit :

1. Avec le bouton droit de l’application\_dossier de données dans la fenêtre Explorateur de solutions et sélectionnez l’option de menu **ajouter, nouvel élément.**
2. À partir de la **ajouter un nouvel élément** boîte de dialogue, sélectionnez **base de données SQL Server**, donnez le nom MoviesDB.mdf à la base de données, puis cliquez sur le **ajouter** bouton.
3. Double-cliquez sur le fichier MoviesDB.mdf pour ouvrir la fenêtre de l’Explorateur de serveurs/base de données de l’Explorateur.
4. Développez la connexion de base de données MoviesDB.mdf, cliquez sur le dossier Tables et sélectionnez l’option de menu **ajouter une nouvelle Table**.
5. Dans le Concepteur de tables, ajoutez les colonnes Id, Title et directeur.
6. Cliquez sur le **enregistrer** bouton (elle possède l’icône de disquette) pour enregistrer la nouvelle table avec les nom de films.

Après avoir créé la table de base de données de films, vous devez ajouter quelques exemples de données à la table. Avec le bouton droit de la table de films et sélectionnez l’option de menu **afficher les données de Table**. Vous pouvez entrer des données vidéo fausse dans la grille qui s’affiche.

## <a name="creating-the-adonet-entity-data-model"></a>Création de l’ADO.NET Entity Data Model

Pour pouvoir utiliser Entity Framework, vous devez créer un Entity Data Model. Vous pouvez tirer parti de Visual Studio *Assistant Entity Data Model* pour générer un Entity Data Model à partir d’une base de données automatiquement.

Procédez comme suit :

1. Cliquez sur le dossier Modèles dans la fenêtre de l’Explorateur de solutions et sélectionnez l’option de menu **ajouter, nouvel élément**.
2. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez la catégorie de données (voir Figure 1).
3. Sélectionnez le **ADO.NET Entity Data Model** modèle, donnez le nom MoviesDBModel.edmx à l’Entity Data Model, puis cliquez sur le **ajouter** bouton. En cliquant sur le **ajouter** bouton lance l’Assistant de modèle de données.
4. Dans le **choisir le contenu du modèle** pas à pas, choisissez la **générer à partir d’une base de données** , puis cliquez sur le **suivant** bouton (voir Figure 2).
5. Dans le **choisir votre connexion de données** étape, sélectionnez la connexion de base de données MoviesDB.mdf, entrez les entités de paramètres de connexion nom MoviesDBEntities, puis cliquez sur le **suivant** bouton (voir Figure 3).
6. Dans le **choisir vos objets de base de données** pas à pas, sélectionnez la table de base de données de film, cliquez sur le **Terminer** bouton (voir Figure 4).

Après avoir effectué ces étapes, ADO.NET Entity Data Model Designer (Concepteur d’entités) s’ouvre.

**Figure 1 : création d’un nouveau modèle de données d’entité**

![clip_image002](creating-model-classes-with-the-entity-framework-cs/_static/image1.jpg)

**Figure 2 : choisissez l’étape de contenu de modèle**

![clip_image004](creating-model-classes-with-the-entity-framework-cs/_static/image2.jpg)

**Figure 3 : choisir votre connexion de données**

![clip_image006](creating-model-classes-with-the-entity-framework-cs/_static/image3.jpg)

**Figure 4 – choisir vos objets de base de données**

![clip_image008](creating-model-classes-with-the-entity-framework-cs/_static/image4.jpg)

#### <a name="modifying-the-adonet-entity-data-model"></a>Modification de l’ADO.NET Entity Data Model

Après avoir créé un Entity Data Model, vous pouvez modifier le modèle en tirant parti du Concepteur d’entités (voir Figure 5). Vous pouvez ouvrir le Concepteur d’entités à tout moment en double-cliquant sur le fichier MoviesDBModel.edmx contenu dans le dossier de modèles dans la fenêtre de l’Explorateur de solutions.

**Figure 5 – l’ADO.NET Entity Data Model Designer**

![clip_image010](creating-model-classes-with-the-entity-framework-cs/_static/image5.jpg)

Par exemple, vous pouvez utiliser le Concepteur d’entités pour modifier les noms des classes de l’Assistant modèle génère. L’Assistant a créé une nouvelle classe d’accès aux données nommée films. En d’autres termes, l’Assistant a le même nom que la table de base de données à la classe. Étant donné que nous utiliserons cette classe pour représenter une instance particulière de film, nous devons renommez la classe à partir de films film.

Si vous souhaitez renommer une classe d’entité, vous pouvez double-cliquer sur le nom de classe dans le Concepteur d’entités et entrez un nouveau nom (voir Figure 6). Ou bien, vous pouvez modifier le nom d’une entité dans la fenêtre Propriétés après avoir sélectionné une entité dans le Concepteur d’entités.

**Figure 6 : modification du nom d’une entité**

![clip_image012](creating-model-classes-with-the-entity-framework-cs/_static/image6.jpg)

Pensez à enregistrer votre Entity Data Model après avoir apporté une modification en cliquant sur le bouton Enregistrer (l’icône de la disquette). En arrière-plan, le Concepteur d’entités génère un ensemble de classes c#. Vous pouvez afficher ces classes en ouvrant le fichier MoviesDBModel.Designer.cs à partir de la fenêtre de l’Explorateur de solutions.


Ne modifiez pas le code dans le fichier Designer.cs depuis vos modifications seront remplacées la prochaine fois que vous utilisez le Concepteur d’entités. Si vous souhaitez étendre les fonctionnalités des classes d’entité défini dans le fichier Designer.cs, vous pouvez créer *classes partielles* dans des fichiers distincts.


#### <a name="selecting-database-records-with-the-entity-framework"></a>Sélectionner des enregistrements de base de données avec Entity Framework

Nous allons commencer la création de notre application de base de données de film en créant une page qui affiche une liste d’enregistrements de film. Le contrôleur Home dans la liste 1 expose une action nommée Index(). L’action Index() retourne tous les enregistrements de films à partir de la table de base de données de film en tirant parti d’Entity Framework.

**La liste 1 – Controllers\HomeController.cs**

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample1.cs)]

Notez que le contrôleur dans la liste 1 comprend un constructeur. Le constructeur initialise un champ de niveau classe nommé \_db. Le \_champ de base de données représente les entités de base de données générées par Microsoft Entity Framework. Le \_champ de base de données est une instance de la classe MoviesDBEntities qui a été générée par le Concepteur d’entités.


Pour pouvoir utiliser theMoviesDBEntities classe dans le contrôleur Home, vous devez importer l’espace de noms MovieEntityApp.Models (*MVCProjectName*. Modèles).


Le \_champ de base de données est utilisé au sein de l’action Index() pour récupérer les enregistrements de la table de base de données de films. L’expression \_db. MovieSet représente tous les enregistrements de la table de base de données de films. La méthode ToList() est utilisée pour convertir le jeu de films dans une collection générique d’objets de film (liste&lt;film&gt;).

Les enregistrements de film sont récupérés à l’aide de LINQ to Entities. L’action Index() dans la liste 1 utilise LINQ *syntaxe de la méthode* pour récupérer le jeu d’enregistrements de base de données. Si vous préférez, vous pouvez utiliser LINQ *syntaxe de requête* à la place. Les deux instructions suivantes effectuent la même chose :

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample2.cs)]

Utiliser quelle que soit la syntaxe LINQ, syntaxe de méthode ou une syntaxe de requête, que vous trouvez plus intuitive. Il n’existe aucune différence de performances entre les deux méthodes : la seule différence est le style.

La vue dans la liste 2 est utilisée pour afficher les enregistrements de film.

**Listing 2 – Views\Home\Index.aspx**

[!code-aspx[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample3.aspx)]

La vue de liste 2 contient un **foreach** boucle qui effectue une itération dans chaque enregistrement vidéo et affiche les valeurs des propriétés du titre et le directeur de l’enregistrement vidéo. Notez qu’un lien d’édition et de suppression s’affiche en regard de chaque enregistrement. En outre, un lien Ajouter un film apparaît au bas de la vue (voir la Figure 7).

**Figure 7 – la vue Index**

![clip_image014](creating-model-classes-with-the-entity-framework-cs/_static/image7.jpg)

La vue de l’Index est un *typée vue*. La vue Index inclut un &lt;% @ Page %&gt; directive avec un *Inherits* attribut qui effectue un cast de la propriété de modèle à une collection de liste générique fortement typée d’objets de film (liste&lt;film).

## <a name="inserting-database-records-with-the-entity-framework"></a>Insertion d’enregistrements de base de données avec Entity Framework

Vous pouvez utiliser Entity Framework pour faciliter l’insérer de nouveaux enregistrements dans une table de base de données. La liste 3 contient deux nouvelles actions ajoutées à la classe de contrôleur d’accueil que vous pouvez utiliser pour insérer de nouveaux enregistrements dans la table de base de données de film.

**La liste 3 – Controllers\HomeController.cs (ajouter des méthodes)**

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample4.cs)]

La première action Add() retourne simplement une vue. La vue contient un formulaire pour ajouter une nouvelle base de données de film enregistrer (voir la Figure 8). Quand vous envoyez le formulaire, la seconde action Add() est appelée.

Notez que la seconde action Add() est décorée avec l’attribut AcceptVerbs. Cette action peut être appelée uniquement lorsque vous effectuez une opération HTTP POST. En d’autres termes, cette action peut uniquement être appelée lors de la validation d’un formulaire HTML.

La seconde action Add() crée une nouvelle instance de la classe Entity Framework film à l’aide de la méthode ASP.NET MVC TryUpdateModel(). La méthode TryUpdateModel() prend les champs dans la FormCollection passé à la méthode Add() et assigne les valeurs de ces champs de formulaire HTML à la classe de film.


Lorsque vous utilisez Entity Framework, vous devez fournir une « liste blanche » des propriétés lorsque vous utilisez les méthodes TryUpdateModel ou UpdateModel pour mettre à jour les propriétés d’une classe d’entité.


Ensuite, l’action Add() effectue une validation de formulaire simple. L’action vérifie que le titre et le directeur des propriétés ont des valeurs. S’il existe une erreur de validation, un message d’erreur de validation est ajouté à ModelState.

S’il n’y a aucune erreur de validation un nouvel enregistrement de film est ajouté à la table de base de données de films à l’aide d’Entity Framework. Le nouvel enregistrement est ajouté à la base de données avec les deux lignes de code suivantes :

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample5.cs)]

La première ligne de code ajoute la nouvelle entité de film à l’ensemble de films suivies par Entity Framework. La deuxième ligne de code enregistre toutes les modifications ont été apportées pour les films suivies à la base de données sous-jacente.

**Figure 8 : la vue de l’ajouter**

![clip_image016](creating-model-classes-with-the-entity-framework-cs/_static/image8.jpg)

#### <a name="updating-database-records-with-the-entity-framework"></a>Mise à jour des enregistrements de base de données avec Entity Framework

Vous pouvez suivre pratiquement la même approche pour modifier un enregistrement de base de données avec Entity Framework en tant que l’approche que nous avons simplement suivies pour insérer un nouvel enregistrement de base de données. La liste 4 contient deux nouvelles actions de contrôleur nommées Edit(). La première action Edit() retourne un formulaire HTML pour la modification d’un enregistrement vidéo. La seconde action Edit() tente de mettre à jour la base de données.

**La liste 4 – Controllers\HomeController.cs (méthodes de modification)**

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample6.cs)]

La seconde action Edit() commence par récupérer de l’enregistrement vidéo à partir de la base de données qui correspond à l’Id de la séquence en cours de modification. LINQ to instruction d’entités suivants extrait le premier enregistrement de base de données qui correspond à un Id spécifique :

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample7.cs)]

Ensuite, la méthode TryUpdateModel() est utilisée pour affecter les valeurs des champs de formulaire HTML pour les propriétés de l’entité de film. Notez qu’une liste blanche est fournie pour spécifier les propriétés exactes à mettre à jour.

Ensuite, une validation simple est exécutée pour vérifier que le titre du film et le directeur des propriétés ont des valeurs. Si une valeur sont manquante dans des propriétés, puis un message d’erreur de validation est ajouté à ModelState et ModelState.IsValid renvoie la valeur false.

Enfin, s’il n’y a aucune erreur de validation, la table de base de données de films sous-jacent est mises à jour en appelant la méthode SaveChanges().

Lors de la modification des enregistrements de base de données, vous devez passer l’Id de l’enregistrement en cours de modification pour l’action du contrôleur qui effectue la mise à jour de la base de données. Sinon, l’action du contrôleur ne saura pas quel enregistrement il doit mettre à jour dans la base de données sous-jacente. La vue de modification contenue dans la liste 5, inclut un champ masqué qui représente l’Id de l’enregistrement de base de données en cours de modification.

**La liste 5 – Views\Home\Edit.aspx**

[!code-aspx[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample8.aspx)]

## <a name="deleting-database-records-with-the-entity-framework"></a>Suppression d’enregistrements de base de données avec Entity Framework

L’opération finale de la base de données, nous avons besoin d’attaquer dans ce didacticiel, supprime les enregistrements de base de données. Vous pouvez utiliser l’action du contrôleur dans la liste 6 pour supprimer un enregistrement de base de données particulière.

**La liste 6--\Controllers\HomeController.cs (action de suppression)**

[!code-csharp[Main](creating-model-classes-with-the-entity-framework-cs/samples/sample9.cs)]

L’action Delete() récupère d’abord le film entité qui correspond à l’Id est transmise à l’action. Ensuite, la séquence est supprimée de la base de données en appelant la méthode DeleteObject() suivie par la méthode SaveChanges(). Enfin, l’utilisateur est redirigé vers la vue Index.

## <a name="summary"></a>Résumé

L’objectif de ce didacticiel a pour illustrer comment vous pouvez générer des applications web orientées sur la base de données en tirant parti d’ASP.NET MVC et Microsoft Entity Framework. Vous avez appris à créer une application qui vous permet de sélectionner, insérer, mettre à jour et supprimer des enregistrements de base de données.

Tout d’abord, nous avons expliqué comment vous pouvez utiliser l’Assistant Entity Data Model pour générer un Entity Data Model à partir de Visual Studio. Ensuite, vous découvrez comment utiliser LINQ to Entities pour récupérer un jeu d’enregistrements de base de données à partir d’une table de base de données. Enfin, nous avons utilisé l’Entity Framework pour insérer, mettre à jour et supprimer des enregistrements de base de données.

>[!div class="step-by-step"]
[Next](creating-model-classes-with-linq-to-sql-cs.md)
