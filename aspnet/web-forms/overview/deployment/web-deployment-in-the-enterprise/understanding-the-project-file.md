---
uid: web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file
title: "Présentation du fichier de projet | Documents Microsoft"
author: jrjlee
description: "Fichiers de projet Microsoft Build Engine (MSBuild) se trouvent au cœur du processus de génération et de déploiement. Cette rubrique commence par une vue d’ensemble conceptuelle de MSBuild..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 07978d9d-341c-4524-bcba-62976f390f77
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file
msc.type: authoredcontent
ms.openlocfilehash: 8d0f9604529db9cf4ee5d333450a551e46e6ba4f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-the-project-file"></a>Présentation du fichier de projet
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Fichiers de projet Microsoft Build Engine (MSBuild) se trouvent au cœur du processus de génération et de déploiement. Cette rubrique commence par une vue d’ensemble conceptuelle de MSBuild et le fichier projet. Il décrit les composants clés que vous rencontrerez lorsque vous travaillez avec des fichiers projet, et il fonctionne via un exemple de comment vous pouvez utiliser des fichiers projet pour déployer des applications du monde réel.
> 
> Ce que vous allez apprendre :
> 
> - Comment MSBuild utilise des fichiers projet MSBuild pour générer des projets.
> - Comment MSBuild s’intègre avec les technologies de déploiement, telles que l’outil de déploiement Web Internet Information Services (IIS) (Web Deploy).
> - Comment comprendre les composants clés d’un fichier projet.
> - Comment vous pouvez utiliser des fichiers de projet pour créer et déployer des applications complexes.


## <a name="msbuild-and-the-project-file"></a>MSBuild et le fichier projet

Lorsque vous créez et créer des solutions dans Visual Studio, Visual Studio utilise MSBuild pour générer chaque projet dans votre solution. Chaque projet Visual Studio inclut un fichier de projet MSBuild, avec une extension de fichier qui reflète le type de projet & #x 2014 ; par exemple, un projet c# (.csproj), un projet Visual Basic.NET (.vbproj) ou un projet de base de données (.dbproj). Pour générer un projet, MSBuild doit traiter le fichier de projet associé au projet. Le fichier projet est un document XML qui contient toutes les informations et les instructions que MSBuild a besoin pour générer votre projet, telles que le contenu à inclure, configuration requise, les informations de contrôle de version, serveur web ou les paramètres de serveur de base de données et le tâches qui doivent être effectuées.

