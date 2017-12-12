---
uid: web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests
title: "À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 2 : ajout d’une couche de logique métier et des Tests unitaires | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par la prise en main de la série de didacticiels Entity Framework 4.0. JE..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2011
ms.topic: article
ms.assetid: efb0e677-10b8-48dc-93d3-9ba3902dd807
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests
msc.type: authoredcontent
ms.openlocfilehash: 0440f807c7baa7b92e5f05590eca9cc237b5aef9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-the-entity-framework-40-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests"></a>À l’aide d’Entity Framework 4.0 et du contrôle ObjectDataSource, partie 2 : ajout d’une couche de logique métier et des Tests unitaires
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> Cette série de didacticiels s’appuie sur l’application web de l’université Contoso qui est créée par le [mise en route avec l’Entity Framework 4.0](https://asp.net/entity-framework/tutorials#Getting%20Started) série de didacticiels. Si vous n’avez pas terminé les didacticiels antérieures, comme point de départ pour ce didacticiel vous pouvez [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-97f8ee9a) que vous devriez avoir créé. Vous pouvez également [télécharger l’application](https://code.msdn.microsoft.com/ASPNET-Web-Forms-6c7197aa) qui est créé par la série de didacticiels terminée. Si vous avez des questions sur les didacticiels, vous pouvez les valider pour le [forum de ASP.NET Entity Framework](https://forums.asp.net/1227.aspx).


Dans le didacticiel précédent, vous avez créé une application multicouche web à l’aide d’Entity Framework et le `ObjectDataSource` contrôle. Ce didacticiel montre comment ajouter la logique métier tout en conservant la couche de logique métier (BLL) et la couche d’accès aux données (DAL) distincts, et indique comment créer des tests unitaires automatisés pour la couche BLL.

Dans ce didacticiel, vous effectuerez les tâches suivantes :

- Créer une interface de référentiel qui déclare les méthodes d’accès aux données que vous avez besoin.
- Implémentez l’interface de référentiel dans la classe de référentiel.
- Créez une classe de logique métier qui appelle la classe de référentiel pour effectuer des fonctions d’accès aux données.
- Connecter le `ObjectDataSource` contrôle à la classe de logique métier au lieu de pour la classe de référentiel.
- Créer un projet de test unitaire et une classe de référentiel qui utilise des collections en mémoire pour stocker ses données.
- Créer un test unitaire pour la logique métier que vous souhaitez ajouter à la classe de la logique métier, puis exécuter le test et consultez il échoue.
- Implémenter la logique métier dans la classe de logique métier, puis exécutez de nouveau l’unité de test et voir passer.

Vous allez travailler avec les *Departments.aspx* et *DepartmentsAdd.aspx* pages que vous avez créé dans le didacticiel précédent.

## <a name="creating-a-repository-interface"></a>Création d’une Interface de référentiel

Vous allez commencer par la création de l’interface de référentiel.

[![Image08](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image2.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image1.png)

Dans le *DAL* dossier, créez un nouveau fichier de classe, nommez-le *ISchoolRepository.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample1.cs)]

L’interface définit une méthode pour chacun du CRUD (créer, lire, mettre à jour, supprimer) les méthodes que vous avez créé dans la classe de référentiel.

Dans le `SchoolRepository` classe dans *SchoolRepository.cs*, indiquer que cette classe implémente la `ISchoolRepository` interface :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample2.cs)]

## <a name="creating-a-business-logic-class"></a>Création d’une classe de logique métier

Ensuite, vous allez créer la classe de logique métier. Cela afin que vous pouvez ajouter une logique métier qui sera exécutée par le `ObjectDataSource` contrôler, même si vous ne qui encore. Pour l’instant, la nouvelle classe de logique métier effectue uniquement les opérations CRUD de même que le référentiel.

[![Image09](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image4.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image3.png)

Créez un nouveau dossier et nommez-le *BLL*. (Dans une application réelle, la couche de logique métier est généralement être implémentée comme une bibliothèque de classes, un projet distinct, mais pour simplifier ce didacticiel, les classes de la couche BLL seront conservés dans un dossier de projet.)

Dans le *BLL* dossier, créez un nouveau fichier de classe, nommez-le *SchoolBL.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample3.cs)]

Ce code crée les mêmes méthodes CRUD, vous avez vu plus haut dans la classe de l’espace de stockage, mais au lieu d’accéder directement aux méthodes Entity Framework, il appelle le référentiel des méthodes de la classe.

