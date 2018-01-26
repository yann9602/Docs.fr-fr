---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction
title: "Création d’applications de Cloud du monde réel avec Azure | Documents Microsoft"
author: MikeWasson
description: "Ce livre électronique vous guide dans une approche basée sur des modèles de création de solutions de cloud du monde réel. Les modèles s’appliquent au processus de développement, ainsi que pour un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: accfa16a-ab15-4c26-9ad4-babdc2a77d2e
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction
msc.type: authoredcontent
ms.openlocfilehash: 4de0b52e0b4ae7ce00e7b07bce2decfc5068964a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="building-real-world-cloud-apps-with-azure"></a>Création d’applications de Cloud du monde réel avec Azure
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Ce livre électronique vous guide dans une approche basée sur des modèles de création de solutions de cloud du monde réel. Les modèles s’appliquent au processus de développement, ainsi qu’à l’architecture et des pratiques de codage.
> 
> Le contenu est basé sur une présentation développé par Scott Guthrie et remis par lui à la conférence de développeurs norvégien (NDC) en juin 2013 ([partie 1](http://vimeo.com/68215538), [partie 2](http://vimeo.com/68215602)) et à Microsoft Tech Ed Australia dans Septembre 2013 ([partie 1](https://channel9.msdn.com/Events/TechEd/Australia/2013/AZR324), [partie 2](https://channel9.msdn.com/Events/TechEd/Australia/2013/AZR325)). [Beaucoup d’autres](more-patterns-and-guidance.md#acknowledgments) mis à jour et augmentée le contenu lors de la transition à partir de la vidéo à la forme écrite.


## <a name="intended-audience"></a>Public visé

Les développeurs qui sont obtenir des informations sur le développement pour le cloud, envisagez de passer au cloud, ou qui sont nouvelles pour le développement en nuage trouverez ici une vue d’ensemble concise des concepts et des pratiques, qu'ils ont besoin de connaître plus importantes. Les concepts sont illustrées par des exemples concrets et chaque chapitre des liens vers des autres ressources pour plus d’informations. Les exemples et les liens vers des ressources supplémentaires sont pour les services et des infrastructures de Microsoft, mais les principes illustrés s’appliquent aux autres infrastructures de développement web et les environnements de cloud computing.

Les développeurs qui développent déjà pour le cloud peuvent trouver ici des idées permettant de les rendre plus efficace. Chaque chapitre dans la série peut être lu indépendamment, afin de pouvoir choisir et choisissez des rubriques qui vous intéressent.

Toute personne observé de Scott Guthrie *construction réelle applications Cloud du monde avec Azure* présentation et souhaite disposer de plus de détails et informations mises à jour seront avère qu’ici.

<a id="patterns"></a>
## <a name="cloud-development-patterns"></a>Modèles de développement cloud

Ce livre électronique explique treize recommandé de modèles pour le développement en nuage. « Modèle » est utilisé ici au sens large pour signifier une méthode recommandée pour effectuer les opérations : la meilleure façon de générer le développement, la conception et le codage des applications cloud. Il s’agit de modèles qui vous aideront « font partie de la table d’importation principale de réussite » si vous les respectez.

- [Automatiser tout](automate-everything.md).

    - Utiliser des scripts pour optimiser l’efficacité et réduire les erreurs dans les processus répétitives.
    - Démo : Scripts de gestion Azure.
- [Contrôle de code source](source-control.md). 

    - Configurer la structure de branches dans le contrôle de code source pour faciliter le flux de travail DevOps.
    - Démo : ajouter des scripts pour le contrôle de code source.
    - Démo : conserver les données sensibles en dehors du contrôle de code source.
    - Démo : utiliser Git dans Visual Studio.
- [Intégration continue et remise](continuous-integration-and-continuous-delivery.md). 

    - Automatiser la génération et déploiement avec chaque archivage du contrôle source.
- [Meilleures pratiques de développement de Web](web-development-best-practices.md). 

    - Conserver le niveau web sans état.
    - Démo : mise à l’échelle et la montée en puissance automatique dans les applications Web dans Azure App Service.
    - Évitez d’état de session.
    - Utiliser un CDN.
    - Utilisez le modèle de programmation asynchrone.
    - Démo : les asynchrones dans ASP.NET MVC et Entity Framework.
- [L’authentification unique](single-sign-on.md). 

    - Introduction à Azure Active Directory.
    - Démonstration : créer une application ASP.NET qui utilise Azure Active Directory.
- [Options de stockage de données](data-storage-options.md). 

    - Types de banques de données.
    - Comment choisir le magasin de données de droite.
    - Démo : De la base de données SQL Azure.
- [Stratégies de partitionnement des données](data-partitioning-strategies.md). 

    - Partitionner les données verticalement, horizontalement ou les deux pour faciliter la mise à l’échelle d’une base de données relationnelle.
- [Stockage d’objets blob non structurées](unstructured-blob-storage.md). 

    - Stockez les fichiers dans le cloud à l’aide du service blob.
    - Démo : à l’aide de stockage d’objets blob dans l’application corriger.
- [Conception pour faire face aux défaillances](design-to-survive-failures.md). 

    - Types de défaillances.
    - Échec de la portée.
    - Présentation des SLA.
- [Surveillance et télémétrie](monitoring-and-telemetry.md). 

    - Pourquoi vous devez acheter une application de télémétrie et écrire votre propre code pour instrumenter votre application.
    - Démo : New Relic pour Azure
    - Démo : le code de journalisation dans l’application corriger.
    - Démo : injection de dépendances dans l’application corriger.
    - Démo : prise en charge de journalisation intégrés dans Azure.
- [Gestion d’erreurs transitoires](transient-fault-handling.md). 

    - Logique de nouvelle tentative/temporisation intelligente permet d’atténuer l’effet d’erreurs temporaires.
    - Démo : nouvelle tentative/temporisation dans Entity Framework 6.
- [Mise en cache distribuée](distributed-caching.md). 

    - Améliorer l’évolutivité et réduire les coûts de transaction de base de données à l’aide de la mise en cache distribuée.
- [Modèle de travail centré sur la file d’attente](queue-centric-work-pattern.md). 

    - Activer la haute disponibilité et améliorer l’évolutivité en faiblement couplage niveaux web et de travail.
    - Démo : Files d’attente de stockage Azure dans l’application corriger.
- [Plus les modèles d’applications et des conseils de cloud](more-patterns-and-guidance.md).
- [Annexe : L’exemple d’application Fix It](the-fix-it-sample-application.md)

    - Problèmes connus
    - Meilleures pratiques
    - Comment télécharger, créer, exécuter et déployer.

Ces modèles s’appliquent à tous les environnements de cloud, mais nous allons illustrer à l’aide d’exemples basés sur les technologies Microsoft et des services, tels que Visual Studio, Team Foundation Service, ASP.NET et Azure.

Cette suite de ce chapitre présente l’application d’exemple corriger et les applications Web dans un environnement en nuage du Service d’applications Azure corriger application s’exécute dans.

<a id="fixit"></a>
## <a name="the-fix-it-sample-application"></a>L’exemple d’application de correctif

La plupart des captures d’écran et des exemples de code présentés dans ce livre électronique sont basées sur l’application Fix It initialement développée par [Scott Guthrie](https://weblogs.asp.net/scottgu/) pour illustrer les modèles de développement d’applications cloud recommandée et pratiques.

![Corriger la page d’accueil application](introduction/_static/image1.png)

L’exemple d’application est un élément de travail simple système de tickets. Lorsque vous devez résoudre un problème, créez un ticket et l’affecter à une personne et d’autres permettre se connecter et voir les tickets affectés les uns aux autres et tickets est marquée comme terminée quand le travail est terminé.

Il s’agit d’un projet web de Visual Studio standard. Il repose sur ASP.NET MVC et utilise une base de données SQL Server. Il peut s’exécuter localement dans IIS Express et peut être déployée pour un Site Web Azure pour s’exécuter dans le cloud. Vous pouvez vous connecter à l’aide de l’authentification par formulaire et la base de données locale ou à l’aide d’un fournisseur de réseaux sociaux tels que Google. (Plus tard nous allons montrer également comment se connecter avec un compte d’organisation Active Directory.)

![Page de connexion](introduction/_static/image2.png)

Une fois que vous êtes connecté dans vous pouvez créer un ticket, l’affecter à une personne et télécharger une image de ce que vous souhaitez obtenir fixe.

![Créer une tâche de réparation](introduction/_static/image3.png)

![Corriger la tâche créée](introduction/_static/image4.png)

Vous pouvez suivre la progression des éléments de travail que vous avez créé, consultez tickets assignés, afficher les détails de ticket et marquer les éléments comme étant terminée.

Il s’agit d’une application très simple à partir d’une perspective de fonctionnalité, mais vous allez apprendre à générer pour qu’il peut s’adapter à des millions d’utilisateurs et est résister aux éléments tels que les échecs de la base de données et les connexions arrêtées. Vous allez également apprendre à créer un flux de travail de développement agile et automatisée, ce qui vous permet de démarrer simple et rendre l’application de plus en plus en effectuant une itération du cycle de développement rapidement et efficacement.

<a id="waws"></a>
## <a name="web-apps-in-azure-app-service"></a>Applications Web dans Azure App Service

Utilisé pour l’application de corriger l’environnement du cloud est un service de Azure que nous appelons des Sites Web. Ce service est une façon que vous pouvez héberger votre application web dans Azure sans avoir à créer des ordinateurs virtuels et leur mise à jour, installer et configurer IIS, etc. Nous héberger votre site sur notre des machines virtuelles et automatiquement fournir de sauvegarde et de récupération et d’autres services pour vous. Le service de Sites Web fonctionne avec ASP.NET, Node.js, PHP et Python. Il vous permet de déployer rapidement à l’aide de Visual Studio, Web Deploy, FTP, Git ou TFS. Il est généralement que quelques secondes entre le démarrage d’un déploiement et l’heure de que votre mise à jour est disponible sur Internet. Il est gratuit commencer, et vous pouvez évoluer à mesure que votre trafic se développe.

En arrière-plan, les applications Web dans Azure App Service fournit un grand nombre de composants de l’architecture et les fonctionnalités que vous devriez créer vous-même si vous souhaitez héberger un site web à l’aide d’IIS sur vos propres machines virtuelles. Un composant est un point de terminaison de déploiement qui configure IIS et installe votre application sur autant de machines virtuelles que vous souhaitez exécuter votre site sur automatiquement.

![Service de déploiement](introduction/_static/image5.png)

Lorsqu’un utilisateur appuie sur le site web, ils n’ont pas accès les machines virtuelles IIS directement, elles passent par [Application Request Routing (ARR)](https://www.iis.net/downloads/microsoft/application-request-routing) équilibreurs de charge. Vous pouvez les utiliser avec vos propres serveurs, mais l’avantage est qu’ils sont configurés pour vous automatiquement. Ils utilisent une méthode heuristique intelligente qui tient compte des facteurs tels que l’affinité de session, profondeur de file d’attente dans IIS, et l’utilisation du processeur sur chaque ordinateur pour diriger le trafic vers les machines virtuelles qui hébergent votre site web.

![Équilibrage de charge ARR](introduction/_static/image6.png)

Si un ordinateur tombe en panne, Azure automatiquement extrait la rotation, tourne à une nouvelle instance de la machine virtuelle et commence à diriger le trafic vers la nouvelle instance--avec aucun temps d’arrêt de votre application.

![Récupération automatique à partir de la défaillance d’ordinateur](introduction/_static/image7.png)

Tout ceci a lieu automatiquement. Il vous suffit de faire est de créer un site web et de déployer votre application, à l’aide de Windows PowerShell, Visual Studio ou le portail de gestion Azure.

Pour rapidement et facilement obtenir un didacticiel qui montre comment créer une application web dans Visual Studio et le déployer sur un Site Web de Azure, consultez [prise en main Azure et ASP.NET](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

<a id="summary"></a>
## <a name="summary"></a>Récapitulatif

Cette introduction a passé à une liste de rubriques que couvre le carnet, captures d’écran de l’exemple d’application et une brève vue d’ensemble des applications Web dans l’environnement Azure App Service cloud. Un des principaux avantages du développement d’applications et pour le cloud est qu’il est facile automatiser les tâches répétitives développement telles que la création d’un environnement de test et de déploiement de votre code à ce dernier. Procédure à suivre qui est le sujet de la [chapitre suivant](automate-everything.md).

## <a name="resources"></a>Ressources

Pour plus d’informations sur les sujets abordés dans ce chapitre, consultez les ressources suivantes.

Documentation :

- [Les applications dans Azure App Service Web](https://azure.microsoft.com/services/app-service/web/). Page de documentation Azure sur les applications Web du portail.
- [Les applications Web, Services de cloud computing et machines virtuelles : quand les utiliser ?](https://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/) WAWS, comme illustré dans ce chapitre est simplement l’une des trois méthodes que vous pouvez exécuter des applications web dans Azure. Cet article explique les différences entre les trois méthodes et fournit des conseils sur le choix qui convient le mieux pour votre scénario. Comme les Sites Web, Services de cloud computing est une fonctionnalité de PaaS Azure. Les machines virtuelles sont une fonctionnalité IaaS. Pour obtenir une explication de PaaS et IaaS, consultez le [Options données](data-storage-options.md#paasiaas) chapitre.

Vidéos :

- [Scott Guthrie commence à l’étape 0 - quel est le système d’exploitation du Cloud Azure ?](https://azure.microsoft.com/documentation/videos/what-is-the-cloud-os-scottgu/)
- [Architecture de Sites Web - avec Stefan Schackow](https://azure.microsoft.com/documentation/videos/why-azure-web-sites-plus-architecture/).
- [Éléments internes de Sites Web Azure avec Nir Mashkowski](https://channel9.msdn.com/Shows/Web+Camps+TV/Windows-Azure-Web-Sites-Internals-with-Nir-Mashkowski).

>[!div class="step-by-step"]
[Next](automate-everything.md)
