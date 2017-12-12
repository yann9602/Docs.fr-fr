---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/running-windows-powershell-scripts-from-msbuild-project-files
title: "En cours d’exécution de Scripts Windows PowerShell à partir de fichiers de projet MSBuild | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment exécuter un script Windows PowerShell en tant que partie d’un processus de génération et de déploiement. Vous pouvez exécuter un script localement (en d’autres termes, sur la b..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 55f1ae45-fcb5-43a9-8415-fa5b935fc9c9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/running-windows-powershell-scripts-from-msbuild-project-files
msc.type: authoredcontent
ms.openlocfilehash: 5f6ba0655f5dc1d043b905428a3797ed141b0fed
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="running-windows-powershell-scripts-from-msbuild-project-files"></a>En cours d’exécution de Scripts Windows PowerShell à partir de fichiers de projet MSBuild
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment exécuter un script Windows PowerShell en tant que partie d’un processus de génération et de déploiement. Vous pouvez exécuter un script localement (en d’autres termes, sur le serveur de builds) ou à distance, sur un serveur de base de données ou un serveur web de destination.
> 
> Il existe un grand nombre de raisons pour lesquelles vous souhaiterez peut-être exécuter un script Windows PowerShell de post-déploiement. Vous pouvez, par exemple, décider d'effectuer les opérations suivantes :
> 
> - Ajoutez une source de l’événement personnalisé dans le Registre.
> - Générer un répertoire de système de fichiers pour les téléchargements.
> - Nettoyer les répertoires de génération.
> - Permet d’écrire des entrées dans un fichier journal personnalisé.
> - Envoyer des courriers électroniques de l’invitation d’utilisateurs à une application web qui vient d’être mis en service.
> - Créer des comptes d’utilisateur disposant des autorisations appropriées.
> - Configurer la réplication entre des instances de SQL Server.
> 
> Cette rubrique vous indique comment exécuter des scripts Windows PowerShell à la fois localement et à distance à partir d’une cible personnalisée dans un fichier de projet Microsoft Build Engine (MSBuild).


Cette rubrique fait partie d’une série de didacticiels basées sur les spécifications de déploiement d’entreprise d’une société fictive nommée Fabrikam, Inc. Cette série de didacticiels utilise un exemple de solution l’a & #x 2014 ; le [solution Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014 ; pour représenter une application web avec un niveau réaliste de complexité, y compris une application ASP.NET MVC 3, Windows Service de communication Foundation (WCF) et un projet de base de données.

La méthode de déploiement au cœur de ces didacticiels est basée sur l’approche de fichier de projet de fractionnement décrite dans [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md), dans lequel le processus de génération est contrôlé par deux fichiers de & projet #x 2014 ; un contenant les instructions qui s’appliquent à chaque environnement de destination et celui qui contient les paramètres de génération et de déploiement spécifiques à l’environnement de génération. Au moment de la génération, le fichier de projet spécifique à un environnement est fusionné dans le fichier de projet d’indépendant de l’environnement pour former un ensemble complet d’instructions de génération.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Pour exécuter un script Windows PowerShell en tant que partie d’un processus de déploiement automatisé ou pas à pas, vous devez effectuer ces tâches de niveau supérieur :

- Ajoutez le script Windows PowerShell à votre solution et contrôle de code source.
- Créer une commande qui appelle votre script Windows PowerShell.
- Séquence d’échappement des caractères XML réservés dans votre commande.
- Création d’une cible dans votre fichier de projet MSBuild personnalisé et utiliser le **Exec** tâche pour exécuter votre commande.

Cette rubrique vous indique comment effectuer ces procédures. Les tâches et les procédures pas à pas dans cette rubrique supposent que vous êtes déjà familiarisé avec les propriétés et les cibles de MSBuild, et que vous comprenez comment utiliser un fichier de projet MSBuild personnalisé pour un processus de génération et de déploiement. Pour plus d’informations, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md) et [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md).

## <a name="creating-and-adding-windows-powershell-scripts"></a>Création et ajout de Scripts Windows PowerShell

Les tâches de cette rubrique utilisent un exemple de script Windows PowerShell nommé **LogDeploy.ps1** pour illustrer comment exécuter des scripts à partir de MSBuild. Le **LogDeploy.ps1** script contient une fonction simple qui écrit une entrée d’une ligne dans un fichier journal :


[!code-javascript[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample1.js)]


