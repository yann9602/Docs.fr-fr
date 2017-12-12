---
uid: web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs
title: "Ateliers pratiques : Générer une Application à Page unique (SPA) avec l’API Web ASP.NET et Angular.js | Documents Microsoft"
author: rick-anderson
description: "Dans les applications web classiques, le client (navigateur) lance la communication avec le serveur en demandant à une page. Le serveur traite ensuite la demande..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/30/2015
ms.topic: article
ms.assetid: 719727b7-bef3-45ad-bfe9-ba5bcdb2305f
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs
msc.type: authoredcontent
ms.openlocfilehash: 9a748628d53878be380869ac5327de0111d2284d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="hands-on-lab-build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs"></a>Ateliers pratiques : Générer une Application à Page unique (SPA) avec l’API Web ASP.NET et Angular.js
====================
par [Web Camps équipe](https://twitter.com/webcamps)

[Télécharger Camps Web Kit de formation](http://aka.ms/webcamps-training-kit)

> Dans les applications web classiques, le client (navigateur) lance la communication avec le serveur en demandant à une page. Ensuite, le serveur traite la demande et envoie au client le code HTML de la page. Dans les interactions suivantes avec la page, par exemple, l’utilisateur accède à un lien ou envoie un formulaire avec des données, une nouvelle demande est envoyée au serveur et le flux recommence : le serveur traite la demande et envoie une nouvelle page au navigateur en réponse à la demande d’action Ed par le client.
> 
> Dans les Applications à Page unique (ZPS) l’ensemble de la page est chargée dans le navigateur après la demande initiale, mais les interactions suivantes ont lieu via les requêtes Ajax. Cela signifie que le navigateur doit mettre à jour uniquement la partie de la page a été modifiée ; Il n’est pas nécessaire de recharger la page entière. L’approche SPA réduit le temps pris par l’application pour répondre aux actions de l’utilisateur, ce qui entraîne une expérience plus fluide.
> 
> L’architecture de SPA implique confrontés à des problèmes qui ne sont pas présents dans les applications web traditionnelles. Toutefois, nouvelles technologies, telles que l’API Web ASP.NET, les infrastructures JavaScript telles qu’AngularJS et nouvelles fonctionnalités de conception de styles fournies par CSS3 rendent facile à concevoir et créer des SPAs.
> 
> Dans ce laboratoire dans main, vous tirera parti de ces technologies pour implémenter le questionnaire de fou, un site Web de nature basé sur le concept SPA. Vous allez tout d’abord implémenter la couche de service avec l’API Web ASP.NET pour exposer les points de terminaison requis pour récupérer les questions du test et de stocker les réponses. Ensuite, vous allez créer une interface utilisateur riche et réactive à l’aide d’AngularJS et CSS3 effets de transformation.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [http://aka.ms/webcamps-training-kit](http://aka.ms/webcamps-training-kit).


## <a name="overview"></a>Vue d'ensemble

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Créez un service de l’API Web ASP.NET pour envoyer et recevoir des données JSON
- Créer une interface utilisateur réactive, à l’aide d’AngularJS
- Améliorer l’expérience de l’interface utilisateur avec des transformations de CSS3

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaire pour terminer cet atelier pratique :

- [Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/) ou supérieur

<a id="Setup"></a>
### <a name="setup"></a>Installation

Pour exécuter les exercices de cet atelier, vous devez configurer votre environnement tout d’abord.

1. Ouvrez l’Explorateur Windows et accédez à de l’atelier **Source** dossier.
2. Avec le bouton droit sur **Setup.cmd** et sélectionnez **exécuter en tant qu’administrateur** pour lancer le processus d’installation qui sera configurer votre environnement et installer les extraits de code Visual Studio pour ce laboratoire.
3. Si la boîte de dialogue contrôle de compte d’utilisateur s’affiche, confirmez l’action pour continuer.

> [!NOTE]
> Assurez-vous que vous avez activé toutes les dépendances pour ce laboratoire avant d’exécuter le programme d’installation.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>À l’aide d’extraits de Code

Dans le document de laboratoire, vous serez invité à insérer des blocs de code. Pour des raisons pratiques, la majeure partie de ce code est fourni en tant que Visual Studio extraits de Code, vous pouvez accéder à partir de Visual Studio 2013 pour éviter d’avoir à ajouter manuellement.

> [!NOTE]
> Chaque exercice est accompagnée d’une solution de départ située dans le **commencer** dossier de l’exercice qui vous permet de suivre chaque exercice indépendamment des autres. Sachez que les extraits de code sont ajoutés au cours d’un exercice sont manquants à partir de ces solutions de démarrage et peut ne pas fonctionneront tant que vous avez terminé l’exercice. Dans le code source pour un exercice, vous trouverez également une **fin** dossier qui contient une solution Visual Studio avec le code qui résulte de la procédure dans l’exercice correspondant. Si vous avez besoin d’aide au cours de cet atelier, vous pouvez utiliser ces solutions comme guide.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique inclut les exercices suivants :

1. [Création d’une API Web](#Exercise1)
2. [Création d’une Interface SPA](#Exercise2)

Durée estimée pour effectuer ce laboratoire : **60 minutes**

> [!NOTE]
> Lorsque vous démarrez Visual Studio, vous devez sélectionner une des collections de paramètres prédéfinis. Chaque collection prédéfinie est conçue pour faire correspondre un style de développement particulier et détermine les dispositions de fenêtres, le comportement de l’éditeur, extraits de code IntelliSense et les options de boîte de dialogue. Les procédures de cet atelier décrivent les actions nécessaires pour accomplir une tâche donnée dans Visual Studio lorsque vous utilisez la **paramètres de développement généraux** collection. Si vous choisissez une collection de paramètres différents pour votre environnement de développement, il est possible les différences dans les étapes que vous devez prendre en compte.


<a id="Exercise1"></a>
### <a name="exercise-1-creating-a-web-api"></a>Exercice 1 : Création d’une API Web

Une des parties clés d’un SPA est la couche de service. Il est chargé de traiter les appels Ajax envoyés par l’interface utilisateur et la recherche de données en réponse à cet appel. Les données récupérées doivent être présentées dans un format lisible par machine afin d’être analysée et consommé par le client.

L’infrastructure API Web fait partie de la pile ASP.NET et est conçu pour faciliter l’implémentation des services HTTP, généralement envoyer et recevoir des données JSON ou XML via une API RESTful. Dans cet exercice, vous allez créer le site Web pour héberger l’application fou questionnaire et implémentez le service principal pour exposer et conserver les données de test à l’aide des API Web ASP.NET.

<a id="Ex1Task1"></a>
#### <a name="task-1--creating-the-initial-project-for-geek-quiz"></a>Tâche 1 : création du projet Initial pour fou questionnaire

Dans cette tâche commence la création d’un nouveau projet ASP.NET MVC avec prise en charge pour l’API Web ASP.NET basée sur le **One ASP.NET** type qui est fourni avec Visual Studio de projet. **Un ASP.NET** unifie toutes les technologies ASP.NET et vous permet de combiner et de les mettre en correspondance comme vous le souhaitez. Vous ajouterez ensuite des classes de modèle d’Entity Framework et l’initializator de la base de données pour insérer les questions du test.

1. Ouvrez **Visual Studio Express 2013 pour le Web** et sélectionnez **fichier | Nouveau projet...**  pour démarrer une nouvelle solution.

    ![Création d’un projet](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image1.png "création d’un projet")

    *Création d’un projet*
2. Dans le **nouveau projet** boîte de dialogue, sélectionnez **Application Web ASP.NET** sous le **Visual C# | Web** onglet. Assurez-vous que **.NET Framework 4.5** est sélectionnée, nommez-le *GeekQuiz*, choisissez un **emplacement** et cliquez sur **OK**.

    ![Création d’un projet d’Application Web ASP.NET](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image2.png "création d’un projet d’Application Web ASP.NET")

    *Création d’un projet d’Application Web ASP.NET*
3. Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **MVC** modèle et sélectionnez le **API Web** option. En outre, assurez-vous que le **authentification** option est définie sur **comptes d’utilisateur individuels**. Cliquez sur **OK** pour continuer.

    ![Création d’un projet avec le modèle MVC, y compris les composants de l’API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image3.png)

    *Création d’un projet avec le modèle MVC, y compris les composants de l’API Web*
4. Dans **l’Explorateur de solutions**, avec le bouton droit le **modèles** dossier de la **GeekQuiz** de projet et sélectionnez **ajouter | Élément existant...** .

    ![Ajout d’un élément existant](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image4.png "Ajout d’un élément existant")

    *Ajout d’un élément existant*
5. Dans le **ajouter un élément existant** boîte de dialogue, accédez à la **Source/actifs/modèles** et sélectionnez tous les fichiers. Cliquez sur **Ajouter**.

    ![Ajouter les composants de modèle](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image5.png "ajouter les composants de modèle")

    *Ajouter les composants de modèle*

    > [!NOTE]
    > En ajoutant ces fichiers, vous ajoutez le modèle de données, de contexte de base de données d’Entity Framework et de l’initialiseur de base de données pour l’application fou questionnaire.
    > 
    > **Entity Framework (EF)** est un mappeur objet / relationnel (ORM) qui vous permet de créer des applications d’accès aux données par programmation avec un modèle d’application conceptuel plutôt que directement à l’aide d’un schéma de stockage relationnel. Plus d’informations sur Entity Framework [ici](../../../entity-framework.md).
    > 
    > Voici une description des classes que vous venez d’ajouter :
    > 
    > - **TriviaOption :** représente une option unique associée à une question du questionnaire
    > - **TriviaQuestion :** représente une question du questionnaire et expose les options associées à la **Options** propriété
    > - **TriviaAnswer :** représente l’option sélectionnée par l’utilisateur en réponse à une question du questionnaire
    > - **TriviaContext :** représente le contexte de base de données d’Entity Framework de l’application fou questionnaire. Cette classe dérive **DContext** et expose **DbSet** propriétés qui représentent des collections d’entités décrites ci-dessus.
    > - **TriviaDatabaseInitializer :** l’implémentation de l’initialiseur d’Entity Framework pour le **TriviaContext** classe qui hérite de **CreateDatabaseIfNotExists**. Le comportement par défaut de cette classe pour créer la base de données uniquement si elle n’existe pas, insertion d’entités spécifié dans le **Seed** (méthode).
6. Ouvrez le **Global.asax.cs** de fichier et ajoutez le code suivant à l’aide d’instruction.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample1.cs)]
7. Ajoutez le code suivant au début de la **Application\_Démarrer** pour définir le **TriviaDatabaseInitializer** en tant qu’initialiseur de la base de données.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample2.cs)]
8. Modifier la **accueil** contrôleur pour restreindre l’accès à des utilisateurs authentifiés. Pour ce faire, ouvrez le **HomeController.cs** à l’intérieur du fichier le **contrôleurs** dossier et ajouter la **Authorize** attribut le **HomeController**définition de classe.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample3.cs)]

    > [!NOTE]
    > Le **Authorize** filtre vérifie si l’utilisateur est authentifié. Si l’utilisateur n’est pas authentifié, il retourne le code d’état HTTP 401 (non autorisé) sans appeler l’action. Vous pouvez appliquer le filtre globalement, au niveau du contrôleur ou au niveau de différentes actions.
