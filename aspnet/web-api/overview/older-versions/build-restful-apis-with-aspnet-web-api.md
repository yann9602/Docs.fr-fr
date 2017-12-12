---
uid: web-api/overview/older-versions/build-restful-apis-with-aspnet-web-api
title: "Générer une API RESTful avec ASP.NET Web API | Documents Microsoft"
author: rick-anderson
description: "Ces dernières années, il est clair que HTTP n’est pas simplement pour traiter des pages HTML. Il est également une plateforme puissante pour la création d’API Web, à l’aide d’un certain nombre de o..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 87daa99f-3810-407e-b969-dd28a192959d
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/build-restful-apis-with-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 49dcd86649ceb77cd5a02ebeb5d9d7b11ff4f344
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="build-restful-apis-with-aspnet-web-api"></a>Générer une API RESTful avec l’API Web ASP.NET
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> Ces dernières années, il est clair que HTTP n’est pas simplement pour traiter des pages HTML. Il est également une plateforme puissante pour la création d’API Web, à l’aide d’un petit nombre de verbes (GET, POST, etc.) ainsi que de quelques concepts simples comme *URI* et *en-têtes*. API Web ASP.NET est un ensemble de composants qui simplifient la programmation HTTP. Car elle repose sur le runtime ASP.NET MVC, API Web gère automatiquement les détails de bas niveau de transport HTTP. En même temps, les API Web expose naturellement le modèle de programmation HTTP. En fait, une API Web vise à *pas* abstraire la réalité de HTTP. Par conséquent, les API Web est flexible et facile à étendre. Dans cet atelier pratique, vous allez utiliser des API Web pour générer une API REST simple pour une application du Gestionnaire de contacts. Vous allez également générer un client pour utiliser l’API. Le style d’architecture REST s’est révélé un moyen efficace pour tirer parti de HTTP - même s’il n’est certainement pas l’approche valide uniquement sur HTTP. Le Gestionnaire de contact exposera le RESTful pour répertorier, ajout et suppression de contacts, entre autres. Cet atelier nécessite une compréhension de base du protocole HTTP, REST et suppose que vous disposez d’une connaissance de base du code HTML, JavaScript et jQuery.
> 
> > [!NOTE]
> > Le site Web ASP.NET a une zone dédiée à l’infrastructure de l’API Web ASP.NET au [ [https://asp.net/web-api](https://asp.net/web-api)](https://asp.net/web-api). Ce site continuera à fournir les informations les plus récentes, des exemples et des informations relatives à l’API Web, donc vérifier fréquemment si vous souhaitez d’explorent l’art de la création d’API Web personnalisées permettent de pratiquement n’importe quelle infrastructure de périphérique ou de développement.
> > 
> > ASP.NET Web API, semblable à ASP.NET MVC 4, a une grande souplesse en termes de séparation de la couche de service à partir des contrôleurs, ce qui vous permet d’utiliser plusieurs les infrastructures d’Injection de dépendance disponibles est assez simple. Il est un bon exemple de MSDN qui montre comment utiliser Ninject pour l’injection de dépendance dans un projet d’API Web ASP.NET que vous pouvez le télécharger à partir de [ici](https://code.msdn.microsoft.com/ASPNET-Web-API-JavaScript-d0d64dd7).
> 
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409).


<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Implémenter une API de Web RESTful
- Appeler l’API à partir d’un client HTML

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaire pour terminer cet atelier pratique :

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe B](#AppendixB) pour obtenir des instructions sur l’installation).

<a id="Setup"></a>
### <a name="setup"></a>Installation

**L’installation des extraits de Code**

Pour plus de commodité, une grande partie du code que vous gérez le long de cet atelier est disponible sous les extraits de code Visual Studio. Pour installer les extraits de code exécuter **.\Source\Setup\CodeSnippets.vsi** fichier.

Si vous n’êtes pas familiarisé avec les extraits de Code Visual Studio et vous souhaitez savoir comment les utiliser, vous pouvez faire référence à l’annexe de ce document &quot; [extraits de Code à l’aide de l’annexe a :](#AppendixA)&quot;.

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique inclut l’exercice suivant :

1. [Exercice 1 : Créer une API Web en lecture seule](#Exercise1)
2. [Exercice 2 : Création d’une API de Web en lecture/écriture](#Exercise2)
3. [Exercice 3 : Utiliser l’API Web à partir d’un Client HTML](#Exercise3)

> [!NOTE]
> Chaque exercice est accompagné par un **fin** dossier qui contient la solution résultante, vous devez obtenir après avoir effectué les exercices. Si vous avez besoin d’aide fonctionne via les exercices, vous pouvez utiliser cette solution comme guide.


Durée estimée pour effectuer ce laboratoire : **60 minutes**.

<a id="Exercise1"></a>

<a id="Exercise_1_Create_a_Read-Only_Web_API"></a>
### <a name="exercise-1-create-a-read-only-web-api"></a>Exercice 1 : Créer une API Web en lecture seule

Dans cet exercice, vous implémenterez les méthodes GET en lecture seule pour le Gestionnaire de contact.

<a id="Ex1Task1"></a>

<a id="Task_1_-_Creating_the_API_Project"></a>
#### <a name="task-1---creating-the-api-project"></a>Tâche 1 - Création du projet d’API

Dans cette tâche, vous utiliserez les nouveaux modèles de projet web ASP.NET pour créer une application de web API Web.

1. Exécutez **Visual Studio 2012 Express pour Web**, pour cela, accédez à **Démarrer** et type **Visual Studio Express pour le Web** puis appuyez sur **entrée**.
2. À partir de la **fichier** menu, sélectionnez **nouveau projet**. Sélectionnez le **Visual C# | Web** type dans l’arborescence de type de projet du projet, puis sélectionnez le **ASP.NET MVC 4 Web Application** type de projet. Attribuez la **nom** à *ContactManager* et **nom de la Solution** à *commencer*, puis cliquez sur **OK**.

    ![Création d’un projet Application ASP.NET MVC 4.0 Web](build-restful-apis-with-aspnet-web-api/_static/image1.png "création d’un projet Application ASP.NET MVC 4.0 Web")

    *Création d’un projet Application ASP.NET MVC 4.0 Web*
3. Dans la boîte de dialogue du type de projet ASP.NET MVC 4, sélectionnez le **API Web** type de projet. Cliquez sur **OK**.

    ![Spécification du type de projet d’API Web](build-restful-apis-with-aspnet-web-api/_static/image2.png "spécifiant le type de projet d’API Web")

    *Spécification du type de projet d’API Web*

<a id="Ex1Task2"></a>

<a id="Task_2_-_Creating_the_Contact_Manager_API_Controllers"></a>
#### <a name="task-2---creating-the-contact-manager-api-controllers"></a>Tâche 2 : créer les contrôleurs d’API de gestionnaire de contacts

Dans cette tâche, vous allez créer les classes de contrôleur dans lequel les méthodes de l’API doit résider.

1. Supprimer le fichier nommé **le fichier ValuesController.cs** dans **contrôleurs** dossier du projet.
2. Avec le bouton droit le **contrôleurs** dossier dans le projet et sélectionnez **ajouter | Contrôleur** dans le menu contextuel.

    ![Ajoutez un nouveau contrôleur au projet](build-restful-apis-with-aspnet-web-api/_static/image3.png "Ajout d’un nouveau contrôleur au projet")

    *Ajoutez un nouveau contrôleur au projet*
3. Dans le **ajouter un contrôleur** boîte de dialogue qui s’affiche, sélectionnez **contrôleur d’API vide** dans le menu modèle. Nom de la classe de contrôleur **ContactController**. Ensuite, cliquez sur **ajouter.**

    ![À l’aide de la boîte de dialogue Ajouter un contrôleur pour créer un nouveau contrôleur Web API](build-restful-apis-with-aspnet-web-api/_static/image4.png "à l’aide de la boîte de dialogue Ajouter un contrôleur pour créer un nouveau contrôleur d’API Web")

    *À l’aide de la boîte de dialogue Ajouter un contrôleur pour créer un nouveau contrôleur d’API Web*
4. Ajoutez le code suivant à la **ContactController**.

    (Code d’extrait de code - *Web API Lab - Ex01 - Get (méthode) de l’API*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample1.cs)]
5. Appuyez sur **F5** pour déboguer l’application. La page d’accueil par défaut pour un projet d’API Web doit apparaître.

    ![La page d’accueil par défaut d’une application ASP.NET Web API](build-restful-apis-with-aspnet-web-api/_static/image5.png "la page d’accueil par défaut d’une application de l’API Web ASP.NET")

    *La page d’accueil par défaut d’une application de l’API Web ASP.NET*
6. Dans la fenêtre Internet Explorer, appuyez sur la **F12** clé pour ouvrir la **outils de développement** fenêtre. Cliquez sur le **réseau** onglet, puis cliquez sur le **démarrer capture** bouton pour commencer la capture du trafic réseau dans la fenêtre.

    ![Ouverture de l’onglet réseau et à lancer de capture réseau](build-restful-apis-with-aspnet-web-api/_static/image6.png "capture réseau de l’onglet réseau et à lancer")

    *Ouverture de l’onglet réseau et le lancement de capture réseau*
7. Ajoutez l’URL dans la barre d’adresses du navigateur avec **/api/contact** et appuyez sur ENTRÉE. Les détails de la transmission apparaîtra dans la fenêtre de capture réseau. Notez que le type MIME de la réponse est **application/json**. Cela montre la façon dont le format de sortie par défaut est JSON.

    ![Affichage de la sortie de la demande d’API Web dans la vue du réseau](build-restful-apis-with-aspnet-web-api/_static/image7.png "affichage de la sortie de la demande d’API Web dans la vue du réseau")

    *Affichage de la sortie de la demande d’API Web dans la vue du réseau*

    > [!NOTE]
    > À ce stade, par défaut d’Internet Explorer 10 sera pour demander si l’utilisateur souhaite enregistrer ou ouvrir le flux de données qui résulte de l’appel d’API Web. Le résultat sera un fichier texte contenant le résultat de l’appel de l’URL d’API Web JSON. N’annulez pas la boîte de dialogue afin d’être en mesure d’observer le contenu de la réponse à la fenêtre outil de développeurs.
8. Cliquez sur le **vue détaillée** bouton pour plus d’informations sur la réponse de cet appel d’API.

    ![Basculer vers la vue détaillée](build-restful-apis-with-aspnet-web-api/_static/image8.png "passer en mode Détails")

    *Basculer vers la vue détaillée*
9. Cliquez sur le **corps de réponse** onglet pour afficher le texte de réponse JSON.

    ![Affichage de l’objet JSON de sortie texte dans le Moniteur réseau](build-restful-apis-with-aspnet-web-api/_static/image9.png "affichage de l’objet JSON de sortie texte dans le Moniteur réseau")

    *Affichage du texte de sortie JSON dans le Moniteur réseau*

<a id="Ex1Task3"></a>

<a id="Task_3_-_Creating_the_Contact_Models_and_Augment_the_Contact_Controller"></a>
#### <a name="task-3---creating-the-contact-models-and-augment-the-contact-controller"></a>Tâche 3 : création des modèles de Contact et d’augmenter le contrôleur de Contact

Dans cette tâche, vous allez créer les classes de contrôleur dans lequel les méthodes de l’API doit résider.

1. Cliquez sur le **modèles** et sélectionnez **ajouter | Classe...**  dans le menu contextuel.

    ![Ajouter un nouveau modèle à l’application web](build-restful-apis-with-aspnet-web-api/_static/image10.png "ajouter un nouveau modèle à l’application web")

    *Ajouter un nouveau modèle à l’application web*
2. Dans le **ajouter un nouvel élément** boîte de dialogue, nommez le nouveau fichier **Contact.cs** et cliquez sur **ajouter.**

    ![Création du nouveau fichier de classe de Contact](build-restful-apis-with-aspnet-web-api/_static/image11.png "crée le nouveau fichier de classe de Contact")

    *Création du nouveau fichier de classe de Contact*
3. Ajoutez le code en surbrillance suivant à la **Contact** classe.

    (Code d’extrait de code - *Web API Lab - Ex01 - classe de Contact*)


    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample2.cs)]
4. Dans le **ContactController** de classes, sélectionnez le mot **chaîne** dans la définition de méthode de la **obtenir** (méthode) et tapez le mot *Contact*. Une fois le mot est tapé dans, un indicateur apparaît au début du mot **Contact**. Soit maintenez le **Ctrl** clé et appuyez sur la touche point (.) ou cliquez sur l’icône à l’aide de la souris pour ouvrir la boîte de dialogue de l’assistance dans l’éditeur de code, pour renseigner automatiquement le **à l’aide de** directive pour les modèles espace de noms.

    ![À l’aide de l’assistance d’Intellisense pour les déclarations d’espace de noms](build-restful-apis-with-aspnet-web-api/_static/image12.png)

    *À l’aide de l’assistance d’Intellisense pour les déclarations d’espace de noms*
5. Modifier le code pour le **obtenir** méthode pour qu’elle retourne un tableau d’instances de modèle de Contact.

    (Code d’extrait de code - *Lab d’API Web - Ex01 - retour d’une liste de contacts*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample3.cs)]
6. Appuyez sur **F5** pour déboguer l’application web dans le navigateur. Pour afficher les modifications apportées à la sortie de réponse de l’API, procédez comme suit.

    1. Une fois que le navigateur s’ouvre, appuyez sur **F12** si les outils de développement ne sont pas encore ouvertes.
    2. Cliquez sur le **réseau** onglet.
    3. Appuyez sur la **démarrer capture** bouton.
    4. Ajouter le suffixe d’URL **/api/contact** à l’URL dans la barre d’adresses et appuyez sur la **entrée** clé.
    5. Appuyez sur la **vue détaillée** bouton.
    6. Sélectionnez le **corps de réponse** onglet. Vous devez voir une chaîne JSON représentant la forme sérialisée d’un tableau d’instances de Contact.

    ![JSON sérialisée des résultats d’un appel de méthode Web API complex](build-restful-apis-with-aspnet-web-api/_static/image13.png "JSON sérialisée des résultats d’un appel de méthode Web API complex")

    *Sortie JSON sérialisée d’un appel de méthode Web API complexe*

<a id="Ex1Task4"></a>

<a id="Task_4_-_Extracting_Functionality_into_a_Service_Layer"></a>
#### <a name="task-4---extracting-functionality-into-a-service-layer"></a>Tâche 4 : extraction de fonctionnalités dans une couche de Service

Cette tâche va vous montrer comment extraire des fonctionnalités dans une couche de Service pour faciliter aux développeurs de séparer leurs fonctionnalités de service à partir de la couche de contrôleur, ce qui permet la réutilisation des services qui effectuent réellement le travail.

1. Créez un nouveau dossier dans la racine de la solution et nommez-le **Services**. Pour ce faire, cliquez sur **ContactManager** projet, sélectionnez **ajouter** | **nouveau dossier**, nommez-le *Services*.

    ![Dossier des Services de création](build-restful-apis-with-aspnet-web-api/_static/image14.png "dossier de création de Services")

    *Création du dossier de Services*
2. Cliquez sur le **Services** et sélectionnez **ajouter | Classe...**  dans le menu contextuel.

    ![Ajout d’une nouvelle classe dans le dossier Services](build-restful-apis-with-aspnet-web-api/_static/image15.png "ajoutant une nouvelle classe dans le dossier Services")

    *Ajout d’une nouvelle classe dans le dossier Services*
3. Lorsque le **ajouter un nouvel élément** boîte de dialogue s’affiche, nommez la nouvelle classe **ContactRepository** et cliquez sur **ajouter**.

    ![Création d’un fichier de classe pour contenir le code de la couche de service de référentiel de Contact](build-restful-apis-with-aspnet-web-api/_static/image16.png "création d’un fichier de classe pour contenir le code de la couche de service de référentiel de Contact")

    *Création d’un fichier de classe pour contenir le code de la couche de service de référentiel de Contact*
4. Ajouter un à l’aide de la directive pour le **ContactRepository.cs** fichier à inclure l’espace de noms de modèles.


    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample4.cs)]
