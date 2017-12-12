---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment
title: "Configuration des propriétés de déploiement pour un environnement cible | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment configurer les propriétés spécifiques à l’environnement pour déployer l’exemple de solution de gestionnaire de contacts pour un environnement cible spécifique..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: b5b86e03-b8ed-46e6-90fa-e1da88ef34e9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment
msc.type: authoredcontent
ms.openlocfilehash: f27b8376b332ff21185be0fd5c00ced7d40a20bd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-deployment-properties-for-a-target-environment"></a>Configuration des propriétés de déploiement pour un environnement cible
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment configurer les propriétés spécifiques à l’environnement pour déployer l’exemple de solution de gestionnaire de contacts pour un environnement cible spécifique.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) solution & #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="process-overview"></a>Vue d’ensemble du processus

Le fichier de projet que vous allez utiliser pour générer et déployer la solution de gestionnaire de contacts est divisé en deux fichiers physiques :

- Un contenant universel générer les paramètres et les instructions (le *Publish.proj* fichier).
- Paramètres de génération qui contient l’environnement spécifiques (*Env-Dev.proj*, *Env-Stage.proj*, et ainsi de suite).

Au moment de la génération, le fichier de projet spécifique à l’environnement approprié est fusionné dans universel *Publish.proj* fichier pour former un ensemble complet d’instructions de génération. Vous pouvez configurer le déploiement dans les environnements de destination spécifique en créant ou en personnalisant les fichiers de projet spécifique à un environnement avec des paramètres qui décrivent votre propre scénario de déploiement.

Un grand nombre de ces valeurs est déterminé par la configuration de votre environnement de destination & #x 2014 ; en particulier, si votre serveur web de cible est configuré pour utiliser le Service de l’Agent de déploiement Web (l’agent distant) ou le Gestionnaire de déploiement Web. Pour plus d’informations sur ces approches et pour obtenir des conseils sur le choix de l’approche appropriée pour votre propre environnement, consultez [en choisissant l’approche de droite pour le déploiement Web](choosing-the-right-approach-to-web-deployment.md).

Le [responsable du Contact scénario](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md) requiert deux fichiers de projet spécifique à l’environnement :

- Déploiement dans un environnement de test de développeur (*Env-Dev.proj*). L’environnement de développement de test est configuré pour accepter les déploiements à distance à l’aide de l’agent distant, comme décrit dans [scénario : configuration d’un environnement de Test pour le déploiement Web](scenario-configuring-a-test-environment-for-web-deployment.md). Ce fichier doit fournir l’agent distant, adresse de point de terminaison ainsi que les paramètres spécifiques à l’emplacement, comme les chaînes de connexion et les points de terminaison de service.
- Déploiement dans un environnement intermédiaire (*Env-Stage.proj*). L’environnement intermédiaire est configuré pour accepter les déploiements à distance à l’aide du Gestionnaire de déploiement Web, comme décrit dans [scénario : configuration d’un environnement intermédiaire pour le déploiement Web](scenario-configuring-a-staging-environment-for-web-deployment.md). Ce fichier doit fournissent l’adresse de point de terminaison d’un gestionnaire de déploiement Web, ainsi que les paramètres spécifiques à l’emplacement, comme des chaînes de connexion et de points de terminaison de service.

Il est important de noter que les paramètres que vous configurez dans le fichier de projet spécifique à un environnement n’affectent pas le contenu du site web du package lui-même & #x 2014 ; au lieu de cela, ils contrôlent la manière dont le package est déployé et les valeurs de paramètre sont fournies lorsque la package est extrait. Vous importez le package web dans l’environnement de production manuellement, comme décrit dans [scénario : configuration d’un environnement de Production pour le déploiement Web](scenario-configuring-a-production-environment-for-web-deployment.md) et [installer manuellement les Packages Web](../web-deployment-in-the-enterprise/manually-installing-web-packages.md), importe donc peu quels paramètres vous avez utilisé dans le fichier de projet spécifique à un environnement quand vous avez généré le package. Gestionnaire des Services Internet (IIS) vous demandera des valeurs paramétrables, telles que les chaînes de connexion et les points de terminaison de service, lorsque vous importez le package.

Pour déployer la solution de gestionnaire de contacts pour votre propre environnement cible, vous pouvez personnaliser ce fichier ou l’utiliser comme un modèle et créer votre propre fichier.

**Pour configurer les paramètres spécifiques à l’environnement de déploiement pour la solution de gestionnaire de contacts**

1. Ouvrez la solution ContactManager WCF dans Visual Studio 2010.
2. Dans le **l’Explorateur de solutions** fenêtre, développez le **publier** dossier, développez le **EnvConfig** dossier, puis double-cliquez sur **Env-Dev.proj**.

    ![](configuring-deployment-properties-for-a-target-environment/_static/image1.png)
3. Remplacez les valeurs de propriété dans le *Env-Dev.proj* fichier avec les valeurs correctes pour votre propre environnement de test.

    > [!NOTE]
    > Le tableau qui suit cette procédure fournit plus d’informations sur chacune de ces propriétés.