9. Vous allez maintenant personnaliser la disposition des pages web et la personnalisation. Pour ce faire, ouvrez le  **\_Layout.cshtml** de fichiers à l’intérieur de la **vues | Partagé** dossier et mettre à jour le contenu de la  **&lt;titre&gt;**  élément en remplaçant *mon Application ASP.NET* avec *fou questionnaire* .

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample4.cshtml)]
10. Dans le même fichier, vous devez mettre à jour la barre de navigation en supprimant le *sur* et *Contact* liens et renommer le *accueil* lier à *lire*. En outre, renommez le *nom de l’Application* lier à *fou questionnaire*. Le code HTML de la barre de navigation doit ressembler le code suivant.

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample5.cshtml)]
11. Mettre à jour le pied de page de la page de disposition en remplaçant *mon Application ASP.NET* avec *fou questionnaire*. Pour ce faire, remplacez le contenu de la  **&lt;pied de page&gt;**  élément avec le code en surbrillance suivant.

    [!code-html[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample6.html)]

<a id="Ex1Task2"></a>
#### <a name="task-2--creating-the-triviacontroller-web-api"></a>Tâche 2 : création de l’API Web de TriviaController

Dans la tâche précédente, vous avez créé la structure initiale de l’application web fou questionnaire. Vous allez maintenant générer un service API Web simple qui interagit avec le modèle de données de test et expose les actions suivantes :