5. Ajoutez le code en surbrillance suivant à la **ContactRepository.cs** fichier pour implémenter la méthode de GetAllContacts.

    (Code d’extrait de code - *Web de référentiel de Contact API Lab - Ex01 -*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample5.cs)]
6. Ouvrez le **ContactController.cs** fichier s’il n’est pas déjà ouvert.
7. Ajoutez le code suivant à l’aide d’instruction à la section de déclaration d’espace de noms du fichier.


    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample6.cs)]
8. Ajoutez le code en surbrillance suivant à la **ContactController.cs** classe pour ajouter un champ privé pour représenter l’instance du référentiel, afin que le reste de la classe les membres peuvent apporter utiliser l’implémentation de service.

    (Code d’extrait de code - *contrôleur Contact d’API Lab - Ex01 - Web*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample7.cs)]
9. Modifier la **obtenir** méthode afin qu’il rend utiliser le service de référentiel de contact.

    (Code d’extrait de code - *Lab d’API Web - Ex01 - retour d’une liste de contacts via le référentiel*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample8.cs)]
10. Placez un point d’arrêt sur la **ContactController**de **obtenir** définition de méthode.

    ![Ajout de points d’arrêt au contrôleur de contact](build-restful-apis-with-aspnet-web-api/_static/image17.png "Ajout de points d’arrêt au contrôleur de contact")

    *Ajout de points d’arrêt au contrôleur de contact*
