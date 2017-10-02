---
title: Chargement de fichiers sur une page Razor dans ASP.NET Core
author: guardrex
description: "Découvrez comment charger des fichiers sur une page Razor."
keywords: ASP.NET Core,Razor,pages Razor,IFormFile,chargement de fichiers,chargementfichier
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/uploading-files
ms.openlocfilehash: 5a3dc302186c7fd0a5730bc2c7599676fb543ba7
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="uploading-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="0b539-104">Chargement de fichiers sur une page Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b539-104">Uploading files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="0b539-105">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0b539-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0b539-106">Dans cette section, vous allez découvrir comment charger des fichiers sur une page Razor.</span><span class="sxs-lookup"><span data-stu-id="0b539-106">In this section, uploading files with a Razor Page is demonstrated.</span></span>

<span data-ttu-id="0b539-107">[L’exemple d’application Razor Pages Movie](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) de ce didacticiel utilise une liaison de données simple pour charger des fichiers, ce qui fonctionne bien pour charger des fichiers de petite taille.</span><span class="sxs-lookup"><span data-stu-id="0b539-107">The [Razor Pages Movie sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) in this tutorial uses simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="0b539-108">Pour plus d’informations sur le streaming de fichiers volumineux, consultez [Chargement de fichiers volumineux par streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="0b539-108">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="0b539-109">Dans les étapes ci-dessous, vous ajoutez une fonctionnalité de chargement de fichiers de planification vidéo dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="0b539-109">In the steps below, you add a movie schedule file upload feature to the sample app.</span></span> <span data-ttu-id="0b539-110">Une planification vidéo est représentée par une classe `Schedule` .</span><span class="sxs-lookup"><span data-stu-id="0b539-110">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="0b539-111">La classe inclut deux versions de la planification.</span><span class="sxs-lookup"><span data-stu-id="0b539-111">The class includes two versions of the schedule.</span></span> <span data-ttu-id="0b539-112">Une version est fournie aux clients, `PublicSchedule`.</span><span class="sxs-lookup"><span data-stu-id="0b539-112">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="0b539-113">L’autre version est utilisée pour les employés de la société, `PrivateSchedule`.</span><span class="sxs-lookup"><span data-stu-id="0b539-113">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="0b539-114">Chaque version est chargée dans un fichier distinct.</span><span class="sxs-lookup"><span data-stu-id="0b539-114">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="0b539-115">Le didacticiel décrit comment effectuer deux chargements de fichier à partir d’une page en envoyant une seule commande POST au serveur.</span><span class="sxs-lookup"><span data-stu-id="0b539-115">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="0b539-116">Ajouter une méthode d’assistance pour charger des fichiers</span><span class="sxs-lookup"><span data-stu-id="0b539-116">Add a helper method to upload files</span></span>

<span data-ttu-id="0b539-117">Pour éviter la duplication de code dans le traitement des fichiers de planification chargés, ajoutez d’abord une méthode d’assistance statique.</span><span class="sxs-lookup"><span data-stu-id="0b539-117">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="0b539-118">Créez un dossier *Utilities* dans l’application et ajoutez un fichier *FileHelpers.cs* avec le contenu suivant.</span><span class="sxs-lookup"><span data-stu-id="0b539-118">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="0b539-119">La méthode d’assistance, `ProcessFormFile`, prend un [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) et un [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary), puis retourne une chaîne contenant la taille et le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="0b539-119">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="0b539-120">Le type de contenu et la longueur sont vérifiés.</span><span class="sxs-lookup"><span data-stu-id="0b539-120">The content type and length are checked.</span></span> <span data-ttu-id="0b539-121">Si le fichier échoue à la vérification de validation, une erreur est ajoutée dans `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="0b539-121">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Utilities/FileHelpers.cs)]

## <a name="add-the-schedule-class"></a><span data-ttu-id="0b539-122">Ajouter la classe Schedule</span><span class="sxs-lookup"><span data-stu-id="0b539-122">Add the Schedule class</span></span>

<span data-ttu-id="0b539-123">Cliquez avec le bouton droit sur le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="0b539-123">Right click the *Models* folder.</span></span> <span data-ttu-id="0b539-124">Sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="0b539-124">Select **Add** > **Class**.</span></span> <span data-ttu-id="0b539-125">Nommez la classe **Schedule**, puis ajoutez les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b539-125">Name the class **Schedule** and add the following properties:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/Schedule.cs)]

<span data-ttu-id="0b539-126">La classe utilise les attributs `Display` et `DisplayFormat` qui donnent une mise en forme et des titres conviviaux quand les données de planification sont restituées.</span><span class="sxs-lookup"><span data-stu-id="0b539-126">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

## <a name="update-the-moviecontext"></a><span data-ttu-id="0b539-127">Mettre à jour MovieContext</span><span class="sxs-lookup"><span data-stu-id="0b539-127">Update the MovieContext</span></span>

<span data-ttu-id="0b539-128">Spécifiez un `DbSet` dans `MovieContext` (*Models/MovieContext.cs*) pour les planifications et ajoutez une ligne à la méthode `OnModelCreating` qui définit un nom de table de base de données au singulier (`Schedule`) pour la propriété `DbSet` :</span><span class="sxs-lookup"><span data-stu-id="0b539-128">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules and add a line to the `OnModelCreating` method that sets a singular database table name (`Schedule`) for the `DbSet` property:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?highlight=13,18)]

<span data-ttu-id="0b539-129">Remarque : Si vous ne substituez pas `OnModelCreating` pour utiliser des noms de table au singulier, Entity Framework suppose que vous utilisez des noms de table de base de données au pluriel (par exemple, `Movies` et `Schedules`).</span><span class="sxs-lookup"><span data-stu-id="0b539-129">Note: If you don't override `OnModelCreating` to use singular table names, Entity Framework assumes that you're using plural database table names (for example, `Movies` and `Schedules`).</span></span> <span data-ttu-id="0b539-130">Les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de table au pluriel.</span><span class="sxs-lookup"><span data-stu-id="0b539-130">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="0b539-131">Configurez `MovieContext` et la base de données de la même façon.</span><span class="sxs-lookup"><span data-stu-id="0b539-131">Configure the `MovieContext` and the database the same way.</span></span> <span data-ttu-id="0b539-132">Utilisez des noms de table de base de données au singulier ou au pluriel dans les deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="0b539-132">Either use singular or pluralized database table names in both places.</span></span>

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="0b539-133">Ajouter la table Schedule à la base de données</span><span class="sxs-lookup"><span data-stu-id="0b539-133">Add the Schedule table to the database</span></span>

<span data-ttu-id="0b539-134">Ouvrez la console du gestionnaire de package : **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="0b539-134">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0b539-136">Dans la console du Gestionnaire de package, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="0b539-136">In the PMC, execute the following commands.</span></span> <span data-ttu-id="0b539-137">Ces commandes ajoutent une table `Schedule` à la base de données :</span><span class="sxs-lookup"><span data-stu-id="0b539-137">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-fileupload-class"></a><span data-ttu-id="0b539-138">Ajouter une classe FileUpload</span><span class="sxs-lookup"><span data-stu-id="0b539-138">Add a FileUpload class</span></span>

<span data-ttu-id="0b539-139">Ensuite, ajoutez une classe `FileUpload`, qui est liée à la page pour obtenir les données de planification.</span><span class="sxs-lookup"><span data-stu-id="0b539-139">Next, add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="0b539-140">Cliquez avec le bouton droit sur le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="0b539-140">Right click the *Models* folder.</span></span> <span data-ttu-id="0b539-141">Sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="0b539-141">Select **Add** > **Class**.</span></span> <span data-ttu-id="0b539-142">Nommez la classe **FileUpload** et ajoutez les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b539-142">Name the class **FileUpload** and add the following properties:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/FileUpload.cs)]

<span data-ttu-id="0b539-143">La classe compte une propriété pour le titre de la planification et une propriété pour chacune des deux versions de la planification.</span><span class="sxs-lookup"><span data-stu-id="0b539-143">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="0b539-144">Les trois propriétés sont obligatoires, et le titre doit comprendre entre 3 et 60 caractères.</span><span class="sxs-lookup"><span data-stu-id="0b539-144">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="0b539-145">Ajouter une page Razor de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="0b539-145">Add a file upload Razor Page</span></span>

<span data-ttu-id="0b539-146">Dans le dossier *Pages*, créez un dossier *Schedules*.</span><span class="sxs-lookup"><span data-stu-id="0b539-146">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="0b539-147">Dans le dossier *Schedules*, créez une page nommée *Index.cshtml* pour charger une planification avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="0b539-147">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

<span data-ttu-id="0b539-148">Chaque groupe de formulaires inclut un élément **\<label>** qui affiche le nom de chaque propriété de classe.</span><span class="sxs-lookup"><span data-stu-id="0b539-148">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="0b539-149">Les attributs `Display` du modèle `FileUpload` fournissent les valeurs d’affichage des étiquettes.</span><span class="sxs-lookup"><span data-stu-id="0b539-149">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="0b539-150">Par exemple, le nom d’affichage de la propriété `UploadPublicSchedule` est défini avec `[Display(Name="Public Schedule")]` et affiche donc « Public Schedule » sur l’étiquette, quand le formulaire est restitué.</span><span class="sxs-lookup"><span data-stu-id="0b539-150">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="0b539-151">Chaque groupe de formulaires comprend un élément **\<span>** de validation.</span><span class="sxs-lookup"><span data-stu-id="0b539-151">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="0b539-152">Si l’entrée de l’utilisateur ne respecte pas les attributs de propriété définis dans la classe `FileUpload` ou si l’une des vérifications de validation de fichier de la méthode `ProcessFormFile` échoue, le modèle échoue à la validation.</span><span class="sxs-lookup"><span data-stu-id="0b539-152">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="0b539-153">En cas d’échec de la validation du modèle, un message de validation utile est affiché pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0b539-153">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="0b539-154">Par exemple, la propriété `Title` est annotée avec `[Required]` et `[StringLength(60, MinimumLength = 3)]`.</span><span class="sxs-lookup"><span data-stu-id="0b539-154">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="0b539-155">Si l’utilisateur ne parvient pas à fournir un titre, il reçoit un message indiquant qu’une valeur est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="0b539-155">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="0b539-156">Si l’utilisateur entre une valeur inférieure à trois caractères ou supérieure à 60 caractères, il reçoit un message indiquant que la valeur a une longueur incorrecte.</span><span class="sxs-lookup"><span data-stu-id="0b539-156">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="0b539-157">Si un fichier fourni n’a pas de contenu, un message apparaît indiquant que le fichier est vide.</span><span class="sxs-lookup"><span data-stu-id="0b539-157">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-code-behind-file"></a><span data-ttu-id="0b539-158">Ajouter le fichier code-behind</span><span class="sxs-lookup"><span data-stu-id="0b539-158">Add the code-behind file</span></span>

<span data-ttu-id="0b539-159">Ajoutez le fichier code-behind (*Index.cshtml.cs*) au dossier *Schedules* :</span><span class="sxs-lookup"><span data-stu-id="0b539-159">Add the code-behind file (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

<span data-ttu-id="0b539-160">Le modèle de page (`IndexModel` dans *Index.cshtml.cs*) relie la classe `FileUpload` :</span><span class="sxs-lookup"><span data-stu-id="0b539-160">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0b539-161">Le modèle utilise également la liste des planifications (`IList<Schedule>`) pour afficher les planifications stockées dans la base de données sur la page :</span><span class="sxs-lookup"><span data-stu-id="0b539-161">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0b539-162">Quand la page est chargée avec `OnGetAsync`, `Schedules` est rempli à partir de la base de données et permet de générer une table HTML des planifications chargées :</span><span class="sxs-lookup"><span data-stu-id="0b539-162">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

<span data-ttu-id="0b539-163">Quand le formulaire est publié sur le serveur, `ModelState` est vérifié.</span><span class="sxs-lookup"><span data-stu-id="0b539-163">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="0b539-164">S’il n’est pas valide, `Schedules` est regénéré et la page s’affiche avec un ou plusieurs messages de validation indiquant pourquoi la validation de la page a échoué.</span><span class="sxs-lookup"><span data-stu-id="0b539-164">If invalid, `Schedules` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="0b539-165">S’il est valide, les propriétés `FileUpload` sont utilisées dans *OnPostAsync* pour effectuer le chargement de fichier pour les deux versions de la planification et créer un objet `Schedule` afin de stocker les données.</span><span class="sxs-lookup"><span data-stu-id="0b539-165">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="0b539-166">La planification est ensuite enregistrée dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="0b539-166">The schedule is then saved to the database:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="0b539-167">Lier la page Razor de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="0b539-167">Link the file upload Razor Page</span></span>

<span data-ttu-id="0b539-168">Ouvrez *_Layout.cshtml* et ajoutez un lien vers la barre de navigation pour accéder à la page de chargement de fichiers :</span><span class="sxs-lookup"><span data-stu-id="0b539-168">Open *_Layout.cshtml* and add a link to the navigation bar to reach the file upload page:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=31-38&highlight=4)]

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="0b539-169">Ajouter une page pour confirmer la suppression de la planification</span><span class="sxs-lookup"><span data-stu-id="0b539-169">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="0b539-170">Quand l’utilisateur clique pour supprimer une planification, il doit avoir la possibilité d’annuler l’opération.</span><span class="sxs-lookup"><span data-stu-id="0b539-170">When the user clicks to delete a schedule, you want them to have a chance to cancel the operation.</span></span> <span data-ttu-id="0b539-171">Ajoutez une page de confirmation de suppression (*Delete.cshtml*) au dossier *Schedules* :</span><span class="sxs-lookup"><span data-stu-id="0b539-171">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

<span data-ttu-id="0b539-172">Le fichier code-behind (*Delete.cshtml.cs*) charge une seule planification identifiée par `id` dans les données de routage de la demande.</span><span class="sxs-lookup"><span data-stu-id="0b539-172">The code-behind file (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="0b539-173">Ajoutez le fichier *Delete.cshtml.cs* au dossier *Schedules* :</span><span class="sxs-lookup"><span data-stu-id="0b539-173">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

<span data-ttu-id="0b539-174">La méthode `OnPostAsync` gère la suppression de la planification par son `id` :</span><span class="sxs-lookup"><span data-stu-id="0b539-174">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

<span data-ttu-id="0b539-175">Après avoir supprimé la planification, `RedirectToPage` renvoie l’utilisateur à la page *Index.cshtml* des planifications.</span><span class="sxs-lookup"><span data-stu-id="0b539-175">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="0b539-176">Page Razor Schedules en cours</span><span class="sxs-lookup"><span data-stu-id="0b539-176">The working Schedules Razor Page</span></span>

<span data-ttu-id="0b539-177">Quand la page est chargée, les étiquettes et les entrées correspondant au titre de la planification, à la planification publique et à la planification privée sont affichées avec un bouton d’envoi :</span><span class="sxs-lookup"><span data-stu-id="0b539-177">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![Page Razor Schedules lors du chargement initial, sans erreur de validation et avec des champs vides](uploading-files/_static/browser1.png)

<span data-ttu-id="0b539-179">Le fait de sélectionner le bouton **Charger** sans remplir les champs constitue une violation des attributs `[Required]` sur le modèle.</span><span class="sxs-lookup"><span data-stu-id="0b539-179">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="0b539-180">`ModelState` n'est pas valide.</span><span class="sxs-lookup"><span data-stu-id="0b539-180">The `ModelState` is invalid.</span></span> <span data-ttu-id="0b539-181">Les messages d’erreur de validation sont affichés à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="0b539-181">The validation error messages are displayed to the user:</span></span>

![Messages d’erreur de validation apparaissant à côté de chaque contrôle d’entrée](uploading-files/_static/browser2.png)

<span data-ttu-id="0b539-183">Tapez deux lettres dans le champ **Titre**.</span><span class="sxs-lookup"><span data-stu-id="0b539-183">Type two letters into the **Title** field.</span></span> <span data-ttu-id="0b539-184">Le message de validation change pour indiquer que le titre doit comprendre entre 3 et 60 caractères :</span><span class="sxs-lookup"><span data-stu-id="0b539-184">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![Message de validation du titre modifié](uploading-files/_static/browser3.png)

<span data-ttu-id="0b539-186">Quand une ou plusieurs planifications sont chargées, la section **Planifications chargées** affiche les planifications chargées :</span><span class="sxs-lookup"><span data-stu-id="0b539-186">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![Table des planifications chargées, affichant le titre de chaque planification, date de chargement en heure UTC, taille de fichier de la version publique et taille de fichier de la version privée](uploading-files/_static/browser4.png)

<span data-ttu-id="0b539-188">L’utilisateur peut cliquer sur le lien **Supprimer** à partir d’ici pour accéder à la vue de confirmation de suppression, où il peut confirmer ou annuler l’opération de suppression.</span><span class="sxs-lookup"><span data-stu-id="0b539-188">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0b539-189">Résolution des problèmes</span><span class="sxs-lookup"><span data-stu-id="0b539-189">Troubleshooting</span></span>

<span data-ttu-id="0b539-190">Pour obtenir des informations sur la résolution des problèmes avec le chargement `IFormFile`, consultez [Chargements de fichier dans ASP.NET Core : Résolution des problèmes](xref:mvc/models/file-uploads#troubleshooting).</span><span class="sxs-lookup"><span data-stu-id="0b539-190">For troubleshooting information with `IFormFile` uploading, see the [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>

<span data-ttu-id="0b539-191">Nous vous remercions d’avoir effectué cette introduction aux pages Razor.</span><span class="sxs-lookup"><span data-stu-id="0b539-191">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="0b539-192">Vos commentaires nous intéressent.</span><span class="sxs-lookup"><span data-stu-id="0b539-192">We appreciate any comments you leave.</span></span> <span data-ttu-id="0b539-193">La rubrique [Bien démarrer avec MVC et EF Core](xref:data/ef-mvc/intro) est un excellent moyen de poursuivre après ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="0b539-193">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b539-194">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0b539-194">Additional resources</span></span>

* [<span data-ttu-id="0b539-195">Chargements de fichier dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b539-195">File uploads in ASP.NET Core</span></span>](xref:mvc/models/file-uploads)
* [<span data-ttu-id="0b539-196">IFormFile</span><span class="sxs-lookup"><span data-stu-id="0b539-196">IFormFile</span></span>](/dotnet/api/microsoft.aspnetcore.http.iformfile)

>[!div class="step-by-step"]
[<span data-ttu-id="0b539-197">Précédent : Validation</span><span class="sxs-lookup"><span data-stu-id="0b539-197">Previous: Validation</span></span>](xref:tutorials/razor-pages/validation)
