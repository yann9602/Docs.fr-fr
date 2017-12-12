---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: "Advanced scénarios Entity Framework pour une Application Web MVC (10 10) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 64906a1d-f734-41cf-9615-ee95f8740996
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: d58a745896b29317c1d1049e3bf1a5ec2e628820
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="advanced-entity-framework-scenarios-for-an-mvc-web-application-10-of-10"></a>Scénarios d’infrastructure entité avancées pour une Application Web MVC (10 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Vous pouvez démarrer la série de didacticiels à partir du début ou [télécharger un projet de démarrage pour ce chapitre](building-the-ef5-mvc4-chapter-downloads.md) et Démarrer ici.
> 
> > [!NOTE] 
> > 
> > Si vous rencontrez un problème que vous ne pouvez pas résoudre, [télécharger le chapitre terminé](building-the-ef5-mvc4-chapter-downloads.md) et essayez de reproduire le problème. En règle générale, vous trouverez la solution au problème en comparant votre code pour le code terminé. Pour certaines erreurs courantes et comment les résoudre, consultez [erreurs et des solutions de contournement.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


Dans le didacticiel précédent vous implémenté le référentiel et une unité de travail des modèles. Ce didacticiel couvre les rubriques suivantes :

- Exécution des requêtes SQL bruts.
- Exécution de requêtes de suivi de non.
- Examen des requêtes envoyées à la base de données.
- Utilisation des classes de proxy.
- La désactivation de la détection automatique des modifications.
- Désactivation de la validation lors de l’enregistrement de modifications.
- [Erreurs et des solutions de contournement](#errors)

Pour la plupart de ces rubriques, vous allez travailler avec des pages que vous avez déjà créé. Pour utiliser SQL brute pour en bloc des mises à jour, vous allez créer une nouvelle page qui met à jour le nombre de crédits de tous les cours dans la base de données :

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

Et d’utiliser une requête non suivi que vous allez ajouter la nouvelle logique de validation à la page Modifier le service :

![Department_Edit_page_with_duplicate_administrator_error_message](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image2.png)

## <a name="performing-raw-sql-queries"></a>Requêtes SQL brut exécution

L’API de premier Code Entity Framework comprend les méthodes qui vous permettent de transmettre des commandes SQL directement à la base de données. Les options suivantes sont disponibles :

- Utilisez la `DbSet.SqlQuery` méthode pour les requêtes qui retournent des types d’entités. Les objets retournés doivent être du type attendu par le `DbSet` objet et elles sont suivies automatiquement par le contexte de base de données sauf si vous désactivez le suivi. (Consultez la section suivante la `AsNoTracking` méthode.)
- Utilisez la `Database.SqlQuery` méthode pour les requêtes qui retournent des types qui ne sont pas des entités. Les données renvoyées n’est pas suivies par le contexte de la base de données, même si vous utilisez cette méthode pour récupérer des types d’entités.
- Utilisez le [Database.ExecuteSqlCommand](https://msdn.microsoft.com/en-us/library/gg679456(v=vs.103).aspx) pour les commandes de requête non.

Un des avantages de l’utilisation d’Entity Framework est qu’elle évite de lier votre code trop près d’une méthode particulière du stockage des données. Cela en générant des requêtes et commandes SQL pour vous, ce qui vous évite de les écrire. Il existe des scénarios exceptionnelles lorsque vous avez besoin exécuter des requêtes SQL spécifiques que vous avez créé manuellement, mais ces méthodes permettent de gérer ces exceptions.

Comme étant toujours true lorsque vous exécutez des commandes SQL dans une application web, vous devez prendre des précautions pour protéger votre site contre les attaques par injection SQL. Une manière de procéder consiste à utiliser des requêtes paramétrables pour vous assurer que les chaînes soumis par une page web ne peut pas être interprétées comme des commandes SQL. Dans ce didacticiel, vous utiliserez des requêtes paramétrables lors de l’intégration de l’entrée d’utilisateur dans une requête.

### <a name="calling-a-query-that-returns-entities"></a>Appel d’une requête qui retourne des entités

Supposons que vous souhaitez le `GenericRepository` classe afin de fournir plus de filtrage et de tri de flexibilité sans nécessiter que vous créez une classe dérivée avec des méthodes supplémentaires. Pour faire qui serait pour ajouter une méthode qui accepte une requête SQL. Vous pouvez ensuite spécifier n’importe quel type de filtrage ou de tri vous voulez dans le contrôleur, tel qu’un `Where` clause qui dépend d’un joint ou une sous-requête. Dans cette section, vous verrez comment implémenter une telle méthode.

Créer le `GetWithRawSql` méthode en ajoutant le code suivant à *GenericRepository.cs*:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs)]

Dans *CourseController.cs*, appelez la nouvelle méthode à partir de la `Details` méthode, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

Dans ce cas vous pouvez avoir utilisé le `GetByID` (méthode), mais que vous utilisez le `GetWithRawSql` méthode pour vérifier que le `GetWithRawSQL` fonctionne de la méthode.

Exécutez la page de détails pour vérifier que la requête select works (sélectionnez le **cours** onglet, puis **détails** pour un cours).

![Course_Details_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image3.png)

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>Appel d’une requête qui retourne les autres Types d’objets

Précédemment, vous avez créé une grille des statistiques de student pour la page à propos montrant le nombre d’élèves pour chaque date d’inscription. Le code qui effectue l’opération dans *HomeController.cs* utilise LINQ :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs)]