11. Appuyez sur **F5** pour exécuter l’application.
12. Lorsque le navigateur s’ouvre, appuyez sur **F12** pour ouvrir les outils de développement.
13. Cliquez sur le **réseau** onglet.
14. Cliquez sur le **démarrer capture** bouton.
15. Ajoutez l’URL dans la barre d’adresses avec le suffixe **/api/contact** et appuyez sur **entrée** pour charger le contrôleur d’API.
16. Visual Studio 2012 doit arrêter une fois **obtenir** méthode commence l’exécution.

    ![Avec rupture dans la méthode Get](build-restful-apis-with-aspnet-web-api/_static/image18.png "avec rupture dans la méthode Get")

    *Avec rupture dans la méthode Get*
17. Appuyez sur **F5** pour continuer.
18. Revenez à Internet Explorer si elle n’est pas déjà dans le focus. Notez la fenêtre de capture réseau.

    ![Réseau dans Internet Explorer affichant les résultats de l’appel d’API Web](build-restful-apis-with-aspnet-web-api/_static/image19.png "réseau dans Internet Explorer affichant les résultats de l’appel d’API Web")

    *Vue du réseau dans Internet Explorer affichant les résultats de l’appel d’API Web*
19. Cliquez sur le **vue détaillée** bouton.
20. Cliquez sur le **corps de réponse** onglet. Notez la sortie JSON de l’appel d’API, et comment il représente les deux contacts récupérées par la couche de service.

    ![Affichage de la sortie JSON à partir de l’API Web dans la fenêtre Outils de développement](build-restful-apis-with-aspnet-web-api/_static/image20.png "affichage de la sortie JSON à partir de l’API Web dans la fenêtre Outils de développement")

    *Affichage de la sortie JSON à partir de l’API Web dans la fenêtre Outils de développement*

