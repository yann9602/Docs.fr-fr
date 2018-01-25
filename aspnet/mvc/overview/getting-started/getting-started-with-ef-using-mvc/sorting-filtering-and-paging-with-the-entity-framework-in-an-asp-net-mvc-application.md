---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: Tri, filtrage et la pagination avec Entity Framework dans une Application ASP.NET MVC | Documents Microsoft
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/01/2015
ms.topic: article
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: d54c0e133bc2f6f2021821dc16cdf86cc23a5667
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Tri, filtrage et la pagination avec Entity Framework dans une Application ASP.NET MVC
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans le didacticiel précédent, vous avez implémenté un ensemble de pages web pour les opérations CRUD de base pour `Student` entités. Dans ce didacticiel, vous allez ajouter le tri, filtrage et la fonctionnalité de pagination à la **étudiants** page d’Index. Vous allez également créer une page qui effectue le regroupement simple.

L’illustration suivante montre à quoi ressemblera la page une lorsque vous avez terminé. Les en-têtes de colonne sont des liens que l’utilisateur peut cliquer pour trier par colonne. En cliquant sur un en-tête à plusieurs reprises de colonne bascule entre croissant et décroissant d’ordre de tri.

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a>Ajouter des liens de tri de colonne à la Page d’Index les étudiants

Pour ajouter le tri à la page d’Index de l’étudiant, vous allez modifier le `Index` méthode de la `Student` contrôleur et ajoutez le code pour le `Student` vue d’Index.

### <a name="add-sorting-functionality-to-the-index-method"></a>Ajouter des fonctionnalités à la méthode de l’Index de tri

Dans *Controllers\StudentController.cs*, remplacez le `Index` méthode avec le code suivant :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

Ce code reçoit un `sortOrder` paramètre à partir de la chaîne de requête dans l’URL. La valeur de chaîne de requête est fournie par ASP.NET MVC en tant que paramètre à la méthode d’action. Le paramètre sera une chaîne qui est « Name » ou « Date », éventuellement suivie d’un trait de soulignement et de la chaîne « desc » pour spécifier l’ordre décroissant. L'ordre de tri par défaut est le tri croissant.

La première fois que la page d’Index est demandée, il n’aucune chaîne de requête. Les étudiants sont affichés en ordre croissant par `LastName`, qui est la valeur par défaut établies par le cas de passage dans le `switch` instruction. Lorsque l’utilisateur clique sur un colonne titre lien hypertexte, approprié `sortOrder` valeur est fournie dans la chaîne de requête.

Les deux `ViewBag` variables sont utilisées afin que la vue peut configurer des liens hypertexte du titre de colonne avec les valeurs de chaîne de requête appropriée :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Il s’agit d’instructions ternaires. Premier spécifie que si le `sortOrder` paramètre est null ou vide, `ViewBag.NameSortParm` doit être défini sur « nom\_desc » ; sinon, elle doit être définie sur une chaîne vide. Ces deux instructions activer l’affichage définir la colonne des liens hypertexte du titre comme suit :

| Ordre de tri en cours | Lien hypertexte du nom du dernier | Lien hypertexte de date |
| --- | --- | --- |
| Dernier nom par ordre croissant | descending | ascending |
| Dernier nom décroissant | ascending | ascending |
| Date de l’ordre croissant | ascending | descending |
| Date décroissant | ascending | ascending |