- **GET/api/nature**: récupère la question suivante dans la liste questionnaire doit répondre à l’utilisateur authentifié.
- **POST/api/nature**: stocke la réponse de test spécifiée par l’utilisateur authentifié.

Les outils de génération de modèles automatique ASP.NET fournis par Visual Studio vous permet de créer la ligne de base pour la classe de contrôleur d’API Web.

1. Ouvrez le **WebApiConfig.cs** de fichiers à l’intérieur de la **application\_Démarrer** dossier. Ce fichier définit la configuration du service API Web, telles que la façon dont les itinéraires sont mappés aux actions de contrôleur d’API Web.
2. Ajoutez le code suivant à l’aide d’instruction au début du fichier.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample7.cs)]
3. Ajoutez le code en surbrillance suivant à la **inscrire** méthode pour configurer le module de formatage pour les données JSON récupérées par les méthodes d’action API Web de manière globale.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample8.cs)]

    > [!NOTE]
    > Le **CamelCasePropertyNamesContractResolver** convertit automatiquement les noms de propriétés à *mixte* casse, ce qui est la convention générale des noms de propriétés dans JavaScript.
4. Dans **l’Explorateur de solutions**, avec le bouton droit le **contrôleurs** dossier de la **GeekQuiz** de projet et sélectionnez **ajouter | Nouvel élément structuré...** .

    ![Création d’un nouvel élément structuré](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image6.png "création d’un nouvel élément de modèle généré automatiquement")

    *Création d’un nouvel élément de modèle généré automatiquement*