Supposons que vous souhaitez écrire le code qui extrait ces données directement dans SQL au lieu d’utiliser LINQ. Pour faire que vous devez exécuter une requête qui retourne une valeur autre que les objets d’entité, ce qui signifie que vous devez utiliser le `Database.SqlQuery` (méthode).

Dans *HomeController.cs*, remplacez l’instruction LINQ dans le `About` méthode avec le code suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

Exécutez la page à propos de. Il affiche les mêmes données qu’auparavant.

![About_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image4.png)

### <a name="calling-an-update-query"></a>Appel d’une requête de mise à jour

Supposons que les administrateurs de Contoso University veulent être en mesure d’effectuer des modifications en bloc dans la base de données, telles que la modification du nombre de crédits pour chaque cours. Si l’université comporte un grand nombre des cours, il est inefficace pour les extraire sous la forme d’entités et les modifier individuellement. Dans cette section, vous allez implémenter une page web qui permet à l’utilisateur spécifier un facteur permettant de modifier le nombre de crédits pour tous les cours, et vous devez apporter la modification en exécutant un SQL `UPDATE` instruction. La page web doit ressembler à l’illustration suivante :

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image5.png)

Dans le didacticiel précédent, vous avez utilisé le référentiel générique pour lire et mettre à jour `Course` entités dans la `Course` contrôleur. Pour cette opération de mise à jour en bloc, vous devez créer une nouvelle méthode de référentiel n’est pas dans le référentiel générique. Pour ce faire, vous allez créer une dédiée `CourseRepository` classe qui dérive de la `GenericRepository` classe.

Dans le *DAL* dossier, créez *CourseRepository.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cs)]

Dans *UnitOfWork.cs*, modifiez le `Course` type de référentiel à partir de `GenericRepository<Course>` à`CourseRepository:`

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.cs)]

Dans *CourseContoller.cs*, ajoutez une `UpdateCourseCredits` méthode :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

Cette méthode servira pour les deux `HttpGet` et `HttpPost`. Lorsque le `HttpGet` `UpdateCourseCredits` exécution de la méthode, le `multiplier` variable est null et la vue affiche une zone de texte vide et un bouton d’envoi, comme indiqué dans l’illustration précédente.

Lorsque le **mise à jour** bouton et le `HttpPost` exécution de la méthode, `multiplier` aura la valeur entrée dans la zone de texte. Le code appelle ensuite le référentiel `UpdateCourseCredits` (méthode), qui retourne le nombre des lignes affectées, et cette valeur est stockée dans le `ViewBag` objet. Lorsque la vue reçoit le nombre de lignes affectées dans la `ViewBag` de l’objet, il affiche ce nombre au lieu de la zone de texte et bouton, envoyer comme indiqué dans l’illustration suivante :

![Update_Course_Credits_rows_affected_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image6.png)

Créer un affichage dans le *Views\Course* dossier pour la page de crédits de cours de mise à jour :

![Add_View_dialog_box_for_Update_Course_Credits](https://asp.net/media/2578203/Windows-Live-Writer_Advanced-Entity-Framework-Scenarios-for-_CEF8_Add_View_dialog_box_for_Update_Course_Credits.png)

Dans *Views\Course\UpdateCourseCredits.cshtml*, remplacez le code de modèle par le code suivant :

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

Exécuter la `UpdateCourseCredits` méthode en sélectionnant le **cours** onglet, puis en ajoutant « / UpdateCourseCredits » à la fin de l’URL dans la barre d’adresses du navigateur (par exemple : `http://localhost:50205/Course/UpdateCourseCredits`). Entrez un nombre dans la zone de texte :

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image7.png)

