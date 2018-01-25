---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: Tri, filtrage et la pagination avec Entity Framework dans une Application ASP.NET MVC (partie 3 sur 10) | Documents Microsoft
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 8af630e0-fffa-4110-9eca-c96e201b2724
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: f9b68abeba19561a327bad5ee4be80d79af1a550
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application-3-of-10"></a>Tri, filtrage et la pagination avec Entity Framework dans une Application ASP.NET MVC (partie 3 sur 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Vous pouvez démarrer la série de didacticiels à partir du début ou [télécharger un projet de démarrage pour ce chapitre](building-the-ef5-mvc4-chapter-downloads.md) et Démarrer ici.
> 
> > [!NOTE] 
> > 
> > Si vous rencontrez un problème que vous ne pouvez pas résoudre, [télécharger le chapitre terminé](building-the-ef5-mvc4-chapter-downloads.md) et essayez de reproduire le problème. En règle générale, vous trouverez la solution au problème en comparant votre code pour le code terminé. Pour certaines erreurs courantes et comment les résoudre, consultez [erreurs et des solutions de contournement.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


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

Vous avez ajouté un `searchString` paramètre à la `Index` (méthode). Vous avez également ajouté à l’instruction LINQ un `where` clause sélectionne uniquement les étudiants dont prénom ou le nom contient la chaîne de recherche. La valeur de chaîne de recherche est reçue à partir d’une zone de texte que vous ajouterez à la vue Index. L’instruction qui ajoute le [où](https://msdn.microsoft.com/library/bb535040.aspx) clause est exécutée uniquement s’il existe une valeur à rechercher.

> [!NOTE]
> Dans de nombreux cas, vous pouvez appeler la même méthode sur un jeu d’entités Entity Framework ou comme une méthode d’extension sur une collection en mémoire. Les résultats sont normalement les mêmes, mais dans certains cas, peuvent être différents. Par exemple, l’implémentation du .NET Framework de la `Contains` méthode retourne toutes les lignes lorsque vous passez une chaîne vide à celui-ci, mais le fournisseur Entity Framework pour SQL Server Compact 4.0 retourne zéro ligne pour les chaînes vides. Par conséquent, le code dans l’exemple (placer les `Where` instruction à l’intérieur d’un `if` instruction) permet de s’assurer que vous obtenez les mêmes résultats pour toutes les versions de SQL Server. En outre, l’implémentation .NET Framework de la `Contains` méthode effectue une comparaison respectant la casse par défaut, mais les fournisseurs Entity Framework SQL Server effectuent des comparaisons sans respecter la casse par défaut. Par conséquent, l’appel le `ToUpper` méthode le test doit être explicitement pas la casse garantit que les résultats ne changent pas lorsque vous modifiez le code ultérieurement pour utiliser un référentiel, ce qui permet de renvoyer un `IEnumerable` collection au lieu d’un `IQueryable` objet. (Lorsque vous appelez le `Contains` méthode sur une `IEnumerable` collection, vous obtenez l’implémentation du .NET Framework ; lorsque vous appelez sur un `IQueryable` de l’objet, vous obtenez l’implémentation du fournisseur de base de données.)


### <a name="add-a-search-box-to-the-student-index-view"></a>Ajouter une zone de recherche à la vue d’Index étudiant

Dans *Views\Student\Index.cshtml*, ajoutez le code en surbrillance immédiatement avant l’ouverture `table` balise afin de créer une légende, une zone de texte et un **recherche** bouton.

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=5-10)]

Exécutez la page, entrez une chaîne de recherche, puis cliquez sur **recherche** pour vérifier que le filtrage fonctionne.

