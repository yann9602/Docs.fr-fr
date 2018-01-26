---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
title: "Implémentation de la fonctionnalité CRUD de base avec Entity Framework dans une Application ASP.NET MVC | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/09/2015
ms.topic: article
ms.assetid: a2f70ba4-83d1-4002-9255-24732726c4f2
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: e3dbea51199722bfe50f201c4ddcc90aa081927d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="implementing-basic-crud-functionality-with-the-entity-framework-in-aspnet-mvc-application"></a>Implémentation de la fonctionnalité CRUD de base avec Entity Framework dans une Application ASP.NET MVC
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans le didacticiel précédent, vous avez créé une application MVC qui stocke et affiche les données à l’aide d’Entity Framework et SQL Server LocalDB. Dans ce didacticiel, vous allez examiner et personnaliser le CRUD (créer, lire, mettre à jour, supprimer) le code que la génération de modèles automatique MVC crée automatiquement pour vous dans les contrôleurs et vues.

> [!NOTE]
> Il est courant de mettre en œuvre le modèle de référentiel afin de créer une couche d’abstraction entre votre contrôleur et de la couche d’accès aux données. Pour conserver ces didacticiels simple et consiste à apprendre à utiliser Entity Framework proprement dit, ils n’utilisent pas les référentiels. Pour plus d’informations sur l’implémentation des référentiels, consultez le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).


Dans ce didacticiel, vous allez créer les pages web suivantes :

![Student_Details_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image1.png)

![Student_Edit_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image2.png)

![Student_delete_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image3.png)

## <a name="create-a-details-page"></a>Créer une Page de détails

Le code de modèle généré automatiquement pour les étudiants `Index` page omis le `Enrollments` propriété, car cette propriété contienne une collection. Dans la `Details` page, vous devez afficher le contenu de la collection dans un tableau HTML.

 Dans *Controllers\StudentController.cs*, la méthode d’action pour le `Details` afficher utilise le [trouver](https://msdn.microsoft.com/library/gg696418(v=VS.103).aspx) méthode pour récupérer un seul `Student` entité. 

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample1.cs)]

La valeur de clé est passée à la méthode comme étant le `id` paramètre et proviennent de *de router les données* dans les **détails** lien hypertexte sur la page d’Index.

### <a name="tip-route-data"></a>Conseil : **de router les données**

Données d’itinéraire sont les données que le classeur de modèles trouvé dans un segment d’URL spécifié dans la table de routage. Par exemple, l’itinéraire par défaut spécifie `controller`, `action`, et `id` segments :

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample2.cs?highlight=3)]

Dans l’URL suivante, mappe l’itinéraire par défaut `Instructor` en tant que le `controller`, `Index` comme le `action` et 1 comme le `id`; il s’agit des valeurs de données d’itinéraire.

`http://localhost:1230/Instructor/Index/1?courseID=2021`

« ? courseID = 2021 » est une valeur de chaîne de requête. Le classeur de modèles fonctionne également si vous passez le `id` en tant que valeur de chaîne de requête :

`http://localhost:1230/Instructor/Index?id=1&CourseID=2021`

Les URL sont créés par `ActionLink` instructions dans la vue Razor. Dans le code suivant, le `id` paramètre correspond à l’itinéraire par défaut, par conséquent, `id` est ajouté pour les données d’itinéraire.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample3.cshtml)]

Dans le code suivant, `courseID` ne correspond pas à un paramètre dans l’itinéraire par défaut, donc elle est ajoutée en tant qu’une chaîne de requête.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample4.cshtml)]


1. Ouvrez *Views\Student\Details.cshtml*. Chaque champ est affichée en utilisant un `DisplayFor` assistance, comme indiqué dans l’exemple suivant :

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample5.cshtml)]
2. Après le `EnrollmentDate` champ et immédiatement avant la fermeture `</dl>` , ajoutez le code en surbrillance pour afficher la liste des inscriptions, comme indiqué dans l’exemple suivant :

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample6.cshtml?highlight=8-29)]

    Si la mise en retrait du code est incorrect, une fois que vous collez le code, appuyez sur CTRL + K + D pour résoudre ce problème.

    Ce code effectue une itération sur les entités dans le `Enrollments` propriété de navigation. Pour chaque `Enrollment` entité dans la propriété, il affiche le titre du cours et la qualité. Le titre du cours est récupéré à partir de la `Course` entité qui est stockée dans le `Course` propriété de navigation de la `Enrollments` entité. Toutes ces données sont récupérées à partir de la base de données automatiquement lorsqu’il est nécessaire. (En d’autres termes, vous utilisez le chargement différé ici. Vous n’avez pas spécifié *chargement hâtif* pour le `Courses` propriété de navigation pour les inscriptions n’ont pas été récupérées dans la même requête qui a obtenu les étudiants. Au lieu de cela, la première fois que vous essayez d’accéder à la `Enrollments` propriété de navigation, une nouvelle requête est envoyée à la base de données pour récupérer les données. Vous pouvez en savoir plus sur le chargement différé et chargement hâtif dans le [lors de la lecture des données connexes](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) didacticiel plus loin dans cette série.)