Cliquez sur **Mettre à jour**. Vous voyez le nombre de lignes affectées :

![Update_Course_Credits_rows_affected_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image8.png)

Cliquez sur **retour à la liste** pour afficher la liste des cours avec la modification du numéro de crédits.

![Courses_Index_page_showing_revised_credits](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image9.png)

Pour plus d’informations sur les requêtes SQL brutes, consultez [des requêtes SQL brutes](https://blogs.msdn.com/b/adonet/archive/2011/02/04/using-dbcontext-in-ef-feature-ctp5-part-10-raw-sql-queries.aspx) sur le blog de l’équipe Entity Framework.

## <a name="no-tracking-queries"></a>Aucun suivi des requêtes

Lorsqu’un contexte de base de données récupère des lignes de base de données et crée des objets d’entité qui représentent les, par défaut il effectue le suivi de si les entités en mémoire sont synchronisées avec ce qui figure dans la base de données. Les données en mémoire agit comme un cache et sont utilisées lorsque vous mettez à jour une entité. Cette mise en cache est souvent inutile dans une application web, car les instances de contexte sont généralement de courte durée (une nouvelle est créé et supprimé pour chaque demande) et le contexte qui lit une entité est généralement supprimée avant que cette entité est utilisée à nouveau.

Vous pouvez spécifier si le contexte suit les objets d’entité pour une requête à l’aide de la `AsNoTracking` (méthode). Scénarios classiques dans lesquels vous souhaiterez faire qui sont les suivantes :

- La requête récupère un gros volume de données que la désactivation du suivi peuvent améliorer sensiblement les performances.
- Vous souhaitez attacher une entité afin de mettre à jour, mais vous avez précédemment récupéré à la même entité à des fins différentes. Étant donné que l’entité est déjà suivie par le contexte de base de données, vous ne peut pas joindre l’entité que vous souhaitez modifier. Une façon d’éviter ce problème consiste à utiliser le `AsNoTracking` option avec la requête précédente.

Dans cette section, vous allez implémenter une logique métier qui illustre le second de ces scénarios. Plus précisément, vous allez appliquer une règle d’entreprise qui indique qu’un formateur ne peut pas être l’administrateur de plus d’un service.

Dans *DepartmentController.cs*, ajoutez une nouvelle méthode que vous pouvez appeler à partir de la `Edit` et `Create` méthodes pour s’assurer qu’aucune des deux services n’ont le même administrateur :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.cs)]

Ajoutez le code dans le `try` bloquer de la `HttpPost` `Edit` méthode à appeler cette nouvelle méthode, s’il n’y a aucune erreur de validation. Le `try` bloc ressemble maintenant à l’exemple suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample11.cs?highlight=9-12)]

Exécutez la page Modifier le service et essayez de modifier un administrateur d’un service à un formateur qui est déjà l’administrateur d’un autre service. Vous obtenez le message d’erreur attendu :

![Department_Edit_page_with_duplicate_administrator_error_message](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

Maintenant exécuter ce changement d’heure et de la page Modifier le service est à nouveau la **Budget** quantité. Lorsque vous cliquez sur **enregistrer**, vous consultez une page d’erreur :

![Department_Edit_page_with_object_state_manager_error_message](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image11.png)

Le message d’erreur est «`An object with the same key already exists in the ObjectStateManager. The ObjectStateManager cannot track multiple objects with the same key.`« Ceci est produit en raison de la séquence d’événements suivante :

- Le `Edit` les appels de méthode le `ValidateOneAdministratorAssignmentPerInstructor` méthode qui Récupère tous les services qui ont Kim Abercrombie en tant que leur administrateur. À l’origine du service en anglais à lire. Car il s’agit du service en cours de modification, aucune erreur n’est signalée. À la suite de cette opération de lecture, toutefois, l’entité du service en anglais qui a été lu à partir de la base de données est maintenant suivie par le contexte de base de données.
- Le `Edit` méthode tente de définir la `Modified` indicateur sur l’anglais entité de service créée par le classeur de modèles MVC, mais qui échoue, car le contexte suit déjà une entité pour le service en anglais.

Une solution à ce problème consiste à conserver le contexte de suivi des entités en mémoire service récupérées par la requête de validation. Il n’existe aucun inconvénient à effectuer cette opération, car vous ne sont pas à jour cette entité ou de la lecture à nouveau d’une manière qui pourrait tirer parti de celui-ci est mis en mémoire cache.

Dans *DepartmentController.cs*, dans le `ValidateOneAdministratorAssignmentPerInstructor` (méthode), ne spécifiez aucun suivi, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample12.cs?highlight=4)]

