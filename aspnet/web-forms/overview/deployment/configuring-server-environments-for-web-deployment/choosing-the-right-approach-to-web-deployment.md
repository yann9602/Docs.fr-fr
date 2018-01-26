---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment
title: "Choisir l’approche appropriée pour le déploiement Web | Documents Microsoft"
author: jrjlee
description: "Lorsque vous travaillez avec les Internet Information Services (IIS) outil de déploiement Web (Web Deploy) 2.0 ou version ultérieure, il existe trois approches principales que vous pouvez utiliser pour obtenir..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 787a53fd-9901-4a11-9d58-61e0509cda45
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: b77aa37160f3822f58908866e44497aea3d3bdc8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="choosing-the-right-approach-to-web-deployment"></a>Choisir l’approche appropriée pour le déploiement Web
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Lorsque vous travaillez avec les Internet Information Services (IIS) outil de déploiement Web (Web Deploy) 2.0 ou version ultérieure, il existe trois approches principales, que vous pouvez utiliser pour obtenir vos applications empaquetées web sur un serveur web. Vous pouvez :
> 
> - Déployer l’application à partir d’un emplacement distant en ciblant la *Service de l’Agent de déploiement Web* (également appelé le « agent distant ») sur le serveur de destination.
> - Déployez l’application à partir d’un emplacement distant à l’aide de la déployer sur demande Web (également appelé le « temp agent »).
> - Déployer l’application à partir d’un emplacement distant en ciblant la *Gestionnaire de déploiement Web IIS* sur le serveur de destination.
> - Déployez l’application en copie le package web vers le serveur de destination et en les important via le Gestionnaire IIS manuellement.
> 
> Façon dont vous configurez vos serveurs web de destination dépend quelle approche au déploiement que vous souhaitez utiliser. Cette rubrique vous permet de déterminer l’approche de déploiement qui vous consiste.


Ce tableau présente les principaux avantages et les inconvénients de chaque approche de déploiement, ainsi que les scénarios qui généralement en fonction de chaque approche.

| Approche | Avantages | Inconvénients | Scénarios typiques |
| --- | --- | --- | --- |
| Agent distant | Il est facile à configurer. Il convient pour les mises à jour régulières pour les applications web et le contenu. | L’utilisateur doit être un administrateur sur le serveur cible. L’utilisateur ne peut pas fournir d’autres informations d’identification. | Environnements de développement. Environnements de test. |
| Agent temporaire | Il est inutile d’installer Web Deploy sur l’ordinateur cible. La dernière version de Web Deploy est automatiquement utilisée. | L’utilisateur doit être un administrateur sur le serveur cible. L’utilisateur ne peut pas fournir d’autres informations d’identification. | Environnements de développement. Environnements de test. |
| Gestionnaire de déploiement Web | Les autres utilisateurs peuvent déployer du contenu. Il convient pour les mises à jour régulières pour les applications web et le contenu. | Il est beaucoup plus complexe à configurer. | Environnements de préproduction. Environnements de production intranet. Environnements hébergés. |
| Déploiement en mode hors connexion | Il est très facile à configurer. Il convient aux environnements isolés. | L’administrateur du serveur doit manuellement copier et importer le package web chaque fois. | Environnements de production sur Internet. Environnements de réseau isolé. |
  

## <a name="using-the-remote-agent"></a>À l’aide de l’Agent distant

Lorsque vous installez Web Deploy sur un serveur de destination à l’aide de paramètres par défaut, le Service de l’Agent de déploiement Web (le « agent à distance ») est automatiquement installé et démarré. Par défaut, l’agent distant expose un point de terminaison HTTP à cette adresse :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample1.cmd)]


> [!NOTE]
> Vous pouvez remplacer [*server*] avec le nom de l’ordinateur de votre serveur web, une adresse IP pour votre serveur web ou un nom d’hôte qui se résout sur votre serveur web.


Les administrateurs de serveur peuvent déployer des packages web à partir d’un emplacement distant, comme un ordinateur de développement ou un serveur de builds, en spécifiant cette adresse de point de terminaison. Par exemple, supposons que Matt Hink chez Fabrikam, Inc. a généré le projet d’application web ContactManager.Mvc sur son ordinateur de développement. Le processus de génération génère un package, avec un *. deploy.cmd* fichier qui contient les commandes de déploiement Web requis pour installer le package. Si Matt est un administrateur de serveur sur le serveur TESTWEB1, il peut déployer l’application web pour le serveur web de test en exécutant cette commande sur son ordinateur de développement :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample2.cmd)]


