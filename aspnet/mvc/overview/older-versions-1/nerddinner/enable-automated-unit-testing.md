---
uid: mvc/overview/older-versions-1/nerddinner/enable-automated-unit-testing
title: "Activer automatisée de tests unitaires | Documents Microsoft"
author: microsoft
description: "Étape 12 montre comment développer une suite de tests unitaires automatisés pour vérifier nos fonctionnalités de NerdDinner, et qui donne la confiance pour apporter des modifications..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: a19ff2ce-3f7e-4358-9a51-a1403da9c63e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/enable-automated-unit-testing
msc.type: authoredcontent
ms.openlocfilehash: 1a4258054d90b2d5bcc06a63fb6f3b4673a4837d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="enable-automated-unit-testing"></a>Activer le test unitaire automatisé
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 12 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 12 montre comment développer une suite de tests unitaires automatisés pour vérifier nos fonctionnalités de NerdDinner, et qui donne la confiance pour apporter des modifications et améliorations apportées à l’application dans le futur.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-12-unit-testing"></a>NerdDinner étape 12 : Tests unitaires

Nous allons développer une suite de tests unitaires automatisés pour vérifier nos fonctionnalités de NerdDinner, et qui donne la confiance pour apporter des modifications et améliorations apportées à l’application dans le futur.

### <a name="why-unit-test"></a>Pourquoi de Test unitaire ?

Sur le lecteur en une journée de travail, vous avez un disque mémoire flash soudain d’inspiration sur une application que vous utilisez. Vous réalisez une modification est que vous pouvez implémenter qui permettront à l’application améliorera considérablement. Il peut être une refactorisation qui nettoie le code, ajoute une nouvelle fonctionnalité ou qui corrige un bogue.

La question qui vous confronts lorsque vous arrivez à votre ordinateur est : « mode sans échec en faire de cette amélioration ? » Que se passe-t-il si la modification a des effets secondaires ou s’arrête quelque chose ? La modification peut être simple et prendra que quelques minutes à implémenter, mais que se passe-t-il s’il faut heures pour tester manuellement tous les scénarios d’application ? Que se passe-t-il si vous oubliez couvrir un scénario et une application interrompue mise en production ? Cette amélioration vraiment toutes la peine fait ?

Des tests unitaires automatisés peuvent fournir un filet de sécurité qui vous permet d’améliorer en permanence de vos applications et éviter d’être peur du code que vous utilisez. Avoir automatisée des tests qui vérifient rapidement la fonctionnalité vous permet de code en toute confiance – et vous aider à apporter des améliorations, vous pouvez sinon pas avoir effectuée en cours. Ils aident également à créer des solutions qui sont plus faciles à gérer et ont une durée de vie plus longue - qui entraîne un meilleur retour sur investissement.

L’infrastructure ASP.NET MVC rend simple et naturelle à la fonctionnalité de l’application test unitaire. Il permet également d’un flux de travail de développement de pilotée par les tests (TDD) qui permet le développement en fonction de test en premier.

### <a name="nerddinnertests-project"></a>NerdDinner.Tests projet

Lorsque nous avons créé notre application NerdDinner au début de ce didacticiel, nous avons invités avec une boîte de dialogue vous demandant si nous voulons créer un projet de test unitaire pour accéder en même temps que le projet d’application :

![](enable-automated-unit-testing/_static/image1.png)

Nous avons conservé la « Oui, créer un projet de test unitaire » case sélectionnée, ce qui a entraîné un projet de « NerdDinner.Tests » ajouté à notre solution :

![](enable-automated-unit-testing/_static/image2.png)

Le projet NerdDinner.Tests fait référence à l’assembly de projet d’application de NerdDinner et nous permet d’ajouter facilement des tests automatisés qui vérifient les fonctionnalités de l’application.

### <a name="creating-unit-tests-for-our-dinner-model-class"></a>Création de Tests unitaires pour notre classe de modèle dîner

Vous allez ajouter des tests à notre projet NerdDinner.Tests qui permettent de vérifier la classe Dinner que nous avons créé lorsque nous avons créé la couche de notre modèle.

