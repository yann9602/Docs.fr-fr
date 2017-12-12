---
uid: signalr/overview/getting-started/real-time-web-applications-with-signalr
title: "Ateliers pratiques : Les Applications Web en temps réel avec SignalR | Documents Microsoft"
author: rick-anderson
description: "La possibilité de distribuer côté serveur de contenu aux clients connectés, il se trouve en temps réel des fonctionnalités des applications Web en temps réel. Pour les développeurs ASP.NET, ASP..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/16/2014
ms.topic: article
ms.assetid: ba07958c-42e1-4da0-81db-ba6925ed6db0
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/real-time-web-applications-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 96d3b8b82f78d8f6da85012aac8a1411cf297e26
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="hands-on-lab-real-time-web-applications-with-signalr"></a>Ateliers pratiques : Les Applications Web en temps réel avec SignalR
====================
par [Web Camps équipe](https://twitter.com/webcamps)

[Télécharger Camps Web Kit de formation](http://aka.ms/webcamps-training-kit)

> La possibilité de distribuer côté serveur de contenu aux clients connectés, il se trouve en temps réel des fonctionnalités des applications Web en temps réel. Pour les développeurs ASP.NET, **ASP.NET SignalR** est une bibliothèque pour ajouter des fonctionnalités web en temps réel à leurs applications. Il bénéficie de plusieurs transports, automatiquement en sélectionnant le mieux transport disponible étant donné le client et mieux transport disponibles du serveur. Il tire parti des **WebSocket**, API HTML5 qui permet la communication bidirectionnelle entre le navigateur et le serveur.
> 
> **SignalR** fournit également une API simple, de haut niveau par le biais du serveur au client RPC (appel de fonctions JavaScript dans les navigateurs de vos clients à partir du code .NET côté serveur) dans votre application ASP.NET, ainsi que l’ajout des raccordements utiles pour la gestion de la connexion, par exemple les événements de connexion/déconnexion, les connexions de regroupement et d’autorisation.
> 
> **SignalR** est une abstraction sur certains des transports qui sont nécessaires pour effectuer un travail en temps réel entre client et serveur. A **SignalR** connexion démarre en tant que HTTP et est ensuite promue à un **WebSocket** connexion si elle est disponible. **WebSocket** est le transport idéal pour **SignalR**, car elle permet d’optimiser l’utilisation de la mémoire du serveur, possède la latence la plus faible et les fonctionnalités plus sous-jacent (tel que les communications en duplex intégral entre client et serveur), mais il comporte également des exigences plus strictes : **WebSocket** nécessite que le serveur à utiliser des **Windows Server 2012** ou **Windows 8**, ainsi que de **.NET framework 4.5**. Si ces conditions ne sont pas remplies, **SignalR** essaie d’utiliser d’autres transports ses connexions (comme *Ajax longue d’interrogation*).
> 
> Le **SignalR** API contient deux modèles pour la communication entre les clients et serveurs : **des connexions persistantes** et **concentrateurs**. A **connexion** représente un point de terminaison simple pour l’envoi de destinataire unique, regroupées ou diffuser des messages. A **Hub** est un pipeline plus haut niveau basé sur l’API de connexion qui permet à votre client et le serveur appeler des méthodes sur eux directement.
> 
> ![Architecture de SignalR](real-time-web-applications-with-signalr/_static/image1.png)
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [http://aka.ms/webcamps-training-kit](http://aka.ms/webcamps-training-kit).


<a id="Overview"></a>
## <a name="overview"></a>Vue d'ensemble

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Envoyer des notifications à partir du serveur au client à l’aide de SignalR.
- Montée en charge votre application SignalR à l’aide **SQL Server**.

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaire pour terminer cet atelier pratique :

- [Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/) ou supérieur

<a id="Setup"></a>
### <a name="setup"></a>Installation

Pour exécuter les exercices de cet atelier, vous devez configurer votre environnement tout d’abord.

1. Ouvrez une fenêtre de l’Explorateur Windows et accédez à l’atelier **Source** dossier.
2. Avec le bouton droit **Setup.cmd** et sélectionnez **exécuter en tant qu’administrateur** pour lancer le processus d’installation qui sera configurer votre environnement et installer les extraits de code Visual Studio pour ce laboratoire.
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

1. [Utilisation des données en temps réel à l’aide de SignalR](#Exercise1)
2. [Mise à l’échelle à l’aide de SQL Server](#Exercise2)

Durée estimée pour effectuer ce laboratoire : **60 minutes**

> [!NOTE]
> Lorsque vous démarrez Visual Studio, vous devez sélectionner une des collections de paramètres prédéfinis. Chaque collection prédéfinie est conçue pour faire correspondre un style de développement particulier et détermine les dispositions de fenêtres, le comportement de l’éditeur, extraits de code IntelliSense et les options de boîte de dialogue. Les procédures de cet atelier décrivent les actions nécessaires pour accomplir une tâche donnée dans Visual Studio lorsque vous utilisez la **paramètres de développement généraux** collection. Si vous choisissez une collection de paramètres différents pour votre environnement de développement, il est possible les différences dans les étapes que vous devez prendre en compte.


<a id="Exercise1"></a>
### <a name="exercise-1-working-with-real-time-data-using-signalr"></a>Exercice 1 : Utilisation des données en temps réel à l’aide de SignalR

Lors de la conversation est souvent utilisée comme un exemple, vous pouvez effectuer un ensemble beaucoup plus avec des fonctionnalités Web en temps réel. Lorsqu’un utilisateur actualise une page web pour afficher les nouvelles données ou les implémente page Ajax longue d’interrogation pour extraire les nouvelles données, vous pouvez utiliser SignalR.

Prend en charge de SignalR **par émission de données serveur** ou **diffusion** fonctionnalité ; il gère automatiquement les gestion des connexions. Dans les connexions HTTP classiques pour la communication client-serveur, la connexion est rétablie pour chaque demande, mais SignalR fournit une connexion permanente entre le client et le serveur. SignalR qu'appelle le code du serveur à un code client dans le navigateur à l’aide d’appels de procédure distante (RPC), plutôt que le modèle demande-réponse nous savoir dès aujourd'hui.

Dans cet exercice, vous allez configurer le **fou questionnaire** application SignalR permet d’afficher le tableau de bord des statistiques avec les mesures de mise à jour sans avoir à actualiser la page entière.

<a id="Ex1Task1"></a>
#### <a name="task-1--exploring-the-geek-quiz-statistics-page"></a>Tâche 1 : découverte de la Page de statistiques du questionnaire fou

Dans cette tâche, vous allez passer par l’application et vérifier l’affichage de la page de statistiques et comment vous pouvez améliorer la façon dont les informations est mises à jour.

1. Ouvrez **Visual Studio Express 2013 pour le Web** et ouvrez le **GeekQuiz.sln** solution situé dans le **Source\Ex1-WorkingWithRealTimeData\Begin** dossier.
2. Appuyez sur **F5** pour exécuter la solution. Le **connecter** page doit apparaître dans le navigateur.

    ![Exécution de la solution](real-time-web-applications-with-signalr/_static/image2.png "la solution en cours d’exécution")

    *Exécution de la solution*
3. Cliquez sur **enregistrer** dans le coin supérieur droit de la page pour créer un nouvel utilisateur dans l’application.

    ![Inscrire le lien](real-time-web-applications-with-signalr/_static/image3.png "lien du Registre")

    *Lien du Registre*
4. Dans le **inscrire** , entrez un **nom d’utilisateur** et **mot de passe**, puis cliquez sur **inscrire**.

    ![Inscription d’un utilisateur](real-time-web-applications-with-signalr/_static/image4.png "inscription d’un utilisateur")

    *Inscription d’un utilisateur*
5. L’application enregistre le nouveau compte et l’utilisateur est authentifié et redirigé vers la page d’accueil affichant la première question du questionnaire.
6. Ouvrir le **statistiques** page dans une nouvelle fenêtre et placer le **accueil** page et **statistiques** page côte-à-côte.

    ![Côte à côte windows](real-time-web-applications-with-signalr/_static/image5.png "côte à windows de côté")

    *Côte à côte windows*
7. Dans le **accueil** page, répondez à la question en cliquant sur une des options.

    ![Répondre à une question](real-time-web-applications-with-signalr/_static/image6.png "répondre à une question")

    *Répondre à une question*
8. Après avoir cliqué sur un des boutons, la réponse doit apparaître.

    ![Question ayant obtenu une réponse correcte](real-time-web-applications-with-signalr/_static/image7.png "Question ayant obtenu une réponse correcte")

    *Question a répondu correctement*
9. Notez que les informations fournies dans la page de statistiques sont obsolètes. Actualisez la page pour afficher les résultats de la mise à jour.

    ![Page de statistiques](real-time-web-applications-with-signalr/_static/image8.png "page de statistiques")

    *Page des statistiques*
10. Revenez à Visual Studio et arrêter le débogage.

<a id="Ex1Task2"></a>
#### <a name="task-2--adding-signalr-to-geek-quiz-to-show-online-charts"></a>Tâche 2 : ajout SignalR au questionnaire fou pour afficher les graphiques en ligne

Dans cette tâche, vous ajoutez SignalR à la solution et envoyer automatiquement des mises à jour aux clients lorsqu’une nouvelle réponse est envoyée au serveur.

1. À partir de la **outils** menu dans Visual Studio, sélectionnez **Gestionnaire de Package de bibliothèque**, puis cliquez sur **Package Manager Console**.
2. Dans le **Package Manager Console** fenêtre, exécutez la commande suivante :

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample1.ps1)]

    ![Installation du package SignalR](real-time-web-applications-with-signalr/_static/image9.png "SignalR installation du package")

    *Installation du package SignalR*

    > [!NOTE]
    > Lors de l’installation **SignalR** version de packages NuGet 2.0.2 à partir d’une application MVC 5 tout nouveau, vous devez mettre à jour manuellement **OWIN** packages vers la version 2.0.1 (ou version ultérieure) avant d’installer SignalR. Pour ce faire, vous pouvez exécuter le script suivant dans le **Package Manager Console**:
    > 
    > [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample2.ps1)]
    > 
    > Dans une version ultérieure de SignalR, les dépendances OWIN seront automatiquement mis à jour.
3. Dans **l’Explorateur de solutions**, développez le **Scripts** dossier et constatez que SignalR *js* fichiers ont été ajoutés à la solution.

    ![SignalR JavaScript fait référence à](real-time-web-applications-with-signalr/_static/image10.png "SignalR JavaScript fait référence à")

    *Références de SignalR JavaScript*
4. Dans **l’Explorateur de solutions**, avec le bouton droit le **GeekQuiz** projet, sélectionnez **ajouter** | **nouveau dossier**et nommez-le  **Concentrateurs**.
5. Avec le bouton droit le **concentrateurs** et sélectionnez **ajouter | Un nouvel élément**.

    ![Ajouter un nouvel élément](real-time-web-applications-with-signalr/_static/image11.png "ajouter un nouvel élément")

    *Ajouter un nouvel élément*
6. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez le **Visual C# | Web | SignalR** nœud dans le volet gauche, sélectionnez **classe de concentrateur SignalR (v2)** dans le volet central, nommez le fichier **StatisticsHub.cs** et cliquez sur **ajouter**.

    ![Boîte de dialogue Ajouter nouvel élément](real-time-web-applications-with-signalr/_static/image12.png "boîte de dialogue Ajouter nouvel élément")

    *Ajouter la boîte de dialogue Nouvel élément*
7. Remplacez le code dans le **StatisticsHub** classe par le code suivant.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - StatisticsHubClass*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample3.cs)]
8. Ouvrez **Startup.cs** et ajoutez la ligne suivante à la fin de la **Configuration** (méthode).

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - MapSignalR*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample4.cs)]
9. Ouvrez le **StatisticsService.cs** page à l’intérieur de la **Services** dossier et ajoutez le code suivant à l’aide de directives.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - UsingDirectives*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample5.cs)]
10. Afin d’informer les clients connectés de mises à jour, vous tout d’abord extraire un **contexte** objet pour la connexion actuelle. Le **Hub** objet contient des méthodes pour envoyer des messages à un seul client ou la diffusion à tous les clients. Ajoutez la méthode suivante à la **StatisticsService** classe pour la diffusion des données statistiques.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - NotifyUpdatesMethod*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample6.cs)]

    > [!NOTE]
    > Dans le code ci-dessus, vous utilisez un nom de méthode arbitraire pour appeler une fonction sur le client (par exemple : *updateStatistics*). Le nom de la méthode que vous spécifiez est interprété comme un objet dynamique, ce qui signifie qu'aucune IntelliSense ou la validation lors de la compilation. L’expression est évaluée au moment de l’exécution. Lorsque l’appel de méthode s’exécute, SignalR envoie le nom de méthode et les valeurs de paramètre au client. Si le client possède une méthode qui correspond au nom, cette méthode est appelée et les valeurs de paramètre sont passés à ce dernier. Si aucune méthode correspondante n’est trouvée sur le client, aucune erreur n’est déclenché. Pour plus d’informations, reportez-vous à [ASP.NET SignalR concentrateurs API Guide](../guide-to-the-api/hubs-api-guide-server.md).