En réalité, le fichier exécutable de Web Deploy peut déduire l’adresse de point de terminaison de l’agent distant si vous fournissez le nom de l’ordinateur, par conséquent, Matt doit uniquement tapez ce qui suit :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample3.cmd)]


> [!NOTE]
> Pour plus d’informations sur la syntaxe de ligne de commande de Web Deploy et *. deploy.cmd* fichiers, voir [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx).


L’agent distant offre un moyen simple de déployer le contenu d’un emplacement distant, et cette approche peut fonctionner correctement avec un déploiement en un clic ou automatisé. Toutefois, l’utilisateur qui exécute la commande de déploiement doit également être un administrateur de domaine ou d’un membre du groupe Administrateurs local sur le serveur de destination. En outre, l’agent distant ne prend en charge l’authentification de base, vous ne pouvez pas passer des autres informations d’identification sur la ligne de commande.

L’agent à distance fournit une approche très utile pour le déploiement dans des scénarios de test, où il n’est pas rare pour les développeurs d’avoir un contrôle administrateur complet sur un environnement de serveur de test et les applications sont généralement reconstruites et redéployées très ou de développement fréquemment. Toutefois, cette approche est généralement moins acceptable pour les environnements de production ou intermédiaire.

Pour obtenir un exemple de bout en bout d’un scénario qui utilise l’approche de l’agent distant, consultez [scénario : configuration d’un environnement de Test pour le déploiement Web](scenario-configuring-a-test-environment-for-web-deployment.md).

## <a name="using-the-temp-agent"></a>À l’aide de l’Agent temporaire

L’approche temporaire de l’agent de déploiement est similaire à l’approche de l’agent distant. Toutefois, contrairement à l’approche de l’agent distant, vous n’avez pas besoin d’installer Web Deploy sur le serveur web de destination. Au lieu de cela, lorsque vous effectuez le déploiement, Web Deploy installe une version temporaire du service de l’agent de déploiement web sur le serveur de destination et utilise ces données pour déployer votre contenu sur le serveur IIS. Lorsque le déploiement est terminé, tous les fichiers temporaires sont supprimés.

Si vous souhaitez utiliser le paramètre du fournisseur temporaire de l’agent, ajoutez le **/g** indicateur à votre commande de déploiement :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample4.cmd)]


> [!NOTE]
> Vous ne pouvez pas utiliser l’agent temporaire si le service de l’agent de déploiement web est installé sur l’ordinateur de destination, même si le service n’est pas en cours d’exécution.


L’avantage de cette approche est que vous n’avez pas besoin de conserver les installations de Web Deploy sur vos serveurs de destination. En outre, vous n’avez pas besoin de s’assurer que les ordinateurs source et de destination exécutent la même version de Web Deploy. Toutefois, cette approche comporte les mêmes limitations principales en tant que l’approche de l’agent distant, à savoir que vous devez être un administrateur local sur le serveur de destination afin de déployer du contenu, et que l’authentification NTLM est pris en charge. L’approche de l’agent temporaire requiert également la configuration initiale de beaucoup plus de l’environnement de destination.

Pour plus d’informations sur l’utilisation de l’agent temporaire, consultez [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx) et [Web déployer à la demande](https://technet.microsoft.com/library/ee517345(WS.10).aspx).

## <a name="using-the-web-deploy-handler"></a>Utilisation du Web de déployer le Gestionnaire

Pour IIS 7 et versions ultérieures, Web Deploy propose une approche de déploiement autre via le Gestionnaire de déploiement Web IIS. Le Gestionnaire de déploiement Web est étroitement intégré avec le Service de gestion Web (WMSvc), IIS qui est conçu pour permettre aux utilisateurs de gérer des sites Web IIS à partir d’emplacements distants.

Par défaut, l’agent distant expose un point de terminaison HTTP à cette adresse :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample5.cmd)]


> [!NOTE]
> Vous pouvez remplacer [*server*] avec le nom de l’ordinateur de votre serveur web, une adresse IP pour votre serveur web ou un nom d’hôte qui se résout sur votre serveur web.


L’avantage du Gestionnaire de déploiement Web sur l’agent distant et l’agent temporaire, est que vous pouvez configurer IIS pour autoriser les utilisateurs non administrateurs déployer des applications et du contenu sur des sites Web IIS spécifiques. Le Gestionnaire de déploiement Web prend également en charge l’authentification de base, afin de pouvoir fournir des autres informations d’identification en tant que paramètres dans vos commandes de déploiement Web. Le principal inconvénient est que le Gestionnaire de déploiement Web est initialement beaucoup plus compliqué à installer et configurer.

