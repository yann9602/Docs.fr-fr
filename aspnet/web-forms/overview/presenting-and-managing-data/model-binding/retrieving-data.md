---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: "Extraction et affichage des données avec modèle de liaison et les formulaires web | Documents Microsoft"
author: tfitzmac
description: "Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus droites-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: e750250285fcb0088da284588d721ac21e73820c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>La récupération et affichage des données avec la liaison de modèle et les web forms
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus simple que vous traitez des données des objets de source (comme ObjectDataSource ou SqlDataSource). Cette série commence par la partie introductive et déplace vers des concepts plus avancés dans des didacticiels ultérieurs.
> 
> Le modèle de liaison fonctionne avec n’importe quel technologie d’accès aux données. Dans ce didacticiel, vous allez utiliser Entity Framework, mais vous pouvez utiliser la technologie d’accès aux données qui est plus familier pour vous. À partir d’un contrôle serveur lié aux données, par exemple un contrôle ListView, GridView, DetailsView ou FormView, vous spécifiez les noms des méthodes à utiliser pour la sélection, la mise à jour, la suppression et la création de données. Dans ce didacticiel, vous spécifierez une valeur de SelectMethod.
> 
> Dans cette méthode, vous fournissez la logique de récupération des données. Dans l’étape suivante du didacticiel, vous allez définir des valeurs pour UpdateMethod et DeleteMethod de InsertMethod.
> 
> Vous pouvez [télécharger](https://go.microsoft.com/fwlink/?LinkId=286116) le projet complet en c# ou VB. Le code téléchargeable fonctionne avec Visual Studio 2012 ou Visual Studio 2013. Elle utilise le modèle Visual Studio 2012, qui est légèrement différent de celle du modèle de Visual Studio 2013 indiqué dans ce didacticiel.
> 
> Dans le didacticiel, vous exécutez l’application dans Visual Studio. Vous pouvez également rendre l’application disponible sur Internet en la déployant sur un fournisseur d’hébergement. Microsoft propose d’hébergement web gratuit pour jusqu'à 10 sites web dans un  
>  [libérer le compte d’évaluation Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604). Pour plus d’informations sur la façon de déployer un projet web Visual Studio pour Azure App Service Web Apps, consultez le [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) série. Ce didacticiel montre également comment utiliser les Migrations de Entity Framework Code First pour déployer votre base de données SQL Server pour la base de données SQL Azure.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Microsoft Visual Studio 2013 ou Microsoft Visual Studio Express 2013 pour le Web
>   
> 
> Ce didacticiel fonctionne également avec Visual Studio 2012, mais il y aura des différences dans le modèle de projet et d’interface utilisateur.


## <a name="what-youll-build"></a>Vous allez générer

Dans ce didacticiel, vous effectuerez les tâches suivantes :

1. Générer des objets qui reflètent une université avec étudiants inscrits en cours
2. Générer des tables de base de données à partir des objets
3. Remplir la base de données de test
4. Afficher des données dans un formulaire web

## <a name="set-up-project"></a>Configurer le projet

Dans Visual Studio 2013, créez un **Application Web ASP.NET** appelé **ContosoUniversityModelBinding**.

![créer le projet](retrieving-data/_static/image2.png)

Sélectionnez le modèle Web Forms et laissez les autres options par défaut. Cliquez sur OK pour le projet d’installation.

![Sélectionner les formulaires web](retrieving-data/_static/image3.png)

Tout d’abord, vous allez apporter quelques modifications mineures pour personnaliser l’apparence du site. Ouvrez le **Site.Master** de fichiers et de modifier le titre pour inclure University Contoso au lieu de mon Application ASP.NET.

[!code-aspx[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

Ensuite, modifiez le texte d’en-tête à partir de **nom de l’Application** à **Contoso University**.

[!code-aspx[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

Également dans Site.Master, modifier les liens de navigation qui s’affichent dans l’en-tête pour refléter les pages qui s’appliquent à ce site. Vous n’aurez pas soit le **sur** page ou le **Contact** page, ces liens peut être supprimée. Vous devez à la place, un lien vers une page appelée **étudiants**. Cette page n’a pas encore été créée.

[!code-aspx[Main](retrieving-data/samples/sample3.aspx)]

Enregistrez et fermez Site.Master.

Maintenant, vous allez créer le formulaire web pour afficher les données de l’étudiant. Avec le bouton droit de votre projet, et **ajouter** un **un nouvel élément**. Sélectionnez le **Web Form avec Page maître** modèle et nommez-le **Students.aspx**.

![Créer une page](retrieving-data/_static/image5.png)

Sélectionnez **Site.Master** en tant que la page maître pour le nouveau formulaire web.

## <a name="create-the-data-models-and-database"></a>Créer la base de données et des modèles de données

Vous allez utiliser les Migrations Code First pour créer des objets et les tables correspondantes de la base de données. Ces tables stocke les informations sur les étudiants et leurs cours.

Dans le dossier de modèles, ajoutez une nouvelle classe nommée **UniversityModels.cs**.

![créer la classe de modèle](retrieving-data/_static/image7.png)

Dans ce fichier, définissez les classes SchoolContext, étudiant, l’inscription et cours comme suit :

[!code-csharp[Main](retrieving-data/samples/sample4.cs)]

La classe SchoolContext dérive DbContext, qui gère la connexion de base de données et les modifications des données.

Dans la classe Student, notez les attributs qui ont été appliqués à la **FirstName**, **LastName**, et **année** propriétés. Ces attributs seront utilisés pour la validation des données dans ce didacticiel. Pour simplifier le code pour ce projet de démonstration, que ces propriétés ont été marquées avec des attributs de validation des données. Dans un projet réel, vous devez appliquer des attributs de validation à toutes les propriétés nécessitant des données validées telles que les propriétés dans les classes d’inscription et de cours.

Enregistrer UniversityModels.cs.

Les outils pour les Migrations Code First vous permet de configurer une base de données en fonction de ces classes.

Dans **Package Manager Console**, exécutez la commande :  
`enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

Si la commande se termine correctement, vous recevrez un message indiquant que les migrations ont été activées,

![Activer les migrations](retrieving-data/_static/image8.png)

Notez qu’un nouveau fichier nommé **Configuration.cs** a été créé. Dans Visual Studio, ce fichier est ouvert automatiquement après sa création. La classe de Configuration contient un **Seed** méthode qui vous permet de préremplir les tables de la base de données de test.

Ajoutez le code suivant à la méthode de valeur initiale. Vous devez ajouter un **à l’aide de** instruction pour le **ContosoUniversityModelBinding.Models** espace de noms.

[!code-csharp[Main](retrieving-data/samples/sample5.cs)]

Enregistrer Configuration.cs.

Dans la Console du Gestionnaire de Package, exécutez la commande `add-migration initial`.

Ensuite, exécutez la commande `update-database`.

Si vous recevez une exception lors de l’exécution de cette commande, il est possible que les valeurs de StudentID et CourseID aient variées à partir des valeurs dans la méthode de valeur initiale. Ouvrez ces tables dans la base de données et de trouver les valeurs existantes pour StudentID et CourseID. Ajouter ces valeurs dans le code pour l’amorçage de la table d’inscriptions.

Le fichier de base de données a été ajouté, mais il est actuellement masqué dans le projet. Cliquez sur **afficher tous les fichiers** pour afficher le fichier.

![Afficher tous les fichiers](retrieving-data/_static/image10.png)

Observez le fichier .mdf apparaît maintenant dans l’application\_dossier de données.

![fichier de base de données](retrieving-data/_static/image12.png)

Double-cliquez sur le fichier .mdf et ouvrez l’Explorateur de serveurs. Désormais, les tables existent et sont remplis avec des données.

![tables de base de données](retrieving-data/_static/image14.png)

## <a name="display-data-from-students-and-related-tables"></a>Afficher les données d’étudiants et les tables associées

Avec des données dans la base de données, vous êtes maintenant prêt à récupérer ces données et les afficher dans une page web. Vous allez utiliser un **GridView** contrôle pour afficher les données dans des colonnes et des lignes.

Ouvrez Students.aspx et recherchez le **MainContent** espace réservé. Dans cet espace réservé, ajoutez un **GridView** contrôle qui inclut le code suivant.

[!code-aspx[Main](retrieving-data/samples/sample6.aspx)]

Il existe quelques concepts importants dans ce code de balisage vous remarquer. Tout d’abord, notez qu’une valeur est définie pour le **SelectMethod** propriété dans l’élément GridView. Cette valeur spécifie le nom de la méthode qui est utilisée pour récupérer des données pour le contrôle GridView. Vous allez créer cette méthode à l’étape suivante. En second lieu, notez que le **ItemType** est définie sur la classe Student que vous avez créé précédemment. En définissant cette valeur, vous pouvez faire référence aux propriétés de cette classe dans le code de balisage. Par exemple, la classe Student contient une collection nommée d’inscriptions. Vous pouvez utiliser **Item.Enrollments** pour récupérer cette collection, puis utiliser la syntaxe LINQ pour récupérer la somme des crédits inscrits pour chaque étudiant.

Dans le fichier code-behind, vous devez ajouter la méthode qui est spécifiée pour le **SelectMethod** valeur. Ouvrez **Students.aspx.cs**et ajoutez **à l’aide de** instructions pour le **ContosoUniversityModelBinding.Models** et **System.Data.Entity**espaces de noms.

[!code-csharp[Main](retrieving-data/samples/sample7.cs)]

Ensuite, ajoutez la méthode suivante. Notez que le nom de cette méthode correspond au nom que vous avez fourni pour SelectMethod.

[!code-csharp[Main](retrieving-data/samples/sample8.cs)]

Le **Include** clause améliore les performances de cette requête, mais n’est pas essentiel pour la requête fonctionne. Sans la clause Include, les données sont récupérées à l’aide d’un chargement différé, ce qui implique l’envoi de qu'une requête distincte pour la base de données liées de chaque fois que les données sont récupérées. En fournissant la clause Include, les données sont récupérées à l’aide d’un chargement hâtif, ce qui signifie que toutes les données associées sont récupérées via une requête unique de la base de données. Dans le cas où la plupart des données connexes n’est pas utilisé, hâtif chargement peut être moins efficace, car davantage de données est récupérée. Toutefois, dans ce cas, un chargement hâtif fournit les meilleures performances car les données associées sont affichées pour chaque enregistrement.

Pour plus d’informations sur la considération de performance lors du chargement des données associées, consultez la section intitulée **Lazy Eager et explicite du chargement des données associées** dans les [lors de la lecture des données associées avec Entity Framework dans une page ASP Application de MVC .NET](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) rubrique.

Par défaut, les données sont triées en fonction des valeurs de la propriété marquée comme clé. Vous pouvez ajouter une clause OrderBy pour spécifier une valeur différente pour le tri. Dans cet exemple, la propriété StudentID par défaut est utilisée pour le tri. Dans le [tri, la pagination et filtrage des données](sorting-paging-and-filtering-data.md) rubrique, vous allez activer l’utilisateur de sélectionner une colonne de tri.

Exécuter votre application web et accédez à la page d’étudiants. La page d’étudiants affiche les informations suivantes.

![Afficher les données](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>Génération automatique de méthodes de liaison de modèle

Lorsque vous parcourez cette série de didacticiels, vous pouvez simplement copier le code à partir du didacticiel à votre projet. Toutefois, l’un des inconvénients de cette approche sont que vous ne pouvez pas avoir connaissance de la fonctionnalité fournie par Visual Studio pour générer automatiquement le code pour les méthodes de liaison de modèle. Lorsque vous travaillez sur vos propres projets, génération de code automatique peut vous gagner du temps et vous découvrez comment implémenter une opération. Cette section décrit la fonctionnalité de génération de code automatique. Cette section est uniquement informatif et ne contient-elle pas de code, que vous devez implémenter dans votre projet.

Lorsque vous définissez une valeur pour le **SelectMethod**, **UpdateMethod**, **InsertMethod**, ou **DeleteMethod** propriétés dans le code de balisage, Vous pouvez sélectionner le **créer une nouvelle méthode** option.

![créer la nouvelle méthode](retrieving-data/_static/image18.png)

Visual Studio crée une méthode dans le code-behind avec la signature correcte mais génère également du code d’implémentation pour vous aider à effectuer l’opération. Si vous définissez tout d’abord le **ItemType** propriété avant d’utiliser la fonctionnalité de génération automatique de code, le code généré utilisera ce type pour les opérations. Par exemple, lorsque vous définissez la propriété UpdateMethod, le code suivant est automatiquement généré :

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

Là encore, le code ci-dessus n’a pas besoin être ajouté à votre projet. Dans l’étape suivante du didacticiel, vous allez implémenter des méthodes pour la mise à jour, suppression et l’ajout de nouvelles données.

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous créé des classes de modèle de données et généré une base de données à partir de ces classes. Vous avez rempli les tables de base de données avec des données de test. Votre liaison de modèle permet de récupérer des données à partir de la base de données, puis affiche les données dans un contrôle GridView.

Dans la prochaine [didacticiel](updating-deleting-and-creating-data.md) dans cette série, vous allez activer la mise à jour, la suppression et la création de données.

>[!div class="step-by-step"]
[Next](updating-deleting-and-creating-data.md)