3. Exécutez la page en sélectionnant le **étudiants** onglet et en cliquant sur un **détails** lien de Alexander Carson. (Si vous appuyez sur CTRL + F5 pendant que le fichier Details.cshtml est ouvert, vous obtiendrez une erreur HTTP 400, car Visual Studio essaie d’exécuter la page de détails, mais il n’a pas été atteint à partir d’une liaison qui spécifie les étudiants à afficher. In that case, simplement supprimer « Étudiant/détails » de l’URL et réessayez, ou fermez le navigateur, droit sur le projet, puis cliquez sur **vue**, puis cliquez sur **afficher dans le navigateur**.)

    Vous voyez la liste des cours et les notes pour l’étudiant sélectionné :

    ![Student_Details_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image4.png)

## <a name="update-the-create-page"></a>Mise à jour de la Page de création

1. Dans *Controllers\StudentController.cs*, remplacez le `HttpPost``Create` méthode d’action avec le code suivant pour ajouter un `try-catch` bloquer et supprimer `ID` à partir de la [attribut de liaison de](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) pour la méthode de modèle généré automatiquement :

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample7.cs?highlight=3,5-6,13-18)]

    Ce code ajoute la `Student` entité créée par le classeur de modèles ASP.NET MVC à le `Students` entité définie et puis enregistre les modifications dans la base de données. (*Binder de modèle* fait référence à la fonctionnalité d’ASP.NET MVC qui facilite la plus facile de travailler avec les données envoyées par un formulaire un classeur de modèles convertit validée formulaire valeurs aux types CLR et les transmet à la méthode d’action dans les paramètres. In this case, le classeur de modèles instancie un `Student` entité pour vous à l’aide de la propriété des valeurs à partir de la `Form` collection.)

    Vous supprimé `ID` à partir de la liaison d’attribut, car `ID` est la valeur de clé primaire SQL Server définira automatiquement lorsque la ligne est insérée. Entrée de l’utilisateur ne définit pas la `ID` valeur.

    <a id="overpost"></a>

    ### <a name="security-warning---the-validateantiforgerytoken-attribute-helps-prevent-cross-site-request-forgerysecurityxsrfcsrf-prevention-in-aspnet-mvc-and-web-pagesmd-attacks-it-requires-a-corresponding-htmlantiforgerytoken-statement-in-the-view-which-youll-see-later"></a>Avertissement de sécurité - le `ValidateAntiForgeryToken` attribut permet d’empêcher les [falsification de requête intersites](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) attaques. Il requiert un correspondant `Html.AntiForgeryToken()` instruction dans la vue, vous verrez plus tard.

    Le `Bind` attribut consiste à protéger contre *trop de validation* de créer des scénarios. Par exemple, supposons que la `Student` entité inclut un `Secret` propriété que vous ne souhaitez pas cette page web à définir.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample8.cs?highlight=7)]

    Même si vous n’avez pas un `Secret` champ sur la page web, un pirate peut utiliser un outil tel que [fiddler](http://fiddler2.com/home), ou écrire du JavaScript, pour valider un `Secret` former la valeur. Sans le [lier](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) attribut limitant les champs que le classeur de modèles utilise lorsqu’il crée un `Student` instance*,* qui choisit le classeur de modèles `Secret` former la valeur et l’utiliser pour créer le `Student` instance d’entité. Ensuite, quel que soit le pirate spécifié pour la valeur du `Secret` champ de formulaire est mis à jour de la base de données. L’illustration suivante montre le fiddler outil Ajout le `Secret` champ (avec la valeur « OverPost ») pour les valeurs de formulaire publiées.

    ![](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image5.png)  

    La valeur « OverPost » puis est correctement ajoutée à la `Secret` propriété le texte inséré de ligne, même si vous n’aviez pas envisagés que la page web est en mesure de définir cette propriété.

    Il est recommandé d’utiliser le `Include` paramètre avec le `Bind` attribut *liste blanche* champs. Il est également possible d’utiliser le `Exclude` paramètre *blacklist* champs que vous souhaitez exclure. La raison `Include` est plus sécurisé est que lorsque vous ajoutez une nouvelle propriété à l’entité, le nouveau champ n'est pas automatiquement protégé par un `Exclude` liste.

    Vous pouvez empêcher overposting dans les scénarios de modifier est en lecture de l’entité à partir de la base de données tout d’abord, puis `TryUpdateModel`, en passant une liste de propriétés autorisés explicites. Qui est la méthode utilisée dans ces didacticiels.

    Une autre façon d’empêcher overposting qui est utilisé par de nombreux développeurs consiste à utiliser les afficher les modèles plutôt que des classes d’entité avec la liaison de modèle. Incluez uniquement les propriétés que vous souhaitez mettre à jour dans le modèle d’affichage. Une fois le classeur de modèles MVC terminée, copier les propriétés de modèle de vue à l’instance d’entité, en utilisant éventuellement un outil tel que [AutoMapper](http://automapper.org/). Utiliser la base de données. Écriture de l’instance d’entité pour définir son état à inchangé, puis définissez Property("PropertyName"). IsModified true sur chaque propriété d’entité qui est incluse dans le modèle d’affichage. Cette méthode est effective à la fois dans modifier et créer des scénarios.

    Autre que le `Bind` attribut, le `try-catch` bloc est la seule modification que vous avez apportées au code de modèle généré automatiquement. Si une exception qui dérive de [DataException](https://msdn.microsoft.com/library/system.data.dataexception.aspx) est interceptée pendant l’enregistrement des modifications, un message d’erreur générique s’affiche. [DataException](https://msdn.microsoft.com/library/system.data.dataexception.aspx) exceptions sont parfois due à un élément externe à l’application plutôt qu’une erreur de programmation, il est conseillé pour réessayer. Bien que non implémenté dans cet exemple, une application de qualité production se connectera l’exception. Pour plus d’informations, consultez la **journal pour obtenir un aperçu** section [surveillance et télémétrie (construction réelle les applications Cloud avec Azure)](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log).

    Le code dans *Views\Student\Create.cshtml* est similaire à ce que vous avez vu dans *Details.cshtml*, sauf que `EditorFor` et `ValidationMessageFor` programmes d’assistance sont utilisées pour chaque champ à la place `DisplayFor`. Voici le code :

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample9.cshtml)]

    *Create.chstml* inclut également `@Html.AntiForgeryToken()`, qui fonctionne avec les `ValidateAntiForgeryToken` attribut dans le contrôleur pour éviter [falsification de requête intersites](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) attaques.

    Aucun changement est nécessaire dans *Create.cshtml*.
2. Exécutez la page en sélectionnant le **étudiants** onglet et en cliquant sur **créer un nouveau**.
3. Entrez les noms et une date non valide, cliquez sur **créer** pour afficher le message d’erreur.

    ![Students_Create_page_error_message](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image6.png)

    Il s’agit de la validation côté serveur que vous obtenez par défaut ; dans un prochain didacticiel, vous verrez comment ajouter des attributs qui génèrent également du code pour la validation côté client. Le code en surbrillance suivant illustre la vérification de validation de modèle dans le **créer** (méthode).

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample10.cs?highlight=1)]
4. Modifier la date à une valeur valide, puis cliquez sur **créer** pour afficher le nouvel étudiant s’affichent dans le **Index** page.

    ![Students_Index_page_with_new_student](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image7.png)