11. Ouvrir le **TriviaController.cs** page à l’intérieur de la **contrôleurs** dossier et ajoutez le code suivant à l’aide de directives.

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample7.cs)]
12. Ajoutez le code en surbrillance suivant à la **Post** méthode d’action.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - NotifyUpdatesCall*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample8.cs)]
13. Ouvrez le **Statistics.cshtml** page à l’intérieur de la **vues | Accueil** dossier. Recherchez le **Scripts** section et ajouter les références de script suivantes au début de la section.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - SignalRScriptReferences*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample9.cshtml)]

    > [!NOTE]
    > Lorsque vous ajoutez SignalR et autres bibliothèques de script à votre projet Visual Studio, le Gestionnaire de Package peut installer une version du fichier de script SignalR qui est plus récente que la version indiquée dans cette rubrique. Assurez-vous que la référence de script dans votre code correspond à la version de la bibliothèque de script installée dans votre projet.
14. Ajoutez le code en surbrillance suivant pour le client de se connecter au concentrateur SignalR et mettre à jour les données de statistiques lorsqu’un nouveau message est reçu à partir du concentrateur.

    (Code d’extrait de code - *RealTimeSignalR - Ex1 - SignalRClientCode*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample10.cshtml)]

    Dans ce code, vous créez un Proxy de Hub et l’inscription d’un gestionnaire d’événements pour écouter les messages envoyés par le serveur. Dans ce cas, vous écoutez les messages envoyés via le *updateStatistics* (méthode).

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Tâche 3 : exécution de la Solution

