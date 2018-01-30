---
title: Pages Razor avec EF Core - trier, filtrer, la pagination - 3 de 8
author: rick-anderson
description: "Dans ce didacticiel, vous allez ajouter le tri, filtrage et d’échange vers la page à l’aide d’ASP.NET Core et Entity Framework Core."
ms.author: riande
ms.date: 10/22/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9c1ee6f8c00f3cd501ea86fbf73f51ae540a010a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-razor-pages-3-of-8"></a>Le tri, le filtrage, la pagination et le regroupement - Core EF avec les Pages Razor (3 sur 8)

Par [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), et [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dans ce didacticiel, le tri, le filtrage, de regroupement et la pagination, la fonctionnalité est ajoutée.

L’illustration suivante montre une page de fin. Les en-têtes de colonne sont des liens hypertexte vers la colonne de tri. En cliquant sur un en-tête à plusieurs reprises de colonne bascule entre croissant et décroissant d’ordre de tri.

![Page d’index les étudiants](sort-filter-page/_static/paging.png)

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

## <a name="add-sorting-to-the-index-page"></a>Ajouter le tri à la page d’Index

Ajouter des chaînes de la *Students/Index.cshtml.cs* `PageModel` pour contenir les paramètres de tri :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]


Mise à jour la *Students/Index.cshtml.cs* `OnGetAsync` avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Le code précédent reçoit un `sortOrder` paramètre à partir de la chaîne de requête dans l’URL. L’URL (y compris la chaîne de requête) est généré par le [application d’assistance de balise d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

Le `sortOrder` paramètre est « Name » ou « Date ». Le `sortOrder` paramètre peut être suivi par « _desc » pour spécifier l’ordre décroissant. L'ordre de tri par défaut est le tri croissant.

Lorsque la page d’Index est demandée à partir de la **étudiants** lier, il n’existe aucune chaîne de requête. Les étudiants sont affichés dans l’ordre croissant par nom. L’ordre croissant en fonction du nom est la valeur par défaut (passage de la casse) dans le `switch` instruction. Lorsque l’utilisateur clique sur un lien d’en-tête de colonne approprié `sortOrder` valeur est fournie dans la valeur de chaîne de requête.

`NameSort`et `DateSort` sont utilisés par la Page Razor pour configurer des liens hypertexte du titre de colonne avec les valeurs de chaîne de requête appropriée :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Le code suivant contient du langage c# [? : opérateur](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

La première ligne spécifie que lorsque `sortOrder` est null ou vide, `NameSort` a la valeur « name_desc ». Si `sortOrder` est **pas** null ou vide, `NameSort` est définie sur une chaîne vide.

Le `?: operator` est également appelé l’opérateur ternaire.

Ces deux instructions activer l’affichage définir la colonne des liens hypertexte du titre comme suit :

| Ordre de tri en cours | Lien hypertexte du nom du dernier | Lien hypertexte de date |
|:--------------------:|:-------------------:|:--------------:|
| Dernier nom par ordre croissant | descending        | ascending      |
| Dernier nom décroissant | ascending           | ascending      |
| Date de l’ordre croissant       | ascending           | descending     |
| Date décroissant      | ascending           | ascending      |

La méthode utilise LINQ to Entities pour spécifier la colonne à trier. Le code initialise un `IQueryable<Student> ` avant l’instruction switch et en modifiant l’instruction switch :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-)]

 Lorsqu’un`IQueryable` est créé ou modifié, aucune requête n’est envoyé à la base de données. La requête n’est pas exécutée tant que le `IQueryable` objet est converti en une collection. `IQueryable`sont converties en une collection en appelant une méthode comme `ToListAsync`. Par conséquent, le `IQueryable` code les résultats dans une requête unique qui n’est pas exécutée tant que l’instruction suivante :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`Impossible d’obtenir documentée avec un grand nombre de colonnes.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Ajouter des liens hypertexte du titre de colonne à la vue de l’Index de l’étudiant

Remplacez le code dans *Students/Index.cshtml*, avec les éléments suivants en surbrillance le code :

[!code-html[](intro/samples/cu/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Le code précédent :

* Ajoute des liens hypertexte vers le `LastName` et `EnrollmentDate` des en-têtes de colonnes.
* Utilise les informations contenues dans `NameSort` et `DateSort` pour définir des liens hypertexte avec les valeurs d’ordre de tri en cours.

Pour vérifier que le tri fonctionne :

* Exécutez l’application et sélectionnez le **étudiants** onglet.
* Cliquez sur **nom**.
* Cliquez sur **Date d’inscription**.

Pour obtenir une meilleure compréhension du code :

* Dans *Student/Index.cshtml.cs*, définir un point d’arrêt sur `switch (sortOrder)`.
* Ajouter un espion pour `NameSort` et `DateSort`.
* Dans *Student/Index.cshtml*, définir un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Parcourez le débogueur.

## <a name="add-a-search-box-to-the-students-index-page"></a>Ajouter une zone de recherche à la page d’Index des étudiants

Pour ajouter le filtrage sur la page d’Index des étudiants :

* Une zone de texte et un bouton d’envoi est ajouté à la Page Razor. La zone de texte fournit une chaîne de recherche sur le nom de la première ou la dernière.
* Le modèle de page est mise à jour pour utiliser la valeur de zone de texte.

### <a name="add-filtering-functionality-to-the-index-method"></a>Ajouter des fonctionnalités de filtrage à l’Index (méthode)

Mise à jour la *Students/Index.cshtml.cs* `OnGetAsync` avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Le code précédent :

* Ajoute le `searchString` paramètre à la `OnGetAsync` (méthode). La valeur de chaîne de recherche est reçue à partir d’une zone de texte qui est ajoutée dans la section suivante.
* Ajouté à l’instruction LINQ un `Where` clause. Le `Where` clause sélectionne uniquement les étudiants dont prénom ou le nom contient la chaîne de recherche. L’instruction LINQ est exécutée uniquement s’il existe une valeur à rechercher.

Remarque : Le précédent code appelle la `Where` méthode sur une `IQueryable` objet et le filtre est traité sur le serveur. Dans certains scénarios, tha application peut appeler le `Where` méthode comme méthode d’extension sur une collection en mémoire. Par exemple, supposons que `_context.Students` change à partir de EF `DbSet` à une méthode de référentiel qui retourne un `IEnumerable` collection. Le résultat serait normalement le même, mais dans certains cas, peut être différent.

Par exemple, l’implémentation du .NET Framework de `Contains` effectue une comparaison respectant la casse par défaut. Dans SQL Server, `Contains` respect de la casse est déterminé par le paramètre de classement de l’instance de SQL Server. SQL Server par défaut à la casse. `ToUpper`peut être appelée pour rendre le test explicitement pas la casse :

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Le code précédent assureriez que résultats ne sont pas la casse si le code change pour utiliser `IEnumerable`. Lorsque `Contains` est appelée sur une `IEnumerable` collection, le .NET Core implémentation est utilisée. Lorsque `Contains` est appelée sur une `IQueryable` de l’objet, l’implémentation de base de données est utilisée. Retourner un `IEnumerable` à partir d’un référentiel peut avoir un penality significative des performances :

1. Toutes les lignes sont retournées à partir du serveur de base de données.
1. Le filtre est appliqué à toutes les lignes retournées dans l’application.

Il existe une baisse des performances de l’appel `ToUpper`. Le `ToUpper` code ajoute une fonction dans la clause WHERE de l’instruction SELECT de TSQL. La fonction ajoutée empêche l’optimiseur d’utiliser un index. Étant donné que SQL est installé sans respecter la casse, il est préférable d’éviter le `ToUpper` appeler lorsqu’il n’est pas nécessaire.

### <a name="add-a-search-box-to-the-student-index-view"></a>Ajouter une zone de recherche à la vue d’Index étudiant

Dans *Views/Student/Index.cshtml*, ajoutez le code en surbrillance suivant pour créer un **recherche** bouton et chrome assortie.

[!code-html[](intro/samples/cu/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Le code précédent utilise la `<form>` [d’assistance de balise](xref:mvc/views/tag-helpers/intro) pour ajouter la zone de texte de recherche et le bouton. Par défaut, le `<form>` application d’assistance de balise envoie des données de formulaire avec une publication. Avec la publication, les paramètres sont passés dans le corps du message HTTP et non dans l’URL. Lorsque HTTP GET est utilisée, les données du formulaire sont transmises dans l’URL sous forme de chaînes de requête. En passant les données avec des chaînes de requête permet aux utilisateurs de l’URL de signet. Le [recommandations du W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) est recommandé que GET doit être utilisée lorsque l’action ne produit pas une mise à jour.

Testez l’application :

* Sélectionnez le **étudiants** onglet et entrez une chaîne de recherche.
* Sélectionnez **recherche**.

Notez que l’URL contient la chaîne de recherche.

```html
http://localhost:5000/Students?SearchString=an
```

Si la page est un signet, le signet contient l’URL de la page et le `SearchString` chaîne de requête. Le `method="get"` dans le `form` balise est ce qui a provoqué la chaîne de requête à générer.

Actuellement, un lien de tri de titre de colonne est sélectionné, le filtre de valeur à partir de la **recherche** zone est perdue. La valeur de filtre perdue est fixe dans la section suivante.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Ajouter la fonctionnalité de pagination à la page d’Index des étudiants

Dans cette section, un `PaginatedList` classe est créée pour prendre en charge la pagination. Le `PaginatedList` classe utilise `Skip` et `Take` instructions pour filtrer les données sur le serveur au lieu de récupérer toutes les lignes de la table. L’illustration suivante montre les boutons de la pagination.

![Page avec des liens de pagination d’index les étudiants](sort-filter-page/_static/paging.png)

Dans le dossier du projet, créez `PaginatedList.cs` avec le code suivant :

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

Le `CreateAsync` méthode dans le code précédent utilise la taille de la page et le numéro de page et applique `Skip` et `Take` instructions pour le `IQueryable`. Lorsque `ToListAsync` est appelée sur le `IQueryable`, elle retourne une liste contenant uniquement la page demandée. Les propriétés `HasPreviousPage` et `HasNextPage` sont utilisés pour activer ou désactiver **précédent** et **suivant** boutons de pagination.

Le `CreateAsync` méthode est utilisée pour créer le `PaginatedList<T>`. Un constructeur ne peut pas créer le `PaginatedList<T>` de l’objet, les constructeurs ne peuvent pas exécuter du code asynchrone.

## <a name="add-paging-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de pagination pour le Index (méthode)

Dans *Students/Index.cshtml.cs*, le type de mise à jour `Student` de `IList<Student>` à `PaginatedList<Student>`:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Mise à jour la *Students/Index.cshtml.cs* `OnGetAsync` avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-)]

Le code précédent ajoute l’index de page actuel `sortOrder`et le `currentFilter` pour la signature de méthode.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Tous les paramètres sont null quand :

* La page est appelée à partir de la **étudiants** lien.
* L’utilisateur n’a pas cliqué sur un échange ou du tri de lien.

Lorsque l’utilisateur clique sur un lien de la pagination, la variable d’index de page contient le numéro de page à afficher.

`CurrentSort`Fournit la Page Razor avec l’ordre de tri en cours. L’ordre de tri en cours doit être inclus dans les liens de la pagination pour conserver l’ordre de tri lors de la pagination.

`CurrentFilter`Fournit la Page Razor avec la chaîne de filtre actuel. Le `CurrentFilter` valeur :

* Doit être inclus dans les liens de la pagination afin de conserver les paramètres de filtre lors de la pagination.
* Doit être restaurée à la zone de texte lorsque la page s’affiche de nouveau.

Si la chaîne de recherche est modifiée lors de la pagination, la page est réinitialisée à 1. La page doit être réinitialisé à 1, car le nouveau filtre peut entraîner des données différentes à afficher. Lors de l’entrée une valeur à rechercher et **Submit** est sélectionnée :

* La chaîne de recherche est modifiée.
* Le `searchString` paramètre n’est pas null.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Le `PaginatedList.CreateAsync` méthode convertit la requête de student à une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette page unique des étudiants est passée à la Page Razor.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Les deux points d’interrogation dans `PaginatedList.CreateAsync` représentent le [opérateur de fusion null](https://docs.microsoft.com/ dotnet/csharp/language-reference/operators/null-conditional-operator). L’opérateur de fusion null définit une valeur par défaut pour un type nullable. L’expression `(pageIndex ?? 1)` signifie retourne la valeur de `pageIndex` si elle a la valeur. Si `pageIndex` n’a de valeur, retournent 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Ajouter des liens de pagination pour les étudiants Page Razor

Mettre à jour le balisage dans *Students/Index.cshtml*. Les modifications sont mises en surbrillance :

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-)]

Les liens d’en-tête de colonne utilisent la chaîne de requête pour passer la chaîne de recherche en cours pour le `OnGetAsync` méthode afin que l’utilisateur peut trier au sein des résultats du filtrage :

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=28-31)]

Les boutons de pagination sont affichés par les programmes d’assistance de balise :

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=72-)]

Exécuter l’application et accédez à la page d’étudiants.

* Pour que la pagination fonctionne, cliquez sur les liens de la pagination dans différents ordres de tri.
* Pour vérifier que la pagination fonctionne correctement avec le tri et le filtrage, entrez une chaîne de recherche et recommencez la pagination.

![Page avec des liens de pagination d’index les étudiants](sort-filter-page/_static/paging.png)

Pour obtenir une meilleure compréhension du code :

* Dans *Student/Index.cshtml.cs*, définir un point d’arrêt sur `switch (sortOrder)`.
* Ajouter un espion pour `NameSort`, `DateSort`, `CurrentSort`, et `Model.Student.PageIndex`.
* Dans *Student/Index.cshtml*, définir un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Parcourez le débogueur.

## <a name="update-the-about-page-to-show-student-statistics"></a>Mise à jour de la page à propos de pour afficher les statistiques de l’étudiant

Dans cette étape, *Pages/About.cshtml* est mis à jour pour afficher le nombre d’étudiants inscrits pour chaque date d’inscription. La mise à jour utilise le regroupement et comprend les étapes suivantes :

* Créer une classe de modèle d’affichage pour les données utilisées par le **sur** Page.
* Modifier le modèle sur la Page de Razor et page.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créer un *SchoolViewModels* dossier dans le *modèles* dossier.

Dans le *SchoolViewModels* dossier, ajoutez un *EnrollmentDateGroup.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Mettre à jour le modèle de page à propos de

Mise à jour la *Pages/About.cshtml.cs* fichier avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/About.cshtml.cs)]

L’instruction LINQ regroupe les entités étudiant par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection de `EnrollmentDateGroup` afficher les objets de modèle.

Remarque : Les LINQ `group` commande n’est pas actuellement prise en charge par EF Core. Dans le code précédent, tous les étudiants sont retournées à partir de SQL Server. Le `group` commande est appliquée dans l’application de Pages Razor, pas sur le serveur SQL Server. EF Core 2.1 prend en charge cette LINQ `group` (opérateur) et le regroupement se produit sur le serveur SQL Server. Consultez [relationnel : prend en charge la traduction GroupBy() SQL](https://github.com/aspnet/EntityFrameworkCore/issues/2341). [EF Core 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/roadmap) sera proposée avec .NET Core 2.1. Pour plus d’informations, consultez la [feuille de route .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

### <a name="modify-the-about-razor-page"></a>Modifier le sur la Page Razor

Remplacez le code dans le *Views/Home/About.cshtml* fichier avec le code suivant :

[!code-html[](intro/samples/cu/Pages/About.cshtml)]

Exécuter l’application et accédez à la page à propos. Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez le [application terminée pour cette étape](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Sur la page](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Débogage d’une source ASP.NET Core 2.x](https://github.com/aspnet/Docs/issues/4155)

Dans le didacticiel suivant, l’application utilise des migrations pour mettre à jour le modèle de données.

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/crud)
[Suivant](xref:data/ef-rp/migrations)
