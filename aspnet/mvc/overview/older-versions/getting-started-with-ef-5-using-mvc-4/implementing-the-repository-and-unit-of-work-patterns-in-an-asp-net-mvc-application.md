---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application
title: "Implémentation du référentiel et une unité de travail des modèles dans une Application ASP.NET MVC (9 10) | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 44761193-04ba-4990-9f90-145d3c10a716
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: c920dc8defe18b6f27d122c2cd1a6c6ffdaad608
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="implementing-the-repository-and-unit-of-work-patterns-in-an-aspnet-mvc-application-9-of-10"></a>Implémentation du référentiel et une unité de travail des modèles dans une Application ASP.NET MVC (9 sur 10)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Vous pouvez démarrer la série de didacticiels à partir du début ou [télécharger un projet de démarrage pour ce chapitre](building-the-ef5-mvc4-chapter-downloads.md) et Démarrer ici.
> 
> > [!NOTE] 
> > 
> > Si vous rencontrez un problème que vous ne pouvez pas résoudre, [télécharger le chapitre terminé](building-the-ef5-mvc4-chapter-downloads.md) et essayez de reproduire le problème. En règle générale, vous trouverez la solution au problème en comparant votre code pour le code terminé. Pour certaines erreurs courantes et comment les résoudre, consultez [erreurs et des solutions de contournement.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


Dans le didacticiel précédent, vous avez utilisé l’héritage pour réduire le code redondant dans le `Student` et `Instructor` des classes d’entité. Dans ce didacticiel, vous verrez plusieurs façons d’utiliser le référentiel et une unité de travail des modèles pour les opérations. Comme dans le didacticiel précédent, dans celle-ci, vous allez modifier la façon dont votre code fonctionne avec des pages vous déjà créée plutôt que de créer de nouvelles pages.

## <a name="the-repository-and-unit-of-work-patterns"></a>Le référentiel et une unité de travail modèles

Le référentiel et une unité de travail modèles sont destinés à créer une couche d’abstraction entre la couche d’accès aux données et la couche de logique métier d’une application. Implémentation de ces modèles peut permettre d’isoler votre application des modifications dans le magasin de données et peuvent faciliter le test unitaire automatisé ou développement piloté par test (TDD).

Dans ce didacticiel, vous allez implémenter une classe de référentiel pour chaque type d’entité. Pour le `Student` type d’entité vous allez créer une interface de référentiel et une classe de référentiel. Lorsque vous instanciez l’espace de stockage dans votre contrôleur, vous allez utiliser l’interface afin que le contrôleur accepte une référence à un objet qui implémente l’interface de référentiel. Lorsque le contrôleur s’exécute sous un serveur web, il reçoit un référentiel qui fonctionne avec Entity Framework. Lorsque le contrôleur s’exécute sous une classe de test unitaire, il reçoit un référentiel qui fonctionne avec les données stockées d’une manière qui vous pouvez de manipuler facilement pour le test, par exemple une collection en mémoire.

Plus loin dans ce didacticiel, vous allez utiliser plusieurs référentiels et une classe d’unité de travail pour le `Course` et `Department` types d’entité de la `Course` contrôleur. La classe d’unité de travail coordonne le travail de plusieurs référentiels en créant une classe de contexte de base de données unique partagée par tous les. Si vous souhaitez être en mesure d’effectuer le test unitaire automatisé, vous serez créer et utiliser des interfaces pour ces classes de la même façon que vous l’avez fait pour le `Student` référentiel. Toutefois, pour simplifier le didacticiel, vous allez créer et utiliser ces classes sans interfaces.

L’illustration suivante montre une façon conceptualiser les relations entre le contrôleur et les classes de contexte par rapport aux ne pas utiliser du tout le référentiel ou une unité de travail modèle.

![Repository_pattern_diagram](https://asp.net/media/2578149/Windows-Live-Writer_8c4963ba1fa3_CE3B_Repository_pattern_diagram_1df790d3-bdf2-4c11-9098-946ddd9cd884.png)

Vous ne créez des tests unitaires dans cette série de didacticiels. Pour obtenir une présentation TDD avec une application MVC qui utilise le modèle de référentiel, consultez [procédure pas à pas : utilisation de TDD avec ASP.NET MVC](https://msdn.microsoft.com/en-us/library/ff847525.aspx). Pour plus d’informations sur le modèle de référentiel, consultez les ressources suivantes :

- [Le modèle de référentiel](https://msdn.microsoft.com/en-us/library/ff649690.aspx) sur MSDN.
- [À l’aide de modèles de référentiel et l’unité de travail avec Entity Framework 4.0](https://blogs.msdn.com/b/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx) sur le blog de l’équipe Entity Framework.
- [Agile Entity Framework 4 référentiel](http://thedatafarm.com/blog/data-access/agile-entity-framework-4-repository-part-1-model-and-poco-classes/) séries de billets sur le blog de Julie Lerman.
- [Création du compte à une Application de HTML5/jQuery aperçu](https://weblogs.asp.net/dwahlin/archive/2011/08/15/building-the-account-at-a-glance-html5-jquery-application.aspx) sur le blog de Wahlin.

> [!NOTE]
> Il existe plusieurs façons d’implémenter le référentiel et une unité de travail des modèles. Vous pouvez utiliser les classes de référentiel avec ou sans une classe d’unité de travail. Vous pouvez implémenter un référentiel unique pour tous les types d’entité ou une pour chaque type. Si vous implémentez un pour chaque type, vous pouvez utiliser des classes séparées, une classe de base générique et des classes dérivées, ou une classe de base abstraite et classes dérivées. Vous pouvez inclure une logique métier dans votre référentiel ou limiter à la logique d’accès aux données. Vous pouvez également créer une couche d’abstraction dans votre classe de contexte de base de données à l’aide de [IDbSet](https://msdn.microsoft.com/en-us/library/gg679233(v=vs.103).aspx) interfaces il au lieu de [DbSet](https://msdn.microsoft.com/en-us/library/system.data.entity.dbset(v=vs.103).aspx) types pour vos jeux d’entités. L’approche de mise en œuvre d’une couche d’abstraction dans ce didacticiel est une option vous permettant de prendre en compte, pas une recommandation pour tous les scénarios et les environnements.


## <a name="creating-the-student-repository-class"></a>Création de la classe de référentiel étudiant

Dans le *DAL* dossier, créez un fichier de classe nommé *IStudentRepository.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample1.cs)]

Ce code déclare un ensemble typique de méthodes CRUD, y compris les deux méthodes de lecture, qui retourne tous les `Student` entités et une qui recherche une seule `Student` entité par ID.

Dans le *DAL* dossier, créez un fichier de classe nommé *StudentRepository.cs* fichier. Remplacez le code existant par le code suivant, qui implémente le `IStudentRepository` interface :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample2.cs)]

Le contexte de base de données est défini dans une variable de classe, et le constructeur attend l’objet appelant pour passer une instance du contexte :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample3.cs)]

Vous pouviez instancier un nouveau contexte dans le référentiel, puis si vous avez utilisé plusieurs référentiels dans un seul contrôleur, chacun retrouviez avec un contexte distinct. Ultérieurement, vous allez utiliser plusieurs référentiels dans les `Course` contrôleur et vous verrez comment une classe d’unité de travail peut garantir que tous les référentiels utilisent le même contexte.

Le référentiel implémente [IDisposable](https://msdn.microsoft.com/en-us/library/system.idisposable.aspx) et supprime le contexte de base de données comme vous l’avez vu plus haut dans le contrôleur, et ses méthodes CRUD effectuent des appels dans le contexte de base de données de la même façon que vous avez vu plus haut.

## <a name="change-the-student-controller-to-use-the-repository"></a>Modifier le contrôleur de l’étudiant pour utiliser l’espace de stockage

Dans *StudentController.cs*, remplacez le code actuellement dans la classe par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=13-18,44,75,77,102-103,120,137-138,159,172-174,186)]

Le contrôleur déclare maintenant une variable de classe pour un objet qui implémente le `IStudentRepository` interface au lieu de la classe de contexte :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample5.cs)]

