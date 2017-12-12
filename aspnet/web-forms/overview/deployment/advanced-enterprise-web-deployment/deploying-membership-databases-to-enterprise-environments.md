---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-membership-databases-to-enterprise-environments
title: "Déploiement de bases de données d’appartenance pour les environnements d’entreprise | Documents Microsoft"
author: jrjlee
description: "Cette rubrique explique les principales considérations et les défis que vous devrez résoudre lorsque vous configurez des bases de données ASP.NET application services (plus fréquent..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 3cf765df-d311-4f68-a295-c9685ceea830
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/deploying-membership-databases-to-enterprise-environments
msc.type: authoredcontent
ms.openlocfilehash: f4d898b6e09b5b9df44b62f9cb4b9d367f288efb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-membership-databases-to-enterprise-environments"></a>Déploiement de bases de données d’appartenance pour les environnements d’entreprise
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique explique les principales considérations et les défis que vous devrez résoudre lorsque les bases de données (plus communément appelé bases de données d’appartenance) dans des environnements de test, intermédiaire ou de production de services de configurer les applications ASP.NET. Elle décrit également les approches que vous permettent de relever ces défis.


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="what-are-the-issues-when-you-deploy-a-membership-database"></a>Quels sont les problèmes lorsque vous déployez une base de données d’appartenance ?

Dans la plupart des cas, lorsque vous concevez une stratégie de déploiement pour une base de données, la première chose que vous devez tenir compte est les données que vous souhaitez déployer. Dans un environnement de développement ou de test, vous souhaiterez déployer des données de compte d’utilisateur pour faciliter le test de rapidement et facilement. Dans un environnement intermédiaire ou de production, il est peu probable que vous pouvez déployer des données de compte d’utilisateur.

Malheureusement, les bases de données de l’appartenance ASP.NET présentent des défis spécifiques que vous prenez cette décision est beaucoup plus complexe :

- Un déploiement de schéma uniquement laisse la base de données d’appartenance dans un état non opérationnel. Il s’agit, car la base de données d’appartenance inclut certaines données de configuration (dans le **aspnet\_SchemaVersions** table) que la base de données requiert pour fonctionner. Par conséquent, si vous effectuez un déploiement de schéma uniquement de votre base de données d’appartenance pour exclure les données de compte d’utilisateur, vous devez exécuter un script de post-déploiement pour ajouter les données de configuration essentielles.
- Selon la configuration de votre base de données d’appartenance, le fournisseur d’appartenances peut utiliser la clé d’ordinateur pour chiffrer des mots de passe et les stocker dans la base de données. Dans ce cas, toutes les données de compte utilisateur que vous déployez la base de données ne seront plus utilisable sur le serveur de destination. Pour cette raison, il n’est pas un scénario pris en charge de déploiement de données de compte d’utilisateur.

## <a name="choosing-a-membership-database-strategy"></a>Choix d’une stratégie de base de données d’appartenance

Utilisez ces instructions lorsque vous choisissez la configuration d’une base de données d’appartenance dans un environnement de serveur d’entreprise :

