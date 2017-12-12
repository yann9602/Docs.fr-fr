---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : résolution des problèmes (12 12) | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: 3fc23eed-921d-4d46-a610-a2d156e4bd03
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12
msc.type: authoredcontent
ms.openlocfilehash: 50de8473d1fd77de4b221f0c96fc7f184621d4b6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-troubleshooting-12-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : résolution des problèmes (12 12)
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer pour les Sites Web Windows Azure, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


Cette page décrit certains problèmes courants qui peuvent survenir lorsque vous déployez une application web ASP.NET à l’aide de Visual Studio. Pour chacune d’elles, un ou plusieurs causes possibles et les solutions correspondantes sont fournies.

## <a name="server-error-in--application---current-custom-error-settings-prevent-details-of-the-error-from-being-viewed-remotely"></a>Erreur de serveur dans l’Application - '/' paramètres d’erreur personnalisés actuels empêchent les détails de l’erreur de s’afficher à distance

### <a name="scenario"></a>Scénario

Après avoir déployé un site à un hôte distant, vous obtenez un message d’erreur qui mentionne le paramètre customErrors dans le fichier Web.config, mais n’indique pas quels était la cause réelle de l’erreur :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample1.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Par défaut, ASP.NET affiche des informations d’erreur détaillées uniquement lorsque votre application web est en cours d’exécution sur l’ordinateur local. En règle générale, vous ne souhaitez afficher les informations d’erreur détaillées lorsque votre application web est disponible publiquement sur Internet, étant donné que les pirates peuvent être en mesure d’utiliser ces informations pour rechercher des vulnérabilités dans l’application. Toutefois, lorsque vous déployez des mises à jour ou un site à un site, parfois dysfonctionnement sont et vous devez obtenir le message d’erreur.

Pour activer l’application afficher les messages d’erreur détaillés lorsqu’il s’exécute sur l’hôte distant, modifiez le fichier Web.config pour définir `customErrors` mode désactivé, redéployez l’application et relancez l’application :

1. Si le fichier Web.config de l’application a un `customErrors` élément dans le `system.web` élément, de modifier le `mode` attribut « désactivé ». Sinon, ajoutez un `customErrors` élément dans le `system.web` élément avec la `mode` attribut défini sur « désactivé », comme indiqué dans l’exemple suivant :

    [!code-xml[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample2.xml?highlight=3)]
2. Déployez l’application.
3. Exécutez l’application et répétez tout ce que vous l’avez fait précédemment à l’origine de l’erreur. Maintenant, vous pouvez voir ce qui est le message d’erreur.
4. Lorsque vous avez résolu l’erreur, restaurez la version d’origine `customErrors` définition et redéployer l’application.

## <a name="access-is-denied-in-a-web-page-that-uses-sql-server-compact"></a>L’accès est refusé dans une Page Web qu’utilise SQL Server Compact

### <a name="scenario"></a>Scénario

Lorsque vous déployez un site qui utilise SQL Server Compact et que vous exécutez une page dans le site déployé qui accède à la base de données, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample3.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le compte de SERVICE réseau sur le serveur doit être en mesure de lire des fichiers binaires native Service SQL Compact qui se trouvent dans le *bin\amd64* ou *bin\x86* dossier, mais il ne pas disposer des autorisations de lecture pour ces dossiers. Jeu en lecture pour le SERVICE réseau sur le *bin* dossier, en veillant à étendre les autorisations de sous-dossiers.

## <a name="cannot-read-configuration-file-due-to-insufficient-permissions"></a>Impossible de lire le fichier de Configuration en raison d’autorisations insuffisantes

### <a name="scenario"></a>Scénario

Lorsque vous cliquez sur le Visual Studio bouton Publier pour déployer une application à IIS sur votre ordinateur local, publication échoue et le **sortie** fenêtre affiche un message d’erreur similaire à ceci :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample4.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Pour utiliser un seul clic publier sur IIS sur votre ordinateur local, vous devez exécuter Visual Studio avec des autorisations d’administrateur. Fermez Visual Studio et le redémarrer avec des autorisations d’administrateur.

## <a name="could-not-connect-to-the-destination-computer--using-the-specified-process"></a>N’a pas pu se connecter à l’ordinateur de Destination... À l’aide de processus spécifié

### <a name="scenario"></a>Scénario

