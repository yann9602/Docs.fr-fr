---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
title: "Gestion (création d’applications de Cloud du monde réel avec Azure) d’erreurs transitoires | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/03/2015
ms.topic: article
ms.assetid: 7ead83bc-c08c-4b26-8617-00e07292e35c
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
msc.type: authoredcontent
ms.openlocfilehash: 3caeeb83e4c074ae0ffc30f035d793a821eb6be2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="transient-fault-handling-building-real-world-cloud-apps-with-azure"></a>Gestion (création d’applications de Cloud du monde réel avec Azure) d’erreurs transitoires
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Lorsque vous créez une application de cloud du monde réel, l’une des choses que vous devez tenir compte est la gestion des interruptions de service temporaire. Ce problème est important identifie de façon unique dans les applications cloud, car vous êtes dépendent des connexions réseau et des services externes. Vous pouvez obtenir fréquemment des problèmes peu sont généralement de réparation automatique, et si vous n’êtes pas prêt à les gérer de manière intelligente, ils allez entraîner une mauvaise expérience pour vos clients.

## <a name="causes-of-transient-failures"></a>Causes des erreurs temporaires

Vous trouverez dans l’environnement de cloud qui a échoué et supprimer les connexions se produisent régulièrement de la base de données. Qui est en partie parce que vous allez via plusieurs équilibreurs de charge par rapport à l’environnement local dans lequel votre serveur web et le serveur de base de données ont une connexion physique directe. En outre, parfois, lorsque vous êtes dépend d’un service mutualisé, vous verrez appels au délai d’attente ou get service plus lent, car une autre personne qui utilise le service il sollicite fortement. Dans d’autres cas, vous pouvez être l’utilisateur qui a atteint le service trop fréquemment, et le service vous – refuse les connexions – limite délibérément afin de vous empêcher d’affecter les autres clients du service.

## <a name="use-smart-retryback-off-logic-to-mitigate-the-effect-of-transient-failures"></a>Logique de nouvelle tentative/temporisation intelligente permet d’atténuer l’effet d’erreurs temporaires

Au lieu de lever une exception et afficher une page non disponible ou une erreur à votre client, vous pouvez identifier les erreurs sont généralement transitoires, et automatiquement réessayer l’opération qui a généré l’erreur qui espère avant que la durée pendant laquelle vous allez aboutisse. La plupart du temps l’opération réussit, la seconde fois, et vous allez récupérer à partir de l’erreur sans le client ayant été prenant en charge les qu’un problème est survenu.

Il existe plusieurs méthodes que vous pouvez implémenter la logique de nouvelle tentative actives.

