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
ms.openlocfilehash: 3c5841f8c623f09530b60cc9997281dcb8e3c4f6
ms.sourcegitcommit: 94b7e0f95b92c98b182a93d2b3dc0287e5f97976
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2017
---
# <a name="uploading-files-to-a-razor-page-in-aspnet-core"></a>Chargement de fichiers sur une page Razor dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Dans cette section, vous allez découvrir comment charger des fichiers sur une page Razor.

[L’exemple d’application Razor Pages Movie](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) de ce didacticiel utilise une liaison de données simple pour charger des fichiers, ce qui fonctionne bien pour charger des fichiers de petite taille. Pour plus d’informations sur le streaming de fichiers volumineux, consultez [Chargement de fichiers volumineux par streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).

Dans les étapes ci-dessous, vous ajoutez une fonctionnalité de chargement de fichiers de planification vidéo dans l’exemple d’application. Une planification vidéo est représentée par une classe `Schedule` . La classe inclut deux versions de la planification. Une version est fournie aux clients, `PublicSchedule`. L’autre version est utilisée pour les employés de la société, `PrivateSchedule`. Chaque version est chargée dans un fichier distinct. Le didacticiel décrit comment effectuer deux chargements de fichier à partir d’une page en envoyant une seule commande POST au serveur.

## <a name="add-a-helper-method-to-upload-files"></a>Ajouter une méthode d’assistance pour charger des fichiers

Pour éviter la duplication de code dans le traitement des fichiers de planification chargés, ajoutez d’abord une méthode d’assistance statique. Créez un dossier *Utilities* dans l’application et ajoutez un fichier *FileHelpers.cs* avec le contenu suivant. La méthode d’assistance, `ProcessFormFile`, prend un [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) et un [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary), puis retourne une chaîne contenant la taille et le contenu du fichier. Le type de contenu et la longueur sont vérifiés. Si le fichier échoue à la vérification de validation, une erreur est ajoutée dans `ModelState`.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Utilities/FileHelpers.cs)]

## <a name="add-the-schedule-class"></a>Ajouter la classe Schedule

Cliquez avec le bouton droit sur le dossier *Models*. Sélectionnez **Ajouter** > **Classe**. Nommez la classe **Schedule**, puis ajoutez les propriétés suivantes :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/Schedule.cs)]

La classe utilise les attributs `Display` et `DisplayFormat` qui donnent une mise en forme et des titres conviviaux quand les données de planification sont restituées.

## <a name="update-the-moviecontext"></a>Mettre à jour MovieContext

Spécifiez `DbSet` dans `MovieContext` (*Models/MovieContext.cs*) pour les planifications :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

## <a name="add-the-schedule-table-to-the-database"></a>Ajouter la table Schedule à la base de données

Ouvrez la console du gestionnaire de package : **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.

![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du Gestionnaire de package, exécutez les commandes suivantes. Ces commandes ajoutent une table `Schedule` à la base de données :

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-fileupload-class"></a>Ajouter une classe FileUpload

Ensuite, ajoutez une classe `FileUpload`, qui est liée à la page pour obtenir les données de planification. Cliquez avec le bouton droit sur le dossier *Models*. Sélectionnez **Ajouter** > **Classe**. Nommez la classe **FileUpload** et ajoutez les propriétés suivantes :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/FileUpload.cs)]

La classe compte une propriété pour le titre de la planification et une propriété pour chacune des deux versions de la planification. Les trois propriétés sont obligatoires, et le titre doit comprendre entre 3 et 60 caractères.

## <a name="add-a-file-upload-razor-page"></a>Ajouter une page Razor de chargement de fichiers

Dans le dossier *Pages*, créez un dossier *Schedules*. Dans le dossier *Schedules*, créez une page nommée *Index.cshtml* pour charger une planification avec le contenu suivant :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

Chaque groupe de formulaires inclut un élément **\<label>** qui affiche le nom de chaque propriété de classe. Les attributs `Display` du modèle `FileUpload` fournissent les valeurs d’affichage des étiquettes. Par exemple, le nom d’affichage de la propriété `UploadPublicSchedule` est défini avec `[Display(Name="Public Schedule")]` et affiche donc « Public Schedule » sur l’étiquette, quand le formulaire est restitué.

Chaque groupe de formulaires comprend un élément **\<span>** de validation. Si l’entrée de l’utilisateur ne respecte pas les attributs de propriété définis dans la classe `FileUpload` ou si l’une des vérifications de validation de fichier de la méthode `ProcessFormFile` échoue, le modèle échoue à la validation. En cas d’échec de la validation du modèle, un message de validation utile est affiché pour l’utilisateur. Par exemple, la propriété `Title` est annotée avec `[Required]` et `[StringLength(60, MinimumLength = 3)]`. Si l’utilisateur ne parvient pas à fournir un titre, il reçoit un message indiquant qu’une valeur est obligatoire. Si l’utilisateur entre une valeur inférieure à trois caractères ou supérieure à 60 caractères, il reçoit un message indiquant que la valeur a une longueur incorrecte. Si un fichier fourni n’a pas de contenu, un message apparaît indiquant que le fichier est vide.

