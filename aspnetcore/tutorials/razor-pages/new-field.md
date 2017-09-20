---
title: "Ajout d’un nouveau champ à une page Razor"
author: rick-anderson
description: "Montre comment ajouter un nouveau champ à une page Razor avec Entity Framework Core"
keywords: ASP.NET Core, Entity Framework Core, migrations
ms.author: riande
manager: wpickett
ms.date: 8/7/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 1b5f4297d4812fbbd60fb8b94446da205cd6bb55
ms.sourcegitcommit: f303a457644ed034a49aa89edecb4e79d9028cb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="adding-a-new-field-to-a-razor-page"></a>Ajout d’un nouveau champ à une page Razor

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans cette section, vous allez utiliser la fonctionnalité Migrations Code First [d’Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) pour ajouter un nouveau champ au modèle et migrer ce changement dans la base de données.

Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First ajoute une table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré. S’ils ne sont pas synchronisés, EF lève une exception. Cela facilite la recherche de problèmes d’incohérence de code/de bases de données.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

Générez l’application (Ctrl+Maj+B).

Modifiez *Pages/Movies/Index.cshtml*et ajoutez un champ `Rating` :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=40-42,61-63)]

Ajoutez le champ `Rating` aux pages Delete et Details.

Mettez à jour *Create.cshtml* avec un champ `Rating`. Vous pouvez copier/coller l’élément `<div>` précédent et laisser IntelliSense vous aider à mettre à jour les champs. IntelliSense fonctionne avec des [Tag Helpers](xref:mvc/views/tag-helpers/intro).

![Le développeur a tapé la lettre R comme valeur d’attribut asp-for dans le deuxième élément étiquette de la vue. Un menu contextuel IntelliSense s’est affiché, montrant les champs disponibles, notamment Rating, qui est automatiquement mis en surbrillance dans la liste. Quand le développeur clique sur le champ ou appuie sur Entrée, la valeur est définie sur Rating.](new-field/_static/cr.png)

Le code suivant montre *Create.cshtml* avec un champ `Rating` :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?highlight=31-35)]

Ajoutez le champ `Rating` à la Page Edit.

L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ. Si vous l’exécutez à présent, l’application lève une `SqlException` :

`SqlException: Invalid column name 'Rating'.`

Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données. (Il n’existe pas de colonne `Rating` dans la table de base de données.)

Plusieurs approches sont possibles pour résoudre l’erreur :

1. Laisser Entity Framework supprimer et recréer automatiquement la base de données à l’aide du nouveau schéma de classe de modèle. Cette approche est très utile au début du cycle de développement. Elle permet de faire évoluer rapidement le modèle et le schéma de base de données ensemble. L’inconvénient est que vous perdez les données existantes dans la base de données. Il ne faut pas adopter cette approche sur une base de données de production. La suppression de la base de données lors des changements de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen efficace pour développer une application.

2. Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est que vous conservez vos données. Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.

3. Utilisez les migrations Code First pour mettre à jour le schéma de base de données.

Pour ce didacticiel, nous allons utiliser les migrations Code First.

Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne. Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque bloc `new Movie`.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Consultez le [fichier SeedData.cs complet](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs).

Générez la solution.

<a name="pmc"></a>

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.
Dans la console du Gestionnaire de package, entrez les commandes suivantes :

```PMC
Add-Migration Rating
Update-Database
```

La commande `Add-Migration` indique au framework qu’il doit :

* Comparer le modèle `Movie` au schéma de base de données `Movie`
* Créer du code pour migrer le schéma de base de données vers le nouveau modèle

Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration. Il est utile d’utiliser un nom explicite pour le fichier de migration.

<a name="ssox"></a> Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le champ `Rating`. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox). Pour supprimer la base de données à partir de SSOX

* Sélectionnez la base de données dans SSOX.
* Cliquez avec le bouton droit sur la base de données, puis sélectionnez *Supprimer*.
* Cochez **Fermer les connexions existantes*.
* Sélectionnez **OK**.
* Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données. 

    ```PMC
    Update-Database
    ```

Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`. Si la base de données n’est pas amorcée, arrêtez IIS Express puis exécutez l’application.

>[!div class="step-by-step"]
[Précédent : Ajout de la recherche](xref:tutorials/razor-pages/search)
[Suivant : Ajout de la validation](xref:tutorials/razor-pages/validation)
