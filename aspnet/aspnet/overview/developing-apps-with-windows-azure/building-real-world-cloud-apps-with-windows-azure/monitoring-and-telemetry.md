---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry
title: "Surveillance et télémétrie (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/09/2015
ms.topic: article
ms.assetid: 7e986ab5-6615-4638-add7-4614ce7b51db
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry
msc.type: authoredcontent
ms.openlocfilehash: dfb0158ec05c890ecf80571d95b22d8c791ba7fc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="monitoring-and-telemetry-building-real-world-cloud-apps-with-azure"></a>Surveillance et télémétrie (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


De nombreuses personnes reposent sur les clients pour leur permettre d’être informé de leur application est arrêté. Qui n’est pas réellement une meilleure pratique en tout lieu et surtout pas dans le cloud. Il n’existe aucune garantie de notification rapide, et lorsque vous recevoir, vous obtenez souvent des données trompeurs ou minime sur ce qui est arrivé. Avec la bonne télémétrie et systèmes de journalisation que vous pouvez être informé de ce qui se passe avec votre application et lorsqu’un élément passe incorrect vous découvrez immédiatement et que vous avez des informations de dépannage utiles pour travailler avec.

## <a name="buy-or-rent-a-telemetry-solution"></a>Acheter ou louer une solution de télémétrie

> [!NOTE]
> Cet article a été écrit avant [Application Insights](https://azure.microsoft.com/services/application-insights/) a été libéré. Application Insights est la meilleure approche pour les solutions de télémétrie sur Azure. Consultez [configurer Application Insights pour votre site Web ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) pour plus d’informations.


Une des choses excellent sur l’environnement de cloud computing est qu’il est très facile d’acheter ou louer votre façon victoire. La télémétrie est un exemple. Sans beaucoup d’efforts, vous pouvez obtenir un système de télémétrie de très bonnes, très économique vers le haut et en cours d’exécution. Il existe un certain nombre de partenaires excellentes qui s’intègrent à Azure, et certaines d'entre elles ont des niveaux gratuits : vous pouvez obtenir la télémétrie de base de la valeur Nothing. Voici quelques ceux actuellement disponibles sur Azure :

- [New Relic](http://newrelic.com/)
- [AppDynamics](http://www.appdynamics.com/)
- [DynaTrace](https://datamarket.azure.com/application/b4011de2-1212-4375-9211-e882766121ff)

À partir de mars 2015, [Microsoft Application Insights pour Visual Studio Online](https://azure.microsoft.com/en-us/documentation/articles/app-insights-get-started/) n’est pas encore libéré, mais est disponible en version préliminaire pour essayer. [Microsoft System Center](http://www.petri.co.il/microsoft-system-center-introduction.htm#) inclut également des fonctionnalités d’analyse.

Nous examinerons rapidement configurer New Relic pour montrer comment il peut être facile à utiliser un système de télémétrie.

Dans le portail de gestion Azure, inscrivez-vous pour le service. Cliquez sur **nouveau**, puis cliquez sur **magasin**. Le **choisir un module complémentaire** boîte de dialogue s’affiche. Faites défiler la liste et cliquez sur **New Relic**.

![Choisissez un module complémentaire](monitoring-and-telemetry/_static/image1.png)

Cliquez sur la flèche droite et choisir la couche de service. Pour cette démonstration, nous allons utiliser le niveau gratuit.

![Personnaliser le module complémentaire](monitoring-and-telemetry/_static/image2.png)

Cliquez sur la flèche droite, confirmez le « achat » et New Relic affiche maintenant sous la forme d’un module complémentaire dans le portail.

![Passez en revue d’achat](monitoring-and-telemetry/_static/image3.png)

![Module complémentaire Relic dans le portail de gestion](monitoring-and-telemetry/_static/image4.png)

Cliquez sur **informations de connexion**, puis copiez la clé de licence.

![Informations de connexion](monitoring-and-telemetry/_static/image5.png)

Accédez à la **configurer** onglet pour votre application web dans le portail, définissez **l’analyse des performances** à **module complémentaire**et définissez la **choisir un module complémentaire** liste déroulante pour **New Relic**. Puis cliquez sur **enregistrer**.

![New Relic dans l’onglet Configurer](monitoring-and-telemetry/_static/image6.png)

Dans Visual Studio, installez le package de nouveau Relic NuGet dans votre application.

![Analytique de développeur dans l’onglet Configurer](monitoring-and-telemetry/_static/image7.png)

Déployer l’application sur Azure et l’utiliser. Créer quelques tâches corriger pour fournir des activités pour New Relic à surveiller.

Puis revenez à la **New Relic** page dans le **modules complémentaires** onglet du portail et cliquez sur **gérer**. Le portail vous envoie au portail de gestion New Relic, à l’aide de l’authentification unique pour l’authentification, vous n’avez pas à entrer de nouveau vos informations d’identification. La page Vue d’ensemble présente une série de statistiques de performances. (Cliquez sur l’image pour afficher la taille totale de page de vue d’ensemble.)

[![Nouvel onglet Relic analyse](monitoring-and-telemetry/_static/image9.png)](monitoring-and-telemetry/_static/image8.png)

Voici quelques-unes des statistiques, que vous pouvez voir :

- Temps de réponse moyen à différents moments de la journée.

    ![Temps de réponse](monitoring-and-telemetry/_static/image10.png)
- Taux de débit (nombre de requêtes par minute) à différents moments de la journée.

    ![Débit](monitoring-and-telemetry/_static/image11.png)
- Temps processeur de serveur consacré à la gestion de différentes demandes HTTP.

    ![Temps de Transaction Web](monitoring-and-telemetry/_static/image12.png)
- Temps processeur passé dans les différentes parties du code d’application :

    ![Détails de suivi](monitoring-and-telemetry/_static/image13.png)
- Statistiques de performances historiques.

    ![Historique des performances](monitoring-and-telemetry/_static/image14.png)
- Les appels aux services externes tels que le service d’objets Blob et les statistiques sur la fiabilité et réactive le service a été.

    ![Services externes](monitoring-and-telemetry/_static/image15.png)

    ![Services externes](monitoring-and-telemetry/_static/image16.png)

    ![Service externe](monitoring-and-telemetry/_static/image17.png)
- Informations sur l’emplacement dans le monde ou dans l’application web des États-Unis trafic provenance.

    ![Geography](monitoring-and-telemetry/_static/image18.png)

Vous pouvez également configurer les rapports et événements. Par exemple, vous pouvez dire tout moment commencer à voir des erreurs, envoyer un courrier électronique au personnel d’alerte de prise en charge pour le problème.

![Rapports](monitoring-and-telemetry/_static/image19.png)

New Relic est un exemple d’un système de télémétrie ; Vous pouvez obtenir tout cela à partir d’autres services. L’avantage du cloud qui est sans avoir à écrire du code et pour un minimum ou aucun frais, soudainement beaucoup plus d’informations sur la façon dont votre application est utilisée et que vos clients réellement rencontrent.

<a id="log"></a>
## <a name="log-for-insight"></a>Journal pour obtenir un aperçu

Un package de télémétrie est une première étape, mais vous devez quand même instrumenter votre propre code. Le service de télémétrie vous indique lorsqu’il existe un problème et vous indique ce que les clients rencontrent, mais il peut ne pas vous donne un lot d’un aperçu de ce qui se passe dans votre code.

Vous ne souhaitez pas avoir à distance à un serveur de production pour voir ce que fait votre application. Qui peut être pratique lorsque vous avez un seul serveur, mais qu’en est-il lorsque vous avez mis à l’échelle à des centaines de serveurs, et vous ne savez pas quel dont vous avez besoin à distance dans ? La journalisation doit fournir suffisamment d’informations que vous devrez jamais à distance dans les serveurs de production à analyser et de déboguer des problèmes. Vous devez journalisation suffisamment d’informations afin que vous pouvez isoler les problèmes uniquement via les journaux.

### <a name="log-in-production"></a>Ouvrez une session en production

De nombreuses personnes activer le suivi en production uniquement lorsqu’il existe un problème et à déboguer. Cela peut introduire un délai important entre l’heure que vous êtes informé d’un problème et l’heure que vous obtenez des informations de dépannage utiles sur elle. Et les informations n’est peut-être pas utiles pour les erreurs intermittentes.

Ce que nous vous recommandons dans l’environnement du cloud où le stockage est bon marché est toujours laisser une session en production. De cette façon, lorsque des erreurs se produisent, vous disposez déjà de les consigner, et vous avez des données historiques qui peuvent aider à vous analysez les problèmes de développement dans le temps ou se produisent régulièrement à des moments différents. Vous pouvez automatiser un processus de purge pour supprimer les anciens journaux, mais vous constaterez peut-être qu’il est plus coûteux configurer un tel processus qu’il est de conserver les journaux.

La dépense supplémentaire de journalisation est triviale par rapport à la quantité de résolution des problèmes de temps et argent que vous pouvez enregistrer en présentant toutes les informations que vous devez déjà disponible lorsqu’une erreur survient. Puis lorsque quelqu'un vous indique qu’elles avaient une erreur aléatoire plus tard environ 8:00 Hier soir, mais ils ne vous souvenez pas de l’erreur, vous pouvez facilement trouver des quel était le problème.

Pour moins de 4 $ un mois, vous pouvez conserver quant à 50 gigaoctets de journaux et l’impact sur les performances de la journalisation est trivial tant que vous conservez l’esprit--afin d’éviter les goulots d’étranglement de performances, assurez-vous que votre bibliothèque de journalisation est asynchrone.

### <a name="differentiate-logs-that-inform-from-logs-that-require-action"></a>Différencier les fichiers journaux qui informent à partir des journaux qui nécessitent une action

Les journaux sont destinés à INFORM (je veux que vous sachiez quelque chose) ou ACT (vous allez devoir faire quelque chose). Veillez à écrire uniquement les journaux de ACT pour les problèmes nécessitant réellement une personne ou un processus automatisé pour prendre des mesures. Trop de journaux ACT créera le bruit, nécessitant trop fastidieux de passer en revue un son à identifier les problèmes authentiques. Et si vos journaux ACT déclenche automatiquement des actions telles que l’envoi de courrier électronique au personnel de support, évitez permettant à des milliers de telles actions déclenchées par un seul problème.

Dans le suivi .NET System.Diagnostics, journaux peuvent être affectés au niveau erreur, avertissement, informations et débogage/Verbose. Vous pouvez différencier ACT à partir des journaux INFORM par réservation le niveau d’erreur pour les journaux de la loi et à l’aide des niveaux inférieurs pour les journaux INFORM.

![Niveaux de journalisation](monitoring-and-telemetry/_static/image20.png)

### <a name="configure-logging-levels-at-run-time"></a>Configurer les niveaux de journalisation en cours d’exécution

S’il est préférable d’avoir une session toujours en production, une autre méthode conseillée consiste à implémenter une infrastructure de journalisation qui vous permet d’ajuster au moment de l’exécution, le niveau de détail que vous êtes connecté, sans redéployer ou le redémarrage de votre application. Par exemple, lorsque vous utilisez la fonctionnalité de traçage dans `System.Diagnostics` vous pouvez créer erreur, avertissement, information, et journaux de débogage/Verbose. Nous vous recommandons de vous connecter toujours erreur, avertissement, consigne des informations en production, et que vous souhaitez être en mesure d’ajouter dynamiquement de journalisation de débogage/Verbose pour la résolution de cas par cas.

Les applications Web dans Azure App Service ont une prise en charge intégrée pour l’écriture `System.Diagnostics` les journaux pour le système de fichiers, le stockage de Table ou le stockage d’objets Blob. Vous pouvez sélectionner différents niveaux de journalisation pour chaque destination de stockage, et vous pouvez modifier le niveau de journalisation à la volée sans avoir à redémarrer votre application. La prise en charge du stockage Blob facilite l’exécution [HDInsight](https://docs.microsoft.com/azure/hdinsight/) travaux d’analyse sur les journaux des applications, car HDInsight sait comment travailler directement avec le stockage d’objets Blob.

### <a name="log-exceptions"></a>Journal des Exceptions

Ne placez pas simplement *exception. ToString()* dans votre code de journalisation. Qui laisse les informations contextuelles. Dans le cas d’erreurs SQL, il omet le numéro d’erreur SQL. Pour toutes les exceptions, inclure des informations de contexte, l’exception elle-même et les exceptions internes pour vous assurer que vous fournissez tous les éléments qui seront nécessaires pour la résolution des problèmes. Par exemple, les informations de contexte peuvent inclure le nom du serveur, un identificateur de transaction et un nom d’utilisateur (mais pas le mot de passe ou les clés secrètes !).

Si vous comptez sur chaque développeur de l’attitude avec l’exception de journalisation, certains d'entre eux ne. Pour veiller à ce qu’il obtient le droit de façon à chaque fois, générez directement dans votre interface de l’enregistreur d’événements de gestion des exceptions : passer l’objet d’exception à la classe de journalisation et les journaux correctement des données de l’exception dans la classe de journalisation.

### <a name="log-calls-to-services"></a>Appels aux services

Nous vous recommandons vivement d’écrire un journal chaque fois que votre application appelle un service, s’il s’agit d’une base de données ou une API REST ou un service externe. Inclure dans vos journaux non seulement une indication de réussite ou échec, mais la durée de chaque demande. Dans l’environnement du cloud, vous verrez souvent des problèmes liés à ralentissements plutôt qu’à des pannes complètes. Quelque chose qui prend généralement 10 millisecondes peut subitement commencez par effectuer une seconde. Lorsque quelqu'un vous indique que votre application est lente, vous souhaitez être en mesure de rechercher sur New Relic ou n’importe quel service de télémétrie, vous avez et validez leur expérience et que vous souhaitez être en mesure de rechercher sont des journaux à approfondir les détails de la raison pour laquelle il est lent.

### <a name="use-an-ilogger-interface"></a>Utiliser une interface ILogger

Ce que nous recommandons cette opération lorsque vous créez une application de production est à créer un simple *ILogger* d’interface et de respecter certaines méthodes qu’elle contient. Cela facilite la modifier ultérieurement l’implémentation de journalisation et évite de devoir parcourir tout votre code pour le faire. Nous aurions pu utiliser le `System.Diagnostics.Trace` classe tout au long de l’application corriger, mais au lieu de cela nous allons l’utiliser en arrière-plan dans une classe de journalisation qui implémente *ILogger*, et nous *ILogger* des appels de méthode dans l’ensemble de l’application.

De cette façon, si vous voulez enrichir la journalisation, vous pouvez remplacer [ `System.Diagnostics.Trace` ](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio#apptracelogs) avec le mécanisme de journalisation souhaité. Par exemple, que votre application se développe vous pouvez décider que vous souhaitez utiliser un package plus complète de la journalisation comme [NLog](http://nlog-project.org/) ou [bloc journalisation de l’Application d’Enterprise Library](https://msdn.microsoft.com/en-us/library/dn440731(v=pandp.60).aspx). ([Log4Net](http://logging.apache.org/log4net/) est une autre infrastructure de journalisation courantes, mais il n’asynchrone de journalisation.)

Une des raisons possibles à l’aide d’une infrastructure telle que NLog sont de faciliter la division de journalisation de sortie dans les magasins de données de volume élevé et de grande valeur distincte. Qui vous permet de stocker efficacement d’importants volumes de données INFORM que vous n’avez pas besoin de l’exécuter rapidement des requêtes, tout en conservant un accès rapide aux données de ACT.

### <a name="semantic-logging"></a>Journalisation de sémantique

Pour une méthode relativement nouvelle effectuer l’enregistrement qui peut générer des informations de diagnostic plus utiles, consultez [Enterprise Library sémantique journalisation Application bloc (plaque)](http://convective.wordpress.com/2013/08/12/semantic-logging-application-block-slab/). PANNEAU utilise [Event Tracing for Windows](https://msdn.microsoft.com/en-us/library/windows/desktop/bb968803.aspx) (ETW) et [EventSource](https://msdn.microsoft.com/en-us/library/system.diagnostics.tracing.eventsource.aspx) prend en charge dans .NET 4.5 pour vous permettre de créer des journaux plus structurés et utilisable dans une requête. Vous définissez une méthode différente pour chaque type d’événement que vous vous connectez, ce qui vous permet de personnaliser les informations que vous écrivez. Par exemple, pour enregistrer une erreur de base de données SQL que vous pouvez appeler un `LogSQLDatabaseError` (méthode). Pour ce type d’exception, vous connaissez qu'une information clée étant le numéro d’erreur, vous pouvez inclure un paramètre de numéro d’erreur dans la signature de méthode et enregistrer le numéro d’erreur comme un champ distinct dans l’enregistrement de journal que vous écrivez. Étant donné que le nombre est dans un champ séparé vous pouvez plus facilement et de manière fiable obtenir des rapports basés sur les numéros d’erreur SQL que vous pourriez le faire si vous ont été simplement concaténez le numéro d’erreur dans une chaîne de message.

## <a name="logging-in-the-fix-it-app"></a>Enregistrer le correctif application

### <a name="the-ilogger-interface"></a>L’interface ILogger

Voici le *ILogger* interface dans l’application corriger.

[!code-csharp[Main](monitoring-and-telemetry/samples/sample1.cs)]

Ces méthodes permettent d’écrire des journaux quatre niveaux pris en charge par *System.Diagnostics*. Les méthodes TraceApi sont pour la journalisation des appels de service externe avec des informations sur la latence. Vous pouvez également ajouter un ensemble de méthodes pour le niveau de débogage/Verbose.

### <a name="the-logger-implementation-of-the-ilogger-interface"></a>L’implémentation de l’enregistreur d’événements de l’interface ILogger

L’implémentation de l’interface est très simple. Il fondamentalement appelle simplement dans la norme *System.Diagnostics* méthodes. L’extrait de code suivant montre les trois méthodes d’informations et un pour chacun des autres.

[!code-csharp[Main](monitoring-and-telemetry/samples/sample2.cs)]

### <a name="calling-the-ilogger-methods"></a>Les méthodes de ILogger

Chaque fois que le code dans l’application corriger intercepte une exception, il appelle une *ILogger* méthode pour enregistrer les détails de l’exception. Et chaque fois qu’il effectue un appel à la base de données, service Blob ou une API REST, il démarre un chronomètre avant l’appel, arrête le chronomètre lorsque le service retourne et enregistre le temps écoulé, ainsi que des informations sur la réussite ou l’échec.

Notez que le message du journal inclut le nom de classe et le nom de la méthode. Il est conseillé de s’assurer que les messages du journal identifient quelle partie du code d’application auteur.

[!code-csharp[Main](monitoring-and-telemetry/samples/sample3.cs?highlight=6,14,20-21,25)]

Maintenant pour chaque fois que l’application Fix It a effectué un appel à la base de données SQL, vous pouvez voir l’appel, la méthode qui l’a appelée, et exactement le temps que nécessaire.

![Requête de base de données SQL dans les journaux](monitoring-and-telemetry/_static/image21.png)

![](monitoring-and-telemetry/_static/image22.png)

Si vous allez parcourir les journaux, vous pouvez voir que la durée des appels de base de données est variable. Que les informations peut s’avérer utiles : étant donné que l’application enregistre tout cela, vous pouvez analyser les tendances historiques dans le fonctionnement du service de base de données au fil du temps. Par exemple, un service peut être rapidement la plupart du temps, mais risque d’échouer les demandes réponses peut être ralentie ou à certains moments de la journée.

Vous pouvez effectuer la même chose pour le service Blob – chaque fois que l’application télécharge un nouveau fichier, il existe un journal, et vous pouvez voir exactement la durée pour charger chaque fichier.

![Journal de téléchargement d’objet BLOB](monitoring-and-telemetry/_static/image23.png)

Il s’agit de quelques lignes de code à écrire chaque fois que vous appelez un service, et désormais chaque fois qu’une personne indique a rencontré un problème, vous savez exactement ce que le problème a été, si elle a une erreur, ou même s’il s’exécutait simplement trop lentement. Vous pouvez identifier la source du problème sans avoir à distance sur un serveur ou activez la journalisation après l’erreur se produit et espérer que créer de nouveau.

## <a name="dependency-injection-in-the-fix-it-app"></a>Injection de dépendances dans le correctif il application

Vous pouvez vous demander comment le constructeur de référentiel dans l’exemple ci-dessus Obtient l’enregistreur d’événements, l’implémentation d’interface :

[!code-csharp[Main](monitoring-and-telemetry/samples/sample4.cs?highlight=6)]

Permet d’associer l’interface à l’implémentation de l’application utilise [injection de dépendance](http://en.wikipedia.org/wiki/Dependency_injection)(DI) avec [AutoFac](http://autofac.org/). DI permet d’utiliser un objet basé sur une interface à différents endroits de votre code et ne devez spécifier dans un seul emplacement, l’implémentation utilisée lorsque l’interface est instancié. Cela rend plus facile de modifier l’implémentation : par exemple, vous souhaiterez peut-être remplacer l’enregistreur d’événements System.Diagnostics avec un enregistreur d’événements NLog. Ou, pour les tests automatisés, vous souhaiterez remplacer par une version fictive de l’enregistreur d’événements.

L’application Fix It utilise DI dans tous les référentiels et tous les contrôleurs. Les constructeurs des classes de contrôleur d’obtenir un *ITaskRepository* la même manière que le référentiel Obtient une interface de journalisation de l’interface :

[!code-csharp[Main](monitoring-and-telemetry/samples/sample5.cs?highlight=5)]

L’application utilise la bibliothèque AutoFac DI pour fournir automatiquement *TaskRepository* et *journal* instances pour ces constructeurs.

[!code-csharp[Main](monitoring-and-telemetry/samples/sample6.cs?highlight=8,10)]

Ce code signalant que n’importe où un constructeur doit un *ILogger* l’interface, passez une instance de la *enregistreur d’événements* (classe), et chaque fois qu’il a besoin d’un *IFixItTaskRepository*l’interface, passez une instance de la *FixItTaskRepository* classe.

[AutoFac](http://autofac.org/) est un des plusieurs infrastructures d’injection de dépendance que vous pouvez utiliser. Un autre populaire est [Unity](https://blogs.msdn.com/b/agile/archive/2013/08/20/new-guide-dependency-injection-with-unity.aspx), qui est recommandé et pris en charge par Microsoft Patterns and Practices.

## <a name="built-in-logging-support-in-azure"></a>Prise en charge de journalisation intégrés dans Azure

Azure prend en charge les types suivants de [journalisation pour les applications Web dans Azure App Service](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio):

- Traçage System.Diagnostics (vous pouvez activer et désactiver et définir des niveaux à la volée sans redémarrage du site).
- Événements Windows.
- Journaux IIS (HTTP/FREB).

Azure prend en charge les types suivants de [journalisation dans les Services de cloud computing](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics):

- Traçage System.Diagnostics.
- Compteurs de performances.
- Événements Windows.
- Journaux IIS (HTTP/FREB).
- Analyse du répertoire personnalisé.

L’application Fix It utilise le traçage System.Diagnostics. Il vous souhaitez pour activer la journalisation dans une application web de System.Diagnostics est de retourner un commutateur dans le portail ou appeler l’API REST. Dans le portail, cliquez sur le **Configuration** onglet pour votre site et faites défiler pour voir les **Application Diagnostics** section. Vous pouvez activer ou désactiver la journalisation et sélectionnez le niveau de journalisation souhaité. Vous pouvez avoir à Azure d’écrire les journaux dans le système de fichiers ou un compte de stockage.

![Diagnostic des applications et les diagnostics de site dans l’onglet Configurer](monitoring-and-telemetry/_static/image24.png)

Une fois que vous activez la journalisation dans Azure, vous pouvez voir les journaux dans la fenêtre Sortie de Visual Studio comme elles sont créées.

![Menu de journaux de diffusion en continu](http://wacomdpsstorage.blob.core.windows.net/articlesmedia/content-ppe.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/20140115062810/tws-viewlogsmenu.png)

![Menu de journaux de diffusion en continu](http://wacomdpsstorage.blob.core.windows.net/articlesmedia/content-ppe.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/20140115062810/tws-nologsyet.png)

Vous pouvez également avoir des journaux écrits pour votre compte de stockage et la vue avec un outil qui peut accéder au service de la Table de stockage Azure, tel que **l’Explorateur de serveurs** dans Visual Studio ou [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Journaux dans l’Explorateur de serveurs](http://wacomdpsstorage.blob.core.windows.net/articlesmedia/content-ppe.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/20140115062810/tws-storagelogs.png)

## <a name="summary"></a>Résumé

Il est très simple à implémenter un système de télémétrie d’out of box, instrumenter journalisation dans votre propre code et configurer la journalisation dans Azure. Et si vous rencontrez des problèmes de production, la combinaison d’un système de télémétrie et les journaux personnalisés vous aideront à résoudre rapidement les problèmes avant qu’ils deviennent des problèmes majeurs pour vos clients.

Dans le [chapitre suivant](transient-fault-handling.md) nous allons examiner comment gérer les erreurs temporaires afin qu’ils ne deviennent des problèmes de production que vous devez examiner.

## <a name="resources"></a>Ressources

Pour plus d'informations, voir les ressources ci-dessous.

Documentation principalement sur les données de télémétrie :

- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Consultez l’Instrumentation et la télémétrie des conseils, des conseils de Service de contrôle, analyse de point de terminaison de contrôle d’intégrité et une Reconfiguration du Runtime modèle.
- [Minime pincement dans le Cloud : l’activation des performances New Relic analyse sur des sites Web Azure](http://www.hanselman.com/blog/PennyPinchingInTheCloudEnablingNewRelicPerformanceMonitoringOnWindowsAzureWebsites.aspx).
- [Meilleures pratiques pour la création de Services à grande échelle sur les Services Cloud Azure](https://msdn.microsoft.com/en-us/library/windowsazure/jj717232.aspx). Livre blanc par Mark Simms et Michael Thomassy. Consultez la section télémesure et Diagnostics.
- [Développement de nouvelle génération avec Application Insights](https://msdn.microsoft.com/en-us/magazine/dn683794.aspx). Article du MSDN Magazine.

Documentation principalement sur la journalisation :

- [Bloc d’Application de journalisation sémantique (plaque)](http://convective.wordpress.com/2013/08/12/semantic-logging-application-block-slab/). Neil Mackenzie présente le cas pour la journalisation sémantique avec panneau.
- [Créer des journaux structurées et explicites avec un enregistrement sémantique](https://channel9.msdn.com/Events/Build/2013/3-336). (Vidéo) Julian Dominguez présente le cas pour la journalisation sémantique avec panneau.
- [EF6 La journalisation SQL – partie 1 : journalisation Simple](http://blog.oneunicorn.com/2013/05/08/ef6-sql-logging-part-1-simple-logging/). Arthur Vickers montre comment enregistrer les requêtes exécutées par Entity Framework dans EF 6.
- [Résilience des connexions et l’Interception de commande avec Entity Framework dans une Application ASP.NET MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md). Quatrième dans une série de didacticiels neuf parties, montre comment utiliser la fonctionnalité de l’interception de commande de EF 6 pour enregistrer les commandes SQL envoyées à la base de données par Entity Framework.
- [Améliorer la journalisation à l’aide des attributs d’informations appelant 5.0 c#](http://www.dotnetcurry.com/showarticle.aspx?ID=972). Comment se facilement le nom de la méthode d’appel sans elle coder en dur dans des littéraux ou à l’aide de la réflexion pour obtenir de manuellement.

Documentation principalement sur la résolution des problèmes :

- [Résolution des problèmes Azure &amp; débogage blog](https://blogs.msdn.com/b/kwill/).
- [AzureTools : l’utilitaire de Diagnostic utilisé par l’équipe de Support du développeur Azure](https://blogs.msdn.com/b/kwill/archive/2013/08/26/azuretools-the-diagnostic-utility-used-by-the-windows-azure-developer-support-team.aspx?Redirected=true). Présente et fournit un lien de téléchargement pour un outil qui peut être utilisé sur une machine virtuelle Azure pour télécharger et exécuter un large éventail d’outils de diagnostic et d’analyse. Utile lorsque vous avez besoin diagnostiquer un problème sur un ordinateur virtuel particulier.
- [Résoudre les problèmes d’une application web dans Azure App Service à l’aide de Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio). Un didacticiel de mise en route avec le traçage System.Diagnostics et le débogage distant.

Vidéos :

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série en neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente les principaux concepts et les principes d’architecture de manière très accessible et intéressante, avec récits tirés de l’expérience de Microsoft Customer Advisory Team (CAT) avec des clients réels. Épisodes 4 et 9 sont sur la surveillance et télémétrie. Épisode 9 inclut une vue d’ensemble de l’analyse des services MetricsHub, AppDynamics, New Relic et PagerDuty.
- [Construction Big : Leçons reçues des clients Azure - partie II](https://channel9.msdn.com/Events/Build/2012/3-030). Mark Simms parle de la conception en cas d’échec et l’instrumentation de tous les éléments. Similaire à la série de prévention de défaillance mais le passage en plus de détails sur les procédures.

Exemple de code :

- [Cloud Service Fundamentals dans Azure](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Exemple d’application créé par l’équipe de consultants clients Microsoft Azure. Illustre la télémétrie et les méthodes de journalisation, comme expliqué dans les articles suivants. L’exemple implémente la journalisation de l’application à l’aide de [NLog](http://nlog-project.org/). Pour plus d’informations connexes, consultez la [série de quatre articles de wiki TechNet sur les données de télémétrie et la journalisation](https://social.technet.microsoft.com/wiki/contents/articles/17987.cloud-service-fundamentals.aspx#Telemetry_coming_soon).

>[!div class="step-by-step"]
[Précédent](design-to-survive-failures.md)
[Suivant](transient-fault-handling.md)