<a id="Exercise2"></a>

<a id="Exercise_2_Create_a_ReadWrite_Web_API"></a>
### <a name="exercise-2-create-a-readwrite-web-api"></a>Exercice 2 : Création d’une API de Web en lecture/écriture

Dans cet exercice, vous allez implémenter POST et PUT des méthodes pour le Gestionnaire de contact pour l’activer avec les fonctionnalités de modification de données.

<a id="Ex2Task1"></a>

<a id="Task_1_-_Opening_the_Web_API_Project"></a>
#### <a name="task-1---opening-the-web-api-project"></a>Tâche 1 : ouvrez le projet d’API Web

Dans cette tâche, vous allez vous préparer à améliorer le projet d’API Web créé dans l’exercice 1 afin qu’il puisse accepter l’entrée d’utilisateur.

1. Exécutez **Visual Studio 2012 Express pour Web**, pour cela, accédez à **Démarrer** et type **Visual Studio Express pour le Web** puis appuyez sur **entrée**.
2. Ouvrez le **commencer** solution situé dans **Source/Ex02-ReadWriteWebAPI/début/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
3. Ouvrez le **Services/ContactRepository.cs** fichier.

<a id="Ex2Task2"></a>

<a id="Task_2_-_Adding_Data-Persistence_Features_to_the_Contact_Repository_Implementation"></a>
#### <a name="task-2---adding-data-persistence-features-to-the-contact-repository-implementation"></a>Tâche 2 : ajout de fonctionnalités de persistance des données à l’implémentation de référentiel de Contact

Dans cette tâche, vous serez augmenter la classe ContactRepository du projet Web API créé dans l’exercice 1 afin qu’il peut conserver et accepter l’entrée d’utilisateur et de nouvelles instances de Contact.

1. Ajoutez la constante suivante à la **ContactRepository** classe pour représenter le nom de la mémoire cache élément clé nom du serveur web plus loin dans cet exercice.

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample9.cs)]
2. Ajoutez un constructeur pour le **ContactRepository** contenant le code suivant.

    (Code d’extrait de code - *Web API Lab - Ex02 - référentiel de Contact constructeur*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample10.cs)]
3. Modifier le code pour le **GetAllContacts** méthode comme illustré ci-dessous.

    (Code d’extrait de code - *Web API Lab - Ex02 - obtenir tous les Contacts*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample11.cs)]

    > [!NOTE]
    > Cet exemple est destiné à des fins de démonstration et utilisera le web cache du serveur comme un support de stockage, afin que les valeurs seront accessibles à plusieurs clients simultanément, plutôt que d’utiliser un mécanisme de stockage de Session ou une durée de vie de stockage de demande. Un peut utiliser Entity Framework, le stockage XML ou toute autre variété à la place le cache du serveur web.
4. Implémentez une nouvelle méthode nommée **SaveContact** à la **ContactRepository** classe pour effectuer le travail de l’enregistrement d’un contact. Le **SaveContact** méthode doit prendre un seul **Contact** signalant la réussite ou Échec de la valeur de paramètre et retourner une valeur booléenne.

    (Code d’extrait de code - *Web API Lab - Ex02 - implémentation de la méthode SaveContact*)

    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample12.cs)]

