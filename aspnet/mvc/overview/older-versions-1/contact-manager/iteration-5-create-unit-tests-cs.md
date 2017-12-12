---
uid: mvc/overview/older-versions-1/contact-manager/iteration-5-create-unit-tests-cs
title: "Les tests unitaires de créer l’itération #5 – (c#) | Documents Microsoft"
author: microsoft
description: "Dans l’itération cinquième, nous faciliter notre application mettre à jour et modifier en ajoutant les tests unitaires. Nous simuler nos classes de modèle de données et générer des tests unitaires pour o..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2009
ms.topic: article
ms.assetid: 28ad8f80-b8a5-444e-b478-8b15a846060c
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-5-create-unit-tests-cs
msc.type: authoredcontent
ms.openlocfilehash: f9b2d05ec8756d68f6bd2f387c85faf03abd167e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="iteration-5--create-unit-tests-c"></a>Itération #5 : créer des tests unitaires (c#)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le Code](iteration-5-create-unit-tests-cs/_static/contactmanager_5_cs1.zip)

> Dans l’itération cinquième, nous faciliter notre application mettre à jour et modifier en ajoutant les tests unitaires. Nous simuler nos classes de modèle de données et générer des tests unitaires pour nos contrôleurs et la logique de validation.


## <a name="building-a-contact-management-aspnet-mvc-application-c"></a>Création d’une Application ASP.NET MVC de gestion des contacts (c#)

Dans cette série de didacticiels, nous générer une application de gestion des contacts entière à partir du début à la fin. L’application Gestionnaire de contacts permet de vous permet de stocker les informations de contact (des noms, des numéros de téléphone et adresses de messagerie) pour obtenir la liste des personnes.

Nous générer l’application sur plusieurs itérations. Avec chaque itération, nous améliorer progressivement l’application. L’objectif de cette approche itération plusieurs est pour vous permettre de comprendre la raison de chaque modification.

- Itération #1 - créer l’application. Dans la première itération, nous créons le Gestionnaire de Contact de la façon la plus simple possible. Prise en charge pour les opérations de base de données : création, lecture, mise à jour et supprimer (CRUD).

- Itération #2 : rendre des applications attrayantes. Dans cette itération, nous améliorer l’apparence de l’application en modifiant la valeur par défaut de page maître de vue ASP.NET MVC en cascade de feuille de style.

- Itération #3 - ajouter la validation de formulaire. Dans la troisième itération, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un formulaire sans terminer les champs obligatoires. Nous avons également valider que les adresses de messagerie et les numéros de téléphone.

- Itération #4 : rendre des applications faiblement couplées. Dans cette troisième itération, nous tirer parti de plusieurs modèles de conception de logiciel pour le rendre plus facile à gérer et modifier l’application Gestionnaire de contacts. Par exemple, nous refactoriser notre application pour utiliser le modèle de référentiel et le modèle d’Injection de dépendances.

- Itération #5 - créer des tests unitaires. Dans l’itération cinquième, nous faciliter notre application mettre à jour et modifier en ajoutant les tests unitaires. Nous simuler nos classes de modèle de données et générer des tests unitaires pour nos contrôleurs et la logique de validation.

- Itération #6 - utiliser le développement piloté par test. Dans cette itération sixième, nous ajouter de nouvelles fonctionnalités à notre application en écrivant des tests unitaires tout d’abord et d’écrire du code pour les tests unitaires. Dans cette itération, nous ajouter des groupes de contacts.

- Itération #7 - ajouter des fonctionnalités Ajax. Dans l’itération septième, nous améliorer la réactivité et les performances de votre application en ajoutant la prise en charge d’Ajax.


## <a name="this-iteration"></a>Cette itération

Dans l’itération précédente de l’application Gestionnaire de Contact, nous avons remanié l’application à être plus faiblement couplée. Nous séparées de l’application dans le contrôleur distincte, service et les couches de référentiel. Chaque couche interagit avec la couche situé sous celui-ci via des interfaces.

Nous avons remanié l’application pour rendre l’application plus facile à gérer et modifier. Par exemple, si nous avons besoin d’utiliser une nouvelle technologie d’accès aux données, nous pouvons simplement modifier la couche de référentiels sans toucher le contrôleur ou la couche de service. En effectuant le Gestionnaire de contacts faiblement couplés et nous ve apportées l’application plus fiable à modifier.

Mais que se passe-t-il si nous avons besoin d’ajouter une nouvelle fonctionnalité à l’application Gestionnaire de Contact ? Ou bien, que se passe-t-il quand nous corriger un bogue ? Un sad, mais également ayant fait leurs preuves, d’écriture de code est vrai chaque fois que vous sélectionnez le code que vous créez le risque d’introduire de nouveaux bogues.

