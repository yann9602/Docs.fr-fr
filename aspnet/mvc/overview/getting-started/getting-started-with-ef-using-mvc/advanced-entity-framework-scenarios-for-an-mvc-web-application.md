---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: "Advanced scénarios Entity Framework 6 pour une Application Web 5 de MVC (12 12) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/08/2014
ms.topic: article
ms.assetid: f35a9b0c-49ef-4cde-b06d-19d1543feb0b
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: 3d6cc52f7fa3089f30f1a6bbd76593f1eca95009
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="advanced-entity-framework-6-scenarios-for-an-mvc-5-web-application-12-of-12"></a>Scénarios d’infrastructure 6 entité avancées pour une Application Web 5 de MVC (12 12)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans le didacticiel précédent vous implémenté l’héritage table par hiérarchie. Ce didacticiel inclut introduit plusieurs rubriques qui sont utiles à connaître lorsque vous dépassent les principes de base du développement d’applications web ASP.NET qui utilisent Entity Framework Code First. Instructions pas à pas vous guident tout le code et à l’aide de Visual Studio pour les rubriques suivantes :

- [Exécution de requêtes SQL bruts](#rawsql)
- [Exécution de requêtes de suivi non](#notracking)
- [Examen SQL envoyées à la base de données](#sql)

Le didacticiel présente plusieurs rubriques avec brèves introductions suivies des liens vers des ressources pour plus d’informations :

- [Espace de stockage et une unité de travail modèles](#repo)
- [Classes de proxy](#proxies)
- [Détection des modifications automatique](#changedetection)
- [Validation automatique](#validation)
- [Outils EF pour Visual Studio](#tools)
- [Code source de Entity Framework](#source)

Le didacticiel comprend également les sections suivantes :

- [Résumé](#summary)
- [Accusés de réception](#acknowledgments)
- [Une note sur VB](#vb)
- [Les erreurs courantes et solutions ou pour les solutions de contournement](#errors)

Pour la plupart de ces rubriques, vous allez travailler avec des pages que vous avez déjà créé. Pour utiliser SQL brute pour en bloc des mises à jour, vous allez créer une nouvelle page qui met à jour le nombre de crédits de tous les cours dans la base de données :

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

<a id="rawsql"></a>
## <a name="performing-raw-sql-queries"></a>Requêtes SQL brut exécution

L’API de premier Code Entity Framework comprend les méthodes qui vous permettent de transmettre des commandes SQL directement à la base de données. Les options suivantes sont disponibles :

- Utilisez le [DbSet.SqlQuery](https://msdn.microsoft.com/en-us/library/system.data.entity.dbset.sqlquery.aspx) méthode pour les requêtes qui retournent des types d’entités. Les objets retournés doivent être du type attendu par le `DbSet` objet et elles sont suivies automatiquement par le contexte de base de données sauf si vous désactivez le suivi. (Consultez la section suivante le [AsNoTracking](https://msdn.microsoft.com/en-us/library/system.data.entity.dbextensions.asnotracking.aspx) méthode.)
- Utilisez le [Database.SqlQuery](https://msdn.microsoft.com/en-us/library/system.data.entity.database.sqlquery.aspx) méthode pour les requêtes qui retournent des types qui ne sont pas des entités. Les données renvoyées n’est pas suivies par le contexte de la base de données, même si vous utilisez cette méthode pour récupérer des types d’entités.
- Utilisez le [Database.ExecuteSqlCommand](https://msdn.microsoft.com/en-us/library/gg679456.aspx) pour les commandes de requête non.

Un des avantages de l’utilisation d’Entity Framework est qu’elle évite de lier votre code trop près d’une méthode particulière du stockage des données. Cela en générant des requêtes et commandes SQL pour vous, ce qui vous évite de les écrire. Il existe des scénarios exceptionnelles lorsque vous avez besoin exécuter des requêtes SQL spécifiques que vous avez créé manuellement, mais ces méthodes permettent de gérer ces exceptions.

Comme étant toujours true lorsque vous exécutez des commandes SQL dans une application web, vous devez prendre des précautions pour protéger votre site contre les attaques par injection SQL. Une manière de procéder consiste à utiliser des requêtes paramétrables pour vous assurer que les chaînes soumis par une page web ne peut pas être interprétées comme des commandes SQL. Dans ce didacticiel, vous utiliserez des requêtes paramétrables lors de l’intégration de l’entrée d’utilisateur dans une requête.

### <a name="calling-a-query-that-returns-entities"></a>Appel d’une requête qui retourne des entités

Le [DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/en-us/library/gg696460.aspx) classe fournit une méthode que vous pouvez utiliser pour exécuter une requête qui retourne une entité de type `TEntity`. Pour voir comment cela fonctionne vous allez modifier le code dans le `Details` méthode de la `Department` contrôleur.

Dans *DepartmentController.cs*, dans le `Details` (méthode), remplacez le `db.Departments.FindAsync` appel de méthode avec un `db.Departments.SqlQuery` appel de méthode, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

Pour vérifier que le nouveau code fonctionne correctement, sélectionnez le **départements** onglet, puis **détails** pour un des services.

![Informations relatives au service](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image2.png)

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>Appel d’une requête qui retourne les autres Types d’objets

Précédemment, vous avez créé une grille des statistiques de student pour la page à propos montrant le nombre d’élèves pour chaque date d’inscription. Le code qui effectue l’opération dans *HomeController.cs* utilise LINQ :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

Supposons que vous souhaitez écrire le code qui extrait ces données directement dans SQL au lieu d’utiliser LINQ. Pour faire que vous devez exécuter une requête qui retourne une valeur autre que les objets d’entité, ce qui signifie que vous devez utiliser le [Database.SqlQuery](https://msdn.microsoft.com/en-us/library/system.data.entity.database.sqlquery(v=VS.103).aspx) (méthode).

Dans *HomeController.cs*, remplacez l’instruction LINQ dans le `About` méthode avec une instruction SQL, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

Exécutez la page à propos de. Il affiche les mêmes données qu’auparavant.

![About_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image3.png)

### <a name="calling-an-update-query"></a>Appel d’une requête de mise à jour

Supposons que les administrateurs de Contoso University veulent être en mesure d’effectuer des modifications en bloc dans la base de données, telles que la modification du nombre de crédits pour chaque cours. Si l’université comporte un grand nombre des cours, il est inefficace pour les extraire sous la forme d’entités et les modifier individuellement. Dans cette section, vous allez implémenter une page web qui permet à l’utilisateur spécifier un facteur permettant de modifier le nombre de crédits pour tous les cours, et vous devez apporter la modification en exécutant un SQL `UPDATE` instruction. La page web doit ressembler à l’illustration suivante :

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image4.png)

Dans *CourseContoller.cs*, ajoutez `UpdateCourseCredits` méthodes pour `HttpGet` et `HttpPost`:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

Lorsque le contrôleur traite un `HttpGet` demande, rien n’est retourné dans le `ViewBag.RowsAffected` variable et la vue affiche une zone de texte vide et un bouton d’envoi, comme indiqué dans l’illustration précédente.

Lorsque le **mise à jour** bouton est activé, le `HttpPost` méthode est appelée, et `multiplier` a la valeur entrée dans la zone de texte. Le code exécute ensuite l’instruction SQL qui met à jour des cours et retourne le nombre de lignes affectées à la vue dans le `ViewBag.RowsAffected` variable. Lorsque l’affichage Obtient une valeur dans cette variable, elle affiche le nombre de lignes mises à jour au lieu de la zone de texte et soumettre, comme indiqué dans l’illustration suivante :

![Update_Course_Credits_rows_affected_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image5.png)

Dans *CourseController.cs*, avec le bouton droit de la `UpdateCourseCredits` méthodes, puis cliquez sur **ajouter une vue**.

![Add_View_dialog_box_for_Update_Course_Credits](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image6.png)

Dans *Views\Course\UpdateCourseCredits.cshtml*, remplacez le code de modèle par le code suivant :

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

Exécuter la `UpdateCourseCredits` méthode en sélectionnant le **cours** onglet, puis en ajoutant « / UpdateCourseCredits » à la fin de l’URL dans la barre d’adresses du navigateur (par exemple : `http://localhost:50205/Course/UpdateCourseCredits`). Entrez un nombre dans la zone de texte :

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image7.png)

Cliquez sur **Mettre à jour**. Vous voyez le nombre de lignes affectées :

![Update_Course_Credits_rows_affected_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image8.png)

Cliquez sur **retour à la liste** pour afficher la liste des cours avec la modification du numéro de crédits.

![Courses_Index_page_showing_revised_credits](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image9.png)

Pour plus d’informations sur les requêtes SQL brutes, consultez [des requêtes SQL brutes](https://msdn.microsoft.com/en-us/data/jj592907) sur MSDN.

<a id="notracking"></a>
## <a name="no-tracking-queries"></a>Aucun suivi des requêtes

Lorsqu’un contexte de base de données récupère les lignes de la table et crée des objets d’entité qui représentent les, par défaut il effectue le suivi de si les entités en mémoire sont synchronisées avec ce qui figure dans la base de données. Les données en mémoire agit comme un cache et sont utilisées lorsque vous mettez à jour une entité. Cette mise en cache est souvent inutile dans une application web, car les instances de contexte sont généralement de courte durée (une nouvelle est créé et supprimé pour chaque demande) et le contexte qui lit une entité est généralement supprimée avant que cette entité est utilisée à nouveau.

Vous pouvez désactiver le suivi des objets d’entité dans la mémoire à l’aide de la [AsNoTracking](https://msdn.microsoft.com/en-us/library/gg679352(v=vs.103).aspx) (méthode). Scénarios classiques dans lesquels vous souhaiterez faire qui sont les suivantes :

- Une requête récupère un gros volume de données que la désactivation du suivi peuvent améliorer sensiblement les performances.
- Vous souhaitez attacher une entité afin de mettre à jour, mais vous avez précédemment récupéré à la même entité à des fins différentes. Étant donné que l’entité est déjà suivie par le contexte de base de données, vous ne peut pas joindre l’entité que vous souhaitez modifier. Une façon de gérer cette situation consiste à utiliser le `AsNoTracking` option avec la requête précédente.

Pour obtenir un exemple qui montre comment utiliser le [AsNoTracking](https://msdn.microsoft.com/en-us/library/gg679352(v=vs.103).aspx) méthode, consultez [la version antérieure de ce didacticiel](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md). Cette version du didacticiel ne définit pas l’indicateur modifié sur une entité de modèle de classeur créé dans la méthode de modification, donc il n’a pas besoin `AsNoTracking`.

<a id="sql"></a>
## <a name="examining-sql-sent-to-the-database"></a>Examen SQL envoyées à la base de données

Il est parfois utile de pouvoir voir les requêtes SQL réelles qui sont envoyées à la base de données. Dans un didacticiel antérieur vous avez vu comment procéder dans le code de l’intercepteur. vous allez maintenant apprendre quelques méthodes permettant de le faire sans écrire de code de l’intercepteur. Pour l’essayer, vous examinez une requête simple et observez que se passe-t-il lui lorsque vous ajoutez des options telles eager le chargement, le filtrage et le tri.

Dans *contrôleurs/CourseController*, remplacez le `Index` méthode avec le code suivant, pour arrêter provisoirement la chargement hâtif :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

Maintenant définir un point d’arrêt sur la `return` instruction (F9 avec le curseur sur cette ligne). Appuyez sur F5 pour exécuter le projet en mode débogage, puis sélectionnez la page d’Index de cours. Lorsque le code atteint le point d’arrêt, examinez le `sql` variable. Vous voyez la requête est envoyée à SQL Server. Il s’agit d’un simple `Select` instruction.

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

Cliquez sur la classe loupe pour afficher la requête dans le **visualiseur de texte**.

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

Vous allez maintenant ajouter une liste déroulante à la page d’Index de cours afin que les utilisateurs peuvent filtrer pour un service particulier. Vous allez trier les cours par titre, et vous devez spécifier un chargement hâtif pour le `Department` propriété de navigation.

Dans *CourseController.cs*, remplacez le `Index` méthode avec le code suivant :

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

Restaurer le point d’arrêt sur la `return` instruction.

La méthode reçoit la valeur sélectionnée de la liste déroulante dans le `SelectedDepartment` paramètre. Si rien n’est sélectionné, ce paramètre sera null.

A `SelectList` collection contenant tous les services est passée à la vue pour la liste déroulante. Les paramètres transmis à la `SelectList` constructeur spécifie le nom de champ de valeur, le nom de champ de texte et l’élément sélectionné.

Pour le `Get` méthode de la `Course` référentiel, le code spécifie une expression de filtre, un ordre de tri et eager de chargement pour le `Department` propriété de navigation. L’expression retourne toujours `true` si rien n’est sélectionné dans la liste déroulante (autrement dit, `SelectedDepartment` a la valeur null).

Dans *Views\Course\Index.cshtml*, immédiatement avant l’ouverture `table` , ajoutez le code suivant pour créer un bouton d’envoi et de la liste déroulante :

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

Avec le point d’arrêt toujours définie, exécutez la page d’Index de cours. Continuez jusqu'à les première fois que le code accède à un point d’arrêt, afin que la page s’affiche dans le navigateur. Sélectionnez un service dans la liste déroulante et cliquez sur **filtre**:

![Course_Index_page_with_department_selected](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image11.png)

Cette fois, le premier point d’arrêt sera de la requête de services pour la liste déroulante. Qui ignorer et afficher le `query` variable la prochaine fois que le code atteint le point d’arrêt afin de voir ce qui le `Course` maintenant il semble que de la requête. Vous verrez quelque chose comme suit :

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

Vous pouvez voir que la requête est maintenant un `JOIN` requête charge `Department` données avec le `Course` données, et qu’elle inclut un `WHERE` clause.

Supprimer le `var sql = courses.ToString()` ligne.

<a id="repo"></a>

## <a name="repository-and-unit-of-work-patterns"></a>Espace de stockage et une unité de travail modèles

De nombreux développeurs écrire du code pour implémenter le référentiel et une unité de travail modèles comme un wrapper autour du code qui fonctionne avec Entity Framework. Ces modèles sont destinés à créer une couche d’abstraction entre la couche d’accès aux données et la couche de logique métier d’une application. Implémentation de ces modèles peut permettre d’isoler votre application des modifications dans le magasin de données et peuvent faciliter le test unitaire automatisé ou développement piloté par test (TDD). Toutefois, l’écriture de code supplémentaire pour implémenter ces modèles n’est pas toujours le meilleur choix pour les applications qui utilisent EF, pour plusieurs raisons :

- La classe de contexte EF lui-même isole votre code à partir du code spécifique au magasin de données.
- La classe de contexte EF peut agir comme une classe de l’unité de travail de base de données mises à jour de procéder à l’aide de EF.
- Fonctionnalités introduites dans Entity Framework 6 rendent plus facile à implémenter TDD sans écrire de code de référentiel.

Pour plus d’informations sur la façon d’implémenter le référentiel et une unité de travail des modèles, consultez [la version d’Entity Framework 5 de cette série de didacticiels](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md). Pour plus d’informations sur les façons d’implémenter TDD dans Entity Framework 6, consultez les ressources suivantes :

- [Comment EF6 Active Mocking DbSets plus facilement.](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [Test avec une infrastructure mocking](https://msdn.microsoft.com/en-us/data/dn314429)
- [Test avec votre propre doubles de test](https://msdn.microsoft.com/en-us/data/dn314431)

<a id="proxies"></a>
## <a name="proxy-classes"></a>Classes de proxy

Lorsque Entity Framework crée des instances d’entité (par exemple, lorsque vous exécutez une requête), souvent leur création en tant qu’instances d’un type dérivé généré dynamiquement qui agit comme un proxy pour l’entité. Par exemple, consultez les deux images suivantes de débogueur. Dans la première image, vous constatez que la `student` variable est attendu `Student` immédiatement après l’instanciation de l’entité de type. L’image de la deuxième fois EF a été utilisé pour lire une entité étudiant à partir de la base de données s’affichent dans la classe proxy.

![Avant de classe proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![Après la classe de proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

Cette classe proxy substitue certaines propriétés virtuelles de l’entité à insérer des raccordements pour effectuer des actions automatiquement lorsque la propriété est accessible. Une fonction de pour que ce mécanisme est utilisé est chargement différé.

La plupart du temps vous n’avez pas besoin de connaître cette utilisation de proxys, mais il existe des exceptions :

- Dans certains scénarios, vous pouvez choisir d’empêcher des Entity Framework à partir de la création d’instances de proxy. Par exemple, lorsque vous sérialisez les entités vous voulez généralement les classes POCO, pas les classes proxy. Une façon d’éviter les problèmes de sérialisation consiste à sérialiser des objets de transfert de données (DTO) au lieu d’objets d’entité, comme indiqué dans le [à l’aide des API Web avec Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md) didacticiel. Une autre méthode consiste à [désactiver la création du proxy](https://msdn.microsoft.com/en-US/data/jj592886.aspx).
- Lorsque vous instanciez une classe d’entité à l’aide du `new` (opérateur), vous n’obtenez pas une instance de proxy. Cela signifie que vous n’obtenez pas des fonctionnalités telles que le suivi des modifications automatique et le chargement différé. Il s’agit généralement d’OK ; Vous ne devez généralement le chargement différé, car vous créez une nouvelle entité qui n’est pas dans la base de données, et vous ne devez généralement pas le suivi des modifications si vous êtes marquer explicitement l’entité en tant que `Added`. Toutefois, si vous avez besoin de chargement différé et que vous avez besoin de suivi des modifications, vous pouvez créer des nouvelles instances d’entité avec les serveurs proxy à l’aide de la [créer](https://msdn.microsoft.com/en-us/library/gg679504.aspx) méthode de la `DbSet` classe.
- Vous pouvez souhaiter obtenir un type d’entité réel à partir d’un type de proxy. Vous pouvez utiliser la [GetObjectType a](https://msdn.microsoft.com/en-us/library/system.data.objects.objectcontext.getobjecttype.aspx) méthode de la `ObjectContext` classe pour obtenir le type d’entité réelle d’une instance de type de proxy.

Pour plus d’informations, consultez [utilisation de serveurs proxy](https://msdn.microsoft.com/en-us/data/JJ592886.aspx) sur MSDN.

<a id="changedetection"></a>
## <a name="automatic-change-detection"></a>Détection des modifications automatique

Entity Framework détermine la manière dont une entité a changé (et par conséquent les mises à jour doivent être envoyées à la base de données) en comparant les valeurs en cours d’une entité avec les valeurs d’origine. Les valeurs d’origine sont stockées lorsque l’entité est interrogée ou attachée. Certaines des méthodes qui provoquent la détection des modifications automatique sont les suivants :

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

Si vous effectuez le suivi d’un grand nombre d’entités et que vous appelez l’une de ces méthodes plusieurs fois dans une boucle, vous pouvez obtenir des améliorations significatives des performances en activant temporairement désactiver la détection de modification automatique à l’aide du [AutoDetectChangesEnabled](https://msdn.microsoft.com/en-us/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx) propriété. Pour plus d’informations, consultez [détecter automatiquement les modifications](https://msdn.microsoft.com/en-us/data/jj556205) sur MSDN.

<a id="validation"></a>
## <a name="automatic-validation"></a>Validation automatique

Lorsque vous appelez le `SaveChanges` (méthode), par défaut Entity Framework valide les données de toutes les propriétés de toutes les entités modifiées avant la mise à jour de la base de données. Si vous avez mis à jour un grand nombre d’entités et vous avez déjà validé les données, ce travail n’est pas nécessaire et vous pouvez apporter le processus d’enregistrement les modifications en moins de temps à désactiver temporairement la validation. Vous pouvez effectuer ce à l’aide de la [ValidateOnSaveEnabled](https://msdn.microsoft.com/en-us/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx) propriété. Pour plus d’informations, consultez [Validation](https://msdn.microsoft.com/en-us/data/gg193959) sur MSDN.

<a id="tools"></a>
## <a name="entity-framework-power-tools"></a>Entity Framework Power Tools

[Entity Framework Power Tools](https://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d) est un complément Visual Studio qui a été utilisé pour créer des diagrammes de modèle de données affiché dans ces didacticiels. Les outils peuvent également effectuer des fonctions, telles que de générer des classes d’entité basée sur les tables dans une base de données afin que vous pouvez utiliser la base de données Code First. Après avoir installé les outils, des options supplémentaires s’affichent dans les menus contextuels. Par exemple, lorsque vous cliquez sur votre classe de contexte de **l’Explorateur de solutions**, vous obtenez une option pour générer un diagramme. Lorsque vous utilisez Code First, vous ne pouvez pas modifier le modèle de données dans le diagramme, mais vous pouvez déplacer des éléments pour le rendre plus facile à comprendre.

![EF dans le menu contextuel](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image14.png)

![Diagramme EF](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

<a id="source"></a>
## <a name="entity-framework-source-code"></a>Code source de Entity Framework

Le code source pour Entity Framework 6 est disponible à l’adresse [GitHub](https://github.com/aspnet/EntityFramework6). Vous pouvez signaler des bogues, et vous pouvez contribuer à vos propres améliorations au code source EF.

Bien que le code source est ouvert, Entity Framework est entièrement pris en charge comme un produit Microsoft. L’équipe Microsoft Entity Framework conserve le contrôle sur lequel les contributions sont acceptées et teste toutes les modifications du code pour garantir la qualité de chaque version.

<a id="summary"></a>
## <a name="summary"></a>Résumé

Cette étape termine cette série de didacticiels sur l’utilisation d’Entity Framework dans une application ASP.NET MVC. Pour plus d’informations sur l’utilisation des données à l’aide d’Entity Framework, consultez le [page EF de la documentation sur MSDN](https://msdn.microsoft.com/en-us/data/ee712907) et [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

Pour plus d’informations sur la façon de déployer votre application web, une fois que vous l’avez créé, consultez [le déploiement Web ASP.NET - ressources recommandées](../../../../whitepapers/aspnet-web-deployment-content-map.md) dans MSDN Library.

Pour plus d’informations sur les autres rubriques relatives à MVC, telles que l’authentification et l’autorisation, consultez le [ASP.NET MVC - ressources recommandées](../recommended-resources-for-mvc.md).

<a id="acknowledgments"></a>
## <a name="acknowledgments"></a>Accusés de réception

- Tom Dykstra a écrit la version d’origine de ce didacticiel, coauteur la mise à jour EF 5 et écrit la mise à jour EF 6. Tom est un enregistreur de programmation senior pour l’équipe de contenu d’outils et la plateforme Web Microsoft.
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) n’a la plupart du travail de mise à jour du didacticiel EF 5 et MVC 4 et coauteur la mise à jour EF 6. Rick est un writer de programmation senior pour Microsoft en mettant l’accent sur Azure et MVC.
- [Rowan Miller](http://www.romiller.com) et autres membres de l’équipe d’Entity Framework assistée avec les révisions du code et aidé au débogage de nombreux problèmes avec les migrations qui est survenue pendant que nous avons mise à jour le didacticiel pour EF 5 et 6 de EF.

<a id="vb"></a>
## <a name="vb"></a>VB

Lorsque le didacticiel a été généré à l’origine pour EF 4.1, nous avons fourni versions c# et VB du projet téléchargement terminé. En raison de contraintes de temps et d’autres priorités nous n'avons pas effectué cette opération pour cette version. Si vous générez un projet VB à l’aide de ces didacticiels et que vous êtes prêt à les partager avec d’autres utilisateurs, n’hésitez pas.

<a id="errors"></a>
## <a name="common-errors-and-solutions-or-workarounds-for-them"></a>Les erreurs courantes et solutions ou pour les solutions de contournement

### <a name="cannot-createshadow-copy"></a>Ne peut pas créer/ombre copie

Message d’erreur :

> Ne peut pas créer/shadow copy '&lt;nom de fichier&gt;' lorsque ce fichier existe déjà.


Solution

Attendez quelques secondes et actualisez la page.

### <a name="update-database-not-recognized"></a>Mise à jour de bases de données non reconnu

Message d’erreur (à partir de la `Update-Database` commande PMC) :

> Le terme 'Mise à jour de base de données' n’est pas reconnu en tant que le nom de l’applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l’orthographe du nom, ou si un chemin d’accès existe, vérifiez que le chemin d’accès est correct et réessayez.


Solution

Quittez Visual Studio. Rouvrez le projet, puis réessayez.

### <a name="validation-failed"></a>Échoué de la validation

Message d’erreur (à partir de la `Update-Database` commande PMC) :

> Échec de la validation pour une ou plusieurs entités. Consultez ' entityvalidationerrors ' pour plus de détails.


Solution

Une des causes de ce problème est erreurs de validation lorsque le `Seed` s’exécute la méthode. Consultez [Seeding et les bases de données Entity Framework (EF) débogage](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) pour obtenir des conseils sur le débogage du `Seed` (méthode).

### <a name="http-50019-error"></a>HTTP 500.19 erreur

Message d’erreur :

> Erreur HTTP 500.19 - erreur interne au serveur  
> Ne peut pas accéder à la page demandée, car les données de configuration de la page ne sont pas valides.


Solution

Vous pouvez obtenir cette erreur consiste à partir de plusieurs copies de la solution, chacun d’eux à l’aide du même numéro de port. Vous pouvez généralement résoudre ce problème en quittant toutes les instances de Visual Studio, puis redémarrer le projet sur lequel vous travaillez. Si cela ne fonctionne pas, essayez de modifier le numéro de port. Cliquez avec le bouton droit sur le fichier projet, puis sur Propriétés. Sélectionnez le **Web** onglet et modifiez le numéro de port dans le **Url Project** zone de texte.

### <a name="error-locating-sql-server-instance"></a>Instance SQL Server recherche d’erreur

Message d’erreur :

> Une erreur liée au réseau ou d’instance spécifique s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou n’est pas accessible. Vérifiez que le nom de l’instance est correct et que SQL Server est configuré pour autoriser les connexions distantes. (fournisseur : Interfaces réseau SQL, erreur : 26 - erreur serveur/de l’Instance spécifiée de localisation)


Solution

Vérifiez la chaîne de connexion. Si vous avez supprimé manuellement de la base de données, modifiez le nom de la base de données dans la chaîne de construction.

>[!div class="step-by-step"]
[Précédent](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)
