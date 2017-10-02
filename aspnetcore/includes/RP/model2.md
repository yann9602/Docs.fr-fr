<span data-ttu-id="b8975-101">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="b8975-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

<span data-ttu-id="b8975-102">Le champ `ID` est requis par la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b8975-102">The `ID` field is required by the database for the primary key.</span></span>

<a name="dc"></a>
### <a name="add-a-database-context-class"></a><span data-ttu-id="b8975-103">Ajouter une classe de contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b8975-103">Add a database context class</span></span>

<span data-ttu-id="b8975-104">Ajoutez une classe dérivée `DbContext` nommée *MovieContext.cs* au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b8975-104">Add a `DbContext` derived class named *MovieContext.cs* to the *Models* folder.</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?range=1-12,14-17,19-21)]

<span data-ttu-id="b8975-105">Le code précédent crée une propriété `DbSet` pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="b8975-105">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b8975-106">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="b8975-106">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="b8975-107">Le nom de la propriété `DbSet` est `Movies`.</span><span class="sxs-lookup"><span data-stu-id="b8975-107">The `DbSet` property name is `Movies`.</span></span> <span data-ttu-id="b8975-108">Comme la base de données utilise des noms au singulier, l’exemple substitue `OnModelCreating` pour utiliser la forme au singulier (`Movie`) du nom de la table.</span><span class="sxs-lookup"><span data-stu-id="b8975-108">Since the database uses singular names, the sample overrides `OnModelCreating` to use the singular form (`Movie`) for the table name.</span></span>