Le constructeur (sans paramètre) par défaut crée une nouvelle instance de contexte et un constructeur facultatif permet à l’appelant de passer une instance de contexte.

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample6.cs)]

(Si vous utilisiez *injection de dépendance*, ou l’injection de dépendance, vous n’avez pas besoin du constructeur par défaut, car le logiciel DI garantir que l’objet de référentiel correct est toujours fourni.)

Dans les méthodes CRUD, le référentiel est désormais appelé à la place du contexte :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample7.cs)]

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample8.cs)]

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample9.cs)]

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample10.cs)]

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample11.cs)]

Et le `Dispose` méthode supprime maintenant le référentiel à la place du contexte :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample12.cs)]

Exécuter le site, puis cliquez sur le **étudiants** onglet.

![Students_Index_page](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/_static/image1.png)

La page recherche et fonctionne comme il le faisait avant que vous avez modifié le code pour utiliser l’espace de stockage, et les autres pages de l’étudiant également fonctionnent de la même. Toutefois, il existe une différence importante près de la façon la `Index` fait de la méthode du contrôleur de filtrage et de classement. La version d’origine de cette méthode contenue le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample13.cs?highlight=1)]

La mise à jour `Index` méthode contient le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample14.cs?highlight=1)]

Seul le code en surbrillance a changé.

