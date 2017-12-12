---
uid: mvc/overview/older-versions-1/movie-database/create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs
title: "Créer une Application de base de données de film des 15 dernières minutes avec ASP.NET MVC (c#) | Documents Microsoft"
author: StephenWalther
description: "Stephen Walther génère une ensemble piloté par base de données application ASP.NET MVC du début à la fin. Ce didacticiel est une bonne introduction aux personnes qui sont nouvelles t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: dd1be137-91c5-47a8-8137-fecf0789c7f5
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/movie-database/create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs
msc.type: authoredcontent
ms.openlocfilehash: 8294b5a8824c6a27e958e1ea78b7909a134447d2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-a-movie-database-application-in-15-minutes-with-aspnet-mvc-c"></a>Créer une Application de base de données de film des 15 dernières minutes avec ASP.NET MVC (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

[Télécharger le Code](http://download.microsoft.com/download/7/2/8/728F8794-E59A-4D18-9A56-7AD2DB05BD9D/MovieApp_CS.zip)

> Stephen Walther génère une ensemble piloté par base de données application ASP.NET MVC du début à la fin. Ce didacticiel est une bonne introduction aux personnes qui ne connaissent pas à l’infrastructure ASP.NET MVC et qui souhaitent avoir une idée du processus de création d’une application ASP.NET MVC.


L’objectif de ce didacticiel est de vous donner une idée de « qu’elle est similaire à » pour générer une application ASP.NET MVC. Dans ce didacticiel, j’ai explosion dans la création d’une application ASP.NET MVC entière à partir du début à la fin. Vous allez apprendre à créer une application pilotée par base de données simple qui illustre comment vous pouvez répertorier, créer et modifier des enregistrements de base de données.

Pour simplifier le processus de création de notre application, nous allons prendre parti des fonctionnalités de génération de modèles automatique de Visual Studio 2008. Nous allons laisser Visual Studio générer le code initial et le contenu de nos contrôleurs, les modèles et les vues.

Si vous avez travaillé avec des Pages ASP ou ASP.NET, puis vous devez trouver ASP.NET MVC très familier. Vues ASP.NET MVC sont très semblables aux pages dans une application Active Server Pages. Et, tout comme une application Web Forms ASP.NET traditionnelle, ASP.NET MVC vous fournit un accès complet à l’ensemble de langages et les classes fournies par le .NET framework.

Mon espoir est que ce didacticiel vous donne une idée de la façon dont l’expérience de création d’une application ASP.NET MVC est différent de celui de l’expérience de création d’une application ASP ou ASP.NET Web Forms et similaires.

## <a name="overview-of-the-movie-database-application"></a>Vue d’ensemble de l’Application de base de données de film

Étant donné que notre objectif est de simplifier les choses, nous allons construire une application de base de données de film très simple. Notre application de base de données de film simple permet d’effectuer trois actions :

1. Liste des enregistrements de base de données de film
2. Créer un nouvel enregistrement de base de données de film
3. Modifier un enregistrement de base de données de film existant

Là encore, car nous souhaitons garder les choses simples, nous allons tirer parti du nombre minimal de fonctionnalités de l’infrastructure ASP.NET MVC nécessaires pour générer votre application. Par exemple, nous ne sera pas profiter de développement piloté par tests.

Pour créer notre application, nous devons effectuer chacune des étapes suivantes :

1. Créer le projet d’Application Web ASP.NET MVC
2. Créer la base de données
3. Créer le modèle de base de données
4. Créer le contrôleur d’ASP.NET MVC
5. Créer des vues de l’ASP.NET MVC

## <a name="preliminaries"></a>Préliminaires

Vous aurez besoin de Visual Studio 2008 ou Visual Web Developer 2008 Express pour générer une application ASP.NET MVC. Vous devez également télécharger l’infrastructure ASP.NET MVC.

Si vous ne possédez pas Visual Studio 2008, vous pouvez télécharger une version d’évaluation de 90 jours de Visual Studio 2008 à partir de ce site Web :

[https://msdn.Microsoft.com/en-us/VS2008/Products/cc268305.aspx](https://msdn.microsoft.com/en-us/vs2008/products/cc268305.aspx)

Vous pouvez également créer ASP.NET MVC applications avec Visual Web Developer Express 2008. Si vous décidez d’utiliser Visual Web Developer Express vous devez avoir installé Service Pack 1. Vous pouvez télécharger Visual Web Developer 2008 Express avec Service Pack 1 à partir de ce site Web :

[https://www.Microsoft.com/downloads/details.aspx?FamilyId=BDB6391C-05CA-4036-9154-6DF4F6DEBD14&amp;displaylang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyId=BDB6391C-05CA-4036-9154-6DF4F6DEBD14&amp;displaylang=en)

Après avoir installé Visual Studio 2008 ou Visual Web Developer 2008, vous devez installer l’infrastructure ASP.NET MVC. Vous pouvez télécharger l’infrastructure ASP.NET MVC à partir du site Web suivant :

[https://www.ASP.NET/MVC/](../../../index.md)

> [!NOTE] 
> 
> Au lieu de télécharger l’infrastructure ASP.NET et l’infrastructure ASP.NET MVC individuellement, vous pouvez tirer parti de Web Platform Installer. Le programme d’installation de la plateforme Web est une application qui vous permet de gérer facilement les applications installées sont installés sur votre ordinateur :
> 
> [https://www.Microsoft.com/Web/Gallery/Install.aspx](https://www.microsoft.com/web/gallery/Install.aspx)


## <a name="creating-an-aspnet-mvc-web-application-project"></a>Création d’un projet d’Application Web ASP.NET MVC

Commençons par créer un nouveau projet d’Application Web ASP.NET MVC dans Visual Studio 2008. Sélectionnez l’option de menu **fichier, nouveau projet** et vous verrez la boîte de dialogue Nouveau projet dans la Figure 1. Sélectionnez c# comme langage de programmation et sélectionnez le modèle de projet d’Application Web ASP.NET MVC. Donnez au projet le nom MovieApp et cliquez sur le bouton OK.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image1.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image1.png)

**Figure 01**: boîte de dialogue du nouveau projet ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image2.png))


Assurez-vous que vous sélectionnez .NET Framework 3.5 dans la liste déroulante en haut de la boîte de dialogue Nouveau projet ou le modèle de projet d’Application Web ASP.NET MVC ne s’affiche.


Chaque fois que vous créez un nouveau projet d’Application Web MVC, Visual Studio vous invite à créer un projet de test unitaire distinct. Dans la Figure 2, la boîte de dialogue s’affiche. Étant donné que nous n’allez pas créer des tests dans ce didacticiel en raison de contraintes de temps (et, Oui, nous devons se sentir un peu coupables à ce sujet) sélectionnez le **non** , puis cliquez sur le **OK** bouton.

> [!NOTE] 
> 
> Visual Web Developer ne prend pas en charge les projets de test.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image2.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image3.png)

**Figure 02**: boîte de dialogue du projet de Test unitaire créer ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image4.png))