5. Dans le **ajouter une vue de structure** boîte de dialogue zone, assurez-vous que le **commune** nœud est sélectionné dans le volet gauche. Ensuite, sélectionnez le **Web vide - contrôleur 2 d’API** modèle dans le volet central et cliquez sur **ajouter**.

    ![Sélectionnez le modèle vide de contrôleur Web API 2](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image7.png "en sélectionnant le modèle vide de contrôleur Web API 2")

    *Sélectionnez le modèle vide de contrôleur Web API 2*

    > [!NOTE]
    > **Génération de modèles automatique ASP.NET** est une infrastructure de génération de code pour les applications Web ASP.NET. Visual Studio 2013 inclut des générateurs de code préalablement installés pour les projets MVC et API Web. Lorsque vous souhaitez ajouter rapidement le code qui interagit avec les modèles de données afin de réduire le temps requis pour développer des opérations de données standard, vous devez utiliser la structure de votre projet.
    > 
    > Le processus de génération de modèles automatique garantit également que toutes les dépendances nécessaires sont installés dans le projet. Par exemple, si vous démarrez avec un projet ASP.NET vide et ensuite utilisez la structure pour ajouter un contrôleur d’API Web, les packages NuGet de l’API Web requis et les références sont ajoutés à votre projet automatiquement.
6. Dans le **ajouter un contrôleur** boîte de dialogue, tapez *TriviaController* dans les **nom de contrôleur** zone de texte et cliquez sur **ajouter**.

    ![Ajout du contrôleur de nature](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image8.png "Ajout du contrôleur de nature")

    *Ajout du contrôleur de nature*
7. Le **TriviaController.cs** fichier est ensuite ajouté à la **contrôleurs** dossier de la **GeekQuiz** projet, contenant vide **TriviaController** classe. Ajoutez le code suivant à l’aide d’instructions au début du fichier.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerUsings*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample9.cs)]
8. Ajoutez le code suivant au début de la **TriviaController** classe pour définir, initialiser et dispose le **TriviaContext** instance dans le contrôleur.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerContext*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample10.cs)]

    > [!NOTE]
    > Le **Dispose** méthode de **TriviaController** appelle la **Dispose** méthode de la **TriviaContext** instance, ce qui garantit que toutes les les ressources utilisées par l’objet de contexte sont libérés lorsque le **TriviaContext** instance est supprimée ou par le garbage collector. Cela inclut la fermeture de toutes les connexions de base de données ouvertes par Entity Framework.
