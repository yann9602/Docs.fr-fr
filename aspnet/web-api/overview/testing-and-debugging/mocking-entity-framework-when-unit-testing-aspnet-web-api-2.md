---
uid: web-api/overview/testing-and-debugging/mocking-entity-framework-when-unit-testing-aspnet-web-api-2
title: "Simulation d’Entity Framework lorsque l’API Web ASP.NET 2 de tests unitaires | Documents Microsoft"
author: tfitzmac
description: "Ce guide et l’application montrent comment créer des tests unitaires pour votre application Web API 2 qui utilise Entity Framework. Il montre comment modifier le..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/13/2013
ms.topic: article
ms.assetid: cd844025-ccad-41ce-8694-595f1022a49f
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/testing-and-debugging/mocking-entity-framework-when-unit-testing-aspnet-web-api-2
msc.type: authoredcontent
ms.openlocfilehash: 2d8a3df94c91d2fac79006916375764c2b90dc85
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="mocking-entity-framework-when-unit-testing-aspnet-web-api-2"></a>Simulation d’Entity Framework lorsque l’API Web ASP.NET 2 de tests unitaires
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-e2867d4d)

> Ce guide et l’application montrent comment créer des tests unitaires pour votre application Web API 2 qui utilise Entity Framework. Il montre comment modifier le contrôleur de modèle généré automatiquement pour activer la transmission d’un objet de contexte pour le test et comment créer des objets de test qui fonctionnent avec Entity Framework.
> 
> Pour une introduction aux tests unitaires avec l’API Web ASP.NET, consultez [tests unitaires avec ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md).
> 
> Ce didacticiel suppose que vous êtes familiarisé avec les concepts de base de l’API Web ASP.NET. Pour un didacticiel d’introduction, consultez [Getting Started with ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2017](https://www.visualstudio.com/vs/)
> - API Web 2


## <a name="in-this-topic"></a>Dans cette rubrique

Cette rubrique contient les sections suivantes :

- [Composants requis](#prereqs)
- [Télécharger le code](#download)
- [Création d’application avec un projet de test unitaire](#appwithunittest)
- [Créer la classe de modèle](#modelclass)
- [Ajouter le contrôleur](#controller)
- [Ajouter l’injection de dépendance](#dependency)
- [Installer les packages NuGet dans le projet de test](#testpackages)
- [Créer un contexte de test](#testcontext)
- [Créer des tests](#tests)
- [Exécuter des tests](#runtests)

Si vous avez déjà effectué les étapes de [tests unitaires avec ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md), vous pouvez passer à la section [ajouter le contrôleur de](#controller).

<a id="prereqs"></a>
## <a name="prerequisites"></a>Conditions préalables

Visual Studio 2017 Community, Professional ou l’édition entreprise

<a id="download"></a>
## <a name="download-code"></a>Télécharger le code

Téléchargez le [projet achevé](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11). Le projet téléchargeable comprend des tests unitaires de code pour cette rubrique et la [unité test ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md) rubrique.

<a id="appwithunittest"></a>
## <a name="create-application-with-unit-test-project"></a>Création d’application avec un projet de test unitaire

Vous pouvez créer un projet de test unitaire lors de la création de votre application ou ajouter un projet de test unitaire à une application existante. Ce didacticiel montre la création d’un projet de test unitaire lors de la création de l’application.

Créer une Application Web ASP.NET nommée **StoreApp**.

Dans la fenêtre Nouveau projet ASP.NET, sélectionnez le **vide** modèle et ajouter des dossiers et les références de base de l’API Web. Sélectionnez le **ajouter des tests unitaires** option. Le projet de test unitaire est automatiquement nommé **StoreApp.Tests**. Vous pouvez conserver ce nom.

![créer un projet de test unitaire](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image1.png)

Après avoir créé l’application, vous verrez qu’il contient deux projets - **StoreApp** et **StoreApp.Tests**.

<a id="modelclass"></a>
## <a name="create-the-model-class"></a>Créer la classe de modèle

Dans votre projet StoreApp, ajoutez un fichier de classe dans le **modèles** dossier nommé **Product.cs**. Remplacez le contenu du fichier par le code suivant.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample1.cs)]

Générez la solution.

<a id="controller"></a>
## <a name="add-the-controller"></a>Ajouter le contrôleur

Cliquez sur le dossier Controllers, puis sélectionnez **ajouter** et **un nouvel élément structuré**. Sélectionnez le contrôleur de Web API 2 avec actions, utilisant Entity Framework.

![Ajouter un nouveau contrôleur](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image2.png)

Définissez les valeurs suivantes :

- Nom du contrôleur : **ProductController**
- Classe de modèle : **produit**
- Classe de contexte de données : [Sélectionnez **nouveau contexte de données** bouton qui remplit les valeurs indiqués ci-dessous]

![Spécifiez le contrôleur](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image3.png)

Cliquez sur **ajouter** pour créer le contrôleur avec le code généré automatiquement. Le code inclut des méthodes pour la création, la récupération, la mise à jour et supprimer des instances de la classe de produit. Le code suivant illustre la méthode pour ajouter un produit. Notez que la méthode retourne une instance de **IHttpActionResult**.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample2.cs)]

IHttpActionResult est une des nouvelles fonctionnalités de l’API Web 2, et il simplifie le développement de test unitaire.

Dans la section suivante, vous allez personnaliser le code généré pour faciliter le passage d’objets de test au contrôleur.

<a id="dependency"></a>
## <a name="add-dependency-injection"></a>Ajouter l’injection de dépendance

Actuellement, la classe ProductController est codé en dur pour utiliser une instance de la classe StoreAppContext. Vous allez utiliser un modèle appelé injection de dépendances pour modifier votre application et de supprimer cette dépendance codée en dur. En rompant cette dépendance, vous pouvez passer d’un objet fictif lors du test.

Cliquez sur le **modèles** dossier et ajoutez une nouvelle interface nommée **IStoreAppContext**.

Remplacez le code par celui-ci :

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample3.cs)]

Ouvrez le fichier StoreAppContext.cs et apportez les modifications en surbrillance suivantes. Notez les modifications importantes sont :

- StoreAppContext classe implémente l’interface de IStoreAppContext
- MarkAsModified méthode est implémentée.


[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample4.cs?highlight=6,14-17)]

Ouvrez le fichier ProductController.cs. Modifier le code existant pour faire correspondre le code en surbrillance. Ces modifications rompre la dépendance sur StoreAppContext et permettent à d’autres classes passer un objet différent pour la classe de contexte. Cette modification vous permettra à passer dans un contexte de test pendant les tests unitaires.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample5.cs?highlight=4,7-12)]

Il existe une modification plus, que vous devez apporter dans ProductController. Dans le **PutProduct** (méthode), remplacez la ligne qui définit l’état d’entité modifiée par un appel à la méthode MarkAsModified.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample6.cs?highlight=14-15)]

Générez la solution.

Vous êtes maintenant prêt à configurer le projet de test.

<a id="testpackages"></a>
## <a name="install-nuget-packages-in-test-project"></a>Installer les packages NuGet dans le projet de test

Lorsque vous utilisez le modèle vide pour créer une application, le projet de test unitaire (StoreApp.Tests) n’inclut pas tous les packages NuGet installées. Autres modèles, par exemple, le modèle de l’API Web, incluent des packages NuGet dans le projet de test unitaire. Pour ce didacticiel, vous devez inclure le package d’Entity Framework et le package Microsoft ASP.NET Web API 2 Core au projet de test.

Cliquez sur le projet StoreApp.Tests et sélectionnez **gérer les Packages NuGet**. Vous devez sélectionner le projet StoreApp.Tests pour ajouter les packages à ce projet.

![gérer les packages](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image4.png)

Des packages en ligne, rechercher et installer le package EntityFramework (version 6.0 ou version ultérieure). S’il apparaît que le package EntityFramework est déjà installé, vous avez peut-être sélectionné le projet StoreApp au lieu du projet StoreApp.Tests.

![ajouter l’Entity Framework](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image5.png)

Rechercher et installer Microsoft ASP.NET Web API 2 Core package.

![installer le package de base des api web](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image6.png)

Fermez la fenêtre Gérer les Packages NuGet.

<a id="testcontext"></a>
## <a name="create-test-context"></a>Créer un contexte de test

Ajoutez une classe nommée **TestDbSet** au projet de test. Cette classe sert de classe de base pour votre jeu de données de test. Remplacez le code par celui-ci :

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample7.cs)]

Ajoutez une classe nommée **TestProductDbSet** au projet de test qui contient le code suivant.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample8.cs)]

Ajoutez une classe nommée **TestStoreAppContext** et remplacez le code existant par le code suivant.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample9.cs)]

<a id="tests"></a>
## <a name="create-tests"></a>Créer des tests

Par défaut, votre projet de test contient un fichier de test vide nommé **UnitTest1.cs**. Ce fichier montre les attributs que vous permet de créer des méthodes de test. Pour ce didacticiel, vous pouvez supprimer ce fichier, car vous allez ajouter une nouvelle classe de test.

Ajoutez une classe nommée **TestProductController** au projet de test. Remplacez le code par celui-ci :

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample10.cs)]

<a id="runtests"></a>
## <a name="run-tests"></a>Exécuter les tests

Vous êtes maintenant prêt à exécuter les tests. Tous les de la méthode qui sont marqués avec le **TestMethod** attribut sera testé. À partir de la **Test** élément de menu, exécutez les tests.

![exécuter des tests](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image7.png)

Ouvrez le **l’Explorateur de tests** fenêtre et notez les résultats des tests.

![résultats des tests](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image8.png)