Dans cette tâche, vous allez exécuter la solution pour vérifier que l’affichage des statistiques est mis à jour automatiquement à l’aide de SignalR après avoir répondu à une nouvelle question.

1. Appuyez sur **F5** pour exécuter la solution.

    > [!NOTE]
    > Si pas connecté à l’application, connectez-vous avec l’utilisateur que vous avez créé dans la tâche 1.
2. Ouvrir le **statistiques** page dans une nouvelle fenêtre et placer le **accueil** page et **statistiques** page côte à côte comme vous l’avez fait dans la tâche 1.
3. Dans le **accueil** page, répondez à la question en cliquant sur une des options.

    ![Répondre à une autre question](real-time-web-applications-with-signalr/_static/image13.png "répondre à une autre question")

    *Répondre à une autre question*
4. Après avoir cliqué sur un des boutons, la réponse doit apparaître. Notez que les informations statistiques sur la page sont automatiquement mis à jour après avoir répondu à la question avec les informations mises à jour sans avoir à actualiser la page entière.

    ![Page de statistiques actualisées après réponse](real-time-web-applications-with-signalr/_static/image14.png "page statistiques actualisée après la réponse")

    *Page de statistiques actualisée après la réponse*

<a id="Exercise2"></a>
### <a name="exercise-2-scaling-out-using-sql-server"></a>Exercice 2 : Mise à l’échelle à l’aide de SQL Server