9. Ajoutez la méthode d’assistance suivante à la fin de la **TriviaController** classe. Cette méthode récupère la question du questionnaire suivante à partir de la base de données doit répondre à l’utilisateur spécifié.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerNextQuestion*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample11.cs)]
10. Ajoutez le code suivant **obtenir** méthode d’action à la **TriviaController** classe. Cette méthode d’action appelle le **NextQuestionAsync** méthode d’assistance définie dans l’étape précédente pour récupérer la question suivante pour l’utilisateur authentifié.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerGetAction*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample12.cs)]
11. Ajoutez la méthode d’assistance suivante à la fin de la **TriviaController** classe. Cette méthode stocke la réponse spécifiée dans la base de données et retourne une valeur booléenne indiquant si la réponse est correcte ou non.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerStoreAsync*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample13.cs)]
12. Ajoutez le code suivant **Post** méthode d’action à la **TriviaController** classe. Cette méthode d’action associe la réponse à l’utilisateur authentifié et appelle le **StoreAsync** méthode d’assistance. Ensuite, il envoie une réponse avec la valeur booléenne retournée par la méthode d’assistance.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex1 - TriviaControllerPostAction*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample14.cs)]
13. Modifier le contrôleur d’API Web pour limiter l’accès aux utilisateurs authentifiés en ajoutant le **Authorize** d’attribut pour le **TriviaController** définition de classe.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample15.cs)]

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Tâche 3 : exécution de la Solution

Dans cette tâche, vous allez vérifier que le service Web API que vous avez créé dans la tâche précédente fonctionne comme prévu. Vous allez utiliser Internet Explorer **outils de développement F12** capturer le trafic réseau et d’inspecter la réponse complète à partir du service de l’API Web.

> [!NOTE]
> Assurez-vous que **Internet Explorer** est sélectionné dans le **Démarrer** bouton situé sur la barre d’outils de Visual Studio.
> 
> ![Option d’Internet Explorer](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image9.png)


1. Appuyez sur **F5** pour exécuter la solution. Le **connecter** page doit apparaître dans le navigateur.

    > [!NOTE]
    > Lorsque l’application démarre, l’itinéraire MVC par défaut est déclenché, ce qui, par défaut est mappé à la **Index** action de la **HomeController** classe. Étant donné que **HomeController** est limité aux utilisateurs authentifiés (Souvenez-vous que vous décorées de cette classe avec la **Authorize** attribut dans l’exercice 1) et qu’il en existe aucun utilisateur authentifié encore, l’application redirige la requête d’origine à la page de connexion.

    ![Exécution de la solution](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image10.png "la solution en cours d’exécution")

    *Exécution de la solution*
2. Cliquez sur **inscrire** pour créer un nouvel utilisateur.

    ![L’inscription d’un nouvel utilisateur](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image11.png "l’inscription d’un nouvel utilisateur")

    *L’inscription d’un nouvel utilisateur*
3. Dans le **inscrire** , entrez un **nom d’utilisateur** et **mot de passe**, puis cliquez sur **inscrire**.

    ![Page inscrire la](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image12.png "page d’inscription")

    *Page d’inscription*
4. L’application enregistre le nouveau compte et l’utilisateur est authentifié et redirigé vers la page d’accueil.

    ![Utilisateur authentifié](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image13.png "utilisateur authentifié")

    *Utilisateur authentifié*
5. Dans le navigateur, appuyez sur **F12** pour ouvrir le **outils de développement** Panneau de configuration. Appuyez sur **CTRL + 4** ou cliquez sur le **réseau** icône, puis cliquez sur la flèche verte pour commencer la capture du trafic réseau.

    ![Initialisation de capture de réseau d’API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image14.png "capture réseau de lancement des API Web")

    *Initialisation de capture de réseau d’API Web*
6. Ajouter **api/nature** à l’URL dans la barre d’adresses du navigateur. Vous inspecte maintenant les détails de la réponse de la **obtenir** méthode d’action de **TriviaController**.

    ![Extraction des données question suivantes via l’API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image15.png "récupérant les données de question suivantes via l’API Web")

    *Extraction des données question suivantes via l’API Web*

    > [!NOTE]
    > Une fois le téléchargement terminé, vous devrez effectuer une action avec le fichier téléchargé. Laissez la boîte de dialogue ouverte pour pouvoir être en mesure d’observer le contenu de réponse via la fenêtre outil de développeurs.