Dans la version d’origine du code, `students` est typé comme un `IQueryable` objet. La requête n’est pas envoyée à la base de données jusqu'à ce qu’il est converti en une collection à l’aide d’une méthode comme `ToList`, qui ne se produit pas jusqu'à ce que la vue Index accède au modèle d’étudiant. Le `Where` méthode dans le code d’origine ci-dessus devient un `WHERE` clause dans la requête SQL qui est envoyée à la base de données. À son tour, cela signifie que seules les entités sélectionnées sont retournées par la base de données. Toutefois, suite à la modification `context.Students` à `studentRepository.GetStudents()`, le `students` variable après cette instruction est une `IEnumerable` collection qui contient tous les étudiants dans la base de données. Le résultat final de l’application de la `Where` méthode est la même, mais maintenant le travail est effectué dans la mémoire sur le serveur web et non par la base de données. Pour les requêtes qui retournent des volumes importants de données, cela peut être inefficace.

> [!TIP] 
> 
> **IQueryable vs. IEnumerable**
> 
> Après avoir implémenté le référentiel comme illustré ici, même si vous entrez un élément dans le **recherche** zone de la requête envoyée à SQL Server renvoie toutes les lignes de Student, car elle n’inclut pas les critères de recherche :
> 
> ![](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/_static/image2.png)
> 
> [!code-sql[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample15.sql)]
> 
> Cette requête retourne toutes les données de l’étudiant, car le référentiel d’exécution de la requête sans connaître les critères de recherche. Le processus de tri, l’application des critères de recherche et sélection d’un sous-ensemble des données pour la pagination (affichant les lignes uniquement 3 dans ce cas) est effectuée dans la mémoire ultérieurement, lorsque le `ToPagedList` méthode est appelée sur le `IEnumerable` collection.
> 
> Dans la version précédente du code (avant d’appliquer le référentiel), la requête n’est pas envoyée à la base de données jusqu'à ce qu’après avoir appliqué les critères de recherche lorsque `ToPagedList` est appelée sur le `IQueryable` objet.
> 
> ![](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/_static/image3.png)
> 
> Lorsque ToPagedList est appelée sur une `IQueryable` de l’objet, la requête envoyée à SQL Server spécifie la chaîne de recherche, ainsi que les lignes qui répondent aux critères de recherche sont retournés, et aucun filtrage ne doit être effectué dans la mémoire.
> 
> [!code-sql[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample16.sql)]
> 
> (Le didacticiel suivant explique comment examiner les requêtes envoyées à SQL Server)


La section suivante montre comment implémenter les méthodes de référentiel qui vous permettent de spécifier que ce travail doit être effectué par la base de données.

Vous venez de créer une couche d’abstraction entre le contrôleur et le contexte de base de données Entity Framework. Si vous souhaitez effectuer automatisée de tests unitaires avec cette application, vous pouvez créer une classe de remplacement de référentiel dans un projet de test unitaire qui implémente `IStudentRepository` *.* Au lieu d’appeler le contexte pour lire et écrire des données, cette classe de données de référentiel fictive peut manipuler des collections en mémoire afin de tester les fonctions du contrôleur.

