---
title: Pages Razor avec EF Core - CRUD - 2 de 8
author: rick-anderson
description: "Montre comment créer, lire, mettre à jour, supprimer avec EF de base"
ms.author: riande
manager: wpickett
ms.date: 10/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-rp/crud
ms.openlocfilehash: d9b34c141401fbeaafe439fae1a7a75f2fe7b4ae
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="create-read-update-and-delete---ef-core-with-razor-pages-2-of-8"></a>Créer, lire, mettre à jour et supprimer - Core EF avec les Pages Razor (2 de 8)

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dans ce didacticiel, le modèle généré automatiquement CRUD (créer, lire, mettre à jour, supprimer) code est révisé et personnalisé.

Remarque : Pour réduire la complexité et de conserver ces didacticiels axés sur EF Core, code EF principal est utilisé dans les fichiers de code-behind des Pages Razor. Certains développeurs utilisent un modèle de couche ou le référentiel de service dans pour créer une couche d’abstraction entre l’interface utilisateur (Pages Razor) et la couche d’accès aux données.

Dans ce didacticiel, créer, modifier, supprimer et Pages Razor de détails dans le *étudiant* dossier sont modifiées.

Le modèle généré automatiquement du code utilise le modèle suivant pour créer, modifier et supprimer des pages :

* Obtenir et afficher les données demandées avec la méthode HTTP GET `OnGetAsync`.
* Enregistrez les modifications dans les données avec la méthode HTTP POST `OnPostAsync`.

Les pages d’Index et les détails de l’obtenir et affichent les données demandées avec la méthode HTTP GET`OnGetAsync`

## <a name="replace-singleordefaultasync-with-firstordefaultasync"></a>Remplacez SingleOrDefaultAsync FirstOrDefaultAsync

Le code généré utilise [SingleOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) pour extraire l’entité demandée. [FirstOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) est plus efficace pour l’extraction d’une entité :

* À moins que le code doit vérifier qui n’est pas plusieurs entités retournées par la requête. 
* `SingleOrDefaultAsync`extrait des données et fonctionne inutiles.
* `SingleOrDefaultAsync`lève une exception s’il existe plusieurs entités qui correspond à la partie du filtre.
*  `FirstOrDefaultAsync`ne lève pas s’il existe plusieurs entités qui correspond à la partie du filtre.

Remplacer `SingleOrDefaultAsync` avec `FirstOrDefaultAsync`. `SingleOrDefaultAsync`est utilisé dans des 5 emplacements :

* `OnGetAsync`dans la page de détails.
* `OnGetAsync`et `OnPostAsync` dans les pages modifier et supprimer.

<a name="FindAsync"></a>
### <a name="findasync"></a>FindAsync

Dans une grande partie du code de modèle généré automatiquement, [FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) peut être utilisé à la place de `FirstOrDefaultAsync` ou `SingleOrDefaultAsync`. 

`FindAsync`:

* Recherche une entité avec la clé primaire (PK). Si une entité avec la clé est suivie par le contexte, il est retourné sans qu’une demande à la base de données.
* Est simple et concis.
* Est optimisé pour rechercher une entité unique.
* Pouvez présentent des avantages de performances dans certaines situations, mais ils rarement intervient pour les scénarios web normales.
* Utilise implicitement [FirstAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) au lieu de [SingleAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).
Toutefois, si vous souhaitez inclure d’autres entités, rechercher n’est plus approprié. Cela signifie que vous devrez peut-être rechercher d’abandon et de passer à une requête fur et à votre application.

## <a name="customize-the-details-page"></a>Personnaliser la page de détails

Accédez à `Pages/Students` page. Le **modifier**, **détails**, et **supprimer** liens sont générés par le [application d’assistance de balise d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le *Pages/étudiants / Index.cshtml* fichier.

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Index1.cshtml?range=40-44)]

Sélectionnez un lien de détails. L’URL est au format `http://localhost:5000/Students/Details?id=2`. L’ID est passé à l’aide d’une chaîne de requête (`?id=2`).

Mettre à jour de l’édition, détails et supprimer des Pages Razor à utiliser le `"{id:int}"` modèle d’itinéraire. Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`.

À la page avec le modèle d’itinéraire « {id : int} » qui effectue une demande de **pas** incluent une erreur entier itinéraire valeur renvoie HTTP 404 (introuvable). Par exemple, `http://localhost:5000/Students/Details` renvoie une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Exécuter l’application, cliquez sur un lien de détails et vérifier l’URL est en passant l’ID en tant que données d’itinéraire (`http://localhost:5000/Students/Details/2`).

Ne modifiez pas globalement `@page` à `@page "{id:int}"`, cela a pour les liens vers la page d’accueil et de créer des pages.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Ajouter des données associées

Le code de modèle généré automatiquement pour la page d’Index des étudiants n’inclut pas le `Enrollments` propriété. Dans cette section, le contenu de la `Enrollments` collection s’affiche dans la page de détails.