- Le Microsoft Patterns &amp; pratiques groupe a un [bloc applicatif de pannes gestion](https://msdn.microsoft.com/en-us/library/dn440719(v=pandp.60).aspx) qui fait tout pour vous si vous utilisez ADO.NET pour l’accès de base de données SQL (et non via Entity Framework). Vous venez de définir une stratégie pour les nouvelles tentatives : nombre de tentatives pour retenter une requête ou commande délai d’attente entre les tentatives – et de type wrap SQL du code dans un *à l’aide de* bloc.

    [!code-csharp[Main](transient-fault-handling/samples/sample1.cs)]

    TFH prend également en charge [Azure In-Role Cache](https://msdn.microsoft.com/en-us/library/windowsazure/dn386103.aspx) et [Service Bus](https://azure.microsoft.com/services/service-bus/).
- Lorsque vous utilisez Entity Framework vous généralement ne fonctionnent pas directement avec les connexions SQL, vous ne pouvez pas utiliser ce package de modèles et pratiques, mais Entity Framework 6 génère ce genre de logique de nouvelle tentative dans le framework. De la même façon, vous spécifiez la stratégie de nouvelle tentative, et puis EF utilise cette stratégie lorsqu’il accède à la base de données.

    Pour utiliser cette fonctionnalité dans l’application corriger, tout ce que nous devons faire est ajouter une classe qui dérive de *DbConfiguration* et activer la logique de nouvelle tentative.

    [!code-csharp[Main](transient-fault-handling/samples/sample2.cs)]

    Pour les exceptions de base de données SQL qui le framework identifie en tant qu’erreurs temporaires en général, le code indique à EF de recommencer l’opération jusqu'à 3 fois, avec un délai de temporisation exponentiels entre les nouvelles tentatives et un délai maximal de 5 secondes. Interruption exponentielle signifie que, après chaque tentative ayant échoué, elle attendra pendant plus de temps avant de réessayer. Si trois tentatives dans une ligne échouent, elle lève une exception. La section suivante sur disjoncteurs explique la raison pour laquelle vous souhaitez qu’interruption exponentielle et un nombre limité de nouvelles tentatives.

    Vous pouvez avoir des problèmes similaires lorsque vous utilisez le service de stockage Azure, que l’application de réparation pour les objets BLOB, et l’API cliente de stockage .NET implémente déjà le même type de logique. Vous spécifiez simplement la stratégie de nouvelle tentative, ou vous ne devez même pas si vous êtes satisfait avec les paramètres par défaut.

<a id="circuitbreakers"></a>
## <a name="circuit-breakers"></a>Disjoncteurs

Il existe plusieurs raisons pour lesquelles vous ne souhaitez pas réessayer trop de fois sur une période trop longue :

- Trop d’utilisateurs persistante une nouvelle tentative de demandes ayant échoué peuvent dégrader expérience des autres utilisateurs. Si des millions de personnes sont toutes les effectue plusieurs tentatives de requêtes vous Impossible monopolise des files d’attente de distribution IIS et empêche que votre application traite les demandes qu’il peut gérer correctement.
- Si tout le monde est une nouvelle tentative en raison d’un échec du service, il pourrait être tel nombre de demandes en file d’attente que le service obtient saturation quand elle commence à récupérer.
- Si l’erreur est due à la limitation et qu’une fenêtre de temps que le service utilise pour la limitation, continue de nouvelles tentatives peuvent déplacer cette fenêtre et entraîner la limitation continuer.
- Vous pouvez avoir un utilisateur en attente d’une page web à restituer. Attente de personnes de fabrication trop long peut être plus agaçantes que relativement rapidement conseillent réessayer ultérieurement.

Interruption exponentielle résout certains de ces problèmes en limitant la fréquence des tentatives de qu'un service peut obtenir à partir de votre application. Mais vous devez également avoir *disjoncteurs*: cela signifie que, à un certain seuil de votre application s’arrête une nouvelle tentative et accepte d’autres actions, telles qu’une de ces réessayer :

- Personnalisée. Si vous ne peut pas obtenir une cotation Reuters, peut-être que vous pouvez l’obtenir de Bloomberg ; ou, si vous ne pouvez pas obtenir des données à partir de la base de données, peut-être que vous pouvez l’obtenir à partir du cache.
- Échec en mode silencieux. Si ce que vous avez besoin d’un service n’est pas tout ou rien pour votre application, simplement retourner null lorsque vous ne pouvez pas obtenir les données. Si vous affichez une tâche corriger et le service Blob ne répond pas, vous pouvez afficher les détails de la tâche sans l’image.
- Échouer rapidement. Erreur de l’utilisateur pour éviter de saturer le service avec une nouvelle demandes susceptibles de provoquer une interruption de service pour d’autres utilisateurs ou d’étendre une fenêtre de limitation. Vous pouvez afficher un message convivial « essayez plus tard ».

Il n’existe aucune stratégie de nouvelle tentative conséquente. Vous pouvez réessayer plusieurs fois et attendre plus longtemps dans un processus de travail asynchrone en arrière-plan que vous le feriez dans une application web synchrone où un utilisateur est en attente d’une réponse. Vous pouvez attendre de plus entre les nouvelles tentatives pour un service de base de données relationnelle que vous le feriez pour un service de cache. Voici un exemple recommandé les stratégies pour vous donner une idée de la façon dont les nombres peuvent varier de nouvelle tentative. (« Première rapide » ne signifie aucun délai avant la première nouvelle tentative.

![Exemples de stratégies de nouvelle tentative](transient-fault-handling/_static/image1.png)

Pour obtenir des conseils de stratégie de base de données SQL nouvelle tentative, consultez [résoudre les erreurs temporaires et les erreurs de connexion à la base de données SQL](https://azure.microsoft.com/documentation/articles/sql-database-connectivity-issues/).

## <a name="summary"></a>Résumé

Une stratégie de nouvelle tentative/temporisation peut contribuer à rendre des erreurs temporaires invisibles au client la plupart du temps, et Microsoft fournit les infrastructures que vous pouvez utiliser afin de réduire votre travail d’implémentation d’une stratégie si vous utilisez ADO.NET, Entity Framework ou Azure Service de stockage.

Dans le [chapitre suivant](distributed-caching.md), nous allons examiner comment améliorer les performances et fiabilité à l’aide de la mise en cache distribuée.

## <a name="resources"></a>Ressources

Pour plus d'informations, reportez-vous aux ressources suivantes :

Documentation

- [Meilleures pratiques pour la création de Services à grande échelle sur les Services Cloud Azure](https://msdn.microsoft.com/en-us/library/windowsazure/jj717232.aspx). Livre blanc par Mark Simms et Michael Thomassy. Similaire à la série de prévention de défaillance mais le passage en plus de détails sur les procédures. Consultez la section télémesure et Diagnostics.
- [Prévention de défaillance : Aide sur les Architectures de Cloud résilientes](https://msdn.microsoft.com/en-us/library/windowsazure/jj853352.aspx). Livre blanc par Marc Mercuri, Ulrich Homann et Andrew Townhill. Version de la page Web de la série de vidéos de prévention de défaillance.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Consultez la nouvelle tentative modèle, le modèle de contrôleur de l’Agent du planificateur.
- [Tolérance de pannes dans la base de données SQL Azure](https://blogs.msdn.com/b/windowsazure/archive/2012/07/30/fault-tolerance-in-windows-azure-sql-database.aspx). Billet de blog de Tony Petrossian.
- [Entity Framework - résilience de connexion / de la logique de nouvelle tentative](https://msdn.microsoft.com/en-us/data/dn456835). Comment utiliser et personnaliser la gestion des fonctionnalités d’Entity Framework 6 d’erreurs transitoires.
- [Résilience des connexions et l’Interception de commande avec Entity Framework dans une Application ASP.NET MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md). Quatrième dans une série de didacticiels neuf parties, montre comment configurer la fonctionnalité de résilience de connexion EF 6 pour la base de données SQL.

Vidéos

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série en neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente les principaux concepts et les principes d’architecture de manière très accessible et intéressante, avec récits tirés de l’expérience de Microsoft Customer Advisory Team (CAT) avec des clients réels. Consultez la description de disjoncteurs dans épisode 3 en commençant à 40:55.
- [Construction Big : Leçons reçues des clients Azure - partie II](https://channel9.msdn.com/Events/Build/2012/3-030). Gestion et l’instrumentation de tous les éléments d’erreur entretiens Mark Simms sur la conception de l’échec, temporaire.

Exemple de code

- [Cloud Service Fundamentals dans Azure](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Exemple d’application créée par le Microsoft Azure équipe de consultants clients qui montre comment utiliser le [Enterprise Library gestion bloc d’erreurs transitoires](http://nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/) (TFH). Pour plus d’informations, consultez [Cloud Service Fundamentals couche Data Access – gestion temporaire des erreurs de](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx). TFH est recommandé pour l’accès de base de données à l’aide d’ADO.NET directement (sans utiliser Entity Framework).

>[!div class="step-by-step"]
[Précédent](monitoring-and-telemetry.md)
[Suivant](distributed-caching.md)