Lors de la mise à l’échelle une application web, vous pouvez généralement choisir entre *l’évolution verticale* et *montée en puissance* options. *Montée en puissance* au moyen d’un plus grand serveur avec davantage de ressources (UC, RAM, etc.) lors de la *montée en puissance parallèle* signifie l’ajout de serveurs pour gérer la charge. Le problème avec ce dernier est que les clients peuvent seront acheminés vers différents serveurs. Un client qui est connecté à un serveur ne recevra pas les messages envoyés à partir d’un autre serveur.

Vous pouvez résoudre ces problèmes à l’aide d’un composant appelé *fond de panier*pour transférer des messages entre les serveurs. Avec un fond de panier activé, chaque instance de l’application envoie des messages au fond de panier, et le fond de panier les transfère vers les autres instances de l’application.

Il existe actuellement trois types de fonds de panier pour SignalR :

- **Windows Azure Service Bus**. Service Bus est une infrastructure de messagerie qui permet aux composants d’envoyer des messages faiblement couplées.
- **SQL Server**. L’infrastructure d’intégration SQL Server écrit des messages dans des tables SQL. L’infrastructure d’intégration utilise Service Broker de messagerie efficace. Toutefois, il fonctionne également si Service Broker n’est pas activé.
- **Redis**. Redis est un magasin clé-valeur de mémoire. Redis prend en charge un modèle de publication/abonnement (« pub/sub ») pour envoyer des messages.