Le `OnGetAsync` méthode *Pages/Students/Details.cshtml.cs* utilise le `FirstOrDefaultAsync` méthode pour récupérer un seul `Student` entité. Ajoutez le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Le `Include` et `ThenInclude` méthodes provoquent le contexte de chargement le `Student.Enrollments` propriété de navigation et dans chaque inscription le `Enrollment.Course` propriété de navigation. Ces méthodes sont examinied en détail dans le didacticiel de la lecture des données.

Le `AsNoTracking` méthode améliore les performances dans les scénarios lorsque les entités retournées ne sont pas mis à jour dans le contexte actuel. `AsNoTracking`le sujet est abordé plus loin dans ce didacticiel.

### <a name="display-related-enrollments-on-the-details-page"></a>Afficher les inscriptions connexes sur la page de détails

Ouvrez *Pages/Students/Details.cshtml*. Ajoutez le code en surbrillance suivant pour afficher la liste des inscriptions :

 <!--2do ricka. if doesn't change, remove dup -->
[!code-cshtml[Main](intro/samples/cu/Pages/Students/Details1.cshtml?highlight=32-53)]

Si la mise en retrait du code est incorrect, une fois que le code est collé, appuyez sur CTRL + K + D pour résoudre ce problème.

Le code précédent effectue une itération sur les entités dans le `Enrollments` propriété de navigation. Pour chaque inscription, il affiche le titre du cours et la qualité. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans le `Course` propriété de navigation de l’entité d’inscriptions.

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur le **détails** lien pour un étudiant. La liste des cours et les notes de l’étudiant sélectionné s’affiche.

## <a name="update-the-create-page"></a>Mise à jour de la page de création

Mise à jour la `OnPostAsync` méthode dans *Pages/Students/Create.cshtml.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>
### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Examinez le [TryUpdateModelAsync](https://docs.microsoft.com/ dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

