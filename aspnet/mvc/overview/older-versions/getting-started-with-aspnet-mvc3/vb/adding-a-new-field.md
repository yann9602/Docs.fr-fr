---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-new-field
title: "Ajout d’un nouveau champ à la Table de base de données (VB) et au modèle de film | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: 28970e1b-1845-4015-86ef-121e52a6c397
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 377c667a56bb5c0d58ecef5c3550ca510ec52546
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-new-field-to-the-movie-model-and-database-table-vb"></a>Ajout d’un nouveau champ à la Table de base de données (VB) et au modèle de film
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code VB.NET source est disponible pour accompagner cette rubrique. [Télécharger la version VB.NET](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez c#, basculez vers le [c# version](../cs/adding-a-new-field.md) de ce didacticiel.


Dans cette section, vous allez apporter des modifications pour les classes du modèle et découvrez comment vous pouvez mettre à jour le schéma de base de données pour refléter les modifications de modèle.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

Commencez par ajouter un nouveau `Rating` à l’objet de propriété `Movie` classe. Ouvrez le *Movie.cs* et ajoutez le `Rating` propriété similaire à celle-ci :

[!code-vb[Main](adding-a-new-field/samples/sample1.vb)]

Le texte complet `Movie` classe se présente comme le code suivant :

[!code-vb[Main](adding-a-new-field/samples/sample2.vb)]

Recompiler l’application à l’aide de la **déboguer** &gt; **générer un film** commande de menu.

Maintenant que vous avez mis à jour le `Model` (classe), vous devez également mettre à jour le *\Views\Movies\Index.vbhtml* et *\Views\Movies\Create.vbhtml* afficher des modèles pour prendre en charge la nouvelle `Rating`propriété.

Ouvrez le*\Views\Movies\Index.vbhtml* et ajoutez un `<th>Rating</th>` juste après l’en-tête de colonne le **prix** colonne. Ajoutez ensuite une `<td>` colonne vers la fin du modèle pour afficher le `@item.Rating` valeur. Voici quelles mis à jour *Index.vbhtml* afficher le modèle ressemble à :

[!code-vbhtml[Main](adding-a-new-field/samples/sample3.vbhtml)]

Ensuite, ouvrez le *\Views\Movies\Create.vbhtml* et ajoutez le balisage suivant à la fin du formulaire. Cela restitue une zone de texte afin que vous pouvez spécifier un classement lors de la création d’un film de nouveau.

[!code-cshtml[Main](adding-a-new-field/samples/sample4.cshtml)]

## <a name="managing-model-and-database-schema-differences"></a>La gestion de modèle et les différences de schéma de base de données

Vous avez maintenant mise à jour le code d’application pour prendre en charge la nouvelle `Rating` propriété.

Maintenant, exécutez l’application et accédez à la */Movies* URL. Dans ce cas, cependant, vous verrez l’erreur suivante :

![](adding-a-new-field/_static/image1.png)

Vous voyez cette erreur, car la mise à jour `Movie` classe de modèle dans l’application est maintenant différent de celui du schéma de la `Movie` table de base de données existante. (Il n’existe pas de colonne `Rating` dans la table de base de données.)

Par défaut, lorsque vous utilisez Entity Framework Code First pour créer automatiquement une base de données, comme vous l’avez fait précédemment dans ce didacticiel, le premier Code ajoute une table pour faciliter le suivi si le schéma de la base de données est synchronisé avec les classes de modèle qu’il a été généré à partir de la base de données. Si elles ne sont pas synchronisés, Entity Framework génère une erreur. Cela rend plus faciles à identifier les problèmes au moment du développement que vous pouvez sinon uniquement découvrir (par des erreurs obscurs) au moment de l’exécution. La fonctionnalité de vérification de la synchronisation est ce qui entraîne que vous venez de voir le message d’erreur à afficher.

Il existe deux approches pour résoudre l’erreur :

1. Laissez Entity Framework supprimer et recréer automatiquement la base de données sur la base du nouveau schéma de classe de modèle. Cette approche est très utile lors de l’exécution de développement actif sur une base de données de test, car elle permet de faire évoluer rapidement le schéma de modèle et de la base de données entre eux. L’inconvénient, cependant, est que vous perdez les données existantes dans la base de données, donc vous *ne* à utiliser cette approche sur une base de données de production !
2. Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est que vous conservez vos données. Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.

Pour ce didacticiel, nous allons utiliser la première approche, vous aurez Entity Framework Code First automatiquement de recréer la base de données chaque fois que le modèle change.

## <a name="automatically-re-creating-the-database-on-model-changes"></a>Recréer automatiquement les modifications du modèle de la base de données

Nous allons mettre à jour l’application afin que le Code First automatiquement supprime et recrée la base de données chaque fois que vous modifiez le modèle d’application.

> [!NOTE] 
> 
> **Avertissement** vous devez activer cette approche d’automatiquement et recréer la base de données uniquement lorsque vous utilisez une base de données de test ou de développement, et *jamais* sur une base de données de production qui contient des données réelles. À l’aide de la sur un serveur de production peut entraîner une perte de données.


Dans **l’Explorateur de solutions**, bouton droit sur le *modèles* dossier, sélectionnez **ajouter**, puis sélectionnez **classe**.

![](adding-a-new-field/_static/image2.png)

Nom de la classe &quot;MovieInitializer&quot;. Mise à jour la `MovieInitializer` classe pour contenir le code suivant :

[!code-vb[Main](adding-a-new-field/samples/sample5.vb)]

La `MovieInitializer` classe spécifie que la base de données utilisée par le modèle doit-elle être supprimée et recréée automatiquement si les classes du modèle est susceptible de changer. Le code inclut un `Seed` méthode pour spécifier des données par défaut pour ajouter automatiquement de la base de données une fois qu’il a créé (ou recréé). Cela fournit un moyen utile pour remplir la base de données avec des exemples de données, sans avoir à remplir manuellement chaque fois que vous modifiez un modèle.

Maintenant que vous avez défini le `MovieInitializer` (classe), vous souhaiterez rattachez afin que chaque fois que l’application s’exécute, il vérifie si les classes de modèle sont différents à partir du schéma dans la base de données. Dans le cas, vous pouvez exécuter l’initialiseur pour recréer la base de données correspond au modèle et ensuite renseigner la base de données avec les exemples de données.

Ouvrez le *Global.asax* fichier situé à la racine de la `MvcMovies` projet :

Le *Global.asax* fichier contient la classe qui définit l’application entière pour le projet et contienne un `Application_Start` Gestionnaire d’événements qui s’exécute au premier démarrage de l’application.

Rechercher les `Application_Start` (méthode) et ajoutez un appel à `Database.SetInitializer` au début de la méthode, comme indiqué ci-dessous :

[!code-vb[Main](adding-a-new-field/samples/sample6.vb)]

Le `Database.SetInitializer` instruction que vous venez d’ajouter indique que la base de données utilisé par le `MovieDBContext` instance doit être automatiquement supprimée et recréée si le schéma et la base de données ne correspondent pas. Et comme vous l’avez vu, il remplit également la base de données avec les exemples de données qui sont spécifiés dans le `MovieInitializer` classe.

Fermer le *Global.asax* fichier.

Exécuter à nouveau l’application et accédez à la */Movies* URL. Lorsque l’application démarre, il détecte que la structure du modèle ne correspond plus le schéma de base de données. Automatiquement, il recrée la base de données pour correspondre à la nouvelle structure de modèle et remplit la base de données avec les films exemple :

![7_MyMovieList_SM](adding-a-new-field/_static/image3.png)

Cliquez sur le **créer un nouveau** pour ajouter un nouveau film. Notez que vous pouvez ajouter un contrôle d’accès.

[![7_CreateRioII](adding-a-new-field/_static/image5.png)](adding-a-new-field/_static/image4.png)

Cliquez sur **Créer**. La nouvelle séquence, y compris l’évaluation, figure désormais dans les liste de films :

![7_ourNewMovie_SM](adding-a-new-field/_static/image6.png)

Dans cette section, vous avez vu comment vous pouvez modifier les objets de modèle et synchroniser la base de données avec les modifications. Vous avez également appris une méthode pour remplir une base de données nouvellement créée avec les exemples de données permettent de tester des scénarios. Ensuite, nous allons voir comment vous pouvez ajouter la logique de validation plus riche pour les classes du modèle et activer des règles d’entreprise être appliqué.

>[!div class="step-by-step"]
[Précédent](examining-the-edit-methods-and-edit-view.md)
[Suivant](adding-validation-to-the-model.md)