## <a name="implement-a-generic-repository-and-a-unit-of-work-class"></a>Implémenter un référentiel générique et une classe d’unité de travail

Création d’une classe de référentiel pour chaque type d’entité qui peut entraîner un grand nombre de code redondant, et peuvent être mises à jour partielles. Par exemple, supposons que vous devez mettre à jour les deux différents types d’entité dans le cadre de la même transaction. Si chacun utilise une instance de contexte de base de données distincte, un peut réussir et l’autre peut échouer. Un moyen de réduire le code redondant consiste à utiliser un référentiel générique et pour vous assurer que tous les référentiels utilisent le même contexte de base de données (et donc coordonnent les mises à jour) consiste à utiliser une classe d’unité de travail.

Dans cette section du didacticiel, vous allez créer un `GenericRepository` classe et un `UnitOfWork` classe et les utiliser dans le `Course` contrôleur pour accéder à la fois à la `Department` et le `Course` jeux d’entités. Comme expliqué précédemment, pour simplifier cette partie du didacticiel, vous ne sont pas Création d’interfaces pour ces classes. Mais si vous souhaitez les utiliser pour faciliter le TDD, serait généralement les implémenter avec les interfaces de la même façon que vous l’avez fait la `Student` référentiel.

### <a name="create-a-generic-repository"></a>Créer un référentiel générique

Dans le *DAL* dossier, créez *GenericRepository.cs* et remplacez le code existant par le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample17.cs)]

Variables de classe sont déclarés pour le contexte de base de données et le jeu d’entités qui le référentiel est instancié pour :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample18.cs)]

Le constructeur accepte une instance de contexte de base de données et initialise la variable de jeu d’entités :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample19.cs)]

Le `Get` méthode utilise les expressions lambda pour autoriser le code appelant spécifier une condition de filtre et une colonne pour trier les résultats par, et un paramètre de chaîne permet à l’appelant de fournir une liste délimitée par des virgules des propriétés de navigation pour le chargement hâtif :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample20.cs)]

Le code `Expression<Func<TEntity, bool>> filter` signifie que l’appelant fournit une expression lambda en fonction de la `TEntity` type et cette expression retourne une valeur booléenne. Par exemple, si le référentiel est instancié pour la `Student` type d’entité, le code dans la méthode d’appel peut spécifier `student => student.LastName == "Smith` &quot; pour la `filter` paramètre.

Le code `Func<IQueryable<TEntity>, IOrderedQueryable<TEntity>> orderBy` signifie également que l’appelant fournit une expression lambda. Mais dans ce cas, l’entrée de l’expression est une `IQueryable` de l’objet pour le `TEntity` type. L’expression retourne une version triée de `IQueryable` objet. Par exemple, si le référentiel est instancié pour la `Student` type d’entité, le code dans la méthode d’appel peut spécifier `q => q.OrderBy(s => s.LastName)` pour la `orderBy` paramètre.

Le code dans le `Get` méthode crée un `IQueryable` de l’objet, puis les applique l’expression de filtre si elle existe :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample21.cs)]

Il applique ensuite les expressions de chargement hâtif après l’analyse de la liste délimitée par des virgules :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample22.cs)]

Enfin, elle s’applique le `orderBy` expression si elle existe et retourne les résultats ; sinon, elle retourne les résultats de la requête non triée :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample23.cs)]

Lorsque vous appelez le `Get` (méthode), vous pouvez effectuer le filtrage et le tri sur le `IEnumerable` collection retournée par la méthode au lieu de fournir des paramètres pour ces fonctions. Mais le tri et filtrage seront ensuite effectuées en mémoire sur le serveur web. À l’aide de ces paramètres, vous assurer que le travail est effectué par la base de données plutôt que par le serveur web. Une alternative consiste à créer des classes dérivées des types d’entités spécifique et ajoutez spécialisé `Get` méthodes, telles que `GetStudentsInNameOrder` ou `GetStudentsByName`. Toutefois, dans une application complexe, ce qui risque d’un grand nombre de ces classes dérivées et les méthodes spécialisées, qui peut être plus de travail pour mettre à jour.