Une application ASP.NET MVC est un ensemble standard de dossiers : un dossier de modèles, vues et contrôleurs. Vous pouvez voir cet ensemble standard de dossiers dans la fenêtre de l’Explorateur de solutions. Nous devrons ajouter des fichiers à chacun des dossiers de modèles, vues et contrôleurs pour pouvoir créer notre application de base de données de film.

Lorsque vous créez une application MVC avec Visual Studio, vous obtenez un exemple d’application. Étant donné que vous souhaitez démarrer à partir de rien, nous devons supprimer le contenu de cet exemple d’application. Vous devez supprimer le fichier suivant et le dossier suivant :

- Controllers\HomeController.cs
- Views\Home

## <a name="creating-the-database"></a>Création de la base de données

Nous devons créer une base de données pour stocker ses enregistrements de base de données de film. Heureusement, Visual Studio inclut une base de données gratuite nommée de SQL Server Express. Suivez ces étapes pour créer la base de données :

1. Avec le bouton droit de l’application\_dossier de données dans la fenêtre Explorateur de solutions et sélectionnez l’option de menu **ajouter, nouvel élément**.
2. Sélectionnez le **données** catégorie et sélectionnez le **base de données SQL Server** modèle (voir Figure 3).
3. Nom de votre nouvelle base de données *MoviesDB.mdf* et cliquez sur le **ajouter** bouton.