Le **LogDeploy.ps1** script accepte deux paramètres. Le premier paramètre représente le chemin d’accès complet au fichier journal auquel vous souhaitez ajouter une entrée, et le deuxième paramètre représente la destination du déploiement que vous souhaitez enregistrer dans le fichier journal. Lorsque vous exécutez le script, il ajoute une ligne dans le fichier journal au format suivant :


[!code-html[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample2.html)]


Pour rendre le **LogDeploy.ps1** script disponible à MSBuild, vous devez :

- Ajoutez le script au contrôle de code source.
- Ajoutez le script à votre solution dans Visual Studio 2010.

Vous n’avez pas besoin de déployer le script avec votre contenu de la solution, indépendamment de si vous projetez d’exécuter le script sur le serveur de build ou sur un ordinateur distant. Une option consiste à ajouter le script dans un dossier de solution. Dans l’exemple de gestionnaire de contacts, parce que vous voulez utiliser le script Windows PowerShell en tant que partie du processus de déploiement, il est logique d’ajouter le script dans le dossier de solution Publish.

![](running-windows-powershell-scripts-from-msbuild-project-files/_static/image1.png)

Le contenu des dossiers de solution est copié pour générer des serveurs sources. Toutefois, ils ne forment aucune partie de la sortie du projet.

## <a name="executing-a-windows-powershell-script-on-the-build-server"></a>L’exécution d’un Script Windows PowerShell sur le serveur de builds

Dans certains scénarios, il pouvez que vous souhaitez exécuter des scripts Windows PowerShell sur l’ordinateur qui génère vos projets. Par exemple, vous pouvez utiliser un script Windows PowerShell pour nettoyer les dossiers de build ou d’écrire des entrées dans un fichier journal personnalisé.

En termes de syntaxe, un script Windows PowerShell en cours d’exécution à partir d’un fichier projet MSBuild est identique à un script Windows PowerShell en cours d’exécution à partir d’une invite de commandes standard. Vous devez appeler l’exécutable de powershell.exe et utiliser le **– commande** commutateur pour fournir les commandes Windows PowerShell à exécuter. (Dans Windows PowerShell v2, vous pouvez également utiliser le **– fichier** basculer). La commande doit prendre ce format :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample3.cmd)]


Exemple :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample4.cmd)]


Si le chemin d’accès à votre script inclut des espaces, vous devez placer le chemin d’accès de fichier entre apostrophes précédés d’un signe. Vous ne pouvez pas utiliser des guillemets doubles, car vous avez déjà utilisé pour placer la commande :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample5.cmd)]


Il existe quelques considérations supplémentaires lorsque vous appelez cette commande à partir de MSBuild. Tout d’abord, vous devez inclure le **– NonInteractive** indicateur pour vous assurer que le script s’exécute en mode silencieux. Ensuite, vous devez inclure le **– ExecutionPolicy** indicateur avec une valeur de l’argument approprié. Spécifie la stratégie d’exécution que Windows PowerShell s’appliquent à votre script et vous permet de substituer la stratégie d’exécution par défaut, ce qui peut empêcher l’exécution de votre script. Vous pouvez choisir parmi ces valeurs d’argument :

- La valeur **Unrestricted** permettra de Windows PowerShell exécuter votre script, indépendamment de si le script est signé.
- La valeur **RemoteSigned** permettra de Windows PowerShell exécuter des scripts non signés qui ont été créés sur l’ordinateur local. Toutefois, les scripts qui ont été créés ailleurs doivent être signés. (Dans la pratique, vous êtes peu probable que vous avez créé un script Windows PowerShell localement sur un serveur de builds).
- La valeur **AllSigned** permettra de Windows PowerShell exécuter des scripts signés uniquement.

La stratégie d’exécution par défaut est **Restricted**, ce qui empêche Windows PowerShell à partir des fichiers de script en cours d’exécution.

Enfin, vous devez échapper des caractères XML réservés qui se produisent dans votre commande de Windows PowerShell :

- Remplacez les guillemets simples avec  **&amp;apos ;**
- Remplacez les guillemets doubles avec  **&amp;quot ;**
- Remplacez les symboles avec  **&amp;amp ;**

- Lorsque vous apportez ces modifications, la commande ressemble à ceci :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample6.cmd)]


Dans votre fichier projet MSBuild personnalisé, vous pouvez créer une cible et utiliser le **Exec** tâche pour exécuter cette commande :


