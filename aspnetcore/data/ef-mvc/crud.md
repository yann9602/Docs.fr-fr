---
title: "Cœur de ASP.NET MVC avec EF Core - CRUD - 2 de 10"
author: tdykstra
description: 
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/crud
ms.openlocfilehash: 7e495ba56958012713836c1dd75ac0c5a8bff942
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-read-update-and-delete---ef-core-with-aspnet-core-mvc-tutorial-2-of-10"></a>Créer, lire, mettre à jour et supprimer - Core EF avec le didacticiel d’ASP.NET MVC de base (2 sur 10)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous avez créé une application MVC qui stocke et affiche les données à l’aide d’Entity Framework et SQL Server LocalDB. Dans ce didacticiel, vous allez examiner et personnaliser le CRUD (créer, lire, mettre à jour, supprimer) le code que la génération de modèles automatique MVC crée automatiquement pour vous dans les contrôleurs et vues.

> [!NOTE] 
> Il est courant de mettre en œuvre le modèle de référentiel afin de créer une couche d’abstraction entre votre contrôleur et de la couche d’accès aux données. Pour conserver ces didacticiels simple et consiste à apprendre à utiliser Entity Framework proprement dit, ils n’utilisent pas les référentiels. Pour plus d’informations sur les référentiels avec EF, consultez [le dernier didacticiel de cette série](advanced.md).

Dans ce didacticiel, vous devez utiliser les pages web suivantes :

![Page de détails de l’étudiant](crud/_static/student-details.png)

![Page de création d’étudiant](crud/_static/student-create.png)

![Page Modifier l’étudiant](crud/_static/student-edit.png)

![Supprimer la page étudiant](crud/_static/student-delete.png)

## <a name="customize-the-details-page"></a>Personnaliser la page de détails

Le code de modèle généré automatiquement pour la page d’Index des étudiants omis le `Enrollments` propriété, car cette propriété contienne une collection. Dans le **détails** page, vous devez afficher le contenu de la collection dans un tableau HTML.

Dans *Controllers/StudentsController.cs*, la méthode d’action pour les détails de l’afficher utilise le `SingleOrDefaultAsync` méthode pour récupérer un seul `Student` entité. Ajoutez le code qui appelle `Include`. `ThenInclude`, et `AsNoTracking` méthodes, comme indiqué dans le code en surbrillance suivant.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Le `Include` et `ThenInclude` méthodes provoquent le contexte de chargement le `Student.Enrollments` propriété de navigation et dans chaque inscription le `Enrollment.Course` propriété de navigation.  Vous en apprendrez davantage sur ces méthodes dans les [la lecture des données connexes](read-related-data.md) didacticiel.

Le `AsNoTracking` méthode améliore les performances dans les scénarios où les entités retournées ne seront pas modifiées dans la durée de vie du contexte actuel. Vous allez en savoir plus sur `AsNoTracking` à la fin de ce didacticiel.

### <a name="route-data"></a>Données d’itinéraire

La valeur de clé qui est transmise à la `Details` provient de la méthode *de router les données*. Données d’itinéraire sont données le classeur de modèles disponibles dans un segment de l’URL. Par exemple, l’itinéraire par défaut Spécifie les segments de contrôleur, action et id :

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

Dans l’URL suivante, l’itinéraire par défaut mappe le formateur en tant que le contrôleur, l’Index en tant que l’action et 1 en tant que l’id ; Il s’agit des valeurs de données d’itinéraire.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

La dernière partie de l’URL (« ? courseID = 2021 ») est une valeur de chaîne de requête. Le classeur de modèles sera également passer la valeur d’ID pour le `Details` méthode `id` paramètre si vous le passez en tant que valeur de chaîne de requête :

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Dans la page d’Index, les URL de liens hypertexte sont créés par les instructions d’assistance de balise dans la vue Razor. Dans le code Razor suivant, le `id` paramètre correspond à l’itinéraire par défaut, par conséquent, `id` est ajouté pour les données d’itinéraire.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Cette opération génère le code HTML suivant lorsque `item.ID` est 6 :

```html
<a href="/Students/Edit/6">Edit</a>
```

