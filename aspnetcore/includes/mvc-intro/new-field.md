# <a name="adding-a-new-field"></a>Ajout d’un nouveau champ

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel ajoute un nouveau champ à la table `Movies`. Nous supprimons la base de données et nous en créons une nouvelle quand nous changeons le schéma (nous ajoutons un nouveau champ). Ce workflow fonctionne bien au début du développement, quand nous n’avons pas de données de production à conserver.

Une fois que votre application est déployée et que vous avez des données à conserver, vous ne pouvez pas supprimer votre base de données quand vous devez changer le schéma. Entity Framework [Migrations Code First](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) vous permet de mettre à jour votre schéma et de migrer la base de données sans perte de données. Les migrations sont une fonctionnalité répandue dans l’utilisation de SQL Server, mais SQLite ne prend pas en charge de nombreuses de schéma de migration : seules des migrations très simples sont donc possibles. Pour plus d’informations, consultez [Limitations de SQLite](https://docs.microsoft.com/ef/core/providers/sqlite/limitations).

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

Comme vous avez ajouté un nouveau champ à la classe `Movie`, vous devez également mettre à jour la liste verte des liaisons pour y inclure cette nouvelle propriété. Dans *MoviesController.cs*, mettez à jour l’attribut `[Bind]` des méthodes d’action `Create` et `Edit` pour y inclure la propriété `Rating` :

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Vous devez aussi mettre à jour les modèles de vue pour afficher, créer et modifier la nouvelle propriété `Rating` dans la vue du navigateur.

Ouvrez le fichier */Views/Movies/Index.cshtml* et ajoutez un champ `Rating` :

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

Mettez à jour le fichier */Views/Movies/Create.cshtml* avec un champ `Rating`.

L’application ne fonctionne pas tant que la mise à jour de la base de données pour inclure le nouveau champ n’est pas effectuée. Si vous l’exécutez maintenant, vous recevez l’exception `SqliteException` suivante :

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

Vous voyez cette erreur car la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données existante. (Il n’existe pas de colonne `Rating` dans la table de base de données.)

Plusieurs approches sont possibles pour résoudre l’erreur :

1. Supprimez la base de données et laissez Entity Framework recréer automatiquement la base de données sur la base du nouveau schéma de la classe du modèle. Avec cette approche, vous perdez les données existantes de la base de données : vous ne pouvez donc pas l’appliquer à une base de données en production. L’utilisation d’un initialiseur pour le seeding automatique d’une base de données avec des données de test est souvent un moyen efficace pour développer une application.

2. Modifiez manuellement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est que vous conservez vos données. Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.

3. Utilisez les migrations Code First pour mettre à jour le schéma de base de données.

Pour ce didacticiel, nous supprimons puis nous recréons la base de données quand le schéma change. À partir d’un terminal, exécutez la commande suivante pour supprimer la base de données :

`dotnet ef database drop`

Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne. Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque `new Movie`.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Ajoutez le champ `Rating` aux vues `Edit`, `Details` et `Delete`.

Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`. modèles.