La variable de classe qui conserve une référence à la classe de l’espace de stockage est définie comme un type d’interface, et le code qui instancie la classe de référentiel est contenu dans les deux constructeurs. Le constructeur sans paramètre sera utilisé par le `ObjectDataSource` contrôle. Il crée une instance de la `SchoolRepository` classe que vous avez créé précédemment. L’autre constructeur permet à tout code qui instancie la classe de logique métier pour transmettre tout objet qui implémente l’interface de référentiel.

Les méthodes CRUD qui appellent la classe de l’espace de stockage et les deux constructeurs permettent d’utiliser la classe de logique métier avec tout magasin de données back-end vous choisissez. La classe de logique métier n’a pas besoin de savoir comment la classe il appelle conserve les données. (Cela est souvent appelé *ignorant la persistance*.) Cela facilite les tests unitaires, car vous pouvez vous connecter à la classe de logique métier pour une implémentation d’un référentiel qui utilise un élément simple en tant que mémoire `List` collections pour stocker les données.

> [!NOTE]
> Techniquement, les objets d’entité sont toujours pas ignorant la persistance, car ils sont instanciés à partir des classes qui héritent d’Entity Framework `EntityObject` classe. Ignorance de persistance terminée, vous pouvez utiliser *bons vieux objets CLR*, ou *POCOs*, à la place des objets qui héritent de la `EntityObject` classe. À l’aide de POCOs est dépasse le cadre de ce didacticiel. Pour plus d’informations, consultez [testabilité et Entity Framework 4.0](https://msdn.microsoft.com/en-us/library/ff714955.aspx) sur le site Web MSDN.)


Vous pouvez vous connecter à présent le `ObjectDataSource` contrôles à la logique métier classe à la place de l’espace de stockage et vérifier que tout fonctionne comme auparavant.

Dans *Departments.aspx* et *DepartmentsAdd.aspx*, modifier chaque occurrence de `TypeName="ContosoUniversity.DAL.SchoolRepository"` à `TypeName="ContosoUniversity.BLL.SchoolBL`». (Il existe quatre instances en tout).

Exécutez le *Departments.aspx* et *DepartmentsAdd.aspx* pages pour vérifier qu’ils continueront de fonctionner comme avant.

[![Image01](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image6.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image5.png)

[![Image02](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image8.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image7.png)

## <a name="creating-a-unit-test-project-and-repository-implementation"></a>Création d’un projet de Test unitaire et l’implémentation d’un référentiel

Ajouter un nouveau projet à la solution en utilisant la **projet de Test** modèle et nommez-le `ContosoUniversity.Tests`.

Dans le projet de test, ajoutez une référence à `System.Data.Entity` et ajouter une référence de projet à la `ContosoUniversity` projet.

Vous pouvez maintenant créer la classe de l’espace de stockage que vous allez utiliser avec des tests unitaires. Le magasin de données pour ce référentiel sera au sein de la classe.

[![Image12](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image10.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image9.png)

Dans le projet de test, créez un nouveau fichier de classe, nommez-le *MockSchoolRepository.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample4.cs)]

Cette classe référentiel a les mêmes méthodes CRUD que celui qui accède directement à Entity Framework, mais ils fonctionnent avec `List` collections en mémoire au lieu d’une base de données. Cela rend plus facile pour une classe de test configurer et valider les tests unitaires pour la classe de logique métier.

## <a name="creating-unit-tests"></a>Création de Tests unitaires

Le **Test** modèle de projet a créé une classe de test unitaire stub pour vous et votre tâche suivante consiste à modifier cette classe en lui ajoutant des méthodes de test unitaire pour la logique métier que vous souhaitez ajouter à la classe de logique métier.

[![Image13](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image12.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image11.png)

Université de Contoso, un formateur individuel peut être uniquement l’administrateur d’un seul service, et vous devez ajouter une logique métier pour appliquer cette règle. Vous allez commencer par ajouter des tests et l’exécution des tests pour les afficher échouent. Vous allez ensuite ajouter le code et réexécuter les tests, qui s’attend à voir les transmettre.

Ouvrez le *UnitTest1.cs* et ajoutez `using` instructions pour les couches de logique et d’accès aux données d’entreprise que vous avez créé dans le projet ContosoUniversity :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample5.cs)]

Remplacez le `TestMethod1` méthode avec les méthodes suivantes :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample6.cs)]

Le `CreateSchoolBL` méthode crée une instance de la classe de l’espace de stockage que vous avez créé pour le test unitaire projet, qu’il passe ensuite à une nouvelle instance de la classe de logique métier. La méthode utilise ensuite la classe de logique métier pour insérer trois services que vous pouvez utiliser dans les méthodes de test.

