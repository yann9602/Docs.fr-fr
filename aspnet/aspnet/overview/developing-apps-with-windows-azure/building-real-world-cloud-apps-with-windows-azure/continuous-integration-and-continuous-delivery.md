---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
title: "Intégration continue et livraison continue (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: eaece9f5-f80c-428b-b771-5db66d275b7d
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
msc.type: authoredcontent
ms.openlocfilehash: 0af5f7e841bb43fa41fa0daa4ad8d59ee0596404
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="continuous-integration-and-continuous-delivery-building-real-world-cloud-apps-with-azure"></a>Intégration continue et livraison continue (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Les deux premières recommandé que les modèles de processus de développement ont été [automatiser tout](automate-everything.md) et [contrôle de code Source](source-control.md), et le modèle de processus tiers de les combine. Intégration continue (CI) signifie que chaque fois qu’un développeur archive du code dans le référentiel source, une build est déclenchée automatiquement. La livraison continue (CD) prend une étape supplémentaire : après une build et des tests unitaires automatisés sont réussies, vous déployez automatiquement l’application dans un environnement où vous pouvez effectuer un test plus approfondie.

Le cloud vous permet de réduire le coût de maintenance d’un environnement de test, car vous ne payez que pour les ressources de l’environnement, que vous utilisez. Votre processus de CD peuvent configurer l’environnement de test lorsque vous en avez besoin, et vous pouvez tirer vers le bas de l’environnement lorsque vous avez terminé test.

## <a name="continuous-integration-and-continuous-delivery-workflow"></a>Intégration continue et livraison continue de flux de travail

En règle générale, nous vous recommandons de procéder de diffusion en continu dans le développement et l’environnement intermédiaire. La plupart des équipes, même chez Microsoft, nécessitent un processus de révision et l’approbation manuels pour le déploiement de production. Pour une production déploiement, que vous pouvez souhaiter vérifier qu’il se produit lorsque des personnes de clé de l’équipe de développement sont disponibles pour la prise en charge ou pendant les périodes de faible trafic. Mais rien ne vous empêche d’automatiser complètement vos environnements de développement et de test afin que tout un développeur doit effectuer est d’archiver une modification et un environnement est configuré pour les tests d’acceptation.

Le diagramme suivant tiré [un Microsoft Patterns and Practices livres sur la livraison continue](http://aka.ms/ReleasePipeline) illustre un flux de travail classique. Cliquez sur l’image pour l’afficher de plein écran dans son contexte d’origine.

[![Flux de travail de livraison continue](continuous-integration-and-continuous-delivery/_static/image1.png)](https://msdn.microsoft.com/en-us/library/dn449955.aspx)

## <a name="how-the-cloud-enables-cost-effective-ci-and-cd"></a>Comment le cloud permet économique de l’élément de configuration et de CD

Il est facile d’automatisation de ces processus dans Azure. Étant donné que vous exécutez tous les éléments dans le cloud, vous n’avez pas à acheter ou gérer les serveurs pour vos builds ou de vos environnements de test. Et vous n’avez pas à attendre pour être disponible pour effectuer vos tests sur un serveur. Avec chaque build que vous effectuez, vous pouvez lancer un environnement de test dans Azure à l’aide de votre script d’automatisation, tests d’acceptation d’exécution ou d’autres tests approfondies par rapport à elle et puis lorsque vous avez terminé de simplement détruire il. Et si vous exécutez uniquement ce serveur pour 2 heures, 8 heures ou un jour, la somme d’argent que vous devez payer est minime, car vous payez uniquement pour la fois un ordinateur est en cours d’exécution. Par exemple, l’environnement requis pour le correctif qu'application fondamentalement coûte environ 1 cent par heure si vous monter d’un niveau à partir du niveau gratuit. Au cours d’un mois, si vous avez uniquement exécuté l’environnement une heure à la fois, votre environnement de test est probablement prix est inférieur à un latte vous achetez au Starbucks.

## <a name="visual-studio-team-services-vsts"></a>Visual Studio Team Services (VSTS)

VSTS fournit un certain nombre de fonctionnalités pour vous aider au développement d’applications à partir de la planification de déploiement.

- Il prend en charge Git (distribuées) et contrôle de code source TFVC (centralisé).
- Il offre un service de build élastique, ce qui signifie qu’il crée des serveurs de builds lorsqu’ils sont nécessaires et sont redirigés vers le bas lorsqu’ils ont terminé de dynamiquement. Vous pouvez déclencher automatiquement une build lorsqu’un utilisateur archive des modifications de code source, et vous n’êtes pas obligé d’avoir allouer et de payer vos propres serveurs de builds qui se trouvent la plupart du temps inactifs. Le service de build est disponible tant que vous ne dépassez pas un certain nombre de builds. Si vous prévoyez d’effectuer un grand nombre de builds, vous pouvez payer un peu supplémentaire pour les serveurs de build réservé.
- Il prend en charge la diffusion en continu dans Azure.
- Il prend en charge le test de charge automatique. Le test de charge est essentiel pour une application cloud, mais est souvent négligé jusqu'à ce qu’il soit trop tard. Le test de charge simule une utilisation intensive d’une application par des milliers d’utilisateurs, ce qui vous permet de rechercher des goulots d’étranglement et améliorer le débit, avant de libérer de l’application en production.
- Il prend en charge la collaboration salle d’équipe, qui facilite la communication en temps réel et collaboration des petites équipes agiles.
- Il prend en charge la gestion de projet agile.


Pour plus d’informations sur l’intégration continue et les fonctionnalités de remise de VSTS, consultez [Visual Studio Team Services](https://www.visualstudio.com/team-services/).

Si vous envisagez d’utiliser pour la gestion de projet de clés en main, collaboration d’équipe et la solution de contrôle de code source, consultez VSTS. Le service est gratuit pour jusqu'à 5 utilisateurs, et vous pouvez souscrire à l’adresse [Visual Studio Team Services](https://www.visualstudio.com/team-services/).

## <a name="summary"></a>Résumé

Les modèles de développement trois cloud première ont été sur la façon d’implémenter un processus de développement reproductibles, fiable et prévisible avec durée de cycle basse. Dans le [chapitre suivant](web-development-best-practices.md) , nous commençons à examiner les modèles de codage et d’architecturales.

## <a name="resources"></a>Ressources

Pour plus d’informations, consultez [déployer une application web dans Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/).

Consultez également les ressources suivantes :

- [Création d’un Pipeline de mise en production avec Team Foundation Server 2012](http://aka.ms/ReleasePipeline). Livre électronique laboratoires pratiques et exemples de code par Microsoft Patterns and Practices, fournit une présentation approfondie de la livraison continue. Aborde l’utilisation de Visual Studio Lab Management et Visual Studio Release Management.
- [ALM Rangers DevOps est pour les outils et conseils](https://aka.ms/vsarsolutions/). Le groupe ALM Rangers a introduit le banc d’essai DevOps exemple le Guide de solution et des conseils pratiques en collaboration avec les modèles &amp; livre de pratiques *création d’un Pipeline de mise en production avec TFS 2012*, afin de démarrer apprendre les concepts de DevOps &amp; Release Management pour TFS 2012 et lancer les pneus. Le guide montre comment générer une seule fois et le déployer dans plusieurs environnements.
- [Test de livraison continue avec Visual Studio 2012](https://msdn.microsoft.com/en-us/library/jj159345.aspx). Livres par Microsoft Patterns and Practices, explique comment intégrer les tests automatisés avec la livraison continue.
- [WindowsAzureDeploymentTracker](https://github.com/RyanTBerry/WindowsAzureDeploymentTracker). Code source pour un outil conçu pour capturer une build TFS (basée sur une étiquette), générer, empaqueter, permet à un utilisateur dans le rôle DevOps pour configurer des aspects spécifiques de celui-ci et poussez-le dans Azure. L’outil effectue le suivi du processus de déploiement afin de permettre les opérations « Annuler » pour une version précédemment déployée. L’outil n’a aucune dépendance externe et peut fonctionner autonome à l’aide d’API de TFS et le Kit de développement logiciel Azure.
- [La livraison continue : Mises à jour de logiciels fiables via l’automatisation du déploiement, de Test et de Build](https://www.amazon.com/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912/ref=sr_1_1?s=books&amp;ie=UTF8&amp;qid=1377126361). Livre Jez modeste.
- [Il Release. Concevoir et déployer des logiciels de l’environnement de production](https://www.amazon.com/Release-It-Production-Ready-Pragmatic-Programmers/dp/0978739213). Livre Michael T. Nygard.

>[!div class="step-by-step"]
[Précédent](source-control.md)
[Suivant](web-development-best-practices.md)
