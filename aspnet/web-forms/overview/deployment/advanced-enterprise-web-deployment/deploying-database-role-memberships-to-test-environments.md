---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-database-role-memberships-to-test-environments
title: "Déploiement des appartenances aux rôles de base de données dans les environnements de Test | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment ajouter des comptes d’utilisateurs aux rôles de base de données dans le cadre d’un déploiement de solution pour un environnement de test. Lorsque vous déployez une solution contenant..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 9b2af539-7ad9-47aa-b66e-873bd9906e79
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-database-role-memberships-to-test-environments
msc.type: authoredcontent
ms.openlocfilehash: ac780c6cd522f9216cafe3b5f23772ef6ebf5d11
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-database-role-memberships-to-test-environments"></a>Déploiement des appartenances aux rôles de base de données dans les environnements de Test
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment ajouter des comptes d’utilisateurs aux rôles de base de données dans le cadre d’un déploiement de solution pour un environnement de test.
> 
> Lorsque vous déployez une solution contenant un projet de base de données dans un environnement intermédiaire ou de production, vous ne souhaitez généralement que le développeur pour automatiser l’ajout de comptes d’utilisateurs aux rôles de base de données. Dans la plupart des cas, le développeur ne sont pas informés les comptes d’utilisateur doivent être ajoutés pour les rôles de base de données, et ces exigences peuvent changer à tout moment. Toutefois, lorsque vous déployez une solution contenant un projet de base de données pour un développement ou l’environnement de test, la situation est généralement différente :
> 
> - Le développeur généralement déploie de nouveau la solution sur une base régulière, souvent plusieurs fois par jour.
> - La base de données est généralement recréé sur chaque déploiement, ce qui signifie que les utilisateurs de base de données doivent être créés et ajoutés aux rôles après chaque déploiement.
> - Le développeur a généralement un contrôle total sur l’environnement de développement ou de test cible.
> 
> Dans ce scénario, il est souvent bénéfique automatiquement créer les utilisateurs de base de données et affecter des appartenances aux rôles de base de données dans le cadre du processus de déploiement.
> 
> Le facteur clé est que cette opération doit être conditionnel en fonction de l’environnement cible. Si vous déployez vers un intermédiaire ou d’un environnement de production, vous souhaitez ignorer l’opération. Si vous effectuez un déploiement à un développeur ou l’environnement de test, que vous souhaitez déployer des appartenances de rôle sans autre intervention. Cette rubrique décrit une approche, que vous pouvez utiliser pour relever ce défi.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Cette rubrique suppose que :

- Vous utilisez l’approche de fichier de projet de fractionnement pour le déploiement de solutions, comme décrit dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md).
- Vous appelez VSDBCMD à partir du fichier projet pour déployer votre projet de base de données, comme décrit dans [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md).

Pour créer des utilisateurs de base de données et affecter des appartenances aux rôles lorsque vous déployez un projet de base de données vers un environnement de test, vous devez :

- Créer un script Transact Structured Query Language (Transact-SQL) qui apporte des modifications de la base de données nécessaires.
- Création d’une cible de Microsoft Build Engine (MSBuild) qui utilise l’utilitaire sqlcmd.exe pour exécuter le script SQL.
- Configurez vos fichiers projet pour appeler la cible lorsque vous déployez votre solution vers un environnement de test.

Cette rubrique vous indique comment effectuer chacune de ces procédures.

## <a name="scripting-the-database-role-memberships"></a>Les appartenances aux rôles de base de données de script

Vous pouvez créer un script Transact-SQL dans un grand nombre de façons différentes, et dans n’importe quel emplacement que vous choisissez. La plus simple consiste à créer le script au sein de votre solution dans Visual Studio 2010.

**Pour créer un script SQL**

1. Dans le **l’Explorateur de solutions** fenêtre, développez le nœud de votre projet de base de données.
2. Avec le bouton droit le **Scripts** dossier, pointez sur **ajouter**, puis cliquez sur **nouveau dossier**.
3. Type **Test** comme nom de dossier, puis appuyez sur ENTRÉE.
4. Cliquez sur le **Test** dossier, pointez sur **ajouter**, puis cliquez sur **Script**.
5. Dans le **ajouter un nouvel élément** boîte de dialogue zone, donnez un nom significatif à votre script (par exemple, **AddRoleMemberships.sql**), puis cliquez sur **ajouter**.

    ![](deploying-database-role-memberships-to-test-environments/_static/image1.png)
6. Dans le *AddRoleMemberships.sql* , ajoutez les instructions Transact-SQL qui :

    1. Créez un utilisateur de base de données pour la connexion SQL Server qui accéder à votre base de données.
    2. Ajoutez l’utilisateur de base de données pour tous les rôles de base de données requis.
7. Le fichier doit ressembler à ceci :

    [!code-sql[Main](deploying-database-role-memberships-to-test-environments/samples/sample1.sql)]
8. Enregistrez le fichier.

## <a name="executing-the-script-on-the-target-database"></a>L’exécution du Script sur la base de données cible

