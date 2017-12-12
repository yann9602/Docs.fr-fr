---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
title: "Automatiser tout (création d’applications de Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: ba6e6baa-9b9f-471f-b39d-b007a3addadc
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
msc.type: authoredcontent
ms.openlocfilehash: cf1cb7b07ffe8750724e58e4fb66854c9a033a54
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="automate-everything-building-real-world-cloud-apps-with-azure"></a>Automatiser tout (création d’applications de Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour obtenir une présentation du livre électronique, consultez [le premier chapitre](introduction.md).


Les trois premiers modèles que nous allons nous intéresser à appliquent en fait à un projet de développement logiciel, mais en particulier pour les projets de cloud. Ce modèle est sur l’automatisation des tâches de développement. Elle est importante, car des processus manuels sont lente et sujette à erreurs ; automatisation autant d’en tant que possible vous aide à configurer un flux de travail agile, fiable et rapide. Il est unique important pour le développement de cloud, car vous pouvez automatiser facilement les nombreuses tâches qui sont difficiles ou impossibles à automatiser dans un environnement sur site. Par exemple, vous pouvez configurer test entier environnements, y compris le nouveau serveur web et les machines virtuelles de serveur principal, les bases de données, blob (stockage de fichier), les files d’attente, etc.

## <a name="devops-workflow"></a>Flux de travail DevOps

Plus vous entendez le terme « DevOps. » Le terme développé en dehors d’une reconnaissance d’avoir à intégrer des tâches de développement et les opérations afin de développer des logiciels de manière efficace. Le type de workflow que vous souhaitez activer est un dans lequel vous pouvez développer une application, déployer, obtenir des informations à partir de l’utilisation de production de celui-ci, modifiez-le en réponse à ce que vous avez appris et répétez le cycle de façon rapide et fiable.

Certaines équipes de développement cloud efficace de déployer plusieurs fois par jour à un environnement dynamique. L’équipe Azure permet de déployer une majeure mettre à jour tous les mois 2-3, mais maintenant mises à jour mineures libère tous les 2 à 3 jours et majeure libère toutes les 2 à 3 semaines. Mise en route dans cette cadence réellement vous aide à répondre aux commentaires des clients.

Pour ce faire, vous devez activer un cycle de développement et de déploiement qui est prévisible, fiables et reproductibles et ait le temps de cycle de basse.

![Flux de travail DevOps](automate-everything/_static/image1.png)

En d’autres termes, la période de temps entre lorsque vous avez une idée pour une fonctionnalité, et lorsque de l’utiliser et d’envoyer des commentaires des clients doit être aussi courte que possible. Les trois premiers modèles – automatiser tout, le contrôle de code source, et d’intégration continue et remise--concernent toutes les meilleures pratiques que nous recommandons afin d’activer ce type de processus.

## <a name="azure-management-scripts"></a>Scripts de gestion Azure

Dans le [présentation de ce livre électronique](introduction.md), vous avez vu la console web, le portail de gestion Azure. Le portail de gestion vous permet de surveiller et gérer toutes les ressources que vous avez déployé sur Azure. Il s’agit d’un moyen simple pour créer et supprimer des services tels que les applications web et les machines virtuelles, configurez ces services, surveiller l’opération de service et ainsi de suite. Il s’agit d’un outil très utile, mais son utilisation est un processus manuel. Si vous allez développer une application de production de n’importe quelle taille, en particulier dans un environnement d’équipe, nous vous recommandons vous accédez via le portail de l’interface utilisateur pour en savoir plus et Explorer Azure et automatisez les processus que vous allez être opération de façon répétée.

Presque tout ce que vous pouvez le faire manuellement dans le portail de gestion ou à partir de Visual Studio peut également faire en appelant l’API de gestion REST. Vous pouvez écrire des scripts à l’aide de [Windows PowerShell](https://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx), ou vous pouvez utiliser une infrastructure open source tels que [Chef](http://www.opscode.com/chef/) ou [Puppet](http://puppetlabs.com/puppet/what-is-puppet). Vous pouvez également utiliser l’outil de ligne de commande d’interpréteur de commandes dans un environnement Mac ou Linux. Azure API de script pour les différents environnements, et il possède un [API de gestion .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx) au cas où vous souhaitez écrire du code au lieu de script.

Pour l’application corriger, nous avons créé des scripts Windows PowerShell qui automatisent les processus de création d’un environnement de test et de déploiement du projet à cet environnement, et nous allons passer en revue quelques-unes du contenu de ces scripts.

## <a name="environment-creation-script"></a>Script de création d’environnement

Le premier script, nous allons examiner est nommé *New-AzureWebsiteEnv.ps1*. Il crée un environnement Azure que vous pouvez déployer les corriger à l’application de test. Les principales tâches réalisées par ce script sont les suivantes :

- Créer une application web.
- Créer un compte de stockage. (Obligatoire pour les objets BLOB et files d’attente, comme vous le verrez dans les chapitres.)
- Créer un serveur de base de données SQL et les deux bases de données : une base de données d’application et une base de données d’appartenance.
- Stocker des paramètres dans Azure que l’application utilisera pour accéder au compte de stockage et les bases de données.
- Créer des fichiers de paramètres qui servira à automatiser le déploiement.

### <a name="run-the-script"></a>Exécutez le script


> [!NOTE]
> Cette partie de ce chapitre présente des exemples de scripts et les commandes que vous entrez pour les exécuter. Présente une démonstration et ne fournit pas tout ce dont vous avez besoin de connaître pour exécuter les scripts. Pour obtenir des instructions étape par étape comment-à--it, consultez [annexe : le corriger il exemple d’Application](the-fix-it-sample-application.md#deploybase).


Pour exécuter un script PowerShell qui gère les services Azure, que vous devez installer la console Azure PowerShell et le configurer pour qu’il fonctionne avec votre abonnement Azure. Une fois que vous êtes prêt, vous pouvez exécuter le script de création environnement corriger avec une commande similaire à celle-ci :

`.\New-AzureWebsiteEnv.ps1 -Name <websitename> -SqlDatabasePassword <password>`

Le `Name` paramètre spécifie le nom à utiliser lorsque vous créez les comptes de stockage et de la base de données et le `SqlDatabasePassword` paramètre spécifie le mot de passe du compte administrateur qui sera créé pour la base de données SQL. Voici les autres paramètres que vous pouvez utiliser que nous allons examiner ultérieurement.

![Fenêtre PowerShell](automate-everything/_static/image2.png)

Après la fin du script vous pouvez voir dans le portail de gestion en ce qui a été créé. Vous trouverez des deux bases de données :

![Bases de données](automate-everything/_static/image3.png)

Un compte de stockage :

![Compte de stockage](automate-everything/_static/image4.png)

Et une application web :

![Site Web](automate-everything/_static/image5.png)

Sur le **configurer** onglet pour l’application web, vous pouvez voir qu’il possède les paramètres de compte de stockage et chaînes de connexion de base de données SQL défini pour le correctif il application.

![appSettings et connectionStrings](automate-everything/_static/image6.png)

Le *Automation* maintenant contient également un  *&lt;websitename&gt;.pubxml* fichier. Ce fichier stocke les paramètres que MSBuild utilisera pour déployer l’application à l’environnement Azure qui vient d’être créé. Exemple :

[!code-xml[Main](automate-everything/samples/sample1.xml)]

Comme vous pouvez le voir, le script a créé un environnement de test terminée, et l’ensemble du processus est effectué dans environ 90 secondes.

Si une autre personne de votre équipe souhaite créer un environnement de test, ils peuvent uniquement exécuter le script. Non seulement est rapide, mais il peuvent également être certain qu’ils utilisent un environnement identique à celui que vous utilisez. Vous n’a pas pu être tout à fait comme garantir que si tout le monde a été configurer les choses manuellement à l’aide de l’interface utilisateur du portail de gestion.

### <a name="a-look-at-the-scripts"></a>Examiner les scripts

Il existe en fait trois scripts qui effectuent ce travail. Vous appelez l’une à partir de la ligne de commande et utilise automatiquement les deux autres pour effectuer certaines de ces tâches :

- *Nouveau-AzureWebSiteEnv.ps1* est le script principal.

    - *Nouveau-AzureStorage.ps1* crée le compte de stockage.
    - *Nouveau-AzureSql.ps1* crée les bases de données.

### <a name="parameters-in-the-main-script"></a>Paramètres dans le script principal

Le script principal, *New-AzureWebSiteEnv.ps1*, définit plusieurs paramètres :

[!code-powershell[Main](automate-everything/samples/sample2.ps1)]

Deux paramètres sont requis :

- Le nom de l’application web que crée le script. (Il est également utilisé pour l’URL : `<name>.azurewebsites.net`.)
- Le mot de passe pour le nouvel utilisateur d’administration du serveur de base de données que crée le script.

Paramètres facultatifs permettent de spécifier l’emplacement de centre de données (par défaut « Ouest des États-Unis »), nom d’administrateur de serveur de base de données (par défaut « dbuser ») et une règle de pare-feu pour le serveur de base de données.

### <a name="create-the-web-app"></a>Créer l’application web

La première chose que le script est de créer l’application web en appelant le `New-AzureWebsite` applet de commande dans en lui passant l’application web nom et l’emplacement des valeurs de paramètre :

[!code-powershell[Main](automate-everything/samples/sample3.ps1?highlight=2)]

### <a name="create-the-storage-account"></a>Créer le compte de stockage

Le script principal s’exécute le *New-AzureStorage.ps1* de script, en spécifiant «*&lt;websitename&gt;*stockage » pour le nom de compte de stockage, et les mêmes données poste en tant que l’application web.

[!code-powershell[Main](automate-everything/samples/sample4.ps1?highlight=3)]

*Nouveau-AzureStorage.ps1* appelle le `New-AzureStorageAccount` pour créer le compte de stockage et il retourne le compte d’accès et le nom des valeurs de clé. L’application devra ces valeurs pour pouvoir accéder aux objets BLOB et files d’attente dans le compte de stockage.

[!code-powershell[Main](automate-everything/samples/sample5.ps1?highlight=2)]

Il est toujours déconseillé créer un nouveau compte de stockage ; susceptibles d’améliorer le script en ajoutant un paramètre qui indique si vous le souhaitez pour utiliser un compte de stockage existant.

### <a name="create-the-databases"></a>Créer les bases de données

Le script principal exécute ensuite le script de création de base de données, *New-AzureSql.ps1*, après configuration de base de données par défaut et les noms de règle de pare-feu :

[!code-powershell[Main](automate-everything/samples/sample6.ps1)]

[!code-powershell[Main](automate-everything/samples/sample7.ps1?highlight=2)]

Le script de création de base de données récupère l’adresse IP de l’ordinateur de développement et définit une règle de pare-feu afin que l’ordinateur de développement permettre se connecter à et gérer le serveur. Le script de création de base de données passe ensuite à travers plusieurs étapes pour configurer les bases de données :

- Crée le serveur à l’aide de la `New-AzureSqlDatabaseServer` applet de commande.

    [!code-powershell[Main](automate-everything/samples/sample8.ps1?highlight=1)]
- Crée des règles de pare-feu pour permettre à l’ordinateur de développement pour gérer le serveur et pour permettre à l’application web pour la connexion. 

    [!code-powershell[Main](automate-everything/samples/sample9.ps1?highlight=3,5)]
- Crée un contexte de base de données qui inclut le nom du serveur et les informations d’identification, à l’aide de la `New-AzureSqlDatabaseServerContext` applet de commande.

    [!code-powershell[Main](automate-everything/samples/sample10.ps1?highlight=4)]

    `New-PSCredentialFromPlainText`est une fonction dans le script qui appelle le `ConvertTo-SecureString` applet de commande pour chiffrer le mot de passe et retourne un `PSCredential` de l’objet, le même type que le `Get-Credential` applet de commande renvoie.
- Crée la base de données de l’application et la base de données d’appartenance à l’aide de la `New-AzureSqlDatabase` applet de commande.

    [!code-powershell[Main](automate-everything/samples/sample11.ps1?highlight=2,5)]
- Appelle une fonction définie localement de tocreates une chaîne de connexion pour chaque base de données. L’application utilisera ces chaînes de connexion pour accéder aux bases de données. 

    [!code-powershell[Main](automate-everything/samples/sample12.ps1?highlight=1-2)]

    Get-SQLAzureDatabaseConnectionString est une fonction définie dans le script qui crée la chaîne de connexion à partir des valeurs de paramètre fournies.

    [!code-powershell[Main](automate-everything/samples/sample13.ps1?highlight=1)]
- Retourne une table de hachage avec le nom du serveur de base de données et les chaînes de connexion.

    [!code-powershell[Main](automate-everything/samples/sample14.ps1)]

L’application Fix It utilise appartenance distinct et application. Il est également possible de placer les données d’appartenance et d’application d’une base de données.

### <a name="store-app-settings-and-connection-strings"></a>Stocker des chaînes de connexion et les paramètres de l’application

Azure propose une fonctionnalité qui vous permet de stocker les paramètres et les chaînes de connexion qui substituent automatiquement ce qui est retourné à l’application lorsqu’elle essaie de lire le `appSettings` ou `connectionStrings` collections dans le fichier Web.config. Il s’agit d’une alternative à l’application [transformations Web.config](../../../../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md) lorsque vous déployez. Pour plus d’informations, consultez [stocker des données sensibles dans Azure](source-control.md#appsettings) plus loin dans ce livre électronique.

Le script de création d’environnement stocke dans Azure tous les `appSettings` et `connectionStrings` valeurs dont l’application a besoin pour accéder au compte de stockage et bases de données lorsqu’elle s’exécute dans Azure.

[!code-powershell[Main](automate-everything/samples/sample15.ps1)]

[!code-powershell[Main](automate-everything/samples/sample16.ps1)]

[!code-powershell[Main](automate-everything/samples/sample17.ps1?highlight=2)]

[New Relic](http://newrelic.com/) est une infrastructure de télémétrie, nous allons montrer dans le [surveillance et télémétrie](monitoring-and-telemetry.md) chapitre. Le script de création d’environnement redémarre également l’application web pour vous assurer qu’il récupère les paramètres de New Relic.

[!code-powershell[Main](automate-everything/samples/sample18.ps1?highlight=2)]

### <a name="preparing-for-deployment"></a>Préparation au déploiement

À la fin du processus, le script de création de l’environnement appelle deux fonctions pour créer des fichiers qui seront utilisés par le script de déploiement.

L’une de ces fonctions crée un profil de publication *(&lt;websitename&gt;.pubxml* fichier). Le code appelle l’API REST de Azure pour obtenir les paramètres de publication, et elle enregistre les informations contenues dans un *.publishsettings* fichier. Ensuite, il utilise les informations de ce fichier ainsi que d’un fichier de modèle (*pubxml.template*) pour créer le *.pubxml* fichier qui contient le profil de publication. Ce processus en deux étapes simule les opérations dans Visual Studio : télécharger un *.publishsettings* de fichier et les importer pour créer un profil de publication.

L’autre fonction utilise un autre fichier de modèle (site Web-environment.template) pour créer un *Environment.XML au sein de site Web* fichier qui contient des paramètres, utilisez le script de déploiement avec le *.pubxml*fichier.

### <a name="troubleshooting-and-error-handling"></a>Résolution des problèmes et la gestion des erreurs

Les scripts sont comme les programmes : elles peuvent échouer et s’ils le font que vous souhaitez connaître autant que possible sur l’erreur et la cause. Pour cette raison, le script de création d’environnement change la valeur de la `VerbosePreference` variable `SilentlyContinue` à `Continue` afin que tous les messages détaillés sont affichent. Il modifie également la valeur de la `ErrorActionPreference` variable `Continue` à `Stop`, de sorte que le script s’arrête encore lorsqu’il détecte les erreurs sans fin d’exécution :

[!code-powershell[Main](automate-everything/samples/sample19.ps1)]

Avant de ce travail, le script stocke l’heure de début afin qu’il peut calculer le temps écoulé lors de l’opération est terminée :

[!code-powershell[Main](automate-everything/samples/sample20.ps1)]

Après avoir terminé son travail, le script affiche le temps écoulé :

[!code-powershell[Main](automate-everything/samples/sample21.ps1)]

Et, pour chaque opération de clé le script écrit les messages détaillés, par exemple :

[!code-powershell[Main](automate-everything/samples/sample22.ps1)]

## <a name="deployment-script"></a>Script de déploiement

Ce que la *New-AzureWebsiteEnv.ps1* script effectue pour la création de l’environnement, le *AzureWebsite.ps1 de publication* script fait pour le déploiement de l’application.

Le script de déploiement Obtient le nom de l’application web à partir de la *Environment.XML au sein de site Web* fichier créé par le script de création d’environnement.

[!code-powershell[Main](automate-everything/samples/sample23.ps1)]

Il obtient le mot de passe utilisateur déploiement à partir de la *.publishsettings* fichier :

[!code-powershell[Main](automate-everything/samples/sample24.ps1)]

Il exécute la [MSBuild](http://msbuildbook.com/) commande qui crée et déploie le projet :

[!code-powershell[Main](automate-everything/samples/sample25.ps1)]

Et si vous avez spécifié le `Launch` paramètre sur la ligne de commande, il appelle le `Show-AzureWebsite` applet de commande pour ouvrir votre navigateur par défaut à l’URL du site Web.

[!code-powershell[Main](automate-everything/samples/sample26.ps1?highlight=3)]

Vous pouvez exécuter le script de déploiement avec une commande similaire à celle-ci :

`.\Publish-AzureWebsite.ps1 ..\MyFixIt\MyFixIt.csproj -Launch`

Et lorsqu’il est terminé, le navigateur s’ouvre avec le site en cours d’exécution dans le cloud à le `<websitename>.azurewebsites.net` URL.

![Corriger application déployée sur Windows Azure](automate-everything/_static/image7.png)

## <a name="summary"></a>Résumé

Avec ces scripts, vous pouvez être certain que les mêmes étapes seront toujours exécutées dans le même ordre que celui utilisant les mêmes options. Cela permet de garantir que chaque développeur de l’équipe n’oublié quelque chose ou tromper quelque chose ou déployer un élément personnalisé sur son propre ordinateur qui ne sont pas réellement fonctionnent de manière identique dans l’environnement d’un autre membre de l’équipe ou de production.

De la même façon, vous pouvez automatiser les fonctions de gestion plus Azure que vous pouvez effectuer dans le portail de gestion, à l’aide de l’API REST, les scripts Windows PowerShell, une API en langage .NET ou un utilitaire de l’interpréteur de commandes que vous pouvez exécuter sur Linux ou Mac.

Dans le [chapitre suivant](source-control.md) nous allons examiner le code source et expliquez pourquoi il est important d’inclure vos scripts dans votre référentiel de code source.

## <a name="resources"></a>Ressources

- [Installer et configurer Windows PowerShell pour Azure](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). Explique comment installer les applets de commande Azure PowerShell et comment installer le certificat que vous avez besoin sur votre ordinateur afin de gérer votre Azure du compte. Il s’agit d’un excellent point de mise en route, car elle contient également des liens vers des ressources pour l’apprentissage de PowerShell.
- [Centre de scripts Azure](https://docs.microsoft.com/azure/automation/automation-runbook-gallery). Portail WindowsAzure.com aux ressources pour le développement de scripts pour gérer des services Azure, avec des liens vers la prise en main des didacticiels et exemples de scripts applet de commande référence documentation et le code source
- [Générateur de script week-end : Prise en main d’Azure et PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/06/22/weekend-scripter-getting-started-with-windows-azure-and-powershell.aspx). Dans un blog dédié à Windows PowerShell, cette publication fournit une bonne introduction à l’aide de PowerShell pour les fonctions de gestion Azure.
- [Installer et configurer l’Interface de ligne de commande multiplateforme Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Didacticiel de mise en route pour une infrastructure de scripts Azure qui fonctionne sur Mac et Linux, ainsi que Windows systèmes.
- [Section des outils de ligne de commande de la rubrique de télécharger le SDK Azure et les outils](https://azure.microsoft.com/downloads/). Page du portail pour la documentation et téléchargements relatifs aux outils de ligne de commande pour Azure.
- [Automatisation de tous les éléments avec les bibliothèques de gestion Azure et .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Scott Hanselman introduit l’API de gestion .NET pour Azure.
- [À l’aide de Scripts Windows PowerShell pour publier dans les environnements de Test et développement](https://msdn.microsoft.com/library/azure/dn642480.aspx). Documentation MSDN qui explique comment utiliser les scripts que Visual Studio génère automatiquement pour les projets web de publication.
- [Outils PowerShell pour Visual Studio 2013](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597). Extension Visual Studio qui ajoute la prise en charge linguistique pour Windows PowerShell dans Visual Studio.

>[!div class="step-by-step"]
[Précédent](introduction.md)
[Suivant](source-control.md)