7. Maintenant vous inspecte le corps de la réponse. Pour ce faire, cliquez sur le **détails** onglet, puis cliquez sur **corps de réponse**. Vous pouvez vérifier que les données téléchargées sont un objet avec les propriétés **options** (qui est une liste de **TriviaOption** objets), **id** et **titre** qui correspondent à la **TriviaQuestion** classe.

    ![Afficher le corps de réponse de l’API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image16.png "afficher le corps de réponse de l’API Web")

    *Corps de réponse de l’API Web de visualisation*
8. Revenez à Visual Studio, puis appuyez sur **MAJ + F5** pour arrêter le débogage.

<a id="Exercise2"></a>
### <a name="exercise-2-creating-the-spa-interface"></a>Exercice 2 : Création de l’Interface SPA

Dans cet exercice vous allez tout d’abord générer la partie frontale web de fou questionnaire, en mettant l’accent sur l’interaction de Page simple Application à l’aide **AngularJS**. Vous allez ensuite améliorer l’expérience utilisateur avec CSS3 pour effectuer des animations riches et fournir un effet visuel de basculements de contexte lors de la transition d’une question à la suivante.

<a id="Ex2Task1"></a>
#### <a name="task-1--creating-the-spa-interface-using-angularjs"></a>Tâche 1 : création de l’Interface SPA à l’aide d’AngularJS

Dans cette tâche, vous allez utiliser **AngularJS** pour implémenter le côté client de l’application fou questionnaire. **AngularJS** est une infrastructure JavaScript open source qui augmente les applications de navigateur avec *Model-View-Controller* fonctionnalité (MVC), faciliter à la fois le développement et de test.

Vous allez commencer par installer AngularJS à partir de la Console du Gestionnaire de Package de Visual Studio. Ensuite, vous allez créer le contrôleur pour fournir le comportement de l’application de fou questionnaire et de la vue à restituer les questions du questionnaire et les réponses à l’aide du moteur de modèle AngularJS.

> [!NOTE]
> Pour plus d’informations sur AngularJS, reportez-vous à [ [http://angularjs.org/](http://angularjs.org/)](http://angularjs.org/).


1. Ouvrez **Visual Studio Express 2013 pour le Web** et ouvrez le **GeekQuiz.sln** solution situé dans le **Source/Ex2-CreatingASPAInterface/début** dossier. Ou bien, vous pouvez continuer avec la solution que vous avez obtenu dans l’exercice précédent.
2. Ouvrez le **Console du Gestionnaire de Package** de **outils** | **Gestionnaire de Package de bibliothèque**. Tapez la commande suivante pour installer le **AngularJS.Core** package NuGet.

    [!code-powershell[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample16.ps1)]
3. Dans **l’Explorateur de solutions**, avec le bouton droit le **Scripts** dossier de la **GeekQuiz** de projet et sélectionnez **ajouter | Nouveau dossier**. Nommez le dossier **application** et appuyez sur **entrée**.
4. Cliquez sur le **application** dossier que vous venez de créer et sélectionnez **ajouter | Fichier JavaScript**.

    ![Création d’un fichier JavaScript](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image17.png)

    *Création d’un fichier JavaScript*
5. Dans le **spécifier le nom de l’élément** boîte de dialogue, tapez *contrôleur de test* dans les **nom de l’élément** zone de texte et cliquez sur **OK**.

    ![Le nouveau fichier JavaScript d’affectation de noms](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image18.png)

    *Le nouveau fichier JavaScript d’affectation de noms*
6. Dans le **questionnaire-controller.js** , ajoutez le code suivant pour déclarer et initialiser la AngularJS **QuizCtrl** contrôleur.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex2 - AngularQuizController*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample17.js)]

    > [!NOTE]
    > La fonction constructeur de la **QuizCtrl** contrôleur attend un paramètre injectable nommé **$scope**. L’état initial de l’étendue doit être définie dans la fonction constructeur en joignant des propriétés pour le **$scope** objet. Les propriétés contiennent le **modèle d’affichage**et sont accessibles sur le modèle lorsque le contrôleur est inscrit.
    > 
    > Le **QuizCtrl** contrôleur est défini à l’intérieur d’un module nommé **QuizApp**. Les modules sont des unités de travail qui vous permettent de diviser votre application en composants distincts. Les principaux avantages de l’utilisation des modules est que le code est plus facile à comprendre et facilite les tests unitaires, réutilisation et la maintenance.