Nous allons commencer en créant un nouveau dossier dans notre projet de test appelée « Modèles » où nous allons placer nos tests modèle lié. Nous allons ensuite avec le bouton droit sur le dossier et choisissez le **Add -&gt;nouveau Test** commande de menu. La boîte de dialogue « Ajouter un nouveau Test » s’affiche.

Nous allons choisir créer un « Test unitaire » et le nommer « DinnerTest.cs » :

![](enable-automated-unit-testing/_static/image3.png)

Lorsque vous cliquez sur le bouton « OK » Visual Studio ajoute (et l’ouvre) un fichier DinnerTest.cs au projet :

![](enable-automated-unit-testing/_static/image4.png)

Le modèle de test unitaire Visual Studio par défaut comporte beaucoup de maquette code que je trouve un peu confu. Nous allons nettoyer simplement contenir le code ci-dessous :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample1.cs)]

L’attribut [TestClass] sur la classe DinnerTest ci-dessus identifie comme une classe qui contiendra les tests, ainsi que l’initialisation de test facultatif et code de destruction. Nous pouvons définir les tests qu’il contient en ajoutant des méthodes publiques qui ont un attribut [TestMethod] sur ces derniers.

Voici la première des deux tests, nous allons ajouter qui exercent notre classe dîner. Le premier test vérifie que notre dîner n’est pas valide si un nouveau dîner est créé sans que toutes les propriétés sont correctement définies. Le deuxième test vérifie que notre dîner est valide lorsqu’un dîner possède toutes les propriétés définies avec les valeurs valides :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample2.cs)]

Vous remarquerez que ci-dessus que notre test les noms sont très explicite (et quelque peu détaillée). Ce faisant, nous allons étant donné que nous pouvons mettre fin à des centaines, voire des milliers de tests peu de créer, et nous permet de déterminer rapidement l’objectif et le comportement de chacun d’eux (en particulier lorsque nous recherchons dans une liste d’erreurs dans un test runner). Les noms de test doivent être nommés d’après les fonctionnalités qu’ils testent. Ci-dessus, nous utilisons un « nom\_doit\_verbe « modèle d’affectation de noms.

Nous sommes structurer les tests à l’aide de tests pattern – ce qui signifie « Arrange, Act, Assert » « AAA » :

- Réorganiser : Le programme d’installation l’unité en cours de test
- ACT : Testez le test unitaire et capturer les résultats
- Assertion : Vérifier le comportement

Écriture des tests, que nous voulons éviter que les tests individuels faire trop. Au lieu de cela, chaque test doit vérifier un concept unique (ce qui rend beaucoup plus facile d’identifier la cause des échecs). Il est recommandé de tenter disposent uniquement d’une seule instruction pour chaque test d’assertion. Si vous avez plus d’une instruction dans une méthode de test d’assertion, assurez-vous qu’ils sont tous est utilisés pour tester le même concept. En cas de doute, vérifiez un autre test.

### <a name="running-tests"></a>Exécution des tests

Visual Studio 2008 Professional (et versions supérieures) incluent un testeur intégré qui peut être utilisé pour exécuter des projets de tests unitaires Visual Studio dans l’IDE. Nous pouvons sélectionner la **Test -&gt;exécuter -&gt;tous les Tests de la Solution** menu command (ou type Ctrl R, A) pour exécuter l’ensemble de nos tests unitaires. Ou vous pouvez également nous pouvons notre curseur est placé dans une méthode de test ou de la classe de test spécifiques et utiliser le **Test -&gt;exécuter -&gt;Tests dans le contexte actuel** commande de menu (ou type Ctrl R, T) pour exécuter un sous-ensemble des tests unitaires.

Nous allons notre curseur est placé dans la classe DinnerTest et tapez « Ctrl R, T » pour exécuter les deux des tests que nous venons de définir. Lorsque vous faire une fenêtre « Résultats de Test » s’affiche dans Visual Studio et nous verrons les résultats de nos tests répertoriés dans celui-ci :

![](enable-automated-unit-testing/_static/image5.png)