Chaque message est envoyé via un bus de messages. Un bus de messages implémente la [IMessageBus](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) interface, qui fournit une abstraction publication/abonnement. Les fonds de panier de travail en remplaçant la valeur par défaut **IMessageBus** avec un bus conçu pour que fond de panier.

Chaque instance de serveur se connecte au fond de panier via le bus. Lorsqu’un message est envoyé, il est placé dans le fond de panier, et le fond de panier envoie à tous les serveurs. Lorsqu’un serveur reçoit un message à partir de l’infrastructure d’intégration, il stocke le message dans sa mémoire cache locale. Le serveur puis remet les messages aux clients à partir de sa mémoire cache locale.

Pour plus d’informations sur le fonctionne du fond de panier SignalR, de lire ce [article](../performance/scaleout-in-signalr.md).

> [!NOTE]
> Il existe certains scénarios où un fond de panier peut devenir un goulot d’étranglement. Voici quelques scénarios classiques de SignalR :
> 
> - [Serveur diffusion](tutorial-server-broadcast-with-signalr.md) (par exemple, les cotations boursières) : fonds de panier fonctionnent bien pour ce scénario, étant donné que le serveur contrôle la vitesse à laquelle les messages sont envoyés.
> - [Client à](tutorial-getting-started-with-signalr.md) (par exemple, chat) : dans ce scénario, l’infrastructure d’intégration peut être un goulot d’étranglement si le nombre de messages augmentent avec le nombre de clients ; autrement dit, si le taux de messages augmente proportionnellement plus les clients se connecter.
> - [En temps réel de haute fréquence](tutorial-high-frequency-realtime-with-signalr.md) (par exemple, les jeux en temps réel) : un fond de panier n’est pas recommandée pour ce scénario.


Dans cet exercice, vous allez utiliser **SQL Server** pour distribuer les messages entre les **fou questionnaire** application. Vous allez exécuter ces tâches sur un ordinateur de test unique pour apprendre à définir la configuration, mais pour obtenir l’effet, vous devez déployer l’application SignalR à deux ou plusieurs serveurs. Vous devez également installer SQL Server sur l’un des serveurs ou sur un serveur dédié distinct.

![Montée en puissance parallèle à l’aide de SQL Server diagramme](real-time-web-applications-with-signalr/_static/image15.png)

<a id="Ex2Task1"></a>
#### <a name="task-1---understanding-the-scenario"></a>Tâche 1 : présentation du scénario

Dans cette tâche, vous allez exécuter 2 instances de **fou questionnaire** simulant IIS plusieurs instances sur votre ordinateur local. Dans ce scénario, lorsque les réponses aux questions de nature d’une application, la mise à jour ne sont pas notifiée sur la page de statistiques de la deuxième instance. Cette simulation ressemble à un environnement dans lequel votre application est déployée sur plusieurs instances et à l’aide d’un équilibreur de charge pour communiquer avec eux.

1. Ouvrez le **Begin.sln** solution situé dans le **Source/Ex2-ScalingOutWithSQLServer/début** dossier. Une fois chargé, vous pouvez remarquer sur le **l’Explorateur de serveurs** des noms différents, mais les structures que la solution comporte deux projets avec identiques. Cela simule deux instances de la même application en cours d’exécution sur votre ordinateur local.

    ![Commencer la Solution en simulant 2 Instances du questionnaire de fou](real-time-web-applications-with-signalr/_static/image16.png "Solution simulant 2 Instances de fou questionnaire de commencer")

    *Solution 2 Instances de fou questionnaire de simulation de commencer*
2. Ouvrez la page de propriétés de la solution en cliquant sur le nœud de la solution et en sélectionnant **propriétés**. Sous **projet de démarrage**, sélectionnez **plusieurs projets de démarrage** et modifiez le **Action** valeur pour les deux projets pour *Démarrer*.

    ![Démarrer plusieurs projets](real-time-web-applications-with-signalr/_static/image17.png "démarrant plusieurs projets")

    *Démarrer plusieurs projets*