4. Enregistrez votre travail et fermez le *Env-Dev.proj* fichier.

## <a name="choosing-the-right-deployment-properties"></a>En choisissant les propriétés de déploiement appropriée

Ce tableau décrit l’objectif de chaque propriété dans l’exemple de fichier de projet spécifique à un environnement, *Env-Dev.proj*et fournit quelques conseils sur les valeurs que vous devez fournir.

| Nom de la propriété | Détails |
| --- | --- |
| **MSDeployComputerName** le nom de la destination web server ou le service de point de terminaison. | Si vous déployez vers le service de l’agent distant sur le serveur web de destination, vous pouvez spécifier le nom de l’ordinateur cible (par exemple, **TESTWEB1** ou **TESTWEB1.fabrikam.net**), ou vous pouvez spécifier l’instance distante point de terminaison de l’agent (par exemple, `http://TESTWEB1/MSDEPLOYAGENTSERVICE`). Le déploiement fonctionne de la même manière dans chaque cas. Si vous déployez vers le Gestionnaire de déploiement Web sur le serveur web de destination, vous devez spécifier le point de terminaison de service et inclure le nom du site Web IIS en tant que paramètre de chaîne de requête (par exemple, `https://STAGEWEB1:8172/MSDeploy.axd?site=DemoSite`). |
| **MSDeployAuth** la méthode de déploiement Web doit utiliser pour s’authentifier auprès de l’ordinateur distant. | Cela doit être défini sur **NTLM** ou **base**. En règle générale, vous allez utiliser **NTLM** si vous déployez vers le service de l’agent distant et **base** si vous déployez vers le Gestionnaire de déploiement Web. Si vous utilisez l’authentification de base, vous devez également spécifier le nom d’utilisateur et un mot de passe que doit emprunter l’identité de l’outil de déploiement Web IIS (Web Deploy) afin d’effectuer le déploiement. Dans cet exemple, ces valeurs sont fournies via le **MSDeployUsername** et **MSDeployPassword** propriétés. Si vous utilisez l’authentification NTLM, vous pouvez omettre ces propriétés ou les laisser vides. |
| **MSDeployUsername** si vous utilisez l’authentification de base, Web Deploy utilisera ce compte sur l’ordinateur distant. | Il doit prendre la forme *domaine*\** nom d’utilisateur (par exemple, **FABRIKAM\matt**). Cette valeur est utilisée uniquement si vous spécifiez l’authentification de base. Si vous utilisez l’authentification NTLM, la propriété peut être omise. Si une valeur est fournie, il sera ignoré. |
| **MSDeployPassword** si vous utilisez l’authentification de base, Web Deploy utilise ce mot de passe sur l’ordinateur distant. | Il s’agit du mot de passe pour le compte d’utilisateur que vous avez spécifié dans le **MSDeployUsername** propriété. Cette valeur est utilisée uniquement si vous spécifiez l’authentification de base. Si vous utilisez l’authentification NTLM, la propriété peut être omise. Si une valeur est fournie, il sera ignoré. |
| **ContactManagerIisPath** chemin d’accès IIS sur lequel vous souhaitez déployer l’application MVC du Gestionnaire de contacts. | Il doit s’agir du chemin d’accès tel qu’il apparaît dans le Gestionnaire des services Internet, sous la forme [*nom du site Web IIS*] / [*web**nom de l’application*]. N’oubliez pas que le site Web IIS doit exister avant de déployer votre application. Par exemple, si vous avez créé un site Web d’IIS nommé DemoSite, vous pouvez spécifier le chemin d’accès IIS pour l’application MVC comme DemoSite/ContactManager. |
| **ContactManagerServiceIisPath** chemin d’accès IIS sur lequel vous souhaitez déployer le service WCF du Gestionnaire de contacts. | Par exemple, si vous avez créé un site Web d’IIS nommé DemoSite, vous pouvez spécifier le chemin d’accès IIS pour le service WCF en tant que **DemoSite/ContactManagerService**. |
| **ContactManagerTargetUrl** l’URL à laquelle le service WCF peut être atteint. | Cette opération prendra la forme [*URL racine du site Web IIS*] / [*nom de l’application service*] / [*point de terminaison de service*]. Par exemple, si vous avez créé un site Web IIS sur le port 85, l’URL prendrait la forme `http://localhost:85/ContactManagerService/ContactService.svc`. N’oubliez pas que l’application MVC et le service WCF sont déployés sur le même serveur. Par conséquent, cette URL est toujours accessible à partir de l’ordinateur sur lequel il est installé. Pour cette raison, il est préférable d’utiliser localhost ou l’adresse IP, plutôt que le nom de l’ordinateur ou un en-tête d’hôte dans l’URL. Si vous utilisez le nom de l’ordinateur ou un en-tête d’hôte, le [contrôle de bouclage](https://go.microsoft.com/?linkid=9805131) fonctionnalité de sécurité dans IIS peut-être bloquer l’URL et retourner un **401.1 HTTP - non autorisé** erreur. |
| **CmDatabaseConnectionString** la chaîne de connexion pour le serveur de base de données. | La chaîne de connexion détermine à la fois les informations d’identification VSDBCMD utilisera pour contacter le serveur de base de données et créer la base de données et les informations d’identification que le pool d’applications de serveur web utilisera pour contacter le serveur de base de données et interagir avec la base de données. Vous avez fait ici deux possibilités. Vous pouvez spécifier **Integrated Security = true**, auquel cas l’authentification Windows intégrée est utilisée : **Source de données = TESTDB1 ; Integrated Security = true** dans ce cas, la base de données sera créée à l’aide de les informations d’identification de l’utilisateur qui exécute l’outil VSDBCMD exécutable et de l’application accédera à la base de données à l’aide de l’identité du compte d’ordinateur du serveur web. Ou bien, vous pouvez spécifier le nom d’utilisateur et un mot de passe d’un compte SQL Server. Dans ce cas, les informations d’identification SQL Server sont utilisées par VSDBCMD pour créer la base de données et par le pool d’applications d’interagir avec la base de données : **Source de données = TESTDB1 ; Id d’utilisateur = ASqlUser ; Mot de passe = Pa$ $w0rd** les procédures de cette rubrique supposent que vous allez utiliser l’authentification Windows intégrée. |
| **CmTargetDatabase** le nom que vous souhaitez donner à la base de données que vous allez créer sur le serveur de base de données. | La valeur que vous fournissez ici est ajoutée à la commande VSDBCMD en tant que paramètre. Il est également utilisé pour générer une chaîne de connexion complète le pool d’applications sur le serveur web peut utiliser pour interagir avec la base de données. |
  

Ces exemples montrent comment vous pouvez configurer ces propriétés pour les scénarios de déploiement spécifique.

### <a name="example-1x2014deployment-to-the-remote-agent-service"></a>Exemple 1 & #x 2014 ; le déploiement pour le Service de l’Agent distant

Dans cet exemple :

- Le service de l’agent distant sur TESTWEB1 que vous déployez.
- Vous vous demandez le déploiement Web pour utiliser l’authentification NTLM. Web Deploy s’exécute à l’aide des informations d’identification que vous avez utilisé pour appeler Microsoft Build Engine (MSBuild).
- Vous utilisez l’authentification intégrée pour déployer le **ContactManager** base de données TESTDB1. La base de données sera déployée à l’aide des informations d’identification que vous avez utilisé pour appeler MSBuild.


[!code-xml[Main](configuring-deployment-properties-for-a-target-environment/samples/sample1.xml)]


### <a name="example-2x2014deployment-to-the-web-deploy-handler-endpoint"></a>Exemple 2 & #x 2014 ; le déploiement sur le Web déployer le Gestionnaire de point de terminaison

Dans cet exemple :

- Vous déployez vers le point de terminaison du service Gestionnaire de déploiement Web sur STAGEWEB1.
- Vous vous demandez Web Deploy pour utiliser l’authentification de base.
- Vous spécifiez que Web Deploy doit emprunter l’identité du compte FABRIKAM\stagingdeployer sur l’ordinateur distant.
- Vous utilisez l’authentification SQL Server pour déployer le **ContactManager** STAGEDB1 base de données.


[!code-xml[Main](configuring-deployment-properties-for-a-target-environment/samples/sample2.xml)]


## <a name="conclusion"></a>Conclusion

À ce stade, vos fichiers projet sont entièrement configurés pour générer et déployer la solution de gestionnaire de contacts pour un ou plusieurs environnements de destination.

Pour utiliser ces fichiers de projet dans le cadre d’un processus de déploiement pas à pas, reproductibles, vous devez exécuter le *Publish.proj* de fichiers à l’aide de MSBuild et le passer à l’emplacement du fichier de projet spécifique à un environnement en tant que paramètre. Pour cela, de différentes manières :

- Pour une vue d’ensemble de MSBuild et une introduction aux fichiers de projet personnalisés, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md).
- Pour plus d’informations sur la façon de formuler une commande de MSBuild qui exécute vos fichiers de projet personnalisés, consultez [déploiement des Packages Web](../web-deployment-in-the-enterprise/deploying-web-packages.md).
- Pour plus d’informations sur la façon d’incorporer vos commandes de MSBuild dans un fichier de commandes pour les déploiements de la seule étape, reproductibles, consultez [création et exécution d’un fichier de commandes de déploiement](../web-deployment-in-the-enterprise/creating-and-running-a-deployment-command-file.md).
- Pour plus d’informations sur la façon d’exécuter vos fichiers de projet personnalisé à partir de Team Build, consultez [création d’une définition de Build que prend en charge le déploiement](../configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment.md).

>[!div class="step-by-step"]
[Précédent](creating-a-server-farm-with-the-web-farm-framework.md)
