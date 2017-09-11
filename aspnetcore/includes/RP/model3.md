<a name="cli"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="8eb6c-101">Ajouter des outils de génération de modèles automatique et effectuer la migration initiale</span><span class="sxs-lookup"><span data-stu-id="8eb6c-101">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="8eb6c-102">À partir de la ligne de commande, exécutez les commandes CLI .NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="8eb6c-102">From the command line, run the following .NET Core CLI commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="8eb6c-103">La commande `add package` installe les outils nécessaires pour exécuter le moteur de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="8eb6c-103">The `add package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="8eb6c-104">La commande `ef migrations add InitialCreate` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="8eb6c-104">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8eb6c-105">Le schéma est basé sur le modèle spécifié dans le `DbContext` (dans le fichier *Models/MovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="8eb6c-105">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="8eb6c-106">L’argument `Initial` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="8eb6c-106">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="8eb6c-107">Vous pouvez utiliser n’importe quel nom, mais par convention vous choisissez un nom qui décrit la migration.</span><span class="sxs-lookup"><span data-stu-id="8eb6c-107">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="8eb6c-108">Pour plus d’informations, consultez [Introduction aux migrations](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="8eb6c-108">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="8eb6c-109">La commande `ef database update` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage> _InitialCreate.cs*, ce qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="8eb6c-109">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>