Lorsque vous cliquez sur le Visual Studio bouton Publier pour déployer une application, publication échoue et le **sortie** fenêtre affiche un message d’erreur similaire à ceci :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample5.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Un serveur proxy est interrompre la communication avec le serveur de destination. À partir du Panneau de configuration de Windows ou dans Internet Explorer, sélectionnez **Options Internet** et sélectionnez le **connexions** onglet. Dans le **propriétés Internet** boîte de dialogue, cliquez sur **paramètres LAN**. Dans le **les paramètres de réseau local (LAN)** boîte de dialogue, désactivez le **détecter automatiquement les paramètres** case à cocher. Puis cliquez sur le bouton Publier à nouveau.

Si le problème persiste, contactez votre administrateur système pour déterminer ce qui peut être fait avec les paramètres de proxy ou pare-feu. Le problème se produit parce que Web Deploy utilise un port non standard pour le déploiement du Service de gestion Web (8172) ; pour les autres connexions, Web Deploy utilise le port 80. Lorsque vous déployez sur un fournisseur d’hébergement tiers, vous utilisez en général, le Service de gestion Web.

## <a name="default-net-40-application-pool-does-not-exist"></a>Par défaut .NET 4.0 Application Pool n’existe pas

### <a name="scenario"></a>Scénario

Lorsque vous déployez une application qui nécessite le .NET Framework 4, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample6.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

ASP.NET 4 n’est pas installé dans IIS. Si le serveur que vous déployez sur votre ordinateur de développement et que Visual Studio 2010 est installé sur celui-ci, ASP.NET 4 est installé sur l’ordinateur, mais ne peut pas être installé dans IIS. Sur le serveur sur lequel vous effectuez le déploiement, ouvrez une invite de commandes avec élévation de privilèges et installer ASP.NET 4 dans IIS en exécutant les commandes suivantes :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample7.cmd)]

Vous devrez peut-être également définir manuellement la version de .NET Framework du pool d’applications par défaut. Pour plus d’informations, consultez la [déploiement vers IIS comme environnement de Test](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md) didacticiel.

## <a name="format-of-the-initialization-string-does-not-conform-to-specification-starting-at-index-0"></a>Format de la chaîne d’initialisation n’est pas conforme à la spécification commençant à l’index 0.

### <a name="scenario"></a>Scénario

Après avoir déployé une application à l’aide d’un seul clic publier, lorsque vous exécutez une page qui accède à la base de données vous obtenez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample8.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Ouvrez le *Web.config* fichier dans le site déployé et vérifiez si les valeurs de chaîne de connexion commencent par `$(ReplacableToken_`, comme dans l’exemple suivant :

[!code-xml[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample9.xml)]

Si les chaînes de connexion ressemblent à cet exemple, modifiez le fichier projet et ajoutez la propriété suivante à la `PropertyGroup` élément est pour toutes les configurations de build :

[!code-xml[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample10.xml)]

Puis redéployer l’application.

## <a name="http-500-internal-server-error"></a>Erreur de serveur interne 500 HTTP

### <a name="scenario"></a>Scénario

Lorsque vous exécutez le site déployé, vous consultez le message d’erreur sans informations spécifiques en indiquant la cause de l’erreur :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample11.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Il existe plusieurs causes de 500 erreurs, mais une des causes possibles si vous suivez ces didacticiels sont de placer un élément XML au mauvais endroit dans un des fichiers de transformation XML. Par exemple, vous obtiendrez cette erreur si vous placez la transformation qui insère un `<location>` élément sous `<system.web>` au lieu de directement sous `<configuration>`. Dans ce cas, la solution est à corriger le fichier de transformation XML et à redéployer.

## <a name="http-50021-internal-server-error"></a>Erreur de serveur interne 500.21 HTTP

### <a name="scenario"></a>Scénario

Lorsque vous exécutez le site déployé, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample12.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le site, vous avez déployé les cibles ASP.NET 4, mais ASP.NET 4 n'est pas enregistré dans IIS sur le serveur. Sur le serveur, ouvrez une invite de commandes avec élévation de privilèges et inscrire ASP.NET 4 en exécutant les commandes suivantes :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample13.cmd)]

Vous devrez peut-être également définir manuellement la version de .NET Framework du pool d’applications par défaut. Pour plus d’informations, consultez la [déploiement vers IIS comme environnement de Test](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md) didacticiel.

