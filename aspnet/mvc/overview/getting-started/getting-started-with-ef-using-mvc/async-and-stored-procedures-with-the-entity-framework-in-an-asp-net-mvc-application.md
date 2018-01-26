---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: "Async et les procédures stockées avec Entity Framework dans une Application ASP.NET MVC | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 7412b32ac29179dfa319544781d4c7165c58196b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="async-and-stored-procedures-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Async et les procédures stockées avec Entity Framework dans une Application ASP.NET MVC
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [télécharger le PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 5 à l’aide de l’Entity Framework 6 Code First et Visual Studio 2013. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


Dans les didacticiels antérieures, vous avez appris comment lire et mettre à jour des données à l’aide du modèle de programmation synchrone. Dans ce didacticiel vous allez apprendre à implémenter le modèle de programmation asynchrone. Code asynchrone permettent à une application de donner de meilleurs résultats, car elle permet une meilleure utilisation des ressources du serveur.

Dans ce didacticiel, vous verrez également comment utiliser des procédures stockées pour insert, update et les opérations de suppression sur une entité.

Enfin, vous devez redéployer l’application sur Windows Azure, ainsi que toutes les modifications de base de données que vous avez implémentées depuis la première fois que vous avez déployé.

Les illustrations suivantes montrent certaines des pages que vous allez utiliser.

![Page des services](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Créer le service](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="why-bother-with-asynchronous-code"></a>Pourquoi utiliser le code asynchrone

Un serveur web a un nombre limité de threads disponibles, et dans les situations de forte charge tous les threads disponibles peuvent être en cours d’utilisation. Lorsque cela se produit, le serveur ne peut pas traiter de nouvelles demandes jusqu'à ce que les threads ne sont pas libérées. Avec le code synchrone, plusieurs threads peuvent être bloqués dans pendant qu’ils ne sont pas réellement effectuer aucun travail car ils sont en attente pour les e/s. Avec le code asynchrone, lorsqu’un processus est en attente d’e/s, son thread est libéré pour le serveur à utiliser pour traiter d’autres demandes. Par conséquent, code asynchrone permet de ressources du serveur à utiliser plus efficacement et le serveur est activé pour gérer plus de trafic sans délai.

Dans les versions antérieures de .NET, écrire et tester le code asynchrone a été complexe, source d’erreurs et difficile à déboguer. Dans .NET 4.5, l’écriture, le test et débogage du code asynchrone sont beaucoup plus simple doit généralement écrire du code asynchrone, à moins que vous n’ayez pas à une raison. Code asynchrone introduit une petite quantité de charge, mais dans les situations de faible trafic le gain de performances est négligeable, lors de la pour les cas de trafic élevé, l’amélioration potentielle des performances est importante.

Pour plus d’informations sur la programmation asynchrone, consultez [prise en charge d’utiliser .NET 4.5 asynchrone pour éviter de bloquer les appels](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).

## <a name="create-the-department-controller"></a>Créer le contrôleur de service

Créer un contrôleur de service sélectionner de la même façon que vous avez effectué les contrôleurs antérieures, mais cette fois le **utiliser async contrôleur** case à cocher actions.

![Structure de contrôleur de service](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Les caractéristiques suivantes montrent ce qui a été ajouté au code synchrone pour le `Index` méthode pour le rendre asynchrone :

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

Quatre modifications ont été appliquées pour permettre à la requête de base de données Entity Framework exécuter de façon asynchrone :

- La méthode est marquée avec la `async` (mot clé), qui indique au compilateur pour générer des rappels pour les parties du corps de méthode et pour créer automatiquement le `Task<ActionResult>` objet qui est retourné.
- Le type de retour est passé de `ActionResult` à `Task<ActionResult>`. Le `Task<T>` type représente un travail en cours avec un résultat de type `T`.
- Le `await` (mot clé) a été appliqué à l’appel du service web. Lorsque le compilateur détecte ce mot clé, il divise les coulisses la méthode en deux parties. La première partie se termine par l’opération qui est démarrée en mode asynchrone. La deuxième partie est placée dans une méthode de rappel qui est appelée lorsque l’opération se termine.
- La version asynchrone de la `ToList` méthode d’extension a été appelée.

Pourquoi est la `departments.ToList` mais pas modifier l’instruction la `departments = db.Departments` instruction ? La raison est que seules des instructions qui provoquent des requêtes ou des commandes à envoyer à la base de données sont exécutées de façon asynchrone. Le `departments = db.Departments` instruction définit une requête, mais la requête n’est pas exécutée tant que la `ToList` méthode est appelée. Par conséquent, seuls les `ToList` méthode est exécutée de façon asynchrone.

Dans le `Details` (méthode) et le `HttpGet` `Edit` et `Delete` méthodes, le `Find` méthode est celle qui entraîne une requête à envoyer à la base de données, c’est la méthode qui est exécutée de façon asynchrone :

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

Dans le `Create`, `HttpPost Edit`, et `DeleteConfirmed` méthodes, elle est la `SaveChanges` appel de méthode qui entraîne une commande à exécuter, pas les instructions telles que `db.Departments.Add(department)` qui seulement provoquer des entités dans la mémoire à modifier.

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

Ouvrez *Views\Department\Index.cshtml*et remplacez le code de modèle par le code suivant :

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

Ce code modifie le titre de l’Index pour les services, déplace le nom de l’administrateur vers la droite et fournit le nom complet de l’administrateur.

Dans la créer, supprimer, plus d’informations et modifier des vues, modifier la légende pour le `InstructorID` au champ « Administrateur » de la même façon que vous avez modifié le champ de nom de service pour « Département » dans les vues de cours.

Dans le créer et modifier des vues utilisent le code suivant :

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

Dans les vues de suppression et de détails, utilisez le code suivant :

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Exécutez l’application, puis cliquez sur le **départements** onglet.

![Page des services](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Tout fonctionne de la même que dans les autres contrôleurs, mais dans ce contrôleur de toutes les requêtes SQL s’exécutent en mode asynchrone.

Éléments à connaître lorsque vous utilisez la programmation asynchrone avec Entity Framework :

- Le code asynchrone n’est pas thread-safe. En d’autres termes, en d’autres termes, ne tentez d’effectuer plusieurs opérations en parallèle à l’aide de la même instance de contexte.
- Si vous souhaitez tirer parti des avantages de performances du code asynchrone, assurez-vous que n’importe quelle bibliothèque packages que vous utilisez (telles que pour la pagination), également utiliser async si elles appellent toutes les méthodes Entity Framework qui provoquent des requêtes à envoyer à la base de données.

## <a name="use-stored-procedures-for-inserting-updating-and-deleting"></a>Utiliser des procédures stockées pour l’insertion, de mise à jour et de suppression

Certains développeurs et les administrateurs préfèrent utiliser des procédures stockées pour l’accès de la base de données. Dans les versions antérieures d’Entity Framework, vous pouvez récupérer les données à l’aide d’une procédure stockée par [exécutant une requête SQL brute](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), mais vous ne pouvez pas demander à EF à utiliser des procédures stockées pour les opérations de mise à jour. EF 6, il est facile de configurer un Code First pour utiliser des procédures stockées.

1. Dans *DAL\SchoolContext.cs*, ajoutez le code en surbrillance à le `OnModelCreating` (méthode).

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    Ce code indique à Entity Framework aux procédures d’utilisation stockée pour insérer, mettre à jour et supprimer des opérations sur les `Department` entité.
2. Dans la Console de gestion des packages, entrez la commande suivante :

    `add-migration DepartmentSP`

    Ouvrez *Migrations\&lt ; horodatage&gt;\_DepartmentSP.cs* pour afficher le code dans le `Up` méthode créé par Insert, Update et Delete de procédures stockées :

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
- Dans la Console de gestion des packages, entrez la commande suivante :

    `update-database`
- Exécutez l’application en mode débogage, cliquez sur le **départements** onglet, puis cliquez sur **créer un nouveau**.
- Entrez les données d’un nouveau service, puis cliquez sur **créer**.

    ![Créer le service](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)
- Dans Visual Studio, consultez les journaux dans le **sortie** fenêtre pour voir qu’une procédure stockée a été utilisée pour insérer la nouvelle ligne de service.

    ![Service Insert SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Code crée d’abord les noms de procédure stockée de par défaut. Si vous utilisez une base de données existante, vous devrez peut-être personnaliser les noms des procédures stockées afin d’utiliser des procédures stockées déjà définies dans la base de données. Pour plus d’informations sur la procédure à suivre, consultez [Entity Framework Code premier Insert/Update/Delete Stored Procedures](https://msdn.microsoft.com/data/dn468673).

Si vous souhaitez personnaliser ce qui a généré do de procédures stockées, vous pouvez modifier le code de modèle généré automatiquement pour les migrations `Up` méthode qui crée la procédure stockée. De cette façon vos modifications sont répercutées chaque fois que que la migration est exécutée et est appliquée à votre base de données de production lors de la migration s’exécute automatiquement en production après le déploiement.

Si vous souhaitez modifier une procédure stockée existante qui a été créée dans une migration précédente, vous pouvez utiliser la commande Add-Migration pour générer une migration vide et écrivez ensuite manuellement le code qui appelle le [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) (méthode) .

## <a name="deploy-to-azure"></a>Déployer vers Azure

Cette section, vous devez avoir terminé le paramètre facultatif **déploiement de l’application dans Azure** section dans le [Migrations et déploiement](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) didacticiel de cette série. Si vous aviez des erreurs de migrations résolus en supprimant la base de données dans votre projet local, ignorez cette section.

1. Dans Visual Studio, cliquez sur le projet dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.
2. Cliquez sur **Publier**.

    Visual Studio déploie l’application sur Azure, et l’application s’ouvre dans votre navigateur par défaut, s’exécutant dans Azure.
3. Tester l’application pour vérifier qu’il fonctionne.

    La première fois que vous exécutez une page qui accède à la base de données, Entity Framework exécute toutes les migrations `Up` méthodes nécessaires à la mise à jour avec le modèle de données actuel de la base de données. Vous pouvez désormais utiliser toutes les pages web que vous avez ajoutés depuis la dernière fois que vous avez déployé, y compris les pages de service que vous avez ajouté dans ce didacticiel.

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel vous a appris comment améliorer l’efficacité du serveur en écrivant du code qui s’exécute de façon asynchrone et comment utiliser des procédures stockées pour insérer, mettre à jour et les opérations de suppression. Dans l’étape suivante du didacticiel, vous allez apprendre à éviter la perte de données lorsque plusieurs utilisateurs essaient de modifier le même enregistrement en même temps.

Vous trouverez des liens vers d’autres ressources Entity Framework dans le [ASP.NET Data Access - ressources recommandées](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Précédent](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Suivant](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
