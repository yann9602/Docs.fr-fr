---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-database-projects
title: "Déploiement de projets de base de données | Documents Microsoft"
author: jrjlee
description: "Remarque : Dans de nombreux scénarios de déploiement d’entreprise, vous devez la possibilité de publier des mises à jour incrémentielles dans une base de données déployée. L’alternative consiste à recréer..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 832f226a-1aa3-4093-8c29-ce4196793259
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-database-projects
msc.type: authoredcontent
ms.openlocfilehash: aef8229f2920bd026e3dbf063afb57cffb9b21d0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-database-projects"></a>Déploiement de projets de base de données
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> [!NOTE]
> Dans de nombreux scénarios de déploiement d’entreprise, vous devez la possibilité de publier des mises à jour incrémentielles dans une base de données déployée. L’alternative consiste à recréer la base de données sur chaque déploiement, ce qui signifie que vous perdez toutes les données dans la base de données existante. Lorsque vous travaillez avec Visual Studio 2010, à l’aide de VSDBCMD est l’approche recommandée pour la publication incrémentielle de la base de données. Toutefois, la prochaine version de Visual Studio et le Pipeline de publication Web (WPP) inclut des outils qui prend en charge la publication incrémentielle de directement.


Si vous ouvrez l’exemple de gestionnaire de contacts solution dans Visual Studio 2010, vous verrez que le projet de base de données inclut un dossier de propriétés qui contient quatre fichiers.

![](deploying-database-projects/_static/image1.png)

Avec le fichier projet (*ContactManager.Database.dbproj* dans ce cas), ces fichiers contrôlent divers aspects du processus de génération et de déploiement :

- Le *Database.sqlcmdvars* fichier fournit les valeurs des variables SQLCMD que vous utilisez lorsque vous déployez le projet. Chaque configuration de solution (par exemple, debug et release) pouvez spécifier un fichier .sqlcmdvars différent.
- Le *Database.sqldeployment* fichier fournit des paramètres spécifiques au déploiement, comme s’il faut utiliser le classement défini dans votre projet ou le classement du serveur de destination, s’il faut recréer la base de données de destination chaque l’heure ou simplement modifier la base de données existante pour mettre à jour et ainsi de suite. Chaque configuration de solution peut spécifier un fichier de .sqldeployment différents.
- Le *Database.sqlpermissions* fichier est un document XML que vous pouvez utiliser pour définir des autorisations que vous souhaitez ajouter à la base de données cible. Toutes les configurations de solution partagent le même fichier .sqlpermissions.
- Le *Database.sqlsettings* fichier spécifie les propriétés de niveau base de données à utiliser lors de la création de la base de données, telles que le classement à utiliser, le comportement des opérateurs de comparaison et ainsi de suite. Toutes les configurations de solution partagent le même fichier .sqlsettings.

Il est important de prendre un moment pour ouvrir ces fichiers dans Visual Studio et vous familiariser avec le contenu.

Lorsque vous générez un projet de base de données, le processus de génération crée deux fichiers :

- A *schéma de la base de données* (fichier .dbschema). Cette section décrit le schéma de la base de données que vous voulez créer au format XML.
- A *manifeste de déploiement* (fichier .deploymanifest). Contient toutes les informations requises pour créer et déployer votre base de données. Il fait référence au fichier de .dbschema ainsi que d’autres ressources, telles que les instructions de déploiement (fichier .sqldeployment) et tous les scripts SQL de prédéploiement ou de post-déploiement.

Cet exemple montre la relation entre ces ressources :

![](deploying-database-projects/_static/image2.png)

Comme vous pouvez le voir, le fichier .sqlsettings et le fichier .sqlpermissions sont entrées dans le processus de génération. En même temps que le fichier de projet de base de données, ces fichiers sont utilisés pour créer le fichier de schéma de base de données. Le fichier .sqldeployment et le fichier .sqlcmdvars passent par le processus de génération inchangé. Le manifeste de déploiement indique l’emplacement du schéma de la base de données, le fichier .sqldeployment, le fichier .sqlcmdvars et tous les scripts SQL de prédéploiement ou de post-déploiement.

## <a name="why-use-vsdbcmd-to-deploy-a-database-project"></a>Pourquoi utiliser VSDBCMD pour déployer un projet de base de données ?