Par exemple, un jour précis, votre gestionnaire peut-être vous demander d’ajouter une nouvelle fonctionnalité pour le Gestionnaire de contacts. Elle veut vous permet d’ajouter la prise en charge pour les groupes de Contact. Elle souhaite permettre aux utilisateurs d’organiser leurs contacts en groupes tels que vos amis, entreprise et ainsi de suite.

Pour implémenter cette nouvelle fonctionnalité, vous devez modifier les trois couches de l’application Gestionnaire de contacts. Vous devez ajouter de nouvelles fonctionnalités pour les contrôleurs, la couche de service et le référentiel. Dès que vous commencez à modifier le code, vous risquez d’endommager la fonctionnalité qui fonctionnait avant.

Refactorisation de notre application en couches distinctes, comme nous l’avons fait dans l’itération précédente, a une bonne chose. Il a une bonne chose, car il nous permet d’apporter des modifications à des couches entières sans toucher le reste de l’application. Toutefois, si vous souhaitez simplifier le code au sein d’une couche mettre à jour et modifier, vous devez créer des tests unitaires pour le code.

Vous utilisez une unité de test à une unité individuelle de code. Ces unités de code sont plus petites que les couches application dans son intégralité. En règle générale, vous utilisez un test unitaire pour vérifier si une méthode particulière dans votre code se comporte de la façon que vous attendez. Par exemple, vous devez créer un test unitaire pour la méthode CreateContact() exposé par la classe ContactManagerService.

Les tests unitaires pour un travail de l’application seulement comme un filet de sécurité. Chaque fois que vous modifiez le code dans une application, vous pouvez exécuter un ensemble de tests unitaires pour vérifier si la modification s’interrompt des fonctionnalités existantes. Tests unitaires rendent votre code sécurisé de modifier. Tests unitaires de rendre tous le code dans votre application plus fiable à modifier.

Dans cette itération, nous ajoutons des tests unitaires à notre application Gestionnaire de contacts. Ainsi, dans l’itération suivante, nous pouvons ajouter des groupes de Contact à notre application sans vous soucier de compromettre les fonctionnalités existantes.

> [!NOTE] 
> 
> Il existe une multitude de notamment NUnit, xUnit.net et MbUnit des infrastructures de tests unitaires. Dans ce didacticiel, nous utilisons l’unité test framework inclus avec Visual Studio. Toutefois, vous pouvez tout aussi facilement utiliser un de ces autres infrastructures.


## <a name="what-gets-tested"></a>Ce qui est testé

Dans le monde parfait, tout votre code sont couverts par les tests unitaires. Dans l’idéal, vous devez le filet de sécurité parfait. Vous serez en mesure de modifier n’importe quelle ligne de code dans votre application et savez instantanément, par l’exécution de vos tests unitaires, si la modification s’est arrêtée de fonctionnalités existantes.

Toutefois, nous n t en direct dans un monde parfait. Dans la pratique, lors de l’écriture de tests unitaires, vous concentrez sur l’écriture de tests pour votre logique métier (par exemple, une logique de validation). En particulier, vous *pas* écrire des tests unitaires pour vos données d’accès logique ou votre logique d’affichage.

Pour être utile, les tests unitaires doivent s’exécuter très rapidement. Vous pouvez facilement s’accumulent des centaines ou voire des milliers de tests unitaires pour une application. Si les tests unitaires prennent beaucoup de temps à exécuter, puis vous devez éviter de les exécuter. En d’autres termes, les tests unitaires longue sont inutiles pour le codage des jours.

Pour cette raison, vous en général, n’écrivez pas les tests unitaires pour le code qui interagit avec une base de données. En cours d’exécution des centaines de tests unitaires sur une base de données active soit trop lente. Au lieu de cela, vous simuler votre base de données et écrivez du code qui interagit avec la base de données fictive (nous aborderons simulation d’une base de données ci-dessous).

Pour des raisons similaires, en général, ne pas écrire les tests unitaires pour les vues. Pour tester une vue, vous devez activer un serveur web. Étant donné que la rotation d’un serveur web est un processus relativement lent, il n’est pas recommandé de création de tests unitaires pour les vues.

Si votre vue contient une logique complexe vous devez envisager le déplacement de la logique dans les méthodes d’assistance. Vous pouvez écrire des tests unitaires pour les méthodes d’assistance qui s’exécutent sans rotation d’un serveur web.