Fichiers projet MSBuild sont basées sur les [schéma XML MSBuild](https://msdn.microsoft.com/en-us/library/5dy88c2e.aspx), et ainsi le processus de génération est entièrement ouvert et transparent. En outre, vous n’avez pas besoin d’installer Visual Studio pour pouvoir utiliser le moteur MSBuild & #x 2014 ; l’exécutable de MSBuild.exe fait partie du .NET Framework, et vous pouvez l’exécuter à partir d’une invite de commandes. En tant que développeur, vous pouvez créer vos propres fichiers projet MSBuild, en à l’aide du schéma XML MSBuild, pour garder le contrôle sophistiqué et affiné comment vos projets sont générés et déployés. Ces fichiers de projet personnalisés fonctionnent dans la même façon que les fichiers de projet Visual Studio génère automatiquement.

> [!NOTE]
> Vous pouvez également utiliser des fichiers projet MSBuild avec le service de Build d’équipe dans Team Foundation Server (TFS). Par exemple, vous pouvez utiliser les fichiers de projet dans les scénarios d’intégration continue (CI) pour automatiser le déploiement dans un environnement de test lorsque le nouveau code est archivé. Pour plus d’informations, consultez [configuration Team Foundation Server pour le déploiement Web automatisé](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md).


### <a name="project-file-naming-conventions"></a>Conventions d’affectation de noms de fichier de projet

Lorsque vous créez vos propres fichiers de projet, vous pouvez utiliser toute extension de fichier que vous le souhaitez. Toutefois, pour rendre vos solutions plus facile à comprendre, vous devez utiliser ces conventions courantes :

- Utilisez l’extension .proj lorsque vous créez un fichier de projet qui génère des projets.
- Utilisez l’extension .targets lorsque vous créez un fichier de projet réutilisable à importer dans d’autres fichiers de projet. Fichiers portant l’extension .targets en général, ne créez pas de quoi que ce soit elles-mêmes, qu’ils contiennent simplement les instructions que vous pouvez importer dans vos fichiers .proj.

### <a name="integration-with-deployment-technologies"></a>Intégration avec les Technologies de déploiement

Si vous avez travaillé avec des projets d’application web dans Visual Studio 2010, telles que les applications web ASP.NET et les applications web ASP.NET MVC, vous savez que ces projets incluent une prise en charge intégrée pour l’empaquetage et déploiement de l’application web pour un environnement cible. Le **propriétés** pour ces projets incluent des pages **Package/Publication Web** et **Package/Publication SQL** onglets que vous pouvez utiliser pour configurer comment les composants de votre application sont empaquetées et déployées. Cet exemple montre la **Package/Publication Web** onglet :

![](understanding-the-project-file/_static/image1.png)

Ces fonctionnalités de la technologie sous-jacente est connue en tant que le Pipeline de publication Web (WPP). Les fournisseurs de services met essentiellement MSBuild et [Web Deploy](https://go.microsoft.com/?linkid=9805122) ensemble pour fournir un processus de génération, déploiement du package et complète pour vos applications web.

La bonne nouvelle est que vous pouvez tirer parti des points d’intégration qui fournit de WPP lorsque vous créez des fichiers de projet personnalisés pour les projets web. Vous pouvez inclure des instructions de déploiement dans votre fichier projet, ce qui permet de créer vos projets, créer des packages de déploiement web et installer ces packages sur des serveurs distants via un seul fichier de projet et un seul appel à MSBuild. Vous pouvez également appeler toutes les autres fichiers exécutables dans le cadre de votre processus de génération. Par exemple, vous pouvez exécuter l’outil de ligne de commande VSDBCMD.exe pour déployer une base de données à partir d’un fichier de schéma. Au cours de cette rubrique, vous verrez comment vous pouvez tirer parti de ces fonctionnalités pour répondre aux exigences de vos scénarios de déploiement d’entreprise.

> [!NOTE]
> Pour plus d’informations sur la façon dont le processus de déploiement d’application web, consultez [vue d’ensemble du déploiement de projet d’Application ASP.NET Web](https://msdn.microsoft.com/en-us/library/dd394698.aspx).


## <a name="the-anatomy-of-a-project-file"></a>Anatomie d’un fichier de projet

Avant d’examiner plus en détail le processus de génération, il est important de prendre quelques instants pour vous familiariser avec la structure de base d’un fichier de projet MSBuild. Cette section fournit une vue d’ensemble des éléments plus communs que vous pouvez rencontrer lorsque vous passez en revue, modifiez ou créez un fichier projet. En particulier, vous apprendrez à :

- L’utilisation de *propriétés* pour gérer les variables pour le processus de génération.
- L’utilisation de *éléments* pour identifier les entrées dans le processus de génération, comme les fichiers de code.
- L’utilisation de *cibles* et *tâches* pour fournir des instructions d’exécution à MSBuild, à l’aide de *propriétés* et *éléments* définie ailleurs dans le fichier projet.

Cet exemple montre la relation entre les éléments clés dans un fichier projet MSBuild :

![](understanding-the-project-file/_static/image2.png)

### <a name="the-project-element"></a>L’élément de projet

Le [projet](https://msdn.microsoft.com/en-us/library/bcxfsh87.aspx) élément est l’élément racine de chaque fichier de projet. Non seulement identifier le schéma XML pour le fichier projet, le **projet** élément peut inclure des attributs pour spécifier les points d’entrée pour le processus de génération. Par exemple, dans le [exemple de solution de gestionnaire de contacts](the-contact-manager-solution.md), le *Publish.proj* fichier Spécifie que la génération doit commencer par l’appel de la cible nommée **FullPublish**.


[!code-xml[Main](understanding-the-project-file/samples/sample1.xml)]


### <a name="properties-and-conditions"></a>Propriétés et des Conditions

Un fichier projet doit généralement fournir un grand nombre d’éléments d’information pour pouvoir générer et déployer vos projets différents. Ces éléments d’information peut inclure des noms de serveur, les chaînes de connexion, informations d’identification, les configurations de build, chemins d’accès des fichiers source et de destination et toute autre information que vous souhaitez inclure pour prendre en charge la personnalisation. Dans un fichier de projet, les propriétés doivent être définies dans un [PropertyGroup](https://msdn.microsoft.com/en-us/library/t4w159bs.aspx) élément. Propriétés MSBuild sont constitués de paires clé-valeur. Dans le **PropertyGroup** élément, le nom d’élément définit la clé de propriété et le contenu de l’élément définit la valeur de propriété. Par exemple, vous pouvez définir des propriétés nommées **nom_serveur** et **ConnectionString** pour stocker une chaîne de nom et une connexion de serveur statique.


[!code-xml[Main](understanding-the-project-file/samples/sample2.xml)]


Pour récupérer une valeur de propriété, vous utilisez le format **$(***PropertyName***)***.* Par exemple, pour récupérer la valeur de la **nom_serveur** propriété, tapez :


[!code-powershell[Main](understanding-the-project-file/samples/sample3.ps1)]


> [!NOTE]
> Vous verrez des exemples d’et quand utiliser les valeurs de propriété plus loin dans cette rubrique.


Incorporation d’informations en tant que propriétés statiques dans un fichier projet n’est pas toujours l’approche idéale pour gérer le processus de génération. Dans de nombreux scénarios, que vous souhaitez obtenir les informations provenant d’autres sources ou de répondre aux besoins de l’utilisateur à fournir les informations à partir de l’invite de commandes. MSBuild vous permet de spécifier n’importe quelle valeur de propriété comme un paramètre de ligne de commande. Par exemple, l’utilisateur peut fournir une valeur pour **nom_serveur** quand il ou elle exécute MSBuild.exe à partir de la ligne de commande.


[!code-console[Main](understanding-the-project-file/samples/sample4.cmd)]


> [!NOTE]
> Pour plus d’informations sur les arguments et les commutateurs que vous pouvez utiliser avec MSBuild.exe, consultez [référence de ligne de commande MSBuild](https://msdn.microsoft.com/en-us/library/ms164311.aspx).


Vous pouvez utiliser la même syntaxe de propriété pour obtenir les valeurs des variables d’environnement et des propriétés de projet prédéfinis. Un grand nombre de propriétés couramment utilisées est défini pour vous, et vous pouvez les utiliser dans vos fichiers projet en incluant le nom du paramètre approprié. Par exemple, pour récupérer la plateforme de projet actuelle & #x 2014 ; par exemple, **x86** ou **AnyCpu**& #x 2014 ; vous pouvez inclure le **$(Platform)** référence de propriété dans votre fichier projet. Pour plus d’informations, consultez [Macros pour les commandes de génération et les propriétés](https://msdn.microsoft.com/en-us/library/c02as0cs.aspx), [propriétés communes des projets MSBuild](https://msdn.microsoft.com/en-us/library/bb629394.aspx), et [propriétés réservées](https://msdn.microsoft.com/en-us/library/ms164309.aspx).

Propriétés sont souvent utilisées en conjonction avec *conditions*. La plupart des éléments MSBuild prend en charge la **Condition** attribut, qui vous permet de spécifier les critères sur lesquels MSBuild doit évaluer l’élément. Par exemple, considérez cette définition de propriété :


[!code-xml[Main](understanding-the-project-file/samples/sample5.xml)]


Lorsque MSBuild traite cette définition de propriété, il vérifie si une **$(OutputRoot)** valeur de propriété n’est disponible. Si la valeur de propriété est vide & #x 2014 ; en d’autres termes, l’utilisateur n’a pas fourni une valeur pour cette propriété, & #x 2014 ; la condition a la valeur **true** et la valeur de propriété est définie sur **... \Publish\Out**. Si l’utilisateur a fourni une valeur pour cette propriété, la condition prend la valeur **false** et la valeur de propriété statique n’est pas utilisée.

Pour plus d’informations sur les différentes façons dans laquelle vous pouvez spécifier des conditions, consultez [Conditions MSBuild](https://msdn.microsoft.com/en-us/library/7szfhaft.aspx).

### <a name="items-and-item-groups"></a>Éléments et des groupes d’éléments

Un rôle important du fichier projet consiste à définir les entrées dans le processus de génération. En règle générale, ces entrées sont des fichiers & #x 2014 ; fichiers de code, les fichiers de configuration, les fichiers de commandes et tous les autres fichiers dont vous avez besoin à traiter ou à copier en tant que partie du processus de génération. Dans le schéma de projet MSBuild, ces entrées sont représentées par [élément](https://msdn.microsoft.com/en-us/library/ms164283.aspx) éléments. Dans un fichier de projet, les éléments doivent être définies dans un [ItemGroup](https://msdn.microsoft.com/en-us/library/646dk05y.aspx) élément. Tout comme **propriété** éléments, vous pouvez nommer un **élément** élément comme vous le souhaitez. Toutefois, vous devez spécifier un **Include** attribut pour identifier le fichier ou le caractère générique représentant l’élément.


[!code-xml[Main](understanding-the-project-file/samples/sample6.xml)]


En spécifiant plusieurs **élément** éléments portant le même nom, que vous créez en réalité une liste nommée des ressources. Une bonne solution pour voir cette opération est à consulter à l’intérieur d’un des fichiers du projet créé par Visual Studio. Par exemple, le *ContactManager.Mvc.csproj* le fichier dans l’exemple de solution inclut un grand nombre de groupes d’éléments, chacun avec plusieurs identique **élément** éléments.


[!code-xml[Main](understanding-the-project-file/samples/sample7.xml)]


De cette façon, le fichier projet est demandant MSBuild pour générer une liste des fichiers qui doivent être traités de la même façon & #x 2014 ; le **référence** liste inclut des assemblys qui doivent être en place pour une génération réussie, le **Compiler** liste inclut les fichiers de code qui doivent être compilés, et le **contenu** liste inclut des ressources qui doivent être copiés sans altération. Nous allons examiner comment le processus de génération référence et utilise ces éléments plus loin dans cette rubrique.

Les éléments item peuvent également inclure [ItemMetadata](https://msdn.microsoft.com/en-us/library/ms164284.aspx) des éléments enfants. Ceux-ci sont des paires clé-valeur définies par l’utilisateur et essentiellement représentent des propriétés qui sont spécifiques à cet élément. Par exemple, un grand nombre de la **compiler** incluent des éléments dans le fichier projet **DependentUpon** des éléments enfants.


[!code-xml[Main](understanding-the-project-file/samples/sample8.xml)]


> [!NOTE]
> En plus des métadonnées de l’élément créé par l’utilisateur, tous les éléments assignés à différentes métadonnées communes lors de la création. Pour plus d’informations, consultez l’article [Well-known Item Metadata (Métadonnées d’élément connues MSBuild)](https://msdn.microsoft.com/en-us/library/ms164313.aspx).


Vous pouvez créer **ItemGroup** éléments dans le niveau racine **projet** élément ou dans spécifiques **cible** éléments. **ItemGroup** éléments prennent également en charge **Condition** attributs, ce qui vous permet de personnaliser les entrées dans le processus de génération en fonction des conditions telles que la configuration de projet ou de la plateforme.

### <a name="targets-and-tasks"></a>Cibles et tâches

Dans le schéma MSBuild, un [tâche](https://msdn.microsoft.com/en-us/library/77f2hx1s.aspx) élément représente une instruction de build individuel (ou tâche). MSBuild inclut une multitude de tâches prédéfinies. Exemple :

- Le **copie** tâche copie des fichiers vers un nouvel emplacement.
- Le **Csc** tâche appelle le compilateur Visual c#.
- Le **Vbc** tâche appelle le compilateur Visual Basic.
- Le **Exec** tâche exécute un programme spécifié.
- Le **Message** tâche écrit un message dans un journal.

> [!NOTE]
> Pour obtenir des informations complètes sur les tâches qui sont disponibles à l’emploi, consultez [référence des tâches MSBuild](https://msdn.microsoft.com/en-us/library/7z253716.aspx). Pour plus d’informations sur les tâches, y compris comment créer vos propres tâches personnalisées, consultez [tâches MSBuild](https://msdn.microsoft.com/en-us/library/ms171466.aspx).


Tâches doivent toujours être contenues dans [cible](https://msdn.microsoft.com/en-us/library/t50z2hka.aspx) éléments. A **cible** élément est un ensemble d’une ou plusieurs tâches sont exécutées séquentiellement, et un fichier projet peut contenir plusieurs cibles. Lorsque vous souhaitez exécuter une tâche ou un ensemble de tâches, vous appelez la cible qui les contient. Par exemple, supposons que vous disposez d’un fichier de projet simple qui enregistre un message.


[!code-xml[Main](understanding-the-project-file/samples/sample9.xml)]


Vous pouvez appeler la cible à partir de la ligne de commande à l’aide de la **/t** commutateur pour spécifier la cible.


[!code-console[Main](understanding-the-project-file/samples/sample10.cmd)]


Vous pouvez également ajouter un **DefaultTargets** d’attribut pour le **projet** élément, pour spécifier les cibles que vous souhaitez appeler.


[!code-xml[Main](understanding-the-project-file/samples/sample11.xml)]


Dans ce cas, vous n’avez pas besoin de spécifier la cible à partir de la ligne de commande. Vous pouvez spécifier simplement le fichier projet et MSBuild appellera la **FullPublish** cible pour vous.


[!code-console[Main](understanding-the-project-file/samples/sample12.cmd)]


Les cibles et les tâches peuvent inclure **Condition** attributs. Par conséquent, vous pouvez choisir d’omettre les cibles entières ou des tâches individuelles si certaines conditions sont remplies.

En règle générale, lorsque vous créez des tâches utiles et cibles, vous devez faire référence aux propriétés et les éléments que vous avez définis ailleurs dans le fichier projet :

- Pour utiliser une valeur de propriété, tapez **$(***PropertyName***)**, où *PropertyName* est le nom de la **propriété** élément ou le nom du paramètre.
- Pour utiliser un élément, tapez **@(***ItemName***)**, où *ItemName* est le nom de la **élément** élément.

> [!NOTE]
> N’oubliez pas que si vous créez plusieurs éléments portant le même nom, vous créez une liste. En revanche, si vous créez plusieurs propriétés portant le même nom, la dernière valeur de propriété que vous fournissez remplace toutes les propriétés précédentes avec le même nom & #x 2014, une propriété peut contenir uniquement une valeur unique.


Par exemple, dans le *Publish.proj* de fichiers dans l’exemple de solution, examinons la **BuildProjects** cible.


[!code-xml[Main](understanding-the-project-file/samples/sample13.xml)]


Dans cet exemple, vous pouvez observer ces points clés :

- Si le **BuildingInTeamBuild** paramètre est spécifié et a la valeur **true**, aucune des tâches de cette cible sera exécuté.
- La cible contient une seule instance de la [MSBuild](https://msdn.microsoft.com/en-us/library/z7f65y0d.aspx) tâche. Cette tâche vous permet de générer des autres projets de MSBuild.
- Le **ProjectsToBuild** élément est passé à la tâche. Cet élément peut représenter une liste des fichiers projet ou solution, définis par **ProjectsToBuild** élément des éléments au sein d’un groupe d’éléments. Dans ce cas, le **ProjectsToBuild** élément fait référence à un fichier de solution unique.

    [!code-xml[Main](understanding-the-project-file/samples/sample14.xml)]
- Les valeurs de propriété passées à la **MSBuild** tâche inclut des paramètres nommés **OutputRoot** et **Configuration**. Si elles ne sont pas, ils sont définis pour les valeurs des paramètres si elles sont fournies, ou les valeurs de propriété statique.

    [!code-xml[Main](understanding-the-project-file/samples/sample15.xml)]

Vous pouvez également voir que le **MSBuild** tâche appelle une cible nommée **Build**. C’est une de plusieurs cibles intégrées qui sont largement utilisés dans les fichiers de projet Visual Studio et sont disponible dans vos fichiers de projet personnalisé, tel que **générer**, **Clean**, **reconstruire**, et **publier**. Vous en apprendrez davantage sur l’utilisation de cibles et des tâches pour contrôler le processus de génération, ainsi que sur le **MSBuild** de tâches, en particulier, plus loin dans cette rubrique.

> [!NOTE]
> Pour plus d’informations sur les cibles, consultez [cibles de MSBuild](https://msdn.microsoft.com/en-us/library/ms171462.aspx).


## <a name="splitting-project-files-to-support-multiple-environments"></a>Fractionner des fichiers de projet pour prendre en charge plusieurs environnements

Supposons que vous souhaitez être en mesure de déployer une solution dans plusieurs environnements, tels que les serveurs de test, intermédiaire plateformes et les environnements de production. La configuration peut varier considérablement entre ces environnements les & #x 2014 ; pas seulement en termes de noms de serveur, les chaînes de connexion et ainsi de suite, mais aussi potentiellement en termes d’informations d’identification, les paramètres de sécurité et un grand nombre d’autres facteurs. Si vous avez besoin pour ce faire régulièrement, il n’est pas vraiment utile de modifier plusieurs propriétés dans votre fichier projet chaque fois que vous basculez de l’environnement cible. Et n’est pas une solution idéale pour exiger une liste d’un nombre infinie de valeurs de propriété doivent être fournies pour le processus de génération.

Il existe heureusement une alternative. MSBuild vous permet de fractionner votre configuration de build dans plusieurs fichiers de projet. Pour voir comment cela fonctionne, dans l’exemple de solution, notez qu’il existe deux fichiers de projet personnalisé :

- *Publish.proj*, qui contient des propriétés, des éléments, et les cibles qui sont communes à tous les environnements.
- *Env-Dev.proj*, qui contient des propriétés qui sont spécifiques à un environnement de développement.

Maintenant Notez que la *Publish.proj* fichier inclut une [importation](https://msdn.microsoft.com/en-us/library/92x05xfs.aspx) élément, immédiatement après l’ouverture **projet** balise.


[!code-xml[Main](understanding-the-project-file/samples/sample16.xml)]


Le **importer** élément est utilisé pour importer le contenu d’un autre fichier de projet MSBuild dans le fichier de projet MSBuild en cours. Dans ce cas, le **TargetEnvPropsFile** paramètre fournit le nom de fichier du fichier de projet que vous souhaitez importer. Vous pouvez fournir une valeur pour ce paramètre lorsque vous exécutez MSBuild.


[!code-console[Main](understanding-the-project-file/samples/sample17.cmd)]


Il fusionne efficacement le contenu des deux fichiers dans un seul fichier de projet. À l’aide de cette approche, vous pouvez créer un fichier de projet qui contient votre configuration de build universel et plusieurs fichiers de projet supplémentaires qui contient les propriétés spécifiques à l’environnement. Par conséquent, il vous suffit d’exécuter une commande avec une valeur de paramètre différente vous permet de déployer votre solution à un autre environnement.

![](understanding-the-project-file/_static/image3.png)

Fractionnement de vos fichiers de projet de cette façon est une bonne pratique à suivre. Il permet aux développeurs de déployer dans plusieurs environnements en exécutant une commande unique, tout en évitant la duplication des propriétés de la build universel dans plusieurs fichiers de projet.

> [!NOTE]
> Pour obtenir des conseils sur la façon de personnaliser les fichiers de projet spécifique à un environnement pour vos propres environnements de serveur, consultez [configuration des propriétés de déploiement pour un environnement cible](../configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment.md).


## <a name="conclusion"></a>Conclusion

Cette rubrique fourni une introduction générale aux fichiers projet MSBuild et explique comment vous pouvez créer vos propres fichiers de projet personnalisé pour contrôler le processus de génération. Il a également introduit le concept de fractionner les fichiers projet dans les instructions de génération universelle et les propriétés spécifiques à l’environnement de génération, afin de simplifier générer et déployer des projets vers plusieurs destinations.

La rubrique suivante, [comprendre le processus de génération](understanding-the-build-process.md), fournit plus de détails sur l’utilisation des fichiers de projet pour la génération de contrôle et de déploiement en vous guidant à travers le déploiement d’une solution avec un niveau réaliste de complexité.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir une présentation plus approfondie des fichiers projet et les fournisseurs de services, consultez [à l’intérieur de la Microsoft Build Engine : à l’aide de MSBuild et Team Foundation Build](http://amzn.com/0735645248) par Sayed Ibrahim Hashimi et William Bartholomew, numéro ISBN : 978-0-7356-4524-0.

>[!div class="step-by-step"]
[Précédent](setting-up-the-contact-manager-solution.md)
[Suivant](understanding-the-build-process.md)
