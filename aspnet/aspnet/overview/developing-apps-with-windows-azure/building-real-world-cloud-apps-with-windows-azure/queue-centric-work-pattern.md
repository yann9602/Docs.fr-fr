---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
title: "Modèle de travail centré sur la file d’attente (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: cc1ad51b-40c3-4c68-8620-9aaa0fd1f6cf
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
msc.type: authoredcontent
ms.openlocfilehash: 125d555a9e170ef35dd99e0409a2442d5f9ae34a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="queue-centric-work-pattern-building-real-world-cloud-apps-with-azure"></a>Modèle de travail centré sur la file d’attente (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Précédemment, nous avons vu que l’utilisation de plusieurs services peut entraîner un contrat SLA de « composite », où le contrat SLA effective de l’application est la *produit* des contrats de service individuels. Par exemple, l’application Fix It utilise des Sites Web, de stockage et de base de données SQL. Si l’un de ces services échoue, l’application renvoie une erreur à l’utilisateur.

La mise en cache est un bon moyen de gérer les défaillances temporaires pour le contenu en lecture seule. Mais que se passe-t-il si votre application a besoin effectuer le travail ? Par exemple, lorsque l’utilisateur envoie une nouvelle tâche corriger, l’application ne peut pas placer uniquement la tâche dans le cache. L’application doit écrire la tâche corriger dans un magasin de données permanentes, il peut être traité.

C’est là le modèle centré sur la file d’attente de travail. Ce modèle permet un couplage faible entre un niveau web et un service principal.

Voici comment le modèle fonctionne. Lorsque l’application reçoit une demande, il place d’un élément de travail dans une file d’attente et retourne immédiatement la réponse. Ensuite, un traitement principal distinct extrait des éléments de travail à partir de la file d’attente et effectue le travail.

Le modèle de travail centré sur la file d’attente est utile pour :

- Travail qui est beaucoup de temps (forte latence).
- Travail nécessitant un service externe ne peut pas toujours possible.
- Le travail gourmandes en ressources (UC élevée).
- Travail qui pourraient tirer parti de taux d’audit (soumise à une charge soudaine forte).

## <a name="reduced-latency"></a>Latence réduite

Les files d’attente sont utiles à tout moment, que vous effectuez un travail de longue durée. Si une tâche prend quelques secondes ou plus, au lieu de bloquer l’utilisateur final, placez l’élément de travail dans une file d’attente. Indiquer à l’utilisateur « Nous y travaillons » et utilisez un écouteur de file d’attente pour traiter la tâche en arrière-plan.

Par exemple, lorsque vous achetez un objet à un site de vente en ligne, le site web confirme immédiatement votre commande. Mais cela ne signifie pas que vos objets est déjà un camion remis. Elles placer une tâche dans une file d’attente, et en arrière-plan ils font la vérification de solvabilité, préparation de vos éléments pour l’expédition et ainsi de suite.

Pour les scénarios avec latence courtes, la durée totale de bout en bout peut être plus de temps à l’aide d’une file d’attente, par rapport à l’exécution de façon synchrone la tâche. Mais même les autres avantages peuvent annuler cet inconvénient.

## <a name="increased-reliability"></a>Fiabilité accrue

Dans la version de corriger ce que nous avons étudié jusqu'à présent, le serveur web frontal est étroitement lié à la base de données SQL principal. Si le service de base de données SQL n’est pas disponible, l’utilisateur reçoit une erreur. Si les nouvelles tentatives ne fonctionnent pas (autrement dit, l’échec est plus de transitoire), la seule chose que vous pouvez effectuer est de montrer une erreur et demandez à l’utilisateur de réessayer ultérieurement.

![Diagramme montrant les serveurs web frontaux échec lorsque le serveur principal de base de données SQL a échoué](queue-centric-work-pattern/_static/image1.png)

À l’aide de files d’attente, lorsqu’un utilisateur soumet une tâche corriger, l’application écrit un message à la file d’attente. La charge utile de message est un [JSON](http://json.org/) la représentation sous forme de la tâche. Dès que le message est écrit dans la file d’attente, l’application retourne et immédiatement un message de réussite à l’utilisateur.

Si un des services principaux, tels que la base de données SQL ou de l’écouteur de file d’attente--travailler hors connexion, les utilisateurs peuvent envoyer toujours nouvelles tâches de réparation. Les messages simplement file d’attente jusqu'à ce que les services principaux sont à nouveau disponibles. À ce stade, les services principaux seront rattraper sur la file d’attente.

![Diagramme montrant le web frontal continuer à fonctionner lorsqu’il existe une erreur de base de données SQL](queue-centric-work-pattern/_static/image2.png)

En outre, maintenant vous pouvez ajouter une logique supplémentaire de serveur principal sans se préoccuper de la résilience du serveur frontal. Par exemple, vous souhaiterez envoyer un courrier électronique ou un message SMS au propriétaire chaque fois qu’un nouveau résoudre il est assigné. Si le courrier électronique ou le service SMS devient indisponible, vous pouvez traiter tous les autres éléments et ensuite placer un message dans une file d’attente distinct pour l’envoi de messages de messagerie/SMS.

Notre contrat SLA efficace était auparavant une applications Web &times; stockage &times; base de données SQL = 99,7 %. (Consultez [conception pour faire face aux défaillances](design-to-survive-failures.md).)

Quand vous modifiez l’application d’utiliser une file d’attente, le serveur web frontal dépend uniquement des applications Web et de stockage, pour un contrat SLA de 99,8 % composite. (Notez que les files d’attente font partie du service de stockage Azure, ils sont inclus dans le contrat SLA de même que le stockage d’objets blob).

Si vous avez besoin de mieux à 99,8 %, vous pouvez créer deux files d’attente dans deux régions différentes. Désigner une comme le serveur principal et l’autre en tant que le serveur secondaire. Dans votre application, basculer vers la file d’attente secondaire si la file d’attente principale n’est pas disponible. Le risque des deux ne soit pas disponible en même temps est très faible.

## <a name="rate-leveling-and-independent-scaling"></a>Taux d’audit et de mise à l’échelle indépendante

Les files d’attente sont également utiles pour nous parlons *taux audit* ou *nivellement de charge*.

Les applications Web sont souvent vulnérables aux pics soudains dans le trafic. Vous pouvez utiliser l’échelle automatique pour ajouter automatiquement des serveurs web pour gérer le trafic web accrue, échelle automatique peut ne pas pouvoir réagir assez rapidement à gérer les pics de charge brusques. Si les serveurs web peuvent alléger en partie du travail qu’ils doivent en écrivant un message à une file d’attente, ils peuvent gérer davantage de trafic. Un service principal peut ensuite lire les messages de la file d’attente et les traiter. La profondeur de la file d’attente concernent l’agrandissement ou la réduction de comme la charge entrante varie.

Avec une grande partie de son travail de longue durée déchargé vers un service principal, la couche web peut répondre plus facilement des pics soudains dans le trafic. Et vous faire des économies, car toute quantité donnée de trafic peut être gérée par un nombre de serveurs web.

Vous pouvez faire évoluer le niveau web et le service principal indépendamment. Par exemple, vous devrez peut-être les trois serveurs web, mais qu’un seul serveur traitement file d’attente des messages. Ou, si vous exécutez une tâche de calcul intensif en arrière-plan, vous devrez peut-être plusieurs serveurs principaux.

![](queue-centric-work-pattern/_static/image3.png)

Échelle fonctionne avec les services principaux, ainsi qu’avec la couche web. Vous pouvez monter ou à l’échelle le nombre de machines virtuelles qui traitent les tâches dans la file d’attente, en fonction de l’utilisation du processeur du serveur principal machines virtuelles. Ou, selon le nombre d’éléments dans une file d’attente de mise à l’échelle. Par exemple, vous voyez la mise à l’échelle de maintenir pas plus de 10 éléments dans la file d’attente. Si la file d’attente a plus de 10 éléments, mise à l’échelle ajoutera des machines virtuelles. Quand ils rattraper, mise à l’échelle détruira les machines virtuelles supplémentaires.

## <a name="adding-queues-to-the-fix-it-application"></a>Ajout des files d’attente pour le correctif il Application

Pour implémenter le modèle de file d’attente, nous devons effectuer deux modifications à l’application corriger.

- Lorsqu’un utilisateur soumet une nouvelle tâche corriger, placez la tâche dans la file d’attente, au lieu d’écrire dans la base de données.
- Créer un service principal qui traite les messages dans la file d’attente.

La file d’attente, nous allons utiliser la [Service de stockage de file d’attente Azure](https://www.windowsazure.com/en-us/develop/net/how-to-guides/queue-service/). Une autre option consiste à utiliser [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

Pour déterminer le service de file d’attente à utiliser, tenez compte des comment votre application a besoin pour envoyer et recevoir les messages dans la file d’attente :

- Si vous avez contributif producteurs et consommateurs concurrents, envisagez d’utiliser le Service de stockage de file d’attente Azure. « Producteurs coopérer » signifie que plusieurs processus sont Ajout de messages à une file d’attente. « Consommateurs concurrents » signifie que plusieurs processus extraient des messages à la file d’attente pour les traiter, mais un message donné peut uniquement être traité par un « consommateur ». Si vous avez besoin de débit supérieur à celui que vous pouvez obtenir une file d’attente unique, utiliser les files d’attente supplémentaires et/ou des comptes de stockage supplémentaires.
- Si vous avez besoin d’un [modèle de publication/abonnement](http://en.wikipedia.org/wiki/Publish/subscribe), envisagez d’utiliser des files d’attente de Azure Service Bus.

L’application de corriger le mieux aux producteurs contributif et des modèles de consommateurs concurrents.

Une autre considération est la disponibilité des applications. Le Service de stockage de file d’attente fait partie du même service que nous utilisons pour le stockage d’objets blob, donc l’utiliser n’a aucun effet sur notre contrat SLA. Azure Service Bus est un service distinct avec sa propre contrat SLA. Si nous utilisons des files d’attente du Bus de Service, il faut tenir compte un pourcentage de SLA supplémentaire et notre contrat SLA composite est moins élevé. Lorsque vous choisissez un service de file d’attente, assurez-vous que vous comprenez l’impact de votre choix sur la disponibilité de l’application. Pour plus d’informations, consultez la [ressources](#resources) section.

## <a name="creating-queue-messages"></a>Création de Messages de la file d’attente

Pour placer une tâche corriger sur la file d’attente, le serveur web frontal effectue les étapes suivantes :

1. Créer un [CloudQueueClient](https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.queue.cloudqueueclient.aspx) instance. Le `CloudQueueClient` instance est utilisée pour exécuter des demandes sur le Service de file d’attente.
2. Créer la file d’attente, s’il n’existe pas encore.
3. La tâche corriger la sérialisation.
4. Appelez [CloudQueue.AddMessageAsync](https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.queue.cloudqueue.addmessageasync.aspx) pour placer le message dans la file d’attente.

Nous allons effectuer ce travail dans le constructeur et `SendMessageAsync` (méthode) d’un nouveau `FixItQueueManager` classe.

[!code-csharp[Main](queue-centric-work-pattern/samples/sample1.cs?highlight=11-12,16,18-25)]

Ici, nous utilisons le [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) library pour sérialiser le correctif au format JSON. Vous pouvez utiliser n’importe quelle approche de sérialisation que vous préférez. JSON a l’avantage d’être contrôlable de visu, tout en étant moins détaillés que XML.

Code de qualité production serait ajouter la logique de gestion des erreurs, interrompre si la base de données n’est plus disponible, gérer plus facilement de récupération, créez la file d’attente au démarrage de l’application et gérer «[poison » messages](https://msdn.microsoft.com/en-us/library/ms789028(v=vs.110).aspx). (Un message incohérent est un message qui ne peut pas être traité pour une raison quelconque. Vous ne souhaitez pas les messages incohérents dans cet état dans la file d’attente, où le rôle de travail essaiera en permanence les traiter, échec, essayez à nouveau, échouer, et ainsi de suite.)

Dans l’application frontale de MVC, nous devons mettre à jour le code qui crée une nouvelle tâche. Au lieu de mettre la tâche dans le référentiel, appelez le `SendMessageAsync` méthode illustrée ci-dessus.

[!code-csharp[Main](queue-centric-work-pattern/samples/sample2.cs?highlight=10)]

## <a name="processing-queue-messages"></a>Le traitement des Messages de la file d’attente

Pour traiter les messages dans la file d’attente, nous allons créer un service principal. Le service de serveur principal s’exécutera une boucle infinie qui effectue les étapes suivantes :

1. Obtenir le message suivant à partir de la file d’attente.
2. Désérialiser le message à une tâche corriger.
3. Écrire la tâche réparer la base de données.

Pour héberger le service principal, nous allons créer un Service Cloud Azure qui contient un *rôle de travail*. Un rôle de travail se compose d’un ou plusieurs ordinateurs virtuels qui peuvent effectuer le traitement du serveur principal. Le code qui s’exécute dans ces machines virtuelles extrairont les messages à partir de la file d’attente dès qu’elles deviennent disponibles. Pour chaque message, nous allons désérialiser la charge utile JSON et écrire une instance de l’entité corriger il tâche à la base de données à l’aide de la même référentiel que nous avons utilisés précédemment dans la couche web.

Les étapes suivantes montrent comment ajouter un processus de travail de projet de rôle pour une solution qui contient un projet web standard. Ces étapes ont déjà été effectuées dans le projet corriger que vous pouvez télécharger.

Tout d’abord ajouter un projet de Service Cloud à la solution Visual Studio. Avec le bouton droit de la solution et sélectionnez **ajouter**, puis **nouveau projet**. Dans le volet gauche, développez **Visual C#** et sélectionnez **Cloud**.

[![](queue-centric-work-pattern/_static/image5.png)](queue-centric-work-pattern/_static/image4.png)

Dans le **nouveau Service Cloud Azure** boîte de dialogue, développez le **Visual C#** nœud dans le volet gauche. Sélectionnez **rôle de travail** et cliquez sur l’icône de flèche droite.

![](queue-centric-work-pattern/_static/image6.png)

(Notez que vous pouvez également ajouter un *rôle web*. Nous aurions pu exécuter la corriger frontal dans le même Service Cloud, au lieu de son exécution dans un Site Web Azure. Qui a utilisé pour effectuer des connexions entre les serveurs frontaux et principaux plus facile de coordonner les avantages. Toutefois, pour simplifier cette démonstration, nous allons conserver le serveur frontal dans une application Azure App Service Web et uniquement en exécutant le serveur principal dans un Service Cloud.)

Un nom par défaut est attribué au rôle de travail. Pour modifier le nom, pointez la souris sur le rôle de travail dans le volet droit, puis cliquez sur l’icône de crayon.

![](queue-centric-work-pattern/_static/image7.png)

Cliquez sur **OK** pour terminer la boîte de dialogue. Cette opération ajoute deux projets à la solution Visual Studio.

- un projet Windows Azure qui définit le service cloud, y compris les informations de configuration.
- Un projet de rôle de travail qui définit le rôle de travail.

![](queue-centric-work-pattern/_static/image8.png)

Pour plus d’informations, consultez [création d’un projet Azure avec Visual Studio.](https://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx)

Dans le rôle de travail, nous interroger pour les messages en appelant le `ProcessMessageAsync` méthode de la `FixItQueueManager` classe que nous avons vu plus haut.

[!code-csharp[Main](queue-centric-work-pattern/samples/sample3.cs?highlight=25)]

Le `ProcessMessagesAsync` méthode vérifie s’il existe un message en attente. Si elle existe, il désérialise le message en un `FixItTask` entité et enregistre l’entité dans la base de données. Il effectue une boucle jusqu'à ce que la file d’attente est vide.

[!code-csharp[Main](queue-centric-work-pattern/samples/sample4.cs)]

Charge l’interrogation pour les messages de la file d’attente entraîne une petite transaction, par conséquent, si aucun message n’est en attente d’être traité, le rôle de travail `RunAsync` méthode seconde avant d’interrogation à attendre en appelant `Task.Delay(1000)`.

Dans un projet web, ajouter du code asynchrone automatiquement améliorent les performances, car IIS gère un pool de threads limité. Qui n’est pas le cas dans un projet de rôle de travail. Pour améliorer l’évolutivité du rôle de travail, vous pouvez écrire du code multithread ou utiliser du code asynchrone pour implémenter [programmation parallèle](https://msdn.microsoft.com/en-us/library/ff963553.aspx). L’exemple n’implémente pas la programmation parallèle, mais montre comment rendre le code asynchrone afin de pouvoir implémenter la programmation parallèle.

## <a name="summary"></a>Résumé

Dans ce chapitre, vous avez vu comment améliorer la réactivité des applications, la fiabilité et l’évolutivité en implémentant le modèle de travail centré sur la file d’attente.

Il s’agit du dernier 13 modèles traités dans ce livre électronique, mais il existe bien entendu nombreux autres modèles et pratiques qui peuvent vous aider à créer des applications de cloud réussie. Le [dernier chapitre](more-patterns-and-guidance.md) fournit des liens vers des ressources pour les rubriques qui n’ont pas été traités dans ces 13 modèles.

<a id="resources"></a>
## <a name="resources"></a>Ressources

Pour plus d’informations sur les files d’attente, consultez les ressources suivantes.

Documentation :

- [Partie de files d’attente de stockage Microsoft Azure 1 : Prise en main](http://justazure.com/microsoft-azure-storage-queues-part-1-getting-started/). Article de Roman Schacherl.
- [L’exécution de tâches en arrière-plan](https://msdn.microsoft.com/en-us/library/ff803365.aspx), chapitre 5 du [déplacement des Applications dans le Cloud, 3e édition](https://msdn.microsoft.com/en-us/library/ff728592.aspx) à partir de Microsoft Patterns and Practices. (En particulier, la section [« À l’aide de stockage files d’attente Azure »](https://msdn.microsoft.com/en-us/library/ff803365.aspx#sec7).)
- [Meilleures pratiques pour optimiser la rentabilité des Solutions de messagerie basée sur la file d’attente sur Azure et l’extensibilité](https://msdn.microsoft.com/en-us/library/windowsazure/hh697709.aspx). Livre blanc par Valery Mizonov.
- [Comparaison des files d’attente Azure et files d’attente du Bus de Service](https://msdn.microsoft.com/en-us/magazine/jj159884.aspx). L’article MSDN Magazine, fournit des informations supplémentaires qui peuvent vous aider à choisir le service de file d’attente à utiliser. L’article mentionne que Service Bus dépend des services ACS pour l’authentification, ce qui signifie que les files d’attente service bus seraient indisponibles lorsque des services ACS n’est pas disponible. Toutefois, étant donné que l’article a été écrit, service bus a été modifiée pour pouvoir utiliser [jetons SAP](https://msdn.microsoft.com/en-us/library/windowsazure/dn170477.aspx) comme alternative à des services ACS.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Consultez le manuel de la messagerie asynchrone, les canaux et les filtres de modèle, modèle de compensation des transactions, modèle de consommateurs concurrents, modèle CQRS.
- [CQRS voyage](https://msdn.microsoft.com/en-us/library/jj554200). Livres sur CQRS par Microsoft Patterns and Practices.

Vidéo :

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série de vidéos de neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente les principaux concepts et les principes d’architecture de manière très accessible et intéressante, avec récits tirés de l’expérience de Microsoft Customer Advisory Team (CAT) avec des clients réels. Pour obtenir une présentation du service de stockage Azure et files d’attente, consultez épisode 5 en commençant à 35:13.

>[!div class="step-by-step"]
[Précédent](distributed-caching.md)
[Suivant](more-patterns-and-guidance.md)