- Dans la mesure du possible, ne déployez pas les bases de données d’appartenance. Au lieu de cela, créez la base de données d’appartenance manuellement sur le serveur de base de données cible. Si vous n’avez pas personnalisé votre schéma de base de données d’abonnement, vous pouvez simplement créer un nouveau sur place à la destination à l’aide de la [ASP.NET SQL Server Registration Tool (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx).
- Si vous ne disposez d’aucune option mais sur déployer une base de données d’appartenance & le #x 2014 ; par exemple, si vous avez apporté des modifications importantes au schéma de base de données & #x 2014 ; vous devez effectuer un déploiement de schéma uniquement de la base de données d’appartenance pour exclure les données de compte d’utilisateur, et puis exécuter un script de post-déploiement pour ajouter des données de configuration requis. Vous trouverez des instructions générales sur ces approches dans [Comment : déployer les ASP.NET d’appartenance de base de données sans y compris les comptes d’utilisateur](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx).

Il est important de se rappeler que *le schéma de votre base de données d’appartenance est susceptible d’être relativement statique*. Même si vous avez personnalisé la base de données d’appartenance, il est peu probable que vous aurez besoin mettre à jour le schéma sur un répertoire régulièrement & #x 2014 ; il ne va pas être modifiés avec la même fréquence que le code dans une application web ou un projet de base de données. Par conséquent, vous devez ne doit pas inclure la base de données d’appartenance dans les processus de déploiement automatisé ou seule étape.

## <a name="using-vsdbcmd-to-update-a-membership-database-schema"></a>À l’aide de VSDBCMD pour mettre à jour un schéma de base de données d’appartenance

Si vous modifiez la structure de votre base de données d’appartenance après le premier déploiement, il pouvez que vous ne souhaitez pas l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) permet de redéployer la base de données. La fonctionnalité de déploiement de base de données de Web Deploy n’inclut pas la possibilité d’apporter des mises à jour différentielles à une base de données de destination et le #x 2014 ; au lieu de cela, Web Deploy doit supprimer et recréer la base de données. Cela signifie que vous perdez les données de compte utilisateur existantes, ce qui ne sont généralement pas souhaitables dans des environnements de production ou intermédiaire.

L’alternative consiste à utiliser l’utilitaire VSDBCMD pour mettre à jour le schéma de votre base de données de destination. VSDBCMD comprend deux fonctionnalités importantes. Tout d’abord, il peut importer le schéma de base de données existante dans un fichier .dbschema. En second lieu, il peut déployer un fichier .dbschema vers une base de données existante en tant que mise à jour différentiel, ce qui signifie que les modifications requises pour mettre la base de données à jour ne s’et vous ne perdez pas de données.

Vous pouvez utiliser ces étapes pour mettre à jour un schéma de base de données d’appartenance :

1. Utilisez l’outil VSDBCMD **importation** action pour générer un fichier .dbschema pour votre base de données source d’appartenance. Cette procédure est décrite dans [Comment : importer un schéma à partir d’une invite de commandes](https://msdn.microsoft.com/en-us/library/dd172135.aspx).
2. Utilisez l’outil VSDBCMD **déployer** action pour déployer le fichier .dbschema à votre base de données d’appartenance de destination. Cette procédure est décrite dans [référence de ligne de commande pour VSDBCMD. EXE (déploiement et importation de schéma)](https://msdn.microsoft.com/en-us/library/dd193283.aspx).

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit les défis que vous pouvez rencontrer lorsque vous avez besoin configurer les bases de données de l’appartenance ASP.NET dans les environnements cibles différents. En particulier, il explique pourquoi les déploiements de schéma uniquement laisser la base de données d’appartenance dans un état non opérationnel et pourquoi déployer des données de compte d’utilisateur n'est pas pris en charge. La rubrique a présenté également des conseils sur la façon de configurer, déployer et mettre à jour des bases de données d’appartenance dans différents scénarios.

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’instructions et des exemples montrant comment utiliser VSDBCMD, consultez [référence de ligne de commande pour VSDBCMD. EXE (déploiement et importation de schéma)](https://msdn.microsoft.com/en-us/library/dd193283.aspx) et [Comment : importer un schéma à partir d’une invite de commandes](https://msdn.microsoft.com/en-us/library/dd172135.aspx). Pour plus d’informations sur l’utilisation d’aspnet\_regsql.exe pour créer des bases de données d’appartenance, consultez [ASP.NET SQL Server Registration Tool (aspnet\_regsql.exe)](https://msdn.microsoft.com/en-us/library/ms229862(v=vs.100).aspx). Pour obtenir des instructions plus générales sur le déploiement de bases de données d’appartenance, consultez [Comment : déployer les ASP.NET d’appartenance de base de données sans y compris les comptes d’utilisateur](https://msdn.microsoft.com/en-us/library/ff361972(v=vs.100).aspx).

>[!div class="step-by-step"]
[Précédent](deploying-database-role-memberships-to-test-environments.md)
[Suivant](excluding-files-and-folders-from-deployment.md)