Les méthodes de test vérifier que la classe de logique métier lève une exception si un utilisateur tente d’insérer un nouveau service avec le même administrateur comme un service existant, ou si un utilisateur tente de mettre à jour d’administrateur d’un service en lui affectant l’ID d’une personne qui est déjà l’administrateur d’un autre département.

Vous n’avez pas encore, créé la classe d’exception pour ce code ne peut pas être compilé. Pour se compiler, avec le bouton droit `DuplicateAdministratorException` et sélectionnez **générer**, puis **classe**.

[![Image14](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image14.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image13.png)

Cela crée une classe dans le projet de test que vous pouvez supprimer une fois que vous avez créé la classe d’exception dans le projet principal. et mis en œuvre de la logique métier.

Exécutez le projet de test. Comme prévu, les tests échouent.

[![Image03](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image16.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image15.png)

## <a name="adding-business-logic-to-make-a-test-pass"></a>Ajout de logique métier pour effectuer un test

Ensuite, vous allez implémenter la logique métier qui rend impossible à définir comme l’administrateur d’un service, une personne qui est déjà administrateur d’un autre département. Vous allez lever une exception à partir de la couche de logique métier et catch dans la couche présentation si un utilisateur modifie un département et clique sur **mise à jour** après avoir sélectionné une personne qui est déjà un administrateur. (Vous pouvez également supprimer les formateurs à partir de la liste déroulante qui sont déjà administrateurs avant de restituer la page, mais le but ici est de travailler avec la couche de logique métier).

Commencez par créer la classe d’exception que vous obtenez quand un utilisateur tente de rendre un formateur de l’administrateur de plus d’un service. Dans le projet principal, créez un nouveau fichier de classe dans le *BLL* dossier, nommez-le *DuplicateAdministratorException.cs*et remplacez le code existant par le code suivant :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample7.cs)]

À présent supprimer la variable temporaire *DuplicateAdministratorException.cs* fichier que vous avez créé précédemment dans le projet de test pour pouvoir les compiler.

Dans le projet principal, ouvrez le *SchoolBL.cs* et ajoutez la méthode suivante qui contient la logique de validation. (Le code fait référence à une méthode que vous allez créer ultérieurement).

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample8.cs)]

Vous devez appeler cette méthode lorsque vous êtes insertion ou mise à jour `Department` entités afin de vérifier si un autre service possède déjà le même administrateur.

Le code appelle une méthode pour rechercher la base de données pour un `Department` entité ayant le même `Administrator` valeur de la propriété en tant que l’entité qui est inséré ou mis à jour. S’il existe, le code lève une exception. Aucune vérification de validation n’est requise si l’entité est insérée ou mise à jour n’a pas `Administrator` valeur et aucune exception n’est levée si la méthode est appelée pendant une mise à jour et la `Department` entité trouvée correspond à la `Department` entité en cours de mise à jour.

Appeler la nouvelle méthode à partir de la `Insert` et `Update` méthodes :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample9.cs)]

Dans *ISchoolRepository.cs*, ajoutez la déclaration suivante pour la nouvelle méthode d’accès aux données :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample10.cs)]

Dans *SchoolRepository.cs*, ajoutez le code suivant `using` instruction :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample11.cs)]

Dans *SchoolRepository.cs*, ajoutez la nouvelle méthode d’accès aux données suivantes :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample12.cs)]

Ce code récupère `Department` des entités qui ont un administrateur spécifié. Doit être trouvé qu’un seul service (le cas échéant). Toutefois, comme aucune contrainte n’est créée dans la base de données, le type de retour est une collection en cas de plusieurs services sont trouvent.

Par défaut, lorsque le contexte de l’objet extrait les entités à partir de la base de données, il effectue le suivi de leur dans le Gestionnaire d’état de son objet. Le `MergeOption.NoTracking` paramètre spécifie que ce suivi n’est pas effectué pour cette requête. Cela est nécessaire car la requête peut retourner l’entité exacte que vous essayez de mettre à jour, puis vous ne serez pas en mesure de joindre cette entité. Par exemple, si vous modifiez le service de l’historique de la *Departments.aspx* page et laisser inchangé de l’administrateur, cette requête renvoie le département de l’historique. Si `NoTracking` n’est pas défini, le contexte de l’objet aurait déjà l’entité de service de l’historique dans le Gestionnaire d’état de son objet. Ensuite, lorsque vous attachez l’entité de service de l’historique est recréée à partir de l’état d’affichage, le contexte de l’objet lève une exception indiquant que `"An object with the same key already exists in the ObjectStateManager. The ObjectStateManager cannot track multiple objects with the same key"`.

