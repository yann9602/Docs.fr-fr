---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-2
title: "Ajoutez des modèles et des contrôleurs | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 88908ff8-51a9-40eb-931c-a8139128b680
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-2
msc.type: authoredcontent
ms.openlocfilehash: b75eae11fd99b60864256f79d4770a3007487964
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="add-models-and-controllers"></a>Ajoutez des modèles et des contrôleurs
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Dans cette section, vous allez ajouter des classes de modèle qui définissent les entités de base de données. Ensuite, vous allez ajouter des contrôleurs Web API qui effectuent des opérations CRUD sur les entités.

## <a name="add-model-classes"></a>Ajouter des Classes de modèle

Dans ce didacticiel, nous allons créer la base de données à l’aide de l’approche « Code First » pour Entity Framework (EF). Code First, vous écrivez des classes c# qui correspondent aux tables de base de données et EF crée la base de données. (Pour plus d’informations, consultez [approches de développement Entity Framework](https://msdn.microsoft.com/en-us/library/ms178359%28v=vs.110%29.aspx#dbfmfcf).)

Nous allons commencer en définissant les objets de domaine en tant que POCOs (des objets brut-old CLR). Nous allons créer les POCOs suivants :

- Auteur
- Livre

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier de modèles. Sélectionnez **ajouter**, puis sélectionnez **classe**. Nommez la classe `Author`.

![](part-2/_static/image1.png)

Remplacez tout le code réutilisable dans Author.cs avec le code suivant.

[!code-csharp[Main](part-2/samples/sample1.cs)]

Ajouter une autre classe nommée `Book`, avec le code suivant.

[!code-csharp[Main](part-2/samples/sample2.cs)]

Entity Framework utilisera ces modèles pour créer des tables de base de données. Pour chaque modèle, le `Id` propriété deviendra la colonne de clé primaire de la table de base de données.

Dans la classe Book, le `AuthorId` définit une clé étrangère dans la `Author` table. (Par souci de simplicité, je suppose qu’un seul auteur de chaque livre.) La classe book contient également une propriété de navigation à la `Author`. Vous pouvez utiliser la propriété de navigation pour accéder aux connexe `Author` dans le code. Plus d’informations sur les propriétés de navigation dans la partie 4, dire [la gestion des Relations d’entité](part-4.md).

## <a name="add-web-api-controllers"></a>Ajouter des contrôleurs de l’API Web

Dans cette section, nous allons ajouter des contrôleurs Web API qui prennent en charge les opérations CRUD (créer, lire, mettre à jour et supprimer). Les contrôleurs utilisera Entity Framework pour communiquer avec la couche de base de données.

Tout d’abord, vous pouvez supprimer le fichier Controllers/ValuesController.cs. Ce fichier contient un contrôleur d’API Web exemple, mais vous n’en avez pas besoin de ce didacticiel.

![](part-2/_static/image2.png)

Ensuite, générez le projet. La génération de modèles automatique API Web utilise la réflexion pour rechercher les classes du modèle, par conséquent, il doit l’assembly compilé.

Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter**, puis sélectionnez **contrôleur**.

![](part-2/_static/image3.png)

Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez « API Web 2 contrôleur avec actions, utilisant Entity Framework ». Cliquez sur **Ajouter**.

![](part-2/_static/image4.png)

Dans le **ajouter un contrôleur** boîte de dialogue, procédez comme suit :

1. Dans le **classe de modèle** liste déroulante, sélectionnez le `Author` classe. (Si vous ne voyez pas répertoriés dans la liste déroulante, assurez-vous que vous avez créé le projet.)
2. Consultez « Utilisez actions asynchrones du contrôleur ».
3. Laissez le nom du contrôleur en tant que &quot;AuthorsController&quot;.
4. Cliquez sur plus (+) situé en regard **classe de contexte de données**.

![](part-2/_static/image5.png)

Dans le **nouveau contexte de données** boîte de dialogue, conservez le nom par défaut et cliquez sur **ajouter**.

![](part-2/_static/image6.png)

Cliquez sur **ajouter** pour terminer le **ajouter un contrôleur** boîte de dialogue. La boîte de dialogue ajoute deux classes à votre projet :

- `AuthorsController`définit un contrôleur d’API Web. Le contrôleur implémente l’API REST que les clients utilisent pour effectuer des opérations CRUD sur la liste des auteurs.
- `BookServiceContext`gère des objets d’entité pendant l’exécution, ce qui inclut le remplissage des objets avec des données à partir d’une base de données, le suivi des modifications et conserver les données de la base de données. Il hérite de `DbContext`.

![](part-2/_static/image7.png)

À ce stade, régénérez le projet. Ensuite suivre les mêmes étapes pour ajouter un contrôleur d’API pour `Book` entités. Cette fois-ci, sélectionnez `Book` pour la classe de modèle, puis sélectionnez existants `BookServiceContext` classe de la classe de contexte de données. (Ne pas créer un nouveau contexte de données.) Cliquez sur **ajouter** pour ajouter le contrôleur.

![](part-2/_static/image8.png)

>[!div class="step-by-step"]
[Précédent](part-1.md)
[Suivant](part-3.md)