Dans l’idéal, vous exécuterez les scripts Transact-SQL requises dans le cadre d’un script de post-déploiement lorsque vous déployez votre projet de base de données. Toutefois, les scripts de post-déploiement ne vous permettent pas d’exécuter la logique conditionnelle basée sur les configurations de solution ou des propriétés de génération. L’alternative consiste à exécuter vos scripts SQL directement dans le fichier de projet MSBuild, en créant un **cible** élément qui exécute une commande sqlcmd.exe. Vous pouvez utiliser cette commande pour exécuter votre script sur la base de données cible :


[!code-console[Main](deploying-database-role-memberships-to-test-environments/samples/sample2.cmd)]


> [!NOTE]
> Pour plus d’informations sur les options de ligne de commande sqlcmd, consultez [utilitaire sqlcmd](https://msdn.microsoft.com/en-us/library/ms162773.aspx).


Avant de vous incorporez cette commande dans une cible MSBuild, vous devez envisager les conditions dans lesquelles vous souhaitez que le script à exécuter :

- La base de données cible doit exister avant de modifier ses appartenances aux rôles. Par conséquent, vous devez exécuter ce script *après* le déploiement de la base de données.
- Vous devez inclure une condition afin que le script est exécuté uniquement pour les environnements de test.
- Si vous exécutez un déploiement « que se passe-t-il si » et le #x 2014 ; en d’autres termes, si vous utilisez la génération de scripts de déploiement, mais pas en cours d’exécution les & #x 2014 ; vous ne devez pas exécuter le script SQL.

Si vous utilisez l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), comme illustré dans l’exemple de solution de gestionnaire de contacts, vous pouvez fractionner les instructions de génération pour votre script SQL comme suit :

- Les requis des propriétés spécifiques à l’environnement, ainsi que la propriété qui détermine s’il faut déployer des autorisations, doit être placé dans le fichier de projet spécifique à l’environnement (par exemple, *Env-Dev.proj*).
- La cible MSBuild, ainsi que toutes les propriétés qui ne change pas entre les environnements de destination, doit être placé dans le fichier projet universel (par exemple, *Publish.proj*).

Dans le fichier de projet spécifique à un environnement, vous devez définir le nom du serveur de base de données, le nom de la base de données cible et une propriété booléenne qui permet à l’utilisateur de spécifier si vous souhaitez déployer les appartenances aux rôles.


[!code-xml[Main](deploying-database-role-memberships-to-test-environments/samples/sample3.xml)]


Dans le fichier de projet d’application universelle, vous devez fournir l’emplacement de l’exécutable sqlcmd et de l’emplacement du script SQL à exécuter. Ces propriétés reste le même quelle que soit l’environnement de destination. Vous devez également créer une cible de MSBuild pour exécuter la commande sqlcmd.


[!code-xml[Main](deploying-database-role-memberships-to-test-environments/samples/sample4.xml)]


Notez que vous ajoutez l’emplacement de l’exécutable sqlcmd une propriété statique, comme cela peut être utile à d’autres cibles. En revanche, définir l’emplacement de votre script SQL et la syntaxe de la commande sqlcmd en tant que propriétés dynamiques dans la cible, car elles ne seront pas requis avant l’exécution de la cible. Dans ce cas, le **DeployTestDBPermissions** cible sera exécutée uniquement si ces conditions sont remplies :

- Le **DeployTestDBRoleMemberships** est définie sur **true**.
- L’utilisateur n’a pas spécifié un **WhatIf = true** indicateur.

Enfin, n’oubliez pas d’appeler la cible. Dans le *Publish.proj* fichier, faire cela, ajoutez la cible à la liste des dépendances pour la valeur par défaut **FullPublish** cible. Vous devez vous assurer que le **DeployTestDBPermissions** cible n’est pas exécutée tant que la **PublishDbPackages** cible a été exécutée.


[!code-xml[Main](deploying-database-role-memberships-to-test-environments/samples/sample5.xml)]


## <a name="conclusion"></a>Conclusion

Cette rubrique décrit une façon dans laquelle vous pouvez ajouter des utilisateurs de base de données et les appartenances aux rôles comme une action de post-déploiement lorsque vous déployez un projet de base de données. Cela est généralement utile lorsque vous recréer régulièrement une base de données dans un environnement de test, mais il doit généralement être évitée lorsque vous déployez des bases de données dans les environnements de production ou intermédiaire. Par conséquent, vous devez vous assurer que vous utilisez la logique conditionnelle nécessaire afin que les utilisateurs de base de données et les appartenances aux rôles sont créés uniquement lorsqu’il convient de le faire.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur l’utilisation de VSDBCMD pour déployer des projets de base de données, consultez [déploiement de projets de base de données](../web-deployment-in-the-enterprise/deploying-database-projects.md). Pour obtenir des conseils sur la personnalisation des déploiements de base de données pour les environnements cibles différentes, consultez [personnalisation des déploiements de base de données pour plusieurs environnements](customizing-database-deployments-for-multiple-environments.md). Pour plus d’informations sur l’utilisation des fichiers projet MSBuild personnalisés pour contrôler le processus de déploiement, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md) et [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md). Pour plus d’informations sur les options de ligne de commande sqlcmd, consultez [utilitaire sqlcmd](https://msdn.microsoft.com/en-us/library/ms162773.aspx).

>[!div class="step-by-step"]
[Précédent](customizing-database-deployments-for-multiple-environments.md)
[Suivant](deploying-membership-databases-to-enterprise-environments.md)
