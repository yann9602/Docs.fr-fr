# <a name="working-with-sqlite-in-an-aspnet-core-mvc-project"></a>Utilisation de SQLite dans un projet ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :

[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]

## <a name="sqlite"></a>SQLite

Le site web [SQLite](https://www.sqlite.org/) indique :

> SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public. SQLite est le moteur de base de données le plus utilisé au monde.

Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite. L’image ci-dessous provient de [DB Browser for SQLite](http://sqlitebrowser.org/). Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.

![DB Browser for SQLite montrant la base de données de films](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a>Amorcer la base de données

Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*. Remplacez le code généré par ce qui suit :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Ajoutez l’initialiseur de valeur initiale à la méthode `Main` dans le fichier *Program.cs* :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]

### <a name="test-the-app"></a>Tester l’application

Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute). Arrêtez et démarrez l’application pour amorcer la base de données.
   
L’application affiche les données de départ.

![Application MVC Movie ouverte dans le navigateur, affichant les données relatives aux films](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)