Dans le code précédent, `TryUpdateModelAsync<Student>` tente de mettre à jour le `emptyStudent` en utilisant les valeurs de formulaire publiées à partir de l’objet le [PageContext](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) propriété dans le [PageModel](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel?view=aspnetcore-2.0). `TryUpdateModelAsync`met à jour uniquement les propriétés répertoriées (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

Dans l’exemple précédent :

* Le deuxième argument (` "student", // Prefix`) est le préfixe utilise pour rechercher des valeurs. Il n’est pas respecter la casse.
* Les valeurs de formulaire publiées sont convertis en types dans les `Student` de modèle à l’aide de [liaison de modèle](xref:mvc/models/model-binding#how-model-binding-works).

<a id="overpost"></a>
### <a name="overposting"></a>Sur-validation

À l’aide de `TryUpdateModel` pour mettre à jour les champs avec les valeurs publiées est une meilleure pratique de sécurité, car elle empêche overposting. Par exemple, supposons que l’entité Student inclut un `Secret` propriété cette page web ne doivent pas mettre à jour ou ajouter :

[!code-csharp[Main](intro/samples/cu/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Même si l’application n’a pas un `Secret` champ sur la création/mise à jour Page Razor, un pirate peut définir le `Secret` la valeur par sur-validation. Un pirate peut utiliser un outil tel que Fiddler ou écrire du JavaScript, pour valider un `Secret` former la valeur. Le code d’origine ne limite pas les champs que le classeur de modèles utilise lorsqu’il crée une instance de l’étudiant.

Valeur qui le pirate spécifié pour le `Secret` champ de formulaire est mis à jour dans la base de données. L’illustration suivante montre l’ajout de l’outil de Fiddler le `Secret` champ (avec la valeur « OverPost ») pour les valeurs de formulaire publiées.

![Champ de clé secrète d’ajout de Fiddler](../ef-mvc/crud/_static/fiddler.png)

La valeur « OverPost » est ajouté avec succès à la `Secret` la propriété de la ligne insérée. Le Concepteur d’application destiné jamais le `Secret` propriété soit définie avec la page de création.

<a name="vm"></a>
### <a name="view-model"></a>Modèle d’affichage

Un modèle d’affichage contient généralement un sous-ensemble des propriétés incluses dans le modèle utilisé par l’application. Le modèle d’application est souvent appelé le modèle de domaine. En règle générale, le modèle de domaine contient toutes les propriétés requises par l’entité correspondante dans la base de données. Le modèle d’affichage contient uniquement les propriétés requises pour la couche d’interface utilisateur (par exemple, la page Créer). Outre le modèle de vue, certaines applications utilisent un modèle de liaison ou d’entrée pour passer des données entre la classe code-behind Pages Razor et le navigateur. Considérez les éléments suivants `Student` modèle d’affichage :

[!code-csharp[Main](intro/samples/cu/Models/StudentVM.cs)]

Afficher les modèles fournissent un autre moyen d’empêcher overposting. Le modèle d’affichage contient uniquement les propriétés pour afficher (affichage) ou de mettre à jour.

Le code suivant utilise la `StudentVM` afficher le modèle pour créer un nouvel étudiant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Le [SetValues](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) méthode définit les valeurs de cet objet en lisant les valeurs d’une autre [PropertyValues](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) objet. `SetValues`utilise la correspondance de nom de propriété. Le type de modèle de vue n’a pas besoin d’être liées pour le type de modèle, il a juste besoin d’avoir des propriétés qui correspondent.

À l’aide de `StudentVM` requiert [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Students/CreateVM.cshtml) mis à jour pour utiliser `StudentVM` plutôt que `Student`.

Dans les Pages Razor, la `PageModel` classe dérivée est le modèle d’affichage. 

## <a name="update-the-edit-page"></a>Mise à jour de la page de modification

Mettre à jour le fichier code-behind de page Modifier :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Les modifications de code sont similaires à la page Créer, à quelques exceptions près :

* `OnPostAsync`option `id` paramètre.
* Les étudiants actuelle sont extraite de la base de données, au lieu de créer un étudiant vide.
* `FirstOrDefaultAsync`a été remplacé par [FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync?view=efcore-2.0). `FindAsync`est un bon choix lors de la sélection d’une entité à partir de la clé primaire. Consultez [FindAsync](#FindAsync) pour plus d’informations.

### <a name="test-the-edit-and-create-pages"></a>La modification de test et créer des pages

Créer et modifier plusieurs entités de student.

## <a name="entity-states"></a>États de l’entité

Le contexte de base de données effectue le suivi de si les entités en mémoire sont synchronisées avec les lignes correspondantes dans la base de données. Les informations de synchronisation de contexte de base de données déterminent que se passe-t-il lorsque `SaveChanges` est appelée. Par exemple, lorsqu’une nouvelle entité est passée à la `Add` (méthode), que l’état de l’entité est défini sur `Added`. Lorsque `SaveChanges` est appelée, la base de données contexte émet une commande SQL INSERT.

Une entité peut être l’un des états suivants :

* `Added`: L’entité n’existe pas encore dans la base de données. Le `SaveChanges` méthode émet une instruction INSERT.

* `Unchanged`: Aucune modification ne doivent être enregistrés avec cette entité. Une entité est dans cet état lorsqu’il est lu à partir de la base de données.

* `Modified`: Tout ou partie des valeurs de propriété de l’entité ont été modifiés. Le `SaveChanges` méthode émet une instruction de mise à jour.

* `Deleted`: L’entité a été marquée pour suppression. Le `SaveChanges` méthode émet une instruction DELETE.

* `Detached`: L’entité n’est pas suivie par le contexte de base de données.

Dans une application de bureau, les modifications d’état sont généralement définies automatiquement. Une entité est en lecture, de modifications sont apportées et l’état d’entité qui sera automatiquement remplacée par `Modified`. Appel de `SaveChanges` génère une instruction SQL UPDATE qui met à jour uniquement les propriétés modifiées.

Dans une application web, le `DbContext` qui lit une entité et affiche les données sont supprimées après le rendu d’une page. Lorsque des pages `OnPostAsync` est appelée, une nouvelle demande de web et avec une nouvelle instance de la `DbContext`. Relecture de l’entité dans ce nouveau contexte simule le traitement de bureau.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Dans cette section, le code est ajouté pour implémenter une erreur personnalisée message lorsque l’appel à `SaveChanges` échoue. Ajouter une chaîne pour contenir des messages d’erreur possibles :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Remplacez la méthode `OnGetAsync` par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Le code précédent contient le paramètre facultatif `saveChangesError`. `saveChangesError`Indique si la méthode a été appelée après un échec de suppression de l’objet de l’étudiant. L’opération de suppression peut échouer en raison des problèmes réseau temporaires. Erreurs réseau transitoires sont probablement dans le cloud. `saveChangesError`a la valeur false lorsque la page de suppression `OnGetAsync` est appelée à partir de l’interface utilisateur. Lorsque `OnGetAsync` est appelée par `OnPostAsync` (parce que l’opération de suppression a échoué), le `saveChangesError` paramètre a la valeur true.

### <a name="the-delete-pages-onpostasync-method"></a>La méthode OnPostAsync de pages Delete

Remplacez le `OnPostAsync` par le code suivant :

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Le code précédent récupère l’entité sélectionnée, puis appelle la `Remove` pour définir l’état de l’entité `Deleted`. Lorsque `SaveChanges` est appelée, une suppression SQL commande est générée. Si `Remove` échoue :

* L’exception de la base de données est interceptée.
* Supprimer des pages `OnGetAsync` méthode est appelée avec `saveChangesError=true`.

### <a name="update-the-delete-razor-page"></a>Mise à jour de la Page de Razor Delete

Ajouter le message d’erreur mis en surbrillance suivant à la Page supprimer de Razor.

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Suppression de test.

## <a name="common-errors"></a>Erreurs courantes

Étudiant à domicile ou autres liens ne fonctionnent pas :

Vérifiez la Page Razor contient le bon `@page` directive. Par exemple, la Page de Razor étudiant/Home doit **pas** contiennent un modèle d’itinéraire :

```cshtml
@page "{id:int}"
```

Chaque Page Razor doit inclure le `@page` la directive.

>[!div class="step-by-step"]
[Précédent](xref:data/ef-rp/intro)
[Suivant](xref:data/ef-rp/sort-filter-page)