7. Vous allez maintenant ajouter le comportement à l’étendue pour réagir aux événements déclenchés à partir de la vue. Ajoutez le code suivant à la fin de la **QuizCtrl** contrôleur pour définir le **nextQuestion** de fonction dans le **$scope** objet.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex2 - AngularQuizControllerNextQuestion*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample18.js)]

    > [!NOTE]
    > Cette fonction extrait la question suivante à partir de la **nature** API Web créé dans l’exercice précédent et attache les données de la question à la **$scope** objet.
8. Insérez le code suivant à la fin de la **QuizCtrl** contrôleur pour définir le **sendAnswer** de fonction dans le **$scope** objet.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex2 - AngularQuizControllerSendAnswer*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample19.js)]

    > [!NOTE]
    > Cette fonction envoie la réponse sélectionnée par l’utilisateur pour le **nature** API Web et stocke le résultat – par exemple, si la réponse est correcte ou non : dans le **$scope** objet.
    > 
    > Le **nextQuestion** et **sendAnswer** fonctions ci-dessus utilisent le AngularJS **$http** objet abstraire la communication avec l’API Web via un XMLHttpRequest Objet JavaScript à partir du navigateur. AngularJS prend en charge un autre service qui offre un niveau supérieur d’abstraction pour effectuer des opérations CRUD sur une ressource via une API RESTful. Le AngularJS **$resource** objet possède des méthodes d’action qui fournissent des comportements de haut niveau sans avoir besoin d’interagir avec les **$http** objet. Envisagez d’utiliser le **$resource** objet dans les scénarios qui nécessite le modèle CRUD (fore plus d’informations, consultez la [$resource documentation](https://docs.angularjs.org/api/ngResource/service/$resource)).
9. L’étape suivante consiste à créer le modèle AngularJS qui définit la vue du questionnaire. Pour ce faire, ouvrez le **Index.cshtml** de fichiers à l’intérieur de la **vues | Accueil** dossier et remplacer le contenu par le code suivant.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex2 - GeekQuizView*)

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample20.cshtml)]

    > [!NOTE]
    > Le modèle AngularJS est une spécification déclarative qui utilise des informations à partir du modèle et le contrôleur pour transformer le balisage statique dans la vue dynamique que l’utilisateur voit dans le navigateur. Voici quelques exemples d’éléments de AngularJS et d’attributs de l’élément qui peuvent être utilisées dans un modèle :
    > 
    > - Le **ng-app** directive indique AngularJS l’élément DOM qui représente l’élément racine de l’application.
    > - Le **ng-contrôleur** directive attache un contrôleur au DOM au point où la directive est déclarée.
    > - La notation avec accolades **{{}}** indique des liaisons à des propriétés de l’étendue définies dans le contrôleur.
    > - Le **ng-click** la directive est utilisée pour appeler les fonctions définies dans l’étendue en réponse aux clics d’utilisateur.
10. Ouvrir le **Site.css** de fichiers à l’intérieur de la **contenu** dossier et ajoutez les styles de mise en surbrillance suivants à la fin du fichier pour fournir une apparence de la vue du questionnaire.

    (Code d’extrait de code - *AspNetWebApiSpa - Ex2 - GeekQuizStyles*)

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample21.css)]

<a id="Ex2Task2"></a>
#### <a name="task-2--running-the-solution"></a>Tâche 2 : exécution de la Solution

Dans cette tâche, vous allez exécuter la solution à l’aide de l’utilisateur de nouvelle interface intègrent AngularJS pour répondre à certaines des questions du questionnaire.

1. Appuyez sur **F5** pour exécuter la solution.
2. Inscrire un nouveau compte d’utilisateur. Pour ce faire, suivez la procédure d’inscription décrite dans l’exercice 1, la tâche 3.

    > [!NOTE]
    > Si vous utilisez la solution à partir de l’exercice précédent, vous pouvez vous connecter avec le compte d’utilisateur que vous avez créé avant.
