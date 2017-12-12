---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : Transformations du fichier Web.Config - 3 sur 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: 2b0df3d9-450b-4ea6-b315-4c9650722cad
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12
msc.type: authoredcontent
ms.openlocfilehash: 8f68a85e44389ed17576436a9210c0ca3f414403
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-webconfig-file-transformations---3-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : Transformations du fichier Web.Config - 3 sur 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel vous montre comment automatiser le processus de modification de la *Web.config* fichier lors de son déploiement dans les environnements de destination différents. La plupart des applications ont des paramètres le *Web.config* fichier doit être différent lorsque l’application est déployée. Automatisation du processus de ces modifications, vous évite d’avoir à le faire manuellement chaque fois que vous déployez, ce qui serait fastidieux et sujet aux erreurs.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md).

## <a name="webconfig-transformations-versus-web-deploy-parameters"></a>Les Transformations Web.config et Web déploiement des paramètres

Il existe deux façons d’automatiser le processus de modification *Web.config* les paramètres des fichiers : [transformations Web.config](https://msdn.microsoft.com/en-us/library/dd465326.aspx) et [paramètres de déploiement Web](https://msdn.microsoft.com/en-us/library/ff398068.aspx). A *Web.config* fichier de transformation contient le balisage XML qui spécifie comment modifier le *Web.config* fichier lorsqu’il est déployé. Vous pouvez spécifier des modifications pour spécifiques à différentes configurations de build et spécifique des profils de publication. Les configurations de build par défaut sont Debug et Release, et vous pouvez créer des configurations de build personnalisée. Un profil de publication correspond généralement à un environnement de destination. (Vous allez apprendre plus sur Publier les profils dans le [déploiement vers IIS comme environnement de Test](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md) didacticiel.)

Paramètres de déploiement Web peuvent servir à spécifier les différents types de paramètres qui doivent être configurés au cours du déploiement, y compris les paramètres qui sont trouvent dans *Web.config* fichiers. Lorsqu’il est utilisé pour spécifier *Web.config* fichier change, les paramètres de Web Deploy sont plus complexes à configurer, mais ils sont utiles lorsque vous ne connaissez pas la valeur à définir jusqu'à ce que vous déployez. Par exemple, dans un environnement d’entreprise, vous pouvez créer un *package de déploiement* et lui donner à une personne du département informatique pour installer en production, et que cette personne doit être en mesure d’entrer des chaînes de connexion ou mots de passe que vous ne connaître.

Pour le scénario qui couvre de ce didacticiel, vous savez que tout ce qui doit être effectuée à la *Web.config* de fichiers, vous n’avez pas besoin d’utiliser les paramètres de déploiement Web. Vous devez configurer certaines transformations qui diffèrent en fonction de la configuration de build utilisée, et certaines qui varient selon le profil de publication utilisé.

## <a name="creating-transformation-files-for-publish-profiles"></a>Création de fichiers de Transformation pour les profils de publication

Dans **l’Explorateur de solutions**, développez *Web.config* pour voir les *Web.Debug.config* et *Web.Release.config* fichiers de transformation sont créés par défaut pour les configurations de build par défaut de deux.

![Web.config_transform_files](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image1.png)

Vous pouvez créer des fichiers de transformation pour les configurations de build personnalisée en double-cliquant sur le fichier Web.config et en choisissant **ajouter des transformations de configuration** dans le menu contextuel, mais pour ce didacticiel vous n’avez pas besoin de le faire.

Vous avez besoin de deux autres fichiers de transformation, pour la configuration des modifications qui sont liées à la destination du déploiement plutôt qu’à la configuration de build. Un exemple typique de ce type de paramètre est un point de terminaison WCF est différent pour le test et production. Dans les didacticiels suivants vous allez créer des profils nommées Test et Production, donc vous devez de publication un *Web.Test.config* fichier et un *Web.Production.config* fichier.

Les fichiers de transformation qui sont liés pour des profils de publication doivent être créés manuellement. Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity et sélectionnez **ouvrir le dossier dans l’Explorateur Windows**.

![Open_folder_in_Windows_Explorer](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image2.png)

Dans **l’Explorateur Windows**, sélectionnez le *Web.Release.config* de fichiers, copiez le fichier, puis collez les deux copies de ce dernier. Renommer ces copies *Web.Production.config* et *Web.Test.config*, puis fermez **l’Explorateur Windows**.

Dans **l’Explorateur de solutions**, cliquez sur **Actualiser** pour afficher les nouveaux fichiers.

Sélectionnez les nouveaux fichiers, avec le bouton droit, puis cliquez sur **inclure dans le projet** dans le menu contextuel.

![Y compris les fichiers de configuration de Test et de Production dans le projet](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image3.png)

Pour éviter ces fichiers en cours de déploiement, sélectionnez-les dans **l’Explorateur de solutions**, puis, dans le **propriétés** modification de la fenêtre du **Action de génération** propriété à partir de **Contenu** à **aucun**. (Les fichiers de transformation qui sont basées sur les configurations de build ne peuvent pas automatiquement du déploiement).

Vous êtes maintenant prêt à entrer *Web.config* les transformations en le *Web.config* les fichiers de transformation.

## <a name="limiting-error-log-access-to-administrators"></a>Limiter l’accès de journal d’erreur pour les administrateurs

Si une erreur est survenue pendant l’exécution de l’application, l’application affiche une page d’erreur générique à la place de la page d’erreur générée par le système et il utilise [package Elmah NuGet](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) pour la journalisation des erreurs et création de rapports. Le `customErrors` élément dans le *Web.config* fichier spécifie la page d’erreur :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample1.xml)]