> [!NOTE] 
> 
> Bien que l’écriture de tests pour la logique d’accès aux données ou la logique d’affichage n’est pas une bonne idée lors de l’écriture de tests unitaires, ces tests peuvent être très utiles lors de la construction fonctionnelle ou l’intégration des tests.


> [!NOTE] 
> 
> ASP.NET MVC est le moteur d’affichage Web Forms. Alors que le moteur d’affichage Web Forms dépend d’un serveur web, les autres moteurs d’affichage ne peuvent pas être.


## <a name="using-a-mock-object-framework"></a>À l’aide d’une infrastructure d’objets fictifs

Lors de la génération des tests unitaires, vous devez presque toujours tirer parti d’une infrastructure de simuler un objet. Une infrastructure de simuler un objet vous permet de vous permet de créer des objets factices et des stubs pour les classes dans votre application.

Par exemple, vous pouvez utiliser une infrastructure de simuler un objet pour générer une version fictive de votre classe de référentiel. De cette façon, vous pouvez utiliser la classe de données de référentiel fictive au lieu de la classe de référentiel réel dans vos tests unitaires. Référentiel fictif permet d’éviter d’exécuter du code de base de données lors de l’exécution d’un test unitaire.

Visual Studio n’inclut pas d’une infrastructure de simuler un objet. Toutefois, il existe plusieurs infrastructures de simuler un objet commerciales et open source pour le .NET framework :

1. Moq - cette infrastructure est disponible sous licence BSD open source. Vous pouvez télécharger Moq de [https://code.google.com/p/moq/](https://code.google.com/p/moq/).
2. Rhino Mocks, cette infrastructure est disponible sous licence BSD open source. Vous pouvez télécharger Rhino Mocks de [http://ayende.com/projects/rhino-mocks.aspx](http://ayende.com/projects/rhino-mocks.aspx).
3. Typemock isolant - il s’agit d’un cadre commercial. Vous pouvez télécharger une version d’évaluation de [http://www.typemock.com/](http://www.typemock.com/).

Dans ce didacticiel, j’ai décidé d’utiliser Moq. Toutefois, vous pouvez utiliser tout aussi facilement Rhino Mocks ou isolant Typemock pour créer le fictifs objets pour l’application Gestionnaire de contacts.

Avant de pouvoir utiliser Moq, vous devez procédez comme suit :

1. .
2. Avant de vous décompressez le téléchargement, assurez-vous que vous cliquez sur le fichier et cliquez sur le bouton **Unblock** (voir Figure 1).
3. Décompressez le téléchargement.
4. Ajouter une référence à l’assembly Moq en double-cliquant sur le dossier références dans le projet ContactManager.Tests et en sélectionnant **ajouter une référence**. Sous l’onglet Parcourir, accédez au dossier où vous avez décompressé Moq et sélectionnez l’assembly Moq.dll. Cliquez sur le **OK** bouton.
5. Après avoir effectué ces étapes, votre dossier références doit ressembler à la Figure 2.


[![Le déblocage Moq](iteration-5-create-unit-tests-cs/_static/image1.jpg)](iteration-5-create-unit-tests-cs/_static/image1.png)

**Figure 01**: Moq déblocage ([cliquez pour afficher l’image en taille réelle](iteration-5-create-unit-tests-cs/_static/image2.png))


[![Références après l’ajout de Moq](iteration-5-create-unit-tests-cs/_static/image2.jpg)](iteration-5-create-unit-tests-cs/_static/image3.png)

**Figure 02**: références après l’ajout de Moq ([cliquez pour afficher l’image en taille réelle](iteration-5-create-unit-tests-cs/_static/image4.png))


## <a name="creating-unit-tests-for-the-service-layer"></a>Création de Tests unitaires pour la couche de Service

Permettent de commencer par créer un ensemble de tests unitaires pour notre couche de service de gestionnaire de contacts application s s. Nous allons utiliser ces tests pour vérifier notre logique de validation.

Créer un nouveau dossier nommé modèles dans le projet ContactManager.Tests. Ensuite, cliquez sur le dossier Modèles et sélectionnez **ajouter, nouveau Test**. Le **ajouter un nouveau Test** boîte de dialogue affichée dans la Figure 3 s’affiche. Sélectionnez le **de Test unitaire** modèle et le nom de votre nouveau test ContactManagerServiceTest.cs. Cliquez sur le **OK** pour ajouter votre nouveau test à votre projet de Test.

> [!NOTE] 
> 
> En général, vous souhaitez que la structure de dossiers de votre projet de Test pour correspondre à la structure de dossiers de votre projet ASP.NET MVC. Par exemple, vous placez des tests du contrôleur dans un dossier contrôleurs, les tests de modèle dans un dossier de modèles et ainsi de suite.


[![Models\ContactManagerServiceTest.cs](iteration-5-create-unit-tests-cs/_static/image3.jpg)](iteration-5-create-unit-tests-cs/_static/image5.png)

**Figure 03**: Models\ContactManagerServiceTest.cs ([cliquez pour afficher l’image en taille réelle](iteration-5-create-unit-tests-cs/_static/image6.png))


Au départ, nous souhaitons tester la méthode CreateContact() exposée par la classe ContactManagerService. Nous allons créer les cinq tests suivants :

- CreateContact() - Tests que CreateContact() retourne la valeur true quand un Contact valide est passé à la méthode.
- CreateContactRequiredFirstName() - vérifie qu’un message d’erreur est ajouté à l’état de modèle lorsqu’un Contact avec un prénom manquant est passé à la méthode CreateContact().
- CreateContactRequredLastName() - vérifie qu’un message d’erreur est ajouté à l’état de modèle lorsqu’un Contact avec un nom de famille manquant est passé à la méthode CreateContact().
- CreateContactInvalidPhone() - vérifie qu’un message d’erreur est ajouté à l’état de modèle lorsqu’un Contact avec un numéro de téléphone non valide est passé à la méthode CreateContact().
- CreateContactInvalidEmail() - Tests qu’un message d’erreur est ajouté à l’état de modèle lorsqu’un Contact avec une adresse de messagerie non valide est passé à la méthode CreateContact()...

Le premier test vérifie qu’un Contact valide ne génère pas d’une erreur de validation. Les tests restants vérifient chacun des règles de validation.

Le code pour ces tests est contenu dans la liste 1.

**La liste 1 - Models\ContactManagerServiceTest.cs**

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample1.cs)]


Étant donné que nous utilisons la classe de Contact dans la liste 1, nous devons ajouter une référence à Entity Framework Microsoft à notre projet de Test. Ajoutez une référence à l’assembly System.Data.Entity.


La liste 1 contient une méthode nommée Initialize() est décorée avec l’attribut [TestInitialize]. Cette méthode est appelée automatiquement avant l’exécution de chacun des tests unitaires (elle est appelée 5 fois juste avant chaque test unitaire). La méthode Initialize() crée un référentiel fictif avec la ligne de code suivante :

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample2.cs)]