3. Le **accueil** page doit apparaître, indiquant la première question du questionnaire. Répondez à la question en cliquant sur une des options. Cette opération déclenche le **sendAnswer** fonction définie précédemment, qui envoie l’option sélectionnée par le **nature** API Web.

    ![Répondre à une question](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image19.png "répondre à une question")

    *Répondre à une question*
4. Après avoir cliqué sur un des boutons, la réponse doit apparaître. Cliquez sur **Question suivante** pour afficher la question suivante. Cette opération déclenche le **nextQuestion** fonction définie dans le contrôleur.

    ![Demande la question suivante](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image20.png "demandant la question suivante")

    *Demande la question suivante*
5. La question suivante doit apparaître. Passez aux questions posées autant de fois que vous le souhaitez. Après avoir effectué toutes les questions, vous devez revenir à la première question.

    ![Une autre question](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image21.png "une autre question")

    *Question suivante*
6. Revenez à Visual Studio, puis appuyez sur **MAJ + F5** pour arrêter le débogage.

<a id="Ex2Task3"></a>
#### <a name="task-3--creating-a-flip-animation-using-css3"></a>Tâche 3 : création d’une Animation de rotation à l’aide de CSS3

Dans cette tâche, vous utiliserez les propriétés CSS3 pour effectuer des animations riches en ajoutant un effet retournement lorsqu’une question est traitée et lorsque la question suivante est récupérée.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le **contenu** dossier de la **GeekQuiz** de projet et sélectionnez **ajouter | Élément existant...** .

    ![Ajout d’un élément existant dans le dossier de contenu](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image22.png "Ajout d’un élément existant dans le dossier de contenu")

    *Ajout d’un élément existant dans le dossier de contenu*
2. Dans le **ajouter un élément existant** boîte de dialogue, accédez à la **Source/ressources** et sélectionnez **Flip.css**. Cliquez sur **Ajouter**.

    ![Ajout du fichier Flip.css parmi les ressources](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image23.png "Ajout du fichier Flip.css à partir de ressources")

    *Ajout du fichier Flip.css à partir de ressources*
3. Ouvrez le **Flip.css** fichier que vous venez d’ajouter et examinez son contenu.
4. Recherchez le **retourner transformation** commentaire. Les styles sous ce commentaire utilisent le code CSS **perspective** et **rotateY** transformations pour générer un &quot;de retournement de carte&quot; effet.

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample22.css)]
5. Recherchez le **masquer arrière volet au cours de retournement de** commentaire. Le style sous ce commentaire masque l’arrière des faces lorsqu’ils sont opposés à la visionneuse en définissant le **visibilité de la face arrière** propriété CSS *masqué*.

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample23.css)]
6. Ouvrir le **BundleConfig.cs** de fichiers à l’intérieur de la **application\_Démarrer** dossier et ajouter la référence à la **Flip.css** de fichiers dans le  **&quot;~/Content/css&quot;**  offre groupée de style

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample24.cs)]
7. Appuyez sur **F5** pour exécuter la solution et connectez-vous avec vos informations d’identification.
8. Répondre à une question en cliquant sur une des options. Notez l’effet de retournement lors de la transition entre les vues.

    ![Répondre à une question avec l’effet de retournement](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image24.png "répondre à une question avec l’effet de rotation")

    *Répondre à une question avec l’effet de rotation*
9. Cliquez sur **Question suivante** pour récupérer la question suivante. L’effet de rotation doit s’afficher à nouveau.

    ![La récupération de la question suivante avec l’effet de retournement](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image25.png "la récupération de la question suivante avec l’effet de rotation")

    *La récupération de la question suivante avec l’effet de rotation*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Résumé

À la fin de cet atelier pratique, vous avez appris comment :

- Créer un contrôleur d’API Web ASP.NET à l’aide de la génération de modèles automatique ASP.NET
- Implémenter une action d’obtenir des API Web pour récupérer la question du questionnaire suivante
- Implémenter une action API Web pour stocker les réponses aux problèmes
- Installez AngularJS à partir de la Console du Gestionnaire de Package Visual Studio
- Implémentez AngularJS modèles et contrôleurs
- Transitions CSS3 permet d’effectuer les effets d’animation