(En guise d’alternative à la spécification `MergeOption.NoTracking`, vous pouvez créer un nouveau contexte de l’objet pour cette requête. Parce que le contexte d’objet aurait son propre gestionnaire d’état objet, il ne serait aucun conflit lorsque vous appelez le `Attach` (méthode). Le nouveau contexte de l’objet doivent partager la connexion de base de données et métadonnées avec le contexte de l’objet d’origine, afin de dégrader les performances de cette approche est minime. L’approche illustrée ici, toutefois, introduit la `NoTracking` option, qui vous seront utiles dans d’autres contextes. Le `NoTracking` option est décrite plus en détail dans un didacticiel plus loin dans cette série.)

Dans le projet de test, ajoutez la nouvelle méthode d’accès aux données *MockSchoolRepository.cs*:

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample13.cs)]

Ce code utilise la requête LINQ pour effectuer la même sélection de données qui le `ContosoUniversity` LINQ to Entities pour le référentiel de projet utilise.

Réexécutez le projet de test. Cette fois-ci, les tests réussissent.

[![Image04](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image18.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image17.png)

## <a name="handling-objectdatasource-exceptions"></a>La gestion des Exceptions de ObjectDataSource

Dans le `ContosoUniversity` projet, exécutez le *Departments.aspx* page et essayez de modifier l’administrateur d’un service à une personne qui est déjà administrateur pour un autre service. (N’oubliez pas que vous pouvez modifier uniquement les services que vous avez ajouté au cours de ce didacticiel, étant donné que la base de données est préinstallé avec des données non valides). Vous obtenez la page d’erreur de serveur suivants :

[![Image05](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image20.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image19.png)

Vous ne souhaitez pas aux utilisateurs d’afficher ce type de page d’erreur, vous devez ajouter du code de gestion des erreurs. Ouvrez *Departments.aspx* et spécifier un gestionnaire pour le `OnUpdated` l’événement de la `DepartmentsObjectDataSource`. Le `ObjectDataSource` balise d’ouverture maintenant ressemble à l’exemple suivant.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample14.aspx)]

Dans *Departments.aspx.cs*, ajoutez le code suivant `using` instruction :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample15.cs)]

Ajoutez le gestionnaire suivant pour le `Updated` événement :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample16.cs)]

Si le `ObjectDataSource` contrôle intercepte une exception lorsqu’il tente d’effectuer la mise à jour, il passe l’exception dans l’argument d’événement (`e`) à ce gestionnaire. Le code dans le Gestionnaire de contrôle pour voir si l’exception est l’exception de l’administrateur en double. Si c’est le cas, le code crée un contrôle du programme de validation qui contient un message d’erreur pour le `ValidationSummary` contrôle à afficher.

Exécuter la page et tentez de réeffectuer une personne l’administrateur des deux services. Cette fois le `ValidationSummary` contrôle affiche un message d’erreur.

[![Image06](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image22.png)](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/_static/image21.png)

Apporter des modifications similaires à la *DepartmentsAdd.aspx* page. Dans *DepartmentsAdd.aspx*, spécifiez un gestionnaire pour le `OnInserted` l’événement de la `DepartmentsObjectDataSource`. Le balisage résultant ressemblera à l’exemple suivant.

[!code-aspx[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample17.aspx)]

Dans *DepartmentsAdd.aspx.cs*, ajoutez le même `using` instruction :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample18.cs)]

Ajoutez le Gestionnaire d’événements suivantes :

[!code-csharp[Main](using-the-entity-framework-and-the-objectdatasource-control-part-2-adding-a-business-logic-layer-and-unit-tests/samples/sample19.cs)]

Vous pouvez maintenant tester la *DepartmentsAdd.aspx.cs* page pour vérifier qu’elle gère également correctement tente d’établir une personne de l’administrateur de plus d’un service.

Cette étape termine la présentation de l’implémentation du modèle de référentiel pour l’utilisation de la `ObjectDataSource` contrôle avec Entity Framework. Pour plus d’informations sur le modèle de référentiel et la testabilité, consultez le livre blanc MSDN [testabilité et Entity Framework 4.0](https://msdn.microsoft.com/en-us/library/ff714955.aspx).

Dans ce didacticiel, vous allez apprendre à ajouter le tri et le filtrage des fonctionnalités à l’application.

>[!div class="step-by-step"]
[Précédent](using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started.md)
[Suivant](using-the-entity-framework-and-the-objectdatasource-control-part-3-sorting-and-filtering.md)