## <a name="login-failed-opening-sql-server-express-database-in-appdata"></a>Échec de la connexion lors de l’ouverture SQL Server Express de base de données dans l’application\_données

### <a name="scenario"></a>Scénario

Vous mis à jour le *Web.config* fichier de chaîne de connexion pour pointer vers une base de données SQL Server Express, comme un *.mdf* de fichiers dans votre *application\_données* dossier et le premier exécution de l’application que vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample14.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le nom de la *.mdf* fichier ne peut pas correspondre au nom d’une base de données SQL Server Express qui a existé sur votre ordinateur, même si vous avez supprimé le *.mdf* fichier de la base de données existante. Modifier le nom de la *.mdf* fichier un nom qui n’a jamais été utilisé comme nom de la base de données et modifier le *Web.config* fichier à utiliser le nouveau nom. En guise d’alternative, vous pouvez utiliser [SQL Server Management Studio Express](https://www.microsoft.com/en-us/download/details.aspx?displaylang=en&amp;id=7593) à supprimer existe déjà SQL Server Express de bases de données.

## <a name="model-compatibility-cannot-be-checked"></a>Compatibilité du modèle ne peut pas être vérifiée.

### <a name="scenario"></a>Scénario

Vous mis à jour le *Web.config* fichier de chaîne de connexion pour pointer vers une nouvelle base de données SQL Server Express et la première fois que vous exécutez l’application que vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample15.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Si le nom de la base de données que vous placez dans le fichier Web.config a déjà été utilisé avant sur votre ordinateur, une base de données existe peut-être déjà avec certaines tables qu’il contient. Sélectionnez un nouveau nom n’a pas été utilisé sur votre ordinateur avant de modifier le *Web.config* pour pointer pour utiliser ce nouveau nom de base de données. En guise d’alternative, vous pouvez utiliser [utilitaire SQL Server Express](https://www.microsoft.com/en-us/download/details.aspx?DisplayLang=en&amp;id=3990) ou [SQL Server Management Studio Express](https://www.microsoft.com/en-us/download/details.aspx?displaylang=en&amp;id=7593) pour supprimer la base de données existante.

## <a name="sql-error-when-a-script-attempts-to-create-users-or-roles"></a>Erreur SQL lors d’un Script tente de créer des utilisateurs ou des rôles

### <a name="scenario"></a>Scénario

À l’aide de déploiement de base de données configurée sur le **Package/Publication SQL** onglet, les scripts SQL qui s’exécutent pendant le déploiement incluent les commandes Create User ou de créer un rôle et Échec de l’exécution de script lorsque ces commandes sont exécutées. Vous pouvez voir plus de messages, tels que les éléments suivants :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample16.cmd)]

Si cette erreur se produit lorsque vous avez configuré le déploiement de la base de données dans le **publier le site Web** Assistant plutôt que la **Package/Publication SQL** , onglet de la création d’un thread dans le [Configuration et Déploiement](https://forums.asp.net/26.aspx/1?Configuration+and+Deployment) forum et la solution seront être ajouté à cette page de résolution des problèmes.

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le compte d’utilisateur que vous utilisez pour réaliser le déploiement n’a pas autorisé à créer des utilisateurs ou des rôles. Par exemple, la société d’hébergement peut affecter la `db_datareader`, `db_datawriter`, et `db_ddladmin` rôles pour le compte d’utilisateur qu’elle configure pour vous. Ceux-ci sont suffisantes pour la création de la plupart des objets de base de données, mais ne pas pour la création d’utilisateurs ou des rôles. Pour éviter l’erreur est en excluant des utilisateurs et des rôles de déploiement de base de données. Ce faire, vous pouvez modifier le `PreSource` , élément pour la base de données généré automatiquement de script afin qu’il inclue les attributs suivants :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample17.cmd)]