La méthode utilise [LINQ to Entities](https://msdn.microsoft.com/library/bb386964.aspx) pour spécifier la colonne à trier. Le code crée une [IQueryable](https://msdn.microsoft.com/library/bb351562.aspx) variable avant le `switch` instruction modifie dans le `switch` instruction et appelle le `ToList` méthode après le `switch` instruction. Lorsque vous créez et modifiez `IQueryable` variables, aucune requête n’est envoyée à la base de données. La requête n’est pas exécutée jusqu'à ce que vous convertissez la `IQueryable` objet dans une collection en appelant une méthode comme `ToList`. Par conséquent, ce code génère une requête unique qui n’est pas exécutée tant que la `return View` instruction.

Comme alternative à l’écriture d’instructions LINQ différentes pour chaque ordre de tri, vous pouvez créer dynamiquement une instruction LINQ. Pour plus d’informations à propos de LINQ dynamique, consultez [LINQ dynamique](https://go.microsoft.com/fwlink/?LinkID=323957).

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Ajouter des liens hypertexte vers la vue Index étudiant d’en-tête de colonne

Dans *Views\Student\Index.cshtml*, remplacez le `<tr>` et `<th>` éléments pour la ligne d’en-tête avec le code en surbrillance :

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

Ce code utilise les informations contenues dans le `ViewBag` les valeurs de chaîne de propriétés pour configurer des liens hypertexte avec la requête appropriée.

Exécutez la page et cliquez sur le **nom** et **Date d’inscription** des en-têtes de colonne pour vérifier que le tri fonctionne.

![Students_Index_page_with_sort_hyperlinks](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

Après avoir cliqué sur le **nom** titre, les étudiants sont affichés dans le dernier nom ordre décroissant.

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

## <a name="add-a-search-box-to-the-students-index-page"></a>Ajouter une zone de recherche à la Page d’Index les étudiants

Pour ajouter le filtrage sur la page d’Index des étudiants, vous allez ajouter une zone de texte et un bouton d’envoi à la vue et apporter les modifications correspondantes dans le `Index` (méthode). La zone de texte vous permet d’entrer une chaîne à rechercher dans le prénom et le champ.

### <a name="add-filtering-functionality-to-the-index-method"></a>Ajouter des fonctionnalités de filtrage à l’Index (méthode)

Dans *Controllers\StudentController.cs*, remplacez le `Index` méthode avec le code suivant (les modifications sont mises en surbrillance) :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

Vous avez ajouté un `searchString` paramètre à la `Index` (méthode). La valeur de chaîne de recherche est reçue à partir d’une zone de texte que vous ajouterez à la vue Index. Vous avez également ajouté à l’instruction LINQ un `where` clause qui sélectionne uniquement les étudiants dont prénom ou le nom contient la chaîne de recherche. L’instruction qui ajoute le [où](https://msdn.microsoft.com/library/bb535040.aspx) clause est exécutée uniquement s’il existe une valeur à rechercher.

> [!NOTE]
> Dans de nombreux cas, vous pouvez appeler la même méthode sur un jeu d’entités Entity Framework ou comme une méthode d’extension sur une collection en mémoire. Les résultats sont normalement les mêmes, mais dans certains cas, peuvent être différents.
> 
> Par exemple, l’implémentation du .NET Framework de la `Contains` méthode retourne toutes les lignes lorsque vous passez une chaîne vide à celui-ci, mais le fournisseur Entity Framework pour SQL Server Compact 4.0 retourne zéro ligne pour les chaînes vides. Par conséquent, le code dans l’exemple (placer les `Where` instruction à l’intérieur d’un `if` instruction) permet de s’assurer que vous obtenez les mêmes résultats pour toutes les versions de SQL Server. En outre, l’implémentation .NET Framework de la `Contains` méthode effectue une comparaison respectant la casse par défaut, mais les fournisseurs Entity Framework SQL Server effectuent des comparaisons sans respecter la casse par défaut. Par conséquent, l’appel le `ToUpper` méthode le test doit être explicitement pas la casse garantit que les résultats ne changent pas lorsque vous modifiez le code ultérieurement pour utiliser un référentiel, ce qui permet de renvoyer un `IEnumerable` collection au lieu d’un `IQueryable` objet. (Lorsque vous appelez le `Contains` méthode sur une `IEnumerable` collection, vous obtenez l’implémentation du .NET Framework ; lorsque vous appelez sur un `IQueryable` de l’objet, vous obtenez l’implémentation du fournisseur de base de données.)
> 
> Gestion des valeurs null peuvent également être différentes pour les fournisseurs de base de données différente ou lorsque vous utilisez un `IQueryable` objet par rapport à lorsque vous utilisez un `IEnumerable` collection. Par exemple, dans certains scénarios un `Where` telles que la condition `table.Column != 0` peut ne pas retourner les colonnes qui ont `null` comme valeur. Pour plus d’informations, consultez [une gestion incorrecte des variables null dans la clause 'where'](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).


### <a name="add-a-search-box-to-the-student-index-view"></a>Ajouter une zone de recherche à la vue d’Index étudiant

Dans *Views\Student\Index.cshtml*, ajoutez le code en surbrillance immédiatement avant l’ouverture `table` balise afin de créer une légende, une zone de texte et un **recherche** bouton.

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=5-10)]

Exécutez la page, entrez une chaîne de recherche, puis cliquez sur **recherche** pour vérifier que le filtrage fonctionne.

![Students_Index_page_with_search_box](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Notez que l’URL ne contient pas la « une » chaîne de recherche, ce qui signifie que si vous créez un signet cette page, vous n’obtiendrez la liste filtrée lorsque vous utilisez le signet. Cela s’applique également aux liens de tri de colonne, comme ils sont triés à l’intégralité de la liste. Vous allez modifier le **recherche** bouton à utiliser des chaînes de requête pour les critères de filtre plus loin dans le didacticiel.

## <a name="add-paging-to-the-students-index-page"></a>Ajouter la pagination à la Page d’Index les étudiants

Pour ajouter la pagination à la page d’Index des étudiants, vous allez commencer en installant le **PagedList.Mvc** package NuGet. Ensuite, vous allez apporter des modifications supplémentaires dans le `Index` (méthode) et ajouter des liens de la pagination dans le `Index` vue. **PagedList.Mvc** est un des nombreux pagination correcte et le tri des packages pour ASP.NET MVC et son utilisation ici est destinée uniquement, par exemple, pas comme une recommandation pour elle sur les autres options. L’illustration suivante montre les liens de la pagination.

![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

### <a name="install-the-pagedlistmvc-nuget-package"></a>Installez le Package NuGet de PagedList.MVC

NuGet **PagedList.Mvc** package installe automatiquement le **PagedList** package en tant que dépendance. Le **PagedList** package installe un `PagedList` les méthodes de type et extension de collecte pour `IQueryable` et `IEnumerable` collections. Les méthodes d’extension créent une seule page de données dans un `PagedList` collection hors de votre `IQueryable` ou `IEnumerable`et le `PagedList` collection fournit plusieurs propriétés et méthodes qui facilitent la pagination. Le **PagedList.Mvc** package installe une application d’assistance de pagination qui affiche des boutons de pagination.

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque** , puis **Package Manager Console**.

Dans le **Package Manager Console** fenêtre, assurez-vous que le **source du Package** est **nuget.org** et **projet par défaut** est **ContosoUniversity**, puis entrez la commande suivante :

`Install-Package PagedList.Mvc`

![Installer PagedList.Mvc](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Générez le projet. 

### <a name="add-paging-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de pagination pour le Index (méthode)

Dans *Controllers\StudentController.cs*, ajoutez un `using` instruction pour le `PagedList` espace de noms :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Remplacez la méthode `Index` par le code suivant :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

Ce code ajoute un `page` paramètre, un paramètre de commande de tri actuelle et un paramètre de filtre actuel pour la signature de méthode :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

La première fois que la page s’affiche, ou si l’utilisateur n’a pas cliqué sur un échange ou le lien de tri, tous les paramètres sont null. Si un lien de la pagination est activé, le `page` variable contient le numéro de page à afficher.

A `ViewBag` propriété fournit l’affichage de l’ordre de tri actuel, car il doit être inclus dans les liens de la pagination afin de conserver l’ordre de tri lors de l’échange :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Une autre propriété, `ViewBag.CurrentFilter`, fournit la vue avec la chaîne de filtre actuel. Cette valeur doit être incluse dans les liens de la pagination afin de conserver les paramètres de filtre lors de la pagination, et elle doit être restaurée à la zone de texte lorsque la page s’affiche de nouveau. Si la chaîne de recherche est modifiée au cours de la pagination, la page doit être réinitialisé à 1, parce que le nouveau filtre pour afficher des données différentes. La chaîne de recherche est modifiée quand une valeur est entrée dans la zone de texte et le bouton d’envoi est enfoncé. Dans ce cas, le `searchString` paramètre n’est pas null.

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

À la fin de la méthode, le `ToPagedList` méthode d’extension sur les étudiants `IQueryable` objet convertit la requête étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette page unique des étudiants est ensuite transmise à la vue :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Le `ToPagedList` méthode prend un numéro de page. Les deux points d’interrogation représentent le [opérateur de fusion null](https://msdn.microsoft.com/library/ms173224.aspx). L’opérateur de fusion null définit une valeur par défaut pour un type nullable ; l’expression `(page ?? 1)` signifie retourne la valeur de `page` si elle a une valeur, ou retourne 1 si `page` a la valeur null.

### <a name="add-paging-links-to-the-student-index-view"></a>Ajouter des liens de pagination à la vue Index étudiant

Dans *Views\Student\Index.cshtml*, remplacez le code existant par le code suivant. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

Le `@model` instruction en haut de la page spécifie que la vue obtient désormais une `PagedList` de l’objet au lieu d’un `List` objet.

Le `using` instruction pour `PagedList.Mvc` donne accès à l’application d’assistance MVC de boutons de pagination.

Le code utilise une surcharge de [BeginForm](https://msdn.microsoft.com/library/system.web.mvc.html.formextensions.beginform(v=vs.108).aspx) qui lui permet de spécifier [FormMethod.Get](https://msdn.microsoft.com/library/system.web.mvc.formmethod(v=vs.100).aspx/css).

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

La valeur par défaut [BeginForm](https://msdn.microsoft.com/library/system.web.mvc.html.formextensions.beginform(v=vs.108).aspx) envoie des données de formulaire avec une publication, ce qui signifie que les paramètres sont passés dans le corps du message HTTP et non dans l’URL sous forme de chaînes de requête. Lorsque vous spécifiez HTTP GET, les données du formulaire sont passées dans l’URL sous forme de chaînes de requête, ce qui permet aux utilisateurs de l’URL de signet. Le [recommandations du W3C pour l’utilisation de HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) est recommandé que vous devez utiliser GET lorsque l’action n’entraîne pas une mise à jour.

La zone de texte est initialisée avec la chaîne de recherche en cours lorsque vous cliquez sur une nouvelle page, vous voyez la chaîne de recherche actuel.

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

Les liens d’en-tête de colonne permet de passer la chaîne de recherche en cours au contrôleur afin que l’utilisateur peut trier les résultats de filtre de la chaîne de requête :

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

Le nombre actuel de page et le nombre total de pages est affiché.

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

S’il n’y a aucune page à afficher, « Page 0 0 » s’affiche. (Dans ce cas, le numéro de page est supérieur au nombre de page, car `Model.PageNumber` est 1, et `Model.PageCount` est 0.)

Les boutons de pagination sont affichés par le `PagedListPager` application d’assistance :

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

Le `PagedListPager` d’assistance offre plusieurs options que vous pouvez personnaliser, y compris les URL et styles. Pour plus d’informations, consultez [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) sur le site GitHub.

Exécutez la page.

![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

Cliquez sur les liens de la pagination dans différents ordres de tri s’assurer que la pagination fonctionne. Entrez une chaîne de recherche, puis recommencez la pagination pour vérifier que la pagination également fonctionne correctement avec le tri et de filtrage.

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

## <a name="create-an-about-page-that-shows-student-statistics"></a>Créer une sur la Page qui affiche les statistiques de l’étudiant

Pour Contoso Université du site Web sur la page, vous afficherez le nombre d’étudiants inscrits pour chaque date d’inscription. Cela nécessite de regroupement et simples des calculs sur les groupes. Pour ce faire, vous devez procédez comme suit :

- Créer une classe de modèle d’affichage pour les données dont vous avez besoin pour passer à la vue.
- Modifier la `About` méthode dans la `Home` contrôleur.
- Modifier la `About` vue.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créer un *ViewModel* dans le dossier de projet. Dans ce dossier, ajoutez un fichier de classe *EnrollmentDateGroup.cs* et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a>Modifier le contrôleur Home

Dans *HomeController.cs*, ajoutez le code suivant `using` instructions en haut du fichier :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

Ajoutez une variable de classe pour le contexte de base de données immédiatement après l’accolade ouvrante de la classe :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

Remplacez la méthode `About` par le code suivant :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

L’instruction LINQ regroupe les entités étudiant par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection de `EnrollmentDateGroup` afficher les objets de modèle.

Ajouter un `Dispose` méthode :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a>Modifier le sur la vue

Remplacez le code dans le *Views\Home\About.cshtml* fichier avec le code suivant :

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

Exécutez l’application et cliquez sur le **sur** lien. Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.

![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez vu comment créer un modèle de données et mettre en œuvre CRUD de base, le tri, le filtrage, la pagination et la fonctionnalité de regroupement. Dans le didacticiel suivant vous commencerez examinant rubriques plus avancées en développant le modèle de données.

Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer. Vous pouvez également demander de nouvelles rubriques à [afficher Me comment avec le Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Vous trouverez des liens vers d’autres ressources Entity Framework dans [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)
[Suivant](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)