Répétez votre tentative de modifier le **Budget** montant d’un service. Cette fois l’opération a réussi, et le site retourne comme prévu pour la page d’Index de services, affichage de la valeur budgétaire révisé.

## <a name="examining-queries-sent-to-the-database"></a>Examen des requêtes envoyées à la base de données

Il est parfois utile de pouvoir voir les requêtes SQL réelles qui sont envoyées à la base de données. Pour ce faire, vous pouvez examiner une variable de requête dans le débogueur, ou appeler la requête `ToString` (méthode). Pour l’essayer, vous examinez une requête simple et observez que se passe-t-il lui lorsque vous ajoutez des options telles eager le chargement, le filtrage et le tri.

Dans *contrôleurs/CourseController*, remplacez le `Index` méthode avec le code suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample13.cs?highlight=3)]

Maintenant définir un point d’arrêt dans *GenericRepository.cs* sur la `return query.ToList();` et `return orderBy(query).ToList();` les instructions de la `Get` (méthode). Exécutez le projet en mode débogage et sélectionnez la page d’Index de cours. Lorsque le code atteint le point d’arrêt, examinez le `query` variable. Vous voyez la requête est envoyée à SQL Server. Il s’agit d’un simple `Select` instruction :

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample14.json)]

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

Requêtes peuvent être trop longs à afficher dans les fenêtres de débogage dans Visual Studio. Pour afficher l’intégralité de la requête, vous pouvez copier la valeur de la variable et collez-le dans un éditeur de texte :

![Copy_value_of_variable_in_debug_mode](https://asp.net/media/2578239/Windows-Live-Writer_Advanced-Entity-Framework-Scenarios-for-_CEF8_Copy_value_of_variable_in_debug_mode_0902a2b1-b799-47a6-9b4b-f266c79d83c1.png)

Vous allez maintenant ajouter une liste déroulante à la page d’Index de cours afin que les utilisateurs peuvent filtrer pour un service particulier. Vous allez trier les cours par titre, et vous devez spécifier un chargement hâtif pour le `Department` propriété de navigation. Dans *CourseController.cs*, remplacez le `Index` méthode avec le code suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample15.cs)]

La méthode reçoit la valeur sélectionnée de la liste déroulante dans le `SelectedDepartment` paramètre. Si rien n’est sélectionné, ce paramètre sera null.

A `SelectList` collection contenant tous les services est passée à la vue pour la liste déroulante. Les paramètres transmis à la `SelectList` constructeur spécifie le nom de champ de valeur, le nom de champ de texte et l’élément sélectionné.

Pour le `Get` méthode de la `Course` référentiel, le code spécifie une expression de filtre, un ordre de tri et eager de chargement pour le `Department` propriété de navigation. L’expression retourne toujours `true` si rien n’est sélectionné dans la liste déroulante (autrement dit, `SelectedDepartment` a la valeur null).

Dans *Views\Course\Index.cshtml*, immédiatement avant l’ouverture `table` , ajoutez le code suivant pour créer un bouton d’envoi et de la liste déroulante :

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample16.cshtml)]

Avec les points d’arrêt toujours définis le `GenericRepository` (classe), exécutez la page d’Index de cours. Poursuivez avec les deux premières fois que le code accède à un point d’arrêt, afin que la page s’affiche dans le navigateur. Sélectionnez un service dans la liste déroulante et cliquez sur **filtre**:

![Course_Index_page_with_department_selected](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

Cette fois, le premier point d’arrêt sera de la requête de services pour la liste déroulante. Qui ignorer et afficher le `query` variable la prochaine fois que le code atteint le point d’arrêt afin de voir ce qui le `Course` maintenant il semble que de la requête. Vous verrez quelque chose comme suit :

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample17.json)]

Vous pouvez voir que la requête est maintenant un `JOIN` requête charge `Department` données avec le `Course` données, et qu’elle inclut un `WHERE` clause.

<a id="proxyclasses"></a>

## <a name="working-with-proxy-classes"></a>Utilisation des Classes de Proxy