## <a name="update-the-edit-httppost-method"></a>Mise à jour de la méthode HttpPost Edit

Dans *Controllers\StudentController.cs*, le `HttpGet` `Edit` (méthode) (l’autre sans le `HttpPost` attribut) utilise le `Find` pour récupérer le texte sélectionné `Student` entité, comme vous l’avez vu dans le `Details` (méthode). Vous n’avez pas besoin de modifier cette méthode.

Toutefois, remplacez le `HttpPost` `Edit` méthode d’action avec le code suivant :

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample11.cs)]

Ces modifications implémentent une meilleure pratique de sécurité pour empêcher [sur-validation](#overpost), le scaffolder généré un `Bind` d’attribut et ajouté l’entité créée par le classeur de modèles à l’entité avec un indicateur modifié. Que code n’est plus recommandé, car le `Bind` attribut efface toutes les données existantes dans les champs non répertoriés dans le `Include` paramètre. À l’avenir, le scaffolder contrôleur MVC sera mise à jour afin qu’il ne génère pas de `Bind` attributs pour modifier les méthodes.

Le nouveau code lit l’entité existante et les appels [TryUpdateModel](https://msdn.microsoft.com/library/system.web.mvc.controller.tryupdatemodel(v=vs.118).aspx) pour mettre à jour les champs à partir de l’entrée d’utilisateur dans les données de formulaire publiées. Le suivi automatique d’Entity Framework définit le [modifié](https://msdn.microsoft.com/library/system.data.entitystate.aspx) indicateur sur l’entité. Lorsque le [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) méthode est appelée, la `Modified` l’indicateur permet à Entity Framework de créer des instructions SQL pour mettre à jour la ligne de base de données. [Conflits d’accès concurrentiel](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md) sont ignorées, et toutes les colonnes de la ligne de base de données sont mises à jour, y compris celles que l’utilisateur n’a pas changé. (Un prochain didacticiel montre comment gérer les conflits d’accès concurrentiel, et si vous souhaitez uniquement des champs individuels d’être mis à jour dans la base de données, vous pouvez définir l’entité Unchanged et définir des champs modifiés.)

Comme meilleure pratique pour éviter les overposting, les champs que vous souhaitez être mise à jour par la page de modification sont autorisés dans le `TryUpdateModel` paramètres. Actuellement il n’y aucun des champs supplémentaires que vous protégez, mais qui répertorie les champs que vous souhaitez le classeur de modèles pour lier garantit que si vous ajoutez les champs pour le modèle de données à l’avenir, s’ils sont automatiquement protégés jusqu'à ce que vous les ajoutez ici.

À la suite de ces modifications, la signature de méthode de la méthode HttpPost Edit est identique à la méthode de modification HttpGet ; Par conséquent, vous avez renommé la méthode EditPost.

> [!TIP] 
> 
> **États de l’entité et les attacher et les méthodes de SaveChanges**
> 
> Le contexte de base de données effectue le suivi des entités en mémoire sont synchronisées avec les lignes correspondantes dans la base de données, et ces informations déterminent ce qui se passe lorsque vous appelez le `SaveChanges` (méthode). Par exemple, lorsque vous passez une nouvelle entité à la [ajouter](https://msdn.microsoft.com/library/system.data.entity.dbset.add(v=vs.103).aspx) (méthode), que l’état de l’entité est défini sur `Added`. Lorsque vous appelez le [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) (méthode), le contexte de base de données émet une SQL `INSERT` commande.
> 
> Une entité peut être dans un de le[suivant états](https://msdn.microsoft.com/library/system.data.entitystate.aspx):
> 
> - `Added`. L’entité n’existe pas encore dans la base de données. Le `SaveChanges` méthode doit émettre une `INSERT` instruction.
> - `Unchanged`. Rien ne doit être effectué avec cette entité par le `SaveChanges` (méthode). Lorsque vous lisez une entité à partir de la base de données, l’entité démarre avec cet état.
> - `Modified`. Tout ou partie des valeurs de propriété de l’entité ont été modifiés. Le `SaveChanges` méthode doit émettre une `UPDATE` instruction.
> - `Deleted`. L’entité a été marquée pour suppression. Le `SaveChanges` méthode doit émettre un `DELETE` instruction.
> - `Detached`. L’entité n’est pas suivie par le contexte de base de données.
> 
> Dans une application de bureau, les modifications d’état sont généralement définies automatiquement. Dans un type d’application de bureau, vous lisez une entité et apportez des modifications à certaines de ses valeurs de propriété. Cela entraîne son état d’entité qui sera automatiquement remplacée par `Modified`. Lorsque vous appelez `SaveChanges`, Entity Framework génère un code SQL `UPDATE` instruction qui met à jour uniquement les propriétés que vous avez modifiée.
> 
> N’autorise pas la nature déconnectée des applications web pour cette séquence continue. Le [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx) qui lit une entité est supprimée après une page est rendue. Lorsque le `HttpPost` `Edit` méthode d’action est appelée, une nouvelle demande est faite, et vous disposez d’une nouvelle instance de la [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx), donc vous devez définir manuellement l’état d’entité `Modified.` puis lorsque vous appelez `SaveChanges`, Entity Framework met à jour toutes les colonnes de la ligne de base de données, car le contexte n’a aucun moyen de savoir quelles propriétés que vous avez modifié.
> 
> Si vous souhaitez que l’instruction SQL `Update` instruction pour mettre à jour uniquement les champs que l’utilisateur est réellement modifié, vous pouvez enregistrer les valeurs d’origine (tels que les champs masqués) afin qu’ils soient disponibles lorsque le `HttpPost` `Edit` méthode est appelée. Vous pouvez ensuite créer un `Student` entité en utilisant les valeurs d’origine, l’appel de la `Attach` méthode avec cette version d’origine de l’entité, mettre à jour les valeurs de l’entité pour les nouvelles valeurs, puis appelez `SaveChanges.` pour plus d’informations, consultez [ États de l’entité et SaveChanges](https://msdn.microsoft.com/data/jj592676) et [données locales](https://msdn.microsoft.com/data/jj592872) dans le centre de développement de données MSDN.


Le code HTML et Razor dans *Views\Student\Edit.cshtml* est similaire à ce que vous avez vu dans *Create.cshtml*, et aucune modification n’est requise.

Exécutez la page en sélectionnant le **étudiants** onglet et puis en cliquant sur un **modifier** lien hypertexte.

![Student_Edit_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image8.png)

Modifier une partie des données et cliquez sur **enregistrer**. Vous consultez les données modifiées dans la page d’Index.

![Students_Index_page_after_edit](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image9.png)

## <a name="updating-the-delete-page"></a>Mise à jour de la Page de suppression

Dans *Controllers\StudentController.cs*, le code du modèle pour le `HttpGet` `Delete` utilise le `Find` pour récupérer le texte sélectionné `Student` entité, comme vous l’avez vu dans les `Details` et `Edit` méthodes. Toutefois, pour implémenter une erreur personnalisée message lorsque l’appel à `SaveChanges` échoue, vous allez ajouter des fonctionnalités à cette méthode et sa vue correspondante.

Comme vous avez vu pour la mise à jour et créez des opérations, opérations de suppression nécessitent deux méthodes d’action. La méthode est appelée en réponse à une demande GET affiche une vue qui permet à l’utilisateur à approuver ou d’annuler l’opération de suppression. Si l’utilisateur approuve le projet, une demande POST est créée. Lorsque cela se produit, le `HttpPost` `Delete` méthode est appelée et que cette méthode effectue ensuite l’opération de suppression.

Vous allez ajouter un `try-catch` bloquer à la `HttpPost` `Delete` méthode pour gérer les erreurs qui peuvent se produire lorsque la base de données est mise à jour. Si une erreur se produit, le `HttpPost` `Delete` les appels de méthode le `HttpGet` `Delete` méthode, en lui passant un paramètre qui indique qu’une erreur s’est produite. Le `HttpGet Delete` méthode réaffiche puis la page de confirmation, ainsi que le message d’erreur, l’utilisateur donner la possibilité d’annuler ou recommencez l’opération.

1. Remplacez le `HttpGet` `Delete` méthode d’action avec le code suivant, qui gère le rapport d’erreurs : 

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample12.cs?highlight=1,7-10)]

    Ce code accepte un [paramètre facultatif](https://msdn.microsoft.com/library/dd264739.aspx) qui indique si la méthode a été appelée après une défaillance pour enregistrer les modifications. Ce paramètre est `false` lors de la `HttpGet` `Delete` méthode est appelée sans un échec. Lorsqu’elle est appelée le `HttpPost` `Delete` en réponse à une erreur de mise à jour de base de données, le paramètre est `true` et un message d’erreur est passé à la vue.
- Remplacez le `HttpPost` `Delete` méthode d’action (nommé `DeleteConfirmed`) avec le code suivant, qui effectue l’opération de suppression et intercepte les erreurs de mise à jour de base de données.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample13.cs)]

    Ce code récupère l’entité sélectionnée, puis appelle la [supprimer](https://msdn.microsoft.com/library/system.data.entity.dbset.remove(v=vs.103).aspx) pour définir l’état de l’entité `Deleted`. Lorsque `SaveChanges` est appelée, SQL `DELETE` commande est générée. Vous avez également modifié le nom de la méthode d’action à partir de `DeleteConfirmed` à `Delete`. Le nom de code modèle généré automatiquement le `HttpPost` `Delete` méthode `DeleteConfirmed` afin de donner la `HttpPost` une signature unique (méthode). (Le CLR a besoin des méthodes surchargées pour avoir des paramètres de l’autre méthode.) Maintenant que les signatures sont uniques, vous pouvez coller avec la convention MVC et utiliser le même nom pour le `HttpPost` et `HttpGet` supprimer les méthodes.

    Si l’amélioration des performances dans une application de volume élevé est une priorité, vous pouvez éviter une requête SQL inutile pour récupérer la ligne en remplaçant les lignes de code qui appellent le `Find` et `Remove` méthodes avec le code suivant :

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample14.cs)]

    Ce code instancie un `Student` entité à l’aide de la valeur de clé primaire, puis définir l’état d’entité à `Deleted`. C’est tout ce qui a besoin d’Entity Framework afin de supprimer l’entité.

    Comme indiqué, le `HttpGet` `Delete` méthode ne supprime pas les données. Exécution d’une opération de suppression en réponse à une commande GET demande (ou dans notre exemple, une opération de modification, créer l’opération, ou toute autre opération qui modifie des données) crée un risque de sécurité. Pour plus d’informations, consultez [ASP.NET MVC Conseil #46 : n’utilisez pas supprimer les liens parce qu’elles créent des failles de sécurité](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx) sur le blog de Stephen Walther.
- Dans *Views\Student\Delete.cshtml*, ajouter un message d’erreur entre la `h2` titre et le `h3` titre, comme indiqué dans l’exemple suivant :

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample15.cshtml?highlight=2)]

    Exécutez la page en sélectionnant le **étudiants** onglet et en cliquant sur un **supprimer** lien hypertexte :

    ![Student_Delete_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image10.png)
