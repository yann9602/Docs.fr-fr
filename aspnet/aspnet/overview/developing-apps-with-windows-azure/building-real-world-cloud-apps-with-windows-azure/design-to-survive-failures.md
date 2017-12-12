---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/design-to-survive-failures
title: "Conception pour faire face aux défaillances (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: 364ce84e-5af8-4e08-afc9-75a512b01f84
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/design-to-survive-failures
msc.type: authoredcontent
ms.openlocfilehash: a0ee790da07c99cdb1279a6bca637a4ce8076e84
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="design-to-survive-failures-building-real-world-cloud-apps-with-azure"></a>Concevoir pour faire face aux défaillances (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Une des choses que vous devez tenir compte lorsque vous générez n’importe quel type d’application, mais en particulier si celle-ci s’exécute dans le cloud, où un grand nombre de personnes utiliseront, est la conception de l’application afin qu’il peut normalement gérer les erreurs et continuer à fournir la valeur de mesure possible. Avec le temps, choses vont se produire dans n’importe quel environnement ou n’importe quel système logiciel. Comment votre application gère les situations détermine comment énervé vos clients obtiendront et combien de temps passé à analyser et résoudre les problèmes.

## <a name="types-of-failures"></a>Types d’échecs

Il existe deux catégories principales d’échecs que vous souhaitez gérer différemment :

- Temporaire, la réparation automatique des erreurs telles que des problèmes de connectivité réseau intermittents.
- Échecs durable qui nécessitent une intervention.

Pour les erreurs temporaires, vous pouvez implémenter une stratégie de nouvelle tentative pour vous assurer que la plupart du temps, l’application récupère rapidement et automatiquement. Vos clients peuvent remarquer des temps de réponse légèrement plus long, mais dans le cas contraire ils ne seront pas affectées. Nous allons montrer quelques méthodes permettant de gérer ces erreurs dans le [chapitre de gestion temporaire des erreurs de](transient-fault-handling.md).

Pour vivants échecs, vous pouvez implémenter la surveillance et la journalisation des fonctionnalités qui vous avertit rapidement lorsque des problèmes surviennent et qui facilite l’analyse des causes. Nous allons montrer quelques méthodes pour vous aider à tenir au courant de ces types d’erreurs dans le [chapitre surveillance et télémétrie](monitoring-and-telemetry.md).

## <a name="failure-scope"></a>Étendue de l’échec

Vous devez également réfléchir à portée de la défaillance : si un seul ordinateur est affecté, un service entier, telles que la base de données SQL ou de stockage ou d’une région entière.

![Étendue de l’échec](design-to-survive-failures/_static/image1.png)

### <a name="machine-failures"></a>Échecs d’ordinateur

Dans Azure, un serveur défaillant est automatiquement remplacé par un autre, et une application bien conçue cloud récupère à partir de ce type d’échec rapidement et automatiquement. Précédemment, nous souligner les avantages de l’évolutivité d’un niveau web sans état et la facilité de récupération à partir d’un serveur défaillant est un autre avantage de l’abandon de l’état. Facilité de récupération est également un des avantages des fonctionnalités de (PaaS) platform-as-a-service telles que la base de données SQL et Azure App Service Web Apps. Les pannes de matériel sont rares, mais quand elles se produisent que ces services de les gérer automatiquement ; Vous ne devez même écrire du code pour gérer les échecs de l’ordinateur lorsque vous utilisez l’un de ces services.

### <a name="service-failures"></a>Échecs du service

Applications cloud utilisent généralement plusieurs services. Par exemple, l’application Fix It utilise le service de base de données SQL, le service de stockage, et l’application web est déployée vers Azure App Service. Que votre application faire si un des services que vous dépendez échoue ? Pour certains service échecs un nom convivial de la « Désolé, réessayez plus tard » message risque d’être la meilleure solution pour vous. Toutefois, dans de nombreux scénarios vous pouvez mieux. Par exemple, lorsque votre banque de données du serveur principal est arrêté, vous pouvez accepter l’entrée d’utilisateur, afficher « votre demande a été reçue » et stocker l’entrée quelque part autre temporairement ; Ensuite, lorsque le service que vous avez besoin est opérationnel, vous pouvez récupérer l’entrée et le traiter.

Le [modèle centré sur la file d’attente de travail](queue-centric-work-pattern.md) chapitre montre une façon de gérer ce scénario. L’application corriger stocke les tâches dans la base de données SQL, mais il ne doit pas nécessairement de fermeture lors de la base de données SQL est arrêté. Dans ce chapitre, nous verrons comment stocker des entrées d’utilisateur pour une tâche dans une file d’attente et un processus de travail permet de lire la file d’attente et mettre à jour de la tâche. Si SQL est arrêté, la possibilité de créer des tâches de réparation est pas affectée ; le processus de travail peut attendre et traiter les nouvelles tâches lors de la base de données SQL est disponible.

### <a name="region-failures"></a>Échecs de la région

Régions entières peuvent échouer. Une catastrophe naturelle peut détruire un centre de données, il peut obtenir aplatie en un meteor, la ligne de jonction dans le centre de données pourrait être tronquée par un exploitant par une vache avec rétro, etc. Que faire si votre application est hébergée dans le centre de données appliquer ? Il est possible de configurer votre application dans Azure pour s’exécuter simultanément dans plusieurs régions afin que s’il existe un reprise après sinistre dans un, vous continuez en cours d’exécution dans une autre région. Ces échecs sont extrêmement rares occurrences et ne quittez pas la plupart des applications via radiaux nécessaire pour éviter toute interruption du service via les échecs de ce type. Consultez la section de ressources à la fin de ce chapitre pour plus d’informations sur la façon de conserver votre application disponible même par le biais d’un échec de la région.

Un Azure vise à faciliter le traitement des tous ces types d’échecs beaucoup plus faciles, et vous verrez quelques exemples de la façon dont nous faisons que dans les chapitres suivants.

## <a name="slas"></a>Contrats SLA

Personnes Découvrez souvent des contrats de niveau de service (SLA) dans l’environnement du cloud. En fait, il s’agit promesses qui rendent des entreprises sur la fiabilité de leur service. Un 99,9 % SLA signifie que vous devez attendre que le service fonctionne correctement 99,9 % du temps. Une valeur typique pour un contrat SLA et il ressemble à un nombre très élevé, mais vous ne réalisez pas combien de temps bas. 1 % correspond réellement. Voici un tableau qui indique le temps d’inactivité différents pourcentages de contrat SLA montant sur une année, un mois et une semaine.

![Table de SLA](design-to-survive-failures/_static/image2.png)

Par conséquent, un contrat SLA signifie que votre service de 99,9 % est en panne 8,76 heures par an ou 43,2 minutes par mois. Qui est plus temps d’arrêt que la plupart des gens réaliser. En tant que développeur vous souhaitez donc Sachez qu’une certaine quantité de temps d’arrêt est possible et de gérer de manière appropriée. À un moment donné une personne va être à l’aide de votre application et un service va être arrêté, et vous souhaitez réduire l’impact négatif que sur le client.

Vous devez savoir sur un contrat SLA est quel laps de temps qu’il fait référence à : l’horloge réinitialiser toutes les semaines, tous les mois ou chaque année ? Dans Azure nous réinitialiser l’horloge chaque mois, ce qui vous convient le mieux à un contrat SLA annuel, dans la mesure où un contrat SLA annuel peut masquer mois incorrects en les décalant avec une série de mois bon.

Bien entendu nous toujours aspirer à faire de meilleures performances que le contrat SLA ; vous serez généralement moins importante que celle vers le bas. La promesse est que si nous sommes jamais plus longue que le maximum de temps d’arrêt, vous pouvez demander le remboursement de. La somme d’argent que vous récupérez probablement ne compense entièrement l’impact de l’excès de temps d’arrêt, mais cet aspect du contrat SLA se comporte comme une stratégie de mise en œuvre et vous permet de savoir que nous le prennent très au sérieux.

### <a name="composite-slas"></a>SLA composite

Important de tenir compte lorsque vous examinez SLA est l’impact de l’utilisation de plusieurs services dans une application, avec chaque service ayant un contrat SLA distinct. Par exemple, l’application Fix It utilise Azure App Service Web Apps, le stockage Azure et base de données SQL. Voici leurs numéros de contrat SLA à compter de la date de que ce livre électronique est en cours d’écriture dans décembre 2013 :

![Base de données SQL de Site Web, le stockage, contrat SLA](design-to-survive-failures/_static/image3.png)

Quelle est la valeur maximale indisponibilité attendue de l’application en fonction de ces contrats de service ? Vous pouvez penser que votre temps d’arrêt serait égal à la pire pourcentage SLA ou 99,9 % dans ce cas. Qui est true si les trois services Échec toujours en même temps, mais qui n’est pas nécessairement que se passe-t-il réellement. Chaque service peut échouer indépendamment à différents moments, donc vous devez calculer le contrat SLA composite en multipliant les numéros de contrat SLA individuels.

![Contrat SLA composite](design-to-survive-failures/_static/image4.png)

Pour votre application peut être arrêtée 43,2 minutes pas seulement un mois, 3 fois cette quantité, 108 minutes par mois – et continuer à dans les limites de contrat SLA Azure.

Ce problème n’est pas unique dans Azure. Nous fournissons réellement le cloud meilleur contrats SLA de n’importe quel service de cloud disponible, et vous avez des problèmes similaires à gérer si vous utilisez les services de cloud computing de n’importe quel fournisseur. Ce que cela met en surbrillance est l’importance de réfléchir à la conception de votre application de gérer les échecs de service inévitable normalement, car ils peuvent se produire assez souvent un impact sur vos clients ou utilisateurs.

### <a name="cloud-slas-compared-to-enterprise-down-time-experience"></a>SLA cloud par rapport à l’expérience de temps d’arrêt d’entreprise

Personnes parfois, par exemple, « Dans mon application d’entreprise je n’ont jamais ces problèmes. » Si vous demandez combien de temps bas mois qu’ils ont en fait, ils affirment généralement, «, il se produit occasionnellement. » Et si vous vous demandez la fréquence à laquelle ils admettent que « Parfois nous n’avez pas besoin de sauvegarder ou installer un nouveau serveur ou mise à jour. » Bien entendu, qui compte comme temps d’arrêt. La plupart des applications d’entreprise, sauf si elles sont particulièrement critique sont réellement vers le bas pour plus d’informations à la quantité de temps autorisée par nos contrats de service. Mais quand il est votre serveur et votre infrastructure et vous êtes responsable pour lui et de contrôle de celui-ci, que vous allez se sentir moins secret sur les temps d’inactivité. Dans un environnement de cloud vous dépend d’une autre personne et si vous ne connaissez pas ce qui se passe, afin que vous allez peut-être obtenir plus inquiéter.

Lorsqu’une entreprise réalise un pourcentage de temps d’exécution supérieur que vous obtenez à partir d’un contrat SLA cloud, ils le faire en money beaucoup plus de dépense sur du matériel. Un service cloud peut faire mais aurait à facturer bien plus encore pour ses services. Au lieu de cela, vous tirer parti d’un service économique et concevez votre logiciel afin que les échecs inévitables provoquent une interruption minimale de vos clients. Votre travail en tant qu’un concepteur d’application cloud n’est pas tellement afin d’éviter l’échec afin d’éviter une catastrophe, et pour ce faire, en mettant l’accent sur le logiciel, pas sur le matériel. Alors que les applications d’entreprise s’efforcent afin d’optimiser le temps moyen entre défaillances, les applications cloud s’efforcent afin de réduire le temps moyen de récupérer.

### <a name="not-all-cloud-services-have-slas"></a>Tous les services cloud ont des contrats SLA

Sachez également pas chaque service cloud même qu’un contrat SLA. Si votre application dépend d’un service sans garantie d’un temps d’exécution, vous êtes peut-être inaccessible beaucoup plus de temps que vous pouvez l’imaginer. Par exemple, si vous activez l’ouverture de session à votre site à l’aide d’un fournisseur de réseaux sociaux tels que Facebook ou Twitter, vérifiez avec le fournisseur de services pour savoir si est un contrat SLA, et il peut y n’est pas un. Mais si le service d’authentification tombe en panne ou ne peut pas prendre en charge le volume de requêtes que vous lui envoyez, vos clients sont verrouillés hors de votre application. Vous pouvez être vers le bas pour les jours ou plus. Les créateurs d’une nouvelle application attendu des centaines de millions de téléchargements et a pris une dépendance sur l’authentification Facebook – mais n’avez pas communiquer avec Facebook avant de passer de live et découvertes trop tard qui s’est pas de contrat SLA pour ce service.

### <a name="not-all-downtime-counts-toward-slas"></a>Pas tous les nombres de temps d’arrêt vers SLA

Certains services de cloud computing peuvent délibérément refus de service si votre application les utilise trop. Il s’agit *limitation*. Si un service dispose d’un contrat SLA, il doit indiquer les conditions dans lesquelles vous pourrez être limitée, et la conception de votre application doit éviter ces conditions et réagir de façon appropriée pour la limitation s’il se produit. Par exemple, si des requêtes à un service commencent à échouer lorsque vous dépassez un certain nombre par seconde, vous souhaitez vous assurer de nouvelles tentatives automatiques ne se produisent pas rapidement qu’ils provoquent la limitation continuer. Nous aurons plus d’informations à propos de la limitation dans les [chapitre de gestion temporaire des erreurs de](transient-fault-handling.md).

## <a name="summary"></a>Résumé

Ce chapitre a essayé de vous permettre de réaliser la raison pour laquelle une application de cloud du monde réel doit être conçu survivre à des échecs en douceur. En commençant par le [chapitre suivant](monitoring-and-telemetry.md), les modèles restants dans cette série aller plus en détail certaines stratégies que vous pouvez utiliser pour ce faire :

- Ont de bonnes [surveillance et télémétrie](monitoring-and-telemetry.md), de sorte que vous découvrez rapidement les défaillances qui nécessitent une intervention, et vous disposez de suffisamment d’informations pour les résoudre.
- [Gérer les erreurs temporaires](transient-fault-handling.md) en implémentant la logique de nouvelle tentative intelligent, afin que votre application récupère automatiquement lorsqu’il peut et revient à [disjoncteur](transient-fault-handling.md#circuitbreakers) logique lorsqu’il ne peut pas.
- Utilisez [mise en cache distribuée](distributed-caching.md) afin de réduire les problèmes de connexion, la latence et débit avec un accès de base de données.
- Implémentez faible couplage la [centré sur la file d’attente de travail modèle](queue-centric-work-pattern.md), de sorte que votre frontale de l’application peut continuer à fonctionner lorsque le serveur principal est arrêté.

## <a name="resources"></a>Ressources

Pour plus d’informations, consultez les chapitres suivants de ce livre électronique et les ressources suivantes.

Documentation :

- [Prévention de défaillance : Aide sur les Architectures de Cloud résilientes](https://msdn.microsoft.com/en-us/library/windowsazure/jj853352.aspx). Livre blanc par Marc Mercuri, Ulrich Homann et Andrew Townhill. Version de la page Web de la série de vidéos de prévention de défaillance.
- [Meilleures pratiques pour la création de Services à grande échelle sur les Services Cloud Azure](https://msdn.microsoft.com/en-us/library/windowsazure/jj717232.aspx). Livre blanc par Mark Simms et Michael Thomassy.
- [Aide technique de continuité des activités Azure](https://msdn.microsoft.com/en-us/library/windowsazure/hh873027.aspx). Livre blanc en Patrick Wickline, Jason Roth.
- [Récupération d’urgence et haute disponibilité pour les Applications Azure](https://msdn.microsoft.com/en-us/library/windowsazure/dn251004.aspx). Livre blanc par Michael McKeown, Hanu Kommalapati et Jason Roth.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Consultez le Guide de déploiement de centre de données multiples, modèle de disjoncteur.
- [Prise en charge de Azure - les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).
- [Continuité des activités dans la base de données SQL Azure](https://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx). Documentation sur la base de données SQL haute disponibilité et reprise après sinistre fonctionnalités de récupération.
- [Haute disponibilité et récupération d’urgence pour SQL Server dans des Machines virtuelles](https://msdn.microsoft.com/en-us/library/windowsazure/jj870962.aspx).

Vidéos :

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série en neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente les principaux concepts et les principes d’architecture de manière très accessible et intéressante, avec récits tirés de l’expérience de Microsoft Customer Advisory Team (CAT) avec des clients réels. Épisodes 1 et 8 aborde en détail les raisons de conception d’applications de cloud pour faire face aux défaillances. Voir aussi la discussion de suivi de la limitation dans l’épisode 2 en commençant à la discussion de disjoncteurs dans épisode 3 en commençant à 40:55 49:57 et la discussion des points d’échec et les modes d’échec du démarrage de l’épisode 2 à 56:05.
- [Construction Big : Leçons reçues des clients Azure - partie II](https://channel9.msdn.com/Events/Build/2012/3-030). Mark Simms parle de la conception en cas d’échec et l’instrumentation de tous les éléments. Similaire à la série de prévention de défaillance mais le passage en plus de détails sur les procédures.

>[!div class="step-by-step"]
[Précédent](unstructured-blob-storage.md)
[Suivant](monitoring-and-telemetry.md)