3. Appuyez sur **F5** pour exécuter la solution. L’application est lancée à deux instances de **fou questionnaire** dans des ports différents, en simulant plusieurs instances de la même application. Épingler un des navigateurs à gauche et l’autre sur la droite de l’écran. Connectez-vous avec vos informations d’identification ou inscrire un nouvel utilisateur. Une fois connecté, conservez la page de nature à gauche et accédez à la **statistiques** page dans le navigateur sur la droite.

    ![Fou questionnaire côte à côte](real-time-web-applications-with-signalr/_static/image18.png)

    *Fou questionnaire côte à côte*

    ![Questionnaire fou dans des Ports différents](real-time-web-applications-with-signalr/_static/image19.png)

    *Questionnaire fou dans des Ports différents*
4. Démarrer répondant à des questions dans le navigateur de gauche et vous remarquerez que la **statistiques** page dans le navigateur de droite n’est pas actualisé. C’est parce que **SignalR** mettre en cache local utilise pour distribuer les messages sur leurs clients et ce scénario simule plusieurs instances, par conséquent, le cache n’est pas partagé entre eux. Vous pouvez vérifier que **SignalR** fonctionne en test les mêmes étapes, mais à l’aide d’une seule application. Dans les tâches suivantes, vous allez configurer une infrastructure d’intégration pour répliquer les messages entre les instances.
5. Revenez à Visual Studio et arrêter le débogage.

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-sql-server-backplane"></a>Tâche 2 : création de l’infrastructure d’intégration SQL Server

Dans cette tâche, vous allez créer une base de données qui servira de fond de panier pour le **fou questionnaire** application. Vous allez utiliser **l’Explorateur d’objets SQL Server** pour parcourir votre serveur et d’initialiser la base de données. En outre, vous allez activer la **Service Broker**.

1. Dans **Visual Studio**, ouvrez menu **vue** et sélectionnez **l’Explorateur d’objets SQL Server**.
2. Se connecter à votre instance de base de données locale en double-cliquant sur le **SQL Server** nœud et en sélectionnant **ajouter SQL Server...**  option.

    ![Ajout d’une Instance SQL Server](real-time-web-applications-with-signalr/_static/image20.png "Ajout d’une Instance SQL Server")

    *Ajout d’une instance de SQL Server à l’Explorateur d’objets SQL Server*
3. Définir le **nom du serveur** à *(localdb) \v11.0* et laisser **l’authentification Windows** comme votre mode d’authentification. Cliquez sur **connexion** pour continuer.

    ![Connexion à LocalDB](real-time-web-applications-with-signalr/_static/image21.png "la connexion à la base de données locale")

    *Connexion à la base de données locale*
4. Maintenant que vous êtes connecté à votre instance de base de données locale, vous devez créer une base de données qui représente le fond de panier de SQL Server pour SignalR. Pour ce faire, cliquez sur le **bases de données** nœud et sélectionnez **ajouter une nouvelle base de données**.

    ![Ajout d’une base de données](real-time-web-applications-with-signalr/_static/image22.png "Ajout d’une base de données")

    *Ajout d’une base de données*
5. Définissez le nom de la base de données sur *SignalR* et cliquez sur **OK** pour le créer.

    ![Création de la base de données SignalR](real-time-web-applications-with-signalr/_static/image23.png "création de la base de données SignalR")

    *Création de la base de données SignalR*

    > [!NOTE]
    > Vous pouvez choisir n’importe quel nom pour la base de données.
6. Pour recevoir des mises à jour plus efficacement à partir de l’infrastructure d’intégration, il est recommandé pour activer Service Broker pour la base de données. Service Broker fournit une prise en charge native pour la messagerie et de files d’attente dans SQL Server. Le fond de panier fonctionne également sans Service Broker. Ouvrez une nouvelle requête en cliquant sur la base de données et sélectionnez **nouvelle requête**.

    ![Ouvrez une nouvelle requête](real-time-web-applications-with-signalr/_static/image24.png "ouverture d’une nouvelle requête")

    *Ouvrez une nouvelle requête*
7. Pour vérifier si le Service Broker est activé, interrogez la **est\_broker\_activé** colonne dans la **sys.databases** vue de catalogue. Exécutez le script suivant dans la fenêtre de requête ouverte récemment.

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample11.sql)]

    ![Interrogation de l’état du Service Broker](real-time-web-applications-with-signalr/_static/image25.png "interroger l’état du Service Broker")

    *Interrogation de l’état du Service Broker*