Dans le code Razor suivant, `studentID` ne correspond pas à un paramètre dans l’itinéraire par défaut, donc elle est ajoutée en tant qu’une chaîne de requête.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Cette opération génère le code HTML suivant lorsque `item.ID` est 6 :

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Pour plus d’informations sur les programmes d’assistance de balise, consultez [programmes d’assistance de balise dans ASP.NET Core](xref:mvc/views/tag-helpers/intro).

### <a name="add-enrollments-to-the-details-view"></a>Ajouter des inscriptions à l’affichage des détails

Ouvrez *Views/Students/Details.cshtml*. Chaque champ est affiché à l’aide de `DisplayNameFor` et `DisplayFor` applications auxiliaires, comme indiqué dans l’exemple suivant :

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Après le dernier champ et immédiatement avant la fermeture `</dl>` , ajoutez le code suivant pour afficher la liste des inscriptions :

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Si la mise en retrait du code est incorrect, une fois que vous collez le code, appuyez sur CTRL + K + D pour résoudre ce problème.

Ce code effectue une itération sur les entités dans le `Enrollments` propriété de navigation. Pour chaque inscription, il affiche le titre du cours et la qualité. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans le `Course` propriété de navigation de l’entité d’inscriptions.

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur le **détails** lien pour un étudiant. Vous voyez la liste des cours et les notes pour l’étudiant sélectionné :