*Remarque : La fenêtre Résultats des tests Visual Studio n’affiche pas la colonne nom de la classe par défaut. Vous pouvez ajouter cela en cliquant dans la fenêtre Résultats des tests et à l’aide de la commande de menu Ajouter/supprimer des colonnes.*

Nos deux tests a duré qu’une fraction de seconde à exécuter et que vous pouvez voir les deux passés. Nous pouvons maintenant passer et les enrichir en créant des tests supplémentaires pour vérifier les validations de règle spécifique, ainsi que pour couvrent les deux méthodes d’assistance - IsUserHost() et IsUserRegisterd() – que nous avons ajouté à la classe Dinner. Absence de tous ces tests pour la classe Dinner rend beaucoup plus facile et plus sûr d’ajouter des règles d’entreprise et des validations à ce dernier dans le futur. Nous pouvons ajouter notre nouvelle logique de règle à dîner et puis vérifiez qu’elle n’a pas encore scindée un de nos fonctionnalités logique précédente en quelques secondes.

Notez comment à l’aide d’un nom descriptif test permet de facilement comprendre rapidement ce que chaque test consiste à vérifier. Je vous recommandons d’utiliser le **Tools -&gt;Options** commande de menu, ouvrir les outils de Test -&gt;écran de configuration d’exécution de tests et la vérification du » en double-cliquant sur un résultat de test unitaire Échec ou non concluant affiche case à cocher du point de défaillance dans le test ». Cela vous permettra de double-cliquer sur une erreur dans la fenêtre Résultats des tests et passer immédiatement à l’échec d’assertion.

### <a name="creating-dinnerscontroller-unit-tests"></a>Création de Tests unitaires de DinnersController

Nous allons maintenant créer des tests unitaires qui vérifient les fonctionnalités de notre DinnersController. Nous allons démarrer en cliquant sur le dossier « Contrôleurs » dans notre projet de Test, puis choisissez le **Add -&gt;nouveau Test** commande de menu. Nous allons créer un « Test unitaire » et nommez-le « DinnersControllerTest.cs ».

Nous allons créer deux méthodes de test qui vérifient la méthode d’action Details() sur le DinnersController. Le premier vérifie qu’une vue est retournée lorsqu’un dîner existant est demandé. Le second vérifie qu’une vue « NotFound » est retournée lorsqu’un dîner inexistante est demandé :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample3.cs)]

Le code ci-dessus compile le nettoyage. Si nous exécutons les tests, cependant, tous les deux échouent :

![](enable-automated-unit-testing/_static/image6.png)

Si vous examinez les messages d’erreur, vous verrez que les tests ont échoué était dû car notre classe DinnersRepository n’a pas pu se connecter à une base de données. Notre application NerdDinner utilise une chaîne de connexion dans un fichier local SQL Server Express qui se trouve sous le \App\_répertoire de données du projet d’application de NerdDinner. Étant donné que notre projet NerdDinner.Tests compile et s’exécute dans un autre répertoire puis le projet d’application, l’emplacement de chemin d’accès relatif de la chaîne de connexion est incorrect.

Nous *impossible* résoudre ce problème en copiant le fichier de base de données SQL Express à notre projet de test, puis ajoutez une chaîne de connexion de test appropriés à ce dernier dans le fichier App.config de votre projet de test. Cela obtiendriez les tests ci-dessus débloqué et en cours d’exécution.

Tests unitaires de code à l’aide d’une base de données réel, cependant, offre un certain nombre de défis. Plus précisément :

- Cela ralentit considérablement le temps d’exécution de tests unitaires. Plus nécessaire pour exécuter des tests, vous êtes susceptible de moins pour les exécuter fréquemment. Dans l’idéal, vous souhaitez que vos tests unitaires pour pouvoir être exécuté en secondes et qu’elle soit chose comme naturellement en tant que la compilation du projet.
- Elle complique la logique de configuration et de nettoyage au sein de tests. Vous souhaitez que chaque test unitaire isolé et indépendant des autres (sans effets secondaires ou avec dépendances). Lorsque vous travaillez sur une base de données réelle, que vous devez penser d’état et de le réinitialiser entre les tests.

