---
uid: web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : Transformations du fichier Web.config | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 5a2a927b-14cb-40bc-867a-f0680f9febd7
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations
msc.type: authoredcontent
ms.openlocfilehash: a88d8f35c770b362b74f787fee2c60a7577bccb2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-webconfig-file-transformations"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : Transformations du fichier Web.config
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel vous montre comment automatiser le processus de modification de la *Web.config* fichier lors de son déploiement dans les environnements de destination différents. La plupart des applications ont des paramètres le *Web.config* fichier doit être différent lorsque l’application est déployée. Automatisation du processus de ces modifications, vous évite d’avoir à le faire manuellement chaque fois que vous déployez, ce qui serait fastidieux et sujet aux erreurs.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](troubleshooting.md).

## <a name="webconfig-transformations-versus-web-deploy-parameters"></a>Transformations Web.config par rapport aux paramètres de déploiement Web

Il existe deux façons d’automatiser le processus de modification *Web.config* les paramètres des fichiers : [transformations Web.config](https://msdn.microsoft.com/en-us/library/dd465326.aspx) et [paramètres de déploiement Web](https://msdn.microsoft.com/en-us/library/ff398068.aspx). A *Web.config* fichier de transformation contient le balisage XML qui spécifie comment modifier le *Web.config* fichier lorsqu’il est déployé. Vous pouvez spécifier des modifications pour spécifiques à différentes configurations de build et spécifique des profils de publication. Les configurations de build par défaut sont Debug et Release, et vous pouvez créer des configurations de build personnalisée. Un profil de publication correspond généralement à un environnement de destination. (Vous allez apprendre plus sur Publier les profils dans le [déploiement vers IIS comme environnement de Test](deploying-to-iis.md) didacticiel.)

Paramètres de déploiement Web peuvent servir à spécifier les différents types de paramètres qui doivent être configurés au cours du déploiement, y compris les paramètres qui sont trouvent dans *Web.config* fichiers. Lorsqu’il est utilisé pour spécifier *Web.config* fichier change, les paramètres de Web Deploy sont plus complexes à configurer, mais ils sont utiles lorsque vous ne connaissez pas la valeur à définir jusqu'à ce que vous déployez. Par exemple, dans un environnement d’entreprise, vous pouvez créer un *package de déploiement* et lui donner à une personne du département informatique pour installer en production, et que cette personne doit être en mesure d’entrer des chaînes de connexion ou mots de passe que vous ne connaître.

Pour le scénario qui couvre de cette série de didacticiels, vous savez à l’avance tout ce qui doit être effectuée à la *Web.config* de fichiers, vous n’avez pas besoin d’utiliser les paramètres de déploiement Web. Vous devez configurer certaines transformations qui diffèrent en fonction de la configuration de build utilisée, et certaines qui varient selon le profil de publication utilisé.

<a id="watransforms"></a>

## <a name="specifying-webconfig-settings-in-azure"></a>Spécifier les paramètres Web.config dans Azure

Si le *Web.config* sont des paramètres du fichier que vous souhaitez modifier dans le `<connectionStrings>` ou `<appSettings>` élément, et si vous déployez des applications Web dans Azure App Service, vous disposez d’une autre option pour l’automatisation des modifications pendant déploiement. Vous pouvez entrer les paramètres que vous souhaitez prendre effet dans Azure dans le **configurer** onglet de la page de portail de gestion pour votre application web (faites défiler jusqu'à la **paramètres de l’application** et **les chaînes de connexion**  sections). Lorsque vous déployez le projet, Azure applique automatiquement les modifications. Pour plus d’informations, consultez [Sites Web Windows Azure : manière dont les chaînes d’Application et de travail des chaînes de connexion](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

## <a name="default-transformation-files"></a>Fichiers de transformation par défaut

Dans **l’Explorateur de solutions**, développez *Web.config* pour voir les *Web.Debug.config* et *Web.Release.config* fichiers de transformation sont créés par défaut pour les configurations de build par défaut de deux.

![Web.config_transform_files](web-config-transformations/_static/image1.png)

Vous pouvez créer des fichiers de transformation pour les configurations de build personnalisée en double-cliquant sur le fichier Web.config et en choisissant **ajouter des transformations de configuration** dans le menu contextuel. Pour ce didacticiel vous n’avez pas besoin pour ce faire, et l’option de menu est désactivée, car vous n’avez pas créé de toutes les configurations de génération personnalisée.

Plus tard vous allez créer trois fichiers de transformation plus, un pour le test, intermédiaire et production des profils de publication. Un exemple typique d’un paramètre que vous pouvez gérer dans un fichier de transformation de profil de publication, car elle dépend de l’environnement de destination est un point de terminaison WCF est différent pour le test et production. Vous allez créer publier les fichiers de transformation de profil dans des didacticiels ultérieurs après avoir créé les profils de publication qui ils vont avec.

## <a name="disable-debug-mode"></a>Désactiver le mode débogage

Un exemple d’un paramètre qui varie selon la configuration de build, plutôt que l’environnement de destination est le `debug` attribut. Pour une version Release, il est souhaitable de désactiver le débogage, quelle que soit l’environnement dans lequel vous effectuez le déploiement. Par conséquent, Visual Studio par défaut, les modèles de projet créent *Web.Release.config* transformer des fichiers avec le code qui supprime la `debug` attribut à partir de la `compilation` élément. Ici est la valeur par défaut *Web.Release.config*: outre un exemple de code de transformation qui est commentée, il inclut le code dans le `compilation` élément supprime le `debug` attribut :

[!code-xml[Main](web-config-transformations/samples/sample1.xml?highlight=18)]

Le `xdt:Transform="RemoveAttributes(debug)"` attribut spécifie que vous souhaitez le `debug` attribut à supprimer de la `system.web/compilation` élément déployé *Web.config* fichier. Ce sera fait chaque fois que vous déployez une version Release.

## <a name="limit-error-log-access-to-administrators"></a>Limiter l’accès de journal d’erreur pour les administrateurs

Si une erreur est survenue pendant l’exécution de l’application, l’application affiche une page d’erreur générique à la place de la page d’erreur générée par le système et il utilise le [package Elmah NuGet](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) pour la journalisation des erreurs et création de rapports. Le `customErrors` élément dans l’application *Web.config* fichier spécifie la page d’erreur :

[!code-xml[Main](web-config-transformations/samples/sample2.xml)]

Pour afficher la page d’erreur, modifiez temporairement le `mode` attribut de la `customErrors` élément à partir de « RemoteOnly » à « Sur » et exécuter l’application à partir de Visual Studio. Génère une erreur en demandant une URL non valide, tel que *Studentsxxx.aspx*. Au lieu d’une erreur générés par IIS » la ressource ne peut pas être trouvée » page, vous voyez le *GenericErrorPage.aspx* page.

![Page d’erreur](web-config-transformations/_static/image2.png)

Pour afficher le journal des erreurs, remplacez tout dans l’URL après le numéro de port avec *elmah.axd* (par exemple, `http://localhost:51130/elmah.axd`) et appuyez sur ENTRÉE :

![Page ELMAH](web-config-transformations/_static/image3.png)

N’oubliez pas de définir la `customErrors` élément vers le mode « RemoteOnly » lorsque vous avez terminé.

Sur votre ordinateur de développement, il convient d’autoriser l’accès libre à la page journal d’erreurs, mais en production qui serait un risque de sécurité. Pour le site de production, vous souhaitez ajouter une règle d’autorisation qui limite l’accès de journal d’erreur pour les administrateurs et pour vous assurer que la restriction fonctionne vous voulez dans le test et également de mise en lots. Par conséquent, il s’agit d’une autre modification que vous souhaitez implémenter chaque fois que vous déployez une version Release, donc elle appartient à la *Web.Release.config* fichier.

Ouvrez *Web.Release.config* et ajouter un nouveau `location` élément immédiatement avant la fermeture `configuration` de balise, comme indiqué ici.

[!code-xml[Main](web-config-transformations/samples/sample3.xml?highlight=27-34)]

Le `Transform` cela entraîne la valeur d’attribut de « Insert » `location` élément à ajouter en tant que frère à existants `location` éléments dans le *Web.config* fichier. (Il existe déjà un `location` élément qui spécifie l’autorisation des règles pour le **crédits de mise à jour** page.)

Maintenant vous pouvez afficher un aperçu de la transformation pour vous assurer codé correctement.

Dans **l’Explorateur de solutions**, avec le bouton droit *Web.Release.config* et cliquez sur **aperçu transformer**.

![Afficher un aperçu du menu de transformation](web-config-transformations/_static/image4.png)

Ouvre une page qui vous présente le développement *Web.config* fichier gauche et déployé *Web.config* fichier ressemblera à droite, avec les modifications mises en surbrillance.

![Aperçu de la transformation de débogage](web-config-transformations/_static/image5.png)

![Aperçu de la transformation de l’emplacement](web-config-transformations/_static/image6.png)

(Dans la version préliminaire, vous remarquerez peut-être des modifications supplémentaires que vous n’avez pas écrire des transformations pour : elles impliquent en général la suppression des espaces blancs qui n’affecte pas les fonctionnalités.)

Lorsque vous testez le site après le déploiement, vous allez également tester pour vérifier que la règle d’autorisation est effective.

> [!NOTE] 
> 
> **Note de sécurité** jamais afficher les détails de l’erreur au public dans une application de production, ou stocker ces informations dans un emplacement public. Des personnes malveillantes peuvent utiliser des informations d’erreur pour détecter des vulnérabilités dans un site. Si vous utilisez ELMAH dans votre propre application, configurez ELMAH afin de minimiser les risques de sécurité. L’exemple ELMAH dans ce didacticiel ne convient pas une configuration recommandée. Il s’agit d’un exemple qui a été choisi pour illustrer comment gérer un dossier que l’application doit être en mesure de créer des fichiers dans. Pour plus d’informations, consultez [sécurisation du point de terminaison ELMAH](https://code.google.com/p/elmah/wiki/SecuringErrorLogPages).


## <a name="a-setting-that-youll-handle-in-publish-profile-transformation-files"></a>Un paramètre que vous allez gérer dans les fichiers de transformation de profil de publication

Un scénario courant consiste à avoir *Web.config* les paramètres qui doivent être différents dans chaque environnement que vous déployez sur des fichiers. Par exemple, une application qui appelle un service WCF peut-être un autre point de terminaison dans les environnements de test et de production. L’application Contoso University inclut un paramètre de ce type également. Ce paramètre contrôle un indicateur visible sur les pages d’un site qui vous indique l’environnement dans lequel vous vous trouvez dans, telles que le développement, test ou de production. La valeur du paramètre détermine si l’application permet d’ajouter « (Dev) » ou « (Test) » pour le titre principal dans le *Site.Master* page maître :

![Indicateur d’environnement](web-config-transformations/_static/image7.png)

L’indicateur d’environnement est omis lorsque l’application s’exécute en production ou intermédiaire.

Les pages web de Contoso University lire une valeur qui est définie dans `appSettings` dans les *Web.config* fichier afin de déterminer quel environnement dans l’application s’exécute :

[!code-xml[Main](web-config-transformations/samples/sample4.xml)]

La valeur doit être « Test » dans l’environnement de test et « Production » intermédiaire et de production.

Le code suivant dans un fichier de transformation implémentera cette transformation :

[!code-xml[Main](web-config-transformations/samples/sample5.xml)]

Le `xdt:Transform` attribut la valeur « SetAttributes » indique que l’objectif de cette transformation est pour modifier les valeurs d’attribut d’un élément existant dans le *Web.config* fichier. Le `xdt:Locator` valeur « Match(key) » indique que l’élément à modifier est celui de l’attribut dont `key` attribut correspond à la `key` attribut spécifié ici. Le seul attribut autres de le `add` élément est `value`, et c’est ce que sera modifié dans la version déployée *Web.config* fichier. Le code indiqué ici causes le `value` attribut de la `Environment` `appSettings` élément soit définie à « Test » dans le *Web.config* fichier qui est déployé.

Cette transformation appartienne dans les fichiers de transformation de profil de publication, qui vous n’avez pas encore créé. Vous allez créer et mettre à jour les fichiers de transformation qui implémentent cette modification lorsque vous créez les profils de publication pour les environnements de test, intermédiaire et de production. Vous devez le faire le [déployer sur IIS](deploying-to-iis.md) et [déployer en production](deploying-to-production.md) didacticiels.

> [!NOTE]
> Étant donné que ce paramètre est dans le `<appSettings>` élément, que vous avez une solution de rechange pour la spécification de la transformation lorsque vous déployez pour des applications Web dans Azure App Service, consultez [paramètres spécifiant le fichier Web.config dans Azure](#watransforms) plus haut dans Cette rubrique.


## <a name="setting-connection-strings"></a>Définition des chaînes de connexion

Bien que le fichier de transformation par défaut contient un exemple qui montre comment mettre à jour une chaîne de connexion, dans la plupart des cas il est inutile configurer des transformations de chaîne de connexion, car vous pouvez spécifier des chaînes de connexion dans le profil de publication. Vous devez le faire le [déployer sur IIS](deploying-to-iis.md) et [déployer en production](deploying-to-production.md) didacticiels.

## <a name="summary"></a>Résumé

Vous avez maintenant terminé l’autant que possible avec *Web.config* transformations avant que vous créez les profils de publication et que vous avez déjà vu un aperçu de ce que sera dans le fichier Web.config déployé.

![Aperçu de la transformation de l’emplacement](web-config-transformations/_static/image8.png)

Dans ce didacticiel, vous devez prendre soin de tâches de configuration de déploiement qui requièrent la définition des propriétés de projet.

## <a name="more-information"></a>Informations complémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez [transformations à l’aide de Web.config pour modifier des paramètres dans le fichier Web.config de destination ou le fichier app.config au cours du déploiement](https://go.microsoft.com/fwlink/p/?LinkId=282413#transforms) dans le plan de contenu de déploiement Web pour Visual Studio et ASP.NET.

>[!div class="step-by-step"]
[Précédent](preparing-databases.md)
[Suivant](project-properties.md)