Il existe différentes approches différentes pour le déploiement de projets de base de données. Toutefois, tous les conviennent pour le déploiement d’un projet de base de données sur des serveurs distants dans un environnement d’entreprise. Envisagez ce que vous souhaitez à partir d’un déploiement de projet de base de données. Dans les scénarios de déploiement d’entreprise, vous êtes susceptible de vouloir :

- La possibilité de déployer le projet de base de données à partir d’un emplacement distant.
- Possibilité d’effectuer des mises à jour incrémentielles dans une base de données existante.
- La possibilité d’inclure des scripts de prédéploiement ou post-déploiement.
- La possibilité de personnaliser le déploiement dans plusieurs environnements de destination.
- La possibilité de déployer le projet de base de données en tant que partie d’une valeur plus élevée, en général, un script, déploiement de solutions de la seule étape.

Il existe trois approches principales que vous pouvez utiliser pour déployer un projet de base de données :

- Vous pouvez utiliser la fonctionnalité de déploiement avec le type de projet de base de données dans Visual Studio 2010. Lorsque vous générez et déployez un projet de base de données dans Visual Studio 2010, le processus de déploiement utilise le manifeste de déploiement pour générer un fichier de déploiement basé sur SQL spécifique à la configuration de build. Cela crée la base de données si elle n’existent déjà ou apportez les modifications nécessaires à la base de données s’il n’existe pas déjà. Vous pouvez également utiliser SQLCMD.exe pour exécuter ce fichier sur votre serveur de destination, ou vous pouvez définir Visual Studio pour créer et exécuter le fichier. L’inconvénient de cette approche est que vous avez uniquement un contrôle limité sur les paramètres de déploiement. Vous devrez souvent également modifier le fichier de déploiement de SQL pour fournir les valeurs des variables spécifiques à l’environnement. Vous pouvez uniquement utiliser cette approche basée sur un ordinateur avec Visual Studio 2010 est installé et que le développeur doit connaître et fournir des informations d’identification et des chaînes de connexion pour tous les environnements de destination.
- Vous pouvez utiliser l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy) pour [déployer une base de données dans le cadre d’un projet d’application web](https://msdn.microsoft.com/en-us/library/dd465343.aspx). Toutefois, cette approche est beaucoup plus complexe si vous souhaitez déployer un projet de base de données au lieu de simplement répliquer une base de données local existant sur un serveur de destination. Vous pouvez configurer Web Deploy pour exécuter le script de déploiement SQL générés par le projet de base de données, mais pour ce faire, vous devez créer un fichier de cibles WPP personnalisé pour votre projet d’application web. Cela ajoute une quantité substantielle de complexité au processus de déploiement. En outre, Web Deploy ne pas directement en charge les mises à jour incrémentielles de bases de données existantes. Pour plus d’informations sur cette approche, consultez [étendre le Pipeline de publication Web au projet de base de données de package déployé fichier SQL](https://go.microsoft.com/?linkid=9805121).
- Vous pouvez utiliser l’utilitaire VSDBCMD pour déployer la base de données à l’aide du schéma de base de données ou le manifeste de déploiement. Vous pouvez appeler VSDBCMD.exe à partir d’une cible de MSBuild, ce qui vous permet de publier des bases de données dans le cadre d’un processus de déploiement plus grande, sous forme de script. Vous pouvez remplacer les variables dans votre fichier .sqlcmdvars et les lots d’autres propriétés de base de données à partir d’une commande VSDBCMD, ce qui vous permet de personnaliser votre déploiement pour différents environnements sans créer plusieurs configurations de build. VSDBCMD fournit des fonctionnalités de différenciation, ce qui signifie que les modifications apportées uniquement le nécessaire pour aligner une base de données de destination avec votre schéma de base de données. VSDBCMD propose également un large éventail d’options de ligne de commande, ce qui vous donne un contrôle affiné sur le processus de déploiement.

À partir de cette vue d’ensemble, vous pouvez voir que l’utilisation de VSDBCMD MSBuild est l’approche la mieux adaptée à un scénario de déploiement standard de l’entreprise :

|  | Visual Studio 2010 | Web Deploy 2.0 | VSDBCMD.exe |
| --- | --- | --- | --- |
| Prend en charge le déploiement à distance ? | Oui | Oui | Oui |
| Prend en charge les mises à jour incrémentielles ? | Oui | Non | Oui |
| Prend en charge les scripts de pré/post-déploiement ? | Oui | Oui | Oui |
| Prend en charge plusieurs environnements et déploiement ? | Limité | Limité | Oui |
| Prend en charge de scripts de déploiement ? | Limité | Oui | Oui |

Le reste de cette rubrique décrit l’utilisation de VSDBCMD avec MSBuild pour déployer des projets de base de données.

## <a name="understanding-the-deployment-process"></a>Comprendre le processus de déploiement

L’utilitaire VSDBCMD vous permet de déployer une base de données dans le schéma de base de données (fichier .dbschema) ou le manifeste de déploiement (fichier .deploymanifest). Dans la pratique, vous utiliserez presque toujours le manifeste de déploiement, comme le manifeste de déploiement vous permet de fournir des valeurs par défaut pour diverses propriétés de déploiement et d’identifier tous les scripts SQL prédéploiement ou de post-déploiement à exécuter. Par exemple, cette commande VSDBCMD permet de déployer le **ContactManager** base de données à un serveur de base de données dans un environnement de test :


[!code-console[Main](deploying-database-projects/samples/sample1.cmd)]


Dans ce cas :

- Le **/a** (ou **/action**) commutateur spécifie ce que vous souhaitez faire VSDBCMD. Vous pouvez définir cette option **importation** ou **déployer**. Le **importation** option est utilisée pour générer un fichier .dbschema à partir d’une base de données existante et la **déployer** option permet de déployer un fichier .dbschema vers une base de données cible.
- Le **de manifeste** (ou **MANIFESTFILE**) commutateur identifie le fichier .deploymanifest que vous souhaitez déployer. Si vous souhaitez utiliser le fichier .dbschema au lieu de cela, vous devez utiliser le **/modèle** (ou **/ModelFile**) basculer.
- Le **/cs** (ou **/ConnectionString**) commutateur fournit la chaîne de connexion pour le serveur de base de données cible. Notez que cela n’inclut pas le nom de la base de données & #x 2014 ; VSDBCMD doit se connecter au serveur pour créer la base de données ; Il n’a pas besoin de se connecter à une base de données individuel. Si votre fichier .deploymanifest inclut une chaîne de connexion, vous pouvez omettre ce commutateur. Si vous utilisez le commutateur tout de même, la valeur du commutateur remplace la valeur .deploymanifest.
- Le **/p : TargetDatabase** fournit le nom que vous souhaitez affecter à la base de données cible lors de la création. Cela remplace la valeur de la **TargetDatabase** propriété dans le fichier .deploymanifest. Vous pouvez utiliser la **/p:** *[nom de propriété]*selon la syntaxe pour définir un large éventail de propriétés de déploiement pour remplacer des variables SQLCMD déclaré dans votre fichier .sqlcmdvars.
- Le **/dd+** (ou **/DeployToDatabase+**) commutateur indique que vous souhaitez créer un déploiement et le déployer dans l’environnement cible. Si vous spécifiez **/dd-**, ou omettez le commutateur, VSDBCMD génère un script de déploiement, mais il déploiera pas à l’environnement cible. Ce commutateur est souvent la source de confusion et est expliqué plus en détail dans la section suivante.
- Le **/script** (ou **/DeploymentScriptFile**) commutateur spécifie où vous souhaitez générer le script de déploiement. Cette valeur n’affecte pas le processus de déploiement.

Pour plus d’informations sur VSDBCMD, consultez [référence de ligne de commande pour VSDBCMD. EXE (déploiement et importation de schéma)](https://msdn.microsoft.com/en-us/library/dd193283.aspx) et [Comment : préparer une base de données pour le déploiement à partir d’une invite de commandes à l’aide de VSDBCMD. EXE](https://msdn.microsoft.com/en-us/library/dd193258.aspx).

Pour obtenir un exemple de comment vous pouvez utiliser VSDBCMD à partir d’un fichier projet MSBuild, consultez [comprendre le processus de génération](understanding-the-build-process.md). Pour obtenir des exemples montrant comment configurer les paramètres de déploiement de base de données pour plusieurs environnements, consultez [personnalisation des déploiements de base de données pour plusieurs environnements](../advanced-enterprise-web-deployment/customizing-database-deployments-for-multiple-environments.md).

## <a name="understanding-the-deploytodatabase-switch"></a>Présentation du commutateur DeployToDatabase

Le comportement de la **/dd** ou **/DeployToDatabase** commutateur varie selon que vous utilisez VSDBCMD avec un fichier .dbschema ou un fichier .deploymanifest. Si vous utilisez un fichier .dbschema, le comportement est assez simple :

- Si vous spécifiez **/dd+** ou **/dd**, VSDBCMD génère un script de déploiement et déployer la base de données.
- Si vous spécifiez **/dd-** ou omettez le commutateur, VSDBCMD génère un script de déploiement uniquement.

Si vous utilisez un fichier .deploymanifest, le comportement est beaucoup plus complexe. Il s’agit, car le fichier .deploymanifest contient un nom de propriété **DeployToDatabase** qui détermine également si la base de données est déployée.


[!code-xml[Main](deploying-database-projects/samples/sample2.xml)]


La valeur de cette propriété est définie en fonction des propriétés du projet de base de données. Si vous définissez la **action de déploiement** à **créer un script de déploiement (.sql)**, la valeur sera **False**. Si vous définissez la **action de déploiement** à **créer un script de déploiement (.sql) et déployer la base de données**, la valeur sera **True**.

> [!NOTE]
> Ces paramètres associés à une plate-forme et la configuration de build spécifique. Par exemple, si vous configurez les paramètres pour le **déboguer** configuration, puis publiez à l’aide de la **version** configuration, vos paramètres ne seront pas utilisées.


![](deploying-database-projects/_static/image3.png)

> [!NOTE]
> Dans ce scénario, le **action de déploiement** doit toujours être défini sur **créer un script de déploiement (.sql)**, car vous ne souhaitez pas que Visual Studio 2010 pour déployer votre base de données. En d’autres termes, le **DeployToDatabase** propriété doit toujours être **False**.


Lorsqu’un **DeployToDatabase** propriété est spécifiée, la **/dd** commutateur remplace uniquement la propriété si la valeur de propriété est **false**:

- Si le **DeployToDatabase** propriété **False**, et que vous spécifiez **/dd+** ou **/dd**, VSDBCMD remplace le  **DeployToDatabase** propriété et déployer la base de données.
- Si le **DeployToDatabase** propriété **False**, et que vous spécifiez **/dd-** ou omettez le commutateur, VSDBCMD ne déploiera pas de la base de données.
- Si le **DeployToDatabase** propriété **True**, VSDBCMD ignore le commutateur et déployer la base de données.
- Un script de déploiement est généré dans chaque cas, indépendamment de si vous déployez la base de données.

## <a name="conclusion"></a>Conclusion

Cette rubrique fourni une vue d’ensemble du processus de génération et de déploiement pour les projets de base de données dans Visual Studio 2010. Il décrit également comment vous pouvez utiliser VSDBCMD.exe avec MSBuild pour prendre en charge le déploiement de base de données de l’échelle de l’entreprise.

Pour plus d’informations sur le fonctionnement en pratique, consultez [personnalisation des déploiements de base de données pour plusieurs environnements](../advanced-enterprise-web-deployment/customizing-database-deployments-for-multiple-environments.md).

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur la personnalisation des déploiements de la base de données en créant un fichier de configuration de déploiement distinct pour chaque environnement, consultez [personnalisation des déploiements de base de données pour plusieurs environnements](../advanced-enterprise-web-deployment/customizing-database-deployments-for-multiple-environments.md). Pour obtenir des conseils sur la façon de configurer les appartenances aux rôles de base de données en exécutant un script de post-déploiement, consultez [appartenances de rôle de base de données de déploiement pour les environnements de Test](../advanced-enterprise-web-deployment/deploying-database-role-memberships-to-test-environments.md). Pour obtenir des conseils sur la gestion des défis uniques cette appartenance bases de données imposent, consultez [déploiement de bases de données d’appartenance pour les environnements d’entreprise](../advanced-enterprise-web-deployment/deploying-membership-databases-to-enterprise-environments.md).

Ces rubriques sur MSDN fournissent des conseils plus large et des informations générales sur les projets de base de données de Visual Studio et le processus de déploiement de base de données :

- [Projets de base de données Visual Studio 2010 SQL Server](https://msdn.microsoft.com/en-us/library/ff678491.aspx)
- [Gestion des modifications de la base de données](https://msdn.microsoft.com/en-us/library/aa833404.aspx)
- [Comment : préparer une base de données pour le déploiement à partir d’une invite de commandes à l’aide de VSDBCMD. EXE](https://msdn.microsoft.com/en-us/library/dd193258.aspx)
- [Une vue d’ensemble de la génération de la base de données et de déploiement](https://msdn.microsoft.com/en-us/library/aa833165.aspx)

>[!div class="step-by-step"]
[Précédent](deploying-web-packages.md)
[Suivant](creating-and-running-a-deployment-command-file.md)