Lorsque Entity Framework crée des instances d’entité (par exemple, lorsque vous exécutez une requête), souvent leur création en tant qu’instances d’un type dérivé généré dynamiquement qui agit comme un proxy pour l’entité. Ce proxy substitue certaines propriétés virtuelles de l’entité à insérer des raccordements pour effectuer des actions automatiquement lorsque la propriété est accessible. Par exemple, ce mécanisme est utilisé pour prendre en charge le chargement tardif des relations.

La plupart du temps vous n’avez pas besoin de connaître cette utilisation de proxys, mais il existe des exceptions :

- Dans certains scénarios, vous pouvez choisir d’empêcher des Entity Framework à partir de la création d’instances de proxy. Par exemple, la sérialisation des instances de proxy non peut être plus efficace que la sérialisation des instances de proxy.
- Lorsque vous instanciez une classe d’entité à l’aide du `new` (opérateur), vous n’obtenez pas une instance de proxy. Cela signifie que vous n’obtenez pas des fonctionnalités telles que le suivi des modifications automatique et le chargement différé. Il s’agit généralement d’OK ; Vous ne devez généralement le chargement différé, car vous créez une nouvelle entité qui n’est pas dans la base de données, et vous ne devez généralement pas le suivi des modifications si vous êtes marquer explicitement l’entité en tant que `Added`. Toutefois, si vous avez besoin de chargement différé et que vous avez besoin de suivi des modifications, vous pouvez créer des nouvelles instances d’entité avec les serveurs proxy à l’aide de la `Create` méthode de la `DbSet` classe.
- Vous pouvez souhaiter obtenir un type d’entité réel à partir d’un type de proxy. Vous pouvez utiliser la `GetObjectType` méthode de la `ObjectContext` classe pour obtenir le type d’entité réelle d’une instance de type de proxy.