Examinons un modèle de conception appelé « injection de dépendance » qui peut nous aider à contourner ces problèmes et d’éviter la nécessité d’utiliser une base de données réel avec nos tests.

### <a name="dependency-injection"></a>Injection de dépendance

Maintenant DinnersController est « étroitement » à la classe DinnerRepository. « COUPLAGE » fait référence à une situation où une classe explicitement s’appuie sur une autre classe afin de fonctionner :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample4.cs)]

Étant donné que la classe DinnerRepository nécessite l’accès à une base de données, la dépendance étroitement couplée la classe DinnersController a aux extrémités DinnerRepository demande à une base de données dans l’ordre pour les méthodes d’action DinnersController à tester.

Nous pouvons contourner ce problème en utilisant un modèle de conception appelé « injection de dépendances : « ce qui constitue une approche où les dépendances (comme les classes de référentiel qui fournissent l’accès aux données) n’est plus implicitement créées dans les classes qui les utilisent. Au lieu de cela, des dépendances peuvent être explicitement transmis à la classe qui les utilise à l’aide des arguments de constructeur. Si les dépendances sont définies à l’aide des interfaces, nous avons ensuite la possibilité de passer dans les implémentations de la dépendance « faux » pour des scénarios de test unitaire. Cela permet de créer des implémentations de dépendance spécifiques aux tests qui ne nécessitent pas réellement d’accès à une base de données.

Pour le voir en action, nous allons implémenter l’injection de dépendance avec notre DinnersController.

#### <a name="extracting-an-idinnerrepository-interface"></a>Extraction d’une interface IDinnerRepository

La première étape est de créer une nouvelle interface IDinnerRepository qui encapsule le contrat de référentiel que nos contrôleurs nécessitent pour récupérer et mettre à jour préparés.

Nous pouvons définir ce contrat d’interface manuellement en cliquant sur le dossier \Models, puis en choisissant le **Add -&gt;un nouvel élément** commande de menu et la création d’une nouvelle interface nommée IDinnerRepository.cs.

Nous pouvons également utiliser l’extraction de refactorisation outils intégrés à Visual Studio Professional (et versions supérieures) à automatiquement et créer une interface pour nous de notre classe DinnerRepository existant. Pour extraire cette interface à l’aide de Visual Studio, simplement placer le curseur de l’éditeur de texte sur la classe DinnerRepository, puis avec le bouton droit et sélectionnez le **refactoriser -&gt;extraire l’Interface** commande de menu :

![](enable-automated-unit-testing/_static/image7.png)

Cela lancer la boîte de dialogue « Extraire l’Interface » et nous demander le nom de l’interface à créer. Il sera par défaut IDinnerRepository et sélectionner automatiquement toutes les méthodes publiques sur la classe DinnerRepository existante à ajouter à l’interface :

![](enable-automated-unit-testing/_static/image8.png)

Lorsque vous cliquez sur le bouton « OK », Visual Studio ajoute une nouvelle interface IDinnerRepository à notre application :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample5.cs)]

Et notre classe DinnerRepository existante sera mise à jour afin qu’elle implémente l’interface :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample6.cs)]

#### <a name="updating-dinnerscontroller-to-support-constructor-injection"></a>DinnersController mise à jour pour prendre en charge d’injection de constructeurs

Nous allons maintenant mettre à jour de la classe DinnersController pour utiliser la nouvelle interface.

Actuellement DinnersController est codé en dur telles que son champ « dinnerRepository » est toujours une classe DinnerRepository :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample7.cs)]

Nous allons le modifier afin que le champ « dinnerRepository » est de type IDinnerRepository au lieu de DinnerRepository. Nous allons ensuite ajouter deux constructeurs DinnersController publics. Un des constructeurs permet une IDinnerRepository à passer en tant qu’argument. L’autre est un constructeur par défaut qui utilise notre implémentation DinnerRepository existante :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample8.cs)]