8. Si la valeur de la **est\_broker\_activé** colonne dans votre base de données est &quot;0&quot;, utilisez la commande suivante pour l’activer. Remplacez  **&lt;votre base de données&gt;**  avec le nom que vous définissez lors de la création de la base de données (par exemple : SignalR).

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample12.sql)]

    ![Activer Service Broker](real-time-web-applications-with-signalr/_static/image26.png "activer Service Broker")

    *Activer Service Broker*

    > [!NOTE]
    > Si cette requête s’affiche en interblocage, vérifiez qu’il n’y a aucune application connectée à la base de données.

<a id="Ex2Task3"></a>
#### <a name="task-3--configuring-the-signalr-application"></a>Tâche 3 : configuration de l’Application SignalR

Dans cette tâche, vous allez configurer **fou questionnaire** pour se connecter à l’infrastructure d’intégration SQL Server. Vous allez tout d’abord ajouter le **SignalR.SqlServer** package NuGet et définissez la connexion à votre base de données de fond de panier de chaîne.

1. Ouvrez le **Console du Gestionnaire de Package** de **outils** | **Gestionnaire de Package de bibliothèque**. Assurez-vous que **GeekQuiz** projet est sélectionné dans le **projet par défaut** liste déroulante. Tapez la commande suivante pour installer le **Microsoft.AspNet.SignalR.SqlServer** package NuGet.

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample13.ps1)]
2. Répétez l’étape précédente, mais cette fois pour le projet **GeekQuiz2**.
3. Pour configurer l’infrastructure d’intégration SQL Server, ouvrez le **Startup.cs** fichier de la **GeekQuiz** de projet et ajoutez le code suivant à la **configurer** (méthode). Remplacez  **&lt;votre base de données&gt;**  avec votre nom de base de données que vous avez utilisé lors de la création de l’infrastructure d’intégration SQL Server. Répétez cette étape pour le **GeekQuiz2** projet.

    (Code d’extrait de code - *RealTimeSignalR - Ex2 - StartupConfiguration*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample14.cs)]
4. Maintenant que les deux projets sont configurés pour utiliser l’infrastructure d’intégration SQL Server, appuyez sur **F5** pour les exécuter simultanément.
5. Là encore, **Visual Studio** lancera deux instances de **fou questionnaire** dans des ports différents. Un des navigateurs épingler sur la gauche et l’autre sur la droite de l’écran et connectez-vous avec vos informations d’identification. Conserver la page de la nature de gauche et accédez à **statistiques** pagedans le navigateur de droite.
6. Démarrer les réponses aux questions dans le navigateur de gauche. Cette fois-ci, le **statistiques** page est mise à jour grâce à l’infrastructure d’intégration. Basculer entre les applications (**statistiques** est désormais sur la gauche, et **nature** se trouve à droite) et répétez le test pour vérifier qu’elle fonctionne pour les deux instances. Le fond de panier sert un *cache partagé* des messages pour chaque serveur connecté et chaque serveur stocke les messages dans leur propre cache local à distribuer aux clients connectés.
7. Revenez à Visual Studio et arrêter le débogage.
8. Le composant de fond de panier de SQL Server génère automatiquement les tables nécessaires sur la base de données spécifié. Dans le **l’Explorateur d’objets SQL Server** panel, ouvrez la base de données que vous avez créé pour le fond de panier (par exemple : SignalR) et développez ses tables. Vous devez voir les tables suivantes :

    ![Fond de panier généré de Tables](real-time-web-applications-with-signalr/_static/image27.png)

    *Fond de panier généré de Tables*
9. Avec le bouton droit le **SignalR.Messages\_0** de table et sélectionnez **des données d’affichage**.

    ![Table de vue de Messages SignalR le fond de panier](real-time-web-applications-with-signalr/_static/image28.png)

    *Table de vue de Messages SignalR le fond de panier*
10. Vous pouvez voir les différents messages envoyés à la **Hub** lorsque les réponses aux questions de la nature. Le fond de panier distribue ces messages à n’importe quelle instance connectée.

    ![Table des Messages de fond de panier](real-time-web-applications-with-signalr/_static/image29.png)

    *Table des Messages de fond de panier*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Résumé

Dans cet atelier, vous avez appris comment ajouter **SignalR** à votre application et envoyer des notifications à partir du serveur à vos clients connectés à l’aide de **concentrateurs**. En outre, vous avez appris comment faire évoluer votre application en utilisant un *fond de panier* composant lorsque votre application est déployée dans plusieurs instances d’IIS.