Cette ligne de code utilise l’infrastructure de Moq pour générer un référentiel fictif à partir de l’interface IContactManagerRepository. Le référentiel fictif est utilisé au lieu du EntityContactManagerRepository réel pour éviter l’accès à la base de données lors de chaque test unitaire est exécuté. Les données de référentiel fictive implémentant les méthodes de l’interface IContactManagerRepository, mais les méthodes don pas réellement à le faire quoi que ce soit.

> [!NOTE] 
> 
> Lorsque vous utilisez l’infrastructure Moq, il existe une distinction entre \_mockRepository et \_mockRepository.Object. La première fait référence à la fictifs&lt;IContactManagerRepository&gt; classe qui contient des méthodes permettant de spécifier le comporte de référentiel fictif. Ce dernier fait référence au référentiel fictif réel qui implémente l’interface IContactManagerRepository.


Le référentiel fictif est utilisé dans la méthode Initialize() lors de la création d’une instance de la classe ContactManagerService. Tous les tests unitaires individuels d’utiliseront cette instance de la classe ContactManagerService.

La liste 1 contient les cinq méthodes qui correspondent à chacun des tests unitaires. Chacune de ces méthodes est décorée avec l’attribut [TestMethod]. Lorsque vous exécutez les tests unitaires, toute méthode qui possède cet attribut est appelée. En d’autres termes, toute méthode qui est décorée avec l’attribut [TestMethod] est un test unitaire.

Le premier test unitaire, nommé CreateContact(), vérifie que l’appelant CreateContact() renvoie la valeur true quand une instance valide de la classe de Contact est passée à la méthode. Le test crée une instance de la classe de Contact, appelle la méthode CreateContact() et vérifie que CreateContact() retourne la valeur true.

Les tests restants vérifient que la lorsque la méthode CreateContact() est appelée avec un Contact valide ensuite la méthode retourne la valeur false et le message d’erreur de validation attendu est ajouté à l’état du modèle. Par exemple, le test CreateContactRequiredFirstName() crée une instance de la classe de Contact avec une chaîne vide pour la propriété de son prénom. Ensuite, la méthode CreateContact() est appelée avec le Contact non valide. Enfin, le test vérifie que CreateContact() retourne la valeur false et l’état du modèle contenant le message d’erreur de validation attendu « prénom est obligatoire. »