Pour plus d’informations, consultez [utilisation de serveurs proxy](https://blogs.msdn.com/b/adonet/archive/2011/02/02/using-dbcontext-in-ef-feature-ctp5-part-8-working-with-proxies.aspx) sur le blog de l’équipe Entity Framework.

## <a name="disabling-automatic-detection-of-changes"></a>La désactivation de la détection automatique des modifications

Entity Framework détermine la manière dont une entité a changé (et par conséquent les mises à jour doivent être envoyées à la base de données) en comparant les valeurs en cours d’une entité avec les valeurs d’origine. Les valeurs d’origine sont stockées lorsque l’entité a été interrogée ou attachée. Certaines des méthodes qui provoquent la détection des modifications automatique sont les suivants :

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

Si vous effectuez le suivi d’un grand nombre d’entités et que vous appelez l’une de ces méthodes plusieurs fois dans une boucle, vous pouvez obtenir des améliorations significatives des performances en activant temporairement désactiver la détection de modification automatique à l’aide du [AutoDetectChangesEnabled](https://msdn.microsoft.com/en-us/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled(VS.103).aspx) propriété. Pour plus d’informations, consultez [détecter automatiquement les modifications](https://blogs.msdn.com/b/adonet/archive/2011/02/06/using-dbcontext-in-ef-feature-ctp5-part-12-automatically-detecting-changes.aspx).

## <a name="disabling-validation-when-saving-changes"></a>Désactivation de la Validation lors de l’enregistrement de modifications

Lorsque vous appelez le `SaveChanges` (méthode), par défaut Entity Framework valide les données de toutes les propriétés de toutes les entités modifiées avant la mise à jour de la base de données. Si vous avez mis à jour un grand nombre d’entités et vous avez déjà validé les données, ce travail n’est pas nécessaire et vous pouvez apporter le processus d’enregistrement les modifications en moins de temps à désactiver temporairement la validation. Vous pouvez effectuer ce à l’aide de la [ValidateOnSaveEnabled](https://msdn.microsoft.com/en-us/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled(VS.103).aspx) propriété. Pour plus d’informations, consultez [Validation](https://blogs.msdn.com/b/adonet/archive/2010/12/15/ef-feature-ctp5-validation.aspx).

## <a name="summary"></a>Résumé

Cette étape termine cette série de didacticiels sur l’utilisation d’Entity Framework dans une application ASP.NET MVC. Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).

Pour plus d’informations sur la façon de déployer votre application web, une fois que vous l’avez créé, consultez [ASP.NET Deployment Content Map](https://msdn.microsoft.com/en-us/library/bb386521.aspx) dans MSDN Library.

Pour plus d’informations sur les autres rubriques relatives à MVC, telles que l’authentification et l’autorisation, consultez le [MVC recommandé de ressources](../../getting-started/recommended-resources-for-mvc.md).

<a id="acknowledgments"></a>

## <a name="acknowledgments"></a>Accusés de réception

- Tom Dykstra a écrit la version d’origine de ce didacticiel et est un enregistreur de programmation senior pour l’équipe de contenu d’outils et la plateforme Web Microsoft.
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) coauteur ce didacticiel et n’a plus le travail de mise à jour pour les 5 EF et MVC 4. Rick est un writer de programmation senior pour Microsoft en mettant l’accent sur Azure et MVC.
- [Rowan Miller](http://www.romiller.com) et autres membres de l’équipe d’Entity Framework assistée avec les révisions du code et aidé au débogage de nombreux problèmes avec les migrations qui est survenue pendant que nous avons mise à jour le didacticiel pour EF 5.

## <a name="vb"></a>VB

Lorsque le didacticiel a été généré à l’origine, nous avons fourni versions c# et VB du projet téléchargement terminé. Cette mise à jour, nous fournissons un projet téléchargeable c# pour chaque chapitre faciliter la prise en main n’importe où dans la série, mais en raison de contraintes de temps et d’autres priorités que nous ne l’avez pas que pour VB. Si vous générez un projet VB à l’aide de ces didacticiels et que vous êtes prêt à les partager avec d’autres utilisateurs, n’hésitez pas.

<a id="errors"></a>

## <a name="errors-and-workarounds"></a>Erreurs et des solutions de contournement

### <a name="cannot-createshadow-copy"></a>Ne peut pas créer/ombre copie

Message d’erreur :

*Ne peut pas créer/shadow copy 'DotNetOpenAuth.OpenId' lorsque ce fichier existe déjà.*

Solution :

Attendez quelques secondes et actualisez la page.

### <a name="update-database-not-recognized"></a>Mise à jour de bases de données non reconnu

Message d’erreur :

*Le terme 'Mise à jour de base de données' n’est pas reconnu en tant que le nom de l’applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l’orthographe du nom, ou si un chemin d’accès existe, vérifiez que le chemin d’accès est correct et réessayez.* (À partir de la  *`Update-Database`*  commande PMC.)

Solution :

Quittez Visual Studio. Rouvrez le projet, puis réessayez.

### <a name="validation-failed"></a>Échoué de la validation

Message d’erreur :

*Échec de la validation pour une ou plusieurs entités. Consultez ' entityvalidationerrors ' pour plus de détails.* (À partir de la  *`Update-Database`*  commande PMC.)

Solution :

Une des causes de ce problème est erreurs de validation lorsque le `Seed` s’exécute la méthode. Consultez [Seeding et les bases de données Entity Framework (EF) débogage](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) pour obtenir des conseils sur le débogage du `Seed` (méthode).

### <a name="http-50019-error"></a>HTTP 500.19 erreur

Message d’erreur :

*Erreur HTTP 500.19 - erreur interne au serveur la page demandée est inaccessible, car les données de configuration de la page ne sont pas valides.*

Solution :

Vous pouvez obtenir cette erreur consiste à partir de plusieurs copies de la solution, chacun d’eux à l’aide du même numéro de port. Vous pouvez généralement résoudre ce problème en quitter toutes les instances de Visual Studio, puis redémarrer le projet sur lequel vous travaillez. Si cela ne fonctionne pas, essayez de modifier le numéro de port. Cliquez avec le bouton droit sur le fichier projet, puis sur Propriétés. Sélectionnez le **Web** onglet et modifiez le numéro de port dans le **Url Project** zone de texte.

### <a name="error-locating-sql-server-instance"></a>Instance SQL Server recherche d’erreur

Message d’erreur :

*Une erreur liée au réseau ou d’instance spécifique s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou n’est pas accessible. Vérifiez que le nom d’instance est correct et que SQL Server est configuré pour autoriser les connexions à distance. (fournisseur : Interfaces réseau SQL, erreur : 26 - erreur serveur/de l’Instance spécifiée de localisation)*

Solution :

Vérifiez la chaîne de connexion. Si vous avez supprimé manuellement de la base de données, modifiez le nom de la base de données dans la chaîne de construction.

>[!div class="step-by-step"]
[Précédent](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md)
[Suivant](building-the-ef5-mvc4-chapter-downloads.md)