Après avoir créé votre base de données, vous pouvez vous connecter à la base de données en double-cliquant sur le fichier MoviesDB.mdf situé dans l’application\_dossier de données. Double-cliquez sur le fichier MoviesDB.mdf s’ouvre la fenêtre de l’Explorateur de serveurs.

> [!NOTE] 
> 
> La fenêtre de l’Explorateur de serveurs est nommée de la fenêtre Explorateur de base de données dans le cas de Visual Web Developer.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image3.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image5.png)

**Figure 03**: création d’une base de données Microsoft SQL Server ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image6.png))


Ensuite, nous avons besoin créer une nouvelle table de base de données. Dans la fenêtre Explorateur de serveurs, cliquez sur le dossier Tables et sélectionnez l’option de menu **ajouter une nouvelle Table**. Cette option de menu ouvre le Concepteur de tables de base de données. Créez les colonnes de base de données suivantes :

<a id="0.1_table01"></a>


| **Nom de la colonne** | **Type de données** | **Autoriser les valeurs null** |
| --- | --- | --- |
| Id | Int | False |
| Titre | Nvarchar (100) | False |
| Directeur | Nvarchar (100) | False |
| DateReleased | DateTime | False |


La première colonne, la colonne Id, possède deux propriétés spéciales. Tout d’abord, vous devez marquer la colonne d’Id en tant que colonne de clé primaire. Après avoir sélectionné la colonne d’Id, cliquez sur le **définir la clé primaire** bouton (c’est l’icône qui ressemble à une clé). Ensuite, vous devez marquer la colonne Id comme une colonne d’identité. Dans la fenêtre Propriétés de la colonne, faites défiler jusqu'à la section de la spécification de l’identité et le développer. Modifier la **est d’identité** valeur à la propriété **Oui**. Lorsque vous avez terminé, la table doit ressembler à la Figure 4.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image4.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image7.png)

**Figure 04**: table de films de la base de données ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image8.png))


La dernière étape consiste à enregistrer la nouvelle table. Cliquez sur le bouton Enregistrer (l’icône de disquette) et donnez à la nouvelle table les films de nom.

Après avoir terminé la création de la table, ajouter des enregistrements vidéo à la table. Avec le bouton droit de la table de films dans la fenêtre de l’Explorateur de serveurs, puis sélectionnez l’option de menu **afficher les données de Table**. Entrez une liste de vos favoris films (voir Figure 5).


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image5.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image9.png)

**Figure 05**: saisie des enregistrements de film ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image10.png))


## <a name="creating-the-model"></a>Création du modèle

Ensuite, nous devons créer un ensemble de classes pour représenter notre base de données. Nous devons créer un modèle de base de données. Nous allons tirer parti de Microsoft Entity Framework pour générer les classes de notre modèle de base de données automatiquement.

> [!NOTE] 
> 
> L’infrastructure ASP.NET MVC n’est pas lié à Microsoft Entity Framework. Vous pouvez créer des classes de modèle de votre base de données en tirant parti d’une variété de mappage relationnel objet (ou / M) des outils y compris LINQ to SQL, Subsonic et NHibernate.


Suivez ces étapes pour lancer l’Assistant Entity Data Model :

1. Cliquez sur le dossier Modèles dans la fenêtre de l’Explorateur de solutions et sélectionnez l’option de menu **ajouter, nouvel élément**.
2. Sélectionnez le **données** catégorie et sélectionnez le **ADO.NET Entity Data Model** modèle.
3. Nommez votre modèle de données *MoviesDBModel.edmx* et cliquez sur le **ajouter** bouton.

Après avoir cliqué sur le bouton Ajouter, l’Assistant Entity Data Model s’affiche (voir Figure 6). Suivez ces étapes pour terminer l’Assistant :

1. Dans le **choisir le contenu du modèle** étape, sélectionnez le **générer à partir de la base de données** option.
2. Dans le **choisir votre connexion de données** pas à pas, utilisez le *MoviesDB.mdf* connexion de données et le nom *MoviesDBEntities* pour les paramètres de connexion. Cliquez sur le **suivant** bouton.
3. Dans le **choisir vos objets de base de données** pas à pas, développez le nœud Tables, sélectionnez la table de films. Entrez l’espace de noms *MovieApp.Models* et cliquez sur le **Terminer** bouton.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image6.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image11.png)

