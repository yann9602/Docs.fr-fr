# <a name="working-with-sqlite-in-an-aspnet-core-mvc-project"></a><span data-ttu-id="d0d8a-101">Utilisation de SQLite dans un projet ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d0d8a-101">Working with SQLite in an ASP.NET Core MVC project</span></span>

<span data-ttu-id="d0d8a-102">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0d8a-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d0d8a-103">L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-103">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d0d8a-104">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d0d8a-104">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<span data-ttu-id="d0d8a-105">[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]</span><span class="sxs-lookup"><span data-stu-id="d0d8a-105">[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]</span></span>

## <a name="sqlite"></a><span data-ttu-id="d0d8a-106">SQLite</span><span class="sxs-lookup"><span data-stu-id="d0d8a-106">SQLite</span></span>

<span data-ttu-id="d0d8a-107">Le site web [SQLite](https://www.sqlite.org/) indique :</span><span class="sxs-lookup"><span data-stu-id="d0d8a-107">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="d0d8a-108">SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-108">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="d0d8a-109">SQLite est le moteur de base de données le plus utilisé au monde.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-109">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="d0d8a-110">Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-110">There are many third party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="d0d8a-111">L’image ci-dessous provient de [DB Browser for SQLite](http://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="d0d8a-111">The image below is from [DB Browser for SQLite](http://sqlitebrowser.org/).</span></span> <span data-ttu-id="d0d8a-112">Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-112">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite montrant la base de données de films](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a><span data-ttu-id="d0d8a-114">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="d0d8a-114">Seed the database</span></span>

<span data-ttu-id="d0d8a-115">Créez une classe nommée `SeedData` dans l’espace de noms *Models*.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-115">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="d0d8a-116">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d0d8a-116">Replace the generated code with the following:</span></span>

<span data-ttu-id="d0d8a-117">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="d0d8a-117">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span></span>

<span data-ttu-id="d0d8a-118">Si la base de données contient des films, l’initialiseur de valeur initiale retourne.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-118">If there are any movies in the DB, the seed initializer returns.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="d0d8a-119">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="d0d8a-119">Add the seed initializer</span></span>

<span data-ttu-id="d0d8a-120">Ajoutez l’initialiseur de valeur initiale à la méthode `Main` dans le fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="d0d8a-120">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

<span data-ttu-id="d0d8a-121">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span><span class="sxs-lookup"><span data-stu-id="d0d8a-121">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span></span>

### <a name="test-the-app"></a><span data-ttu-id="d0d8a-122">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="d0d8a-122">Test the app</span></span>

<span data-ttu-id="d0d8a-123">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="d0d8a-123">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d0d8a-124">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-124">Stop and start the app to seed the database.</span></span>
   
<span data-ttu-id="d0d8a-125">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="d0d8a-125">The app shows the seeded data.</span></span>

![Application MVC Movie ouverte dans le navigateur, affichant les données relatives aux films](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)