Étant donné que ASP.NET MVC par défaut crée des classes de contrôleur à l’aide des constructeurs par défaut, notre DinnersController lors de l’exécution continue à utiliser la classe DinnerRepository pour accéder aux données.

Nous pouvons maintenant mettre à jour de nos tests unitaires, cependant, pour passer dans une implémentation de référentiel dinner « faux » en utilisant le paramètre de constructeur. Ce référentiel dinner « faux » ne nécessite pas d’accès à une base de données réel et à la place utilise les données d’exemple en mémoire.

#### <a name="creating-the-fakedinnerrepository-class"></a>Création de la classe FakeDinnerRepository

Nous allons créer une classe FakeDinnerRepository.

Nous commencez par créer un répertoire « Fakes » dans notre projet NerdDinner.Tests et puis ajoutez une nouvelle classe FakeDinnerRepository lui (avec le bouton droit sur le dossier et choisissez **Add -&gt;nouvelle classe**) :

![](enable-automated-unit-testing/_static/image9.png)

Nous allons mettre à jour le code afin que la classe FakeDinnerRepository implémente l’interface IDinnerRepository. Nous pouvons ensuite avec le bouton droit dessus et choisissez la commande de menu contextuel « Implémenter l’interface IDinnerRepository » :

![](enable-automated-unit-testing/_static/image10.png)

Cela entraîne de Visual Studio pour tous les membres d’interface IDinnerRepository ajouter automatiquement à la classe FakeDinnerRepository avec les implémentations par défaut « stub out » :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample9.cs)]

Nous pouvons puis mettre à jour l’implémentation FakeDinnerRepository pour travailler sur une liste en mémoire&lt;Dinner&gt; collection passée comme un argument de constructeur :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample10.cs)]

Nous disposons désormais d’une implémentation IDinnerRepository factice qui ne nécessite pas d’une base de données et peut fonctionner à la place une liste en mémoire des objets de Dinner.

#### <a name="using-the-fakedinnerrepository-with-unit-tests"></a>À l’aide de la FakeDinnerRepository avec des Tests unitaires

Revenons aux tests unitaires DinnersController qui a échoué précédemment, car la base de données n’était pas disponible. Nous pouvons mettre à jour les méthodes de test pour utiliser un FakeDinnerRepository rempli avec des données de dîner exemple en mémoire pour le DinnersController en utilisant le code ci-dessous :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample11.cs)]

Et si nous exécutons ces tests qu’elles réussissent maintenant :

![](enable-automated-unit-testing/_static/image11.png)

De plus, elles nécessitent qu’une fraction de seconde à exécuter et ne nécessitent pas une logique complexe d’installation/nettoyage. Nous pouvons maintenant de test unitaire tous notre code de la méthode action DinnersController (y compris la liste, la pagination, plus d’informations, créer, mettre à jour et supprimer) sans avoir à se connecter à une base de données réel.

| **Côté rubrique : Infrastructures de d’Injection de dépendance** |
| --- |
| Exécution d’injection de dépendance manuelle (par exemple, nous sommes ci-dessus) fonctionne correctement, mais devient-elle plus difficile à gérer en tant que le nombre de dépendances et les composants d’une application augmente. Plusieurs infrastructures d’injection de dépendance existent pour .NET qui peuvent aider à fournir davantage souplesse de gestion de dépendance. Ces infrastructures, également appelés des conteneurs de « Inversion de contrôle » (IoC), fournissent des mécanismes qui permettent à un niveau supplémentaire de prise en charge de configuration pour spécifier et en passant des dépendances pour les objets lors de l’exécution (généralement à l’aide d’injection de constructeur ). Parmi les plus populaires Injection de dépendances OSS / incluent des infrastructures d’inversion de contrôle dans .NET : AutoFac, Ninject, Spring.NET, StructureMap et Windsor. Expose les API qui permettent aux développeurs de participer à la résolution et l’instanciation des contrôleurs et qui permet l’Injection de dépendances d’extensibilité ASP.NET MVC / infrastructures d’inversion de contrôle pour être correctement intégré au sein de ce processus. À l’aide d’une infrastructure de ce type est également nous permettent de supprimer le constructeur par défaut à partir de notre DinnersController – supprimer complètement le couplage entre lui-même et le DinnerRepositorys. Nous n’utiliserez pas une injection de dépendances / framework IOC avec notre application NerdDinner. Mais il est quelque chose que nous avons prendre en compte pour l’avenir si les fonctionnalités de NerdDinner-base de code a fait l’objet. |