Pour afficher la page d’erreur, modifiez temporairement le `mode` attribut de la `customErrors` élément à partir de « RemoteOnly » à « Sur » et exécuter l’application à partir de Visual Studio. Génère une erreur en demandant une URL non valide, tel que *Studentsxxx.aspx*. Au lieu d’une page d’erreur générés par IIS « page introuvable », vous voyez le *GenericErrorPage.aspx* page.

[![Error_page](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image5.png)](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image4.png)

Pour afficher le journal des erreurs, remplacez tout dans l’URL après le numéro de port avec *elmah.axd* (dans l’exemple de la capture d’écran, `http://localhost:51130/elmah.axd`) et appuyez sur ENTRÉE :

[![Elmah_log_page](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image7.png)](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image6.png)

N’oubliez pas de définir la `customErrors` élément vers le mode « RemoteOnly » lorsque vous avez terminé.

Sur votre ordinateur de développement, il convient d’autoriser l’accès libre à la page journal d’erreurs, mais en production qui serait un risque de sécurité. Pour le site de production, vous pouvez ajouter une règle d’autorisation qui limite l’accès de journal d’erreur seulement par les administrateurs en configurant une transformation dans le *Web.Production.config* fichier.

Ouvrez *Web.Production.config* et ajouter un nouveau `location` élément situé juste après l’ouverture `configuration` de balise, comme indiqué ici. (Assurez-vous que vous ajoutez uniquement les `location` élément et pas le balisage environnant qui est indiqué ici uniquement à fournir un contexte.)

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample2.xml?highlight=2-9)]

Le `Transform` cela entraîne la valeur d’attribut de « Insert » `location` élément à ajouter en tant que frère à existants `location` éléments dans le *Web.config* fichier. (Il existe déjà un `location` élément qui spécifie l’autorisation des règles pour le **crédits de mise à jour** page.) Lorsque vous testez le site de production après le déploiement, vous testerez pour vérifier que la règle d’autorisation est effective.

Vous n’êtes pas obligé de restreindre l’accès de journal d’erreur dans l’environnement de test, vous n’avez pas à ajouter ce code à la *Web.Test.config* fichier.

> [!NOTE] 
> 
> **Note de sécurité** jamais afficher les détails de l’erreur au public dans une application de production, ou stocker ces informations dans un emplacement public. Des personnes malveillantes peuvent utiliser des informations d’erreur pour détecter des vulnérabilités dans un site. Si vous utilisez ELMAH dans votre propre application, veillez à examiner les différentes façons dans lequel ELMAH peut être configuré afin de minimiser les risques de sécurité. L’exemple ELMAH dans ce didacticiel ne convient pas une configuration recommandée. Il s’agit d’un exemple qui a été choisi pour illustrer comment gérer un dossier que l’application doit être en mesure de créer des fichiers dans.


## <a name="setting-an-environment-indicator"></a>Définition d’un indicateur d’environnement

Un scénario courant consiste à avoir *Web.config* les paramètres qui doivent être différents dans chaque environnement que vous déployez sur des fichiers. Par exemple, une application qui appelle un service WCF peut-être un autre point de terminaison dans les environnements de test et de production. L’application Contoso University inclut un paramètre de ce type également. Ce paramètre contrôle un indicateur visible sur les pages d’un site qui vous indique l’environnement dans lequel vous vous trouvez dans, telles que le développement, test ou de production. La valeur du paramètre détermine si l’application permet d’ajouter « (Dev) » ou « (Test) » pour le titre principal dans le *Site.Master* page maître :