Pour plus d’informations sur la façon de modifier le `PreSource` élément dans le fichier projet, consultez [Comment : modifier les paramètres de déploiement dans le fichier projet](https://msdn.microsoft.com/en-us/library/ff398069(v=vs.100).aspx). Si les utilisateurs ou les rôles dans votre base de données de développement doivent se trouver dans la base de données de destination, contactez votre fournisseur d’hébergement pour obtenir une assistance.

## <a name="sql-server-timeout-error-when-running-custom-scripts-during-deployment"></a>Erreur de délai d’expiration SQL Server lors de l’exécution des Scripts personnalisés pendant le déploiement

### <a name="scenario"></a>Scénario

Vous avez spécifié des scripts SQL personnalisés pour s’exécuter pendant le déploiement et exécution de Web Deploy les leur délai d’attente.

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Exécution de plusieurs scripts qui ont des modes de transaction différentes peut provoquer des erreurs de délai d’attente. Par défaut, les scripts générés automatiquement s’exécutent dans une transaction, mais les scripts personnalisés ne sont pas. Si vous sélectionnez le **par extraction de données et/ou un schéma à partir d’une base de données** option sur le **Package/Publication SQL** onglet, et si vous ajoutez un script SQL personnalisé, vous devez modifier les paramètres des transactions dans des scripts afin que tous les scripts utilisent les mêmes paramètres de transaction. Pour plus d’informations, consultez [Comment : déployer une base de données avec un projet d’Application Web](https://msdn.microsoft.com/en-us/library/dd465343.aspx).

Si vous avez configuré les paramètres de transaction afin que toutes sont identiques mais que vous obtenez encore cette erreur, une solution de contournement possible consiste à exécuter les scripts séparément. Dans le **des Scripts de base de données** grille dans le **Package/Publication** onglet SQL, désactivez le **Include** case à cocher pour le script qui provoque l’erreur de délai d’attente, puis publiez le projet. Puis revenez dans le **des Scripts de base de données** grille, sélectionnez de ce script **Include** case à cocher, puis désactivez la **Include** cases à cocher pour les autres scripts. Publiez ensuite le projet. Cette fois, lorsque vous publiez, uniquement l’exécution du script personnalisé sélectionné.

## <a name="stream-data-of-site-manifest-is-not-yet-available"></a>Données de flux de manifeste de Site ne sont pas encore disponibles

### <a name="scenario"></a>Scénario

Lorsque vous installez un package à l’aide de la *deploy.cmd* de fichiers avec la `t` (test), option, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample18.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le message d’erreur signifie que la commande ne peut pas produire un rapport de test. Toutefois, la commande peut s’exécuter si vous utilisez la `y` option (installation réelle). Le message indique uniquement qu’il existe un problème avec la commande en cours d’exécution en mode test.

## <a name="this-application-requires-managedruntimeversion-v40"></a>Cette Application nécessite ManagedRuntimeVersion v4.0

### <a name="scenario"></a>Scénario

Lorsque vous tentez de déployer, vous voyez le message d’erreur suivant :

 Erreur : Les données de flux de ' sitemanifest/dbFullSql [@path= 'C:\TEMP\AdventureWorksGrant.sql']/sqlScript' n’est pas encore disponible. Le pool d’applications que vous tentez d’utiliser a la propriété « managedRuntimeVersion » la valeur « v2.0 ». Cette application est « v4.0 ». 

### <a name="possible-cause-and-solution"></a>Cause possible et solution

ASP.NET 4 n’est pas installé dans IIS. Si le serveur que vous déployez sur votre ordinateur de développement et que Visual Studio 2010 est installé sur celui-ci, ASP.NET 4 est installé sur l’ordinateur, mais ne peut pas être installé dans IIS. Sur le serveur sur lequel vous effectuez le déploiement, ouvrez une invite de commandes avec élévation de privilèges et installer ASP.NET 4 dans IIS en exécutant les commandes suivantes :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample19.cmd)]

## <a name="unable-to-cast-microsoftwebdeploymentdeploymentprovideroptions"></a>Impossible de convertir Microsoft.Web.Deployment.DeploymentProviderOptions

### <a name="scenario"></a>Scénario

Lorsque vous déployez un package, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample20.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Vous tentez de déployer à partir de Gestionnaire des services Internet à l’aide de la déployer 1.1 l’interface utilisateur Web à un serveur doté de Web Deploy 2.0 est installé. Si vous utilisez l’outil d’Administration à distance de IIS pour déployer à l’importation d’un package, vérifiez le **nouvelles fonctionnalités disponibles** boîte de dialogue lorsque vous établissez la connexion. (Cette boîte de dialogue peut uniquement être affichée qu’une seule fois lorsque la connexion est établie pour la première fois. Pour effacer la connexion et recommencer, fermez le Gestionnaire IIS et démarrent à nouveau en entrant `inetmgr /reset` à l’invite de commandes.) Si l’une des fonctionnalités dans la liste est **l’interface utilisateur du déploiement Web**et il a un numéro de version inférieur à 8, le serveur que vous effectuez le déploiement peut avoir les versions 1.1 et 2.0 de déploiement Web est installé. Pour déployer à partir d’un client 2.0 est installé, le serveur doit avoir uniquement Web Deploy 2.0 installé. Vous devrez contacter votre fournisseur d’hébergement pour résoudre ce problème.