Le code dans le `GetByID`, `Insert`, et `Update` méthodes est similaire à ce que vous avez vu dans le référentiel non générique. (Vous n’êtes pas en fournissant un paramètre de chargement hâtif dans le `GetByID` signature, car vous ne pouvez pas faire un chargement hâtif avec la `Find` méthode.)

Deux surcharges sont fournies pour le `Delete` méthode :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample24.cs)]

Une de ces vous permet de vous transmettez uniquement l’ID de l’entité à supprimer, et l’une prend une instance d’entité. Comme vous l’avez vu dans les [concurrence de la gestion des](../../getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md) (didacticiel), pour vous gestion concurrentielle un `Delete` méthode qui prend une instance d’entité qui inclut la valeur d’origine d’une propriété de suivi.

Ce référentiel générique gérera les exigences CRUD classiques. Lorsqu’un type d’entité particulier a des exigences particulières, telles que le tri, ou de filtrage plus complexes, vous pouvez créer une classe dérivée qui a des méthodes supplémentaires pour ce type.

## <a name="creating-the-unit-of-work-class"></a>Création de la classe d’unité de travail

La classe d’unité de travail a une fonction : pour vous assurer que lorsque vous utilisez plusieurs référentiels, ils partagent un contexte de base de données unique. De cette façon, quand une unité de travail terminée vous pouvez appeler la `SaveChanges` méthode sur cette instance du contexte et s’assurer que tous les connexes modifications seront coordonnées. Les besoins de la classe qu’un `Save` méthode et une propriété pour chaque espace de stockage. Chaque propriété de référentiel retourne une instance de référentiel qui a été instanciée à l’aide de la même instance de contexte de base de données en tant que les autres instances de référentiel.

Dans le *DAL* dossier, créez un fichier de classe nommé *UnitOfWork.cs* et remplacez le code de modèle par le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample25.cs)]

Le code crée des variables de classe pour le contexte de base de données et de chaque référentiel. Pour le `context` variable, un nouveau contexte est instancié :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample26.cs)]

Chaque propriété de référentiel vérifie si le référentiel existe déjà. Si ce n’est pas le cas, il instancie le référentiel, en passant l’instance de contexte. Par conséquent, tous les référentiels partagent la même instance de contexte.

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample27.cs)]

Le `Save` les appels de méthode `SaveChanges` sur le contexte de base de données.

Comme n’importe quelle classe qui instancie un contexte de base de données dans une variable de classe, le `UnitOfWork` la classe implémente `IDisposable` et supprime le contexte.

### <a name="changing-the-course-controller-to-use-the-unitofwork-class-and-repositories"></a>Modification du contrôleur de cours pour utiliser la classe de UnitOfWork et des référentiels de

Remplacez le code que vous avez actuellement dans *CourseController.cs* avec le code suivant :

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample28.cs?highlight=15,20,22,31,54-55,70,85-86,101-102,122-124,130)]

Ce code ajoute une variable de classe pour la `UnitOfWork` classe. (Si vous utilisez les interfaces ici, vous n’initialiser la variable ici ; au lieu de cela, vous pouvez implémenter un modèle de deux constructeurs tout comme vous l’avez fait le `Student` référentiel.)

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample29.cs)]

Dans le reste de la classe, toutes les références dans le contexte de base de données sont remplacées par des références à du référentiel approprié, à l’aide de `UnitOfWork` propriétés pour accéder au référentiel. Le `Dispose` méthode supprime le `UnitOfWork` instance.

[!code-csharp[Main](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/samples/sample30.cs)]

Exécuter le site, puis cliquez sur le **cours** onglet.

![Courses_Index_page](implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application/_static/image4.png)

La page recherche et fonctionne comme auparavant vos modifications et les autres pages de cours également fonctionnent de la même.

## <a name="summary"></a>Résumé

Vous avez maintenant implémenté le référentiel et l’unité de travail des modèles. Vous avez utilisé des expressions lambda comme paramètres de méthode dans le référentiel générique. Pour plus d’informations sur l’utilisation de ces expressions avec un `IQueryable` d’objets, consultez [IQueryable(T) Interface (System.Linq)](https://msdn.microsoft.com/en-us/library/bb351562.aspx) dans MSDN Library. Dans la prochaine didacticiel, vous allez apprendre à gérer certains scénarios avancés.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access Content Map](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)