### <a name="creating-edit-action-unit-tests"></a>Création de Tests unitaires de Action Edition

Nous allons maintenant créer des tests unitaires qui permettent de vérifier la fonctionnalité d’édition de la DinnersController. Nous allons commencer par tester la version HTTP-GET de notre modifier l’action :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample12.cs)]

Nous allons créer un test qui vérifie qu’une vue soutenue par un objet DinnerFormViewModel est rendue à un dîner valid est demandé :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample13.cs)]

Lorsque nous exécutons le test, cependant, nous trouverez qu’il échoue, car une exception NullReferenceException est levée lorsque la méthode Edit accède à la propriété User.Identity.Name pour effectuer la vérification de Dinner.IsHostedBy().

L’objet utilisateur sur la classe de base de contrôleur contient des informations sur l’utilisateur connecté et est remplie par ASP.NET MVC lorsqu’il crée le contrôleur lors de l’exécution. Étant donné que nous testons la DinnersController en dehors d’un environnement de serveur web, l’objet utilisateur n’est pas défini (par conséquent, l’exception de référence null).

### <a name="mocking-the-useridentityname-property"></a>Simulation de la propriété User.Identity.Name

Infrastructures factices faciliter le test en permettant de créer dynamiquement des versions fausses des objets dépendants qui prennent en charge de nos tests. Par exemple, nous pouvons utiliser une infrastructure mocking dans notre test d’action de modification pour créer dynamiquement un objet utilisateur que notre DinnersController peut utiliser pour rechercher un nom d’utilisateur simulé. Cela permet d’éviter une référence null ne soit levée si nous exécutons notre test.

Il existe de nombreuses .NET simulation des infrastructures qui peuvent être utilisés avec ASP.NET MVC (vous pouvez voir une liste ici : [http://www.mockframeworks.com/](http://www.mockframeworks.com/)). Test de notre application NerdDinner que nous allons utiliser une open source simulation infrastructure appelée « Moq », qui peut être téléchargé gratuitement à partir de [http://www.mockframeworks.com/moq](http://www.mockframeworks.com/moq).

Une fois téléchargé, nous allons ajouter une référence dans notre projet NerdDinner.Tests à l’assembly Moq.dll :

![](enable-automated-unit-testing/_static/image12.png)

Nous allons ensuite ajouter une méthode d’assistance de « CreateDinnersControllerAs(username) » à notre classe de test qui prend un nom d’utilisateur comme paramètre et qui, puis la propriété User.Identity.Name sur l’instance DinnersController « mocks » :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample14.cs)]

Ci-dessus, nous utilisons Moq pour créer un objet fictifs que fakes un objet ControllerContext (qui est ce que ASP.NET MVC passe aux classes de contrôleur d’exposer des objets runtime tels que l’utilisateur, de demande, de réponse et de Session). Nous appelons la méthode « SetupGet » sur l’objet fictif pour indiquer que la propriété HttpContext.User.Identity.Name sur ControllerContext doit retourner la chaîne de nom d’utilisateur que nous avons passés à la méthode d’assistance.

Nous pouvons simuler un nombre quelconque de ControllerContext propriétés et méthodes. Pour illustrer ceci, j’ai également ajouté un appel SetupGet() pour la propriété Request.IsAuthenticated (qui n’est pas réellement nécessaire pour les tests ci-dessous, mais qui permet d’illustrer comment vous pouvez simuler des propriétés de la demande). Lorsque nous avons terminé nous assigner une instance de l’objet factice ControllerContext à la DinnersController notre méthode d’assistance est retournée.

Nous pouvons désormais écrire des tests unitaires qui utilisent cette méthode d’assistance pour modifier les scénarios impliquant des différents utilisateurs de test :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample15.cs)]