Dans le cas des utilisateurs non administrateurs, le Service de gestion Web (WMSvc) uniquement permet à l’utilisateur pour se connecter à IIS à l’aide d’une connexion au niveau du site, plutôt que d’une connexion au niveau du serveur. Pour accéder à un site particulier, vous pouvez inclure une chaîne de requête spécifique dans l’adresse de point de terminaison :


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample6.cmd)]


Par exemple, qu'un processus de build est configuré pour déployer automatiquement une application web dans un environnement intermédiaire après chaque génération réussie. Si vous avez utilisé l’approche de l’agent distant, vous devez rendre l’identité de processus de génération d’un administrateur sur vos serveurs de destination. En revanche, à l’aide de la méthode de gestionnaire de déploiement Web vous pouvez donner à un utilisateur non administrateur & le #x 2014 ; **FABRIKAM\stagingdeployer** dans cette cas, & #x 2014 ; autorisé à un site IIS spécifique uniquement et le processus de génération peut fournir ces informations d’identification pour déployer le package web.


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample7.cmd)]


> [!NOTE]
> Pour plus d’informations sur les opérations de ligne de commande de déploiement Web et la syntaxe, consultez [référence de ligne de commande de déploiement Web](https://technet.microsoft.com/library/dd568991(v=ws.10).aspx). Pour plus d’informations sur l’utilisation de la *. deploy.cmd* de fichiers, consultez [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx).


Le Gestionnaire de déploiement Web fournit une approche très utile pour le déploiement dans les environnements, les environnements hébergés et les environnements de production basée sur l’intranet, où l’accès à distance sur le serveur est disponible mais les informations d’identification d’administrateur ne sont pas de transfert.

Pour obtenir un exemple de bout en bout d’un scénario qui utilise l’approche de gestionnaire de déploiement Web, consultez [scénario : configuration d’un environnement intermédiaire pour le déploiement Web](scenario-configuring-a-staging-environment-for-web-deployment.md).

## <a name="using-offline-deployment"></a>À l’aide de déploiement en mode hors connexion

Dans certains cas, il n’est pas possible ou pratique déployer des applications et du contenu à un site Web IIS à partir d’un emplacement distant. Par exemple, les ordinateurs source et de destination peuvent être dans des réseaux isolés ou des segments de réseau, ou la stratégie de pare-feu ne peut pas autoriser l’accès à distance.

Dans les scénarios comme celles-ci, vous pouvez toujours utiliser l’empaquetage et publication des fonctionnalités de déploiement de Web ; vous venez ne peut pas les utiliser à partir d’un emplacement distant. Au lieu de cela, un administrateur sur le serveur de destination doit copier le package sur le serveur web et importez-le dans le Gestionnaire des services Internet.

![](choosing-the-right-approach-to-web-deployment/_static/image1.png)

L’approche de déploiement en mode hors connexion est généralement utile dans les environnements de production sur Internet, où les serveurs dans un réseau de périmètre peuvent restreinte connectivité avec les ordinateurs du réseau interne.

Pour obtenir un exemple de bout en bout d’un scénario qui utilise l’approche de déploiement en mode hors connexion, consultez [scénario : configuration d’un environnement de Production pour le déploiement Web](scenario-configuring-a-production-environment-for-web-deployment.md).

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les opérations de ligne de commande de déploiement Web et la syntaxe, consultez [référence de ligne de commande de déploiement Web](https://technet.microsoft.com/library/dd568991(v=ws.10).aspx). Pour plus d’informations sur l’utilisation de la *. deploy.cmd* de fichiers, consultez [Comment : installer un Package de déploiement à l’aide du fichier deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx).

Pour obtenir des instructions plus générales sur les différentes façons dans laquelle vous pouvez déployer des packages web à partir d’un ordinateur distant, consultez [à l’aide de Web Deploy Remotely](https://technet.microsoft.com/library/ee461175(WS.10).aspx). Pour plus d’informations sur l’utilisation de Web déployer à la demande, consultez [Web déployer à la demande](https://technet.microsoft.com/library/ee517345(WS.10).aspx).

>[!div class="step-by-step"]
[Précédent](configuring-server-environments-for-web-deployment.md)
[Suivant](scenario-configuring-a-test-environment-for-web-deployment.md)
