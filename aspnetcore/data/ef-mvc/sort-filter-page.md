---
title: "Cœur de ASP.NET MVC avec EF Core - trier, filtrer, la pagination - 3 sur 10"
author: tdykstra
description: "Dans ce didacticiel, vous allez ajouter le tri, filtrage et d’échange vers la page à l’aide d’ASP.NET Core et Entity Framework Core."
ms.author: tdykstra
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 6da2073b18f6fff9738808c84441e59240caefe3
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-aspnet-core-mvc-tutorial-3-of-10"></a>Le tri, le filtrage, la pagination et le regroupement - Core EF avec le didacticiel ASP.NET Core MVC (partie 3 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous implémenté un ensemble de pages web pour les opérations CRUD de base pour les entités de Student. Dans ce didacticiel, vous allez ajouter le tri, filtrage et la fonctionnalité de pagination à la page d’Index d’étudiants. Vous allez également créer une page qui effectue le regroupement simple.

L’illustration suivante montre à quoi ressemblera la page une lorsque vous avez terminé. Les en-têtes de colonne sont des liens que l’utilisateur peut cliquer pour trier par colonne. En cliquant sur un en-tête à plusieurs reprises de colonne bascule entre croissant et décroissant d’ordre de tri.

![Page d’index les étudiants](sort-filter-page/_static/paging.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a>Ajouter des liens de tri de colonne à la Page d’Index les étudiants

Pour ajouter le tri à la page d’Index de l’étudiant, vous allez modifier le `Index` méthode du contrôleur étudiants et ajouter du code à la vue de l’Index de l’étudiant.

### <a name="add-sorting-functionality-to-the-index-method"></a>Ajouter les fonctionnalités de tri pour l’Index (méthode)

Dans *StudentsController.cs*, remplacez le `Index` méthode avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Ce code reçoit un `sortOrder` paramètre à partir de la chaîne de requête dans l’URL. La valeur de chaîne de requête est fournie par ASP.NET MVC de base en tant que paramètre à la méthode d’action. Le paramètre sera une chaîne qui est « Name » ou « Date », éventuellement suivie d’un trait de soulignement et de la chaîne « desc » pour spécifier l’ordre décroissant. L'ordre de tri par défaut est le tri croissant.

La première fois que la page d’Index est demandée, il n’aucune chaîne de requête. Les étudiants sont affichés dans l’ordre croissant par nom, qui est la valeur par défaut établies par le cas de passage dans le `switch` instruction. Lorsque l’utilisateur clique sur un colonne titre lien hypertexte, approprié `sortOrder` valeur est fournie dans la chaîne de requête.

Les deux `ViewData` éléments (NameSortParm et DateSortParm) sont utilisés par la vue pour configurer des liens hypertexte du titre de colonne avec les valeurs de chaîne de requête appropriée.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Il s’agit d’instructions ternaires. La première condition spécifie que si le `sortOrder` paramètre est null ou vide, NameSortParm doit être défini sur « name_desc » ; sinon, il doit être défini sur une chaîne vide. Ces deux instructions activer l’affichage définir la colonne des liens hypertexte du titre comme suit :

|  Ordre de tri en cours  | Lien hypertexte du nom du dernier | Lien hypertexte de date |
|:--------------------:|:-------------------:|:--------------:|
| Dernier nom par ordre croissant  | descending          | ascending      |
| Dernier nom décroissant | ascending           | ascending      |
| Date de l’ordre croissant       | ascending           | descending     |
| Date décroissant      | ascending           | ascending      |

La méthode utilise LINQ to Entities pour spécifier la colonne à trier. Le code crée un `IQueryable` variable avant l’instruction switch, il modifie dans l’instruction switch et appelle le `ToListAsync` méthode après le `switch` instruction. Lorsque vous créez et modifiez `IQueryable` variables, aucune requête n’est envoyée à la base de données. La requête n’est pas exécutée jusqu'à ce que vous convertissez la `IQueryable` objet dans une collection en appelant une méthode comme `ToListAsync`. Par conséquent, ce code génère une requête unique qui n’est pas exécutée tant que la `return View` instruction.

Ce code peut obtenir documentée avec un grand nombre de colonnes. [Le didacticiel dernière de cette série](advanced.md#dynamic-linq) montre comment écrire du code qui vous permet de passer le nom de la `OrderBy` colonne dans une variable de chaîne.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Ajouter des liens hypertexte du titre de colonne à la vue de l’Index de l’étudiant

Remplacez le code dans *Views/Students/Index.cshtml*, avec le code suivant pour ajouter des liens hypertexte du titre de colonne. Les lignes modifiées sont mises en surbrillance.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Ce code utilise les informations contenues dans `ViewData` les valeurs de chaîne de propriétés pour configurer des liens hypertexte avec la requête appropriée.

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur le **nom** et **Date d’inscription** des en-têtes de colonne pour vérifier que le tri fonctionne.

![Page d’index étudiants dans l’ordre de nom](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box-to-the-students-index-page"></a>Ajouter une zone de recherche à la page d’Index des étudiants

Pour ajouter le filtrage sur la page d’Index des étudiants, vous allez ajouter une zone de texte et un bouton d’envoi à la vue et apporter les modifications correspondantes dans le `Index` (méthode). La zone de texte vous permet d’entrer une chaîne à rechercher dans le prénom et le champ.

### <a name="add-filtering-functionality-to-the-index-method"></a>Ajouter des fonctionnalités de filtrage à l’Index (méthode)

Dans *StudentsController.cs*, remplacez le `Index` méthode avec le code suivant (les modifications sont mises en surbrillance).

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Vous avez ajouté un `searchString` paramètre à la `Index` (méthode). La valeur de chaîne de recherche est reçue à partir d’une zone de texte que vous ajouterez à la vue Index. Vous avez également ajouté à l’instruction LINQ where clause qui sélectionne uniquement les étudiants dont prénom ou le nom contient la chaîne de recherche. L’instruction qui ajoute where clause est exécutée uniquement s’il existe une valeur à rechercher.

> [!NOTE]
> Ici vous appelez le `Where` méthode sur une `IQueryable` objet et le filtre seront traités sur le serveur. Dans certains scénarios vous pouvez appeler la `Where` méthode comme méthode d’extension sur une collection en mémoire. (Par exemple, supposons que vous modifiez la référence à `_context.Students` ainsi qu’au lieu d’un FE `DbSet` il fait référence à une méthode de référentiel qui retourne un `IEnumerable` collection.) Le résultat serait normalement le même, mais dans certains cas, peut être différent.
>
>Par exemple, l’implémentation du .NET Framework de la `Contains` méthode effectue une comparaison respectant la casse par défaut, mais dans SQL Server, cela est déterminé par le paramètre de classement de l’instance de SQL Server. Ce paramètre par défaut à la casse. Vous pouvez appeler la `ToUpper` méthode le test doit être explicitement pas la casse : *où (s = > s.LastName.ToUpper(). Contains(searchString.ToUpper())*. Qui s’assurent que que résultats restent le même si vous modifiez le code ultérieurement pour utiliser un référentiel qui retourne un `IEnumerable` collection au lieu d’un `IQueryable` objet. (Lorsque vous appelez le `Contains` méthode sur une `IEnumerable` collection, vous obtenez l’implémentation du .NET Framework ; lorsque vous appelez sur un `IQueryable` de l’objet, vous obtenez l’implémentation du fournisseur de base de données.) Toutefois, il est d’une baisse des performances de cette solution. Le `ToUpper` code place une fonction dans la clause WHERE de l’instruction SELECT de TSQL. Qui empêche l’optimiseur d’à l’aide d’un index. Étant donné que SQL est installé principalement sans respecter la casse, il est préférable d’éviter le `ToUpper` code jusqu'à ce que vous migrez vers un magasin de données qui respecte la casse.

### <a name="add-a-search-box-to-the-student-index-view"></a>Ajouter une zone de recherche à la vue d’Index étudiant

Dans *Views/Student/Index.cshtml*, ajoutez le code en surbrillance immédiatement avant l’ouverture de balise table afin de créer une légende, une zone de texte et un **recherche** bouton.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Ce code utilise le `<form>` [d’assistance de balise](xref:mvc/views/tag-helpers/intro) pour ajouter la zone de texte de recherche et le bouton. Par défaut, le `<form>` application d’assistance de balise envoie des données de formulaire avec une publication, ce qui signifie que les paramètres sont passés dans le corps du message HTTP et non dans l’URL sous forme de chaînes de requête. Lorsque vous spécifiez HTTP GET, les données du formulaire sont passées dans l’URL sous forme de chaînes de requête, ce qui permet aux utilisateurs de l’URL de signet. W3C instructions il est conseillé que vous devez utiliser obtenir lors de l’action n’entraîne pas une mise à jour.

Exécuter l’application, sélectionnez le **étudiants** onglet, entrez une chaîne de recherche, puis cliquez sur Rechercher pour vérifier que le filtrage fonctionne.

![Page d’index de stagiaires de filtrage](sort-filter-page/_static/filtering.png)

Notez que l’URL contient la chaîne de recherche.

```html
http://localhost:5813/Students?SearchString=an
```

Si vous créez un signet cette page, vous obtenez la liste filtrée lorsque vous utilisez le signet. Ajout de `method="get"` à la `form` balise est ce qui a provoqué la chaîne de requête à générer.

À ce stade, si vous cliquez sur un lien de tri de titre de colonne vous perdez la valeur de filtre que vous avez entré dans le **recherche** boîte. Vous allez résoudre cela dans la section suivante.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Ajouter la fonctionnalité de pagination à la page d’Index des étudiants

Pour ajouter la pagination à la page d’Index des étudiants, vous allez créer un `PaginatedList` classe utilise `Skip` et `Take` instructions pour filtrer les données sur le serveur au lieu de toujours récupérer toutes les lignes de la table. Ensuite, vous allez apporter des modifications supplémentaires dans le `Index` (méthode) et ajouter des boutons de la pagination à la `Index` vue. L’illustration suivante montre les boutons de la pagination.

![Page avec des liens de pagination d’index les étudiants](sort-filter-page/_static/paging.png)

Dans le dossier du projet, créez `PaginatedList.cs`, puis remplacez le code du modèle par le code suivant.

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

Le `CreateAsync` méthode dans ce code prend la taille de la page et le numéro de page et applique `Skip` et `Take` instructions pour le `IQueryable`. Lorsque `ToListAsync` est appelée sur le `IQueryable`, il retourne une liste contenant uniquement la page demandée. Les propriétés `HasPreviousPage` et `HasNextPage` peut être utilisé pour activer ou désactiver **précédent** et **suivant** boutons de pagination.

A `CreateAsync` méthode est utilisée au lieu d’un constructeur pour créer le `PaginatedList<T>` de l’objet, car les constructeurs ne peuvent pas exécuter du code asynchrone.

## <a name="add-paging-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de pagination pour le Index (méthode)

Dans *StudentsController.cs*, remplacez le `Index` méthode avec le code suivant.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Ce code ajoute un paramètre de numéro de page, un paramètre de commande de tri actuelle et un paramètre de filtre actuel pour la signature de méthode.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? page)
```

La première fois que la page s’affiche, ou si l’utilisateur n’a pas cliqué sur un échange ou le lien de tri, tous les paramètres sont null.  Si un lien de la pagination est activé, la variable de page contient le numéro de page à afficher.

Le `ViewData` élément nommé CurrentSort fournit la vue de l’ordre de tri actuel, car il doit être incluse dans les liens de la pagination afin de conserver l’ordre de tri lors de la pagination.

Le `ViewData` élément nommé FiltreActif fournit la vue avec la chaîne de filtre actuel. Cette valeur doit être incluse dans les liens de la pagination afin de conserver les paramètres de filtre lors de la pagination, et elle doit être restaurée à la zone de texte lorsque la page s’affiche de nouveau.

Si la chaîne de recherche est modifiée au cours de la pagination, la page doit être réinitialisé à 1, parce que le nouveau filtre pour afficher des données différentes. La chaîne de recherche est modifiée quand une valeur est entrée dans la zone de texte et le bouton d’envoi est enfoncé. Dans ce cas, le `searchString` paramètre n’est pas null.

```csharp
if (searchString != null)
{
    page = 1;
}
else
{
    searchString = currentFilter;
}
```

À la fin de la `Index` (méthode), la `PaginatedList.CreateAsync` méthode convertit la requête de student à une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette page unique des étudiants est ensuite passée à la vue.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), page ?? 1, pageSize));
```

Le `PaginatedList.CreateAsync` méthode prend un numéro de page. Les deux points d’interrogation représenter l’opérateur de fusion null. L’opérateur de fusion null définit une valeur par défaut pour un type nullable ; l’expression `(page ?? 1)` signifie retourne la valeur de `page` si elle a une valeur, ou retourne 1 si `page` a la valeur null.

## <a name="add-paging-links-to-the-student-index-view"></a>Ajouter des liens de pagination à la vue de l’Index de l’étudiant

Dans *Views/Students/Index.cshtml*, remplacez le code existant par le code suivant. Les modifications sont mises en surbrillance.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Le `@model` instruction en haut de la page spécifie que la vue obtient désormais une `PaginatedList<T>` de l’objet au lieu d’un `List<T>` objet.

Les liens d’en-tête de colonne permet de passer la chaîne de recherche en cours au contrôleur afin que l’utilisateur peut trier les résultats de filtre de la chaîne de requête :

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Les boutons de pagination sont affichés par les programmes d’assistance de balise :

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-page="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Exécutez l’application et accédez à la page d’étudiants.

![Page avec des liens de pagination d’index les étudiants](sort-filter-page/_static/paging.png)

Cliquez sur les liens de la pagination dans différents ordres de tri s’assurer que la pagination fonctionne. Entrez une chaîne de recherche, puis recommencez la pagination pour vérifier que la pagination également fonctionne correctement avec le tri et de filtrage.

## <a name="create-an-about-page-that-shows-student-statistics"></a>Créer une page à propos qui affiche les statistiques de l’étudiant

Pour le site de Web Contoso University **sur** page, vous afficherez le nombre d’étudiants inscrits pour chaque date d’inscription. Cela nécessite de regroupement et simples des calculs sur les groupes. Pour ce faire, vous devez procédez comme suit :

* Créer une classe de modèle d’affichage pour les données dont vous avez besoin pour passer à la vue.

* Modifiez la méthode à propos du contrôleur Home.

* Modifier la vue à propos de.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créer un *SchoolViewModels* dossier dans le *modèles* dossier.

Dans le nouveau dossier, ajoutez un fichier de classe *EnrollmentDateGroup.cs* et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Modifier le contrôleur Home

Dans *HomeController.cs*, ajoutez le code suivant à l’aide d’instructions en haut du fichier :

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Ajouter une variable de classe pour le contexte de base de données immédiatement après l’accolade ouvrante de la classe et en obtenir une instance du contexte ASP.NET Core DI :

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Remplacez la méthode `About` par le code suivant :

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

L’instruction LINQ regroupe les entités étudiant par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection de `EnrollmentDateGroup` afficher les objets de modèle.
> [!NOTE] 
> Dans la 1.0 version de Entity Framework Core, le jeu de résultats entier est retourné au client, et de regroupement est effectué sur le client. Dans certains scénarios, cela pourrait créer des problèmes de performances. Veillez à tester les performances avec les volumes de données de production et si nécessaire utilisez brute SQL pour effectuer le regroupement sur le serveur. Pour plus d’informations sur l’utilisation de SQL brute, consultez [le dernier didacticiel de cette série](advanced.md).

### <a name="modify-the-about-view"></a>Modifier le sur la vue

Remplacez le code dans le *Views/Home/About.cshtml* fichier avec le code suivant :

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Exécutez l’application et accédez à la page à propos de. Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.

![Sur la page](sort-filter-page/_static/about.png)

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez vu comment effectuer le tri, le filtrage, la pagination et regroupement. Dans l’étape suivante du didacticiel, vous allez apprendre à gérer les modifications du modèle de données à l’aide des migrations.

>[!div class="step-by-step"]
[Précédent](crud.md)
[Suivant](migrations.md)  