<a id="Exercise3"></a>

<a id="Exercise_3_Consume_the_Web_API_from_an_HTML_Client"></a>
### <a name="exercise-3-consume-the-web-api-from-an-html-client"></a>Exercice 3 : Utiliser l’API Web à partir d’un Client HTML

Dans cet exercice, vous allez créer un client HTML pour appeler l’API Web. Ce client facilitera l’échange de données avec l’API Web à l’aide de JavaScript et affiche les résultats dans un navigateur web à l’aide d’un balisage HTML.

<a id="Ex3Task1"></a>

<a id="Task_1_-_Modifying_the_Index_View_to_Provide_a_GUI_for_Displaying_Contacts"></a>
#### <a name="task-1---modifying-the-index-view-to-provide-a-gui-for-displaying-contacts"></a>Tâche 1 : modification de l’affichage de l’Index pour fournir une interface graphique utilisateur pour l’affichage des Contacts

Dans cette tâche, vous allez modifier la vue d’Index par défaut de l’application web pour prendre en charge la spécification de l’affichage de la liste des contacts existants dans un navigateur HTML.

1. Ouvrez **Visual Studio 2012 Express pour Web** s’il n’est pas déjà ouvert.
2. Ouvrez le **commencer** solution situé dans **Source/Ex03-ConsumingWebAPI/début/** dossier. Dans le cas contraire, vous pouvez continuer à utiliser le **fin** solution obtenue par la fin de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
3. Ouvrez le **Index.cshtml** fichier situé à **Views/Home** dossier.
4. Remplacez le code HTML au sein de l’élément div avec l’id **corps** afin qu’il ressemble au code suivant.


    [!code-html[Main](build-restful-apis-with-aspnet-web-api/samples/sample13.html)]
5. Ajoutez le code Javascript suivant en bas du fichier à exécuter la requête HTTP à l’API Web.


    [!code-cshtml[Main](build-restful-apis-with-aspnet-web-api/samples/sample14.cshtml)]
6. Ouvrez le **ContactController.cs** fichier s’il n’est pas déjà ouvert.
7. Placez un point d’arrêt sur la **obtenir** méthode de la **ContactController** classe.

    ![Le fait de placer un point d’arrêt sur la méthode Get de contrôleur d’API](build-restful-apis-with-aspnet-web-api/_static/image21.png "placer un point d’arrêt sur la méthode Get de contrôleur d’API")

    *Le fait de placer un point d’arrêt sur la méthode Get de contrôleur d’API*
8. Appuyez sur **F5** pour exécuter le projet. Le navigateur chargera le document HTML.

    > [!NOTE]
    > Assurez-vous que vous y accédez à l’URL racine de votre application.
9. Une fois le chargement de la page et le code JavaScript s’exécute, le point d’arrêt est atteint et suspend l’exécution du code dans le contrôleur.

    ![Débogage dans les appels d’API Web à l’aide de Visual Studio Express pour le Web](build-restful-apis-with-aspnet-web-api/_static/image22.png "débogage dans les appels d’API Web à l’aide de Visual Studio Express pour le Web")

    *Débogage dans l’appel d’API Web à l’aide de Visual Studio 2012 Express pour le Web*
10. Supprimer le point d’arrêt et appuyez sur **F5** ou de la barre d’outils débogage **continuer** pour continuer le chargement de la vue dans le navigateur. Une fois que la fin de l’appel d’API Web vous devez voir les contacts retournés à partir de l’API Web appeler affichée en tant qu’éléments de liste dans le navigateur.

    ![Résultats de l’appel d’API affichées dans le navigateur en tant qu’éléments de liste](build-restful-apis-with-aspnet-web-api/_static/image23.png "résultats de l’appel d’API affichées dans le navigateur en tant qu’éléments de liste")

    *Résultats de l’appel d’API affichées dans le navigateur en tant qu’éléments de liste*
11. Arrêter le débogage.

<a id="Ex3Task2"></a>

<a id="Task_2_-_Modifying_the_Index_View_to_Provide_a_GUI_for_Creating_Contacts"></a>
#### <a name="task-2---modifying-the-index-view-to-provide-a-gui-for-creating-contacts"></a>Tâche 2 : modification de l’affichage de l’Index pour fournir une interface graphique utilisateur pour la création de Contacts

Dans cette tâche, vous continuerez à modifier l’affichage de l’Index de l’application MVC. Un formulaire sera être ajouté à la page HTML qui capture l’entrée d’utilisateur et les envoyer à l’API Web pour créer un nouveau Contact, et une nouvelle méthode de contrôleur d’API Web sera créée pour collecter la date à partir de l’interface utilisateur graphique.

1. Ouvrez le **ContactController.cs** fichier.
2. Ajouter une nouvelle méthode à la classe de contrôleur nommée **Post** comme indiqué dans le code suivant.

    (Code d’extrait de code - *API Lab - Ex03 - Post méthode Web*)


    [!code-csharp[Main](build-restful-apis-with-aspnet-web-api/samples/sample15.cs)]
3. Ouvrez le **Index.cshtml** de fichiers dans Visual Studio s’il n’est pas déjà ouvert.
4. Ajoutez le code HTML suivant au fichier juste après la liste non triée, que vous avez ajouté dans la tâche précédente.


    [!code-html[Main](build-restful-apis-with-aspnet-web-api/samples/sample16.html)]
5. Dans l’élément de script en bas du document, ajoutez le code en surbrillance suivant pour gérer les événements de clic de bouton, ce qui valide les données à l’API Web à l’aide d’un appel HTTP POST.

    [!code-html[Main](build-restful-apis-with-aspnet-web-api/samples/sample17.html)]
6. Dans **ContactController.cs**, placez un point d’arrêt sur la **Post** (méthode).
7. Appuyez sur **F5** pour exécuter l’application dans le navigateur.
8. Une fois que la page est chargée dans le navigateur, tapez un nouveau contact nom et Id et cliquez sur le **enregistrer** bouton.

    ![Le document de client HTML est chargé dans le navigateur](build-restful-apis-with-aspnet-web-api/_static/image24.png "le document de client HTML est chargé dans le navigateur")

    *Le document HTML client chargée dans le navigateur*
9. Lorsque la fenêtre du débogueur s’arrête le **Post** méthode, étudier les propriétés de la **contactez** paramètre. Les valeurs doivent correspondre les données que vous avez entré dans le formulaire.

    ![L’objet de Contact qui est envoyée à l’API Web depuis le client](build-restful-apis-with-aspnet-web-api/_static/image25.png "objet du Contact qui est envoyé à l’API Web à partir du client")

    *L’objet de Contact qui est envoyé à l’API Web à partir du client*
10. Étape via la méthode dans le débogueur jusqu'à ce que le **réponse** variable a été créée. Lors de l’inspection de la **variables locales** fenêtre du débogueur, vous verrez que toutes les propriétés ont été définies.

    ![La réponse après création dans le débogueur](build-restful-apis-with-aspnet-web-api/_static/image26.png "la réponse après création dans le débogueur")

    *La réponse après création dans le débogueur*
11. Si vous appuyez sur **F5** ou cliquez sur **continuer** dans le débogueur va terminer la demande. Une fois que vous passez au navigateur, le nouveau contact a été ajouté à la liste des contacts stockés par le **ContactRepository** mise en œuvre.

    ![Le navigateur reflète la création réussie de la nouvelle instance de contact](build-restful-apis-with-aspnet-web-api/_static/image27.png "le navigateur reflète la création réussie de la nouvelle instance de contact")

    *Le navigateur reflète la création réussie de la nouvelle instance de contact*

> [!NOTE]
> En outre, vous pouvez déployer cette application à Azure suit [annexe c : publication une Application ASP.NET MVC 4, à l’aide de Web Deploy](#AppendixC).


* * *

<a id="Summary"></a>
## <a name="summary"></a>Résumé

Cet atelier vous a présenté à la nouvelle infrastructure de l’API Web ASP.NET et à l’implémentation de l’API Web RESTful à l’aide de l’infrastructure. À partir d’ici, créer un référentiel qui facilite la persistance des données à l’aide de n’importe quel nombre de mécanismes et associer ce service plutôt que de simple celui fourni en exemple dans ce laboratoire. API Web prend en charge un nombre de fonctionnalités supplémentaires, telles que l’activation des communications des clients non-HTML écrites dans n’importe quel langage qui prend en charge HTTP et JSON ou XML. La capacité d’héberger une API Web en dehors d’une application web par défaut est également possible, ainsi que des est la possibilité de créer vos propres formats de sérialisation.

Le site Web ASP.NET a une zone dédiée à l’infrastructure de l’API Web ASP.NET au [ [https://asp.net/web-api](https://asp.net/web-api)](https://asp.net/web-api). Ce site continuera à fournir les informations les plus récentes, des exemples et des informations relatives à l’API Web, donc vérifier fréquemment si vous souhaitez d’explorent l’art de la création d’API Web personnalisées permettent de pratiquement n’importe quelle infrastructure de périphérique ou de développement.

<a id="AppendixA"></a>

<a id="Appendix_A_Using_Code_Snippets"></a>
## <a name="appendix-a-using-code-snippets"></a>Annexe a : à l’aide d’extraits de Code

Avec des extraits de code, vous avez tout le code que vous avez besoin. Le document lab vous indique exactement quand vous pouvez les utiliser, comme indiqué dans l’illustration suivante.

![À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet](build-restful-apis-with-aspnet-web-api/_static/image28.png "des extraits de code à l’aide de Visual Studio pour insérer du code dans votre projet")

*À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet*

<a id="CodeSnippetUsingKeyBoard"></a>

<a id="To_add_a_code_snippet_using_the_keyboard_C_only"></a>
### <a name="to-add-a-code-snippet-using-the-keyboard-c-only"></a>Pour ajouter un extrait de code à l’aide du clavier (c# uniquement)

1. Placez le curseur où vous souhaitez insérer le code.
2. Commencez à taper le nom de l’extrait de code (sans espaces ou des traits d’union).
3. Observez comment IntelliSense affiche les noms des extraits de code de mise en correspondance.
4. Sélectionnez l’extrait de code correct (ou continuez à taper jusqu'à ce que le nom de l’extrait de code entier est sélectionné).
5. Appuyez sur la touche Tab à deux reprises pour insérer l’extrait de code à l’emplacement du curseur.

    ![Commencez à taper le nom de l’extrait de code](build-restful-apis-with-aspnet-web-api/_static/image29.png "commencez à taper le nom de l’extrait de code")

    *Commencez à taper le nom de l’extrait de code*

    ![Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance](build-restful-apis-with-aspnet-web-api/_static/image30.png "appuyez sur Tab pour sélectionner l’extrait de code en surbrillance")

    *Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance*

    ![Appuyez sur Tab à nouveau et l’extrait de code sont développés](build-restful-apis-with-aspnet-web-api/_static/image31.png "appuyez sur Tab à nouveau et l’extrait de code seront développe.")

    *Appuyez sur Tab à nouveau et l’extrait de code seront développe.*

<a id="CodeSnippetUsingMouse"></a>

<a id="To_add_a_code_snippet_using_the_mouse_C_Visual_Basic_and_XML"></a>
### <a name="to-add-a-code-snippet-using-the-mouse-c-visual-basic-and-xml"></a>Pour ajouter un extrait de code à l’aide de la souris (c#, Visual Basic et XML)

1. Clic droit où vous souhaitez insérer l’extrait de code.
2. Sélectionnez **insérer un extrait** suivie **mes extraits de Code**.
3. Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus.

    ![Avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait](build-restful-apis-with-aspnet-web-api/_static/image32.png "avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait")

    *Avec le bouton droit sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait*

    ![Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus](build-restful-apis-with-aspnet-web-api/_static/image33.png "choisir l’extrait de code approprié dans la liste, en cliquant sur celle-ci")

    *Sélectionnez l’extrait de code approprié dans la liste, en cliquant sur celle-ci*

<a id="AppendixB"></a>

<a id="Appendix_B_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-b-installing-visual-studio-express-2012-for-web"></a>Annexe b : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](build-restful-apis-with-aspnet-web-api/_static/image34.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](build-restful-apis-with-aspnet-web-api/_static/image35.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](build-restful-apis-with-aspnet-web-api/_static/image36.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](build-restful-apis-with-aspnet-web-api/_static/image37.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](build-restful-apis-with-aspnet-web-api/_static/image38.png)

    *VS Express pour la vignette du Web*

<a id="AppendixC"></a>

<a id="Appendix_C_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
## <a name="appendix-c-publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Annexe c : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

Cette annexe sera vous montrent comment créer un nouveau site web à partir du portail Azure et de publier l’application que vous avez obtenu en suivant le laboratoire, en tirant parti de la fonctionnalité de publication Web Deploy fournie par Azure.

<a id="ApxCTask1"></a>

<a id="Task_1_-_Creating_a_New_Web_Site_from_the_Windows_Azure_Portal"></a>
#### <a name="task-1---creating-a-new-web-site-from-the-azure-portal"></a>Tâche 1 - Création d’un Site Web à partir du portail Azure

1. Accédez à la [portail de gestion Azure](https://manage.windowsazure.com/) et connectez-vous en utilisant les informations d’identification Microsoft associées à votre abonnement.

    > [!NOTE]
    > Avec Azure, vous pouvez héberger des Sites Web ASP.NET 10 gratuitement et puis faire évoluer à mesure que votre trafic augmente. Vous pouvez vous inscrire [ici](http://aka.ms/aspnet-hol-azure).

    ![Ouvrez une session sur le portail Windows Azure](build-restful-apis-with-aspnet-web-api/_static/image39.png "connectez-vous au portail Windows Azure")

    *Connectez-vous au portail*
2. Cliquez sur **nouveau** sur la barre de commandes.

    ![Création d’un Site Web](build-restful-apis-with-aspnet-web-api/_static/image40.png "création d’un Site Web")

    *Création d’un Site Web*
3. Cliquez sur **de calcul** | **Site Web**. Puis sélectionnez **création rapide** option. Fournir une URL disponible pour le nouveau site web et cliquez sur **créer un Site Web**.

    > [!NOTE]
    > Azure est l’hôte pour une application web en cours d’exécution dans le cloud que vous pouvez contrôler et gérer. L’option Création rapide vous permet de déployer une application web complète sur Azure à partir du portail à l’extérieur. Il n’inclut pas les étapes de configuration d’une base de données.

    ![Création d’un Site Web à l’aide de la création rapide](build-restful-apis-with-aspnet-web-api/_static/image41.png "création d’un Site Web à l’aide de la création rapide")

    *Création d’un Site Web à l’aide de la création rapide*
4. Attendez que la nouvelle **Site Web** est créé.
5. Une fois que le Site Web est créé, cliquez sur le lien situé sous le **URL** colonne. Vérifiez que le nouveau Site Web fonctionne.

    ![Navigation vers le nouveau site web](build-restful-apis-with-aspnet-web-api/_static/image42.png "exploration vers le nouveau site web")

    *Navigation vers le nouveau site web*

    ![Site Web en cours d’exécution](build-restful-apis-with-aspnet-web-api/_static/image43.png "site Web en cours d’exécution")

    *Site Web en cours d’exécution*
6. Revenez au portail et cliquez sur le nom du site web sous le **nom** colonne pour afficher les pages de gestion.

    ![Ouvrir les pages de gestion du site web](build-restful-apis-with-aspnet-web-api/_static/image44.png "ouvrir les pages de gestion de site web")

    *Ouvrir les pages de gestion de Site Web*
7. Dans le **tableau de bord** sous le **coup de œil rapide** , cliquez sur le **télécharger le profil de publication** lien.

    > [!NOTE]
    > Le *le profil de publication* contient toutes les informations nécessaires pour publier une application web à une Azure pour chaque méthode de publication activée. Le profil de publication contient les URL, les informations d’identification utilisateur et les chaînes de base de données requis pour se connecter à et de s’authentifier auprès de chacun des points de terminaison pour laquelle une méthode de publication est activée. **Microsoft WebMatrix 2**, **Microsoft Visual Studio Express pour Web** et **Microsoft Visual Studio 2012** prise en charge la lecture de publier les profils pour automatiser la configuration de ces programmes pour la publication d’applications web vers Azure.

    ![Profil de publication de téléchargement du site web](build-restful-apis-with-aspnet-web-api/_static/image45.png "profil de publication de téléchargement du site web")

    *Profil de publication de téléchargement du Site Web*
8. Téléchargez le fichier de profil de publication à un emplacement connu. Davantage dans cet exercice, vous verrez comment utiliser ce fichier pour publier une application web vers Azure à partir de Visual Studio.

    ![L’enregistrement du fichier de profil de publication](build-restful-apis-with-aspnet-web-api/_static/image46.png "l’enregistrement du profil de publication")

    *L’enregistrement du fichier de profil de publication*

<a id="ApxCTask2"></a>

<a id="Task_2_-_Configuring_the_Database_Server"></a>
#### <a name="task-2---configuring-the-database-server"></a>Tâche 2 : configuration du serveur de base de données

Si votre application se sert de SQL Server vous devez créer un serveur de base de données SQL des bases de données. Si vous souhaitez déployer une application simple qui n’utilise pas de SQL Server, vous pouvez ignorer cette tâche.

1. Vous devez un serveur de base de données SQL pour stocker la base de données de l’application. Vous pouvez afficher les serveurs de base de données SQL à partir de votre abonnement dans le portail de gestion Azure à **bases de données Sql** | **serveurs** | **tableau de bord du serveur**. Si vous ne disposez pas d’un serveur créé, vous pouvez créer un à l’aide de la **ajouter** bouton sur la barre de commandes. Prenez note de la **nom du serveur et les URL, nom de connexion d’administrateur et un mot de passe**, comme vous allez l’utiliser dans les tâches suivantes. Ne créez pas encore, la base de données telle qu’elle sera créée dans une étape ultérieure.

    ![Tableau de bord de serveur SQL de base de données](build-restful-apis-with-aspnet-web-api/_static/image47.png "tableau de bord de serveur SQL de base de données")

    *Tableau de bord de serveur SQL de base de données*
2. Dans la tâche suivante, vous allez tester la connexion de base de données à partir de Visual Studio, pour cette raison, vous devez inclure votre adresse IP locale dans la liste du serveur de **adresses IP autorisées**. Pour ce faire, cliquez sur **configurer**, sélectionnez l’adresse IP à partir de **adresse IP du Client actuel** et le coller dans le **adresse IP de début** et **adresse IP de fin** zones de texte et cliquez sur le ![add-client-ip-address-ok-button](build-restful-apis-with-aspnet-web-api/_static/image48.png) bouton.

    ![Ajout d’adresse IP du Client](build-restful-apis-with-aspnet-web-api/_static/image49.png)

    *Ajout d’adresse IP du Client*
3. Une fois la **adresse IP du Client** est ajouté aux adresses IP autorisées de liste, cliquez sur **enregistrer** pour confirmer les modifications.

    ![Confirmer les modifications](build-restful-apis-with-aspnet-web-api/_static/image50.png)

    *Confirmer les modifications*

<a id="ApxCTask3"></a>

<a id="Task_3_-_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
#### <a name="task-3---publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Tâche 3 : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

1. Revenez à la solution ASP.NET MVC 4. Dans le **l’Explorateur de solutions**, cliquez sur le projet de site web et sélectionnez **publier**.

    ![Publication de l’Application](build-restful-apis-with-aspnet-web-api/_static/image51.png "publication de l’Application")

    *Publier le site web*
2. Importer le profil de publication que vous avez enregistré dans la première tâche.

    ![L’importation du profil de publication](build-restful-apis-with-aspnet-web-api/_static/image52.png "l’importation du profil de publication")

    *Importation du profil de publication*
3. Cliquez sur **valider la connexion**. Une fois la Validation terminée. Cliquez sur **suivant**.

    > [!NOTE]
    > La validation est terminée une fois que vous voyez une coche verte apparaît en regard du bouton Valider la connexion.

    ![Validation de la connexion](build-restful-apis-with-aspnet-web-api/_static/image53.png "validation de la connexion")

    *Validation de la connexion*
4. Dans le **paramètres** sous le **bases de données** , cliquez sur le bouton en regard de la zone de texte de la connexion de votre base de données (par exemple, **DefaultConnection**).

    ![Configuration de déploiement Web](build-restful-apis-with-aspnet-web-api/_static/image54.png "configuration de déploiement Web")

    *Configuration de déploiement Web*
5. Configurer la connexion de base de données comme suit :

    - Dans le **nom du serveur** tapez votre URL de base de données SQL server à l’aide du *tcp :* préfixe.
    - Dans **nom d’utilisateur** tapez le nom de connexion de votre administrateur de serveur.
    - Dans **mot de passe** votre mot de passe du compte de connexion administrateur serveur.
    - Tapez un nouveau nom de base de données, par exemple : *MVC4SampleDB*.

    ![Configuration de chaîne de connexion de destination](build-restful-apis-with-aspnet-web-api/_static/image55.png "configuration de chaîne de connexion de destination")

    *Configuration de chaîne de connexion de destination*
6. Cliquez ensuite sur **OK**. Lorsque vous êtes invité à créer la base de données, cliquez sur **Oui**.

    ![Création de la base de données](build-restful-apis-with-aspnet-web-api/_static/image56.png "création de la chaîne de la base de données")

    *Création de la base de données*
7. La chaîne de connexion que vous allez utiliser pour se connecter à la base de données SQL dans Windows Azure est indiquée dans la zone de texte par défaut de connexion. Cliquez ensuite sur **Suivant**.

    ![Chaîne de connexion pointant vers la base de données SQL](build-restful-apis-with-aspnet-web-api/_static/image57.png "chaîne de connexion pointant vers la base de données SQL")

    *Chaîne de connexion pointant vers la base de données SQL*
8. Dans le **aperçu** , cliquez sur **publier**.

    ![Publication de l’application web](build-restful-apis-with-aspnet-web-api/_static/image58.png "publication de l’application web")

    *Publication de l’application web*
9. Une fois le processus de publication terminé, votre navigateur par défaut s’ouvre le site web publié.

    ![Publication d’application sur Windows Azure](build-restful-apis-with-aspnet-web-api/_static/image59.png "Application publiée dans Windows Azure")

    *Application publiée sur Azure*
