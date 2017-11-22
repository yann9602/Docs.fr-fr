<span data-ttu-id="decf3-101">Le tableau suivant détaille les paramètres des générateurs de code ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="decf3-101">The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="decf3-102">Paramètre</span><span class="sxs-lookup"><span data-stu-id="decf3-102">Parameter</span></span>               | <span data-ttu-id="decf3-103">Description</span><span class="sxs-lookup"><span data-stu-id="decf3-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="decf3-104">-m</span><span class="sxs-lookup"><span data-stu-id="decf3-104">-m</span></span>  | <span data-ttu-id="decf3-105">Nom du modèle.</span><span class="sxs-lookup"><span data-stu-id="decf3-105">The name of the model.</span></span> |
| <span data-ttu-id="decf3-106">-dc</span><span class="sxs-lookup"><span data-stu-id="decf3-106">-dc</span></span>  | <span data-ttu-id="decf3-107">Contexte de données.</span><span class="sxs-lookup"><span data-stu-id="decf3-107">The data context.</span></span> |
| <span data-ttu-id="decf3-108">-udl</span><span class="sxs-lookup"><span data-stu-id="decf3-108">-udl</span></span> | <span data-ttu-id="decf3-109">Utiliser la disposition par défaut.</span><span class="sxs-lookup"><span data-stu-id="decf3-109">Use the default layout.</span></span> |
| <span data-ttu-id="decf3-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="decf3-110">-outDir</span></span> | <span data-ttu-id="decf3-111">Chemin du dossier de sortie relatif pour créer les vues.</span><span class="sxs-lookup"><span data-stu-id="decf3-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="decf3-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="decf3-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="decf3-113">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="decf3-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="decf3-114">Utilisez le commutateur `h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="decf3-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="decf3-115">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="decf3-115">Test the app</span></span>

* <span data-ttu-id="decf3-116">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="decf3-116">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="decf3-117">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="decf3-117">Test the **Create** link.</span></span>

 ![Créer une page](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="decf3-119">Testez les liens **Modifier**, **Détails** et **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="decf3-119">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="decf3-120">Si vous obtenez l’erreur suivante, vérifiez que vous avez exécuté les migrations et mis à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="decf3-120">If you get the error similar to the following, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
'no such table: Movie'.
```