[!code-xml[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample7.xml)]


Dans cet exemple, notez que :

- Les variables, telles que les valeurs de paramètre et l’emplacement de l’exécutable de Windows PowerShell, sont déclarées en tant que propriétés de MSBuild.
- Conditions sont incluses pour permettre aux utilisateurs de remplacer ces valeurs à partir de la ligne de commande.
- Le **MSDeployComputerName** propriété déclarée ailleurs dans le fichier projet.

Lorsque vous exécutez cette cible dans le cadre de votre processus de génération, Windows PowerShell exécute la commande et écrire une entrée de journal dans le fichier que vous avez spécifié.

## <a name="executing-a-windows-powershell-script-on-a-remote-computer"></a>L’exécution d’un Script Windows PowerShell sur un ordinateur distant

Windows PowerShell est capable d’exécuter des scripts sur des ordinateurs distants via [Windows Remote Management](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426.aspx) (WinRM). Pour ce faire, vous devez utiliser le [Invoke-Command](https://technet.microsoft.com/en-us/library/dd347578.aspx) applet de commande. Cela vous permet d’exécuter votre script sur un ou plusieurs ordinateurs distants sans copier le script pour les ordinateurs distants. Tous les résultats sont retournés à l’ordinateur local à partir duquel vous avez exécuté le script.

> [!NOTE]
> Avant d’utiliser le **Invoke-Command** applet de commande à exécuter Windows PowerShell scripts sur un ordinateur distant, vous devez configurer un écouteur WinRM pour accepter les messages à distance. Vous pouvez le faire en exécutant la commande **winrm quickconfig** sur l’ordinateur distant. Pour plus d’informations, consultez [Installation et Configuration de Windows Remote Management](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384372(v=vs.85).aspx).


À partir d’une fenêtre Windows PowerShell, vous devez utiliser cette syntaxe pour exécuter le **LogDeploy.ps1** script sur un ordinateur distant :


[!code-powershell[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample8.ps1)]


> [!NOTE]
> Il existe plusieurs autres méthodes d’utilisation **Invoke-Command** pour exécuter un script de fichier, mais cette approche est la plus simple lorsque vous devez fournir des valeurs de paramètre et de gérer les chemins d’accès avec des espaces.


Lorsque vous exécutez ceci à partir d’une invite de commandes, vous devez appeler l’exécutable Windows PowerShell et utiliser le **– commande** pour fournir des instructions du paramètre :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample9.cmd)]


Comme avant, vous devez fournir certains commutateurs supplémentaires et d’échappement des caractères XML réservés lorsque vous exécutez la commande de MSBuild :


[!code-console[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample10.cmd)]


Enfin, comme avant, vous pouvez utiliser la **Exec** tâche dans une cible MSBuild personnalisée pour exécuter votre commande :


[!code-xml[Main](running-windows-powershell-scripts-from-msbuild-project-files/samples/sample11.xml)]


Lorsque vous exécutez cette cible dans le cadre de votre processus de génération, Windows PowerShell exécute votre script sur l’ordinateur que vous avez spécifié dans le **– computername** argument.

## <a name="conclusion"></a>Conclusion

Cette rubrique décrit comment exécuter un script Windows PowerShell à partir d’un fichier projet MSBuild. Vous pouvez utiliser cette approche pour exécuter un script Windows PowerShell, localement ou sur un ordinateur distant, dans le cadre d’un processus de génération et de déploiement automatique ou seule étape.

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la signature des scripts Windows PowerShell et la gestion des stratégies d’exécution, consultez [en cours d’exécution de Scripts Windows PowerShell](https://technet.microsoft.com/en-us/library/ee176949.aspx). Pour obtenir des conseils sur l’exécution de commandes Windows PowerShell à partir d’un ordinateur distant, consultez [en cours d’exécution des commandes à distance](https://technet.microsoft.com/en-us/library/dd819505.aspx).

Pour plus d’informations sur l’utilisation des fichiers projet MSBuild personnalisés pour contrôler le processus de déploiement, consultez [présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md) et [comprendre le processus de génération](../web-deployment-in-the-enterprise/understanding-the-build-process.md).

>[!div class="step-by-step"]
[Précédent](taking-web-applications-offline-with-web-deploy.md)
[Suivant](troubleshooting-the-packaging-process.md)