**Figure 06**: génération d’un modèle de base de données avec l’Assistant Entity Data Model ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image12.png))


Après avoir terminé l’Assistant Entity Data Model, Entity Data Model Designer s’ouvre. Le concepteur doit afficher la table de base de données de films (voir la Figure 7).


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image7.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image13.png)

**Figure 07**: l’Entity Data Model Designer ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image14.png))


Nous devons apporter une modification avant de continuer. L’Assistant génère une classe de modèle nommée *films* qui représente la table de base de données de films. Étant donné que nous allons utiliser la classe de films pour représenter un film, nous avons besoin de modifier le nom de la classe est *film* au lieu de *films* (singulier plutôt qu’au pluriel).

Double-cliquez sur le nom de la classe sur l’aire du concepteur et modifiez le nom de la classe de films par film. Après avoir apporté cette modification, cliquez sur le **enregistrer** bouton (l’icône de la disquette) pour générer la classe de film.

## <a name="creating-the-aspnet-mvc-controller"></a>Création du contrôleur ASP.NET MVC

L’étape suivante consiste à créer le contrôleur d’ASP.NET MVC. Un contrôleur est chargé de contrôler la façon dont un utilisateur interagit avec une application ASP.NET MVC.

Procédez comme suit :

1. Dans la fenêtre Explorateur de solutions, cliquez sur le dossier Controllers, puis sélectionnez l’option de menu **ajouter, de contrôleur**.
2. Dans la boîte de dialogue Ajouter un contrôleur, entrez le nom *HomeController* et cochez la case à cocher **ajouter des méthodes d’action pour les scénarios Create, Update et Details** (voir la Figure 8).
3. Cliquez sur le **ajouter** pour ajouter le nouveau contrôleur à votre projet.

Après avoir effectué ces étapes, le contrôleur dans la liste 1 est créé. Notez qu’il contient des méthodes nommées Index, plus de détails, créer et le modifier. Dans les sections suivantes, nous allons ajouter le code nécessaire pour obtenir ces méthodes fonctionnent.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image8.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image15.png)

**Figure 08**: ajout d’un nouveau contrôleur de MVC ASP.NET ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image16.png))


**La liste 1 – Controllers\HomeController.cs**

[!code-csharp[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample1.cs)]

## <a name="listing-database-records"></a>Enregistrements de base de données de liste

La méthode Index() du contrôleur Home est la méthode par défaut pour une application ASP.NET MVC. Lorsque vous exécutez une application ASP.NET MVC, la méthode Index() est la première méthode de contrôleur est appelée.

Nous allons utiliser la méthode Index() pour afficher la liste des enregistrements de la table de base de données de films. Classes de modèle que vous avez créée précédemment pour récupérer les enregistrements de base de données de film avec la méthode Index(), nous allons prendre parti de la base de données.

J’ai modifié la classe HomeController dans la liste 2 pour qu’il contienne un nouveau champ privé nommé \_db. La classe MoviesDBEntities représente notre modèle de base de données, et nous allons utiliser cette classe pour communiquer avec notre base de données.

J’ai également modifié la méthode Index() dans la liste 2. La méthode Index() utilise la classe MoviesDBEntities pour récupérer tous les enregistrements de films à partir de la table de base de données de films. L’expression  *\_base de données. MovieSet.ToList()* renvoie une liste de tous les enregistrements de films à partir de la table de base de données de films.

La liste de films est passée à la vue. Tout ce qui est passé à la méthode View() est passé à la vue en tant que données d’affichage.

**Listing 2 – Controllers/HomeController.cs (méthode d’Index modifié)**

[!code-csharp[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample2.cs)]

La méthode Index() retourne une vue nommée Index. Nous devons créer cette vue pour afficher la liste des enregistrements de base de données de film. Procédez comme suit :


Vous devez générer votre projet (sélectionnez l’option de menu **générer, générer la Solution**) avant d’ouvrir le **ajouter une vue** boîte de dialogue ou aucune classe s’affiche dans le **afficher la classe de données** liste déroulante.