Vous pouvez exécuter les tests unitaires dans la liste 1 en sélectionnant l’option de menu **série de tests, tous les Tests de la Solution (CTRL + R, A)**. Les résultats des tests sont affichés dans la fenêtre Résultats des tests (voir Figure 4).


[![Résultats des tests](iteration-5-create-unit-tests-cs/_static/image4.jpg)](iteration-5-create-unit-tests-cs/_static/image7.png)

**Figure 04**: résultats de Test ([cliquez pour afficher l’image en taille réelle](iteration-5-create-unit-tests-cs/_static/image8.png))


## <a name="creating-unit-tests-for-controllers"></a>Création de Tests unitaires pour les contrôleurs

Application de ASP.NETMVC contrôler le flux d’interaction de l’utilisateur. Quand un contrôleur de test, que vous souhaitez vérifier si le contrôleur retourne l’action correcte résultat et afficher les données. Vous pouvez également vérifier si un contrôleur interagit avec les classes de modèle de la manière attendue.

Par exemple, le Listing 2 contient deux tests unitaires pour le contrôleur de Contact méthode Create(). Le premier test unitaire vérifie que lorsqu’un Contact valide est passé à la méthode Create() ensuite la méthode Create() redirige vers l’action de l’Index. En d’autres termes, lorsqu’il est passé d’un Contact valide, la méthode Create() doit retourner un RedirectToRouteResult qui représente l’action de l’Index.

Nous n t que vous souhaitez tester la couche de service ContactManager quand nous testons la couche de contrôleur. Par conséquent, nous simuler la couche de service avec le code suivant dans la méthode Initialize :

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample3.cs)]

Dans le test unitaire CreateValidContact(), nous simuler le comportement de l’appel de méthode CreateContact() avec la ligne suivante de code de la couche de service :

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample4.cs)]

Cette ligne de code, le service de ContactManager fictif retourner la valeur true lorsque sa méthode CreateContact() est appelée. Par la simulation de la couche de service, nous pouvons tester le comportement de notre contrôleur sans avoir à exécuter le code dans la couche de service.

Le deuxième test unitaire vérifie que l’action Create() retourne la vue à créer lors d’un contact non valide est passé à la méthode. Nous entraîner la couche de service CreateContact() méthode retourne la valeur false à la ligne de code suivante :

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample5.cs)]

Si la méthode Create() se comporte comme prévu elle doit retourner la vue à créer lors de la couche de service renvoie la valeur false. De cette façon, le contrôleur peut afficher les messages d’erreur de validation dans la vue de la création et l’utilisateur a la possibilité de corriger ce Contact de propriétés non valides.


Si vous envisagez de générer des tests unitaires pour les contrôleurs vous devez retourner les noms des vues explicite à partir de vos actions de contrôleur. Par exemple, ne retournent pas d’une vue comme suit :

retourner View() ;

De retour à la place, la vue comme suit :

retourner View("Create") ;

Si vous n’êtes pas explicite lors du retour d’une vue de la propriété ViewResult.ViewName retourne une chaîne vide.


**Listing 2 - Controllers\ContactControllerTest.cs**

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample6.cs)]

## <a name="summary"></a>Résumé

Dans cette itération, nous avons créé des tests unitaires pour notre application Gestionnaire de contacts. Nous pouvons exécuter ces tests unitaires à tout moment pour vérifier que votre application se comporte toujours de la manière que nous espérons. Les tests unitaires agissent comme un filet de sécurité pour notre application nous permettant de modifier en toute sécurité de notre application à l’avenir.

Nous avons créé deux jeux de tests unitaires. Tout d’abord, nous avons testé notre logique de validation en créant des tests unitaires pour la couche de service. Ensuite, nous avons testé notre logique de contrôle de flux en créant des tests unitaires pour notre couche contrôleur. Lors du test de la couche de service, nous isolé nos tests pour nos couche de service à partir de la couche de notre référentiel par simulation couche de notre référentiel. Lorsque la couche de contrôleur de test, nous isolé nos tests pour nos couche contrôleur par simulation de la couche de service.

Dans l’itération suivante, nous modifions l’application Gestionnaire de contacts afin qu’il prend en charge les groupes de Contact. Nous allons ajouter cette nouvelle fonctionnalité à notre application à l’aide d’un processus de conception de logiciel appelé développement piloté par test.

>[!div class="step-by-step"]
[Précédent](iteration-4-make-the-application-loosely-coupled-cs.md)
[Suivant](iteration-6-use-test-driven-development-cs.md)