Et si nous exécutons les tests qu’ils réussissent maintenant :

![](enable-automated-unit-testing/_static/image13.png)

### <a name="testing-updatemodel-scenarios"></a>Scénarios de test UpdateModel()

Nous avons créé des tests qui couvrent la version HTTP-GET de l’action de modification. Nous allons maintenant créer des tests qui vérifient la version HTTP-POST de l’action de modification :

[!code-csharp[Main](enable-automated-unit-testing/samples/sample16.cs)]

Le scénario de test nouveau intéressant pour nous prendre en charge avec cette méthode d’action est l’utilisation de la méthode d’assistance UpdateModel() sur la classe de base du contrôleur. Nous utilisons cette méthode d’assistance pour lier des valeurs de publication de formulaire à notre instance d’objet dîner.

Voici deux tests qui montre comment nous puissions fournir le formulaire publié des valeurs pour la méthode d’assistance UpdateModel() à utiliser. Nous allons faire cela en créant et en remplissant un objet FormCollection et assigner à la propriété « ValueProvider » sur le contrôleur.

Le premier test vérifie qu’une sauvegarde réussie, le navigateur est redirigé vers l’action détails. Le deuxième test vérifie que lors de la validation d’entrée non valide l’action actualise l’affichage de modifier à nouveau avec un message d’erreur.


[!code-csharp[Main](enable-automated-unit-testing/samples/sample17.cs)]

### <a name="testing-wrap-up"></a>Synthèse de test

Nous avons couvert les concepts principaux impliqués dans les classes de contrôleur de test unitaire. Nous pouvons utiliser ces techniques pour créer facilement des centaines de tests simples qui vérifient le comportement de notre application.

Étant donné que notre contrôleur et les tests de modèle ne nécessitent pas une base de données réel, ils sont extrêmement rapide et facile à exécuter. Nous serons en mesure d’exécuter des centaines de tests automatisés en secondes et obtenez immédiatement des commentaires que nous avons apporté une modification de quelque chose s’est arrêtée si. Cela permettra de nous faire part de la confiance pour améliorer, refactoriser et affiner notre application régulièrement.

Nous avons abordé de test en tant que la dernière rubrique de ce chapitre – mais pas parce que le test est un élément à la fin d’un processus de développement ! Au contraire, vous devez écrire des tests automatisés dès que possible dans votre processus de développement. Cette opération permet donc immédiatement obtenir des commentaires lorsque vous développez, vous permet de considérer réfléchie sur les scénarios d’utilisation de votre application et vous guide pour concevoir votre application avec propre superposition et n’oubliez pas de couplage.

Traite un chapitre ultérieur dans le carnet de développement piloté par des tests (TDD) et comment l’utiliser avec ASP.NET MVC. TDD est une pratique de codage itérative dans lequel vous écrivez d’abord les tests répondant à votre code qui en résulte. Avec TDD commencer chaque fonctionnalité en créant un test qui vérifie que la fonctionnalité que vous allez implémenter. L’écriture de l’unité de test permet tout d’abord vous assurer que vous comprenez clairement la fonctionnalité et comment il est censé pour fonctionner. Une fois que le test est écrit (et que vous avez vérifié qu’il échoue) ne vous puis implémenter les fonctionnalités réelle, que le test vérifie. Étant donné que vous avez déjà passé de temps à réfléchir sur le cas d’utilisation de la façon dont la fonction est supposée fonctionne, vous aurez une meilleure compréhension de la configuration requise et la meilleure façon de les implémenter. Lorsque vous avez terminé avec l’implémentation, vous pouvez réexécuter le test – et immédiatement obtenir des commentaires en tant que si la fonctionnalité fonctionne correctement. Nous aborderons TDD plus dans le chapitre 10.

### <a name="next-step"></a>Étape suivante

Certains wrap finale des commentaires.

>[!div class="step-by-step"]
[Précédent](use-ajax-to-implement-mapping-scenarios.md)
[Suivant](nerddinner-wrap-up.md)