1. Avec le bouton droit de la méthode Index() dans l’éditeur de code et sélectionnez l’option de menu **ajouter une vue** (voir la Figure 9).
2. Dans la boîte de dialogue Ajouter une vue, vérifiez que la case à cocher intitulée **créer une vue fortement typée** est activée.
3. À partir de la **afficher le contenu** liste déroulante, sélectionnez la valeur *liste*.
4. À partir de la **afficher la classe de données** liste déroulante, sélectionnez la valeur *MovieApp.Models.Movie*.
5. Cliquez sur le bouton Ajouter pour créer le nouveau afficher (voir Figure 10).

Après avoir effectué ces étapes, une nouvelle vue nommée Index.aspx est ajoutée au dossier Views\Home. Le contenu de la vue de l’Index est inclus dans la liste 3.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image9.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image17.png)

**Figure 09**: ajout d’une vue à partir d’une action de contrôleur ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image18.png))


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image10.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image19.png)

**La figure 10**: création d’une nouvelle vue de la boîte de dialogue Ajouter une vue ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image20.png))


**La liste 3 – Views\Home\Index.aspx**

[!code-aspx[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample3.aspx)]

La vue Index affiche tous les enregistrements de films à partir de la table de base de données de films dans une table HTML. La vue contient une boucle foreach qui effectue une itération dans chaque séquence représenté par la propriété ViewData.Model. Si vous exécutez votre application en appuyant sur la touche F5, vous verrez la page web dans la Figure 11.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image11.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image21.png)

**Figure 11**: affichage de l’Index ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image22.png))


## <a name="creating-new-database-records"></a>Création de nouveaux enregistrements de base de données

La vue de l’Index que vous avez créée dans la section précédente inclut un lien pour la création de nouveaux enregistrements de base de données. Commençons et implémenter la logique et créer la vue nécessaire pour la création de nouveaux enregistrements de base de données de film.

Le contrôleur Home contient deux méthodes nommées Create(). La première méthode Create() n’a aucun paramètre. Cette surcharge de la méthode Create() est utilisée pour afficher le formulaire HTML pour la création d’un nouvel enregistrement de base de données de film.

La deuxième méthode Create() a un paramètre FormCollection. Cette surcharge de la méthode Create() est appelée lorsque le formulaire HTML pour la création d’une nouvelle séquence est publié sur le serveur. Notez que cette méthode Create() deuxième a un attribut AcceptVerbs qui empêche la méthode d’appel, sauf si une opération HTTP POST.

Cette deuxième méthode Create() a été modifiée dans la classe HomeController mis à jour dans la liste 4. La nouvelle version de la méthode Create() accepte un paramètre de film et contient la logique permettant d’insérer une nouvelle séquence dans la table de base de données de films.

> [!NOTE] 
> 
> Notez que l’attribut de liaison. Étant donné que nous ne souhaitez pas mettre à jour la propriété d’Id de film à partir de formulaire HTML, nous devons exclure explicitement cette propriété.


**La liste 4 – Controllers\HomeController.cs (méthode de création modifié)**

[!code-csharp[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample4.cs)]

Visual Studio vous permet de créer le formulaire de création d’une nouvelle base de données de film enregistrer (voir Figure 12). Procédez comme suit :

1. Avec le bouton droit de la méthode Create() dans l’éditeur de code et sélectionnez l’option de menu **ajouter une vue**.
2. Vérifiez que la case à cocher intitulée **créer une vue fortement typée** est activée.
3. À partir de la **afficher le contenu** liste déroulante, sélectionnez la valeur *créer*.
4. À partir de la **afficher la classe de données** liste déroulante, sélectionnez la valeur *MovieApp.Models.Movie*.
5. Cliquez sur le **ajouter** bouton permettant de créer la nouvelle vue.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image12.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image23.png)

**Figure 12**: ajout de la vue Create ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image24.png))


Visual Studio génère la vue dans la liste 5 automatiquement. Cette vue contient un formulaire HTML qui inclut des champs qui correspondent à chacune des propriétés de la classe de film.

**La liste 5 – Views\Home\Create.aspx**

[!code-aspx[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample5.aspx)]