![Students_Index_page_with_search_box](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Notez que l’URL ne contient pas la « une » chaîne de recherche, ce qui signifie que si vous créez un signet cette page, vous n’obtiendrez la liste filtrée lorsque vous utilisez le signet. Vous allez modifier le **recherche** bouton à utiliser des chaînes de requête pour les critères de filtre plus loin dans le didacticiel.

## <a name="add-paging-to-the-students-index-page"></a>Ajouter la pagination à la Page d’Index les étudiants

Pour ajouter la pagination à la page d’Index des étudiants, vous allez commencer en installant le **PagedList.Mvc** package NuGet. Ensuite, vous allez apporter des modifications supplémentaires dans le `Index` (méthode) et ajouter des liens de la pagination dans le `Index` vue. **PagedList.Mvc** est un des nombreux pagination correcte et le tri des packages pour ASP.NET MVC et son utilisation ici est destinée uniquement, par exemple, pas comme une recommandation pour elle sur les autres options. L’illustration suivante montre les liens de la pagination.

![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

### <a name="install-the-pagedlistmvc-nuget-package"></a>Installez le Package NuGet de PagedList.MVC

NuGet **PagedList.Mvc** package installe automatiquement le **PagedList** package en tant que dépendance. Le **PagedList** package installe un `PagedList` les méthodes de type et extension de collecte pour `IQueryable` et `IEnumerable` collections. Les méthodes d’extension créent une seule page de données dans un `PagedList` collection hors de votre `IQueryable` ou `IEnumerable`et le `PagedList` collection fournit plusieurs propriétés et méthodes qui facilitent la pagination. Le **PagedList.Mvc** package installe une application d’assistance de pagination qui affiche des boutons de pagination.

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque** , puis **gérer les Packages NuGet pour la Solution**.

Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur le **Online** onglet sur la gauche et « paginée » dans la zone de recherche. Lorsque vous voyez le **PagedList.Mvc** du package, cliquez sur **installer**.

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Dans le **sélectionner les projets** , cliquez sur **OK**.

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

### <a name="add-paging-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de pagination pour le Index (méthode)

Dans *Controllers\StudentController.cs*, ajoutez un `using` instruction pour le `PagedList` espace de noms :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Remplacez la méthode `Index` par le code suivant :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Ce code ajoute un `page` paramètre, un paramètre de commande de tri actuelle et un paramètre de filtre actuel pour la signature de méthode, comme illustré ici :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

La première fois que la page s’affiche, ou si l’utilisateur n’a pas cliqué sur un échange ou le lien de tri, tous les paramètres sont null. Si un lien de la pagination est activé, le `page` variable contient le numéro de page à afficher.

`A ViewBag`propriété fournit l’affichage de l’ordre de tri actuel, car il doit être inclus dans les liens de la pagination afin de conserver l’ordre de tri lors de l’échange :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Une autre propriété, `ViewBag.CurrentFilter`, fournit la vue avec la chaîne de filtre actuel. Cette valeur doit être incluse dans les liens de la pagination afin de conserver les paramètres de filtre lors de la pagination, et elle doit être restaurée à la zone de texte lorsque la page s’affiche de nouveau. Si la chaîne de recherche est modifiée au cours de la pagination, la page doit être réinitialisé à 1, parce que le nouveau filtre pour afficher des données différentes. La chaîne de recherche est modifiée quand une valeur est entrée dans la zone de texte et le bouton d’envoi est enfoncé. Dans ce cas, le `searchString` paramètre n’est pas null.

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

À la fin de la méthode, le `ToPagedList` méthode d’extension sur les étudiants `IQueryable` objet convertit la requête étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette page unique des étudiants est ensuite transmise à la vue :

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Le `ToPagedList` méthode prend un numéro de page. Les deux points d’interrogation représentent le [opérateur de fusion null](https://msdn.microsoft.com/library/ms173224.aspx). L’opérateur de fusion null définit une valeur par défaut pour un type nullable ; l’expression `(page ?? 1)` signifie retourne la valeur de `page` si elle a une valeur, ou retourne 1 si `page` a la valeur null.

### <a name="add-paging-links-to-the-student-index-view"></a>Ajouter des liens de pagination à la vue Index étudiant

Dans *Views\Student\Index.cshtml*, remplacez le code existant par le code suivant :

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=6,9,14-20,56-58)]

Le `@model` instruction en haut de la page spécifie que la vue obtient désormais une `PagedList` de l’objet au lieu d’un `List` objet.

Le `using` instruction pour `PagedList.Mvc` donne accès à l’application d’assistance MVC de boutons de pagination.

Le code utilise une surcharge de [BeginForm](https://msdn.microsoft.com/library/system.web.mvc.html.formextensions.beginform(v=vs.108).aspx) qui lui permet de spécifier [FormMethod.Get](https://msdn.microsoft.com/library/system.web.mvc.formmethod(v=vs.100).aspx/css).

[!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

La valeur par défaut [BeginForm](https://msdn.microsoft.com/library/system.web.mvc.html.formextensions.beginform(v=vs.108).aspx) envoie des données de formulaire avec une publication, ce qui signifie que les paramètres sont passés dans le corps du message HTTP et non dans l’URL sous forme de chaînes de requête. Lorsque vous spécifiez HTTP GET, les données du formulaire sont passées dans l’URL sous forme de chaînes de requête, ce qui permet aux utilisateurs de l’URL de signet. Le [recommandations du W3C pour l’utilisation de HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) spécifier que vous devez utiliser GET lorsque l’action n’entraîne pas une mise à jour.

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

![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Cliquez sur les liens de la pagination dans différents ordres de tri s’assurer que la pagination fonctionne. Entrez une chaîne de recherche, puis recommencez la pagination pour vérifier que la pagination également fonctionne correctement avec le tri et de filtrage.

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="create-an-about-page-that-shows-student-statistics"></a>Créer une sur la Page qui affiche les statistiques de l’étudiant

Pour Contoso Université du site Web sur la page, vous afficherez le nombre d’étudiants inscrits pour chaque date d’inscription. Cela nécessite de regroupement et simples des calculs sur les groupes. Pour ce faire, vous devez procédez comme suit :

- Créer une classe de modèle d’affichage pour les données dont vous avez besoin pour passer à la vue.
- Modifier la `About` méthode dans la `Home` contrôleur.
- Modifier la `About` vue.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créer un *ViewModel* dossier. Dans ce dossier, ajoutez un fichier de classe *EnrollmentDateGroup.cs* et remplacez le code existant par le code suivant :

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

![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

## <a name="optional-deploy-the-app-to-windows-azure"></a>Facultatif : Déployer l’application sur Windows Azure

Jusqu'à présent votre application a été exécuté localement dans IIS Express sur votre ordinateur de développement. Pour le rendre disponible pour d’autres personnes à utiliser sur Internet, vous devez déployer vers un fournisseur d’hébergement web. Dans cette section facultative du didacticiel vous allez déployer sur un Site Web de Windows Azure.

### <a name="using-code-first-migrations-to-deploy-the-database"></a>À l’aide de Migrations Code First pour déployer la base de données

Pour déployer la base de données, vous utiliserez des Migrations Code First. Lorsque vous créez le profil de publication que vous utilisez pour configurer les paramètres de déploiement à partir de Visual Studio, vous devez sélectionner une case à cocher qui étiqueté **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**. Ce paramètre oblige le processus de déploiement configurer automatiquement l’application *Web.config* des fichiers sur le serveur de destination afin qu’utilise le premier Code la `MigrateDatabaseToLatestVersion` classe de l’initialiseur.

Visual Studio ne fait rien avec la base de données pendant le processus de déploiement. Lorsque l’application déployée accède à la base de données pour la première fois après le déploiement, Code First crée automatiquement la base de données ou met à jour le schéma de base de données vers la dernière version. Si l’application implémente une Migrations `Seed` méthode, l’exécution de la méthode après la création de la base de données ou le schéma est mis à jour.

Vos Migrations `Seed` méthode insère les données de test. Si vous déployez dans un environnement de production, vous devrez modifier le `Seed` méthode afin qu’il insère uniquement les données que vous souhaitez insérer dans votre base de données de production. Par exemple, dans votre modèle de données en cours, vous souhaiterez ont cours réels, mais les étudiants fictifs dans la base de données de développement. Vous pouvez écrire un `Seed` méthode charger à la fois dans le développement, puis commentez les étudiants fictifs avant de déployer en production. Ou vous pouvez écrire un `Seed` méthode pour charger uniquement des cours et entrer manuellement des étudiants fictifs dans la base de données de test à l’aide de l’interface utilisateur de l’application.

### <a name="get-a-windows-azure-account"></a>Obtenir un compte Windows Azure

Vous aurez besoin d’un compte Windows Azure. Si vous n’en avez déjà, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [version d’évaluation gratuite de Windows Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).

### <a name="create-a-web-site-and-a-sql-database-in-windows-azure"></a>Créer un site web et une base de données SQL dans Windows Azure

Votre Site Web de Windows Azure s’exécute dans un environnement d’hébergement partagé, ce qui signifie qu’il s’exécute sur des machines virtuelles (VM) qui sont partagés avec d’autres clients de Windows Azure. Un environnement d’hébergement partagé est un moyen économique pour commencer dans le cloud. Ultérieurement, si le trafic web augmente, l’application peut évoluer pour répondre aux besoins en exécutant sur des machines virtuelles dédiées. Si vous avez besoin d’une architecture plus complexe, vous pouvez migrer vers un Service de Cloud Windows Azure. Services de cloud s’exécutent sur des machines virtuelles dédiés que vous pouvez configurer selon vos besoins.

Base de données SQL Windows Azure est un service de base de données relationnelle informatique qui repose sur les technologies SQL Server. Outils et applications qui fonctionnent avec SQL Server fonctionnent également avec la base de données SQL.

1. Dans le [portail de gestion Windows Azure](https://manage.windowsazure.com/), cliquez sur **Sites Web** dans l’onglet gauche, puis cliquez sur **nouveau**.

    ![Nouveau bouton dans le portail de gestion](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image11.png)
2. Cliquez sur **création personnalisée**.

    ![Créer avec un lien de base de données dans le portail de gestion](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image12.png)

 Le **nouveau Site Web - création personnalisée** Assistant s’ouvre.
3. Dans le **nouveau Site Web** étape de l’Assistant, entrez une chaîne dans le **URL** zone à utiliser comme URL unique pour votre application. L’URL complète consiste à ce que vous entrez ici ainsi que le suffixe que vous voyez en regard de la zone de texte. L’illustration montre la « ConU », mais cette URL est probablement prise afin de vous devrez choisir un autre.

    ![Créer avec un lien de base de données dans le portail de gestion](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)
4. Dans le **région** liste déroulante, sélectionnez une région proche de vous. Ce paramètre spécifie votre site web s’exécutera dans quel centre de données.
5. Dans le **base de données** liste déroulante, sélectionnez **créer une base de données SQL 20 Mo gratuite**.

    ![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image14.png)
6. Dans le **nom de chaîne de connexion de base de données**, entrez *SchoolContext*.

    ![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)
7. Cliquez sur la flèche pointant vers la droite en bas de la zone. L’Assistant passe à le **les paramètres de base de données** étape.
8. Dans le **nom** , entrez *ContosoUniversityDB*.
9. Dans le **Server** boîte, sélectionnez **serveur de base de données SQL**. Vous pouvez également, si vous avez créé précédemment d’un serveur, vous pouvez sélectionner ce serveur dans la liste déroulante.
10. Entrez un administrateur **nom de connexion** et **mot de passe**. Si vous avez sélectionné **serveur de base de données SQL** vous n’êtes pas entrer un nom existant et un mot de passe ici, vous entrez un nouveau nom et mot de passe que vous définissez maintenant pour une utilisation ultérieure lorsque vous accédez à la base de données. Si vous avez sélectionné un serveur que vous avez créé précédemment, vous devez entrer les informations d’identification pour ce serveur. Pour ce didacticiel, vous ne sélectionnez le ***avancé*** case à cocher. Le ***avancé*** options permettent de définir la base de données [classement](https://msdn.microsoft.com/library/aa174903(v=SQL.80).aspx).
11. Choisissez le même **région** que vous avez choisi pour le site web.
12. Cliquez sur la coche en bas à droite de la case pour indiquer que vous avez terminé.   
  
    ![Étape des paramètres de base de données du nouveau Site Web de-créer avec l’Assistant de base de données](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image16.png)  

 L’illustration suivante montre à l’aide d’une connexion et le serveur SQL Server existant.   
  
    ![Étape des paramètres de base de données du nouveau Site Web de-créer avec l’Assistant de base de données](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image17.png)  
  
 Le portail de gestion renvoie à la page de Sites Web et le **état** colonne indique que le site est créé. Après un certain temps (en général moins d’une minute), la **état** colonne indique que le site a été créé. Dans la barre de navigation à gauche, le nombre de sites que vous avez dans votre compte s’affiche en regard du **Sites Web** icône et le nombre de bases de données s’affiche en regard du **bases de données SQL** icône.

## <a name="deploy-the-application-to-windows-azure"></a>Déployer l’application sur Windows Azure

1. Dans Visual Studio, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.  
  
    ![Publier dans le menu contextuel du projet](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image18.png)
2. Dans le **profil** onglet de la **publier le site Web** Assistant, cliquez sur **importation**.  
  
    ![Importer les paramètres de publication](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image19.png)
3. Si vous n’avez pas précédemment ajouté votre abonnement Windows Azure dans Visual Studio, procédez comme suit. Dans ces étapes, vous ajoutez votre abonnement afin que la liste déroulante répertorie sous **importer à partir d’un site web Windows Azure** inclura votre site web.

    a. Dans le **importer un profil de publication** boîte de dialogue, cliquez sur **importer à partir d’un site web Windows Azure**, puis cliquez sur **abonnement ajouter Windows Azure**.

    ![ajouter l’abonnement Windows Azure](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image20.png)

    b. Dans le **importer les abonnements Windows Azure** boîte de dialogue, cliquez sur **fichier d’abonnement téléchargement**.

    ![Télécharger le fichier d’abonnement](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image21.png)

    c. Dans la fenêtre du navigateur, enregistrez le *.publishsettings* fichier.

    ![Téléchargez le fichier .publishsettings](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image22.png)

    > [!WARNING]
    > Sécurité - le *publishsettings* fichier contient vos informations d’identification (non codées) utilisées pour gérer vos abonnements Windows Azure et les services. La meilleure pratique de sécurité pour ce fichier consiste à stocker temporairement en dehors de vos répertoires sources (par exemple, dans le *bibliothèques\documents* dossier), puis le supprimez une fois l’importation terminée. Un utilisateur malveillant qui accède à la `.publishsettings` fichier peut modifier, créer et supprimer vos services Windows Azure.

    d. Dans le **importer les abonnements Windows Azure** boîte de dialogue, cliquez sur **Parcourir** et accédez à la *.publishsettings* fichier.

    ![sub de téléchargement](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image23.png)

    e. Cliquez sur **Importer**.

    ![d'importation](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image24.png)
4. Dans le **importer un profil de publication** boîte de dialogue, sélectionnez **importer à partir d’un site web Windows Azure**, sélectionnez votre site web dans la liste déroulante, puis cliquez sur **OK**.  
  
    ![Importation de profil de publication](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image25.png)
5. Dans le **connexion** , cliquez sur **valider la connexion** pour vous assurer que les paramètres sont corrects.  
  
    ![Valider la connexion](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)
6. Lorsque la connexion a été validée, une coche verte s’affiche en regard du **valider la connexion** bouton. Cliquez sur **Suivant**.  
  
    ![Connexion validée avec succès](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image27.png)
7. Ouvrez le **chaîne de connexion à distance** liste déroulante sous **SchoolContext** et sélectionnez la chaîne de connexion pour la base de données que vous avez créé.
8. Sélectionnez **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**.
9. Décochez la case **utiliser cette chaîne de connexion lors de l’exécution** pour le **UserContext (DefaultConnection)**, étant donné que cette application n’utilise pas la base de données d’appartenance.   
  
    ![Onglet Paramètres](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image28.png)
10. Cliquez sur **Suivant**.
11. Dans le **aperçu** , cliquez sur **démarrer la version préliminaire**.  
  
    ![Bouton du démarrage dans l’onglet d’aperçu](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image29.png)  
  
 L’onglet affiche une liste des fichiers qui seront copiés vers le serveur. Affichage de l’aperçu n’est pas requise pour publier l’application, mais elle est une fonction utile de connaître. Dans ce cas, vous n’avez pas besoin de faire quelque chose avec la liste des fichiers qui s’affiche. La prochaine fois que vous déployez cette application, seuls les fichiers qui ont été modifiés seront dans cette liste.  
  
    ![Sortie de fichier du démarrage](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image30.png)
12. Cliquez sur **Publier**.  
 Visual Studio lance le processus de copie des fichiers sur le serveur Windows Azure.
13. Le **sortie** fenêtre affiche les actions de déploiement ont été effectuées et signale la réussite du déploiement.  
  
    ![Fenêtre Sortie reporting réussir le déploiement](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image31.png)
14. Lors du déploiement réussi, le navigateur par défaut s’ouvre automatiquement à l’URL du site web déployé.  
 L’application que vous avez créé est en cours d’exécution dans le cloud. Cliquez sur l’onglet d’étudiants.  
  
    ![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image32.png)

À ce stade votre *SchoolContext* base de données a été créé dans la base de données SQL Windows Azure, car vous avez sélectionné **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**. Le *Web.config* dans le site web déployé a été modifié afin que le [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) initialiseur exécuteriez la première fois que votre code lit ou écrit des données dans la base de données (qui s’est-il passé lorsque vous avez sélectionné le **étudiants** onglet) :

![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image33.png)

Le processus de déploiement créé également une nouvelle chaîne de connexion *(SchoolContext\_DatabasePublish*) pour les Migrations Code First à utiliser pour la mise à jour le schéma de base de données et d’amorçage de la base de données.

![Chaîne de connexion Database_Publish](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image34.png)

Le *DefaultConnection* chaîne de connexion est la base de données d’appartenance (que nous n’utilisons pas dans ce didacticiel). Le *SchoolContext* chaîne de connexion est la base de données ContosoUniversity.

Vous pouvez rechercher la version déployée du fichier Web.config sur votre ordinateur dans *ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*. Vous pouvez accéder à la version déployée *Web.config* fichier lui-même à l’aide de FTP. Pour obtenir des instructions, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement d’une mise à jour du Code](../../../../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md). Suivez les instructions qui commencent par « pour utiliser un outil FTP, vous avez besoin de trois opérations : l’URL du serveur FTP, le nom d’utilisateur et le mot de passe. »

> [!NOTE]
> L’application web n’implémente pas la sécurité, toute personne qui recherche l’URL permettant de changer les données. Pour obtenir des instructions sur la façon de sécuriser le site web, consultez [déployer une application sécurisée ASP.NET MVC avec l’appartenance, OAuth et base de données SQL à un Site Web de Windows Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Vous pouvez empêcher d’autres personnes d’utiliser le site à l’aide du portail de gestion Windows Azure ou **l’Explorateur de serveurs** dans Visual Studio pour arrêter le site.


![](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image35.png)

## <a name="code-first-initializers"></a>Initialiseurs de premier code

Dans la section déploiement, vous avez vu le [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) initialiseur utilisé. Code d’abord fournit également des autres initialiseurs que vous pouvez utiliser, y compris [CreateDatabaseIfNotExists](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (la valeur par défaut), [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) et [ DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx). Le `DropCreateAlways` initialiseur peut être utile pour configurer des conditions pour les tests unitaires. Vous pouvez également écrire votre propre initialiseurs, et vous pouvez appeler explicitement un initialiseur si vous ne souhaitez pas attendre que l’application lit ou écrit dans la base de données. Pour obtenir une explication complète des initialiseurs, consultez le chapitre 6 du livre [programmation Entity Framework : Code First](http://shop.oreilly.com/product/0636920022220.do) par Julie Lerman et Rowan Miller.

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez vu comment créer un modèle de données et mettre en œuvre CRUD de base, le tri, le filtrage, la pagination et la fonctionnalité de regroupement. Dans le didacticiel suivant vous commencerez examinant rubriques plus avancées en développant le modèle de données.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)
[Suivant](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