## <a name="unable-to-load-the-native-components-of-sql-server-compact"></a>Impossible de charger les composants natives de SQL Server Compact

### <a name="scenario"></a>Scénario

Lorsque vous exécutez le site déployé, vous voyez le message d’erreur suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample21.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le site déployé n’a pas *amd64* et *x86* sous-dossiers contenant les assemblys natifs dans les sous l’application *bin* dossier. Sur un ordinateur doté de SQL Server Compact installé, les assemblys natifs sont situés dans *C:\Program Files\Microsoft SQL Server Compact Edition\v4.0\Private*. La meilleure façon d’obtenir les fichiers appropriés dans les dossiers appropriés dans un projet Visual Studio est d’installer le package NuGet SqlServerCompact. Installation du package ajoute un script de post-build pour copier les assemblys natifs dans *amd64* et *x86*. Toutefois, dans l’ordre de ces à déployer, vous devez manuellement les inclure dans le projet. Pour plus d’informations, consultez la [déploiement SQL Server Compact](deployment-to-a-hosting-provider-deploying-sql-server-compact-databases-2-of-12.md) didacticiel.

## <a name="path-is-not-valid-error-after-deploying-an-entity-framework-code-first-application"></a>Erreur de « Chemin d’accès n’est pas valide » après avoir déployé une application Entity Framework Code First

### <a name="scenario"></a>Scénario

Vous déployez une application qui utilise des Migrations Entity Framework Code First et un SGBD tels que SQL Server Compact, qui stocke sa base de données dans un fichier dans l’application\_dossier de données. Vous avez des Migrations Code First configuré pour créer la base de données après le premier déploiement. Lorsque vous exécutez l’application, vous obtenez un message d’erreur à l’exemple suivant :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample22.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Code tout d’abord tente de créer la base de données, mais l’application\_dossier de données n’existe pas. Soit vous n’aviez pas tous les fichiers le *application\_données* dossier lorsque vous avez déployé ou que vous avez sélectionné **exclure une application\_données** sur le **Package/Publication Web** onglet de la **propriétés du projet** fenêtre. Le processus de déploiement ne sont pas créer un dossier sur le serveur s’il n’existe aucun fichier dans le dossier à copier sur le serveur. Si vous disposez déjà de la base de données défini dans le site, le processus de déploiement supprime les fichiers et les *application\_données* dossier lui-même. Si vous avez sélectionné **supprimer les fichiers supplémentaires à la destination** dans le profil de publication. Pour résoudre ce problème, placez un fichier de l’espace réservé tel qu’un fichier .txt dans le *application\_données* dossier, assurez-vous que vous n’avez pas **exclure une application\_données** sélectionné, puis les redéployer. 

## <a name="com-object-that-has-been-separated-from-its-underlying-rcw-cannot-be-used"></a>« Objet COM qui a été séparé de son RCW sous-jacent ne peut pas être utilisé. »

### <a name="scenario"></a>Scénario

Vous avez été correctement à l’aide d’un seul clic publier pour déployer votre application et puis vous commencez à recevoir cette erreur :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample23.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Fermer et de redémarrer Visual Studio est généralement tout ce qui est nécessaire pour résoudre cette erreur.

## <a name="deployment-fails-because-user-credentials-used-for-publishing-dont-have-setacl-authority"></a>Déploiement échoue car utilisateur informations d’identification utilisées pour la publication n’avez pas setACL autorité

### <a name="scenario"></a>Scénario