> [!NOTE] 
> 
> Le formulaire HTML généré par la boîte de dialogue Ajouter une vue génère un champ de formulaire Id. Étant donné que la colonne Id est une colonne d’identité, nous ne devons ce champ du formulaire et vous pouvez le supprimer en toute sécurité.


Après avoir ajouté la vue de créer, vous pouvez ajouter de nouveaux enregistrements de film à la base de données. Exécutez votre application en appuyant sur la touche F5, puis cliquez sur le lien Créer un nouveau pour voir le formulaire à la Figure 13. Si vous créez et envoyez le formulaire, un nouvel enregistrement de base de données de film est créé.

Notez que vous obtenez automatiquement la validation de formulaire. Si vous oubliez d’entrer une date de publication pour un film ou si vous entrez une date de version non valide, puis le formulaire s’affiche de nouveau et le champ date de mise en production est mis en surbrillance.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image13.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image25.png)

**Figure 13**: création d’un enregistrement de base de données de film ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image26.png))


## <a name="editing-existing-database-records"></a>Modification des enregistrements de base de données existants

Dans les sections précédentes, nous avons abordé la façon dont vous pouvez répertorier et créer de nouveaux enregistrements de base de données. Dans cette dernière section, nous expliquent comment vous pouvez modifier les enregistrements de base de données existante.

Tout d’abord, nous devons générer le formulaire d’édition. Cette étape est facile, car Visual Studio génère le formulaire d’édition pour nous automatiquement. Ouvrez la classe HomeController.cs dans l’éditeur de code Visual Studio et procédez comme suit :

1. Avec le bouton droit de la méthode Edit() dans l’éditeur de code et sélectionnez l’option de menu **ajouter une vue** (voir Figure 14).
2. Cochez la case intitulée **créer une vue fortement typée**.
3. À partir de la **afficher le contenu** liste déroulante, sélectionnez la valeur *modifier*.
4. À partir de la **afficher la classe de données** liste déroulante, sélectionnez la valeur *MovieApp.Models.Movie*.
5. Cliquez sur le **ajouter** bouton permettant de créer la nouvelle vue.

Ces étapes ajoute une nouvelle vue nommée Edit.aspx dans le dossier Views\Home. Cette vue contient un formulaire HTML pour la modification d’un enregistrement vidéo.


[![La boîte de dialogue Nouveau projet](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image14.jpg)](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image27.png)

**La figure 14**: ajout de la vue Edit ([cliquez pour afficher l’image en taille réelle](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/_static/image28.png))


> [!NOTE] 
> 
> Le mode édition contient un champ de formulaire HTML qui correspond à la propriété Id de film. Étant donné que vous ne souhaitez pas modifier la valeur de la propriété Id des personnes, vous devez supprimer ce champ du formulaire.


Enfin, nous devons modifier le contrôleur Home afin qu’il prend en charge la modification d’un enregistrement de base de données. La classe HomeController mis à jour est contenue dans la liste 6.

**La liste 6 – Controllers\HomeController.cs (méthodes de modification)**

[!code-csharp[Main](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-cs/samples/sample6.cs)]

Dans la liste 6, j’ai ajouté une logique supplémentaire pour les deux surcharges de la méthode Edit(). La première méthode Edit() retourne l’enregistrement de base de données de film qui correspond au paramètre Id passé à la méthode. La deuxième surcharge effectue les mises à jour un enregistrement vidéo dans la base de données.

Notez que vous devez récupérer la séquence d’origine et ensuite appeler ApplyPropertyChanges(), pour mettre à jour la séquence existante dans la base de données.

## <a name="summary"></a>Résumé

L’objectif de ce didacticiel a pour vous donner une idée de l’expérience de création d’une application ASP.NET MVC. J’espère que vous avez découvert que la génération d’un ASP.NET MVC application web est très similaire à celle de la création d’une application ASP ou ASP.NET.

Dans ce didacticiel, nous avons examiné uniquement les principales fonctionnalités de base de l’infrastructure ASP.NET MVC. Dans les didacticiels futures, nous Explorer plus en détail des sujets tels que les contrôleurs, les actions de contrôleur, des vues, afficher les données et programmes d’assistance HTML.

>[!div class="step-by-step"]
[Next](create-a-movie-database-application-in-15-minutes-with-asp-net-mvc-vb.md)