![Page de détails de l’étudiant](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Mise à jour de la page de création

Dans *StudentsController.cs*, modifier le HttpPost `Create` méthode en ajoutant un bloc try-catch et en supprimant des ID à partir de la `Bind` attribut.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Ce code ajoute l’entité Student créée par le classeur de modèles ASP.NET MVC à l’entité étudiants défini et puis enregistre les modifications dans la base de données. (Classeur de modèles fait référence à la fonctionnalité d’ASP.NET MVC qui rend plus facile de travailler avec les données envoyées par un formulaire, un classeur de modèles convertit les valeurs de formulaire publiées à des types CLR et les transmet à la méthode d’action dans les paramètres. Dans ce cas, le classeur de modèles instancie une entité de l’étudiant pour vous à l’aide des valeurs de propriété de la collection de formulaires.)

Vous supprimé `ID` à partir de la `Bind` d’attribut, car l’ID est la valeur de clé primaire SQL Server définira automatiquement lorsque la ligne est insérée. Entrée de l’utilisateur ne définit pas la valeur d’ID.

Autre que le `Bind` attribut, le bloc try-catch est la seule modification que vous avez apportées au code de modèle généré automatiquement. Si une exception qui dérive de `DbUpdateException` est interceptée pendant l’enregistrement des modifications, un message d’erreur générique s’affiche. `DbUpdateException`exceptions sont parfois due à un élément externe à l’application plutôt qu’une erreur de programmation, il est conseillé pour réessayer. Bien que non implémenté dans cet exemple, une application de qualité production se connectera l’exception. Pour plus d’informations, consultez la **journal pour obtenir un aperçu** section [surveillance et télémétrie (construction réelle les applications Cloud avec Azure)](https://docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

Le `ValidateAntiForgeryToken` attribut aide à empêcher les attaques par falsification (CSRF) de demande entre sites. Le jeton est automatiquement injecté dans la vue par le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) et est inclus lorsque le formulaire est envoyé par l’utilisateur. Le jeton est validé par le `ValidateAntiForgeryToken` attribut. Pour plus d’informations sur CSRF, consultez [falsification de requête anti](../../security/anti-request-forgery.md).

<a id="overpost"></a>
### <a name="security-note-about-overposting"></a>Note de sécurité sur overposting

Le `Bind` attribut incluant le code de modèle généré automatiquement sur le `Create` méthode consiste à protéger contre overposting de créer des scénarios. Par exemple, supposons que l’entité Student inclut un `Secret` propriété que vous ne souhaitez pas cette page web à définir.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Même si vous n’avez pas un `Secret` champ sur la page web, un pirate peut utiliser un outil tel que Fiddler ou écrire du JavaScript, pour valider un `Secret` former la valeur. Sans le `Bind` attribut limitant les champs que le classeur de modèles utilise lorsqu’il crée une instance d’étudiant, le classeur de modèles choisit qui `Secret` former la valeur et l’utiliser pour créer l’instance d’entité étudiant. Ensuite, quel que soit le pirate spécifié pour la valeur du `Secret` champ de formulaire est mis à jour de la base de données. L’illustration suivante montre l’ajout de l’outil de Fiddler le `Secret` champ (avec la valeur « OverPost ») pour les valeurs de formulaire publiées.

![Champ de clé secrète d’ajout de Fiddler](crud/_static/fiddler.png)

La valeur « OverPost » puis est correctement ajoutée à la `Secret` propriété le texte inséré de ligne, même si vous n’aviez pas envisagés que la page web est en mesure de définir cette propriété.

Vous pouvez empêcher overposting dans les scénarios de modification par la lecture de l’entité à partir de la base de données tout d’abord, puis en appelant `TryUpdateModel`, en passant une liste de propriétés autorisés explicites. Qui est la méthode utilisée dans ces didacticiels.

Une autre façon d’empêcher overposting qui est utilisé par de nombreux développeurs consiste à utiliser les afficher les modèles plutôt que des classes d’entité avec la liaison de modèle. Incluez uniquement les propriétés que vous souhaitez mettre à jour dans le modèle d’affichage. Une fois le classeur de modèles MVC terminée, copiez les propriétés de modèle d’affichage pour l’instance d’entité, si vous le souhaitez à l’aide d’un outil tel que AutoMapper. Utilisez `_context.Entry` sur l’instance d’entité pour définir son état sur `Unchanged`, puis définissez `Property("PropertyName").IsModified` sur true sur chaque propriété d’entité qui est incluse dans le modèle d’affichage. Cette méthode est effective à la fois dans modifier et créer des scénarios.

### <a name="test-the-create-page"></a>La page de création de test

Le code dans *Views/Students/Create.cshtml* utilise `label`, `input`, et `span` (pour les messages de validation) programmes d’assistance pour chaque champ de balise.

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur **créer un nouveau**.

Entrez les noms et une date. Essayez d’entrer une date non valide si votre navigateur vous permet de le faire. (Certains navigateurs qui vous oblige à utiliser un sélecteur de dates.) Puis cliquez sur **créer** pour afficher le message d’erreur.

![Erreur de validation de date](crud/_static/date-error.png)

Il s’agit de la validation côté serveur que vous obtenez par défaut ; dans un prochain didacticiel, vous verrez comment ajouter des attributs qui génèrent également du code pour la validation côté client. Le code en surbrillance suivant illustre la vérification de validation de modèle dans le `Create` (méthode).

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Modifier la date à une valeur valide, puis cliquez sur **créer** pour afficher le nouvel étudiant s’affichent dans le **Index** page.

## <a name="update-the-edit-page"></a>Mise à jour de la page de modification

Dans *StudentController.cs*, le HttpGet `Edit` (méthode) (l’autre sans le `HttpPost` attribut) utilise le `SingleOrDefaultAsync` méthode pour récupérer l’entité sélectionnée de l’étudiant, comme vous l’avez vu dans la `Details` (méthode). Vous n’avez pas besoin de modifier cette méthode.

### <a name="recommended-httppost-edit-code-read-and-update"></a>HttpPost Modifier code recommandé : lecture et mise à jour

Remplacez la méthode d’action HttpPost modifier avec le code suivant.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Ces modifications implémentent une meilleure pratique de sécurité pour empêcher overposting. Le scaffolder généré un `Bind` d’attribut et ajouté l’entité créée par le classeur de modèles à l’entité avec une `Modified` indicateur. Que code n’est pas recommandé pour de nombreux scénarios, car le `Bind` attribut efface toutes les données existantes dans les champs non répertoriés dans le `Include` paramètre.

Le nouveau code lit l’entité existante et les appels `TryUpdateModel` pour mettre à jour les champs dans l’entité récupérée [en fonction de l’entrée d’utilisateur dans les données de formulaire publiées](xref:mvc/models/model-binding#how-model-binding-works). Le suivi automatique d’Entity Framework définit le `Modified` indicateur sur les champs qui sont modifiés par l’entrée du formulaire. Lorsque le `SaveChanges` est appelée, Entity Framework crée des instructions SQL pour mettre à jour la ligne de base de données. Conflits d’accès concurrentiel sont ignorés, et uniquement les colonnes de table qui ont été mis à jour par l’utilisateur sont mises à jour dans la base de données. (Un prochain didacticiel montre comment gérer les conflits d’accès concurrentiel.)

Comme une meilleure pratique pour empêcher sur-validation, les champs que vous souhaitez être mise à jour par le **modifier** page sont autorisés dans le `TryUpdateModel` paramètres. (Une chaîne vide avant la liste de champs dans la liste de paramètres est pour un préfixe à utiliser avec les noms de champs de formulaire.) Actuellement il n’y aucun des champs supplémentaires que vous protégez, mais qui répertorie les champs que vous souhaitez le classeur de modèles pour lier garantit que si vous ajoutez les champs pour le modèle de données à l’avenir, s’ils sont automatiquement protégés jusqu'à ce que vous les ajoutez ici.

À la suite de ces modifications, la signature de méthode de HttpPost `Edit` méthode est le même que le HttpGet `Edit` méthode ; par conséquent, vous avez renommé la méthode `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Code de substitution HttpPost modifier : créer et attacher

Du code de modification HttpPost recommandé garantit que seules les colonnes modifiées mis à jour et conserve les données dans les propriétés que vous ne souhaitez incluses pour la liaison de modèle. Toutefois, l’approche en lecture requiert une base de données supplémentaire en lecture et peut entraîner un code plus complexe pour la gestion des conflits d’accès concurrentiel. Une alternative consiste à attacher une entité créée par le classeur de modèles dans le contexte EF et la marquer comme étant modifiée. (Ne mettez pas à jour votre projet avec ce code, elle a affichée uniquement à illustrer une approche facultatif.) 

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Vous pouvez utiliser cette approche lorsque l’interface utilisateur de la page web inclut tous les champs dans l’entité et peut mettre à jour un d'entre eux.

Le code de modèle généré automatiquement utilise l’approche de créer et d’attachement, mais il intercepte uniquement `DbUpdateConcurrencyException` exceptions et retourne des codes d’erreur 404.  L’exemple suivant intercepte toute exception de mise à jour de base de données et affiche un message d’erreur.

### <a name="entity-states"></a>États de l’entité

Le contexte de base de données effectue le suivi des entités en mémoire sont synchronisées avec les lignes correspondantes dans la base de données, et ces informations déterminent ce qui se passe lorsque vous appelez le `SaveChanges` (méthode). Par exemple, lorsque vous passez une nouvelle entité à la `Add` (méthode), que l’état de l’entité est défini sur `Added`. Lorsque vous appelez le `SaveChanges` (méthode), le contexte de base de données émet une commande SQL INSERT.

Une entité peut être l’un des états suivants :

* `Added`. L’entité n’existe pas encore dans la base de données. Le `SaveChanges` méthode émet une instruction INSERT.

* `Unchanged`. Rien ne doit être effectué avec cette entité par le `SaveChanges` (méthode). Lorsque vous lisez une entité à partir de la base de données, l’entité démarre avec cet état.

* `Modified`. Tout ou partie des valeurs de propriété de l’entité ont été modifiés. Le `SaveChanges` méthode émet une instruction de mise à jour.

* `Deleted`. L’entité a été marquée pour suppression. Le `SaveChanges` méthode émet une instruction DELETE.

* `Detached`. L’entité n’est pas suivie par le contexte de base de données.

Dans une application de bureau, les modifications d’état sont généralement définies automatiquement. Lire une entité et apporter des modifications à certaines de ses valeurs de propriété. Cela entraîne son état d’entité qui sera automatiquement remplacée par `Modified`. Lorsque vous appelez `SaveChanges`, Entity Framework génère une instruction SQL UPDATE qui met à jour uniquement les propriétés que vous avez modifiée.

Dans une application web, le `DbContext` qui lit initialement une entité et affiche ses données à être modifié sont supprimées après le rendu d’une page. Lorsque le HttpPost `Edit` méthode d’action est appelée, une nouvelle requête web est lancée, et vous disposez d’une nouvelle instance de la `DbContext`. Si vous ré-Lisez l’entité dans ce nouveau contexte, vous simulez le traitement de bureau.

Mais si vous ne voulez extra opération de lecture, vous devez utiliser l’objet d’entité créé par le classeur de modèles.  Le plus simple consiste à définir l’état d’entité Modified comme dans l’autre code HttpPost modifier illustré précédemment. Lorsque vous appelez `SaveChanges`, Entity Framework met à jour toutes les colonnes de la ligne de base de données, car le contexte n’a aucun moyen de savoir quelles propriétés que vous avez modifié.

Si vous souhaitez éviter l’approche de lecture en premier, mais vous souhaitez également que l’instruction SQL UPDATE pour mettre à jour uniquement les champs que l’utilisateur a réellement changé, le code est plus complexe. Vous devez enregistrer les valeurs d’origine d’une certaine façon (par exemple à l’aide de champs masqués) afin qu’ils soient disponibles lorsque HttpPost `Edit` méthode est appelée. Vous pouvez ensuite créer une entité d’étudiants en utilisant les valeurs d’origine, l’appel du `Attach` méthode avec cette version d’origine de l’entité, mettre à jour les valeurs de l’entité pour les nouvelles valeurs, puis appelez `SaveChanges`.

### <a name="test-the-edit-page"></a>Test de la page de modification

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur une **modifier** lien hypertexte.

![Page Modifier les étudiants](crud/_static/student-edit.png)

Modifier une partie des données et cliquez sur **enregistrer**. Le **Index** page s’ouvre et affiche les données modifiées.

## <a name="update-the-delete-page"></a>Mise à jour de la page de suppression

Dans *StudentController.cs*, le code du modèle pour le HttpGet `Delete` utilise le `SingleOrDefaultAsync` pour récupérer l’entité sélectionnée de l’étudiant, comme vous l’avez vu dans les détails et modifier les méthodes. Toutefois, pour implémenter une erreur personnalisée message lorsque l’appel à `SaveChanges` échoue, vous allez ajouter des fonctionnalités à cette méthode et sa vue correspondante.

Comme vous avez vu pour la mise à jour et créez des opérations, opérations de suppression nécessitent deux méthodes d’action. La méthode est appelée en réponse à une demande GET affiche une vue qui permet à l’utilisateur à approuver ou d’annuler l’opération de suppression. Si l’utilisateur approuve le projet, une demande POST est créée. Lorsque cela se produit, HttpPost `Delete` méthode est appelée et que cette méthode effectue ensuite l’opération de suppression.

Vous allez ajouter un bloc try-catch à HttpPost `Delete` méthode pour gérer les erreurs qui peuvent se produire lorsque la base de données est mise à jour. Si une erreur se produit, la méthode HttpPost Delete appelle la méthode de suppression de HttpGet, en lui passant un paramètre qui indique qu’une erreur s’est produite. La méthode de suppression de HttpGet réaffiche puis la page de confirmation, ainsi que le message d’erreur, l’utilisateur donner la possibilité d’annuler ou recommencez l’opération.

Remplacez le HttpGet `Delete` méthode d’action avec le code suivant, qui gère le rapport d’erreurs.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Ce code accepte un paramètre facultatif qui indique si la méthode a été appelée après une défaillance pour enregistrer les modifications. Ce paramètre a la valeur false lorsque la HttpGet `Delete` méthode est appelée sans un échec. Lorsqu’elle est appelée par le HttpPost `Delete` méthode en réponse à une erreur de mise à jour de base de données, le paramètre a la valeur true et un message d’erreur est passé à la vue.

### <a name="the-read-first-approach-to-httppost-delete"></a>L’approche de lecture en premier HttpPost Delete

Remplacez le HttpPost `Delete` méthode d’action (nommé `DeleteConfirmed`) avec le code suivant, qui effectue l’opération de suppression et intercepte les erreurs de mise à jour de base de données.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

Ce code récupère l’entité sélectionnée, puis appelle la `Remove` pour définir l’état de l’entité `Deleted`. Lorsque `SaveChanges` est appelée, une suppression SQL commande est générée.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>L’approche de créer et d’attachement HttpPost Delete

Si l’amélioration des performances dans une application de volume élevé est une priorité, vous pouvez éviter une requête SQL inutile en instanciant une entité Student uniquement sur le serveur principal à l’aide de la clé de valeur, puis en définissant l’état d’entité `Deleted`. C’est tout ce qui a besoin d’Entity Framework afin de supprimer l’entité. (Ne placez pas de ce code dans votre projet ; il est ici uniquement à illustrer une alternative).

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Si les données doivent également être supprimées sont liées à l’entité, assurez-vous que cette suppression en cascade est configurée dans la base de données. Avec cette approche pour la suppression de l’entité, EF peut-être ignorer il existe des entités associées à supprimer.

### <a name="update-the-delete-view"></a>Mettre à jour la vue Delete

Dans *Views/Student/Delete.cshtml*, ajouter un message d’erreur entre l’en-tête h2 et l’en-tête h3, comme indiqué dans l’exemple suivant :

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Exécuter l’application, sélectionnez le **étudiants** onglet, puis cliquez sur un **supprimer** lien hypertexte :

![Page Confirmer la suppression](crud/_static/student-delete.png)

Cliquez sur **supprimer**. La page d’Index s’affiche sans l’étudiant supprimé. (Vous verrez un exemple de l’erreur de la gestion du code dans l’action dans le didacticiel d’accès concurrentiel).

## <a name="closing-database-connections"></a>Fermeture des connexions de base de données

Pour libérer les ressources contenant une connexion de base de données, l’instance de contexte doit être supprimée dès que possible lorsque vous avez terminé avec lui. La fonction intégrée ASP.NET Core [injection de dépendance](../../fundamentals/dependency-injection.md) prend en charge de cette tâche pour vous.

Dans *Startup.cs*, vous appelez le [méthode d’extension AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) à configurer la `DbContext` classe dans le conteneur DI d’ASP.NET. Que méthode définit la durée de vie du service `Scoped` par défaut. `Scoped`signifie que la durée de vie contexte coïncide avec la durée de vie de demande web, et le `Dispose` méthode sera appelée automatiquement à la fin de la demande web.

## <a name="handling-transactions"></a>La gestion des Transactions

Entity Framework implémente implicitement des transactions par défaut. Dans les scénarios où vous apportez des modifications à plusieurs lignes ou les tables, puis appelez `SaveChanges`, Entity Framework automatiquement permet de s’assurer que toutes vos modifications réussissent ou qu’elles échouent. Si des modifications sont tout d’abord faites ensuite une erreur se produit, ces modifications sont automatiquement restaurées. Pour les scénarios où vous avez besoin de plus contrôler--par exemple, si vous souhaitez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Aucun suivi des requêtes

Lorsqu’un contexte de base de données récupère les lignes de la table et crée des objets d’entité qui représentent les, par défaut il effectue le suivi de si les entités en mémoire sont synchronisées avec ce qui figure dans la base de données. Les données en mémoire agit comme un cache et sont utilisées lorsque vous mettez à jour une entité. Cette mise en cache est souvent inutile dans une application web, car les instances de contexte sont généralement de courte durée (une nouvelle est créé et supprimé pour chaque demande) et le contexte qui lit une entité est généralement supprimée avant que cette entité est utilisée à nouveau.

Vous pouvez désactiver le suivi des objets d’entité dans la mémoire en appelant le `AsNoTracking` (méthode). Scénarios classiques dans lesquels vous souhaiterez faire qui sont les suivantes :

* Pendant la durée de vie de contexte vous n’avez pas besoin de mettre à jour toutes les entités, et vous n’avez pas besoin d’EF à [charger automatiquement les propriétés de navigation avec les entités récupérées par des requêtes distinctes](read-related-data.md). Souvent, ces conditions sont remplies dans les méthodes d’action d’un contrôleur HttpGet.

* Vous exécutez une requête qui Récupère un gros volume de données, et qu’une petite partie des données retournées est mises à jour. Il peut être plus efficace de désactiver le suivi de la requête de grande taille et exécuter une requête plus tard de quelques entités qui doivent être mis à jour.

* Vous souhaitez attacher une entité afin de mettre à jour, mais plus haut que vous avez récupéré la même entité à des fins différentes. Étant donné que l’entité est déjà suivie par le contexte de base de données, vous ne peut pas joindre l’entité que vous souhaitez modifier. Une façon de gérer cette situation consiste à appeler `AsNoTracking` sur la requête précédente.

Pour plus d’informations, consultez [vs de suivi. Aucun suivi](https://docs.microsoft.com/ef/core/querying/tracking).

## <a name="summary"></a>Récapitulatif

Vous avez maintenant un ensemble complet de pages qui effectuent des opérations CRUD simples pour les entités de Student. Dans l’étape suivante du didacticiel, vous allez développer les fonctionnalités de la **Index** page en ajoutant le tri, filtrage et la pagination.

>[!div class="step-by-step"]
[Précédent](intro.md)
[Suivant](sort-filter-page.md)  