[![Environment_indicator](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image9.png)](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/_static/image8.png)

L’indicateur d’environnement est omis lorsque l’application est en cours d’exécution en production.

Les pages web de Contoso University lire une valeur qui est définie dans `appSettings` dans les *Web.config* fichier afin de déterminer quel environnement dans l’application s’exécute :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample3.xml)]

La valeur doit être « Test » dans l’environnement de test et « Production » dans l’environnement de production.

Ouvrez *Web.Production.config* et ajoutez un `appSettings` élément immédiatement avant la balise d’ouverture de la `location` élément que vous avez ajouté précédemment :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample4.xml)]

Le `xdt:Transform` attribut la valeur « SetAttributes » indique que l’objectif de cette transformation est pour modifier les valeurs d’attribut d’un élément existant dans le *Web.config* fichier. Le `xdt:Locator` valeur « Match(key) » indique que l’élément à modifier est celui de l’attribut dont `key` attribut correspond à la `key` attribut spécifié ici. Le seul attribut autres de le `add` élément est `value`, et c’est ce que sera modifié dans la version déployée *Web.config* fichier. Ce code génère la `value` attribut de la `Environment` `appSettings` élément avoir pour valeur « Production » le *Web.config* fichier qui est déployée en production.

Ensuite, appliquez le même changement *Web.Test.config* fichier, à l’exception du jeu le `value` à « Test » au lieu de « Production ». Lorsque vous avez terminé, le `appSettings` section *Web.Test.config* ressemblera à l’exemple suivant :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample5.xml)]

## <a name="disabling-debug-mode"></a>Désactivez le Mode débogage

Pour une version Release, vous ne souhaitez pas activer le débogage, quelle que soit l’environnement dans lequel vous effectuez le déploiement. Par défaut le *Web.Release.config* fichier de transformation est automatiquement créé par le code qui supprime la `debug` attribut à partir de la `compilation` élément :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample6.xml)]

Le `Transform` attribut provoque la `debug` attribut manquant à partir de la version déployée *Web.config* fichier chaque fois que vous déployez une version Release.

Cette transformation même est dans le Test et de Production des fichiers de transformation, car vous les avez créés en copiant le fichier de transformation mise en production. Vous n’avez pas besoin de dupliquer il, ouvrez chacun de ces fichiers, supprimez le **compilation** élément et enregistrez et fermez chaque fichier.

## <a name="setting-connection-strings"></a>Définition des chaînes de connexion

Dans la plupart des cas il est inutile configurer des transformations de chaîne de connexion, car vous pouvez spécifier des chaînes de connexion dans le profil de publication. Mais il existe une exception lorsque vous déployez une base de données SQL Server Compact, et vous utilisez des Migrations Entity Framework Code First pour mettre à jour la base de données sur le serveur de destination. Pour ce scénario, vous devez spécifier une chaîne de connexion supplémentaire qui sera utilisée pour mettre à jour le schéma de base de données sur le serveur. Pour définir cette transformation, ajoutez un  **&lt;connectionStrings&gt;**  élément situé juste après l’ouverture  **&lt;configuration&gt;**  balise dans les deux le *Web.Test.config* et *Web.Production.config* transformer des fichiers :

[!code-xml[Main](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12/samples/sample7.xml)]

Le `Transform` attribut spécifie que cette chaîne de connexion est ajoutée à la *connectionStrings* élément déployé *Web.config* fichier. (Le processus de publication crée automatiquement cette chaîne de connexion supplémentaires pour vous si elle n’existe pas, mais par défaut le **providerName** attribut Obtient la valeur `System.Data.SqlClient`, qui ne fonctionne pas pas pour SQL Server Compact. En ajoutant la chaîne de connexion manuellement, vous gardez le processus de déploiement à partir de la création d’un élément de chaîne de connexion avec le nom du fournisseur incorrect.)

Vous avez spécifié maintenant toutes les *Web.config* transformations dont vous avez besoin pour le déploiement de l’application Contoso University afin de tester et production. Dans ce didacticiel, vous devez prendre soin de tâches de configuration de déploiement qui requièrent la définition des propriétés de projet.

## <a name="more-information"></a>Informations complémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez le scénario de transformation Web.config dans [ASP.NET Deployment Content Map](https://msdn.microsoft.com/en-us/library/bb386521.aspx).

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-deploying-sql-server-compact-databases-2-of-12.md)
[Suivant](deployment-to-a-hosting-provider-configuring-project-properties-4-of-12.md)