Publication échoue avec une erreur indiquant que vous n’avez pas autorité pour définir les autorisations de dossier (le compte d’utilisateur que vous utilisez ne dispose pas setACL autorité).

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Par défaut, Visual Studio définit autorisations de lecture sur le dossier racine du site et les autorisations en écriture sur l’application\_dossier de données. Si vous savez que les autorisations par défaut sur les dossiers du site sont correctes et n’avez pas besoin être défini, vous désactivez ce comportement en ajoutant  **&lt;IncludeSetACLProviderOn Destination&gt;False&lt;/ IncludeSetACLProviderOnDestination&gt;**  au fichier de profil de publication (pour affecter un profil unique) ou au fichier wpp.targets (à affecter tous les profils). Pour plus d’informations sur la façon de modifier ces fichiers, consultez [Comment : modifier les paramètres de déploiement dans les fichiers de profil (.pubxml)](https://msdn.microsoft.com/en-us/library/ff398069.aspx). 

## <a name="access-denied-errors-when-the-application-tries-to-write-to-an-application-folder"></a>Erreurs d’accès refusé lors de l’Application tente d’écrire dans un dossier d’Application

### <a name="scenario"></a>Scénario

Erreurs de votre application lorsqu’il tente créer ou modifier un fichier dans un des dossiers d’application, car il ne dispose pas d’autorité d’écriture pour ce dossier.

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Par défaut, Visual Studio définit autorisations de lecture sur le dossier racine du site et les autorisations en écriture sur l’application\_dossier de données. Si votre application requiert un accès en écriture à un sous-dossier, vous pouvez définir des autorisations pour ce dossier comme indiqué dans le [définition des autorisations de dossier](deployment-to-a-hosting-provider-setting-folder-permissions-6-of-12.md) et [déploiement dans l’environnement de Production](deployment-to-a-hosting-provider-deploying-to-the-production-environment-7-of-12.md) didacticiels. Si votre application requiert un accès en écriture au dossier racine du site, vous devez l’empêcher de la définition de l’accès en lecture seule sur le dossier racine en ajoutant  **&lt;IncludeSetACLProviderOn Destination&gt;False&lt;/ IncludeSetACLProviderOnDestination&gt;**  au fichier de profil de publication (pour affecter un profil unique) ou au fichier wpp.targets (à affecter tous les profils). Pour plus d’informations sur la façon de modifier ces fichiers, consultez [Comment : modifier les paramètres de déploiement dans les fichiers de profil (.pubxml)](https://msdn.microsoft.com/en-us/library/ff398069.aspx). <a id="aspnet45error"></a>

## <a name="configuration-error---targetframework-attribute-references-a-version-that-is-later-than-the-installed-version-of-the-net-framework"></a>Erreur de configuration - attribut targetFramework fait référence à une version ultérieure à la version installée du .NET Framework

### <a name="scenario"></a>Scénario

Vous avez publié avec succès un projet web qui cible ASP.NET 4.5, mais lorsque vous exécutez l’application (avec la `customErrors` mode la valeur « désactivé » dans le fichier Web.config) vous obtenez l’erreur suivante :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample24.cmd)]

La zone d’erreur de Source de la page d’erreur met en surbrillance la ligne suivante à partir de Web.config en tant que la cause de l’erreur :

[!code-console[Main](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12/samples/sample25.cmd)]

### <a name="possible-cause-and-solution"></a>Cause possible et solution

Le serveur ne prend pas en charge ASP.NET 4.5. Contactez le fournisseur d’hébergement pour déterminer quand et si la prise en charge pour ASP.NET 4.5 peut être ajoutée. Si la mise à niveau le serveur n’est pas une option, vous devez déployer un projet web qui cible ASP.NET 4 ou une version antérieure à la place. Si vous déployez un 4 d’ASP.NET ou d’un projet web antérieur à la même destination, sélectionnez le **supprimer les fichiers supplémentaires à la destination** case à cocher sur la **paramètres** onglet du **publier le site Web**Assistant. Si vous ne sélectionnez pas **supprimer les fichiers supplémentaires à la destination**, vous continuez à obtenir la page d’erreur de Configuration.

Le projet **propriétés** windows inclut une liste déroulante du framework cible, mais vous ne peut pas résoudre ce problème en modifiant simplement le celui de **.NET Framework 4.5** à **de.NETFramework4**. Si vous modifiez le framework cible pour une version antérieure de framework, le projet contiendra encore des références aux assemblys de la dernière version framework et ne s’exécutera pas. Vous devez manuellement modifier ces références ou créez un projet qui cible .NET Framework 4 ou version antérieure. Pour plus d’informations, consultez [ciblage de .NET Framework pour les Sites Web](https://msdn.microsoft.com/en-us/library/bb398791(v=vs.100).aspx).

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-deploying-a-sql-server-database-update-11-of-12.md)
