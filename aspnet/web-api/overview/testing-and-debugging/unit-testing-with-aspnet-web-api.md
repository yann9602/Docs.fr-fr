---
uid: web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
title: ASP.NET Web API 2 de tests unitaires | Documents Microsoft
author: tfitzmac
description: "Ce guide et l’application montrent comment créer des tests unitaires simple pour votre application Web API 2. Ce didacticiel montre comment inclure un projet de test unitaire..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/05/2014
ms.topic: article
ms.assetid: bf20f78d-ff91-48be-abd1-88e23dcc70ba
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 13211ee4543e17a4bfb2f83495f4041880f37df2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="unit-testing-aspnet-web-api-2"></a>ASP.NET Web API 2 de tests unitaires
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-e2867d4d)

> Ce guide et l’application montrent comment créer des tests unitaires simple pour votre application Web API 2. Ce didacticiel montre comment inclure un projet de test unitaire dans votre solution et écrire des méthodes de test qui vérifient les valeurs retournées à partir d’une méthode de contrôleur.
> 
> Ce didacticiel suppose que vous êtes familiarisé avec les concepts de base de l’API Web ASP.NET. Pour un didacticiel d’introduction, consultez [Getting Started with ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).
> 
> Les tests unitaires dans cette rubrique sont volontairement limitées à des scénarios de données simple. Pour des scénarios plus avancés de données de tests unitaires, consultez [simulation Entity Framework lorsque unité test ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).
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

    - [Ajouter le projet de test unitaire lors de la création de l’application](#whencreate)
    - [Ajouter un projet de test unitaire à une application existante](#addtoexisting)
- [Configurer l’application Web API 2](#setupproject)
- [Installer les packages NuGet dans le projet de test](#testpackages)
- [Créer des tests](#tests)
- [Exécuter des tests](#runtests)

<a id="prereqs"></a>
## <a name="prerequisites"></a>Conditions préalables

Visual Studio 2017 Community, Professional ou l’édition entreprise

<a id="download"></a>
## <a name="download-code"></a>Télécharger le code

Téléchargez le [projet achevé](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11). Le projet téléchargeable comprend des tests unitaires de code pour cette rubrique et la [simulation Entity Framework lorsque unité test ASP.NET Web API](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md) rubrique.

<a id="appwithunittest"></a>
## <a name="create-application-with-unit-test-project"></a>Création d’application avec un projet de test unitaire

Vous pouvez créer un projet de test unitaire lors de la création de votre application ou ajouter un projet de test unitaire à une application existante. Ce didacticiel montre les deux méthodes pour la création d’un projet de test unitaire. Pour suivre ce didacticiel, vous pouvez utiliser des méthodes suivantes.

<a id="whencreate"></a>
### <a name="add-unit-test-project-when-creating-the-application"></a>Ajouter le projet de test unitaire lors de la création de l’application

Créer une Application Web ASP.NET nommée **StoreApp**.

![créer le projet](unit-testing-with-aspnet-web-api/_static/image1.png)

Dans la fenêtre Nouveau projet ASP.NET, sélectionnez le **vide** modèle et ajouter des dossiers et les références de base de l’API Web. Sélectionnez le **ajouter des tests unitaires** option. Le projet de test unitaire est automatiquement nommé **StoreApp.Tests**. Vous pouvez conserver ce nom.

![créer un projet de test unitaire](unit-testing-with-aspnet-web-api/_static/image2.png)

Après avoir créé l’application, vous verrez qu’il contient deux projets.

![deux projets](unit-testing-with-aspnet-web-api/_static/image3.png)

<a id="addtoexisting"></a>
### <a name="add-unit-test-project-to-an-existing-application"></a>Ajouter un projet de test unitaire à une application existante

Si vous n’avez pas créé le projet de test unitaire quand vous avez créé votre application, vous pouvez ajouter un à tout moment. Par exemple, supposons que vous disposez déjà d’une application nommée StoreApp, et que vous souhaitez ajouter des tests unitaires. Pour ajouter un projet de test unitaire, avec le bouton droit de votre solution et sélectionnez **ajouter** et **nouveau projet**.

![Ajouter un nouveau projet à la solution](unit-testing-with-aspnet-web-api/_static/image4.png)

Sélectionnez **Test** dans le volet gauche, sélectionnez **projet de Test unitaire** pour le type de projet. Nommez le projet **StoreApp.Tests**.

![ajouter le projet de test unitaire](unit-testing-with-aspnet-web-api/_static/image5.png)

Vous verrez le projet de test unitaire dans votre solution.

Dans le projet de test unitaire, ajoutez une référence de projet au projet d’origine.

<a id="setupproject"></a>
## <a name="set-up-the-web-api-2-application"></a>Configurer l’application Web API 2

Dans votre projet StoreApp, ajoutez un fichier de classe dans le **modèles** dossier nommé **Product.cs**. Remplacez le contenu du fichier par le code suivant.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample1.cs)]

Générez la solution.

Cliquez sur le dossier Controllers, puis sélectionnez **ajouter** et **un nouvel élément structuré**. Sélectionnez **vide de l’API Web 2 contrôleur -**.

![Ajouter un nouveau contrôleur](unit-testing-with-aspnet-web-api/_static/image6.png)

Définissez le nom du contrôleur sur **SimpleProductController**, puis cliquez sur **ajouter**.

![Spécifiez le contrôleur](unit-testing-with-aspnet-web-api/_static/image7.png)

Remplacez le code existant par le code ci-dessous. Pour simplifier cet exemple, les données sont stockées dans une liste au lieu d’une base de données. La liste définie dans cette classe représente les données de production. Notez que le contrôleur inclut un constructeur qui prend comme paramètre une liste d’objets de produit. Ce constructeur vous permet de passer des données de test lors de tests unitaires. Le contrôleur inclut également deux **async** méthodes pour illustrer les méthodes asynchrones de tests unitaires. Ces méthodes asynchrones ont été implémentées en appelant **Task.FromResult** afin de réduire le code superflu, mais normalement les méthodes inclurait opérations gourmandes en ressources.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample2.cs)]

La méthode GetProduct retourne une instance de la **IHttpActionResult** interface. IHttpActionResult est une des nouvelles fonctionnalités de l’API Web 2, et il simplifie le développement de test unitaire. Les classes qui implémentent l’interface IHttpActionResult se trouvent dans le [System.Web.Http.Results](https://msdn.microsoft.com/en-us/library/system.web.http.results.aspx) espace de noms. Ces classes représentent des réponses possibles à partir d’une demande d’action, et elles correspondent aux codes d’état HTTP.

Générez la solution.

Vous êtes maintenant prêt à configurer le projet de test.

<a id="testpackages"></a>
## <a name="install-nuget-packages-in-test-project"></a>Installer les packages NuGet dans le projet de test

Lorsque vous utilisez le modèle vide pour créer une application, le projet de test unitaire (StoreApp.Tests) n’inclut pas tous les packages NuGet installées. Autres modèles, par exemple, le modèle de l’API Web, incluent des packages NuGet dans le projet de test unitaire. Pour ce didacticiel, vous devez inclure le package Microsoft ASP.NET Web API 2 Core au projet de test.

Cliquez sur le projet StoreApp.Tests et sélectionnez **gérer les Packages NuGet**. Vous devez sélectionner le projet StoreApp.Tests pour ajouter les packages à ce projet.

![gérer les packages](unit-testing-with-aspnet-web-api/_static/image8.png)

Rechercher et installer Microsoft ASP.NET Web API 2 Core package.

![installer le package de base des api web](unit-testing-with-aspnet-web-api/_static/image9.png)

Fermez la fenêtre Gérer les Packages NuGet.

<a id="tests"></a>
## <a name="create-tests"></a>Créer des tests

Par défaut, votre projet de test inclut un fichier de test vide nommé UnitTest1.cs. Ce fichier montre les attributs que vous permet de créer des méthodes de test. Pour vos tests unitaires, vous pouvez utiliser ce fichier ou créer votre propre fichier.

![UnitTest1](unit-testing-with-aspnet-web-api/_static/image10.png)

Pour ce didacticiel, vous allez créer votre propre classe de test. Vous pouvez supprimer le fichier UnitTest1.cs. Ajoutez une classe nommée **TestSimpleProductController.cs**et remplacez le code par le code suivant.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample3.cs)]

<a id="runtests"></a>
## <a name="run-tests"></a>Exécuter les tests

Vous êtes maintenant prêt à exécuter les tests. Tous les de la méthode qui sont marqués avec le **TestMethod** attribut sera testé. À partir de la **Test** élément de menu, exécutez les tests.

![exécuter des tests](unit-testing-with-aspnet-web-api/_static/image11.png)

Ouvrez le **l’Explorateur de tests** fenêtre et notez les résultats des tests.

![résultats des tests](unit-testing-with-aspnet-web-api/_static/image12.png)

## <a name="summary"></a>Résumé

Vous avez terminé ce didacticiel. Les données dans ce didacticiel a été intentionnellement simplifiées pour vous concentrer sur les conditions de test unitaire. Pour des scénarios plus avancés de données de tests unitaires, consultez [simulation Entity Framework lorsque unité test ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).