## <a name="add-the-code-behind-file"></a>Ajouter le fichier code-behind

Ajoutez le fichier code-behind (*Index.cshtml.cs*) au dossier *Schedules* :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

Le modèle de page (`IndexModel` dans *Index.cshtml.cs*) relie la classe `FileUpload` :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

Le modèle utilise également la liste des planifications (`IList<Schedule>`) pour afficher les planifications stockées dans la base de données sur la page :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

Quand la page est chargée avec `OnGetAsync`, `Schedules` est rempli à partir de la base de données et permet de générer une table HTML des planifications chargées :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

Quand le formulaire est publié sur le serveur, `ModelState` est vérifié. S’il n’est pas valide, `Schedule` est regénéré et la page s’affiche avec un ou plusieurs messages de validation indiquant pourquoi la validation de la page a échoué. S’il est valide, les propriétés `FileUpload` sont utilisées dans *OnPostAsync* pour effectuer le chargement de fichier pour les deux versions de la planification et créer un objet `Schedule` afin de stocker les données. La planification est ensuite enregistrée dans la base de données :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

## <a name="link-the-file-upload-razor-page"></a>Lier la page Razor de chargement de fichiers

Ouvrez *_Layout.cshtml* et ajoutez un lien vers la barre de navigation pour accéder à la page de chargement de fichiers :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=31-38&highlight=4)]

## <a name="add-a-page-to-confirm-schedule-deletion"></a>Ajouter une page pour confirmer la suppression de la planification

Quand l’utilisateur clique pour supprimer une planification, il doit avoir la possibilité d’annuler l’opération. Ajoutez une page de confirmation de suppression (*Delete.cshtml*) au dossier *Schedules* :

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

Le fichier code-behind (*Delete.cshtml.cs*) charge une seule planification identifiée par `id` dans les données de routage de la demande. Ajoutez le fichier *Delete.cshtml.cs* au dossier *Schedules* :

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

La méthode `OnPostAsync` gère la suppression de la planification par son `id` :

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

Après avoir supprimé la planification, `RedirectToPage` renvoie l’utilisateur à la page *Index.cshtml* des planifications.

## <a name="the-working-schedules-razor-page"></a>Page Razor Schedules en cours

Quand la page est chargée, les étiquettes et les entrées correspondant au titre de la planification, à la planification publique et à la planification privée sont affichées avec un bouton d’envoi :

![Page Razor Schedules lors du chargement initial, sans erreur de validation et avec des champs vides](uploading-files/_static/browser1.png)

Le fait de sélectionner le bouton **Charger** sans remplir les champs constitue une violation des attributs `[Required]` sur le modèle. `ModelState` n'est pas valide. Les messages d’erreur de validation sont affichés à l’utilisateur :

![Messages d’erreur de validation apparaissant à côté de chaque contrôle d’entrée](uploading-files/_static/browser2.png)

Tapez deux lettres dans le champ **Titre**. Le message de validation change pour indiquer que le titre doit comprendre entre 3 et 60 caractères :

![Message de validation du titre modifié](uploading-files/_static/browser3.png)

Quand une ou plusieurs planifications sont chargées, la section **Planifications chargées** affiche les planifications chargées :

![Table des planifications chargées, affichant le titre de chaque planification, date de chargement en heure UTC, taille de fichier de la version publique et taille de fichier de la version privée](uploading-files/_static/browser4.png)

L’utilisateur peut cliquer sur le lien **Supprimer** à partir d’ici pour accéder à la vue de confirmation de suppression, où il peut confirmer ou annuler l’opération de suppression.

## <a name="troubleshooting"></a>Résolution des problèmes

Pour obtenir des informations sur la résolution des problèmes avec le chargement `IFormFile`, consultez [Chargements de fichier dans ASP.NET Core : Résolution des problèmes](xref:mvc/models/file-uploads#troubleshooting).

Nous vous remercions d’avoir effectué cette introduction aux pages Razor. Vos commentaires nous intéressent. La rubrique [Bien démarrer avec MVC et EF Core](xref:data/ef-mvc/intro) est un excellent moyen de poursuivre après ce didacticiel.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Chargements de fichier dans ASP.NET Core](xref:mvc/models/file-uploads)
* [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile)

>[!div class="step-by-step"]
[Précédent : Validation](xref:tutorials/razor-pages/validation)
