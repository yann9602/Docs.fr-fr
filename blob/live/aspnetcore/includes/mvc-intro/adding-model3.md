
## <a name="test-the-app"></a><span data-ttu-id="04c3d-101">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="04c3d-101">Test the app</span></span>

* <span data-ttu-id="04c3d-102">Exécutez l’application et appuyez sur le lien **Mvc Movie**.</span><span class="sxs-lookup"><span data-stu-id="04c3d-102">Run the app and tap the **Mvc Movie** link.</span></span>
* <span data-ttu-id="04c3d-103">Appuyez sur le lien **Create New** et créez un film.</span><span class="sxs-lookup"><span data-stu-id="04c3d-103">Tap the **Create New** link and create a movie.</span></span>

  ![Créer une vue avec des champs pour le genre, le prix, la date de sortie et le titre](../../tutorials/first-mvc-app/adding-model/_static/movies.png)

* <span data-ttu-id="04c3d-105">Vous ne pourrez peut-être pas entrer des décimales ou des virgules dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="04c3d-105">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="04c3d-106">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (« , ») comme décimale et des formats de date autres que l’anglais des États-Unis, vous devez effectuer des étapes pour localiser votre application.</span><span class="sxs-lookup"><span data-stu-id="04c3d-106">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="04c3d-107">Pour plus d’informations, consultez https://github.com/aspnet/Docs/issues/4076 et [Ressources supplémentaires](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="04c3d-107">See https://github.com/aspnet/Docs/issues/4076 and [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="04c3d-108">Pour le moment, entrez simplement des nombres entiers tels que 10.</span><span class="sxs-lookup"><span data-stu-id="04c3d-108">For now, just enter whole numbers like 10.</span></span>

<a name="displayformatdatelocal"></a>

* <span data-ttu-id="04c3d-109">Avec certains paramètres régionaux, vous devez spécifier le format de date.</span><span class="sxs-lookup"><span data-stu-id="04c3d-109">In some locales you need to specify the date format.</span></span> <span data-ttu-id="04c3d-110">Consultez le code en surbrillance ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="04c3d-110">See the highlighted code below.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

<span data-ttu-id="04c3d-111">Nous parlerons de `DataAnnotations` plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="04c3d-111">We'll talk about `DataAnnotations` later in the tutorial.</span></span>

<span data-ttu-id="04c3d-112">Quand vous appuyez sur **Create**, le formulaire est envoyé au serveur, où les informations sur le film sont enregistrées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="04c3d-112">Tapping **Create** causes the form to be posted to the server, where the movie information is saved in a database.</span></span> <span data-ttu-id="04c3d-113">L’application redirige vers l’URL */Movies*, où les nouvelles informations sur le film sont affichées.</span><span class="sxs-lookup"><span data-stu-id="04c3d-113">The app redirects to the */Movies* URL, where the newly created movie information is displayed.</span></span>

![Vue de film montrant une nouvelle entrée](../../tutorials/first-mvc-app/adding-model/_static/h.png)

<span data-ttu-id="04c3d-115">Créez deux ou trois autres entrées.</span><span class="sxs-lookup"><span data-stu-id="04c3d-115">Create a couple more movie entries.</span></span> <span data-ttu-id="04c3d-116">Essayez les liens **Edit**, **Details** et **Delete**, qui sont tous opérationnels.</span><span class="sxs-lookup"><span data-stu-id="04c3d-116">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>