- Cliquez sur **supprimer**. La page d’Index s’affiche sans l’étudiant supprimé. (Vous verrez un exemple de l’erreur de la gestion du code dans l’action dans le [didacticiel de concurrence](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md).)

## <a name="closing-database-connections"></a>Fermeture des connexions de base de données

Pour fermer les connexions de base de données et libérer les ressources qu’ils détiennent dès que possible, supprimez l’instance de contexte lorsque vous avez terminé avec lui. Autrement dit pourquoi le code de modèle généré automatiquement fournit un [Dispose](https://msdn.microsoft.com/library/system.idisposable.dispose(v=vs.110).aspx) méthode à la fin de la `StudentController` classe dans *StudentController.cs*, comme illustré dans l’exemple suivant :

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample16.cs)]

La base de `Controller` classe déjà implémente la `IDisposable` interface, ce code ajoute simplement une substitution de la `Dispose(bool)` méthode explicitement supprimer l’instance de contexte.

<a id="transactions"></a>
## <a name="handling-transactions"></a>La gestion des Transactions

Entity Framework implémente implicitement des transactions par défaut. Dans les scénarios où vous apportez des modifications à plusieurs lignes ou les tables, puis appelez `SaveChanges`, Entity Framework automatiquement permet de s’assurer que toutes vos modifications réussissent ou toutes les échouent. Si des modifications sont tout d’abord faites ensuite une erreur se produit, ces modifications sont automatiquement restaurées. Pour les scénarios où vous avez besoin de plus contrôler--par exemple, si vous souhaitez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [utiliser des Transactions](https://msdn.microsoft.com/data/dn456843) sur MSDN.

## <a name="summary"></a>Récapitulatif

Vous avez maintenant un ensemble complet de pages qui effectuent des opérations CRUD simples pour `Student` entités. Programmes d’assistance MVC vous permet de générer des éléments d’interface utilisateur pour les champs de données. Pour plus d’informations sur les programmes d’assistance MVC, consultez [rendu d’un formulaire à l’aide de HTML programmes d’assistance pour](https://msdn.microsoft.com/library/dd410596(v=VS.98).aspx) (la page est pour MVC 3, mais sont toujours applicables pour MVC 5).

Dans l’étape suivante du didacticiel, vous allez développer les fonctionnalités de la page d’Index en ajoutant le tri et de pagination.

Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer. Vous pouvez également demander de nouvelles rubriques à [afficher Me comment avec le Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Vous trouverez des liens vers d’autres ressources Entity Framework dans [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)
[Suivant](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)
